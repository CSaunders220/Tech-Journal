# Lab #3: Linux Basics

### **General Remarks**

At this point in my learning, I am quite familiar with various Linux operating systems and quite often simply test some in my free time so this lab and its contents were quite familiar to me. Using CentOS though was a change for me albeit slight. Most of the commands and functions were similar to other distros that I have used but this was the first time that I have interacted with an OS that was based on Red Hat Linux.

### **Configuration and Setup**

The configuration process was similar to most other machines that I have used within my domain in the lab environment but this time it was of course in Linux. One hurdle that I ran into this time was related to the network configurations. I have worked with Linux machines before but this setup process for CentOS was very different and utilized the "nmtui" command which I have never used before. Once learning that I had to use that command to access the network configurations, the rest was rather intuitive based on the other machines that I have worked with so far.

### Steps For Network Configuration in CentOS

1. Access the network settings using the command “nmtui”
   * **NOTE: use the arrow keys and tab to navigate**
2. Select the device form the menu (In this case the name was ems192)
3. Set the IP configuration to “Manual”
4. Give the proper IP address with the proper subnet marking in /XX form.
5. Give the machine a default gateway.
6. Add the DNS server IP address
7. Save the network configurations.

### **Glossary of Commands Used / Learned**

cat - prints the contents of a file onto the terminal lines.

ls - lists the files in a directory.

cd - changes the active working directory.

tree - shows the list of files and directories that are nestled beneath each other in a branching fashion.

history - shows the command line history. This is also where the arrow keys pull form when cycling past commands.

mkdir - makes a directory at the specified path with the specified name.

echo - prints the input onto the designated location. Prints to the command line by default but can be used to add text to files.

cp - copies a file to a designated path.

man - shows the manual page for a given command. Similar to the -h option for most commands.

pwd - shows the current directory path.

adduser - creates a new user.

passwrd - changes the password of a designated user.

usermod - manages a user's assigned groups. This was used to add the user to the wheel group for sudo privileges.

who - prints the current logged in user as well as login times and methods.

### **Problems and Barriers**

One of the issues that I ran into with this lab was already mentioned and that was figuring out how to access the network configurations. However, I did have another issue while working on a later part of the lab. I also struggled in this lab with managing the working directory. At one point in the lab I had gotten confused with which working directory I was currently in and therefore the "tree" command was not displaying what I needed it to. This is something that I have been known to struggle with in the past and something that I will have to work on in the future when working with Linux systems. In the end, the issue was me misinterpreting the "home" directory. I had assumed that this meant the \~ directory but instead it meant the literal directory called "home". This led to a roadblock for some time but was eventually discovered.
