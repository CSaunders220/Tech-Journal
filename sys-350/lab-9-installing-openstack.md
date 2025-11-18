# Lab #9: Installing OpenStack

## Overview



## Installing Chrome Remote Desktop

Before I begin with the install of OpenStack, the first part of the lab is to install Chrome and Chrome Remote Desktop onto the systems. This is rather simple and can be done with a few short commands and interactions with the CRD webpage.&#x20;

Firstly, there needed to be a web browser on the machine and for this I chose Chrome to make things simple with CRD. The following commands were used to install chrome onto my Xubuntu server.

```
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install ./google-chrome-stable_current_amd64.deb
```

Now that Chrome existed on my host, I was able to open chrome, navigate to the CRD webpage, and set up my system using the SSH method for installation. To do this, I downloaded the debian package from the CRD page, and then ran the provided command to set up chrome remote desktop. After doing so, the host was visible in my CRD home page.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## Deliverable 1

The following screenshot is me accessing my new Xubuntu server from the CRD viewer in my browser.&#x20;

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Installing OpenStack

