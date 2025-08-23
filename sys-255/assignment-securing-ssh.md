# Assignment: Securing SSH

### **Procedure**

Generally speaking, to change the SSH abilities of the root user, you would have to change the config file of the sshd\_config file which is located in the /etc/ssh directory. As pictured below, the line which has to be changed is under the authentication section of the file and is, by default, set to "yes" but I have already set it to "no" as shown in the screenshot.

![Root\_Login\_No](https://github.com/user-attachments/assets/631ec351-8e22-4c49-afdd-5acc1a807b2b)

Once this is done, there should be no ability for the root user to ssh into the machine through powershell or PuTTY.

### **Issues Encountered**

In the course of this assignment, I encountered some interesting issues while working to disable the root user ssh abilities. After disabling the ssh ability in the config file, I attempted to log in as the root user through my workstation. While it was denied, the log showed a completely different denial reason. The log file after attempting to ssh in as root read as follows:

![Logs of failed password attempts](https://github.com/user-attachments/assets/03af33b4-b393-489b-83e8-aef4626d8af1)

In light of this, I attempted to change the password of the root user in case it was truly a typo or a memory issue where I was using the wrong password but even after changing the password to something definitely memorable, the issue persisted. Regardless of the password, the attempt to remote into the machine as root was always denied, even when using the correct password as tested by logging into the machine directly.

One running theory is that the version of OpenSSH that was being used (8.0p1) may be outdated and this may be a logging visual bug that was since corrected. Another theory is that while the config file root user permission is set to "no", the logs are presenting as if the setting was set to "prohibit-password". This setting would disable the ability to input a password and therefore render the user unable to login and would log it as password error. At the time of writing this journal, I am unsure of the cause of the issue but I will continue to investigate.
