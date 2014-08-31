.. title: Teenuste edasisuunamine
.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags:  port forwarding, iptables
.. date: 2013-10-31

Teenuste edasisuunamine
=======================

Kui marsruutimine toimib, tekib tihtipeale vajadus sisevõrgus olev teenus,
näiteks veebiserver, maailmale nähtavaks teha. Selleks tuleb lisada
reegel edastamise ahelasse. Tähelepanu tuleks pöörata sellele, et siinkohal
lisame reegli algusesse võtmega -I (*insert*), erinevalt eelnevatest käskudest
kus kasutasime võtit -A (*append*). Seda seetõttu, et viimane reegel
mille lisasime, käseb tulemüüril tagasi lükata kõik paketid ning 
peale seda lisatud reeglid ei oma enam tähtsust.

.. code:: bash

    sudo iptables \
             -I FORWARD \
             -i eth0 \
             -p tcp -d 192.168.3.100 --dport 22 \
             -j ACCEPT
             
Lisatud reegel kontrollib tuuma sissetuleva paketi võrguliidest, et see oleks
WAN, mitte LAN-liides; sihtaadressi 192.168.3.100; 
kapseldusprotokolli, et see oleks TCP ning sihtporti,
et see oleks 22.
Kui pakett vastab nendele tingimustele, võetakse ta vastu edastamise (*forward*) ahelasse.

Kui kõik on hästi siis keegi väljapool ei ole teadlik meie kohtvõrgu 
struktuurist ning isegi kui oleks, ei hakkaks ta igasse masinasse käsitsi
marsruutimisreegleid lisama.
Teenusele pöördutaks ikkagi marsruuteri avaliku aadressi kaudu
ning seetõttu peame lisama täiendava reegli sihtaadressi ning -pordi
ümberkirjutamiseks:

.. code:: bash

    sudo iptables \
             -I PREROUTING -t nat \
             -p tcp -i eth0 --dport 54622 \
             -j DNAT --to 192.168.3.100:22

Nii olemegi välisvõrku paistvalt IP-lt pordi 54622 suunanud sisevõrgus
asuva masina 192.168.3.100 pordile 22.

Harjutused:

* Paigalda mõnes sisevõrgus olevas masinas veebiserver (nt Apache) ning
  suuna port 80 marsruuteris edasi selle masina pordile 80
