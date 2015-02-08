.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags: dnsmasq
.. date: 2013-10-31

Kohtvõrgu jaoks DHCP ja DNS puhvri seadistamine dnsmasq abil
============================================================

Sissejuhatus
------------

Kohtvõrgu elu teeb palju mugavamaks DHCP server jagades välja võrguseadistusi
kohtvõrku lülitatud masinatele.
Käesolev näide tugineb *dnsmasq*-le, mis on kaks-ühes DHCP server ning DNS vahemälu.
See tähendab, et *dnsmasq* vastab nii DHCP päringutele kui ka 
puhverdab nimeserveri päringuid.
Nimetatud tarkvara leiab näiteks OpenWrt marsruuteri tarkvarakogumikust ning
ka paljudest teistest müügil olevatest WiFi ruuteritest.

Paigaldus
---------

Debianis ning Ubuntus saab *dnsmasq* paigaldada järgneva käsuga:

.. code:: bash

    sudo apt-get install dnsmasq

Tähele tuleks panna seda, et Ubuntu töölaua puhul on *dnsmasq-base* pakett
juba paigaldatud, kuna NetworkManager vajab seda oma. Eraldiseisva *dnsmasq*
paketi paigaldamine võib konflikti minna NetworkManager-iga.
Selleks, et kohaliku võrgu nimed korrektselt lahenduks, *dnsmasq* kirjutab */etc/resolv.conf* faili üle,


Seadistamine
------------

Järgnevalt seadistame võrguliidesed failis */etc/network/interfaces*:

.. code:: bash

    # This file describes the network interfaces available on your system
    # and how to activate them. For more information, see interfaces(5).

    # The loopback network interface
    auto lo
    iface lo inet loopback

    # WAN liides saab IP-aadressi DHCP-ga
    auto eth0
    iface eth0 inet dhcp

    # LAN on meie sisemine võrk
    auto eth1
    iface eth1 inet static
	    address 192.168.55.1
	    netmask 255.255.255.0
	    
Laadime võrguseadistused uuesti:

.. code:: bash

    sudo service networking restart

Lisame uued seadistused failis */etc/dnsmasq.d/lan*:

.. code:: ini

    # Kuula nimeserveri päringuid pordil 53
    port=53
    
    # Täpsusta domeeninimi, et masinad oleks masinanimi.lan kujul ka kättesaadavad
    domain=lan
    
    # Käita rakendust privilegeerimata kasutajana
    # minimeerimaks turvariske
    user=nobody
    group=nogroup

    # Seo DHCP server LAN võrguliidesega
    interface=eth1

    # Serveeri LAN liidesel IP vahemikku 192.168.55.50-150 kuni 12 tunniks
    dhcp-range=192.168.55.50,192.168.55.150,12h
    
    # Täpsusta LAN lüüsi aadress, vaikimisi sama mis DHCP serveri aadress
    #dhcp-option=option:router,192.168.55.20
    
    # Täpsusta LAN nimeserverid, vaikimisi sama mis DHCP serveri aadress
    #dhcp-option=option:dns-server,8.8.8.8,8.8.4.4
    
    # Määra WAN võrgu nimeserver
    server=194.126.115.18

Laadi uuesti *dnsmasq* seadistused:

.. code:: bash

    sudo service dnsmasq restart

Nüüdseks peaksid sisevõrku ühendatud arvutid olema saanud IP-aadressi
vahemikus 192.168.55.50-150. DHCP abil IP saanud masinate nimekirja kirjutab
*dnsmasq* ka välja:

.. code:: bash

    cat /var/lib/misc/dnsmasq.leases
