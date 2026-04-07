# Lab #10: Windows Logging With Wazuh

## Overview

This lab will go through installing wazuh on workstation and mgmt02 so that it can be better visualized and also adding a domain to the environment, domain joining workstation across networks, and allowing windows AD communication over VyOS.

## Allowing Windows AD Through a Firewall

There are many ports that Windows AD uses to communicate between the server and the host and thus there are a lot of ports to have to allow through the firewall. The ports that needed to be added to my fw-mgmt can be seen below, ordered numerically and sorted by protocol.

* TCP
  * 53 (DNS)
  * 88 (Kerberos)
  * 389 (LDAP)
  * 445 (SMB)
  * 135 (RPC)
  * 464 (Kerberos)
  * 3268 (LDAP Global Catalog)
  * 9389 (Active Directory Web Services)
* UDP
  * 53 (DNS)
  * 88 (Kerberos)
  * 389 (LDAP)
  * 445 (SMB)
  * 464 (Kerberos)

Additionally, there is an additional port range that must be allowed for the RPC service to communicate between host and server. Traditionally, this is a port range form ports 49152-65535, however, in environments with firewalls such as this it is possible to limit the port range to a defined range. In my case, I decided to use the windows registry to add some keys and limit the port range between 5000 and 6000. The key path that was used was HKEY\_LOCAL\_MACHINE\Software\Microsoft\Rpc where I added the three keys as seen below with the values as such.

<figure><img src="../.gitbook/assets/image (205).png" alt=""><figcaption></figcaption></figure>

Disclaimer: key names are critical in the registry and pathing is even more so. All changes must be followed by a restart of the system.&#x20;

## Defining Port Groups in VyOS

In order to better set up the ports for the systems, I used port groups, one for each protocol, to better organize the ports in my VyOS system. I used the command below to establish a port group.

```
set firewall group port-group WINDOWS_TCP port 53
```

This is the command for adding port 53 to the group and can be repeated for each and every port needed in the group. Once made, I applied it to my firewall rules using the following commands.&#x20;

```
set firewall name MGMT-to-LAN rule 60 action accept
set firewall name LAN-TO-WAN rule 60 description 'Windows TCP Ports'
set firewall name LAN-TO-WAN rule 60 destination port group WINDOWS_TCP
set firewall name LAN-TO-WAN rule 60 protocol tcp
```

This functionally creates an allow rule for all of the ports in the group meaning that it can also be dynamically updated by simply adding ports to the group to allow more or removing to deny.&#x20;

## Deliverable 1

Once all of the ports were ready to go, I downloaded windows AD services and added a domain to my network called chris.local. The screenshot below shows my workstation logged in as my domain admin account on my chris.local domain and DNS server.

<figure><img src="../.gitbook/assets/image (206).png" alt=""><figcaption></figcaption></figure>

## Deliverable 2

The screenshot below shows my wazuh dashboard that has both my workstation and my management02 VM added to the list of monitored systems and agents. The best way that I found to install the service is to download and install on workstation and then scp the package to mgmt02 to avoid internet access on that network.&#x20;

<figure><img src="../.gitbook/assets/image (207).png" alt=""><figcaption></figcaption></figure>

## Deliverable 3

Now that Wazuh is installed on wks, I logged in as a standard user and checked for the user in the logs of wazuh. The following screenshots show the event in the wazuh dashboard for this logon (in multiple screenshots because there are a lot of fields in the event).

<figure><img src="../.gitbook/assets/image (208).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (209).png" alt=""><figcaption></figcaption></figure>

## Deliverable 4

Similarly, I then tested with a nonexistent user to check the Wazuh logs for this event in the logging service and the screenshots below show the failed logon from the fake user.

<figure><img src="../.gitbook/assets/image (210).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (211).png" alt=""><figcaption></figcaption></figure>

## Deliverable 5

I also attempted a valid windows RDP session form workstation to management 02 to attempt to catch it in wazuh. The following screenshot shows the logged security event in the Wazuh dashboard. Notably, the IP address is highlighted from the source machine and the username. Not highlighted, but the event ID in the second screenshot is seen as well which is for a valid RDP connection.&#x20;

<figure><img src="../.gitbook/assets/image (212).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (213).png" alt=""><figcaption></figcaption></figure>

## Deliverable 6

Similarly to the native logons, I now attempted to RDP as an invalid user to catch this in the Wazuh dashboard. The screenshot below shows the logged event in the Wazuh event logs for my management 02 VM. This time the event ID is also highlighted which is for a failed RDP connection.&#x20;

<figure><img src="../.gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (215).png" alt=""><figcaption></figcaption></figure>

## Deliverable 7

This tech journal serves as my notes for this lab and all of my technical information for all of the new concepts covered in the lab.&#x20;
