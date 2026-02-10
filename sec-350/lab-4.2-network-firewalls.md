# Lab #4.2: Network Firewalls

## Configuring Firewall Zones in VyOS

the first step to making firewall rules in VyOS is to create firewall zones. These act similar to folders in the sense that they will become a reference for a larger number of firewall rules. The following commands were used to create firewall zones on fw01-chris for the lab.

```
set firewall zone WAN
set firewall zone DMZ
set firewall zone LAN
set firewall zone WAN member interface eth0
set firewall zone DMZ member interface eth1
set firewall zone LAN member interface eth2
```

These commands establish the zones and then also assign member interfaces which will be used to define the rules later.

## Establishing Default Actions

To create a default action on a VyOS firewall, the zones have to be established first and once that is in place I used the following commands to tell the system what to do by default. These rules will apply to and from the WAN and DMZ thus creating a firewall system where packets cannot come in or out of the DMZ to or from the WAN unless otherwise explicitly specified.&#x20;

These commands also establish that when a packet is dropped or the rule is triggered, that it will log the action by default.&#x20;

```
set firewall ipv4 name WAN-to-DMZ default-action drop
set firewall ipv4 name DMZ-to-WAN default-action drop
set firewall ipv4 name WAN-to-DMZ default-log 
set firewall ipv4 name DMZ-to-WAN default-log
```

## Assigning Zones

Not that default options are set and the default rules are established, I used the following commands to assign the zones to the actual firewalls.

```
set firewall zone WAN from DMZ firewall name DMZ-to-WAN 
set firewall zone DMZ from WAN firewall name WAN-to-DMZ
```

Once these zones were applied the firewall was set into effect. To test this, I used my rw01 VM which is connected to the WAN to attempt to reach my web server which is located on the DMZ. The screenshot below is the resulting ping showing that it can no longer reach my web01 VM on the DMZ.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## Deliverable 1

The screenshot below is of the fw01 log showing that the firewall rule for reaching DMZ from WAN was triggered by rw01 and that it was stopped.

The command used to achieve this log output was "tail -f /var/log/messages.1 | grep WAN"

Breaking the output down, the first field is the time stamp with date and time to the second, the second shows the firewall kernel triggered this log, the next defines all of the parameters related to the logged error. The important fields here are:

* Interface was eth0
* The source and destination MAC
* The source and destination IP addresses
* The TTL
* The protocol
* The type and code responses of said protocol
* And the sequence number which will help with other types of packets.&#x20;

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Allowing Ports and Services

Now that the default rules are in place and have been tested, the DMZ is important to access because it hosts my web server which needs to be accessed from outside of my "company" network. ion order to do this I need to establish an allow rule for port 80 or the http service outbound of my DMZ.

For the WAN to the DMZ I first had to establish an allow rule for port 80 traffic. These commands can be seen below.

```
set firewall ipv4 name WAN-to-DMZ rule 10 action accept
set firewall ipv4 name WAN-to-DMZ rule 10 description "Allow HTTP"
set firewall ipv4 name WAN-to-DMZ rule 10 destination port 80
set firewall ipv4 name WAN-to-DMZ rule 10 destination address 172.16.50.3
set firewall ipv4 name WAN-to-DMZ rule 10 protocol tcp
```

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

For the DMZ firewall specifically, the rule I will be establishing will make it so that established connections can move from the DMZ to the WAN. The commands used for this can be found below.

```
set firewall ipv4 name DMZ-to-WAN rule 1 action accept
set firewall ipv4 name DMZ-to-WAN rule 1 description 'Allow established'
set firewall ipv4 name DMZ-to-WAN rule 1 state established
```

Once these commands were issued and saved, the firewall rule listing for the DMZ to WAN firewall zone looked like the screenshot below where the new rule is present.&#x20;

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Deliverable 2

To test this new rule, I created a very basic webpage called welcome.html that will server as the home page for the test. As seen in the screenshot below I can access the webpage from rw01 now that the rule was in place.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## Setting Up Other Firewalls

Now that the WAN to DMZ firewall is in a good spot, I moved onto the LAN to DMZ and vice versa. These are the third and fourth firewalls of the 6 that will be in the system on fw01. I started by establishing default rules and default logging using the same commands as for the WAN to DMZ firewall.&#x20;

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

## Deliverable 3

Because of the default ruleset being to drop packets, my Wazuh server cannot communicate between the DMZ and the MGMT segment because there is no traffic allowed between the DMZ and the LAN which is between DMZ and MGMT so therefore it does not work. The screenshot below is from my VyOS logs showing the failed communications over the Wazuh ports on my network.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

## Allowing Wazuh Through Firewalls

