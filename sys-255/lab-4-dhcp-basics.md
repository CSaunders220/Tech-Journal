# Lab #4: DHCP Basics

### **General Remarks**

This lab was an interesting one for sure in many ways. The actual configuration of the DHCP service was not too difficult in my opinion, but the fallout of the errors and issues that insured after were quire interesting. More on those can be found in the issues section below so I won't go into detail here. Overall, my personal remarks are just that the lab was smooth until it came time to changing the lease times for the final part of the lab.

### **Other DHCP Research**

Beyond the four main packet types, there are several other types that may pop up when using DHCP on a much larger scale than my one workstation. Some of those include:

* DHCPDECLINE: A packet to tell the machine that the allocated IP address conflicts with another active IP address.
* DHCPNAK: The opposite of an ACKK request. Tells the client that the server is refusing the lease of the requested IP.
* DHCPRELEASE: Sent to the server from the client and makes the server aware that the client is releasing the IP address backk into the pool of leasable addresses within the scope.
* DHCPDECLINE: This packet is sent from the client side and is similar to DHCPNAK but instead of the server rejecting the IP it is the client. The client can reject for several reasons including the lease time being refused, the IP address being refused, or the network parameters being refused.

These are just a few of the ones that I found important and interesting of the many other DHCP packet types and codes that are available.

### **Configurations**

Below is a screenshot of the conf file for the DHCPD service:

![image](https://github.com/user-attachments/assets/64bf0c3d-d505-4ba8-835d-f8ac5ab6e90c)

As shown above, the configurations that were used and the syntax for doing those was generally not that complicated. Going line by line through the configuration file, the options are:

* default-lease-time: meaning the default time which is given when creating a new lease in seconds.
* max-lease-time: the maximum lease time in seconds
* subnet x.x.x.x netmask x.x.x.x: The actual network ID that you wish to have the clients on and the subnet of that networkk ID
* option routers x.x.x.x: the default gateway
* option subnet-mask x.x.x.x: the subnet mask of the network
* option domain-name "xxxx": the name of the domain that you are adding the workstation to
* option domain-name-servers x.x.x.x: The server IP for the domain controller.
* range x.x.x.x x.x.x.x: The range of IPs that the DHCP is allotted to lease.

These are the bare bones basics of setting up the DHCP server and must be configured for any DHCP server in order for it to be usable.

### **Issues Encountered**

While working on the last part of the lab and configuring the lease times, I ran into a strange issue. The lease time was defaulting to 24 and some odd hours even after fixing the configuration file. While troubleshooting this, I attempted to renew the lease multiple times, I captured Wireshark packets to verify that all four of the DORA packets were being sent and received, and I attempted to restart the service many times but to no avail. Eventually, I changed the range of IPs that the DHCP was allowed to lease out and this fixed the issue and reset the lease time so that it did not default to 24 hours but instead to the hour time that I had asked it to in the configuration file. This to me was perplexing and at the time of writing this journal I still have not figured out why exactly that I had this issue. More research will be needed to investigate the issue.
