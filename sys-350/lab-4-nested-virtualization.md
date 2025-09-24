# Lab #4: Nested Virtualization

## Overview



## New DNS Records

In order to deploy the new ESXi hosts for nested virtualization there had to be new DNS entries for the soon to be hosts. To do this, I went into my AD as normal and created the new A and PTR records as seen below.&#x20;

<figure><img src="../.gitbook/assets/image (91).png" alt=""><figcaption></figcaption></figure>

Once this was done, the DNS was all set for the new ESXi hosts to be deployed.

## ESXi AutoStart

With the recent power surges and power problems in the Freeman server room, I enabled AutoStart on the VMs inside of my ESXi environment to prevent denial of remote access due to VMs being down or missing. Below are the steps that I used to set up AutoStart on my firewall which were then repeated for all of the subsequent VMs in my environment.

First, by right clicking on the VM that AutoStart should be enabled on, you can see the AutoStart dropdown menu. First you have to enable it as seen below.

<figure><img src="../.gitbook/assets/image (92).png" alt=""><figcaption></figcaption></figure>

Once enabled, right click again and go to the same dropdown to get to the configuration menu for how the AutoStart behaves.

<figure><img src="../.gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

In this menu, since the firewall is the first of all of the dependents in accessing the outbound connections, I left the AutoStart timing as default and then delayed all of the subsequent dependent VMs proportionally to their chain of access.

<figure><img src="../.gitbook/assets/image (94).png" alt=""><figcaption></figcaption></figure>

For all VMs below the firewall, I delayed them in order of importance by an interval of 5 seconds. I start with my domain controller for DHCP and DNS services, and then I move onto the rest of the bulk of the VMs to start.

## Creating the Nested ESXi Hosts

&#x20;To start, I downloaded the OVA file from the cybershare and added it to my satastore2 ISOs folder for use in the deployments of the new ESXi hosts as seen below.

<figure><img src="../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

NOTE: In order to upload it to vCenter due to the fact that vCenter is hosted on the same ESXi host that the storage for ISOs is attached to, I had to go into my management Xubuntu machine, download the OVA from the datastore to the management VM, and then re-upload it directly to the vCenter due t the fact that the vCenter is not on the cyber.local network.&#x20;

Once on the management box, I opened the vCenter portal and logged in. In order to have VMs though I needed a datacenter. To Create a datacenter in vCenter I right clicked my vCenter instance from the left menu and then selected "New Datacenter"

<figure><img src="../.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

I then gave it the name datastore01 and created the datacenter.

Once the datacenter was created, I needed to add my physical ESXi to the vCenter interface before I can add the new nested ESXi hosts. In order to do this, I went to my new datacenter, right clicked, and then selected "Add Host"

<figure><img src="../.gitbook/assets/image (97).png" alt=""><figcaption></figcaption></figure>

I then gave it the target of my physical ESXi host IP address and provided credentials.&#x20;

<figure><img src="../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (99).png" alt=""><figcaption></figcaption></figure>

For the licensing, I left this as the evaluation license since we do not have an actual license for these ESXi hosts. Once completed, I was given a summary before finalizing changes.

<figure><img src="../.gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>

