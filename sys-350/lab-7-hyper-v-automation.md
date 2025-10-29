# Lab #7: Hyper-V Automation

## Overview



## Configuring a Parent Disk

For the purpose of cloning, I created a new Ubuntu VM with the following specs:

* Xubuntu 25.10
* 2GB RAM
* 2 vCPU
* No secure boot
* Connected to WAN



I also ran the following prep [script](https://github.com/gmcyber/480share/blob/master/hyperv-ubuntu-sealer.sh) supplied by the professor and developed by [GMCyber](https://github.com/gmcyber). This script does some basic configurations to prepare an ubuntu VM for cloning on a mass scale.

```
#!/bin/sh
#script to prepare ubuntu desktop vm for cloning
apt-get dist-upgrade

#open ssh
apt-get install -y linux-tools-virtual linux-cloud-tools-virtual
cat /dev/null > /var/log/wtmp 
cat /dev/null > /var/log/lastlog 
rm -rf /tmp/*
rm -rf /var/tmp/*
rm -f /etc/ssh/ssh_host*
rm -f /etc/udev/rules.d/70-persistent-net.rules
cat <<EOL | sudo tee /etc/rc.local
#!/bin/sh -e
test -f /etc/ssh/ssh_host_dsa_key || dpkg-reconfigure openssh-server
exit 0
EOL
echo ubuntu > /etc/hostname
apt-get clean
history -c
history -w
chmod +x /etc/rc.local
systemctl stop apt-daily-upgrade.timer
systemctl disable apt-daily-upgrade.timer
systemctl stop apt-daily.timer
systemctl disable apt-daily.timer
sudo apt autoremove -y
#truncate the machine id to avoid duplicate dhcp
echo > /etc/machine-id

echo "remove git repo and then issue a shutdown - h now"
```

