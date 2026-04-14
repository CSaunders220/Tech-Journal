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

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

For the DMZ firewall specifically, the rule I will be establishing will make it so that established connections can move from the DMZ to the WAN. The commands used for this can be found below.

```
set firewall ipv4 name DMZ-to-WAN rule 1 action accept
set firewall ipv4 name DMZ-to-WAN rule 1 description 'Allow established'
set firewall ipv4 name DMZ-to-WAN rule 1 state established
```

Once these commands were issued and saved, the firewall rule listing for the DMZ to WAN firewall zone looked like the screenshot below where the new rule is present.&#x20;

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 2

To test this new rule, I created a very basic webpage called welcome.html that will server as the home page for the test. As seen in the screenshot below I can access the webpage from rw01 now that the rule was in place.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Setting Up Other Firewalls

Now that the WAN to DMZ firewall is in a good spot, I moved onto the LAN to DMZ and vice versa. These are the third and fourth firewalls of the 6 that will be in the system on fw01. I started by establishing default rules and default logging using the same commands as for the WAN to DMZ firewall.&#x20;

<figure><img src="../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 3

Because of the default ruleset being to drop packets, my Wazuh server cannot communicate between the DMZ and the MGMT segment because there is no traffic allowed between the DMZ and the LAN which is between DMZ and MGMT so therefore it does not work. The screenshot below is from my VyOS logs showing the failed communications over the Wazuh ports on my network.

<figure><img src="../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

## Allowing Wazuh Through Firewalls

Now that all traffic is blocked through the firewalls, similarly to web01, I have to allow the traffic for Wazuh through my DMZ to my LAN and later down the line through my MGMT network once I get to the fw-mgmt VM.&#x20;

Since Wazuh has multiple ports that it uses for its controls I opted to establish a port group for the firewall rule that will have both port 1514 and 1515 in its definition.&#x20;

The syntax for adding this firewall rule with a port group can be seen below.

```
set firewall group port-group WAZUH port '1514'
set firewall group port-group WAZUH port '1515'

set firewall ipv4 name DMZ-to-LAN rule 10 action accept
set firewall ipv4 name DMZ-to-LAN rule 10 description "Wazuh agent communications wiht server"
set firewall ipv4 name DMZ-to-LAN rule 10 destination port group WAZUH
set firewall ipv4 name DMZ-to-LAN rule 10 destination address 172.16.200.10
set firewall ipv4 name DMZ-to-LAN rule 10 protocol tcp
```

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Now that wazuh traffic was allowed out, it is also important to allow established connections to allow traffic in. The following commands and configurations were made to allow this change.

```
set firewall ipv4 name LAN-to-DMZ rule 1 action accept
set firewall ipv4 name LAN-to-DMZ rule 1 description 'Allow established'
set firewall ipv4 name LAN-to-DMZ rule 1 state established
```

## Deliverable 4

The following screenshot is of my show firewall rule command showing that rule 1 has been created from LAN to DMZ to allow established connections.&#x20;

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Allowing LAN Traffic

Now that security based controls are in place, it is pertinent that the LAN employees who are on this network segment are allowed to browse the internet and make connections to the rest of the internet beyond the internal network.&#x20;

To complete this I added the following zones, rules, and paths to the VyOS firewall from LAN to WAN.&#x20;

```
set firewall ipv4 name WAN-to-LAN default-action drop
set firewall ipv4 name LAN-to-WAN default-action drop
set firewall ipv4 name WAN-to-LAN default-log 
set firewall ipv4 name LAN-to-WAN default-log
set firewall ipv4 name WAN-to-LAN rule 1 action accept
set firewall ipv4 name WAN-to-LAN rule 1 state extablished
set firewall ipv4 name LAN-to-WAN rule 1 action accept
```

Now that these configurations were made the users on LAN can access the internet through the WAN interface.

## Deliverable 5

The following screenshot proves that I am able to access the internet beyond my network on the workstation after configuring the firewall rules on the fw01-chris.

<figure><img src="../.gitbook/assets/image (171).png" alt=""><figcaption></figcaption></figure>

## Allowing LAN to DMZ

The LAN for the most part shouldn't really need into the DMZ except for port 80 for the web server for the entire LAN and port 22 for mgmt01 so that it can SSH into the web01 host and make configuration changes. The following commands were added to the LAN to DMZ firewall for this part of the lab.

```
set firewall ipv4 name LAN-to-DMZ rule 10 action accept
set firewall ipv4 name LAN-to-DMZ rule 10 destination port 80
set firewall ipv4 name LAN-to-DMZ rule 10 protocol tcp
set firewall ipv4 name LAN-to-DMZ rule 20 action accept
set firewall ipv4 name LAN-to-DMZ rule 20 destination port 22
set firewall ipv4 name LAN-to-DMZ rule 20 source address 172.16.150.10
set firewall ipv4 name LAN-to-DMZ rule 20 protocol tcp
```

