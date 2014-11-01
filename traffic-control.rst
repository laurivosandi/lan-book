.. title: Liikluse kujundamine
.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags:  tc, iptables, netfilter, QoS, traffic shaping

Liikluse kujundamine
====================

Sissejuhatus
------------

Tihtipeale tekib vajadus seada piiranguid võrgus, selleks et üks
kasutaja kogu ressursse omale ei võtaks. Kõige labasemas näites
üks kasutajatest käitab BitTorrent-it oma masinas. BitTorrent
allalaadimise ajal laadib ka üles andmeid. Tihtipeale on nii, et
nõudlust on rohkem kui pakkujaid ning BitTorrent-i kasutaja kulutab 
rohkem ribalaiust üleslaadimiseks, kui ise alla saab laaditud.
Üleslaadimise ribalaiust võib kuluda nii palju, et teiste
kasutajate veebilehitsemine jääb pidama selle taha, et kohtvõrgust
väljaminevad ühendused ei mahu piraadi üleslaadimiste kõrvale ära 
või tõstab väljaminevate ühenduste latentsust märgatavalt ning
näib et Internet on aeglane. Kuna BitTorrent loob sadu ühendusi
erinevate masinatega üle Interneti, võib juhtuda et marsruuteri ühenduste
jälgmise (*conntrack*) tabelid võtavad nii palju ruumi, et marsruuter
jookseb kokku. Säärast nähtust võib täheldada eriti ruuteritega,
kus mälu on vähe, näiteks WRT54G v5.0 koos 8MB operatiivmäluga.

Kuidas lahendada sääraseid probleeme? Kõige lihtsam on kontori seinale panna
suur silt, et meil ei piraadita. Ilmselgelt see ei takista kedagi, teisalt
see näitab võrguadministraatori ebapädevust.
Tehniliste lahedustena võiks proovida piirata ribalaiust ning ühenduste arvu 
kohtvõrgu masina kohta. Vähese mäluga marsruuteritel võib proovida piirata
ka kogu ühenduste arvu, et võrguaadresside tõlkimise tabel täis ei saaks. Ühenduste
piiramisel on aga oma pahupooled, kui mälu täis ei saa siis ühenduste tabeli täis
saamise korral lihtsalt uusi ühendusi ei lubata luua. Selle vastu aitab 
TCP ühenduste ooteaja (*timeout*) lühendamine nt 10 sekundi peale.
Täiendavalt võiks pakette prioritiseerida protokolli järgi, 
eelisjärjekorras läbi lasta reaalajarakendused nagu OpenSSH ning mängud,
peale seda veeb ning viimasena kõik üleliigne nagu BitTorrent.

Võrguliikluse kujundamine
-------------------------

Kokkuvõtvalt liikluse kujundamine koosneb:

* Ühenduste arvu (*connection* *count*) piiramisest
* Ribalaiuse (*bandwidth*) piiramisest
* Protokollide prioritiseerimisest

Reaalseld mõõdetavad attribuudid mille järgi võrgu jõudlust mõõta on kaks:
latentsus ehk aeg mis kulub võrgurakendusel päringu tegemisest vastuse saamiseni ning
läbilaskevõime ehk allalaadimis- ning üleslaadimiskiirus.


Ühenduste arvu piiramine
------------------------

Kohalikule võrgurakendusele sissetulevaid ühendusi saab
piirata *connlimit* mooduli abil.
Järgnevas näites seame veebiserveri pordile 80 sissetulevate ühenduste ülempiiriks
100 ühendust lähte IP-aadressi kohta:

.. code:: bash

    # Kuni 100 samaaegset ühendust pordile 80
    iptables \
        -A INPUT -p tcp --syn --dport 80 \
        -m connlimit --connlimit-above 100 \
        -j REJECT --reject-with tcp-reset

Analoogselt peaks saama piirata sisevõrgust Internetti minevate ühenduste arvu:

.. code:: bash

    # LAN-liideselt WAN-liidesele edastatavate pakettide puhul
    # sea lähte IP-aadressi kohta ühenduste ülempiiriks 50
    iptables \
        -o eth0 -i eth1 -s 192.168.55.0/24 \
        -I FORWARD -p tcp --syn \
        -m connlimit --connlimit-above 50 \
        -j REJECT --reject-with tcp-reset
        
        
