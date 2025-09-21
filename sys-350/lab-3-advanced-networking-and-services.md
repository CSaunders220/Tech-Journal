# Lab#3: Advanced Networking and Services

## Overview

This lab covers the addition of two new virtual networks on the environment, a DMZ and a new LAN for management, a basic web server to emulate how the DMZ would work, and just general services configuration to prepare for automation and nested virtualization in the next few weeks.&#x20;

## New Networking

Before even touching the PFsense VM, I went into my ESXi and added two new port groups that all route through the 350-internal virtual switch in order to functionally create VLANS for the new networks. Once that was completed I was able to create new interfaces

Moving to the PFsense VM, I added two additional virtual network adapters for the two new networks. Once they were added to the VM, I reset the PFsense VM, re-assigned interfaces, and then assigned them all new network assignments. For ease of remembering, I kept the LAN on 10.0.17.0, I added management on 10.0.18.0, and then the DMZ on 10.0.19.0.

<figure><img src="../.gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

In order for DNS to work with the new networks, I needed to create new reverse lookup zones for the DNS management so I went over to my AD and added two new reverse lookup zones as seen below.

<figure><img src="../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

With that done, I tested the internet connection from my LAN to make sure that none of the new changes disrupted the networking that I had before for my AD and vCenter. I also changed the networking of my Xubuntu machine to the new management network and verified that I was able to access the AD and the internet outside of my networks.&#x20;

<figure><img src="../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>







### Management Network

