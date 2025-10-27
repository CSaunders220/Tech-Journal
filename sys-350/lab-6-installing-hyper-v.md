# Lab #6: Installing Hyper-V

## Overview

This lab will cover the installation of windows server 2019, the installation of a Hyper-V instance on that windows server, the creation of virtual networking in a Hyper-V environment, the creation of a vyos router for my internal network, and the creation of a Windows 11 workstation VM inside of my network.&#x20;

## Imaging Super11 with Windows Server

To start, the image for Windows Server 2019 had to be booted to the server before any other changes could be made. This would be directly overwriting the ESXI install that was on there from the first half of the class. I started by selecting the language and keyboard layout:

<figure><img src="../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

Next, I begun the installation process. For the purposes of this lab and the class, we will all be using the desktop experience so I selected this from the list of options.

<figure><img src="../.gitbook/assets/image (104).png" alt=""><figcaption></figcaption></figure>

Next, I had to specify the drive for the install of Windows and clear out all the old ESXI partitions. Once completed, I initialized a new Windows NTFS partitioned space on the larger of the two drives I had in my environment and used the entire disk for this space as seen below.

<figure><img src="../.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>

After a brief install period and a computer restart, the Windows Server 2019 was successfully installed onto my SuperMicro server.

With windows installed, I quickly switched over the IP address to be my proper IP assignment for the server (192.168.3.211) and changed all of the other networkign related assignments.

<figure><img src="../.gitbook/assets/image (106).png" alt=""><figcaption></figcaption></figure>

With networking straightened out, I also quickly installed Chrome to set up CRD. This step is not required but it is in my best interest for simplicity to avoid using the VPN to access my server.&#x20;

<figure><img src="../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

Now that I can access my server remotely, I also went in and changed the storage configuration for the server. Since I have two drives I decided to format the partitions in a way that there was a VM storage that was considerable large on the primary drive and then a dedicated local storage for any local scripts, files, or ISOs.

<figure><img src="../.gitbook/assets/image (109).png" alt=""><figcaption></figcaption></figure>

## Installing Necessary Services

To start installing features, I navigated to the server manager page and used the manage menu and selected to add roles and features.&#x20;

<figure><img src="../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>

From the list of available roles for the server, I added the Hyper-V role which also will install some additional tools like RSAT and Hyper-V Management tools.

<figure><img src="../.gitbook/assets/image (111).png" alt=""><figcaption></figcaption></figure>

Hyper-V, when installing it for the first time on a server, needs a virtual switch to act as the primary access point for the VMs that will be on the server. This can be configured at the time of install. Since my server only has one uplink to the main network and therefore only one active network adapter, this would be the primary line for any of my VM connections. A firewall will later be added as a VM so that the rest of my infrastructure can have DHCP.&#x20;

<figure><img src="../.gitbook/assets/image (112).png" alt=""><figcaption></figcaption></figure>

For the sake of the course, live VM migration was foregone because there will only ever be the one Hyper-V instance per student and there is no need for migration between hosts at this time.&#x20;

The default storage for the VM disks and the configuration files can also be configured at this point and I set these to be on the VM Storage partition that I had previously configured.&#x20;

<figure><img src="../.gitbook/assets/image (113).png" alt=""><figcaption></figcaption></figure>

Lastly, for the installation, I selected restart if needed and then begun the install after confirming my changes.

<figure><img src="../.gitbook/assets/image (114).png" alt=""><figcaption></figcaption></figure>

After the installation completed and the computer had restarted, upon opening server manager again the left hand menu bar now had the option to manage and observe the Hyper-V service on the local machine.&#x20;

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

I also took one last moment to ise sconfig to make sure that my windows updates were disabled, that my hostname was accrate, and that the remote desktop protocol was enabled and allowed.

<figure><img src="../.gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>

## Configuring Virtual Networking

Hyper-V uses virtual switches just the same as any other hypervisor to manage the network connections of the VMs within the infrastructure. Because the server blade that I am using only has one active ethernet that is connected, the virtual switch will treat this as an uplink and forward all traffic through that and switch between all the VMs in the infrastructure. The settings for my WAN virtual switch that I set can be found below.

<figure><img src="../.gitbook/assets/image (117).png" alt=""><figcaption></figcaption></figure>

I will also need a LAN for my network to connect to and for my future vyos firewall so I also have created an internal network only virtual switch as seen below.

<figure><img src="../.gitbook/assets/image (118).png" alt=""><figcaption></figcaption></figure>

## Deliverable 1

The screeshot below is of my local server manager view from the server manager application showing the hosntmae of the system set to super11, the remote desktop and remote management features enabled, and the two new network adapters for the Hyper-V WAN and the LAN-INTERNAL.

<figure><img src="../.gitbook/assets/image (120).png" alt=""><figcaption></figcaption></figure>

## Installing WAC