Queuing discipline
------------------

Keerukamate stsenaariumite puhul tuleks kasutada *tc* käsku, et tuumale ette 
sööta pakettide järjekorrastaja [#traffic-control]_.
Käsk *ip* *link* *list* näitab ära,
mis *qdisc* (*queuing* *discipline*) ehk pakettide järjestamise kord kehtib võrguliidesel:

* *noqueue* - Pakette ei järjestata ega puhverdata
* *pfifo* - Paketid ühes järjekorras, *qlen* määrab maks. pakettide arvu
* *bfifo* - Sama mis *pfifo*, *qlen* määrab maks. pakettide kogusuuruse baitides
* *pfifo_fast* - Paketid kolmes järjekorras, eelistatud on interaktiivsete rakenduste paketid
* *sfq* (*stochastic* *fair* *queuing*) - Kaootiline pakettide järjekorrastaja
* *tbf* (*token* *bucket* *filter*) - Lihtne pakettide väljastamise kiiruse piiraja
* *htb* (*hierarchical* *token* *bucket*) - Hierarhiline pakettide järjekorrastaja
* *hsfc* (*hierarchical* *fair* *service* *curve*)
* *prio* (*priority* *scheduler*)
* *cbq* (*class* *based* *queuing*)

Vaikimisi pakettide järjekorrastaja on *pfifo_fast*, mis tavakastuja
arvuti puhul pädeb väga hästi. Kui, meil on tegemist serveri või 
pakette vahendava arvutiga lihtsamatest pakettide järjekorrastajatest ei piisa.
Üheks kõige populaarsemaks on osutunud *htb*.

Pakettide järjekorrastamist saab rakendada ainult väljaminevatele pakettidele.
Marsruuteri rolli täitvas masinas saab seega kiirust piirata mõlemas suunas:

.. figure:: dia/router-packet-flow-nat.svg

    Pakettide järjekorrastamine toimub peale POSTROUTING ahela läbimist.


Selleks, et vaikimisi järjekorrastaja vahetada *htb* vastu:

.. code:: bash

    # Kui võrguliidesel on juba järjekorrastaja, püüa see eemaldada
    tc qdisc del root dev eth1

    # Vaheta välja vaikimisi pfifo_fast järjekorrastaja
    tc qdisc add dev $LAN root handle 1: htb default 0


Lisa peaklassi sisse alamklassid, millega piirata peaklassi sees liiklust:

.. code:: bash

    # Lisa liidese juurklassi (1:0) HTB järjekorrastaja klass (1:1) 8MBit/s limiidiga
    tc class add dev eth1 parent 1: classid 1:1 htb rate 8192kbps ceil 8192kbps prio 0

    # Lisa liidese juurklassi (1:0) HTB järjekorrastaja klass (1:2) 1MBit/s limiidiga
    tc class add dev eth1 parent 1: classid 1:2 htb rate 1024kbps ceil 1024kbps 

Iganenud *tc* kasutusjuht nägi ette ka *tc* *filter* käsuga filtrite lisamist,
kuid pisut modernsem, paindlikum ning jõudluse poolest parem viis on
klassifitseerida paketid *iptables* abil:

.. code:: bash

    # Internetist sisevõrku liikuvad paketid paigutatakse 1:2 klassi
    iptables -I FORWARD -t mangle -i eth0 -o eth1 -j CLASSIFY --set-class 1:2

Klassifitseerimist saab ka kasutada käsikäes markeerimisega kuna
klassifitseerimist ei saa igas ahelas kasutada:

.. code:: bash

    # Markeeri prerouting ahelas paketid
    iptables -I PREROUTING -t mangle -i eth0 -j MARK --set-mark 0x42

    # Klassifitseeri eelnevalt markeeritud paketid
    iptables -I POSTROUTING -t mangle -m mark --mark 0x42 -j CLASSIFY --set-class 1:3

Pakettide liikumist erinevates klassides saab jälgida *tc* abil:

.. code:: bash

    watch --interval=0.1 tc -s class show dev eth1

.. [#traffic-control] `Traffic Control HOWTO <http://tldp.org/HOWTO/Traffic-Control-HOWTO/>`_
