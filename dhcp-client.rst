.. title: DHCP klient
.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags: 
.. date: 2013-10-31

DHCP klient
===========

Arvutitele IP aadresside jagamine käib DHCP ehk
dünaamilise hostikonfiguratsiooni protokolli
(*dynamic* *host* *configuration* *protocol*) abil.

Arvuti, mis soovib võrgust konfiguratsiooni hankida, saadab välja
paketi UDP üldleviaadressile (*broadcast* *address*) pordil 67.
DHCP server vastab paketiga, kus on kirjas vähemalt:

* Masinale eraldatud IP-aadress
* Alamvõrgu mask, 
* Lüüsi aadress
* Nimeserverite aadressid

Täiendavalt võib server anda:

* Kellaserverite aadressid
* Masinanime (*hostname*)
* Domeeninime (*domain* *name*)

Masinad mis teevad võrgust alglaadimist saavad ka:

* Tuuma (*kernel*) aadressi
* Juurfailisüsteemi (*root* *filesystem*) aadressi
* Saaleala (*swap*) aadressi

Ubuntu ning Debian on kaasa pakendanud 
`Internet Systems Consortium-i DHCP <http://www.isc.org/downloads/dhcp/>`_
klientrakenduse, mille saab käivitada *dhclient* käsuga:

.. code::

    root@localhost:~# dhclient -v eth0 
    Internet Systems Consortium DHCP Client 4.2.2
    Copyright 2004-2011 Internet Systems Consortium.
    All rights reserved.
    For info, please visit https://www.isc.org/software/dhcp/

    Listening on LPF/eth0/08:00:27:a6:bf:58
    Sending on   LPF/eth0/08:00:27:a6:bf:58
    Sending on   Socket/fallback
    DHCPREQUEST on eth0 to 255.255.255.255 port 67
    DHCPACK from 192.168.3.1
    bound to 192.168.3.248 -- renewal in 18529 seconds.

Käesolevas näites andis DHCP server aadressil 192.168.3.1 meie masinale 
aadressiks 192.168.3.248 orienteeruvalt viieks päevaks.

Analoogselt staatilise seadistusega, ei ole need jäävad. Selleks, 
et seaded ka Debian/Ubuntu taaskäivituse üle elaks tuleb lisada nad
*/etc/network/interfaces* faili:

.. code::

    auto eth0
    iface eth0 inet dhcp

OpenWrt-s ning teistes sardsüsteemides on kasutusel BusyBox koosseisu kuuluv
µDHCPc (*micro* *DHCP* *client*). Sellele vastav käsk *udhcpc* erineb
märgatavalt ISC DHCP kliendist.

