# Lab #2: AD, vCenter, and SSO

## Overview

This lab covers a lot of the housekeeping for our future vCenter environments including setting up a domain controller, setting up a VCSA machine, and setting up SSO between the DC and our vCenter machines for later use.

## Creating and Configuring a Domain Controller

### Installing a Windows Server VM

I started by creating a new VM for the windows server installation. In this case I gave it the settings as seen below. The VM is temporarily on the VM Network but will later, after sysprep, end up on the internal network.&#x20;

<figure><img src="../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>

I started the Windows Server installation, chose the language for the installation, and then selected the type of windows server installation as seen below. For this lab, I had the option to select the windows server core installation but with the uncertainty of what is down the pipeline I opted to use the desktop experience installation.&#x20;

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Once selected, I chose a drive and created a new partition scheme for the fresh windows server install. This was automatic and I opted to have it auto partition the drive space for the install.&#x20;

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Once completed, the drive partitions were completed as seen below.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Once the install was completed, I was prompted for an admin credential. At this time I entered the setup mode and bypassed this by pressing ctl+shift+f3. This resulted in the following screen:

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



### Using Sysprep and Base Image Creation

Now that the windows machine had completed its install and I had bypassed the administration login, it was time to begin sysprep. This process of finding updates takes a large amount of time depending on the network traffic. To begin, I ran sysprep and chose option 6 as seen below to start the windows update processes. I selected All for update types and then All for the returned available updates.

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I repeated this process until there were no longer any available updates. Once completed, I set the updates to manual by selecting option 5 and then setting the update property to manual only meaning that in the future Windows will no longer search automatically for updates as shown below. While here, I also set the time zone to Eastern to match the rest of the systems in my environment.&#x20;

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Before sysprepping, I also wen ahead and installed the VMware tools on this machine by navigating back to my ESXI interface, selecting the VM, Actions -> GuestOS -> Install VMWare Tools. This mounted a new drive to my windows machine as seen below with the following contents. Here I wan the setup64 application for this specific instance.

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Once downloaded, I ran and installed VMware tools on the machine.&#x20;

Once the system was completely prepped and ready for sysprepping, I followed the script as provided and as shown below. Credits for the script go to the GitHub user GMCyber. This script was given to the class as a tool to use in the process of prepping the machines.&#x20;

```
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
Start-Service sshd
# a good time to complete via remote ssh
Set-Service -Name sshd -StartupType 'Automatic'
Set-ItemProperty "HKLM:\Software\Microsoft\Powershell\1\ShellIds" -Name ConsolePrompting -Value $true
New-ItemProperty -Path HKLM:\SOFTWARE\OpenSSH -Name DefaultShell -Value "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -PropertyType 
String -Force
Write-Host "Create a deployer user: Enter Password"
$pw = Read-Host -AsSecureString
New-LocalUser -Name deployer -Password $pw -AccountNeverExpires -PasswordNeverExpires:$true
Add-LocalGroupMember -Group Administrators -Member deployer
Write-Host "Pull down unattend.xml and then sysprep the box"
wget https://raw.githubusercontent.com/gmcyber/480share/master/unattend.xml -Outfile C:\Unattend.xml
C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /unattend:C:\unattend.xml
```

Once run, the machine was powered off, the networking was disconnected from the VMNetwork, and the ISO was removed. I then snapshotted the machine and called this new snapshot "Base"

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### DC Installation and Configuring

Now that the image was snapshotted and sysprepped, the last step for the domain controller portion of this is to install ADDS, get DNS running, and get networking happening on the internal virtual network. To start, I opened the machine as deployer and gave it the following network settings:

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

At this time, I also changed the hostname to dc01-chris. Once I restarted to finalize the hostname change, I began the installation of ADDS and DNS management tools on the new domain controller. To begin this process within the server manager window, I went to Manage -> Add roles and features -> Role based or feature based installation -> select the DC -> select ADDS (Active directory domain services) -> Accept all features and include management tools -> same thing with DNS server -> Install.

This itself is a lengthy process and requires some configuration once the install is complete before restarting to finalizing changes, this is where the other information like forest name and DNS configurations will come into play.&#x20;

In order to continue the configurations I needed to promote the server to a domain controller. Through the flag notifications I entered this menu. This is where I selected to add a new forest and gave it the name chris.local

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I then left the domain controller options blank and gave it a DSRM password for recovery purposes as seen below.

<figure><img src="../.gitbook/assets/image (10) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I left the NetBIOS name as the default to what it had assigned based on the domain name. I also left the paths on the next page the same as the defaults. Once completed, it allowed me to double check changes, and then ran a prerequisite check to make sure that the server had all of the necessary things to be promoted to a domain controller. Once completed, the install and restart needed for promoting the server to a domain controller took place and took a decent amount of time but once completed, the server was restarted automatically by the installer.&#x20;

