# Lab #1: Network Management

## SNMP Protocol Overview

SNMP or the Simple Network Management Protocol is an application layer protocol used in environments where you want to monitor and automate reactions when certain machines hit certain benchmarks. For example, when a computer hits a large percentage of used storage, send an alert to a person to go and clean it or when network usage skyrockets for no apparent reason to alert security.&#x20;

The protocol most often uses agents or services that have agents to create automations and alerts based on what results from the SNMP packets returning.&#x20;

## Host Configurations

To install SNMP onto a windows machine, particularly a Windows Server machine, you had to add the role first, then once the role is added you can open the services manager (services.msc) and navigate to SNMP, make sure it is automatically starting on startup, and then configure what machines that you want to be able to recieve SNMP packets from. Leaving this open would allow for network storms and SNMP packets from malicious machines so be sure to change this to only allow packets from machines you want to be recieving them from.&#x20;

The firewall must also be configured on the machine for this to be able to work. To change the firewall, navigate to the firewall in contorl panel and then to advanced firewall. Find or create a rule for SNMP packets and the port, and then allow the traffic as needed.
