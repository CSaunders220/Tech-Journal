# Lab #7: Assessment Prep

## Setting Up A GIT Repo with SSH

A reminder to myself that I didn't have in here documented previously is on how to set up a git repo using an SSH key pair. For some reason I did not have this previously and so the steps are listed below with commands. Anything in all caps must be replaced by whatever it indicates that it should be replaced by.&#x20;

1. ssh-keygen -t ed25519 -f /KEY/STORAGE/DIRECTORY -C "NAME OF KEY"
2. cat /KEY/STORAGE/DIRECTORY
3. Add the contents just printed to GitHub SSH key list with any name given
4. git config --global user.name "USERNAME"   \
   git config --global user.email "EMAIL"
5. git clone -c "core.sshCommand=ssh -F/dev/null -i/KEY/STORAGE/DIRECTORY" git@github.com:USERNAME/REPO-NAME.git

Once I had completed this on my firewall system, I was able to copy the boot configurations found in the /config directory of my VyOS machine to fw01Configs.bak and then add it to the git directory and then push it to my toolkit repo for the class.&#x20;

## Deliverable 1

The screenshot below shows my firewall commands that are backed up in my GitHub. A direct link to the file can be found here as well: [https://github.com/CSaunders220/Toolkit/blob/Tools/SEC-350/fw01Config.bak](https://github.com/CSaunders220/Toolkit/blob/Tools/SEC-350/fw01Config.bak)

<figure><img src="../.gitbook/assets/image (202).png" alt=""><figcaption></figcaption></figure>

## Setting Up NGINX on Ubuntu

This is a rather easy task that only really involves the basic steps for installing the packages. The commands to install and start nginx can be found below.

```
sudo apt install nginx
sudo systemctl start nginx
sudo systemctl enable nginx
sudo ufw allow "nginx http"
sudo ufw reload
```

With this, the nginx service is installed and started and allowed through the system's firewall. Unlike Apache, nginx has a sites available and sites enabled list that you can add sites too, or, by default the /var/www/html directory is forwarded to the sites enabled directory.&#x20;

## Deliverable 2

The screenshot below is of my test page that I made on jump to play with nginx and test its capabilities and configurations.

<figure><img src="../.gitbook/assets/image (203).png" alt=""><figcaption></figcaption></figure>

## SSH Authentication Between Windows and Linux

Some important things that are of note when I was testing this ahead of the assessment:

1. The .SSH file if it does not exist must have 700 permission and the authorized keys file must have 600 permission
2. the home directory may not have more than 700 permission otherwise it will be seen as too permissible and SSH will fail
3. The key can be transferred using the command below assuming that the two machines can ssh to one another and can reach each other on the network.

```
scp $env:USERPROFILE\.ssh\id_rsa.pub <username>@<host_ip>:/home/<username>/id_rsa.pub
```

## Deliverable 3

The screenshot below shows the password less authentication into the ssh session for jump form wks.&#x20;

<figure><img src="../.gitbook/assets/image (204).png" alt=""><figcaption></figcaption></figure>

