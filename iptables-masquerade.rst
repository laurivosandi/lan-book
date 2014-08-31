.. title: Interneti jagamine
.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags: masquerade, iptables, internet sharing, conntrack, NAT

Interneti jagamine
==================

Sõltumata sellest, kas kohtvõrgu masinad saavad 
`IP automaatselt <dhcp-client.html>`_ või on need
`käsitsi paika pandud <iproute2-static-ip.html>`_ on marsruuteris vaja seadistada tulemüür nõnda, et
ta lüüsiks sisevõrgu ühendusi edasi Internetti.

.. figure:: dia/router-packet-flow-nat-simple.svg

    Pakettide töötlemine marsruuteris.

Vaikimisi on Linux-i tuumas välja lülitatud pakettide edastamine.
Masinast saab marsruuteri teha, kui pakettide edastamine sisse lülitada:

.. code:: bash

    echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
    sudo sysctl -p

Täiendavalt on vaja öelda tuumale, et väljuvatele pakettidele tehtaks
maskeraadi. See tähendab, et masinatest väljuvate IP-pakettide
lähte IP-aadressiks saab marsruuteri IP-aadress:

.. code:: bash

    # Tühjenda marsruutimisjärgne ahel
    sudo iptables -t nat -F POSTROUTING
    
    # WAN liideselt väljuvate pakettide lähte IP-aadressiks saab WAN-liidese IP-aadress
    sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE


Hetkeseisuga vahendab masin nüüd nii pakette sisevõrgust internetti kui ka vastupidi,
eeldusel et *forward* ahel on vaikimisi lubavas asendis.
Selleks et keelata internetist ligipääs sisevõrgule, tuleks lisada reegel,
missuguseid pakette lubatakse edastada ning ülejäänud paketid selgesõnaliselt
tagasi lükata:

.. code:: bash

    # Tühjenda edastamise ahel
    sudo iptables -F FORWARD

    # Luba ühendused LAN võrgust WAN võrku 
    sudo iptables -A FORWARD -o eth0 -i eth1 -s 192.168.3.0/24 -m conntrack --ctstate NEW -j ACCEPT
    
    # Luba eelnevalt loodud ühenduste sihtkohast tagasi tulevad paketid
    sudo iptables -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
    
    # Lükka tagasi kõik paketid eelnevatele tingimustele mittevastavad paketid
    sudo iptables -A FORWARD -j REJECT

Nii olemegi loonud marsruuteri *iptables* abil.
