# Week #5: Small Enterprise Class Lab

## Subnet Table

The table below is the VLAN table for this week's class lab. For this lab the set of IP addresses that the enterprise owns is 10.7.0.0/8 where 7 is my workstation number in the IRE017 classroom.&#x20;

<table><thead><tr><th>Segment and Hosts</th><th>CIDR</th><th>Start IP</th><th width="114">End IP</th><th width="166">Netmask</th></tr></thead><tbody><tr><td>Clinic (300)</td><td>/23</td><td>10.7.0.1</td><td>10.7.1.254</td><td>255.255.254.0</td></tr><tr><td>Visitor (300)</td><td>/23</td><td>10.7.2.1</td><td>10.7.3.254</td><td>255.255.254.0</td></tr><tr><td>Office (300)</td><td>/23</td><td>10.7.4.1</td><td>10.7.5.254</td><td>255.255.254.0</td></tr><tr><td>Counseling (150)</td><td>/24</td><td>10.7.6.1</td><td>10.7.6.254</td><td>255.255.255.0</td></tr><tr><td>Datacenter / Default VLAN (150)</td><td>/24</td><td>10.7.7.1</td><td>10.7.7.254</td><td>255.255.255.0</td></tr></tbody></table>

## Network Layout and Design

For this lab we are using only 1 distribution layer so instead of having multiple routing layers before the edge switches I simply will have one upstream switch that will be acting as a router through the VLANs instead of having an entirely separate distribution layer. (Border,Core,Edge vs Border,Core,Distribution,Edge)

Each of the different departments will have a separate edge switch with the counseling and south wing sharing a core switch. The North core is only servicing the North Edge switch and the Datacenter is in the middle with its own network segment.&#x20;

The network, without hosts, can be seen below.

<figure><img src="../.gitbook/assets/image (180).png" alt=""><figcaption></figcaption></figure>

## Setting Up Router

The following commands were used to create, enable routing, and set up IP addresses for each of the VLANs on the hospital router.

```
configure terminal
vlan 1
name default
vlan 100
name Clinic
vlan 110
name Visitor
vlan 120
name Office
vlan 130
name Counseling
ip routing
interface vlan 100
ip address 10.7.0.1 255.255.254.0
no shutdown
exit
interface vlan 110
ip address 10.7.2.1 255.255.254.0
no shutdown
exit
interface vlan 120
ip address 10.7.4.1 255.255.254.0
no shutdown
exit
interface vlan 130
ip address 10.7.6.1 255.255.255.0
no shutdown
exit
interface vlan 1
ip address 10.7.7.1 255.255.255.0
no shutdown
exit
interface FastEthernet 0/1
switchport mode trunk
switchport trunk allowed vlan add 1
switchport trunk allowed vlan add 100
switchport trunk allowed vlan add 110
switchport trunk allowed vlan add 120
exit
interface FastEthernet 0/2
switchport access vlan 1
interface FastEthernet 0/3
switchport mode trunk
switchport trunk allowed vlan add 1
switchport trunk allowed vlan add 100
switchport trunk allowed vlan add 110
switchport trunk allowed vlan add 120
switchport trunk allowed vlan add 130
```

## Configuring Core Switches

The North and South Core switches are virtually the same with the exception that the south core switch will have VLAN 130 in its database because the counseling network is on the south wing.&#x20;

### North Core

The configurations made to the North switch and the commands to do so can be found below.

```
configure terminal
vlan 100
name Clinic
vlan 110
name Visitor
vlan 120
name Office
interface FastEthernet 0/1
switchport mode trunk
switchport trunk allowed vlan add 100
switchport trunk allowed vlan add 110
switchport trunk allowed vlan add 120
exit
interface FastEthernet 0/2
switchport mode trunk
switchport trunk allowed vlan add 1
switchport trunk allowed vlan add 100
switchport trunk allowed vlan add 110
switchport trunk allowed vlan add 120
exit
```

### South Core

Similar to the north switch, the configuration setup is the exact same provided that allof my hosts and switches are connected to the same ports, which they are.

```
configure terminal
vlan 100
name Clinic
vlan 110
name Visitor
vlan 120
name Office
vlan 130
name Counseling
interface FastEthernet 0/1
switchport mode trunk
switchport trunk allowed vlan add 100
switchport trunk allowed vlan add 110
switchport trunk allowed vlan add 120
exit
interface FastEthernet 0/2
switchport mode trunk
switchport trunk allowed vlan add 1
switchport trunk allowed vlan add 100
switchport trunk allowed vlan add 110
switchport trunk allowed vlan add 120
exit
interface FastEthernet 0/3
switchport mode trunk
switchport trunk allowed vlan add 1
switchport trunk allowed vlan add 130
exit
```

### Datacenter Core

The datacenter core switch is likely the easiest of all of the switches to configure since it will only use the default VLAN 1 for the network segment so therefore no trunking is needed on this segment on either side of the core switch.&#x20;

```
configure terminal
vlan 1
name default
interface FastEthernet 0/1
switchport access vlan 1
interface FastEthernet 0/2
switchport access vlan 1
interface FastEthernet 0/3
switchport access vlan 1
```

## Configuring Edge Switches

The North and South edge switches are also virtually the same in the sense that the north and south will both have the clinic, visitor, and office VLAN access but the south will have the counseling center as well.&#x20;

### North Edge

The configurations made to the north edge switch can be found below. These establish the access ports for all of the three VLANs that need access on this switch (not counseling).&#x20;

