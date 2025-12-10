# Lab#3: Advanced Networking and Services

## Overview

This lab covers the addition of two new virtual networks on the environment, a DMZ and a new LAN for management, a basic web server to emulate how the DMZ would work, and just general services configuration to prepare for automation and nested virtualization in the next few weeks.&#x20;

## New Networking

Before even touching the PFsense VM, I went into my ESXi and added two new port groups that all route through the 350-internal virtual switch in order to functionally create VLANS for the new networks. Once that was completed I was able to create new interfaces

Moving to the PFsense VM, I added two additional virtual network adapters for the two new networks. Once they were added to the VM, I reset the PFsense VM, re-assigned interfaces, and then assigned them all new network assignments. For ease of remembering, I kept the LAN on 10.0.17.0, I added management on 10.0.18.0, and then the DMZ on 10.0.19.0.

<figure><img src="../.gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

In order for DNS to work with the new networks, I needed to create new reverse lookup zones for the DNS management so I went over to my AD and added two new reverse lookup zones as seen below.

<figure><img src="../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

With that done, I tested the internet connection from my LAN to make sure that none of the new changes disrupted the networking that I had before for my AD and vCenter.&#x20;

### Management Network

Now that the network for my management LAN was created, I needed to configure the mgmt01-chris VM to use this network and make sure that it can route properly. I switched the IP address, default gateway, and subnet mask to be on the new network and I kept the DNS pointing at my AD since it has the new pointer zones for the new networks.&#x20;

<figure><img src="../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

Lastly, to make sure the new networking for the management network was done, I went into my PFSense web portal and made sure that the management network was able to route to the internet and that packets were able to go through the firewall as seen in the rules list below. I also ran a ping and DNS test on the machine to verify.

<figure><img src="../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>

### DMZ Network

The only machine that will be inside of the DMZ at the time of this lab will be the web server. This VM should not be able to access the LAN or MGMT networks but should be able to be reached by both of these networks. Additionally, the DMZ should have availability to provide HTTP and HTTPS services outbound and inbound since it will be hosting a web server.&#x20;

To start, I created a new rocky VM on the ESXi called web01-chris and networked it to the DMZ network. For now, I allowed internet traffic through the PFsense firewall for configuration purposes. Before configuring the web services, I had to get the DMZ network configured for the new rocky VM so I applied the internet settings as seen below.

<figure><img src="../.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>

Since this machine will not be able to connect to the LAN or the MGMT networks, this machine was set to use the google DNS servers (quad 8). With that done, the machine was wired properly and configured properly but the firewall rules to allow connections had to be changed in the PFsense web configurator and for that I used my management box to make these changes.

Unlike the LAN and MGMT networks, the DMZ has a few more firewall configurations that change its permissions and capabilities when traversing the network. Since it is a DMZ, I wanted to make sure that the web server could not reach the LAN or the MGMT networks.

<figure><img src="../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

In order, top to bottom, we have several rules. There are:

* Disallowing the DMZ network from accessing the LAN
* Disallowing the DMZ from accessing the MGMT network
* Allowing all HTTPS connections over port 443 onto the DMZ network
* Allowing and HTTP connections over port 80 onto the DMZ network
* Allowing the MGMT network to access the DMZ network
* Allowing the LAN network to access the DMX network

When all enabled, it allows for just web requests to access the DMZ, both the MGMT and LAN networks to have access to monitor and manage the DMZ machines, and it prevents the DMZ from being able to traverse laterally across the broader network.&#x20;

It is important to note that I learned that PFsense managed the firewall rules in DESCENDING order, so any rules at the top of the list will be considered before any of the following rules which can lead to contradictions and issues if not done right.

When configured right, I am able to view the web page being hosted on the DMZ network on my management box as seen below.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Video Proof Submission

Below is a short vide demonstrating the networking capabilities of the new environemt and prooving all of the above claims and configurations.&#x20;

{% embed url="https://drive.google.com/file/d/1l7u1dF1eot252jKEwIho0e-ECV7SdD4i/view?usp=sharing" %}

## Reflection

This lab was very new to me as a whole since I am a third year at the time of taking this class compared to most students in this section who are fourth years. I have never really touched the concept of a DMZ. The concept was intuitive though and it didn't take me long to figure it out but for some other students who have taken more advanced SEC courses it probably was a little easier. Overall, when all is said and done, once I understood what a DMZ was and its purpose, it was simple to just configure the new networking and firewall rules.&#x20;
