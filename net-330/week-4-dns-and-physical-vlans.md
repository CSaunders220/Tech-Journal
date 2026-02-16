# Week #4: DNS and Physical VLANs

## What is DNS

DNS is the service that provides a hostname to IP address translation for humans to be able to better understand the internet as a whole. For example, the hostname google.com is simply a name given to the IP address for a server that is hosting the google web browser pages.&#x20;

Setting up a DNS server is rather easy because all that is needed is simply the desired hostname or reachable domain name for the server and the server's IP address in order to make an A record. Other records like MX and Pointer records can be more complex but not by much.

## Accessing Cisco Switches via Console

To access a cisco switch via putty, I used the following steps:

* Opened putty as an administrator
* Connected the serial console cable from the desktop's USB to the console port on the switch.
* Used the following settings on the putty console connection window:
  * Serial Line to Connect: COM port from Step 2
  * Bits per sec : 9600
  * Data bits : 8
  * Parity : none
  * Stop bits : 1
  * Flow control : none
* Confirm the connection and enter the console

If the switch had previous configurations, which my group's one did, I used the following steps and methods to reset the switch:

* Hold the mode button on the front of the switch
* Wait 15 seconds until you are able to select the flash\_init option from a presented menu
* type the flash\_init command
* type dir flash: to view the contents
* Type del flash:config.text
* Type del flash:vlan.dat
* Now that the config and VLAN database was wiped, type boot to reload the switch and N for entering setup

## Configuring VLANs on Cisco Hardware

The following series of commands are used to do various VLAN actions on cisco hardware that we had used in this last hardware lab.

### Setting up a VLAN

```
Router(config)# interface gigabitethernet 0/0.XXX
Router(config-subif)# encapsulation dot1Q XXX
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# exit
```

### Setting up Access Ports

```
Switch> enable
Switch# configure terminal
Switch(config)# interface fastethernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan XX
Switch(config-if)# exit
```

### Setting up Trunk Ports

```
Switch> enable
Switch# configure terminal
Switch(config)# interface gigabitethernet 0/1
Switch(config-if)# switchport trunk encapsulation dot1q
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan XXX
Switch(config-if)# no shutdown
Switch(config-if)# exit
```
