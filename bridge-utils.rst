.. title: Võrguliideste sildamine
.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags:  bridge-utils, interfaces
.. date: 2013-10-31

Võrguliideste sildamine
=======================

Võrguliideste sildamine (*bridging network interfaces*)
tähendab sisuliselt nende liitmist virtuaalsesse
kommutaatorisse (*switch*). Sild ise on samas ka virtuaalne võrguliides mis on justkui
ühendatud samasse virtuaalsesse kommutaatorisse. Füüsilised võrguliidesed,
mis on silda liidetud on ilma IP-aadressita.

.. image:: img/bridge-utils.png
    :width: 600px
    :height: 333px
    :align: center

Käesolevas näites on marsruuteris kolm füüsilist võrguliidest:

* *eth0* - Avalikku internetti ligipääs
* *eth1* - Juhtmega kohtvõrk
* *wlan0* - Juhtmeta kohtvõrk

Eeldused:

* *hostapd* on juba käivitatud *wlan0* võrguliidesel

Kuna me soovime kohtvõrgus käitada ühte DHCP serverit ning liita juhtmeta ning
juhtmega kohtvõrgu masinad ühte alamvõrku on kõige lihtsam viis seda teha
sillates *eth1* ning *ath0* võrguliidesed.

Alustame silla loomisest:

.. code:: bash

    # Loo sild br0
    brctl addbr br0

Nagu näha ilmub peale seda ka sillale vastav virtuaalne võrguliides suvaliselt
genereeritud MAC-aadressiga:

.. code::

    marsruuter ~ # ip link show dev br0
    5: br0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN 
        link/ether 0e:f6:34:c5:60:93 brd ff:ff:ff:ff:ff:ff

Järgnevalt liidame füüsilised võrguliidesed silda:

.. code:: bash

    # Lisa võrguliidesed eth1 ning wlan0 silla br0 koosseisu
    brctl addif br0 eth1
    brctl addif br0 wlan0

Olemasolevast sildade konfiguratsioonist saab ülevaate *brctl* *show* käsuga:

.. code::

    marsruuter ~ # brctl show
    bridge name        bridge id                STP enabled        interfaces
    br0                8000.6466b302bf91        no                 eth1
                                                                   wlan0

Hetkeseisuga pole IP-aadressi määratud *br0* võrguliidesel ning
on võimalik, et *eth1* ning *wlan0* võrguliidestel on juba IP-aadress määratud.

.. code:: bash

    # Eemaldame kõik IP-aadressid võrguliidestelt
    ip addr flush dev br0
    ip addr flush dev eth1
    ip addr flush dev wlan0

    # Lisame silla võrguliidesele IP-aadressi 192.168.5.1 alamvõrgus 255.255.255.0
    ip addr add 192.168.5.1/24 dev br0

Nagu ikka, ei ela need muudatused taaskäivitust üle. Debian/Ubuntu puhul
tuleks sillaga seotud võrguliideste kohta vastavad sissekanded teha
*/etc/network/interfaces* faili:

.. code:: bash

    # Võrguliidesed eth1 ning wlan0 seadistatakse käsitsi, selleks et NetworkManager vms ei sekkuks
    iface eth1 inet manual
    iface wlan0 inet manual

    # Silla seadistused
    auto br0
    iface br0 inet static
        address 192.168.5.1
        netmask 255.255.255.0
        bridge_ports eth1 wlan0

Nii olemegi seadistanud silla, mis ühendab marsruuteri läbi *br0* liidese, 
juhtmega võrgu läbi *eth1* liidese ning juhtmeta võrgu läbi *wlan0* liidese.
Siit võib edasi minna `DHCP serveri seadistamise </bootcamp/dnsmasq>`_ juurde,
ainus erinevus on see, et DHCP server peab kuulama võrguliidesel *br0*.
`Maskeraadi tegemisel </bootcamp/iptables-masquerade>`_ on erinevus analoogne, pakette vahendatakse
võrguliideselt *br0* võrguliidesele *eth0*.
