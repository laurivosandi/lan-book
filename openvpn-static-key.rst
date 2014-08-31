.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. license: cc-by-3

OpenVPN võrgu loomine staatilise võtmega
========================================

Staatiline võti on kõige lihtsam moodus luua krüpteeritud võrk OpenVPN-iga.

Käesolevas näites ühendame Zone pilveserveri koduse OpenWrt baasil ruuteriga
ning võimaldame ligipääsu kodusele võrgule läbi OpenVPN ühenduse.


Alustame staatilse võtme genereerimisest serveris:

.. code:: bash

    /usr/sbin/openvpn --genkey --secret ~/pilveserver-kodu-sild.key

Järgnevalt kopeerime võtme marsruuterisse millest soovime ühendust luua
pilveserveriga:

.. code:: bash

    scp 217.146.67.29:~/pilveserver-kodu-sild.key /etc/openvpn/pilveserver-kodu-sild.key
   
Liigutame võtme nii pilveserveris turvalisemasse kohta:

.. code:: bash

    sudo mv ~/pilveserver-kodu-sild.key /etc/openvpn/
    sudo chown root:root /etc/openvpn/pilveserver-kodu-sild.key
    sudo chmod 700 /etc/openvpn/pilveserver-kodu-sild.key

Käivitame OpenVPN pilveserveris ning anname täiendava *--route* võtme, 
mis ütleb et 192.168.3.0/24 alamvõrk tuleks läbi selle TUN seadme marsruutida:

.. code:: bash

    openvpn \
        --proto tcp-server \
        --secret /etc/openvpn/pilveserver-kodu-sild.key \
        --ifconfig 192.168.44.1 192.168.44.2 \
        --route 192.168.3.0 255.255.255.0 \
        --dev tun2

Käivitame OpenVPN marsruuteril, siinkohal täiendav *--route* võti ütleb, et
koduses võrgust saab selle TUN ühenduse kaudu marsruutida 192.168.44.0/24
alamvõrku:

.. code:: bash

    openvpn \
        --proto tcp-client \
        --remote 217.146.67.29 \
        --secret /etc/openvpn/pilveserver-kodu-sild.key \
        --ifconfig 192.168.44.2 192.168.44.1 \
        --route 192.168.44.0 255.255.255.0 \
        --dev tun2


Lisame marsruuteris maskeraadi reeglid, mis ütlevad kuidas ruuter peaks
IP aadresse tõlkima kui need liiguvad 192.168.44.0/24 alamvõrgust
192.168.3.0/24 alamvõrku ning vastupidi:

.. code:: bash

    iptables -t nat -A POSTROUTING -s 192.168.3.0/24 -o tun2 -j MASQUERADE
    iptables -t nat -A POSTROUTING -s 192.168.44.0/24 -o br-lan -j MASQUERADE
    
Lisame *forward* reeglid, mis ütlevad missuguselt võrguliideselt missugusele
võrguliidesele marsruuter peaks pakette edastama:

.. code:: bash

    iptables -I FORWARD -o tun2 -i br-lan -j ACCEPT
    iptables -I FORWARD -i tun2 -o br-lan -j ACCEPT
    
Luba IPv4 pakettide edastamine üleüldse:

.. code:: bash

    echo 1 > /proc/sys/net/ipv4/ip_forward

Käesoleva seadistuse lõpptulemusena on kogu kodune võrk ligipääsetav Zone
pilveserverist ning ka vastupidi, kodusest võrgust on ligipääs pilveserveri
aadressile 192.168.44.1, millel võib käitada sääraseid teenuseid
(Samba failijagamine, PostgreSQL serveri vms) mida nii naljalt avalikku
internetti  ei julgeks panna.

Harjutused:

* Ühenda kaks alamvõrku kasutades OpenVPN-i staatilise võtmega
* Mugavuse huvides pane seadistused /etc/openvpn/silla-nimi.conf failidesse

