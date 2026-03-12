# Week #6: NAT and PAT

## Setting Up Static NAT Between Networks

Static NAT can be set between two hosts using the following set of comands. These commands will work providing that routing between the internal and the "router" is already operational and that the external router also has some sort of definition of protocol to do with the packets after they NAT and reach the other side.

```
(config)#interface fastEthernet x/x
(config-if)#ip nat inside
(config-if)#exit
(config)#interface serial x/x/x (or fastethernet / gigabit)
(config-if)#ip nat outside
(config-if)#exit
(config)#ip nat inside source static x.x.x.x (inside source) y.y.y.y (outside address)
```

With these commands, an inside interface will be set, an outside interface will be set, and the nat definition states that nat will operate from inside source, is static, from x.x.x.x ip address range or interface address, and will translate to y.y.y.y on the outside interface.

## Setting Up PAT Between Networks

PAT, unlike NAT, is a many to many solution where the translation pulls from a list of available defined public IP addresses and is tracked with the communications at the router with port numbers in conjunction to IP addresses so that packets can be routed properly. The commands below can be used to define a PAT pool and enable PAT on a cisco router.

```
(config)#interface fastEthernet x/x
(config-if)#ip nat inside
(config-if)#exit
(config)#interface serial x/x/x (or fastethernet / gigabit)
(config-if)#ip nat outside
(config-if)#exit
(config)#ip nat pool test x.x.x.x (pool start) y.y.y.y (pool end) netmask 255.255.255.255
(config)#access-list 1 permit z.z.z.z (net ID of inside) 255.255.255.255 (wildcard netmask)
(config)#ip nat inside source list 1 pool test overload
```

In this scenario, the interfaces still need to be definited as inside or outside for it to work but instead of simply saying that this source address will be translated to this public address statically, the nat statement states that the inside source will pull from a list or a pool called "test" in this case which is defined to have a certain number of IP addressses. This example only has one address in the pool but this can be more addresses if needed.&#x20;

