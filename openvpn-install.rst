.. author: Lauri Võsandi <lauri.vosandi@gmail.com>

OpenVPN paigaldamine
====================

OpenVPN on avatud lähtekoodiga tarkvara mis võimaldab luua virtuaalse
krüpteeritud võrgu üle avaliku ning turvamata ühenduse.

OpenVPN paigaldus Debian 7.1 (Wheezy) ning Ubuntu 12.04 LTS (Precise Pangolin) peal:

.. code:: bash

    sudo apt-get install openvpn
   
OpenVPN toetab TUN ning TAP virtuaalvõrgu seadmeid. TUN ja TAP seadmete
tugi on Linux-is ning FreeBSD-s olnud juba pikka aega. Windows 2000/XP/Vista/7/8
jaoks on tüürel ka olemas ning see on ka kaasas OpenVPN paketis Windows-ile.

* TUN (*network* *tunnel*) simuleerib võrgukihti luues virtuaalse ühenduse ühe teatud IP-ga. 
  TUN opereerib *layer 3* tasemel vahendades IP pakette.
  TUN režiimi kasutatakse üle turvamata ühenduse kahe arvuti ühendamiseks.
  Erinevate marsruutimismeetoditega saab üle TUN ühenduse siduda ka mitu võrku.

* TAP (*network* *tap*) simuleerib lingikihti luues ühenduse virtuaalse *switch*-i näol.
  TAP režiimi kasutatakse üle turvamata ühenduse mitme arvuti ühendamiseks.
  TAP on ka kõige tavalisem ja paindlikum meetod OpenVPN-iga virtuaalprivaatvõrgu
  loomiseks.

Esimses näites ühendame Zone pilveserveri ning sülearvuti virtuaalsesse võrku
OpenVPN abil. Kuna ühenduse krüpteerimist antud juhul ei kasutata, ei ole ka
järgnevat varianti soovitatav reaalselt kasutusele võtta. Eelduseks siinkohal
on see, et *gn29.zone.eu* pilveserveri pordile 1194 kasutades UDP protokolli
on ligipääs avalikust internetist.

OpenVPN serveri käivitamine krüpteeringuta, testimaks ühendust:

.. code:: bash

    sudo openvpn --local 192.168.6.198 --port 1194 --proto udp \
             --dev tun1 --ifconfig 192.168.55.1 192.168.55.2
  
OpenVPN kliendi käivitamine:

.. code:: bash

    sudo openvpn \
             --local 192.168.6.199 --port 1194 --proto udp \
             --remote 192.168.6.198 \
             --dev tun1 --ifconfig 192.168.55.2 192.168.55.1
   
Mõlemas arvutis peaks ilmuma *ifconfig* all nähtavale *tun1* võrguliides:

.. code::

    gn29.zone.eu ~ # ifconfig tun1
    tun1      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  
              inet aadress:192.168.55.1  P-t-P:192.168.55.2  mask:255.255.255.255
              UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1500 meetrika:1
              RX pakette:141405 vigu:0 ära visatud:0 ületäit:0 kaadri vigu:0
              TX pakette:141399 vigu:0 ära visatud:0 ületäit:0 carrier:0
              kollisioone:0 txqueuelen:100 
              RX baite:11881036 (11.3 MiB)  TX baite:11880052 (11.3 MiB)

Käesolevas variandis saab ühenduse korrasolekus hõlpsalt veenduda *ping* abil:

.. code::

    gn29.zone.eu ~ # ping 192.168.55.2
    PING 192.168.55.2 (192.168.55.2) 56(84) bytes of data.
    64 bytes from 192.168.55.2: icmp_req=1 ttl=64 time=4.02 ms
    64 bytes from 192.168.55.2: icmp_req=2 ttl=64 time=3.57 ms
    64 bytes from 192.168.55.2: icmp_req=3 ttl=64 time=3.62 ms
    64 bytes from 192.168.55.2: icmp_req=4 ttl=64 time=4.71 ms
    ^C
    --- 192.168.55.2 ping statistics ---
    4 packets transmitted, 4 received, 0% packet loss, time 3003ms
    rtt min/avg/max/mdev = 3.575/3.984/4.713/0.462 ms

Nii olemegi esimese virtuaalse võrgu loonud üle interneti kasutades OpenVPN-i.
Kõik OpenVPN võtmed saab tegelikult faili kirja panna, iga võti eraldi real ning
eemaldades võtme eest -- märgid.
Linuxi puhul loeb OpenVPN teenus seadistuste failid /etc/openvpn/seadistuse-nimi.ovpn failidest.
Windowsi OpenVPN klient loeb seadistused C:\\Program Files\\OpenVPN\\config\\seadistuse-nimi.ovpn failidest:

.. code::

     local 192.168.6.199
     port 1194
     proto udp
     remote 192.168.6.198
     dev tun1
     ifconfig 192.168.55.2 192.168.55.1
     
Sellisest konfiguratsioonifailist piisab, et ülal näidatud OpenVPN kliendi
võtmed faili kujul olemas oleks.

