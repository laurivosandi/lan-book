.. title: Pakettide markeerimine
.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags: iptables, connmark

Pakettide markeerimine
======================

Igal *iptables* ahelal on oma eesmärk ning igas ahelas on erinev
paketi metainfo (lähte/siht MAC/IP/port) alamhulk
teada. Tihtipeale tekib vajadus ühes ahelas teha otsus
mõnes muus ahelas oleva info järgi.
Selleks saab kasutada *iptables*-i *mark* moodulit [#connmark]_.

Näide ilma *mark* moodulita:

.. code:: bash

    # Blokeeri masin MAC-aadressi järgi nii input kui forward ahelas
    iptables -I INPUT -m mac --mac-source 88:30:8a:43:bf:53 -j REJECT
    iptables -I FORWARD -m mac --mac-source 88:30:8a:43:bf:53 -j REJECT

Näide *mark* mooduliga, MAC-aadress on esindatud üks kord:

.. code:: bash

    # Markeeri pakett
    iptables -I PREROUTING -t mangle \
        -i eth1 -m mac --mac-source 88:30:8a:43:bf:53 \
        -j MARK --set-mark 0x55


    # Lükka tagasi 
    iptables -I INPUT -m mark --mark 0x55 -j REJECT
    iptables -I FORWARD -m mark --mark 0x55 -j REJECT

Pakettide markeering on puhtalt tuuma (*kernel*) konseptsioon ning
võrgus see välja ei paista. See tähendab seda, et vaikimisi arvutist väljuva 
paketiga kaob ka info markeeringu kohta ning olemasoleva ühenduse korral
arvutisse sisenevad uued paketid seda infot ei kanna.

Lisamooduliga *connmark* saab selle info säilitada olemasolevate (*related*) ühenduste kohta:

.. code:: bash

    iptables -A PREROUTING -t mangle -j CONNMARK --restore-mark
    iptables -A POSTROUTING -t mangle -j CONNMARK --save-mark
    
.. [#connmark] `Netfilter Connmark <https://home.regit.org/netfilter-en/netfilter-connmark/>`_
