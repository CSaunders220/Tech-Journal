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

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

I then added the hostname of the target server that I wanted to add to the vCenter as seen below.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I then provided login information for my ESXi host.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Lastly, I reviewed the information that vCenter collected from the host and then added the host to vCenter.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Since we are using the free evaluation editions, I skipped the licensing part, I left it in the one and only datacenter I have, and I disabled lockdown mode. Once added, it appeared in my overall view as seen below.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Creating the ESXi Hosts from an OVA

Tos tart, I right clicked the datacenter in vCenter and selected to deploy a VM from an OVF Template as seen below.

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I then selected the OVF template that I had previously downloaded and uploaded it as a local file to the vCenter from my management machine.&#x20;

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I gave it a name, and selected the datacenter as the target location for the new VM from OVF template.

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I selected the physical ESXi host as the compute target for the actual processing of this addition to the vCenter. I also bypassed the certificate warning and accepted the EULA. I added the storage target to my second datastore from the physical ESXi at this point as well making sure that it was thin provisioned.

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I added the 350LAN as the network target for the new VM

<figure><img src="../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>

I then configured the networking rules and settings for the nre VM. The Screenshot below details the networking, DNS, SSH, and Domain settings that I inputted.

<figure><img src="../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

Once completed it appeared as a new VM on the physical ESXI Super11. I followed all the steps for the physical host to add it as a new host in vCenter once it had completed its boot and configuration operations.&#x20;

I also copied these steps exactly for ESXi 2 and 3 but changed the networking configurations as seen below for hosts 2 and 3 respectively.&#x20;

<figure><img src="../.gitbook/assets/image (12) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (13) (1) (1).png" alt=""><figcaption></figcaption></figure>

Lastly for this section, I edited the vSwitch settings for my networking so that the ESXi hosts and the nested networking can work as seen below.

<figure><img src="../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 2

<figure><img src="../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

## Setting up DHCP

In order to create the templates and have them deploy properly, I needed to install DHCP onto my environment in a manner of my choice and I chose to go through my AD. I went through the add roles and features menu and selected the DHCP server install as seen below.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I also specified the user that was permitting the install of the DHCP services onto the AD.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I then quickly configured a scope that does not conflict with any IPs currently statically set in the environment and set a decent enough range for IPs.&#x20;

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Creating and Deploying VM Templates

To create a templateable VM, I started with using an ISO from my datastore and I chose to go with Xubuntu. I set the VM configurations as such:

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I set the network to be the SYS-350 LAN and also connected the datastore 2 ISO folder file for the Xubuntu ISO that I previously had in that location.&#x20;

Once the VM was deployed I quickly made the base configurations including assigning DHCP, setting a deployer account, and installing perl.&#x20;

After making the necissary OS configurations, I went back to the vCenter and right clicked the VM, took a snapshot called Base. Once completed, I right clicked, selected convert to template, and confirmed. When completed, the new template appeared in my second tab menu as seen below.

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

To deploy, I went to the datacenter, selected new VM, and then selected deploy from template as seen below.

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I chose the template from the datacenter saved location.

<figure><img src="../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

I then gave the new VM a name and chose its storage location. Since I am deploying via vCenter I chose the only datacenter available for my vCenter.

<figure><img src="../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

I selected the super11 ESXi for the deployment for now to simplify the networking.

<figure><img src="../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

I verified that the new VM would use thin provisioning and selected datastore 2 as the default storage location for the new VM and also set it to prompt for the operating system config.

<figure><img src="../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

Because I had prompted for the config settings Iw as able to see and use the Xubuntu specification file that I had recently created. This file dictates the basic OS questions and configurations and allows the user to specify the settings on deploy. I have attached a screenshot of the specification taht I created below as well.

<figure><img src="../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

Once ready to be deployed, I reviewed the configurations and started the deployment.&#x20;

<figure><img src="../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 3

Once all of the above steps were completed, I had a new VM and the clone task was visible and had completed without a hitch. The clone task information can be seen below:

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

## New VM Templates

As a secondary VM, I decided to install Rocky Linux 9.6

I once again quickly configured the machine to use a static IP this time through the VM specifications file that I created as seen below. In this specification, unlike the previous one, I hardcode an IP address to be assigned.

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

Once I had the new VM created and snapshotted, the Vm was configured with all necissary tools, and the specification was made, it was time to clone the template into a new VM.

The VM template appears in the VM list of the ESXi host as seen below as well.

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

I give the new VM a name, select the target datastore, select a template from the list, and seleect the storage location for the new VM as seen below.

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

I also make sure that it is thin provisioned and on the right datastore.

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

When prompted, I selected the new Rock9 specification for the new VM for the autoconfigured settings for the deployment.

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

Once completed, the task appeared in the task log showing successful completion of the deployment.

## Deliverable 4

As seen below, the task deployment was visible from the vCenter management portal.&#x20;

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

VMware tools did get corrupted though and caused some issues, however, the IP address was deployed with the machine and can be seen via accessing the VM and giving the ip a command.

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>
