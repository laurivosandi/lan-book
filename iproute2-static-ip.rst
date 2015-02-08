.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. license: cc-by-3
.. tags:  iptables, IP, Internet Protocol
.. date: 2013-10-31

Võrguliidesele staatilise IP-aadressi seadistamine
==================================================

Nüüd kui teame,
`mis võrguliidesed meie arvutis on <iproute2-introduction.html>`_,
mis IP-aadressid neil on ning mis alamvõrkudes meie masin suhtleb,
võime edasi liikuda nende võrguliideste
seadistamise juurde.

Eeldades, et võrguliides pole hallatud mõne muu vahendi poolt (nt NetworkManager),
peaksime kõigepealt võrguliidesele lisama staatilise IP-aadressi:

.. code:: bash

    # Eemalda eelnevalt antud IP-aadressid
    ip address flush dev eth0

    # Seosta võrguliides eth0 IP-aadressiga 192.168.3.2 võrgumaskiga 255.255.255.0
    ip address add 192.168.3.2/24 dev eth0

    # Tõsta üles liides
    ip link set dev eth0 up

Järgnevalt oleks paslik kontrollida *ping* käsu abil, kas ühendus lüüsiga on olemas.
Kui see tõepoolest nii on, võime lisada marsruutimistabelisse lüüsi aadressi:

.. code:: bash

    # Lisa marsruutimistabelisse kirje, et vaikimisi lüüsiks on 192.168.3.1
    ip route add default via 192.168.3.1

Siit edasi võiks *ping*-ida mõnda avalikku IP-aadressi. Kuna meil on
naguinii nimeserver veel paika panemata, võibki proovida *ping*-ida
näiteks Elioni nimeservereid 194.126.115.18 või Google omi 8.8.8.8, 8.8.4.4.
Kui vähemalt üks neist vastab, võib eeldada et ühendus Internetiga on olemas.
DNS (*Domain* *Name* *Service*) ehk nimelahendus on võrguteenus 
mis tõlgendab domeeninime, nt. www.google.com ümber IP-aadressiks,
nt. 173.194.78.139:

Selleks et nimed lahenduks, tuleb vastav kirje lisada */etc/resolv.conf* faili:

.. code:: bash

    echo "nameserver 8.8.8.8 8.8.4.4" > /etc/resolv.conf

Peale seda peaks Internet Systems Consortium-i BIND9 tarkvara koosseisu kuuluv
*nslookup* käsk pisut pädevamat väljundit andma:

.. code::

    marsruuter ~ # nslookup google.com
    Server:		127.0.1.1
    Address:	127.0.1.1#53

    Non-authoritative answer:
    Name:	google.com
    Address: 173.194.78.102
    Name:	google.com
    Address: 173.194.78.139
    Name:	google.com
    Address: 173.194.78.113
    Name:	google.com
    Address: 173.194.78.101
    Name:	google.com
    Address: 173.194.78.100
    Name:	google.com
    Address: 173.194.78.138

Kuna käsk *ip* manipuleerib jookvaid tuuma seadistusti, tuleb need seadistused
kuidagi jäädavaks teha. Kõige labasem oleks käsud pista mõnesse algkäivitusel
käitatavasse faili nt /etc/rc.local, 
Debian ning Ubuntu on omal moel standardiseerinud
võrguseadistuste jäädvustamise /etc/network/interfaces failis.
Sellel on ka oma süntaks, mille kohta räägib täpsemalt *man* *interfaces*:

.. code::

    auto eth0
    iface eth0 inet static
	    address 192.168.3.2
	    netmask 255.255.255.0
	    gateway 192.168.3.1
	    dns-nameservers 8.8.8.8 8.8.4.4

Seadete rakendamiseks tuleb taaskäivitada teenus *networking*:

.. code:: bash

    service networking restart

Üksikute võrguliideste käivitamiseks */etc/networking/interfaces* faili alusel:

.. code:: bash

    ifup eth0

Ning peatamiseks:

.. code:: bash

    ifdown eth0

