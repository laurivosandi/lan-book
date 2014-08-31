.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags: hidden

Koolituse läbiviijale
=====================

Esimene etapp
-------------

Esimeses osas lase koolitusel osalejatel paigaldada kolm virtuaalmasinat:

* Debian baasil marsruuter (debian-7.1.0-amd64-netinst.iso)
* Ubuntu baasil veebiserver (ubuntu-12.04.3-server-amd64.iso)
* Ubuntu baasil töölaud (ubuntu-13.04-desktop-amd64.iso)

Iga klass IT Kolledžis kuulub oma alamvõrku 192.168.1-8.0/24.
Igas klassis on kuni 30 masinat, mis saavad IP aadressi
alamvõrgu algusest. Näiteks ruumis 419 on IP aadressid 
192.168.3.1 kuni 192.168.3.29. Lüüsiks on alamvõrgu viimane masin, 
ehk siis antud juhul 192.168.3.254.
Kooli võrgus saavad DHCP abil IP-aadressi vaid heaks kiidetud MAC-aadressid.
Kõik muud masinad tuleb võrgu lülitada staatilise konfiguratsiooniga.

Lase võrk ümber seadistada VirtualBox-is:

* Marsruuteri esimene võrguliides viia üle *Bridged* *Adapter* režiimi
* Marsruuterile lisada teine võrguliides ning see viia *Internal* *Network* režiimi
* Veebiserveri masina võrguliides viia *Internal* *Network* režiimi
* Töölaua masina võrguliides viia *Internal* *Network* režiimi

Lase õppuritel tuvastada füüsilise masina võrguliidese IP-aadress.
Näiteks 419 ruumi masina 192.168.3.5 puhul lase virtuaalmasinad
seadistada järgnevalt:

* Marsruuteri esimene võrguliides staatiliste võrguseadistustega:
  IP-aadress 192.168.3.105, võrgumask 255.255.255.0, lüüs 192.168.3.254, nimeserver 8.8.8.8
* Marsruuteri teine võrguliides staatiliste võrguseadistustega:
  IP-aadress 192.168.105.1, võrgumask 255.255.255.0, lüüs puudub, nimeserver puudub
* Veebiserveri masina võrguliides staatilise võrguseadistustega:
  IP-aadress 192.168.105.2, võrgumask 255.255.255.0, lüüs 192.168.105.1, nimeserver 192.168.105.1
* Töölaua masin saab võrguseadistused DHCP-ga

Järgnevalt liigu edasi samm sammult:

* Lase paigaldada OpenSSH
* Lase seadistada OpenSSH võtmetega sisselogimine
* Näita ära kuidas OpenSSH kliendi seaded elu mugavamaks teevad
* Näita ära *mosh* ning autossh* võimekus, eriti just 3G ühenduse seisukohast
* Lase seadistada pakettide edastamine ning maskeraad
* Näita ära kuidas *mtr*-i kasutada pakettide edastamise silumiseks
* Lase paigaldada ja seadistada dnsmasq
* Lase piirata ligipääs marsruuteri võrguteenustele võrguliidese järgi
* Näita ära kuidas nmap-i kasutada tulemüüri silumiseks
* Lase paigaldada veebiserveri virtuaalmasinasse Apache2
* Lase seadistada port 80 edasisuunamine veebiserverile

Teine etapp
-----------

Kui igal õppuril on oma sisevõrk korras ning seadistatud
jaota nad paaridesse ning lase neil oma sisevõrgud ühendada OpenVPN-i abil.

* Lase paigaldada OpenVPN
* Lase mõlemal paarilisel testida OpenVPN-i TUN-režiimis võtmeteta
* Lase ühel paarilisel genereerida staatiline võti ning see teise paarilise marsruuteri virtuaalmasinasse kopeerida
* Lase tunnel ümber seadistada kasutades staatilist võtit
* Lase seadistada marsruutimine nii et paarilise sisevõrk oleks marsruuditud
  läbi OpenVPN tunneli
* Lase seadistada pakettide edastamine tunneli võrguliidese ning kohtvõrgu võrguliideste vahel

Kolmas etapp
------------

Siit edasi võib iga õppur oma sülearvuti liita marsruuteri külge:

