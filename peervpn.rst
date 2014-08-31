.. author: Lauri Võsandi <lauri.vosandi@gmail.com>
.. tags: VPN, PeerVPN, iptables, OpenWrt

PeerVPN
=======

Introduction
------------

PeerVPN uses full-mesh topology and tries to establish direct links if it is possible.
Usually that means that one of the nodes in a communication pair has to have
a open port facing the public Internet.

The links between nodes are encrypted using AES-256 encryption algorithm
and every time a link is established a new key is used  to encrypt that 
connection. The checksum algorithm SHA-256 is used in PeerVPN.

The PeerVPN configuration is placed in */etc/peervpn/peervpn.conf* or
*/etc/peervpn.conf* depending on distribution.

Topology
--------

Current configuration example is based on following network topology:

.. image:: /shared/diagrams/peervpn-topoloogia.png
    :align: center

The constraints are following:

* The VPS has public IP address of 217.146.67.29 and the PeerVPN service runs on 
  port 7000
* The 3G router is behind ISP's NAT, which means that it has to proxy it's traffic though VPS
* The router at home has public but dynamic IP address and it's configured to 
  accept PeerVPN traffic on port 7000
* The NAS box is in the home subnet
* The laptop may be NAT'ed or not

The goals:

* The VPS should be the entry point to the PeerVPN network
* Once links are established the traffic between NAS and laptop should 
  go only through the router at home
  
VPS configuration
-----------------

In my cloud server I enabled relaying so nodes which can't connect directly
could still communicate:

.. code:: bash

    # Network
    networkname lauri-vpn
    psk verysecurepassword
    enabletunneling yes
    enablerelay no
        
    # Virtual interface
    interface peervpn0
    ifconfig4 10.8.0.1/24
    #ifconfig6 2001:db8:1:2::3/64
    #upcmd /etc/peervpn-connected.sh
    
    # Physical interface
    local 217.146.67.29                # Public static IP address of the cloud server
    port 7000
    #sockmark 0
    #enableipv4 yes
    #enableipv6 yes
    #engine padlock
    
    # Priviliege drop
    enableprivdrop yes
    user nobody
    group nogroup
    chroot /var/empty
    
The router at home
------------------

In my OpenWRT router I enabled
following configuration for PeerVPN:

.. code:: bash

    # Network
    networkname lauri-vpn
    psk verysecurepassword             # Pre-shared key
    initpeers lauri.vosandi.com 7000   # Initial peer, could be more than one
    enabletunneling yes                # Disable if this node only relays traffic
    enablendpcache yes                 # Cache tunneled IPv6 NDP messages
    enablerelay no                     # Do not relay traffic between nodes

    # Virtual interface
    interface peervpn0
    ifconfig4 10.8.0.2/24
    #ifconfig6 2001:db8:1:2::3/64
    #upcmd /etc/peervpn-connected.sh

    # Physical interface
    #local 0.0.0.0                     # My router has public but dynamic IP
    port 7000                          # Bind to fixed port

    # Privilege drop
    enableprivdrop yes
    user nobody
    group nogroup
    chroot /var/empty

I also opened up port 7000 in */etc/config/firewall*:

.. code:: bash

    config rule
	    option name 'Allow PeerVPN'
	    option src 'wan'
	    option proto 'udp'
	    option dest_port '7000'
	    option target 'ACCEPT'
	    
And added custom forwarding rules to allow PeerVPN nodes to access home subnet
in */etc/firewall.user*:

.. code:: bash

    iptables -I FORWARD -i br-lan -o peervpn0 -j ACCEPT
    iptables -I FORWARD -i peervpn0 -o br-lan -j ACCEPT

Laptop configuration
--------------------

In my laptop I enabled following configuration in */etc/peervpn.conf*

.. code:: bash

    # Network
    port 7000
    networkname lauri-vpn
    psk verysecurepassword
    enabletunneling yes
    interface peervpn0
    ifconfig4 10.8.0.3/24
    initpeers lauri.vosandi.com 7000
    upcmd route add -net 192.168.72.0/24 gw 10.8.0.2

The last line adds route to 192.168.72.0/24 subnet behind my router
which means that I gain direct access to my NAS box behind the router.

To wrap up PeerVPN - I use cloud server to get initial access to my virtual
private network,
but once the mesh-topology is established I get direct access to my NAS box
via my router.

.. [#belug] `PeerVPN talk at BeLUG <http://www.belug.de/termine/talk-peervpn-english.html>`_


