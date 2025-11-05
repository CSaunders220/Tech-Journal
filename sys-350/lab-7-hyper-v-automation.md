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

Scripting Hyper-V actions is rather simple compared to scripting actions using something like PyVmomi due to the fact that there are significantly less lines of code needed for executing actions on the infrastructure.&#x20;

