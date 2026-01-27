# Lab #2: Time Zones and Logging

## RW01 Setup

To begin this lab, on all of the current systems but for this first step more specifically, rw01, I had to go in and change the format of the rsyslog service to log in the extended time format for the logs so that it can show the time zone and the full extended time. To do this, I went into the configuration file for the service and commented out the line that said "ActionFileDefaultTemplate RSYSLOG TraditionalFileFormat". Once the line was commented out the log format looked like the screenshot below in deliverable 1.

## Deliverable 1

The screenshot below shows the rw01 VM and the rsyslog in the proper time format. The green box shows the updated format as well as the old logs pre update and the red box shows the first test with the outdated log time format on rw01.

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

## Web01 and Log01 Setup

After doing this on rw01, I did the exact same thing on log01 and web01. The deliverables below show the screenshots detailing the changes.

## Deliverable 2

The screenshot below shows the before and after changes to the rsyslog service on web01. The green box shows the correct updated format and the red box highlights the logs from before editing the configurations.&#x20;

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 3

The screenshot below shows the changes after editing the configurations to the rsyslog service on log01.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Setting Up Mgmt01

This VM is a XUbuntu box that will exist on the LAN for my network. This box will be on the LAN segment of my network which means another segment will be added to the network diagram. I actual already configured this segment properly in the last lab so please refer to the complete list of vyos configuration commands that I had used previously to see the LAN segment settings. Once I gave the box a proper IP address and created a named user, I installed CRD onto the box for remote access.

Let it be known that I will never intend to use CRD in any of my connections since I use the VPN most of the time but for the sake of the lab, the google bloatware exists on my mgmt system now.&#x20;

## Deliverable 4

The screenshot below shows my chrome remote desktop session into my mgmt VM which is in an ssh session to my log01 VM.&#x20;

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## Log01 Reformatting

Now that the service is configured properly on all machines, I went in and backed out the changes that I had made to the rsyslog service last week in terms of the configurations. Going forward, I will be making a sec250.conf file in the rsyslog.d configuration directory so that the configurations can be added through the custom file and not manually.&#x20;

I started making this file by using wget to scrape the configurations made [here](https://raw.githubusercontent.com/gmcyber/sec350-share/main/03-sec350.conf) by Devin (GMCyber) as provided by the lab.

Once this file existed in the rsyslog.d directory and the configurations were changed from last week to be recommended out, I restarted the service to test the new logging format.&#x20;

INCREDIBLY IMPORTANT: Not mentioned in the lab, however, it is important to uncomment out the last line of the rsyslog configuration file that mentions the forwarding to target host on the client machines that are being forwarded from. This will target an IP and port for the logs to be sent to and otherwise it will not work.&#x20;

## Deliverable 5

The following screenshot shows me navigating to the new directory (/var/log/remote-syslog/web01-chris/) and reading the testing log from the remote host that I created using the logger command on web01 and the resulting log in the newly created file.&#x20;

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

