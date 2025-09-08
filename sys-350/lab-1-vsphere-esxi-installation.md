# Lab #1: VSphere ESXi Installation

## Overview

## Accessing Via IPMI

## Installation onto Server

## Networking and Configurations

## Deliverable 1

## Configuring Datastores

## Deliverable 2

## Creating Virtual Networking

## Deliverable 3

## Creating VMs

## Deliverable 4

## Reflection



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

## Configuring Host Networking

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

## Deliverable 1



<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

## Adding/Creating Datastores

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>







## Deliverable 2

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>



## Creating Virtual Networking



### Creating a Virtual Switch

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>



### Creating a Port Group



<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>



## Deliverable 3



<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

## Installing VMs

### PFSense VM Install



<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>



### Xubuntu Install



## Configuring VMs

### PFSense Configurations

### Xubnutu Configurations

