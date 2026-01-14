# Lab #1: Environment Setup

```
configure
set system host-name fw01-chris
set interface ethernet eth0 description "SEC-350-WAN"
set interface ethernet eth1 description "SAUNDERS-LAN"
set interface ethernet eth2 description "SAUNDERS-DMZ"
set interface ethernet eth0 name "WAN"
set interface ethernet eth1 name "LAN"
set interface ethernet eth2 name "DMZ"
set interface ethernet eth0 address 10.0.17.126/24
set interface ethernet eth1 address 172.16.150.2/24
set interface ethernet eth2 address 172.16.50.2/29
set service dhcp-server shared-network-name LAN subnet 172.16.150.0/24 option default-router '172.16.150.2'
set service dhcp-server shared-network-name LAN subnet 172.16.150.0/24 option name-server '8.8.8.8'
set service dhcp-server shared-network-name LAN subnet 172.16.150.0/24 lease '86400'
set service dhcp-server shared-network-name LAN subnet 172.16.150.0/24 range 0 start '172.16.150.100'
set service dhcp-server shared-network-name LAN subnet 172.16.150.0/24 range 0 start '172.16.150.200'
set nat source rule 10 outbound-interface name eth0
set nat source rule 10 address 172.16.150.0
set nat source rule 10 translation address masquerade
set nat source rule 10 description 'LAN to WAN'
set nat source rule 20 outbound-interface eth0
set nat source rule 20 address 172.16.50.0
set nat source rule 20 translation address masquerade
set nat source rule 20 description 'DMZ to WAN'
set nat source rule 30 outbound interface eth1
set nat source rule 30 address 172.16.50.0
set nat source rule 30 translation assress masquerade
set nat source rule 30 description 'DMZ to LAN'
set service dns forwarding listen-address 172.16.50.2
set service dns forwarding allow-from 172.16.50.0/24
```
