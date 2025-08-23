# Lab #7: Apache and Web Servers

### **General Overview**

This lab introduced a new box to my environment, web01. This VM was meant to host the Apache service which I configured on the machine as well as a few other things to be able to join the Linux (CentOS) box to my domain (chris.local). There were some small bumps along the road that I faced but none of them too major. Those will be outlined below under the issues and errors section of this reflection. Overall, this was a new and uncharted territory that I have not faced before but I believe that i got a good grasp on what need to be done going forward in order to get a better understanding of these topics.

### **Joining the Domain**

Joining the Linux box to the domain was a tedious process that took me a little while to do simply because of an issue in the process. Below are the steps that I took in order to get the box to be discoverable on the AD:

* Installed the necessary services as listed below:
  * realmd: The main component of joining a Linux box to an AD domain. This is the service that allows you to discover and join the boxes to a windows domain.
  * samba and samba-common: A service that allows for file share and print sharing between different types of OS' in a domain. -oddjob and oddjob-mkhomedir: A service that helps with task management and the message bus.
  * sssd: stands for system security services daemon and allows for the access of remote directories and helps with remote user authentication.
* Change the time zone of the Linux box to match my AD
  * **THIS IS A CRITICAL PART**, without doing this the authentication processes for joining and logging will be completely and entirely messed up from the start and the remote connections will not work. Times between the machines must be no longer than five minutes difference.
* Use the following command: realm join --user=chris.saunders-adm@CHRIS.LOCAL CHRIS.LOCAL
  * Note: the domain using the realm command must be in all capitals for the command to work!
* Verify that the computer has joined in the users and computers windows in the AD.

### **Apache and Firewall Configurations**

The first part of this lab focused on the installation of Apache and the configurations that must be made in order to get it to connect properly within the domain. The following steps were the steps that I followed to get Apache to work as intended:

* Install httpd (Apache)
* Start and enable apache
* Use the command: sudo firewall-cmd --permanent --add-port=80/tcp
  * This opens the port and sets this open status to permanent so that the port never closes. This is the command for only http (port 80).
* Use the command: sudo firewall-cmd --permanent --add-port=443/tcp
  * Similar to the above command, this one is a permanent opening of port 443 aka https.
* Use the command: sudo firewall-cmd --list-all
  * Verifies that the two ports that you just opened actually processed on your firewall

Once these steps are done, you should be able to go to another machine on your domain, open the web browser, and get to the http test page that is defaulted to Apache services.

### **Issues and Errors**

Throughout the lab, I struggled primarily with the joining of the machine to the domain. This took me quite a while only because I did not know that the realm command needed the domain name to be in all capital letters. I had researched this command and there are a couple different variations to it but I eventually found that the only thing that was stopping me was the domain capitalization. This was a challenge because the error that it threw was improper domain credentials and a Kerberos error while authenticating on the domain so I believed it to be a connectivity issue at first and wasted my time.
