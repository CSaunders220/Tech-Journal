# Lab #2: AD, vCenter, and SSO

## Overview

## Creating and Configuring a Domain Controller

### Installing a Windows Server VM

I started by creating a new VM for the windows server installation. In this case I gave it the settings as seen below. The VM is temporarily on the VM Network but will later, after sysprep, end up on the internal network.&#x20;

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

I started the Windows Server installation, chose the language for the installation, and then selected the type of windows server installation as seen below. For this lab, I had the option to select the windows server core installation but with the uncertainty of what is down the pipeline I opted to use the desktop experience installation.&#x20;

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Once selected, I chose a drive and created a new partition scheme for the fresh windows server install. This was automatic and I opted to have it auto partition the drive space for the install.&#x20;

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Once completed, the drive partitions were completed as seen below.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Once the install was completed, I was prompted for an admin credential. At this time I entered the setup mode and bypassed this by pressing ctl+shift+f3. This resulted in the following screen:

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>



### Using Sysprep and Base Image Creation

Now that the windows machine had completed its install and I had bypassed the administration login, it was time to begin sysprep. This process of finding updates takes a large amount of time depending on the network traffic. To begin, I ran sysprep and chose option 6 as seen below to start the windows update processes. I selected All for update types and then All for the returned available updates.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

I repeated this process until there were no longer any available updates.

