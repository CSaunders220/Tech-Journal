# Lab #1: VSphere ESXi Installation

## Overview



## Accessing via IPMI



## Installing the ESXi host

In this case, I was installing the bootable files via USB drive so we started by flashing the ISO to a USB via Rufus. Once that was completed and the networking on the physical server was completed, I plugged in the USB into the server blade and restarted the server leading to the automatic start of the install. Once completed, the boot installation started as shown by the screenshot below:

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Once the core services and tools were installed, it came time to select a disk for the install of the boot partition. Below I have a screenshot showing the options for the selection of which disk to install onto. In this case, I selected the 500 GB internal drive and left the TB drive for the datastore that will be used later.&#x20;

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

After selecting which drive to partition and format for install, I set a root password for the root account of the new ESXi.&#x20;

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Once all configurations had been made, the installation was ready to start as shown below. After a chunk of time, the server was ready to reboot after removing the USB from the server.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Finally, once all of the steps for the initial install for the ESXI onto the server blade had been completed it prompted for a confirmation of actions and finally started a reboot of the system to proceed with the installation as seen above.&#x20;

## Configuring Host Networking

Once the install of the ESXi image had completed I first removed the USB installation media from the physical server blade. Then, I began the post install configrations starting with the networking.&#x20;

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

First, I selected the network adapter that I wanted to use as the default outbound network connection for the server itself, this will later functionally become the "VM Network" and is the direct link to the Freeman lab and cyber.local network.&#x20;

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Once that was selected, I chose to edit the IP settings for the machine and filled in the settings as seen above. The IP of the machine is the assigned HOST network assignment, not the IPMI interface network assignment, and the default gateway was simply the Freeman lab default gateway IP.&#x20;

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Next, I set the defauly DNS configurations to point towards the cyber.local DNS servers, one as a primary and the other as the secondary. I also changed the hostname of the machine at this stage to the assigned superX number, in this case my server is super11.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Lastly, I changed the default domain to be cyber.local to assist with the hostname resolution later down the line and have the default be more easily accessible.&#x20;

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Once completed, I accepted the network configurations, allowed it to finalize, and then accessed my ESXi host from my browser via IP as seen in the screenshot above.&#x20;

## Deliverable 1

The following screenshot is my submission for Deliverable 1, it is the home screen after login in with the set root password in order to see my VM dashboard before configuring anything on the ESXi.&#x20;

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

## Adding/Creating Datastores

To configure and add the datastores, you first have to login to the ESXi portal via IP in the browser. Once that is done, on the homepage, select the storage menu.

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Inside of the storage menu, at the center of the screen there is a listing of all of the datastores that are attached to the machine. In my case, there was one unconfigured SSD in my machine and the one started datastore that I had installed the ESXI onto. I started by renaming the datastore by right clicking the first datastore and the selecting rename. I then set it to the apropriate name.

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Next, I wanted to add the second SSD as a secondary datastore on the ESXi host. To do this, I hit the new datastore button on that datastore default screen as seen below.&#x20;

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

Next, I followed the wizard to add the new datastore to my ESXI host. I first selected to create a new VMFS datastore.

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

I then selected the second Sanmsubng SSD (1TB) that I had inside of the server and added that as a datastore target. I also gave it the name datastore2-super11 at this stage to match the naming scheme for the datastores.&#x20;

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

I then waited for it to scan the disk to see how much available storage there was and in this case it was empty. I configured it to use the entire disk with this new partition and chose the VMFS 6 scheme since it was the most recent and up to date. This is the same partitioning scheme that the other datastore followed as well by default.&#x20;

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

ofr the last step of the wizard, I confrmed all of my selections before it formatted and partitioned the new drive as a secondary datastore.&#x20;

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

Once complete, the new datastore appeared in the menu as a storage location. I then used the create directory button to make a new folder called ISOs, uploaded the PFsense and Xubuntu ISOs to the new datastore from my local machine by selection upload, and then allowed them to complete uploading to the ESXi.&#x20;

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

## Deliverable 2

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

Above and below are my submissions for Deliverable 2. In the screenshot above I have captured a screenshot of the two datastores as how they appear in my ESXI host from the web portal. I have both Datastore1-super11 and Datastore2-super11 which are my two internal SSDs attached to my server.

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

Above is the screenshot of my ISOs folder on datastore 2. This is a new directory that I created, uploaded the two ISOs for PFsense and Xubuntu, and stored.&#x20;

## Creating Virtual Networking

Once my datastores were configured and set up, I had to create a virtual network for my soon to be created VMs to exist on so that they can reach the outbound internet. This step entailed creating both a virtual switch and a virtual port group for the new connections.&#x20;

### Creating a Virtual Switch

To create my virtual switch, I started by navigating to the networking tab of my ESXi home page on the left right underneath storage. I then opened the virtual switches menu as seen below.&#x20;

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

Once opened, I selected the option to create a new Virtual Switch.

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

Once selected, in the popup menu for the new virtual switch I gave it the name 350-internal and kept the default MTU. Once done, I added it to my list of virtual switches as shown below.

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

### Creating a Port Group

Now that I had a virtual switch, I needed to create a virtual port group for my virtual network. To do this, I navigated back to the networking page and opened the port groups menu as seen below.&#x20;

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

Similarly to before, I selected to add a port group and the new popup menu for configuration appeared. I gave my new port group the name 350-internal, kept the VLAN ID at 0, and added it to the 350-internal virtual switch I created before.&#x20;

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

Once completed, I had three total port groups. Two of the default ones that were configured before while making the ESXi and the one new 350-internal port group on the 350-internal virtual switch as seen below.

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

## Deliverable 3

Below are my submissions for Deliverable 3. This deliverable outlines the virtual networking and that i meed the requirements for this milestone. Below I have screenshotted the two virtual switches, the one I created and the default switch, as seen from my switches menu.&#x20;

<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

Next, below is a screenshot of the virtual port groups that I have on my ESXI that includes the two default ones and the new 350-internal port group.

<figure><img src="../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

## Installing VMs

The next and final part of the lab was to install and begion the process of populating my environment with VMs. For this lab, it was simply a PFSense VM and a management box that is running XUbuntu.&#x20;

### PFSense VM Install

To start the installation of PFsense ontoa  VM i needed to create a VM first. To do this, I started at the Virtual Machines menu and selected Create/register a VM.&#x20;

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

From here, I followed the wizard to install the first VM onto the system as seen below by selecting the option to create a new virtual machine.

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

I then gave the new VM a name, chose the compatibility level of the VM

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>



### Xubuntu Install

<figure><img src="../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>



## Configuring VMs

### PFSense Configurations

<figure><img src="../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>







### Xubnutu Configurations



<figure><img src="../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>



## Deliverable 4



<figure><img src="../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

## Reflection