```
Switch(config)#interface range FastEthernet 0/19-24
Switch(config-if-range)#switchport access vlan 120
Switch(config-if-range)#exit
Switch(config)#interface range FastEthernet 0/15-18
Switch(config-if-range)#switchport access vlan 110
Switch(config-if-range)#exit
Switch(config)#interface range FastEthernet 0/9-14
Switch(config-if-range)#switchport access vlan 100
Switch(config-if-range)#exit
Switch(config)#interface FastEthernet0/1
Switch(config-if)#switchport mode trunk
```

### South Edge

The configurations made to the south edge switch can be found below. These establish the access ports for all of the three VLANs that need access on this switch

```
Switch(config)#interface range FastEthernet 0/19-24
Switch(config-if-range)#switchport access vlan 120
Switch(config-if-range)#exit
Switch(config)#interface range FastEthernet 0/15-18
Switch(config-if-range)#switchport access vlan 110
Switch(config-if-range)#exit
Switch(config)#interface range FastEthernet 0/9-14
Switch(config-if-range)#switchport access vlan 100
Switch(config-if-range)#exit
Switch(config)#interface FastEthernet0/1
Switch(config-if)#switchport mode trunk
```

### Counseling Edge

The configurations made to the north edge switch can be found below. These establish the access ports for all of the single VLAN that is on this switch (counseling).

```
Switch(config)#interface range FastEthernet 0/9-14
Switch(config-if-range)#switchport access vlan 130
Switch(config-if-range)#exit
Switch(config)#interface FastEthernet0/1
Switch(config-if)#switchport mode trunk
```

## DHCP

These two services will exist on the datacenter core switch on separate servers.

### DHCP Server

The DHCP server will host pools for each of the different departments of the hospital with the exception of VLAN 1 (datacenter) since everything in the datacenter will be statically set. The DHCP pool configurations for each of the different network segments can be seen in the screenshots below.&#x20;

#### Clinic

<figure><img src="../.gitbook/assets/image (181).png" alt=""><figcaption></figcaption></figure>

#### Visitor

<figure><img src="../.gitbook/assets/image (182).png" alt=""><figcaption></figcaption></figure>

#### Office

<figure><img src="../.gitbook/assets/image (183).png" alt=""><figcaption></figcaption></figure>

#### Counseling

<figure><img src="../.gitbook/assets/image (184).png" alt=""><figcaption></figcaption></figure>

### DHCP Settings on Router

Since DHCP needs to be seen throughout the entire network, it is important to go into the router and set the DHCP server's static IP address as a helper address.&#x20;

The commands below were issued on the router to achieve this goal.&#x20;

```
interface vlan 100
ip helper-address 10.7.7.2
exit
interface vlan 110
ip helper-address 10.7.7.2
exit
interface vlan 120
ip helper-address 10.7.7.2
exit
interface vlan 130
ip helper-address 10.7.7.2
exit
```

### DHCP In Action

Once these settings were properly configured, I added some hosts to the three VLANs that are on the North Edge switch and set their configurations to be on DHCP. (Important to put these hosts on the right access ports).&#x20;

#### Clinic Host 1

<figure><img src="../.gitbook/assets/image (185).png" alt=""><figcaption></figcaption></figure>

#### Visitor Host 1

<figure><img src="../.gitbook/assets/image (186).png" alt=""><figcaption></figcaption></figure>

#### Office Host 1

<figure><img src="../.gitbook/assets/image (187).png" alt=""><figcaption></figcaption></figure>

#### Counseling Host 1

<figure><img src="../.gitbook/assets/image (193).png" alt=""><figcaption></figcaption></figure>

## Proof of Routing

Now that DHCP was set up and enabled for all VLANs and interfaces, I copied my configurations from my north to the south switches and verified the proper ports were set for access and trunking. Once completed, I added three more hosts, one per VLAN, so that now I can ping between hosts on the north segment and the south segment.&#x20;

The screenshot below is of my Clinic 1 host (10.7.0.2) on the north segment pinging my Clinic 2 host (10.7.0.3) on the south segment.

<figure><img src="../.gitbook/assets/image (188).png" alt=""><figcaption></figcaption></figure>

Similarly, the screenshot below is from my visitor 1 host pinging my office 2 host which is between VLANs (Visitor to Office) and also between buildings in my network (North to South).

<figure><img src="../.gitbook/assets/image (192).png" alt=""><figcaption></figcaption></figure>

## DNS

TO start with the DNS service, I first networked my server that will host the service on the datacenter network. I gave it the IP address 10.7.7.3/24 and assigned its proper default gateway. Once that was done I added the DHCP server and the DNS server to the host records on the DNS service and enabled the service.&#x20;

Below is a screenshot of the DNS configurations made in these first steps of the lab.&#x20;

<figure><img src="../.gitbook/assets/image (189).png" alt=""><figcaption></figcaption></figure>

### Testing on Host PCs

Now that the DNS server existed, I went back in and changed the DHCP service to assign an IP address for the DNS server in its leases and then tested the name resolution for both the DHCP and DNS servers on my hosts.&#x20;

The screenshot below show the results from my client on the clinic segment resolving the hostname of the DNS server

<figure><img src="../.gitbook/assets/image (190).png" alt=""><figcaption></figcaption></figure>

Similarly, the screenshot below shows the same host resolving the DHCP server.

<figure><img src="../.gitbook/assets/image (191).png" alt=""><figcaption></figcaption></figure>

## Final Network Layout

After making all configurations and adding hosts to the network, my final layout can be seen in the screenshot below.&#x20;

<figure><img src="../.gitbook/assets/image (194).png" alt=""><figcaption></figcaption></figure>

## Issues

I encountered no issues during the process of this lab. This was kind of expected since the only new thing that was introduced to the lab was configuring a DNS server on the network and everything else is similar to past activities.&#x20;
