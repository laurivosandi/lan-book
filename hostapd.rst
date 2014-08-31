.. title: Juhtmeta kuumpunkti loomine
.. author: Lauri võsandi <lauri.vosandi@gmail.com>
.. license: cc-by-3
.. date: 2014-04-16
.. tags: hostapd, WPA, DHCP, access point, kuumpunkt, hotspot

Juhtmeta kuumpunkti loomine
===========================

Sissejuhatus
------------

Juhtmeta võrgus võib masin olla ühes kolmest režiimist:

* Hallatud (*managed* *mode*)
* Haldur (*master* *mode*, *access* *point* *mode*)
* Automaatselt seadistatud (*ad-hoc*)

Tüüpiliselt on sülearvuti hallatud (*managed* *mode*) režiimis ning 
pääsupunkt (*access* *point*) võrgu haldaja rollis (*master* *mode*).

Vanasti võimaldas *iwconfig* muuda režiimi, kuid *cfg80211/nl80211* ehk Linux-i
802.11 seadistamise liidesega see enam ei toimi.
Esiteks on võrgu krüpteerimise seadistused on märgatavalt keerulisemaks läinud
ning nende laadimiseks kasutatakse *wpasupplicant*-i ning
võrgukaardi pääsupunkti režiimi viimiseks ning masinate autentimiseks 
võrgus kasutatakse *hostapd* deemonit.

Võrgukaartide kiibistikud
-------------------------

Juhtmeta võrgukaartide kiibistikke on laias laastus neljalt tootjalt:

* Atheros - Avatud lähtekoodiga tüürelid ning püsivara, kuumpunkt võimalik
* Intel - Avatud lähtekoodiga tüürelid, kinnise lähtekoodiga püsivara
* Ralink/Mediatek - Avatud lähtekoodiga tüürelid, kinnise lähtekoodiga püsivara, kuumpunkt võimalik
* Broadcom - Tüüreleid on mitmeid, kuna tootja ei tee koostööd kommuuniga
* Realtek - Avatud lähtekoodiga tüürelid, püsivara tavaliselt kirjutatud juba tehases

Kuumpunkti tarkvara paigaldamine
--------------------------------

Suvalisest masinast kus on Atheros või Ralink kiibistikuga juhtmeta võrgukaart
saab luua juhtmeta pääsupunkti kasutades *hostapd* tarkvara:

.. code:: bash

    apt-get install hostapd

Ubuntu puhul muudame failis */etc/default/hostapd* ära rea mis viitab
*hostapd* seadistuste failile:

.. code:: bash

    sed -i -r 's/#?DAEMON_CONF=".*"/DAEMON_CONF="\/etc\/hostapd\/hostapd.conf"/g' /etc/default/hostapd

Lisame */etc/default/hostapd* faili järgnevad seadistused:

.. code:: ini

    # Kuulame võrguliidesel wlan0
    interface=wlan0

    # Kasutame nl80211 liidest tuumaga suhtlemisel
    driver=nl80211

    # Võrgunimi
    ssid=lauri-hackerspace

    # Kuulame kanalil 6 ehk 2.437 GHz
    channel=6

    # Kasutame WPA2 krüptot
    wpa=2
    wpa_passphrase=salakala

Viimane samm on taaskäivitada *hostapd*:

.. code:: bash

    service hostapd restart

Kui *wlan0* võrguliides on ainus, mis kuulub sisevõrku, siis piisab sellest, 
et `DHCP server seadistada <dnsmasq.html>`_ kuulama võrguliidesel *wlan0*.

Kui sisevõrku kuulub ühes arvutis mitu võrguliidest, nt juhtmega võrk, 
juhtmeta võrk või mõni virtualiseeritud võrguliides, on mõistlik nad kõigepealt
`sillata üheks võrguliideseks <bridge-utils.html>`_.

