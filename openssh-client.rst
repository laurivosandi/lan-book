.. title: OpenSSH klient
.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags: 
.. date: 2013-10-31


OpenSSH on võrgurakendus ning samas ka võrguprotokoll, mis võimaldab üle 
võrgu saada ligipääsu kaugmasina käsureale kui ka failidele.

.. image:: http://www.liberiangeek.net/wp-content/uploads/2013/04/network_sharing_ubuntu_windows_thumb.png

OpenSSH on integreeritud Ubuntu töölauda läbi GNOME virtuaalfailisüsteemi,
mis tähendab et Nautilus faililehitsejaga saab kaugmasina faile lehitseda:

.. image:: http://www.memset.com/media/medialibrary/2011/08/container-access.png

Ka paljud muud graafilised programmid, nt *gedit* jms toetavad sama aadressikuju
*sftp://lvosandi@enos.itcollege.ee/home/lvosandi*.

GNOME virtuaalfailisüsteemi omapäraks on ka see, et *legacy* rakenduste jaoks 
monteerib ta kaugresursi kohalikku failipuusse. Ubuntu 13.04-s on iga kasutaja
haakepunktid kättesaadavad */run/user/kohaliku_kasutajanimi/gvfs/* all:

.. code::

    lauri@lauri-t420 /run/user/lauri/gvfs/sftp:host=enos.itcollege.ee,user=lvosandi/home/lvosandi $ ls
    2riplaan_v6sax.html    biz.html     eee.odp		i202		jura		   loogika_aeg-1.xls  NX Client for Windows  public_html.tar  uus
    alu.epb		       bitchx.sh    erasmus		i202b		kaka		   Mallid	      pages		     pulss	      vana
    antti		       booze	    export		i202c		katse		   mata		      pask		     rss.xml	      whatnot.php
    antti2		       bs	    fibo.py		images		katse.htm	   matastat	      pic1.bmp		     scrollz.sh       Videod
    arhiveeri.bat	       cisco.txt    files		index.html	katse.py	   moo2.txt	      pic2.bmp		     server	      viki
    ariplaan_v6sax.html    _config.php  fix_encoding.patch	infot88tlus	kuhuiganes	   moo3.txt	      Pildid		     style.css	      workspace
    ariplaan_v6sax.html~   COPYING	    fyysika.pdf		info.txt	lauri_vosandi.xls  moo.txt	      plugins		     template.html    ylesanne1.ods
    arvutid3.epb	       Desktop	    harjutus123.java	java		local.cshrc	   Muusika	      pomm.pl		     Töölaud	      ylesanne1.xls
    arvutid_harjutus1.epb  Dokumendid   hinded.py		java_algkursus	local.login	   New Folder	      projekt1		     ubuntu	      ylesanne4
    Avalik		       dot	    history		java.html	local.profile	   nk.sh	      public_html	     usr
    lauri@lauri-t420 /run/user/lauri/gvfs/sftp:host=enos.itcollege.ee,user=lvosandi/home/lvosandi $ 

Ligipääsu kaugmasina **käsureale** saab läbi *ssh* rakenduse:

.. code::

    lauri@lauri-t420 ~ $ ssh lvosandi@enos.itcollege.ee
    lvosandi@enos.itcollege.ee's password: 
    Welcome to Ubuntu 12.04.2 LTS (GNU/Linux 3.2.0-51-generic x86_64)

     * Documentation:  https://help.ubuntu.com/

      System information as of Wed Aug 21 19:11:23 EEST 2013

      System load:  0.0                Processes:           141
      Usage of /:   12.5% of 42.26GB   Users logged in:     2
      Memory usage: 17%                IP address for eth1: 172.16.0.144
      Swap usage:   0%

      Graph this data and manage this system at https://landscape.canonical.com/

    3 packages can be updated.
    0 updates are security updates.

    *** System restart required ***
    Last login: Mon Jun 24 21:16:31 2013 from 196.65.50.84.dyn.estpak.ee
    lvosandi@enos:~$ 

Sülearvutiga ringi liikudes ning tihti sülearvutit uinakule pannes muutub tüütuks see,
et kui sülearvuti unest üles ärkab, on ta ilmselt juba uude võrku ennast sisse loginud
ning kõik lahtised SSH ühendused on pisut zombises asendis. Lõpptulemusena
on kaugmasina käsurida hangunud ning Nautiluses kataloogi sirvimine annab veateateid.
Selle vastu aitab üliagressiivsete seadete
panemine OpenSSH kliendi konfiguratsiooniks
failis *~/.ssh/config*:

.. code::

    Host *
        ServerAliveInterval 1
        ServerAliveCountMax 1

Täindavalt võib sinna lisada ka masinanime *alias*-eid:

.. code::

    Host e
        Hostname enos.itcollege.ee
        Username lvosandi

Käesolevas näites võime *sftp://lvosandi@enos.itcollege.ee* asendada reaga *sftp://e*
ning käsureal sisselogides *ssh* *lvosandi@enos.itcollege.ee* asemel sobib ka *ssh* *e*.

Mobiilse eluviisi juures tuleb kasuks programm nimega *mosh*, 
mis alustab OpenSSH ühenduse TCP kaudu, kuid seejärel läheb üle UDP peale.
Tulemuseks on see, et näiteks kolides 3G ühenduse pealt WiFi peale
ei tule uuesti serverisse sisse logida ning kui 3G ühenduse
puhul leviauku liikuda, näitab *mosh* mitu sekundit pole serveriga ühendust
olnud. Eriti mugavaks teeb *mosh*-i kohaliku kaja (*local* *echo*) lahendus,
mis tähendab et klahvivajutused on ajutiselt näha ka kohalikus terminalis, 
enne kui nad serverist läbi käivad.

Nii Ubuntu kui ka Debian tarkvarahoidlates on *mosh* olemas, rakendus tuleb paigaldada
nii serveris kui sülearvutis:

.. code:: bash

    sudo apt-get install mosh

