# Lab #10: Decommissioning Server

## Overview

As the last lab in the class, we were tasked with using Gparted to clean the drives on our supermicro servers in preparation for the next semester students who will be in the class. This document will go over using Gparted live via USB to wipe the drives.&#x20;

## Preparing Gparted

The first thing needed is a live USB that contains the Gparted ISO. This can be retrieved from [https://sourceforge.net/projects/gparted/](https://sourceforge.net/projects/gparted/)

Once installed onto the USB, insert it into the server and boot to the USB.&#x20;

When promted, select the language and bypass any changes to the keymap. Once inside the ISO image and booted fully, the Gparted software will start automatically.&#x20;

The software looks similar to the screenshot

&#x20;below where in to top right you can select the drive from ones available on the sustem (including the USB used ot boot into the image) and then you can see what partitions and what formats exist for the storage.&#x20;

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

To remove a partition, select the partition from the list below the drive visualizer and then click the red X button to delete. Once all changes are made, press the green check mark to confirm changes.

## Deliverable 1

The following screenshots show both drives on my server which are completely unallocated.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