* Räägi avalik/privaatne krüptograafiast,
  võtmevahetusalgoritmidest, sümmeetriliste sihvrite jõudlusest,
  CA-sertifikaatide hierarhiast
* Lase õppuritel sülearvutisse, mobiiltelefoni vms paigaldada OpenVPN
* Lase igal õppuril luua oma marsruuterisse CA-sertifikaat ning
  serveri võti/sertiikaat
* Lase seadistada marsruuterisse teine OpenVPN instants TAP-režiimis
  ning koos DHCP-serveriga
* Lase igal õppuril luua oma seadme jaoks võti/sertifikaat ning see vastavasse
  seadmesse kopeerida
* Lase seadistada OpenVPN seadmel

Kui ka nüüd sülearvutid on liidetud marsruuterite külge võib liikuda edasi 
selle juurde, et OpenVPN-i TAP-režiimi masinad ning sisevõrgu masinad oleks
ühes alamvõrgus:

* Lase keelata DHCP server TAP-režiimi OpenVPN instantsil
* Lase keelata *ifconfig* direktiiv TAP-režiimi OpenVPN instantsil
* Lase marsruuteri teisel võrguliidesel (*eth1*) keelata IP-aadress
* Lase luua silla võrguliides (*br0*) ning sellesse liita sisevõrgu liides (*eth1*)
* Lase *dnsmasq* ümber seadistada kuulama silla võrguliidesel (*br0*)
* Lase lisada *up* ja *down* direktiivid OpenVPN konfiguratsiooni, et
  TAP-võrguliidese üles toomisel liidetaks ta silla koosseisu
* Too välja tulemüüri skriptitavuse plussid-miinused (eth0 -> br0 näitel)

Silumine
--------

Viimases etapis silume võrguseadistusi veelgi:

* Tee kokkuvõte seni räägitust ja realiseeritust
* Räägi pakettide markeerimisest ning *connmark* moodulist
* Räägi kasutaja defineeritud tulemüüri ahelatest,
  blacklist/whitelist-idest ning nende skriptitavusest *captive* *portal* näitel
* Räägi ühenduste klassifitseerimisest, arvu piiramisest, ribalaiuse piiramisest, prioritiseerimisest
* Lase rakendada kiirusepiirangud ning prioriteedid marsruuteri interneti ning 
  sisevõrgu/silla võrguliidestel

Kontroll
--------

* Kas avalikust võrgust pääseb sisevõrku kui lisada vastav marsruutimisreegel?
* Kas avalikust võrgust pääseb ligi ainult lubatud teenustele?
* Kas sisevõrgust pääseb internetti?
* Kas erinevate alamvõrkude masinad näevad üksteist mille marsruuterid on
  tunneliga ühendatud?
* Kas kiirusepiirangud rakenduvad?

Muu
---

Vuugitäide:

* Räägi `kinnise lähtekoodiga võrgukaartide püsivarast ning
  nendega seotud potentsiaalsetest riskidest
  <http://esec-lab.sogeti.com/post/2010/11/21/Presentation-at-Hack.lu-%3A-Reversing-the-Broacom-NetExtreme-s-firmware>`_
* Räägi `kinnise lähtekoodiga võrguseadmetega seotud potensiaalsetest riskidest
  <http://www.theregister.co.uk/2013/07/11/hp_prepping_fix_for_latest_storage_vuln/>`_
* Räägi `GSM modemite turvalisusest <http://www.osnews.com/story/27416/The_second_operating_system_hiding_in_every_mobile_phone>`_
* Riistvaralised AES-128 kiirendid

Muud nipid:

* Kontrolli, et BIOS-es on virtualiseerimislaiendused sisse lülitatud kui
  VirtualBox-i virtuaalmasin hangub algkäivitamisel.
* Kontrolli, et VirtualBox-i võrguliides oleks *promiscuous* režiimis, kui
  püüad võrguliideseid sillata.
* Võimalik, et VirtualBoxi võrguliideste režiimide muutmisel tuleb
  virtuaalmasinale taaskäivitus teha selleks et teatud spetsiifilised muudatused rakenduks
* Host-only adapteri sisse lülitamisel tuleb VirtualBoxi teenustele taaskäivitus teha,
  administraatori õiguste puudumisel tuleb kogu alusmasinale taaskäivitus teha sel juhul


