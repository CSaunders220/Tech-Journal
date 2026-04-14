# Lab #11: Zeek

## Overview

This lab covers the usage of Zeek as a network security monitoring tool. Since the environment is running in Proxmox though, and promiscuous mode is not enabled on the interfaces that it needs to be enabled on for this to work, the lab will cover the capture of PCAP files and then statically using zeek to analyze the captured packets.&#x20;

## Deliverable 1

The first step of this lab is to set up the box so that it can reach the internet, has a hostname, and can resolve DNS. The screenshot below demonstrates all three of these by showing a ping where it is resolving and also where it shows the hostname of the VM.

<figure><img src="../.gitbook/assets/image (216).png" alt=""><figcaption></figcaption></figure>

## Setting Up Zeek



## Deliverable 2

For this one, I got the processes to start working but not as intended because of the lack of promiscuous mode available on the interfaces that were on my mgmt box. The sreenshot below shows my zeekctl deploy but with the worker errors. The following two screenshots show the error in the lgos.

<figure><img src="../.gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (218).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (219).png" alt=""><figcaption></figcaption></figure>

## Capturing Logs on Edge01



## Deliverable 3

