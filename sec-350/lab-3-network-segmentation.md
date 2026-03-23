# Lab #3: Network Segmentation

## Configuring New VMs

The first thing that I had to do with this lab was to configure some newly deployed and old boxes that I had not yet touched. This includes the mgmt01 VM, the workstation VM, and the new firewall VM for the management network segment that will be branching off of the WAN.&#x20;

Setup and information regarding the setup of workstation and the initial setup of mgmt01 have been omitted because these are basic tasks that have been completed on all boxes within the infrastructure.&#x20;

## Deliverable 1

To prove that I set up the LAN correctly, this is a screenshot from the workstation VM showing the hostname, the new user, and also the ability to ping champlain.edu showing outbound internet and name resolution.

<figure><img src="../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 2

Similarly to the last deliverable, this is another to test connections between my LAN and DMZ.&#x20;

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Enabling RIP on VyOS

One major part of this lab is to enable RIP on the two routers to link the MGMT and DMZ networks. This can be done for an interface using the commands below. These commands were repeated on both routers for the respective interfaces where the ethernet interface is the outbound advertiser and the ip address is the network that it will be advertising.&#x20;

```
set protocols rip interface eth0
set protocols rip network '172.16.200.0/28'
```

## Deliverable 3

The screenshot below is of my new management box which can not ping the hosts on the DMZ, has a set hostname, and a set named administrator user.&#x20;

<figure><img src="../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

## Configuring Wazuh

The Wazuh server at this stage in the labs will not be used for anything but for now will instead just need to be set up. This machine is an ubuntu server VM that will eventually host the Wazuh service and will be on the MGMT network.&#x20;

The one "new" thing that I haven't done in a while for setting this up was editing the netplan configuration file which is a YAML file, notorious for spacing issues.&#x20;

The following sample below shows my configurations made to my /etc/netplan configuration YAML file.&#x20;

```
network:
    version: 2
    ethernets:
        en018:
            addresses:
                - 172.16.200.10/24
            nameservers:
                addresses: [172.16.200.2]
            routes:
                - to: default
                  via: 172.16.200.2
```

Once these edits were made to my configuration file I restarted the service and then attempted to reach out of the network as seen in the following deliverable.&#x20;

## Deliverable 4

The following screenshot illustrates that I can reach google.com from my Wazuh server as well as that I can curl the resources on my DMZ web server.&#x20;

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 5

The following screenshot shows that I am able to ssh from mgmt01 to my wazuh server, and it also shows the first four hops that the wazuh server takes to reach the outside internet through my LAN network, and then out from the WAN to the datacenter network.&#x20;

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 6

The following screenshot is of my web server showing that I am able to ping the management network, more specifically that I can ping my Wazuh server on the management network which will be important later once the services are set up.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 7

This deliverable is in two parts, the first section is of fw01-chris showing my configuration commands on that machine, and the second part showing the same for my fw-mgmt VyOS machine.&#x20;

### fw01-chris

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

### fw-mgmt-chris

<figure><img src="../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 8

The final deliverable for this lab, is of course the network diagram. The screenshot below shows my up to date network diagram for my network at this stage in the class.&#x20;

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>
