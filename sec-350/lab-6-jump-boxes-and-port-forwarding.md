# Lab #6: Jump Boxes and Port Forwarding

## Deliverable 1

The first thing in this lab is to remove the direct routing statement from RW01 to the DMZ thusly removing its ability to route to the network because I will be rebuilding it with port forwarding rules. The screenshot below shows the lack of the ability to route to DMZ and the lack of a routing statement.&#x20;

<figure><img src="../.gitbook/assets/image (195).png" alt=""><figcaption></figcaption></figure>

## Setting Up Port Forwarding

In VyOS, the port forwarding is actually called NAT destination rules so it is the reverse of the NAT source rules that I used for IP address NAT between my network segments.

The following commands were the ones I used to allow web traffic over my WAN interface to my web01 VM in the vyos NAT destination rules.

```
set nat destination rule 10 inbound-interface 'eth0'
set nat destination rule 10 protocol 'tcp'
set nat destination rule 10 translation address 172.16.50.3
set nat destination rule 10 translation port 80
set nat destination rule 10 description "http to web"
set nat destination rule 10 destination port 80
```

Now that the forwarding rule was made, I used my rw01 box that still does not have a route statement to curl the WEN interface of my fw01 and the webpage content was viewable from there.&#x20;

## Deliverable 2

The screenshot below shows my curl attempt on my firewall interface from rw01 without the route statement. The curl returns with the web shell that I had installed for the last lab.

<figure><img src="../.gitbook/assets/image (196).png" alt=""><figcaption></figcaption></figure>

## Configuring Jump01

This is a new box for the environment so it needed a new IP, hostname, user, and all of the necessary OS specific configurations. This was an ubuntu server machine so naturally all network configurations were made through netplan.&#x20;

## Deliverable 3

The following screenshots show verification that the proper configurations have been made on my jump01 VM. The bottom screenshot also shows my curling and pinging my web01 neighbor VM and displaying my jump box IP address at the bottom.

<figure><img src="../.gitbook/assets/image (197).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (198).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (199).png" alt=""><figcaption></figcaption></figure>

## Keygen and SSH Security

Now that the basic configurations have been made for Jump, I am now tasked with allowing SSH via key pair form rw01 to jump over the WAN interface of the firewall.&#x20;

One of the firs things that I did was used the following vyos command to set the firewall to listen for SSH only on the internal LAN interface.

```
set service ssh listen-address 172.16.150.2
```

I also generated a keypair really quickly using the "ssh-keygen" command on rw01 so that  have a pair of keys for my jump box.

I created a new user called jump-chris on jump01 and gave it a temporary password for now but once the key pair is over I will not have to enter the password for the user to access the computer via SSH. With the keys generates, I can use the "ssh-copy-id" command with the key pair targeting my jump box after I had temporarily opened the firewall and re-added a route statement so the boxes can temporarily communicate.&#x20;

Once these were done I configured the ssh service to only accept key based authentication, verified the login was capable, and then added a new rule to my firewall to allow ssh from WAN to DMZ at this specific address using the commands below:

```
// Some code
```

I also allowed port forwarding for port 22 over my WAN interface so that my RW01 VM could be able to ssh into the DMZ to the jump box using the following commands:

```
// Some code
```

## Deliverable 4

The screenshot below shows the password less authentication via SSH to my jump box from road warrior. The only password prompt is for the ssh key passphrase which I configured but the user itself is passwordless.&#x20;

<figure><img src="../.gitbook/assets/image (200).png" alt=""><figcaption></figcaption></figure>

## Configuring Wazuh on Jump

For the next part it is important to distinguish that I have two separate users on my jump box, one is a named administrator user and the other is my non privileged jump user as seen in the two identifications below.

<figure><img src="../.gitbook/assets/image (201).png" alt=""><figcaption></figcaption></figure>

With this, I was able to pull the wazuh configurations as provided by the wazuh service through the web portal onto mgmt01 and scp it to jump using teh allowed ssh traffic that I verified at the very start of the lab.&#x20;

