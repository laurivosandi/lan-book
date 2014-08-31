.. title: Kasutaja defineeritud ahelad
.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags: 
.. date: 2013-10-31

Ise saab luua filtreerimistabelitega (*filter*) ahelaid mida saab määrata sisse-ehitatud
ahelate sihtmärkideks (*target*):

.. code:: bash

    # Loo uus ahel nimega blacklist
    iptables -N blacklist

    # Lisa blacklist ahel sihtmärkideks input ning forward ahelates
    iptables -I INPUT -j blacklist
    iptables -I FORWARD -j blacklist

Nii saame blacklist ahelat tühjendada ja uuesti täita mõne skriptiga,
mis ei pruugi ülejäänud tulemüüri seadistustest teadlik olla:

.. code:: bash

    iptables -F blacklist
    iptables -A blacklist -m mac --mac-source 88:30:8a:43:bf:53 -j DROP

Kasutaja defineeritud ahelast saab ka poole pealt välja hüpata:

.. code:: bash

    # Sõltumata MAC aadressist luba vähemalt pöörduda DNS serveri poole
    iptables -I blacklist -p udp --dport 53 -j RETURN
