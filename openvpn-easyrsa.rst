.. author: Lauri Võsandi <lauri.vosandi@gmail.com>

OpenVPN võrgu loomine privaatne/avalik võtmepaaridega
=====================================================

Unix-laadsetes operatsioonisüsteemides hoiab OpenVPN oma seadistusi failides kujul
/etc/openvpn/paigalduse-nimi.conf ning neid võib selles kataloogis olla mitu.

Käesolevas näites genereerime CA (*Certificate* *Authority*) võtme,
genereerime serveri ning klientide võtmepaarid,
signeerime CA abil serveri ja terve hunniku klientide sertifikaadid.

Alustame *easyrsa* skriptide kopeerimisest, tegu on sisuliselt väga algelise
*openssl* kasutajaliidesega:

.. code:: bash

    sudo mkdir /etc/openvpn/easy-rsa/ 
    sudo cp -R /usr/share/doc/openvpn/examples/easy-rsa/2.0/* /etc/openvpn/easy-rsa/
    cd /etc/openvpn/easy-rsa

Avame faili /etc/openvpn/easy-rsa/vars ning lisame faili lõppu sertifikaatide info:

.. code:: bash

    export KEY_COUNTRY="ee"
    export KEY_PROVINCE="Harjumaa"
    export KEY_CITY="Tallinn"
    export KEY_ORG="Koodur OÜ"
    export KEY_EMAIL="lauri.vosandi@gmail.com"

Siit edaspidi peab alati *vars* muutujate sisu olema kättesaadav,
selleks puhuks käivitage järgnev käsk alati kui avate uue terminaliakna vms:

.. code:: bash

    cd /etc/openvpn/easy-rsa
    source ./vars

Esmalt käivitame clean-all skripti, mis muuseas **kustutab** */etc/openvpn/easy-rsa/keys* seest kõik võtmed:

.. code:: bash

    ./clean-all

Järgnevalt loome Diffie-Hellman parameetrite jaoks faili *dh1024.pem* kataloogi */etc/openvpn/keys*:

.. code:: bash

    ./build-dh

Järgnev käsk loob CA (*certificate* *authority*) privaatse võtme failis *ca.key* ning sertifikaadi failis *ca.crt*:

.. code:: bash

    export KEY_CN="ca"
    export KEY_NAME="Koodur Certificate Authority"
    export KEY_OU="Koodur Certificate Department"
    ./pkitool --initca

Järgnev käsk loob OpenVPN serveri võtmepaari failides *server.key* ning *server.crt*:

.. code:: bash

    export KEY_CN="server"
    export KEY_NAME="Koodur OpenVPN server"
    export KEY_OU="Koodur Certificate Department"
    ./pkitool --server server

Igale kliendile saame võtmepaari luua analoogselt:

.. code:: bash

    export KEY_CN="lauri-t420"
    export KEY_NAME="Lauri Lenovo Thinkpad T420"
    export KEY_OU="Koodur Certificate Department"
    ./pkitool lauri-t420

Transpordi võtmed ning CA sertifikaat klientide masinatesse ning seadista
kõigepealt OpenVPN serveri konfiguratsioon */etc/openvpn/server.conf*:

.. code:: bash

    # Töötame OpenVPN serveri režiimis
    mode server
    tls-server

    # Kuula järgneval IP aadressil ning UDP pordiga
    local 217.146.67.29
    port 1194
    proto udp

    # Loo tap0 võrguliides
    dev tap0

    # Võtmete ja sertifikaatide asukohad
    ca /etc/openvpn/easy-rsa/keys/ca.crt
    key /etc/openvpn/easy-rsa/keys/server.key
    cert /etc/openvpn/easy-rsa/keys/server.crt
    dh /etc/openvpn/easy-rsa/keys/dh1024.pem

    # Luba tihendamine
    comp-lzo

    # Käita privilegeerimata kasutajana
    user nobody
    group nogroup

    # DHCP serveri seadistused
    ifconfig-pool-persist /tmp/openvpn-leases.txt
    ifconfig 192.168.33.10 255.255.255.0
    server-bridge 192.168.33.10 255.255.255.0 192.168.33.100 192.168.33.110
    max-clients 10

Seejärel klientide konfiguratsioon */etc/openvpn/klient.conf*:

.. code:: bash

    # Tegu on kliendiga TAP-režiimis
    client
    dev tap0

    # Kaugmasina IP-aadress ja port
    remote 217.146.67.29
    port 1194
    nobind
    persist-key
    persist-tun

    # Sertifikaatide ning kliendi võtme asukoht
    ca /etc/openvpn/keys/ca.crt
    cert /etc/openvpn/keys/lauri-t420.crt
    key /etc/openvpn/keys/lauri-t420.key

    # Kasuta Blowfish sihvrit
    cipher BF-CBC

    # Luba tihendamine
    comp-lzo

    # Ole jutukas
    verb 3

Konfiguratsioone saab testida nii:

.. code:: bash

    openvpn --config /etc/config/server-või-klient.conf

Käima saab mõlemad konfiguratsioonid tõmmata *service* käsuga:

.. code:: bash

    service openvpn restart
