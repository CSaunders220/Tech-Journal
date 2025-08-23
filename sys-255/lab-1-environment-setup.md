# Lab #1: Environment Setup

### **Setting Up A Firewall and a Workstation:**

This lab covered the basic principles of setting up a firewall in conjunction with a simple workstation. In order to achieve this the workstation has to be connected to the firewall, the firewall to the upstream gateway, and the configurations for the networking adapters of both machines must be completely, and correctly, configured.

### **Notes on Firewall Machine:**

The hardest part about setting up the firewall machine was making sure that the DNS server was set properly in the GUI version of the configuration and that the ethernet ports were set up properly between VMware and the actual PFsense machine. Using the MAC address as recommended in the lab assisted greatly in making sure not to cross the two ports and confuse them with each other. In the end, the WLAN port became eth0 on the firewall and the LAN port to my workstation became eth1.

Something that I was vaguely familiar with prior to this lab but did not look into until now was the idea of DHCP. In the lab, it was required that this be disabled for both the WLAN and LAN configurations in the firewall. After researching what the acronym means (Dynamic Host Configuration Protocol) I understood more clearly _why_ this needed to be disabled. The DHCP controls the assigning of IP addresses but for the case of this lab and this environment it is not necessary and would cause confusion later down the line when assigning the rolls in the AD and in the later and more complex architecture configurations. In summary, it was disabled form the machine to avoid future problems.

Moving further into the actual configurations of the machine, the DNS server chosen and used in my environment was 8.8.8.8 which is the Google DNS server. Alternatively, the secondary server 1.1.1.1 was used which is the primary server for Cloudflare which is a bit more filtered than the google DNS server but would work in a pinch should something go wrong with the primary server. Alternatively to the Google DNS server address, there is a mirrored version of this DNS server that can be reached at 8.8.4.4 which can be useful for future reference.
