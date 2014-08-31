.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags:  iptables
.. date: 2013-10-31

Kohalike rakenduste turvamine
=============================

Sissejuhatus
------------

Kohalikud rakendused (Apache, Firefox, Skype jms)
opereerivad INPUT ning OUTPUT ahelates.
TCP ning UDP ühenduste koosseisu kuuluvad IP-paketid
väljuvad masinast OUTPUT ahela kaudu ning sisenedes läbivad
INPUT ahela.

.. figure:: dia/firewall-packet-flow.svg
    :width: 80%

    Pakettide töötlemine võrguteenust pakkuvas masinas

Ubuntu vaikimisi tulemüür on lubavas asendis, kõik *input* ja *output* ahelatesse
sattunud paketid lubatakse läbi.
Probleemi pole seni, kui mõni võrgurakendus (Apache, MySQL vms)
avab kuulava sokli. Iga kuulav sokkel on potensiaalne turvarisk ning
isegi kui võrgurakendus on seadistatud nii paranoiliselt kui võimalik, 
kulub ära ka seada võimalikult palju piiranguid *netfilter/iptables* tasemel.


Keerame torud kinni
-------------------

Alustame sellest, et muudame vaikimisi *iptables* poliitika ära:

.. code:: bash

    iptables -P INPUT DROP
    iptables -P OUTPUT DROP

Selle tulemusena ei ole võimalik ligi pääseda selles masinas käitatavatele
võrgurakendustele ega ka internetti lehitseda kuna välja minevaid IP-pakette
ei lasta läbi.

.. code:: bash

    # Tühjenda input ja output ahelad
    iptables -F INPUT
    iptables -F OUTPUT

    # Luba väljaminevad paketid mis algatavad uue ühenduse või
    # kuuluvad loodud ühenduste koosseisu
    iptables -A OUTPUT -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT

    # Luba sisse loodud ühendustega seotud paketid
    iptables -A INPUT -m conntrack --ctstate ESTABLISHED -j ACCEPT

    # Lükka selgesõnaliselt tagasi paketid mis eelnevatele reeglitele ei vasta
    iptables -A INPUT -j REJECT
    iptables -A OUTPUT -j REJECT

Kui miskit valesti läheb ja enam Internetti ei pääse, saab tabelid tühjaks teha
nõnda:

.. code:: bash

    iptables -F INPUT
    iptables -F OUTPUT
    iptables -A INPUT -j ACCEPT
    iptables -A OUTPUT -j ACCEPT

Ubuntu töölaua puhul, kus kasutatakse *dnsmasq* tarkvara DNS päringute puhverdamiseks,
peab lisama kirje, mis lubab UDP pordil 53 liiklust *localhost*-ist:

.. code:: bash

    # Luba kohalikud DNS päringud
    iptables -I INPUT -p udp --dport 53 -i lo -j ACCEPT

Üleüldse oleks viisakas lubada kogu liiklust *localhost*-ile:

.. code:: bash

    # Luba alati localhost liiklus
    iptables -I INPUT -i lo -j ACCEPT
    iptables -I OUTPUT -o lo -j ACCEPT


Lubame võrgust ligipääsu mõnele teenusele
-----------------------------------------

OpenSSH teenusele võiks anda ka ligipääsu kohtvõrgust:

.. code:: bash

    # Luba ligipääs teenusele OpenSSH
    iptables -I INPUT -p tcp --dport 22 -s 192.168.55.0/24 -j ACCEPT

Sama reegli võib ka kirjutada **teisel** **kujul** - 
selleks et pordile 22 ühendudes antaks selgesõnaline ei, võib reeglid
tahurpidi keerata:

.. code:: bash

    # Keela võõrastelt IP-delt ligipääs teenusele OpenSSH
    iptables -I INPUT -p tcp --dport 22 ! -s 192.168.55.0/24 -j REJECT

Ka liidese järgi lubamine on sobiv:

.. code:: bash

    # Luba kohtvõrgu liideselt tulevad päringud
    iptables -I INPUT -i eth1 -p tcp --dport 22 -j ACCEPT
