# Lab #5: Automation with Ansible

## Overview

This lab introduced a few new machines to our environment, two target machines and one controller machine in preparation for the introduction for Ansible. Ansible is a tool that allows for the remote execution of a configured machine through the usage of an ansible agent on the machine to be able to remotely automate a task across a broad range of computers all at once. This is an immensely useful tool that really allows for most anything to be done remotely to manage an environment of machines.&#x20;

## Lab Setup

In order to get the machines ready a few things needed to be done before ansible could be used. Firstly, the machines that were going to be the targets needed a deployer account created that can SSH securely without a password prompt (for linux). Once this is done, I was able to SSH remotely from the deployer account logged into my controller box that will host ansible and I was able to access the machines as seen below.

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXeiIb6gwC_Ar2FDOSmv87HMtYoKIH4n6zLwgOki-sxlfMXLm0FX34_bLWNgmBqo12Eo55lRX2QTyfk7nOeOY9vWEK5yTYZ8EFdSQGDL0hVM8E0HsaEha8GYoOIQQ-iXwNpSb9ROTg?key=zSihq-rlU5Bb1-RF0PzROJsq" alt=""><figcaption></figcaption></figure>

On the controller box, it is best to create an ansible directory for hosting all of the inventory scripts and scripts that are desired to be run. Once I created that I then installed ansible with the code as seen below.

```
sudo dnf install epel-release
sudo dnf install ansible
```

Once done, I created an inventory file and called it inventory.txt. This allowed for me to use this as a reference for which hosts should be targeted when I run an ansible command. This can also be segmented into groups. For the lab, we separated our Windows and Linux machines similar to the list below.

```
nano ../ansible/inventory.txt

[linux]
ansible01-chris
ansible02-chris

[windows]
mgmt01-chris
wks01-chris
```

## Using and Forming Ansible Commands

The basic syntax of an ansible command that we used throughout the lab and that I adapted for each use case (until ansible galaxy) was similar to the one below.

{% code overflow="wrap" %}
```bash
ansible <group_name> -a <linux_command> -i <inventory_file> -m <module> -u <user_for_windows> --ask-pass
```
{% endcode %}

This basic framework was the backbone of all of the ansible commands that I had run throughout the basic parts of the lab and the activities. This one specifically was used for windows because on a Linux environment the user would be simply the deployer account so that would not be necessary.&#x20;

### Example Ansible Commands

Below are some of the commands that I had used for the lab as an example of formatting and how the commands would work as well as a breakdown of the command below each one.

***

```bash
ansible all -a ifconfig -i inventory.txt
```

This command above used an inventory list that consisted of entirely Linux machines (both of my ansible target machines) and ran the command ifconfig on them. It went through each machine and ran the command and then returned the output to the terminal that I was working from. Below is the actual output of the command as seen from my deployer account on my controller machine.&#x20;

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXe2MTZAKxMG0YD84IJIIj07pGmENv6lYt94FdhrwZVAQg5nvFVnteIn4P9HwVDB4JrCd9TLfR2v-FkoK1Qq3cXHxjZ1ow_JMVhyDKenjtgvvsY-WAr9z9zdAxHsVZPJX-Zjheiq?key=zSihq-rlU5Bb1-RF0PzROJsq" alt=""><figcaption></figcaption></figure>

***

{% code overflow="wrap" %}
```bash
ansible windows -i inventory.txt -m win_ping -u chris.saunders-adm@chris.local --ask-pass
```
{% endcode %}

This command basically did the same as the above command but for windows machines. This command had a user input for an authorized windows remote user and then had a secure string prompt after running the command for the user's password. Similarly, this outputted the results to the terminal but formatted differently since this time it was a module being used for windows machines. The output for running this on my management and workstation machines can be seen below.

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXf-Fant272dzh9hCWmVdE_ytpRl1F8hqfez7UfRa6td4NavL630TS5smiM2BD_6RjoXC8UDD28rKX8CUprUJjaoKgQlC5JfYGDaeJyW2MLsDKvkqgDVRCY9Kh3J76SUDmFteeLEAQ?key=zSihq-rlU5Bb1-RF0PzROJsq" alt=""><figcaption></figcaption></figure>

***

## Ansible Playbooks

