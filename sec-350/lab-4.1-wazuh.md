# Lab #4.1: Wazuh

## Installing and Configuring Wazuh Service

the actual install of the wazuh server service is relatively easy because there is a provided installation script for the server side appliance. The service can be started and installed using the following command.

```
curl -sO https://packages.wazuh.com/4.3/wazuh-install.sh && 
sudo bash ./wazuh-install.sh -a -i
```

Once completed, the admin username and password printed to the terminal which can be used to log into the dashboard of the service.&#x20;

It is also important to note that the dashboard serves over port 443 and that since it is an ubuntu box the internal firewall has to be set up for it to be able to reach out. The following commands can be used to add a port to the ufw firewall list and reload the firewall.&#x20;

```
sudo ufw enable
sudo ufw allow 443
sudo ufw reload
```

Once the firewall was all set, I transferred over to my mgmt01-chris VM to test the web page. If done properly, the admin username and password prompt can be seen once the page is loaded via IP similar to below.

<figure><img src="../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

## Deliverable 1

Once fully logged into the service dashboard using the default admin creds provided by the install script, the dashboard was visible from my MGMT01 VM as seen in the screenshot below.

<figure><img src="../.gitbook/assets/image (168).png" alt=""><figcaption></figcaption></figure>

## Installing Wazuh Agent on Web01

To install the agent, the commands are rather simple because the dashboard supplies most of the commands to install the service on the different OS'. One thing of note for this part though is that the chkconfig service is REQUIRED on the rocky images that we are using in order for the agent to run so that must be manually installed first before the agent installation can take place.&#x20;

The following commands were run on web01 to install the agent.&#x20;

```
sudo yum isntakk chkconfig -y
sudo WAZUH_MANAGER='172.16.200.10' WAZUH_AGENT_GROUP='linux' yum install https://packages.wazuh.com/4.x/yum/wazuh-agent-4.3.11-1.x86_64.rpm
sudo systemclt daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

The installation command was retrieved by entering the following information into the agent configuration page on the wazuh dashboard.

* Redhat/CentoS
* CentOS 6 or higher (Note, it will work on Rocky)
* x86\_64
* 172.16.200.10
* Linux

## Deliverable 2

The screenshot below is the wazuh dashboard after i had registered my web01 host meaning that the dashboard is now populated with information.&#x20;

<figure><img src="../.gitbook/assets/image (169).png" alt=""><figcaption></figcaption></figure>

## Deliverable 3

Once the agent was installed I used the ssh command from my mgmt01 box to simulate a log in with an invalid user in order to generate the logs and compare what it looks like on the agent dashboard. The screenshot below shows the ssh attempt in the terminal and the log in the agent dashboard.

<figure><img src="../.gitbook/assets/image (170).png" alt=""><figcaption></figcaption></figure>
