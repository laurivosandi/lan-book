.. title: ebtables
.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags: ebtables, iptables, private
.. date: 2013-10-31

ebtables
========

Võiks arvata et *iptables* abil saab filtreerida ka MAC-aadresse.
Järgnev näide keelab internetti ligipääsu teatud sisevõrgu masinal,
tuvastades sellelt masinalt tulevad paketid lähte MAC-aadressi järgi:

.. code:: bash

    # Lauri telefon internetti ei saa!
    iptables -I FORWARD \
        -i eth1 -m mac --mac-source 88:30:8a:43:bf:53 \
        -j REJECT


Selleks ülesandeks on aga rohkem spetisaliseerunud *ebtables* ehk 
Linux-i silla tulemüür.

Eelneva näite võime ümber kirjutada järgnevalt:

.. code:: bash

    ebtables -t nat -I PREROUTING \
        -s 88:30:8a:43:bf:53 \
        -j DROP

Mille poolest see käsk erineb eelmisest? Esiteks võib eeldada mõningast
jõudluse võitu, kuna *ebtables* reegleid hinnatakse enne kui *iptables* omi.
Teiseks on süntaks MAC-aadressi keskne, vastandudes *iptables*-i 
IP-aadressi kesksele süntaksile.

Veel mõningaid kasutusalasid *ebtables*-ile:

* IP-aadressi võltsimisevastased võtted
* MAC-aadresside NAT
* Pakettide markeerimine



    
