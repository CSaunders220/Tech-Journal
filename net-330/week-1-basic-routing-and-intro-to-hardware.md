# Week  #1: Basic Routing and Intro to Hardware

## Lab 1-1: Basic Hardware Lab

This was the first lab where physical hardware was used and in this case it was a cisco router and two simple switches connected to two end host devices.&#x20;

### Setting Up Putty

Putty is a common remote access tool that allows one to either ssh, serial, or connect via other methods into another host, and in this case we had used it to connect to the router for the activity. The following settings were needed in order to console into the router via the USB serial cable and putty:

* Connect the serial line to the host and find out which port it is connected to via device manager
* Set the bits per second to 9600
* Set the data bits to 8
* Set parity to none
* Set stop bits to 1
* Set flow control to none
* Set the connection type on the main menu to serial.

Once these steps are completed, pressing connect on the putty interface should result in a new window being opened with the routers console available.&#x20;

### Resetting a Cisco Router Password

The first thing that needed to happen on the router after booting it was to reset whatever old password was on the device to a new password for the purpose of the lab. From the documentation guide, these were the steps that we as a group took to reset the router password on boot:

* As router starts to boot send a break signal (Ctrl-Break on Putty)
* Type confreg 0x2142  This sets a bit in the router to bypass the startup configuration
* Type reset to reboot the router.&#x20;
* Respond no to all startup questions
* Type enable  to get into privileged mode
* copy startup-config running-config
* configure terminal
* enable secret <_aSecretPassword>_
* config-register 0x2102
* copy running-config starup-config

### Configuring Routing on Cisco Hardware

Once we were able to reset the password on the router and access it reliably, we wired the two kali workstations to the two provided switches and then to the router. Once the physical cabling was done we enabled routing on the router and gave the following commands:

```
enable
configure terminal
interface [interface-name]
ip address [ip-address] [subnet-mask]
no shutdown
end
write memory
```

In order from top to bottom these commands enabled routing, entered into configuration mode, selected an interface to configure (we repeated this command for both interfaces as needed), set an IP address and a subnet mask for that interface to route on, enabled the port, and then exited the interface configuration mode, and saved the configuration to the router.&#x20;

Once executing these commands for both interfaces on the router, ensuring the cabling was correct, and verifying access to the Kali USB bootable, we were able to then route between the two subnets that we just created for both workstations.

One key command that assisted in this process was the "ip interface brief" command which is similar to the VyOS equivalent of show int in the sense that it lists all available interfaces and their configurations, IP address settings, and their statuses.&#x20;

## Issues

First and foremost, when connecting Putty from the workstation to the router we ran into issues where the configurations were made on putty but the mode was not set to serial on the home page of putty so the connection would not sync.

Secondly, when saving the configs from the running config after making configurations to the startup configs, we realized that we had to exit the terminal configuration mode in order to save them properly.

Next, we could not ping the router from either machine. This issue was due to not turning on the ports for the ethernets on the router and setting the IP addresses for the interfaces. Without doing that, the interfaces were reporting that they were “administratively down” in the interface list.

Lastly, the final issue that we had when completing the lab was that when adjusting the uplink cable from the router to the switch on one of the workstations, the cable from the NUC to the switch came loose and the link lights were down. Once the cable was fixed, the ping command worked successfully.
