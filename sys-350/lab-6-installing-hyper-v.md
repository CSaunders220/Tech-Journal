# Lab #6: Installing Hyper-V

## Overview



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

## Configuring Virtual Networking

