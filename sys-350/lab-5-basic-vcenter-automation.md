# Lab #5: Basic vCenter Automation

## Overview

This lab will cover using the pyvmomi tool to automate some basic tasks and information retrieval scripts to prepare for VM manipulation through automation in future labs. This covers the prep work for this including installing VMware Tools, making and using the pyvmomi connection script, and creating python menus that can be expanded upon later with more options.&#x20;

## Installing VMware Tools

Before getting into the scripting, I had to verify that all of my VMs had a current install of VMware tools on it and that the ESXi can recognize that.&#x20;

### Windows Systems

Installing VMware tools on windows is probably the easiest of all the installations since you mount the ISO through the ESXi, open it, and run the windows installer for the VMware tools.&#x20;

### Linux Systems

Installing on linux is also relatively easy since depenidn on the install there are some different command line tools you can use depenidng on the default package manager. For RockyOS, the default package manager is apt so the command to install VMware tools is below:

```
sudo apt install open-vm-tools -y
```

For something like Ubuntu based systems, you would want to use the dnf package installer since that is the native installer. That command is below:

```
sudo dnf install open-vm-tools
```

### FreeBSD Systems

In the environemnt currently the main firewall and router is a FreeBSD PFsense VM that is acting as the gateway for all of the different netowks. PFsense handles packages and installs differently than any other systems, linux or windows, so the install for VMware tools is a bit different. For PFsense, you would first want to navigate to the shell and give it the following sommands to update the package repository and cert lists:

```
certctl rehash
pkg-static -d update
```

Once that is completed, I navigated to the web comfiguration portal for my firewall and ernt to System > Package Manager > Available Packages and searched for the open vm tools package at that time.

Once found, I clicked the install button on the right as seen in the screenshot below.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

After confirming and installing the package, the VMware tools was ready to go for my PFsense VM.&#x20;

## Setting Up VSCode and Git

With all of the VMware tools installed on each VM, it was time to begin the scripting portion of the lab. To start, I installed VScode and Git onto my MGMT VM

To install Git, I used the following commands. I have omitted usernames and emails for the documentation but these would have been filled:

```
sudo apt install git
ssh-keygen -t rsa -b 4096 -C “<email>”
eval "$(ssh-agent -s)"
cat <path_to_key>.pub
# Copy and paste key into github account ssh key menu here
git clone <ssh_github_link_for_repo>
cd <cloned_repo>
git config --global user.email "<email>"
git config --global user.name "<username>"
```

Once GIt existed on the system and I was able to pull, commit, and push to my repo in the designated folders, I then moved onto installing VScode. To do this, I used the following commands:

```
sudo snap install code --classic
sudo chmod 777 <git_repo>
```

I then opened VScode and opened my git directory as seen in the screenshot for deliverable 1.

## Deliverable 1

The screenshot below illustrates that I have VScode installed onto my machine, that I have a new folder in a repo for SYS-350 scripts with a README.md file, and my IP configurations for my MGMT VM.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Python Scripts and Configs

For starters, I had to install python and the pyvmomi dependencies onto my MGMT vm so to do this I inputted the following commands:

```
sudo apt update
sudo apt install python3-pip
pip3 install wheel
pip3 install pyvmomi
pip3 install pyvim
```

Once installed, it was time to begin creating the python files and configuration files needed to create the basic scripting tasks.

### JSON Configuration File

One of the main tasks of this lab is to use a JSON file to pass configurations to the python script for a more dynamic scripting environment.&#x20;
