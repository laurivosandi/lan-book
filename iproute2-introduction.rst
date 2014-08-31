.. title: iproute2
.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags:  iproute2, ifconfig
.. date: 2013-10-31

iproute2
========

Olles mananud ette käsurea, saab modernses Linux tuumaga masinas
manipuleerida võrguseadistusi *iproute2* tarkvara abil. Teemade skoop, mida
*iproute* katab on võrdlemisi suur:

* Saadaolevad võrguliidesed ning nende olek
* Võrguliidestega seotud loogilised aadressid (*IPv4*, *IPv6*)
* Naabrid ning nende MAC-aadressid
* Marsruutimistabel
* Virtuaalvõrgud (*VLAN*)
* Tunnelid
* Multicast
* Statistika
  
Alustame kõige lihtsamast küsimusest, missugused võrguliidesed on minu masinal?
Vasteks saame nimekirja liidestest ning nende riistvara aadressidest
(*hardware* *address*; *MAC*-*address* ehk *media* *access* *control* *address*):

.. code::

    localhost ~ # ip link list
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN 
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether c8:60:00:87:9b:94 brd ff:ff:ff:ff:ff:ff
    3: wlan0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
        link/ether 74:2f:68:87:18:da brd ff:ff:ff:ff:ff:ff

Käsk *ip* erineb vanakooli *ifconfig* käsust mitmeti, eraldades
IP-aadressi konseptsiooni võrguliidesest.
Seda sel põhjusel, et võrguliidesel võib olla defineeritud mitmeid
aadressitüüpe, nagu IPv4 või IPv6 või ka mitu sama tüüpi aadressi,
sel juhul nimetatakse neid *alias*-teks.
   
.. code::

    localhost ~ # ip address list
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN 
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        inet6 ::1/128 scope host 
           valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether c8:60:00:87:9b:94 brd ff:ff:ff:ff:ff:ff
        inet 192.168.3.2/24 brd 192.168.3.255 scope global eth0
        inet6 fe80::ca60:ff:fe87:9b94/64 scope link 
           valid_lft forever preferred_lft forever
    3: wlan0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
        link/ether 74:2f:68:87:18:da brd ff:ff:ff:ff:ff:ff

Käsu *ip* abil saab ligipääsu ka tuuma (*kernel*) marsruutimistabelile
(*routing* *table*).

.. code::

    localhost ~ # ip route list
    default via 192.168.3.1 dev eth0  metric 100 
    169.254.0.0/16 dev eth0  scope link  metric 1000 
    192.168.3.0/24 dev eth0  proto kernel  scope link  src 192.168.3.2
    
Marsruutimistabeli järgi teab tuum, mis alamvõrgu pakette missugusel 
võrguliidesel kuulama peab ning missuguselt võrguliideselt välja saata
millise alamvõrgu pakette. Kui sobivat alamvõrku ei leita marsruutimistabelist, 
siis need paketid edastatakse lüüsile (*gateway*). Marsruutimistabelis on
lüüs märgitud alamvõrguga 0.0.0.0 või *default*.

