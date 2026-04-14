# Week #3: DHCP

## What is DHCP

DHCP is a protocol that allows for the dynamic configuration of host's IP addresses, DNS servers, default gateways, and more. This is an incredibly important service in large scale enterprise networks as it allows for the rapid configurations of a large number of hosts on the same network.&#x20;

DHCP follows a four packet initialization to begin, these packets go in the following order:

* Discover - client sends this to see if there are DHCP servers on the network
* Offer - The server replies with a lease offer
* Request - the client sends a official request to use the previously sent lease
* Acknowledge - the server sends a confirmation to the client allowing them to use the lease

When a client is attempting to rebind an address, the client will only send the request and the server will send the acknowledge packet in reply to this and the address will be rebound.

It is important to note that renewal begins 50% of the way through the lease and rebinding happens at 87.5% of the lease time.&#x20;

DHCP traffic uses two separate ports to communicate between client and server. The first is UDP 67 for the server listening for broadcasts and the second is UDP 68 for client listening for communication from the DHCP server.

## Capturing DHCP Packets

DHCP packets will often be in a short burst of four packets in a row, one of each packet type as described above. The following screenshot shows a single FHCP interaction between client and server where the IP address 192.168.3.1 is the client and 192.168.1.10 is the DHCP server.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

The following graphic also shows the DHCP packet header and the components that make up these packets.&#x20;

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Setting Up a DHCP Server With Cisco Routers

Setting up a DHCP server itself is easy, both as a concept and within packet tracer. The general steps for getting a DHCP server running are as follows:

* Setting up a pool starting IP
* Setting up a pool stop IP
* Telling it what interface to serve over
* Defining the pool's default gateway
* Giving it a DNS address for the pool

Once these are complete and the service is on there is one last step for allowing the DHCP server to properly work on a subnet with the cisco routers. This would be to define a helper address.

A helper address basically tells the router that there is a service over this IP address that the different subnets may be requesting for something like DHCP. To set a helper address in the cisco CLI I used the following commands:

```
Router# configure terminal
Router(config)# interface FastEthernet 0/X
Router(config-if)# ip helper-address 192.168.3.10
Router(config-if)# exit
```

It is important to note that for each interface / VLAN that you wish to be able to access the DHCP server you have to define the helper address.&#x20;
