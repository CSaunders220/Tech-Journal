# Lab #4: Nested Virtualization

## Overview



## New DNS Records / Deliverable 1

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

## Preparing for Nested ESXi Hosts

&#x20;To start, I downloaded the OVA file from the cybershare and added it to my satastore2 ISOs folder for use in the deployments of the new ESXi hosts as seen below.

<figure><img src="../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

NOTE: In order to upload it to vCenter due to the fact that vCenter is hosted on the same ESXi host that the storage for ISOs is attached to, I had to go into my management Xubuntu machine, download the OVA from the datastore to the management VM, and then re-upload it directly to the vCenter due t the fact that the vCenter is not on the cyber.local network.&#x20;

### Adding a vCenter Datacenter

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

### Adding Physical ESXi Hosts to Virtual vCenter

Now that the Datacenter is created, the ESXi host that is running my other existing infrastructure has to be added to the machine to view and manage the ESXi hosts as a whole. To do this, I right clicked the datacenter and selected "Add Host" as seen below.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

I then added the hostname of the target server that I wanted to add to the vCenter as seen below.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

I then provided login information for my ESXi host.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Lastly, I reviewed the information that vCenter collected from the host and then added the host to vCenter.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Since we are using the free evaluation editions, I skipped the licensing part, I left it in the one and only datacenter I have, and I disabled lockdown mode. Once added, it appeared in my overall view as seen below.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## Creating the ESXi Hosts from an OVA

Tos tart, I right clicked the datacenter in vCenter and selected to deploy a VM from an OVF Template as seen below.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

I then selected the OVF template that I had previously downloaded and uploaded it as a local file to the vCenter from my management machine.&#x20;

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

I gave it a name, and selected the datacenter as the target location for the new VM from OVF template.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

I selected the physical ESXi host as the compute target for the actual processing of this addition to the vCenter. I also bypassed the certificate warning and accepted the EULA. I added the storage target to my second datastore from the physical ESXi at this point as well making sure that it was thin provisioned.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

I added the 350LAN as the network target for the new VM

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

I then configured the networking rules and settings for the nre VM. The Screenshot below details the networking, DNS, SSH, and Domain settings that I inputted.

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Once completed it appeared as a new VM on the physical ESXI Super11. I followed all the steps for the physical host to add it as a new host in vCenter once it had completed its boot and configuration operations.&#x20;

I also copied these steps exactly for ESXi 2 and 3 but changed the networking configurations as seen below for hosts 2 and 3 respectively.&#x20;

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

Lastly for this section, I edited the vSwitch settings for my networking so that the ESXi hosts and the nested networking can work as seen below.

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

## Deliverable 2

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

## VM Templates

