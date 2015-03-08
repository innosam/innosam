---
layout: post
title:  "Openstack Networking"
date:   2015-03-08 14:20:24
---

I will discuss these important terminologies which makes virtual networking the way it is:
This is for people who are starting out:

#Linux Bridge:
Virtual bridge connects one or more interfaces together, it acts just like a switch, the packet forwarding 
is done based on the MAC address. 

Create Linux bridge:

virbr - name of the bridge
	 
`#sudo brctl addbr virbr`

Add eth0 interface to the bridge 

`#sudo brctl addif virbr eth0`

Also enable [promiscous mode](http://en.wikipedia.org/wiki/Promiscuous_mode) of the eth0 interface. 

`#ip link set dev eth0 promisc on`

Show all the bridges 

`#brctl show`
	
Set the bridge up

`#ip link set up dev virbr`




#VLAN:
Quoting from wikipedia

`In computer networking, a single layer-2 network may be partitioned to create multiple distinct broadcast domains, which are mutually isolated so that packets can only pass between them via one or more routers; such a domain is referred to as a virtual local area network, virtual LAN or VLAN.`

In short, the packets are distinguised by the VLAN tags. The VLAN tags are inserted in the L2 header, the tag has maximum size of 12 bits, which limits the number of tags to 4094, as other 2 tag ids are reserved.


VLAN allows to create multiple broadcast domain and share a common gateway.



                                                             +-------------+
    +---------------------------------------+  TRUNK PORT    |             |
    |            SWITCH                     |                |             |
    |                                       +----------------+  ROUTER     |
    |                                       |                |             |
    |                                       |                |             |
    |                                       |                |             |
    |      tag-2    tag-1    tag-1   tag-2  |                +-------------+
    |       +        +        +       +     |                               
    +---------------------------------------+                               
       |         |        |       |                                     
       |         |        |       |                                     
       |         |        |       |                                     
       |         |        |       |                                     
     +-+--+    +-+--+   +-+--+  +-+--+                                  
     |    |    |    |   |    |  |    |                                  
     | A  |    | B  |   | C  |  | D  |                                  
     |    |    |    |   |    |  |    |                                  
     +----+    +----+   +----+  +----+                                  

When host A sends [ARP](http://en.wikipedia.org/wiki/Address_Resolution_Protocol) request(broadcast) it will be recieved only by D and the router. As trunk port will allow all tagged packets to pass through.




#GRE/VXLAN Tunneling:
[VXLAN](https://tools.ietf.org/html/rfc7348) is a industry wide attempt to remove the scalability issues of VLAN 
It is an overlay network and works over L3 to create broadcast domain. The packets from the VM's are encapusulated in a UDP and then communicated to other VXLAN tunnel end points(VTEP).

The point to point traffic is tunneled between the VTEP's. Although the broadcast traffic(ARP) is sent using multicast technology and response is again point to point.

Importantly, VXLAN Network Identifer(VNI) is mapped to each endpoint, and it is included in the encapsulated UDP packet.
The VNI identifier is 24-bit segment ID, this allows up to 16 Million VXLAND segments to coexist within the same administrative domain.




#OVS:
[Open vSwitch](http://openvswitch.org/), sometimes abbreviated to OVS, is a production-quality open source implementation of a distributed virtual multilayer switch.


[WHY-OVS](https://github.com/openvswitch/ovs/blob/master/WHY-OVS.md)

[OVS TUTORIAL WITH HANDS ON](http://openvswitch.org/support/dist-docs/tutorial/Tutorial.md.txt)














