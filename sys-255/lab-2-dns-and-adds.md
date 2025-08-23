# Lab #2: DNS and ADDS

### **Overview**

This lab covered the basics of establishing an active directory, making the active directory elevated and transforming it into a domain controller, and how to add a workstation or any device to the active directory and domain.

### **Active Directory Configuration**

The Active directory, as defined in class this week, is the manager for all computers and user's information connected to a certain domain or network. Throughout the lab, I configured the chris.local domain to include the workstation within my network, but also to include its own DNS server and record system. For the purposes of our network, we are using a copy of Windows Server 2019 for the active directory.

In order to configure the active directory I first connected it to the network as we do with all new devices that we add. (IP configuration through the control panel). Going forward, after completing the basics of the configurations (hostname, machine time zone, and passwords for local admin), we rebooted the system to secure these configurations. Once that was done I then converted it to a domain controller by adding the role through the "Add roles and features" tab in the server manager.

Throughout this process, there were only a few configurations that I specified and changed. These included adding a new local domain and naming it chris.local, creating a new domain admin, and most notable, adding a DSRM password in the event of catastrophic failure.

### **Domain Records**

Once the server was converted to a domain controller, we also added the ability to have DNS records hoisted locally. Most notably in this process, I learned how to add a DNS record manually and how to create a new DNS Zone. In out server, we specifically needed to add a reverse lookup zone for the storage of pointer records.

There are many types of DNS records beyond just the ones that we covered in class. Some of them but not all of them may include:

A - Holds the IP address of a domain. Specifically, used to hold IPv4.

AAAA - Does the same thing functionally as an A record but is used to hold IPv6 records.

Pointer (PTR) - Used to hold the domain name. this is the only DNS record that is used and can be used for reverse DNS lookups.

MX - Mail server exchange DNS record. Allows the record to point to the mail server name but notably will not have an IP address.

TXT - Unformatted plaintext that allows for users to track and debug framework issues and policies. Useful in many other validation methods when working with DNS.

CAA - Used to specify which certificate authorities are able to issue a certificate

### **Adding A Workstation To A Domain**

This was the last part of the lab which included how to add a workstation or a device to a domain. In the example I added my Windows 10 workstation to the domain as a computer. Functionally, this was pretty simple so my notes on the process are quite limited. Simply access the control panel after making sure that the local time on the machine and hostname are accurate, go to System, and follow the wizard to add the computer to the domain. The most notable step in this process is the fact that you will need _**domain admin**_ credentials in order to add the computer to the domain. Once done the computer needs to be reset.
