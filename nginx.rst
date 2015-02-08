.. title: Nginx seadistamine
.. tags: 
.. date: 2013-10-31

Nginx seadistamine
==================

Sissejuhatus
------------

Nginx on vene päritolu BSD litsentsi all levitatav veebiserver
mis on jõudsalt turuosa haaranud `viimaste aastate jooksul 
<http://news.netcraft.com/archives/2013/01/07/january-2013-web-server-survey-2.html>`_.
Apache2 kasutab lõimesi (*thread*), et mitmeid kasutajaid teenindada.
Selle puhul lööb välja niinimetatud C10K probleem, ehk et üle 10000 ühenduse
tegemine Apache2 veebiserverisse muutub keeruliseks.
Nginx kasutab Linux puhul *epoll* meetodit võrgusoklitega majandamiseks,
mistõttu ta saab vabalt hakkama 500000+ ühenduse üleval hoidmisega.

Paigaldus
---------

Nginx veebiserveri pakett on Debian ning Ubuntu varamutes olemas,
kuid sellest on mitu varianti: *nginx*, *nginx-full*, *nginx-extras*.
Viimases on kõige rohkem kellasid ja vilesid sisse kompileeritud:

.. code:: bash

    sudo apt-get install nginx-extras

Seadistused
-----------

Seadistuste failid paigutatakse /etc/nginx/sites-available/hostinimi.domeeninimi alla ning 
sisselülitatud seadistuste jaoks tehakse *symlink* /etc/nginx/sites-enabled/ alla:

.. code::

    # If there is & in the filename this fails
    map $request_uri $dirname {
            ~^(?<captured_dirname>.*)/[^/]*$ $captured_dirname;
    }

    server {
            # Web server virtual hostname
            server_name nas.povi.ee;

            root /var/www;

            # Enable fancy indexes if this nginx build supports it
            fancyindex on;
            fancyindex_exact_size off;
            
            # Public ~/Shared directories
            location ~ ^/~(?<user>.+?)/Shared/(?<path>/.*)?$ {
                    alias /home/$user/Shared$path;
                    index  index.html index.htm;
                    autoindex on;

                    # Follow symlinks if symlink target owner is the symlink owner
                    disable_symlinks if_not_owner;

                    # Expand URL of XSPF playlist files
                    sub_filter_types application/xspf+xml;
                    sub_filter '<location>' '<location>http://$server_name$dirname/';
                    sub_filter_once off;
            }
            
            # PAM authenticated home directories
            location ~ ^/~(?<user>.+?)(?<path>/.*)?$ {
                    alias /home/$user$path;
                    auth_pam "Me want cookie!";
                    auth_pam_service_name "nginx";
                    dav_methods PUT DELETE MOVE COPY MKCOL;
                    dav_ext_methods PROPFIND OPTIONS;
                    dav_access user:rw  group:rw;
                    client_body_temp_path /tmp/;

            }

            # Public passwordless writable /incoming/ directory
            location /incoming {
                    alias /home/incoming;
                    client_max_body_size 20M;
                    dav_methods PUT;
                    dav_ext_methods PROPFIND OPTIONS;
                    dav_access user:rw  group:r all:r;
                    client_body_temp_path /tmp;
            }
    }
    
Käesolev seadistus eeldab PAM teenuse olemasolu failis /etc/pam.d/nginx.
Selles failis saab ka ära piirata kes saavad veebiserverisse sisse logida:

.. code::

    @include common-account
    @include common-session
    @include common-password
    
Selline lähenemine muidugi eeldab, et veebiserveril on õigus kohalike
kasutajate autentimiseks lugeda paroolide räsisid failist */etc/shadow*.
Kui *common-account* ja *common-session* viitavad LDAP serverile,
siis järgnev liigutus vajalik **ei** **ole**:

.. code:: bash

    gpasswd -a www-data shadow

Teenuse seadistuste taaslaadimine:

.. code:: bash

    sudo service nginx reload
    
WebDAV
------

WebDAV on HTTP protokolli laiendus mis võimaldab üle HTTP 
üles laadida, kopeerida, liigutada ning kustutada faile.

Põhiline probleem WebDAV puhul on see, et kõik failid millele
ligipääsu soovitakse üle veebi peavad olema kirjutatavad/loetavad
veebiserveri kasutaja õigustes (*www-data:www-data*).
OwnCloud puhul ongi naguinii kõik kasutaja failid veebiserveri kasutaja õigustes
/var/www/owncloud/data/kasutajanimi all.
Ligipääsuõigused lahendatakse autentimisega.

Ubuntu graafilises liideses saab kasutaja kodukataloogilie ligi nautilus abil:

.. code:: bash

    nautilus dav://nas.povi.com/~kasutajanimi

   
Käesolevas näites seadistatud /incoming/ tee võimaldab käsurealt väga hõlpsalt
üles laadida faile:

.. code:: bash

    curl -T failinimi.blah nas.povi.ee/incoming/
    
Androidi jaoks leiab ka FolderSync nimelise programmi mille abil
millega saab näiteks sünkroniseerida kaamera kataloogi veebiserveriga.

