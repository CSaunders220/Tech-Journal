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