When logging into the domain controller after install, I could tell that the install was successful as there was now an option to log into the chris.local domain. Since I did not yet have a named domain admin account, I simply logged in as deployer for now.&#x20;

After creating a domain admin (AND Enterprise Admin) user account and disabling the administrator and guest accounts, I moved onto setting up the DNS service. For this, I simply created a reverse lookup zone, a forward lookup zone, and created the records needed for my environment. These included records for my pfsense VM, my management VM, and records for my ESXi host as well as a record for the future vCenter VM that I will be creating later, all of which can be seen below. Note that I had to create a separate pointer zone for my Super11 IP for the DNS because it was not on the 10.0.17.0 network.

<figure><img src="../.gitbook/assets/image (11) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I also went over to my management box and changed the DNS settings to now point at my DC and ran a quick ping to make sure that everything was resolving properly.&#x20;

<figure><img src="../.gitbook/assets/image (12) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (13) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 1

<figure><img src="../.gitbook/assets/image (14) (1) (1).png" alt=""><figcaption></figcaption></figure>

Above is a screenshot that illustrated three separate parts of this deliverable. The first part in the red box at the top is the current logged user that is remoting in via SSH into the AD through management which is my admin user. The second part in the blue box below that is a read of the current groups that my admin account is registered to in the domain. Lastly, the third box in green at the bottom is a list of all of the type A records in my domain.

## Installing vCenter

Now that the domain is configured, my management box is completely networked properly, and that the remainder of the VMs are completely configured, it was time to install vCenter into my environment. To ensure a smooth installation, I added the intended IP and hostname of my new vCenter instance to my /etc/hosts file as seen below.

<figure><img src="../.gitbook/assets/image (15) (1) (1).png" alt=""><figcaption></figcaption></figure>

I then verified name resolution for my vcenter record. Once completed, I made sure that my ESXi host was synced to the proper time for this part of the install by going to Host -> Manage -> time & date -> NTP Server: pool.ntp.org. I set this to start with host and then manually started the service.&#x20;

<figure><img src="../.gitbook/assets/image (16) (1) (1).png" alt=""><figcaption></figcaption></figure>

I then mounted the VCSA ISO to my management box that I had uploaded earlier while I was uploading the Windows Server ISO. Once I navigated to the mount location for the new VCSA ISO, I ran the Linux 64 UI installer and this opened the GUI for the installation process as seen below. I enabled the install mode from this menu.

<figure><img src="../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

I then provided my ESXi location and credentials for root so that the VM may be created, gave the new VM a name of vcenter-chris, selected the small deployment size, selected datastore2 and gave it a THIN disk, and lastly, gave it the following network settings.

<figure><img src="../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

Once completed, it gave me one last overlook at the settings that were to be implemented and then I begun the stage 1 installation.

<figure><img src="../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

Now that the first stage of the deployment was completed, it was time to move to the second stage. For these configurations, I synced the time in the vCenter with the ESXi host, gave it an SSO administrator password, and completed the installation. After another long install timeframe, stage two had completed. &#x20;

After connecting to the host via my management box's web browser and entering the credentials for my newly created administrator@vsphere.local account, I was able to procure the vCenter home page as seen below in the second deliverable.

## Deliverable 2

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Above is the home page for my vCenter instance with the version number, build, and when it was last updated being 9/15 when I installed it.&#x20;

## SSO Integration

For the SSO integration, I started by opening the administration menu and joining the vCenter to my domain and went under SSO and then to configuration. Here I added my domain as an identity source by clicking add, giving it my domain and a domain admin account credentials, and then restarting the server. Once completed, I was able to see my domain in the sources as seen below.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Now that my domain was an identity source for SSO, I added the Domain Admin and Enterprise Admin groups to the vCenter administrators built in group as seen below.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Finally, I logged out and logged back into my machine as my administrator account for my AD domain.&#x20;

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 3

Because my VM window was being cut off and you could not see the user that was currently logged in the vCenter home pace, I have attached below a screenshot of the same webpage but of the change password screen and highlighted the user that vCenter was attempting to change the password of, aka my domain admin account.&#x20;

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Reflection

This lab was extremely insightful into the inner working of setting up a vCenter environment. I have had experiences in the past with user interaction and management of fully configured environments inside of a VMware based environment but I have never gone this in depth into setting up and configuring a vCenter or ESXI inegrated environment. I have done AD and SSO before for other applications but it was very useful to go over how VMware handles it for their vCenter environments. Overall, a rather insightful lab that introduced me to some more hands-on vCenter management.&#x20;
