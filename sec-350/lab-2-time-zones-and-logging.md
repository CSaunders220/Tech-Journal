# Lab #2: Time Zones and Logging

## RW01 Setup

To begin this lab, on all of the current systems but for this first step more specifically, rw01, I had to go in and change the format of the rsyslog service to log in the extended time format for the logs so that it can show the time zone and the full extended time. To do this, I went into the configuration file for the service and commented out the line that said "ActionFileDefaultTemplate RSYSLOG TraditionalFileFormat". Once the line was commented out the log format looked like the screenshot below in deliverable 1.

## Deliverable 1

The screenshot below shows the rw01 VM and the rsyslog in the proper time format. The green box shows the updated format as well as the old logs pre update and the red box shows the first test with the outdated log time format on rw01.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Web01 and Log01 Setup

After doing this on rw01, I did the exact same thing on log01 and web01. The deliverables below show the screenshots detailing the changes.

## Deliverable 2

The screenshot below shows the before and after changes to the rsyslog service on web01. The green box shows the correct updated format and the red box highlights the logs from before editing the configurations.&#x20;

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 3

The screenshot below shows the changes after editing the configurations to the rsyslog service on log01.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Setting Up Mgmt01

This VM is a XUbuntu box that will exist on the LAN for my network. This box will be on the LAN segment of my network which means another segment will be added to the network diagram. I actual already configured this segment properly in the last lab so please refer to the complete list of vyos configuration commands that I had used previously to see the LAN segment settings. Once I gave the box a proper IP address and created a named user, I installed CRD onto the box for remote access.

Let it be known that I will never intend to use CRD in any of my connections since I use the VPN most of the time but for the sake of the lab, the google bloatware exists on my mgmt system now.&#x20;

## Deliverable 4

The screenshot below shows my chrome remote desktop session into my mgmt VM which is in an ssh session to my log01 VM.&#x20;

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Log01 Reformatting

Now that the service is configured properly on all machines, I went in and backed out the changes that I had made to the rsyslog service last week in terms of the configurations. Going forward, I will be making a sec250.conf file in the rsyslog.d configuration directory so that the configurations can be added through the custom file and not manually.&#x20;

I started making this file by using wget to scrape the configurations made [here](https://raw.githubusercontent.com/gmcyber/sec350-share/main/03-sec350.conf) by Devin (GMCyber) as provided by the lab.

Once this file existed in the rsyslog.d directory and the configurations were changed from last week to be recommended out, I restarted the service to test the new logging format.&#x20;

## Deliverable 5

The following screenshot shows me navigating to the new directory (/var/log/remote-syslog/web01-chris/) and reading the testing log from the remote host that I created using the logger command on web01 and the resulting log in the newly created file.&#x20;

<figure><img src="../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Forwarding Auth Logs from Web01 to Log01

Next, once I was able to format the logs properly and transfer them between the machines I was tasked with forwarding the authentication logs between web and log. For this, the following one line of code was added the web01's configuration file.

```
authpriv.* @@172.16.50.5
```

## Deliverable 6

The screenshot below shows the ssh logs on log01 through the ssh window in mgmt01-chris. This log was transfered from web01 and onto my log01 box via the rsyslog configurations made in the previous deliverable.&#x20;

<figure><img src="../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

## Logging Auth Events on Fw01

Now that web had been added to the logging circle, it was time to add fw01-chris to this. This is a VyOS machine which is build on debian linux so the process was relatively similar at the end of the day.

In order to get vyos to sent syslog notifications to log 01, the following set of commands were used to configure this service.

```
configure
set system syslog remote 172.16.50.5 facility authpriv level info
commit
save
```

As a side note, I had already done this by the time this lab was around but to change a password for a user on vyos, the following command can be used.

```
configure
set system login user <username> authentication plaintext-password <new_password>
commit
save
```

## Deliverable 7

The following screenshot illustrates the tree format of the remote-syslog directory containing now my web01 and fw01 subdirectories as well as the failed logs from when I had failed to log into my vyos firewall VM.&#x20;

<figure><img src="../.gitbook/assets/image (165).png" alt=""><figcaption></figcaption></figure>

## Deliverable 8

The following image is the updated version of my network diagram for this environment. This now contains the mgmt01 box on the LAN network segment as well as all of the new services.

<figure><img src="../.gitbook/assets/image (166).png" alt=""><figcaption></figcaption></figure>
