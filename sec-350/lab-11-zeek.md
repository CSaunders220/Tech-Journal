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

The screenshot below shows the filtered output for the dns.log for champlain.edu, a web page that I did a DNS resolve for in the capture.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## Deliverable 4

The screenshot below shows the HTTP responses as well from the pcap file as captured to vermont.gov.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 5

The screenshot below shows my pcap capture of the http packets between traveler and my nginx VM over the WNA port of edge01.&#x20;

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Deliverable 6

The following screenshot shows the packets captured over my WAN port for the ssh logs between my traveler VM and jump.&#x20;

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## Deliverable 7

Funnily enough, I actually do not have a screenshot for this portion of the lab because the RDP traffic, as set up in the previous lab, was forwarded thorugh the SSH tunnel with jump as an intermediary to the RDP target within my environment. Therefore, I cannot acctually see the logs properly after processing the PCAP through zeek.&#x20;

## Deliverable 9

The screenshot below is of my final updated network diagram for this environment with all of the changes made to date.&#x20;
