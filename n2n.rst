n2n
===

Introduction
------------

`n2n <http://www.ntop.org/products/n2n/>`_
is *layer2* peer-to-peer virtual private network software.
A n2n network consists of supernodes and edges. Simply put supernode
is the entry point to the network behaving similarility to a tracker in a
BitTorrent network. The edges initially connect to supernode to exchange
information about other nodes.

n2n uses Twofish symmetric cipher which is not very widely used but it 
should be fast.

In n2n terminology a VPN is called a community. The community name and password
is required to join a community.
If I am not mistaken then the n2n design was restricted to 254 machines per 
community.

Even though I opened a port on one of the edges and told edge to bind to the 
same port I was unable to avoid relaying traffic through supernode.
On the positive side a supernode is capable of serving many communities.


Running
-------

To launch supernode on the default UDP port 7654:

.. code:: bash

    supernode -f
    
To launch edge on the same machine:

.. code:: bash

    edge -a 10.1.2.1 -c lauri-vpn -l 127.0.0.1:7654 -E -r

To launch edge on another machine:

.. code:: bash

    edge -a 10.1.2.2 -c lauri-vpn -l lauri.vosandi.com:7654 -E -r
    
