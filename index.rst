.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. license: cc-by-3
.. tags: iptables, training, netfilter, OpenVPN, ESF, küberkaitse

Kohtvõrgu rajamine ja turvamine kasutades vabasid vahendeid
===========================================================

Sissejuhatus
------------

Käesolev õppematerjal käsitleb kohtvõrgu rajamist ning turvamist kasutades
Linux-põhiseid operatsioonisüsteemie (Ubuntu, Debian, jms).
Tulemüüri vahenditest on kaetud *netfilter* ja *iptables*.
Sisevõrgu DHCP-serverina ning DNS-vahemäluna on välja toodud *dnsmasq*.
Räägitakse võrguliideste sildamisest *bridge-utils* abil.
Kaughalduse teemas puudutakse põgusalt OpenSSH-d.

Virtuaalprivaatvõrkude teemas kaetakse tunneldamine alamvõrkude
vahel kasutades OpenVPN-i TUN-režiimi, virtuaalse kommutaatori loomist
OpenVPN-i TAP-režiimi abil ning ka pordi edasisuunamist OpenSSH abil.
Erinevates vahendites kasutatava krüptograafilise võimekuse võtab kokku
OpenSSL.

Õppematerjalid kättesaadavad siinsamas veebis [#lanweb]_
Creative Commons Attribution 3.0 [#cc]_ litsentsi all.
Materjalid valmisid Euroopa Struktuurifondide toel
"Praktiline küberkaitse IT süsteemide administraatoritele" projekti raames [#esf]_.
Koolituse läbi viimiseks on juhend saadaval [#readme]_ käesoleva materjali
täiendamine on teretulnud Github vahendusel [#lanbook]_.

.. [#lanweb]  http://lauri.vosandi.com/lan/
.. [#cc]      http://creativecommons.org/licenses/by/3.0/
.. [#esf]     http://www.itcollege.ee/koostoo/projektid/projektid/kuberkaitse/
.. [#readme]  `Koolitaja juhend <readme.html>`_
.. [#lanbook] http://github.com/v6sa/lan-book/


Esialgne topoloogia
-------------------

Alustame minimaalse võrgutopoloogiaga kuhu on lülitatud:
Debianipõhine marsruuter (*router*) vahendab Internetist pakette
kohtvõrku ning vastupidi.
Ubuntupõhine veebiserver (*web* *server*) teenindab võrgurakenduse kasutajaid.
Kommutaator (*switch*) vahendab pakette kohtvõrgu masinate vahel:

.. figure:: dia/topology-basic.svg
    :width: 60%

    Minimaalne võrgu topoloogia

Kasutame Debian või Ubuntu põhiseid masinaid, sest mõlema puhul on tegu
avatud lähtekoodiga ning vaba tarkvaraga. Debiani näol on tegemist
ühe kõige vanema ning küpsema GNU/Linux tarkvara kogumikuga (*distribution*).
Ubuntu puhul on tegemist Debiani edasiarendusega, mis on mitmeid aastaid
juba kõige populaarsem valik tavakasutajate seas säilitades seejuures
suures osas ühilduvuse Debianiga.

Koodinäited eeldavad, et on paigaldatud võrdlemisi hiline Linux-i tuuma (*kernel*)
väljalase ning *iproute2*. Debian 7.0 (Wheezy) ning Ubuntu 12.04 LTS (Precise Pangolin)
peal on koodinäited ka testitud. Sobiv peaks ka olema 
mitmesugustele marsruuteri karbitoodetele pakendatud tarkvara OpenWrt,
küll aga tuleks tähelepanu pöörata sellele, et näiteks OpenWrt tulemüürireeglid
on paigutatud eraldi failidesse, omamoodi süntaksiga.

Enne OpenVPN tunnelite seadistamist tuleks käima saada:

* Marsruuteri masin peaks sisevõrgule IP-sid välja jagama DHCP-ga
* Marsruuter peaks oskama lahendada domeeninimesid
* Töölauamasin peaks aadressi küsima DHCP abil
* Veebiserverile anda staatiline IP aadress
* Marsruuterisse peaks saama sisse logida paroolita
* Välisvõrgust tulles marsruuteri avalikule pordile 80 suunataks paketid edasi veebiserverisse
* DNS ja DHCP teenused on nähtavad ainult sisevõrgust

Abiks on järgmine lugemismaterjal:

* `Võrguteenused <osi-model.html>`_ ehk OSI mudel
* `iproute2 <iproute2-introduction.html>`_ ehk Linux-i võrguliideste seadistamine.
* Masinale `staatilise IP-aadressi <iproute2-static-ip.html>`_ seadistamine.
* Masinale `dünaamilise IP-aadressi <dhcp-client.html>`_ seadistamine.
* `OpenSSH <openssh-client.html>`_ ehk kaughaldus
* `dnsmasq <dnsmasq.html>`_ ehk kaks-ühes DHCP ja DNS server
* `Asjalikke tööriistu võrgu silumiseks <useful-tools.html>`_

Linuxilistes on tulemüür realiseeritud kahe komponendina.
Kerneli koosseisu kuuluva osa nimeks on netfilter ning
Käsurearakendiks on iptables mis manipuleerib kerneli käsutuses olevaid reegleid.
Käesoleva materjali valimimisel olid abiks mitmed materjalid. [#kuutorvaja]_ [#lartc]_

* `iptables <iptables-introduction.html>`_ ehk *layer3* tulemüür
* `Maskeraad <iptables-masquerade.html>`_ ehk interneti jagamine
* `Kohalike rakenduste turvamine <iptables-local-applications.html>`_
* `Teenuste ümbersuunamine <iptables-port-redirection.html>`_
* `Pakettide markeerimine <iptables-mark.html>`_
* `Kasutaja defineeritud tulemüüri ahelad <iptables-user-defined-chain.html>`_
* `ebtables <ebtables.html>`_ ehk *layer2* tulemüür
* `Üldine loogika <https://www.dropbox.com/s/qu3ds44704nsw2n/2013-08-27%2011.57.46.jpg>`_

.. comment: * `Minimaalne marsruuteri tulemüür </paste/minimal-router-firewall.sh>`_

.. [#kuutorvaja] `iptables puust ja punaseks <http://kuutorvaja.eenet.ee/wiki/Iptables_puust_ja_punaseks>`_
.. [#lartc] `Linux Advanced Routing & Traffic Control <http://www.lartc.org/>`_


Site-to-site tunnel
-------------------

Kui sisevõrk toimib on aeg seadistada OpenVPN tunnel kahe marsruuteri vahele
nii et kahe sisevõrgu vaheline liiklus liigub läbi krüpteeritud tunneli:

.. figure:: dia/topology-site-to-site.svg

    Järgmine samm topoloogia arenduses
    
Abiks on järgnev:

* `VPN-idest üldiselt <virtual-private-network.html>`_
* `Erinevad VPN-i loomise variandid <https://www.dropbox.com/s/ze2zgiluucjl6nm/2013-08-27%2016.25.35.jpg>`_.
* `OpenVPN paigaldus <openvpn-install.html>`_.
* `Kahe alamvõrgu ühendamine <openvpn-static-key.html>`_ kasutades OpenVPN-i ja staatilist võtit.


Sülearvutite ühendamine
-----------------------

Järgmine samm on seadistada teine OpenVPN-i instants pakkuma rändlusteenust
sülearvutitele ning paika panna kiirusepiirangud:
    
.. figure:: dia/topology-laptops.svg

    Kõik töötab!
    
Abiks on jällegi mõned lingid:

* `Sülearvutite ühendamine <openvpn-easyrsa.html>`_ virtuaalprivaatvõrku kasutades OpenVPN-i ning avalik/privaatne võtmepaare.
* `TAP-režiimis OpenVPN-i ja füüsilise võrguliidese sildamine <http://www.serverubuntu.it/openvpn-bridge-configuration>`_.
* Mitme masina ja alamvõrgu ühendamine `PeerVPN <peervpn.html>`_ abil.
* `tc <traffic-control.html>`_ ehk võrguliikluse kujundamine.

.. comment: * `Kõik ühes OpenVPN konfiguratsioonifail </paste/inline-keys.ovpn>`_.

Kokkuvõte
---------

Koolitusel käsitletud marsruutimise ja kiiruse piiramise teemad võtab kokku järgnev joonis:

.. figure:: dia/router-packet-flow-nat.svg

    Pakettide töötlemine marsruuteris


Misc
----

.. comment: * `Näide #1 </paste/firewall.sh>`_
.. comment: * `Näide #2 </paste/firewall2.sh>`_
* `hostapd <hostapd.html>`_ ehk juhtmeta kuumpunkti rajamine
* `bridge-utils <bridge-utils.html>`_ ehk võrguliideste sildamine
* `nmap <nmap.html>`_ ehk võrgu skanneerimine
* `Packet flow <http://blog.schaal-24.de/wp-content/uploads/2013/08/2683-PacketFlow.png>`_
* `iproute commands <http://jazstudios.blogspot.com/2007/04/iproute-commands.html>`_

