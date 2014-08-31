.. title: Virtuaalprivaatvõrgud
.. tags: iptables, OpenVPN, PeerVPN, n2n, security, privacy
.. date: 2013-10-31

Virtuaalprivaatvõrgud
=====================

Sissejuhatus
------------

Sama firma kahes erinevas hoones asuva võrgu ühendamiseks oleks kõige turvalisem 
tõmmata kahe hoone vahele valguskaabel. Tihtipeale see on võimatu või liiga kallis,
seetõttu kasutatakse internetti, et need võrgud ühendada.
Üle Interneti krüpteerimata pakettide saatmine võib ohtlikuks osutuda,
seetõttu on võrsunud mitmeid tarkvaraprojekte, et seda turvaliselt teha.
Sel moel üle Interneti sillatud kohtvõrke nimetataksegi virtuaalprivaatvõrguks
(*virtual* *private* *network*).

Avatud lähtekoodiga VPN tarkvara
--------------------------------

Virtuaalprivaatvõrke saab luua erinevate avatud lähtekoodiga tehnoloogiate abil:

* `OpenVPN <http://openvpn.net/>`_ on kõige laialdasemalt kasutuses olev
  layer2/3 VPN lahendus.
* `OpenSSH <http://www.openssh.com/>`_ võimaldab tunneldada TCP ühendusi (-L ja -R)
  ning ka IP-pakette (-w).
* `IPsec <http://en.wikipedia.org/wiki/IPsec>`_ on IP-paketi krüpteerimisel
  tuginev standard mida toetavad mitmed võrguseadmed kuid seda on keerukam
  üles seada.
* `Point-to-Point Tunneling Protocol <http://en.wikipedia.org/wiki/Point-to-Point_Tunneling_Protocol>`_
  on nüüdseks ebaturvaliseks tunnistatud IP-pakettide tunneldamise protokoll.
* `CIPE <http://sites.inka.de/~W1011/devel/cipe.html>`_ on ajaloolise väärtusega ning nüüdseks ebaturvaliseks tunnistatud projekt
* `vtun <http://vtun.sourceforge.net/>`_ on laialdaselt kasutusel ent mitte väga turvaline
* `OpenConnect <http://www.infradead.org/openconnect/>`_.

Peter Gutmann on kirjutanud Linuxi ja VPN-ide kohta 
`pika postituse aastal 2003 <https://www.cs.auckland.ac.nz/~pgut001/pubs/linux_vpn.txt>`_
mis tasub kindlasti läbi lugeda

P2P VPN tarkvara
----------------

Tüüpilise VPN tarkvara seadistamine võib valuliseks muutuda kui masinaid on palju
mis on vaja omavahel ühendada ning liikluse lüüsimine läbi ühe keskse masina ei tule 
kõne alla.
Seetõttu on välja töötatud mitmeid VPN lahendusi mis Skype eeskujul püüavad
leida selle kõige otsema tee. 

Probleemid mida P2P VPN lahendused addresseerivad:

* Keskse pudelikaela moodustumine
* Ruuter maskeerib aadressi ehk NAT
* Ruuter ei võimalda avada avalikku kuulavat porti
* Mitmetasemeline NAT

P2P lahendused on kõik üldjuhul *layer2* VPN-id:

* `tinc <http://www.tinc-vpn.org/>`_
* `PeerVPN <peervpn.html>`_ kõige lihtsamalt seadistatav P2P VPN lahendus
  mis kasutab AES-256 sihvrit liikluse krüpteerimiseks osapoolte vahel ja
  võrgunime ning parooli sessiooni alustamiseks osapoolte vahel.
* `n2n <n2n.html>`_ on sisuliselt PeerVPN, ainus erinevus on see et
  *supernode* käitatakse eraldi programmina.g
* `Campagnol <http://campagnol.sourceforge.net/>`_ on prantsuse päritolu
  hajutatud IP-põhine VPN,   kasutab transpordina UDP-d,
  turvamiseks `DTLS <http://en.wikipedia.org/wiki/Datagram_Transport_Layer_Security>`_-i ning
  võimaldab NAT-ist mööduda `UDP hole punching <http://en.wikipedia.org/wiki/UDP_hole_punching>`_ tehnoloogia abil.
* `badvpn <https://code.google.com/p/badvpn/>`_ on multiedastus (*multicast*) toega VPN lahendus.

OpenWrt ning Debian varamutes on reaalselt kasutuskõlblikud OpenVPN ning PeerVPN.


Viited
------

* `VPN over SSH - ArchWiki <https://wiki.archlinux.org/index.php/VPN_over_SSH>`_