WAC, or Windows Admin Center, is a service that allows the user to manage a server from a web interface that is hosted somewhere in the infrastructure. In this case, this will be hosted on the local host and accessed form the local host. The first step for downloading the WAC was to actually get the packages from the internet over Chrome by going to [https://aka.ms/WACDownload](https://aka.ms/WACDownload)

Navigating to this immediately starts the download for the .exe file needed.

<figure><img src="../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>

Once downloaded, I ran the program to begin the wizard for installation. Here, I accepted the terms and agreements and then it began to install the needed installation files. For simplicity sake, I opted to use the Express Setup when prompted but ins some environments a custom setup may be advised.&#x20;

<figure><img src="../.gitbook/assets/image (122).png" alt=""><figcaption></figcaption></figure>

I then generated a TLS certificate that would expire in 60 days but until then would allow for secure encrypted traffic. I also disabled automatic updates at this stage as well just to not bork the environment down the road when an update decides to ruin it all. Once completed, the final install begun with the final configurations.

After a good chunk of time, the WAC administration portal was accessible via going to https:/localhost:6516 and the home page can be seen in the screenshot below.



Once installed, I went over to the settings, the extensions page, and then to available extensions as seen below.

<figure><img src="../.gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>

I then selected and downloaded the latest version of the virtual machines and switches extension for WAC. This will give me the availability to manage the virtual machines on Hyper-V from any windows machine accessible in the network providing it can reach my server IP. After the page auto refreshed, the two new features were accessible in the sidebar menu for the server to manage.

## Deliverable 2

Below is a screenshot proving that I can manage my virtual machines and switches from the newly installed WAC on the server from my chrome browser.

<p align="center"><img src="../.gitbook/assets/image (125).png" alt=""> </p>

## Installing a Virtual Router

I chose of my own volition to use vyos for this project even though this router is more than necessary for the environment but I wanted to get more interaction and usage with this OS for the sake of practice so I opted for installing vyos over installing pfsense for the main router in my environment.&#x20;

Since it is a router and a rather lightweight one at that, I gave the VM only 1GB or memory and 2 virtual processors. This VM also has two networking links, one for the WAN and another for the LAN. The storage for the machine is also rather low only at 32 GB since it is a small install.&#x20;

<figure><img src="../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>

Once all of the configurations were made, I booted the VM to begin the VYOS install and configuration.&#x20;

## VYOS Configuration

Vyos, unlike PFSense, is strictly command line based and uses its own "dialect" of debian based linux commands. A lot of the vyos commands follow a verb noun tree that allows the user to interact with specific services and ports. Below is a complete list of the startup commands and configurations that I made on my VYOS router to allow networking. I added a few extra modules in the case that we need them later down the line. For now, this is the basic shell for the router that will be in my environment.&#x20;

```
install image

---------------------------------------------------------------------------------------

set interfaces ethernet eth0 address '192.168.3.21/24'
set interfaces ethernet eth0 description 'WAN'
set interfaces ethernet eth1 address '10.0.17.2/24'
set interfaces ethernet eth1 description 'LAN'

set service dhcp-server shared-network-name LAN subnet 10.0.17.0/24 option default-router '10.0.17.2'
set service dhcp-server shared-network-name LAN subnet 10.0.17.0/24 option name-server '10.0.17.2'
set service dhcp-server shared-network-name LAN subnet 10.0.17.0/24 lease '86400'
set service dhcp-server shared-network-name LAN subnet 10.0.17.0/24 range 0 start '10.0.17.100'
set service dhcp-server shared-network-name LAN subnet 10.0.17.0/24 range 0 stop '10.0.17.150'
set service dhcp-server shared-network-name LAN subnet 10.0.17.0/24 subnet-id '1'

set service dns forwarding listen-address '10.0.17.2'
set service dns forwarding allow-from '10.0.17.0/24'
set service dns forwarding name-server 192.168.4.4
set service dns forwarding name-server 192.168.4.5

set nat source rule 10 description 'NAT FROM LAN TO WAN'
set nat source rule 10 outbound-interface name 'eth0'
set nat source rule 10 source address '10.0.17.0/24'
set nat source rule 10 translation address masquerade

set protocols static route 0.0.0.0/0 next-hop 192.168.3.250

set system conntrack modules ftp
set system conntrack modules h323
set system conntrack modules nfs
set system conntrack modules pptp
set system conntrack modules sip
set system conntrack modules sqlnet

set system host-name 'fw01-chris'
set system name-server '10.0.17.2'
```

## Installing Windows 11 VM

For the install of the new Windows 11 VM, I used a different method of getting the VM working than what I did with the router. As opposed to downloading an ISO from a downloads page for a distro or from the Windows boot mead creation tool, I instead used a preconfigured and premade VHDX file and built the VM around it. I downloaded the virtual disk file from the link provided in the lab and then created a VM with the following settings where the VHDX file was the given drive.

<figure><img src="../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

After booting the device, I allowed it to take a moment to receive a DHCP address from the vyos router and then I was able to create a new named user, and reboot to confirm the new user. Once logged back in, I installed chrome and was able to view the Windows 11 desktop of the VM as seen below.

<figure><img src="../.gitbook/assets/image (128).png" alt=""><figcaption></figcaption></figure>

## Deliverable 3

Below is a screenshot taken from the command line of my Windows 11 VM in my Hyper-V infrastructure where the first box at the top is the RDP window title showing I am in the workstation machine on my Hyper-V server, the second box to the top is the current logged user which is my local administrator profile, the next box down is the ip settings for my machine including the current DHCP received address of 10.0.17.101 as well as the default gateway being set to my vyos router, and the last box at the bottom is the successful ping to champlain.edu.

<figure><img src="../.gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>
