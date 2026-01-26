# Week #2: Variable Length Subnetting

## Lab 2-1: Subnet Design

This lab was a packet tracer lab where the goal was to configure subnets for a small enterprise network on a /16 network. My network for this was the 10.20.0.0/16 network. The following table is the subnetting table that I used to configure the network in packet tracer.

<table><thead><tr><th width="100">VLAN</th><th>VLAN_NAME</th><th width="99">Hosts Needed</th><th>Network</th><th width="106">Netmask</th><th>Router Address</th></tr></thead><tbody><tr><td>1</td><td>Management</td><td>250</td><td>10.20.11.0</td><td>/24</td><td>10.20.11.1</td></tr><tr><td>100</td><td>FacStaff</td><td>200</td><td>10.20.10.0</td><td>/24</td><td>10.20.10.1</td></tr><tr><td>110</td><td>Student</td><td>450</td><td>10.20.8.0</td><td>/23</td><td>10.20.8.1</td></tr><tr><td>130</td><td>StuLab1</td><td>35</td><td>10.20.12.0</td><td>/26</td><td>10.20.12.1</td></tr><tr><td>140</td><td>StuLab2</td><td>35</td><td>10.20.12.64</td><td>/26</td><td>10.20.12.65</td></tr><tr><td>200</td><td>StuWireless</td><td>900</td><td>10.20.0.0</td><td>/22</td><td>10.20.0.1</td></tr><tr><td>210</td><td>FSWireless</td><td>650</td><td>10.20.4.0</td><td>/22</td><td>10.20.4.1</td></tr></tbody></table>

The simple network in packet tracer was set up where there was an east and west upstream switch that divided the network into two primary segments with those segments being divided up onto the different VLANs as seen above.

NOTE: the wireless VLANs and networks were omitted from the packet tracer file and activity, they only exist in the VLSM table.

### Configuring VLANs on Cisco Switches

The following commands are used to set up and add the VLANs to the VLAN database on each switch. The commands were the ones used for setting up the first switch (East Edge 02) and were repeated with proper ports for the other switches and VLANs.&#x20;

```
enable
configure terminal
vlan 100
name FacStaff
vlan 110
name Student
vlan 130
name StuLab1
end
copy running-config startup-config
```

Once the VLANs existed in the VLAN database for each edge switch, it was time to tag the ports with the proper VLANs assignments with access ports (not trunk). Trunk will be used with the gigabit connections between the core switches and the edge switches. The following commands were used for the tagging of a VLAN on a port, thee commands were repeated for all ports across all VLANs for their proper assignment. These assignments were 4-12 for FacStaff on all edge switches, Student VLAN on ports 13-20 on all edge switches, StuLab1 on East-Edge-02 ports 20-24, and StuLab2 on West-Edge-02.

The commands for a single port for a single VLAN are as follows:

```
configure terminal
interface FastEthernet0/4
switchport access vlan 100
```

The commands for doing multiple ports at cone in sequence for the same VLAN are as follows (much more useful):

```
configure terminal
interface range FastEthernet 0/4-12
switchport access vlan 100
```

The following screenshot is the VLAN port tagging process with commands for the Ease-Edge-02 router for example. This shows the tagging for the Student, FacStaff, and StuLab1 VLANs and all of the commands for this process.

<figure><img src="../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>

Once all switches were tagged and configured properly, I assigned IP address to the hosts below the switches as appropriate based on the IP addressing and subnet table above.&#x20;

### VLAN Port Trunking on Cisco Hardware

Trunking on a Cisco switch using the command line is rather simple, the following commands can be used to set a port to trunk mode and then assign it a VLAN to trunk (or multiple if the command is repeated).

```
configure terminal
interface FastEthernet0/1
switchport mode trunk
switchport trunk allowed vlan add 100
exit
copy running-config startup-config
```

After running this, the designated port will not be set to trunk mode, to set a port to access mode, the following commands can be used instead.

```
configure terminal
interface FastEthernet0/1
switchport mode access
switchport access vlan 100
exit
copy running-config startup-config
```

### Routing on Cisco Multilayer Hardware

The packet tracer lab used multilayer switches for this use case and in order to get them to route properly between VLANs there needed to be some configurations made in order for the routing table to be populated. The following code can be used to add a single VLAN to the routing table and was repeated for all VLANs.&#x20;

```
configure terminal
ip routing
interface vlan 100
ip address 10.20.10.1 255.255.255.0
no shutdown
```

Once this was completed for all VLANs and the trunk ports on the core switches in the environment were completed the routing worked between different VLANs across different switches within the whole environment.&#x20;

## Issues

One massive issue encountered that too the longest time to troubleshoot was the fact that when configuring routing on the core switches for the environment, it is imperative that you add the final "no shutdown" command in order for the routing to work. Otherwise, there will be no routing at all whatsoever between your VLANs on this particular switch.&#x20;

Another issue that I had encountered during this lab was the fact that it is best practice going forward to cable things after assigning and configuring switches and services. I did not do this and ended up deleting and redoing the cabling multiple times across the lab and if it were not packet tracer this process would have been extremely tedious.&#x20;

