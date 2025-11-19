# Lab #9: Installing OpenStack

## Overview



## Installing OS

For this lab and for open stack, a specific version of Ubuntu is needed for the services to properly work. This being said, the version that will be used for these OpenStack labs will be Ubuntu 24.04.3 (Noble Numbat).&#x20;

I installed the OS the same way that I have installed all other OSs onto my system by flashing a flash drive with the ISO using Rufus and then booting to that USB on my Super Micro server and installing the new OS onto the smaller of the two drives. Because I have done this many times I will omit the details here in favor of saving space and time for the new services covered in the lab.&#x20;

## Installing XFCE and XRDP

For the desktop environment and for the XRDP service, I chose to use the XFCE environment for my server. XRDP and XFCE was installed onto my Linux system using the following commands.

```
sudo apt update && sudo apt upgrade -y
sudo apt install xfce4 xfce-goodies -y
sudo apt install xrdp -y
sudo systemctl start xrdp
sudo systemctl enable xrdp
sudo systemctl set-default graphical.target
echo xfce4-session > ~/.xsession
sudo apt install ufw
sudo ufw allow 33899/tcp
sudo ufw reload
sudo adduser xrdp ssl-cert
sudo reboot
```

After rebooting, the XFCE desktop environment was present on my Ubuntu session and I was able to access the desktop environment using my Windows RDP service.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## Deliverable 1

The following screenshots demonstrate my ability to use Windows RDP service to access my server via IP and shows the XFCE desktop after using XRDP to log into my server.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Installing Open Stack

