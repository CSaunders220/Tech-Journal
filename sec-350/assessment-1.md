# Assessment #1

## Overview

This assessment saw the removal of fw01, rw01, and web01 and instead I received new VMs nginx, traveler, DHCP, and edge. The configuration file for fw01 was saved and can be imported onto edge01 in its stead of the default boot configurations.&#x20;

## Deliverable 1

The following screenshot shows my new named user, who is in the sudo group, as well as a successful ping to champlain.edu from the new ubuntu system nginx01. This is with an outbound firewall exception that I created on edge to allow the packets in and out temporarily.&#x20;

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

## Merging Configuration Files in VyOS

I had struggled on this for a little while in order to do this right. When importing a configuration file you first have to download it with wget or curl from wherever the file is, or download it via other means. Once downloaded, rename it to \*.cfg and store it in the /configs directory. Enter configuration mode and use the load command with the path to this config file.&#x20;

KEY DETAIL: Make sure to then edit the config.boot file to change the MAC addresses to the new interface MAC address and not the old addresses. If this does not happen new interfaces will be created with new interface names.

## Deliverable 2

The following screenshot is of my traveler VM showing the current logged user which is my administrator named account, the hostname of the machine, and a successful curl of my webpage (currently the test nginx page) on nginx through the firewall's port forwarded rule after disallowing all traffic from DMZ-to-WAN and WAN-to-DMZ.

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 3

The following screenshot is of my Wazuh dashboard where I can see the events for the nginx-chris machine and I have a failed SSH attempt from an invalid user logged in the system security events. The screenshot below shows the event in the lost of events.

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 4

The following  screenshot shows a successful SSH attempt from mgmt01-chris to nginx-chris.

<figure><img src="../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 5

The following is a screenshot of my SSH session on mgmt01-chris into nginx01-chris that is attempting to curl champlain.edu as well as the logs on edge01 showing that the firewall is dropping the packets because there should be no outbound internet allowed through DMZ to WAN and beyond.

<figure><img src="../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Setting Up a DHCP Server on Ubuntu

To configure my DHCP server on the new DHCP box I used the following commands to install the service, edit the configurations, and start the service.

```
sudo apt install isc-dhcp-server -y
sudo nano /etc/default/isc-dhcp-server
#Set the name of the network interface ex. INTERFACESv4="ens18"
sudo nano /etc/dhcp/dhcpd.conf
subnet 172.16.150.0 netmask 255.255.255.0 {
  range 172.16.150.50 172.16.150.99;
  option routers 172.16.150.2;
  option domain-name-servers 172.16.150.2;
  default-lease-time 600;
  max-lease-time 7200;
}
sudo systemctl restart isc-dhcp-server
sudo systemctl enable isc-dhcp-server
```

Once completed, the server began its work and I changed workstation to now retrieve its IP addressing information automatically through DHCP.&#x20;

## Deliverable 6

The following screenshot shows my workstation performing a release, renew, and listing its IP address information after it gets the new lease from the new DHCP server.&#x20;

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## Deliverable 7

The following screenshot is of the security events for my DHCP server in Wazuh showing that I attempted to log in as a user called notreal which didn't exist and failed to authenticate on the server.

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 8

The following screenshot is of my traveler system that is remotely logging into the jump VM through the IP address of the port forwarded firewall passwordlessly through the generated SSH key for jump from traveler.

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 9

The following is a screenshot showing my workstation viewing the web page hosted on my nginx VM on the DMZ network.

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 10

The final deliverable is to show all of the configurations of edge01 in plaintext configuration command form so the following GitHub link is directly to the configurations command list for my firewall which I imported from the original configurations for fw01 and modified.

{% embed url="https://github.com/CSaunders220/Toolkit/blob/Tools/SEC-350/edgeConfigurations.txt" %}
