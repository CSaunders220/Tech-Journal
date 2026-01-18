# Lab #1: Environment Setup

## Setting Up Road Warrior

Road Warrior is a virtual machine in the network that will exist on the class WAN outside of my personal network. This is an example of a remote worker or a computer that is not part of the enterprise network directly but may need to access the company's resources.

Setting up the VM is rather simple because this machine is only on the class WAN so the steps that I took to initialize this VM was to re-cable the VM to be on the WAN, create a named user on the Linux machine and add them to the sudo group, changed the password and secured the default account, set a static IP address for the machine, and changed the hostname. With all of these steps completed I left Road Warrior alone for future testing of firewall rules.

## Deliverable 1

The following screenshot has the champlain.edu website highlighted as well as the current user, IP address, and the hostname in the screenshot showing that Road Warrior is completely set up for this stage of the lab.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## Setting Up Vyos Firewall

VyOS is a firewall and router operating system built on top of Debian Linux. This is a system that is configured entirely through a conf file and through terminal commands.&#x20;

This primary firewall (fw01-chris) will server as the primary firewall that will have the WAN upstream and then will have the DMZ and the LAN branching from it (network diagram at the bottom of the page).&#x20;

The following commands were used to configure the VyOS machine for the purposes of my environment currently.

```
configure
set system login user vyos authentication plaintext-password <new_password>
commit
save
exit
reboot
configure
set system host-name fw01-chris
commit
save
exit
reboot
configure
set interface ethernet eth0 description "SEC-350-WAN"
set interface ethernet eth1 description "SAUNDERS-LAN"
set interface ethernet eth2 description "SAUNDERS-DMZ"
set interface ethernet eth0 address 10.0.17.126/24
set interface ethernet eth1 address 172.16.150.2/24
set interface ethernet eth2 address 172.16.50.2/29
set nat source rule 10 outbound-interface name eth0
set nat source rule 10 address 172.16.150.0
set nat source rule 10 translation address masquerade
set nat source rule 10 description 'LAN to WAN'
set nat source rule 20 outbound-interface eth0
set nat source rule 20 address 172.16.50.0
set nat source rule 20 translation address masquerade
set nat source rule 20 description 'DMZ to WAN'
set protocols static route 0.0.0.0/0 next-hop 10.0.17.2
set system name-server 10.0.17.2
commit
save
exit
```

Once these steps were completed my interfaces list looked as it does in the screenshot below with all three interfaces showing their proper IP addresses, netmasks, MAC addresses, and names set in the description box.&#x20;

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 2

The following screenshot shows my VyOS firewall pinging google.com from the command line interface. The pings are successful meaning that the NAT and all of the DNS forwarding settings as well as the IP addressing is set correctly.&#x20;

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Configuring Web Server

web01-chris aka my web server will live on the DMZ segment of the network. This is because I want this area to be accessible for its purposes as intended but not accessible for any more than what it needs to be so that attacks may not traverse my networks across subnets.&#x20;

This machine is a Rocky OS VM that will be hosting a simple web page for later testing.&#x20;

First and foremost, I created a chris.saunders user, set the password, added to wheel, and secured champuser. I also set the hostname before rebooting the VM. Next I set the IP address according to the IP assignment sheet (statically).&#x20;

## Deliverable 3

The web server is now configured at the basic level. The following screenshot shows the successful ping to 8.8.8.8 but the failed DNS resolve of google.com because at this time my firewall is not configured for forwarding DNS to the DMZ network.&#x20;

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## Setting up DNS Forwarding

The following commadns must be added the the VyOS command configuration in order to allow for DNS forwarding from the DMZ and from the LAN. Once completed, the DNS attempts to resolve in the DMZ and in the LAN will be successful.

```
set service dns forwarding cache-size 0
set service dns forwarding listen-address 172.16.50.2
set service dns forwarding listen-address 172.16.150.2
set service dns forwarding allow-from 172.16.50.0/29
set service dns forwarding allow-from 172.16.150.0/24
```

## Deliverable 4

The following screenshot was taken on web01-chris after configuring the DNS forwarding from my DMZ to the internet and therefore allowing for the google.com ping to resolve and be successful.&#x20;

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## Configuring Logging VM

log01-chris will be the aggregator of the logs from the web server for later visualization from a centralized space. At this time however, I completed the basic configurations for the VM including setting the VM hostname to log01-chris, creating a named account, changing passwords and securing default users, and setting the IP addressing.&#x20;

## Deliverable 5

The following screenshot shows the results of my 'ip a' command on my log01-chris VM as well as the successful output of a google.com ping on this VM.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

## Configuring HTTP Service

Next, circling back to the web01 VM, I now moved to install httpd and configure the local firewall to enable ports 80 and 443 for web traffic. The following commands were used to do so.

```
sudo yum install httpd
sudo systemctl enable httpd
sudo systemctl start httpd
sudo firewall-cmd --zone=public --permanent --add-port=80/tcp
sudo firewall-cmd --zone=public --permanent --add-port=443/tcp
sudo firewall-cmd --reload
```

In order to test this new firewall and the ability to reach the http test web page from road warrior, I had to route in the XUbuntu networking configurations from the WAN to the DMZ. To do this, I navigated to the Ubuntu network connections GUI > IPv4 > Routes, and added a route to the DMZ network ID with subnet mask (172.16.50.0/29) that routes to my firewall WAN interface (10.0.17.126).

## Deliverable 6

The following screenshot shows my connection from my Firefox browser via the ID address of my web server with the system's hostname and user as well.&#x20;

<figure><img src="../.gitbook/assets/image (162).png" alt=""><figcaption></figcaption></figure>

## Configuring rsyslog Service

