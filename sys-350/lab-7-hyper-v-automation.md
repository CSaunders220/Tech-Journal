# Lab #7: Hyper-V Automation

## Overview



## Configuring a Parent Disk

For the purpose of cloning, I created a new Ubuntu VM with the following specs:

* Xubuntu 25.10
* 2GB RAM
* 2 vCPU
* No secure boot
* Connected to WAN

<figure><img src="../.gitbook/assets/image (134).png" alt=""><figcaption></figcaption></figure>

I also ran the following prep [script](https://github.com/gmcyber/480share/blob/master/hyperv-ubuntu-sealer.sh) supplied by the professor and developed by [GMCyber](https://github.com/gmcyber). This script does some basic configurations to prepare an ubuntu VM for cloning on a mass scale.

```
#!/bin/sh
#script to prepare ubuntu desktop vm for cloning
apt-get dist-upgrade

#open ssh
apt-get install -y linux-tools-virtual linux-cloud-tools-virtual
cat /dev/null > /var/log/wtmp 
cat /dev/null > /var/log/lastlog 
rm -rf /tmp/*
rm -rf /var/tmp/*
rm -f /etc/ssh/ssh_host*
rm -f /etc/udev/rules.d/70-persistent-net.rules
cat <<EOL | sudo tee /etc/rc.local
#!/bin/sh -e
test -f /etc/ssh/ssh_host_dsa_key || dpkg-reconfigure openssh-server
exit 0
EOL
echo ubuntu > /etc/hostname
apt-get clean
history -c
history -w
chmod +x /etc/rc.local
systemctl stop apt-daily-upgrade.timer
systemctl disable apt-daily-upgrade.timer
systemctl stop apt-daily.timer
systemctl disable apt-daily.timer
sudo apt autoremove -y
#truncate the machine id to avoid duplicate dhcp
echo > /etc/machine-id

echo "remove git repo and then issue a shutdown - h now"
```

Once the above script was run, the git repository was removed, and the computer was shutdown, I took a snapshot and called it BaseSnapshot as seen below. This VM is not ready to be used to clone since it is completely hardened and syspreped.

<figure><img src="../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

## Deliverable 1

After creating the base image for the VHD and snapshotting and powering off the VM, I went into the file explorer, found the VHSX file for my Xuubuntu machine, and applied the read only setting to the drive. This is so that it cannot be edited when using it to make clones later in this lab.

<figure><img src="../.gitbook/assets/image (136).png" alt=""><figcaption></figcaption></figure>

## Making Linked Clones in Hyper-V

To make a child disk in Hyper-V, I first had to navigate back to my host manager for the Hyper-V service and right click my host in order to create a new hard disk only, not a whole VM.&#x20;

<figure><img src="../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>

Next, I selected the format, the type of disk that I wish to use and for this use case I chose to make it a differencing disk. this means that it will use the referenced disk (the original) and save any changes to the dynamic new differencing disk. I gave the new disk the name "SonOfXubuntu", I gave it a storage location path, and finally I directed it to use the original XubuntuBase virtual hard disk as a source for the new hard disk.&#x20;

<figure><img src="../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>

Now that the hard disk was created, I made a new VM on the Hyper-V Server that has 2GB or memory and 2 vCPUs and I targeted the hard disk location to be the newly created thin provisioned linked cloned hard disk as the storage location for this new VM.

<figure><img src="../.gitbook/assets/image (143).png" alt=""><figcaption></figcaption></figure>

Once the VM was created and configured, I booted the OS, logged into the account that I had previously configured in the base image, and then created a new file on the desktop called "DifferencingTest.txt" and saved it. This new file is small but is new compared to the original image so it will be stored on the differencing disk that  had previously created.

<figure><img src="../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>

## Deliverable 2

Now that there is a change on the differencing drive, I opeend the file explorer of my actual windows server running Hyper-V and navigated to the properties of the VHDX filr of my new Son of Ubuntu drive. As seen in the screenshot below, the SonOfUbuntu machine is running, and the file properties of the differencing drive shows that the drive is incredibly small since it is only storing the one txt file and some OS file system data for it.

<figure><img src="../.gitbook/assets/image (145).png" alt=""><figcaption></figcaption></figure>

## Automating Hyper-V Actions

Scripting Hyper-V actions is rather simple compared to scripting actions using something like PyVmomi due to the fact that there are significantly less lines of code needed for executing actions on the infrastructure. Hyper-V automation for this lab will consist of entirely PowerShell using the Hyper-V module.&#x20;

## Deliverable 3

The screenshots and code below are for deliverable three, turning on a VM using powershell in the Hyper-V server. The following code can be broken into three main chunks: Checking if the Hyper-V powershell module exists and if not installing it, Collecting user input for the VM to power on, and checking if the VM exists and if so starting it. The first screenshot is my script output seen from the terminal, the second block is the code I made to achieve this goal, and the last screenshot is the powered on VM as seen from the Hyper-V manager.

<figure><img src="../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

```powershell
#Take an inputted VM name and start the VM in Hyper-V
#System must have the Hyper-V management module installed

$VM = Read-Host -Prompt "What VM would you like to start?"

#Check if the Hyper-V module exists and attempt to install if it does not
if (-not (Get-Module -Name "hyper-v" -ListAvailable)){
  Write-Host "Hyper-V module not installed..."
  Write-Host "Attenpting to install module..."
  Get-Command -Module hyper-v | Out-GridView
  if (Get-Module -Name "hyper-v" -ListAvailable){
    Write-Host "Hyper-V module installed!"
  }
  else {
    Write-Host "Error installing Hyper-V module" -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "Starting task!"
}

#Start a VM with name passed from command
if (Get-VM | where {$_.Name -eq $VM}){
  Start-VM -Name $VM
  if (Get-VM | where {$_.Name -eq $VM} | where {$_.State -eq 'Running'}){
    Write-Host "Started VM $VM successfully!"
  }
  else{
    Write-Host "VM $VM failed to start..." -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "VM Not found..." -ForegroundColor Red -BackgroundColor Black
}

```

<figure><img src="../.gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>

## Deliverable 4

The fourth deliverable is to use PowerShell to take a checkpoint, or snapshot, of a VM. I do all the checks as I do in the first script for the Hyper-V module and checking if the VM exists on the server before I create the checkpoint. I also allow the user to give the checkpoint a name in the first part of the script.&#x20;

<figure><img src="../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

```powershell
#Take an inputted VM name and snapshot the VM in Hyper-V
#System must have the Hyper-V management module installed

$VM = Read-Host -Prompt "What VM would you like to snapshot?"
$SnapName = Read-Host -Prompt "What would you like to name the new shapshot?"

#Check if the Hyper-V module exists and attempt to install if it does not
if (-not (Get-Module -Name "hyper-v" -ListAvailable)){
  Write-Host "Hyper-V module not installed..."
  Write-Host "Attenpting to install module..."
  Get-Command -Module hyper-v | Out-GridView
  if (Get-Module -Name "hyper-v" -ListAvailable){
    Write-Host "Hyper-V module installed!"
  }
  else {
    Write-Host "Error installing Hyper-V module" -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "Starting task!"
}

#Snapshot a VM with name passed from command
if (Get-VM | where {$_.Name -eq $VM}){
  Checkpoint-VM -VMName $VM -SnapshotName $SnapName
  if (Get-VmSnapshot -VMName $VM | where {$_.Name -eq $SnapName}){
    Write-Host "Snapshotted $VM successfully!"
  }
  else{
    Write-Host "Failed to snapshot #VM ..." -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "VM Not found..." -ForegroundColor Red -BackgroundColor Black
}

```

<figure><img src="../.gitbook/assets/image (154).png" alt=""><figcaption></figcaption></figure>

## Deliverable 5

The fifth deliverable is similar to the third one but for stopping VMs. Below I have attached my code and screenshots of it running and the outcome. In terms of the code and the flow of the script, it is the exact same as the starting script but instead of performing the start action it stops the given VM.

<figure><img src="../.gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

```powershell
#Take an inputted VM name and stop the VM in Hyper-V
#System must have the Hyper-V management module installed

$VM = Read-Host -Prompt "What VM would you like to shutdown?"

#Check if the Hyper-V module exists and attempt to install if it does not
if (-not (Get-Module -Name "hyper-v" -ListAvailable)){
  Write-Host "Hyper-V module not installed..."
  Write-Host "Attenpting to install module..."
  Get-Command -Module hyper-v | Out-GridView
  if (Get-Module -Name "hyper-v" -ListAvailable){
    Write-Host "Hyper-V module installed!"
  }
  else {
    Write-Host "Error installing Hyper-V module" -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "Hyper-V module already installed..."
}

#Stop a VM with name passed from command
if (Get-VM | where {$_.Name -eq $VM}){
  Stop-VM -Name $VM
  if (Get-VM | where {$_.Name -eq $VM} | where {$_.State -eq 'Off'}){
    Write-Host "Shutdown VM $VM successfully!"
  }
  else{
    Write-Host "VM $VM failed to stop..." -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "VM Not found..." -ForegroundColor Red -BackgroundColor Black
}

```

<figure><img src="../.gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>

## Deliverable 6

The final small script is to switch what virtual switch a VM is connected to. Similar to the last ones a lot of my code is error handling for checking but the following screenshots and code show my process.&#x20;

<figure><img src="../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

```
#Take an inputted VM name and change the virtual switch of the VM
#System must have the Hyper-V management module installed

$VM = Read-Host -Prompt "What VM would you like to change the networking on?"
$Switch = Read-Host -Prompt "What switch would you like to switch to?"

#Check if the Hyper-V module exists and attempt to install if it does not
if (-not (Get-Module -Name "hyper-v" -ListAvailable)){
  Write-Host "Hyper-V module not installed..."
  Write-Host "Attenpting to install module..."
  Get-Command -Module hyper-v | Out-GridView
  if (Get-Module -Name "hyper-v" -ListAvailable){
    Write-Host "Hyper-V module installed!"
  }
  else {
    Write-Host "Error installing Hyper-V module" -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "Starting task!"
}

#Change networking of a VM with name passed from command to inputted switch
if (Get-VM | where {$_.Name -eq $VM}){
  if (Get-VMSwitch | where {$_.Name -eq $Switch}){
    Connect-VMNetworkAdapter -VMName $VM -SwitchName $Switch
  }
  else{
    Write-Host "Virtual switch not found..." -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "VM Not found..." -ForegroundColor Red -BackgroundColor Black
}
```

<figure><img src="../.gitbook/assets/image (156).png" alt=""><figcaption></figcaption></figure>

## Creating a New Cloneable Base

For the last deliverable, a new distro of linux was required so I chose to use the good old reliable RockyOS for this part of the project. In order to make a base image, I first needed to download the ISO before I could create the VM. Once I had the ISO, I created a VM with the following settings.

<figure><img src="../.gitbook/assets/image (157).png" alt=""><figcaption></figcaption></figure>

Now that I had a VM created, there were some base configurations that I wanted to have on my rocky machine before I copied it. First I booted the VM and defined the base configurations for the OS including storage location, creating a  deployer user, and changing the default hostname as seen in the screenshot of the RockyOS installer below.&#x20;

<figure><img src="../.gitbook/assets/image (158).png" alt=""><figcaption></figcaption></figure>

Once installed and restarted, I loaded into the terminal to make changes to the installed packages and to create a desktop environment. I chose to install the XFCE environment for this image.&#x20;

```
sudo dnf update
sudo dnf install epel-release
sudo dnf groupinstall "Xfce"
sudo systemctl set-default graphical.target
reboot
```

After the second reboot, I made sure that the user could still logon and that the desktop environment loaded properly before I shut down the VM and moved onto creating the linked clone.&#x20;

<figure><img src="../.gitbook/assets/image (159).png" alt=""><figcaption></figcaption></figure>

## Deliverable 7

The last part of this lab is to automate the creation of a linked clone of the newly created VM, in my case this is my rocky-base VM. Below are the screenshots of the terminal output, the code block containing my full code for creating the linked clone, and lastly the created clone as seen from my Hyper-V manager.&#x20;

The script will basically take user input for the target VM, ask for a new name for the linked clone to be created, ask the user what network they want it to be on, then does the checks for the Hyper-V module, established storage paths dynamically into my VM storage locations based on the named inputted, check if the parent VM exists, create a linked clone of the drive of the parent VM, and finally established the new VM.

<figure><img src="../.gitbook/assets/image (160).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>

```
#Take an inputted VM target and new VM information to make a linked clone
#System must have the Hyper-V management module installed

$VM = Read-Host -Prompt "What VM would you like to create a linked clone of?"
$LinkedName = Read-Host -Prompt "What would you like to name the new VM?"
$LinkedSwitch = Read-Host -Prompt "What Switch would you like to assign to the clone?"

#Check if the Hyper-V module exists and attempt to install if it does not
if (-not (Get-Module -Name "hyper-v" -ListAvailable)){
  Write-Host "Hyper-V module not installed..."
  Write-Host "Attenpting to install module..."
  Get-Command -Module hyper-v | Out-GridView
  if (Get-Module -Name "hyper-v" -ListAvailable){
    Write-Host "Hyper-V module installed!"
  }
  else {
    Write-Host "Error installing Hyper-V module" -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "Starting task!"
}

$Storage = "V:\ProgramData\Microsoft\Windows\Hyper-V" + "\" + $LinkedName
$ParentVMPath = "V:\Users\Public\Documents\Hyper-V\Virtual Hard Disks" + "\" + $VM + ".vhdx"
$LinkedVMPath = "V:\Users\Public\Documents\Hyper-V\Virtual Hard Disks" + "\" + $LinkedName + ".vhdx"

if (Get-VM | where {$_.Name -eq $VM}){
  if (Get-VMSwitch | where {$_.Name -eq $LinkedSwitch}){
    if (Test-Path -Path $ParentVMPath) {
      Write-Host "Parent virtualdisk exists, continuing!"
      New-VHD -Path $LinkedVMPath -ParentPath $ParentVMPath -Differencing
    }else{
    Write-Host "Parent path not found..." -ForegroundColor Red -BackgroundColor Black
    }
  New-VM -Name $LinkedName -Switch $LinkedSwitch -Path $Storage -VHDPath $LinkedVMPath -Generation 2 -MemoryStartupBytes 2GB
  }else{
    Write-Host "Virtual switch not found..." -ForegroundColor Red -BackgroundColor Black
  }
}else{
  Write-Host "Parent VM not found..." -ForegroundColor Red -BackgroundColor Black
}

if (Get-VM | where {$_.Name -eq $LinkedName}){
  Write-Host "Linked clone created successfully!"
}
```