And of course if we allow it one way we have to allow established connections back out the firewall from the DMZ side of things.

```
set firewall ipv4 name DMZ-to-LAN rule 1 action accept
set firewall ipv4 name DMZ-to-LAN rule 1 state established
```

## Deliverable 6

Now that these rules were implemented in the previous step my workstation is allowed to browse the webpage hosted on web01 by IP in the browser as seen below.

<figure><img src="../.gitbook/assets/image (172).png" alt=""><figcaption></figcaption></figure>

## Deliverable 7

Similarly to the last deliverable this is also to test the rules that I had just made using my firewall to allow ssh over port 22 from specifically mgmt01 to my DMZ. The screenshot below shows me attempting to log in as testwazuhafterfirewall which does not exist on my web host and Wazuh capturing this log and showing it in the web interface.

<figure><img src="../.gitbook/assets/image (173).png" alt=""><figcaption></figcaption></figure>

## Configuring fw-mgmt

This part of the lab was done in one big chunk so the configurations commands below are all of the commands that I have used on fw-mgmt in order to properly configure the firewall rules on the two interfaces that this VM has.&#x20;

```
set firewall zone LAN
set firewall zone MGMT
set firewall zone LAN member interface eth0
set firewall zone MGMT member interface eth1
set firewall ipv4 name LAN-to-MGMT default-action drop
set firewall ipv4 name MGMT-to-LAN default-action drop
set firewall ipv4 name LAN-to-MGMT default-log 
set firewall ipv4 name MGMT-to-LAN default-log

set firewall ipv4 name LAN-to-MGMT rule 10 action accept
set firewall ipv4 name LAN-to-MGMT rule 10 source port 1515
set firewall ipv4 name LAN-to-MGMT rule 10 protocol tcp
set firewall ipv4 name LAN-to-MGMT rule 20 action accept
set firewall ipv4 name LAN-to-MGMT rule 20 destination port 22,443
set firewall ipv4 name LAN-to-MGMT rule 20 source address 172.16.150.10
set firewall ipv4 name LAN-to-MGMT rule 20 destination address 172.16.200.10
set firewall ipv4 name LAN-to-MGMT rule 20 protocol tcp
set firewall ipv4 name LAN-to-MGMT rule 1 action accept
set firewall ipv4 name LAN-to-MGMT rule 1 state established
set firewall ipv4 name LAN-to-MGMT rule 40 action accept
set firewall ipv4 name LAN-to-MGMT rule 40 source port 1515
set firewall ipv4 name LAN-to-MGMT rule 40 protocol udp

set firewall ipv4 name MGMT-to-LAN rule 1 action accept
set firewall ipv4 name MGMT-to-LAN rule 1 state established
set firewall ipv4 name MGMT-to-LAN rule 10 action accept
set firewall ipv4 name MGMT-to-LAN rule 10 destination address 172.16.150.0/24
set firewall ipv4 name MGMT-to-LAN rule 20 action accept
set firewall ipv4 name MGMT-to-LAN rule 20 destination address 172.16.50.0/29
```

Once all of these things were configured, this segment of my network was able to complete a variety of things such as:

* Allows 1514,1515/tcp from LAN to wazuh
* Allows 443/tcp from mgmt01 on LAN to wazuh
* Allows 22/tcp from mgmt01 on LAN to wazuh
* Allows MGMT to initiate any connection to the LAN
* Allows MGMT to initiate any connection to the DMZ
* Allows established traffic back through the related firewalls

## Deliverable 8

The screenshot below is taken from my mgmt02 VM which, in order of the boxes from top to bottom of the screenshot, is pinging my other mgmt01 VM on the LAN, being denied access to the internet through the WAN, and being able to invoke a web request for the webpage on my web01 server on the DMZ.

<figure><img src="../.gitbook/assets/image (174).png" alt=""><figcaption></figcaption></figure>

## Deliverable 9

Below is the output of my "show firewall ipv4 zone" on my fw-mgmt VM

<figure><img src="../.gitbook/assets/image (175).png" alt=""><figcaption></figcaption></figure>

## Deliverable 10

Below is the output of my "show firewall name LAN-TO-MGMT" on fw-mgmt

<figure><img src="../.gitbook/assets/image (176).png" alt=""><figcaption></figcaption></figure>

## Deliverable 11

Below is the output of my "show firewall name MGMT-TO-LAN" on fw-mgmt

<figure><img src="../.gitbook/assets/image (177).png" alt=""><figcaption></figcaption></figure>

## Deliverable 12

The following screenshoit is after I had configured the mgmt firewall to allow communication voer port 1514 and 1515 to allow web to communicate with wazuh. The screenshto shows that wazuh can still log the incidents as they happen on web.

<figure><img src="../.gitbook/assets/image (178).png" alt=""><figcaption></figcaption></figure>
