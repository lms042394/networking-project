# Networking Protocols and Features Overview

In this walkthrough, we will go over the five (5) most often used protocols in computer networking and firewalls, and how they can be used in a client-server setting (for example, an employee's and IT worker's respective workstations).

## Technology used

* Cloud-based virtual machines in Microsoft Azure (alternatively, Oracle VirtualBox or VMware Workstation can be used for locally-installed virtual machines)
* Windows App (similar to Microsoft Remote Desktop)
* Windows PowerShell
* Wireshark
* Windows 10 Pro (the client)
* Ubuntu Server 22.04 (Linux server)

## Walkthrough
### Setup
**Video version:** <https://youtu.be/76Z_3Tqazwc>  
![Screenshot](https://i.imgur.com/BSz8Y0i.png)  

1. Go to [the Microsoft Azure portal](https://portal.azure.com/) and log in with your Microsoft account.
2. Open the Resource Groups section, click Create (take note of the region), and fill in the fields.
3. Search for and open the Virtual Machines section, click Create to make the first virtual machine, and select the following settings:
	* Change the zone if needed to match the region of the Resource Group you made in step 2.
	* Windows 10 Pro, 64-bit, 22H2
	* 2 vCPU's and 8 GB RAM
	* Set a username and a password to log in.
	* Confirm that you have Windows 10 use rights.
	* Go to the Networking tab, and create a new Virtual Network from there.
4. Click "Review + Create", and wait for the first virtual machine to finish deploying.
5. Return to Virtual Machines, click Create to make the second virtual machine, and select the following settings:
	* Select the same resource group that you created in step 2.
	* Change the zone if needed to match the region of the Resource Group you made in step 2.
	* Ubuntu Server, 64-bit, 22.04
	* 2 vCPU's and 8 GB RAM
	* Set a username and a password to log in, instead of an SSH key.
	* Go to the Networking tab, and select the same Virtual Network you made in step 3.
6. Open Microsoft Remote Desktop or the Windows App:
	1. Windows: Click the taskbar search bar, then look up and open Microsoft Remote Desktop from there.
	2. Mac OS X: Search for and download the Windows App from the Mac App Store, and open it from there.
7. Go to the Virtual Machine section of the Azure portal, and copy the Windows 10 virtual machine's public IP address:
	1. Return to Microsoft Remote Desktop, and paste the same IP address to launch the virtual machine.
	2. Click the Add (+) button in the top-right corner of the Windows App, and paste the same IP address to launch the virtual machine.
8. Log in with the credentials you set back in step 3.
9. Install Wireshark inside the Windows virtual machine from [this link](https://www.wireshark.org/#downloadLink).

### ICMP (Internet Control Message Protocol)
**Video version:** <https://youtu.be/CHwUp0Q2vWA>  
![Screenshot](https://i.imgur.com/exlKwSC.png)  

1. Open Wireshark, and click the Ethernet or Wi-fi with a signal bar icon to begin analyzing the packets going through your network.
2. Type `icmp` in Wireshark's search bar to filter for ping-related packets only, which use ICMP with no port number.
3. In the Azure portal, click on the name of the Ubuntu Linux virtual machine to open its cloud-based settings, then copy its private IP address.
4. Open PowerShell in the Windows virtual machine, type `ping` with a space, paste the private IP address, and press Enter or Return. (It will look something like `ping 10.0.0.5`.)
	* Ping packets in both PowerShell and Wireshark will then appear.
5. Run `ping www.google.com` and observe the resulting ping packets.

### Firewall via an NSG (network security group)
**Video version:** <https://youtu.be/tZaAPWZgKho>  
![Screenshot](https://i.imgur.com/eFcrmmn.png)  

1. Create an ongoing ping in PowerShell by typing `ping -t`, paste the private IP address in between, then pressing Enter or Return. (It will look something like `ping 10.0.0.5 -t`.)
2. In the Azure portal, launch the Ubuntu virtual machine's settings, and click "Network Settings" in the left sidebar under Networking.
3. Under the Rules in the right side, click "Create Port Rule".
4. Add an inbound security rule with the following settings to make the firewall block the ping packets you got going in step 1:
	* Destination port ranges: * (to cover any and all ports)
	* Protocol: ICMPv4
	* Action: Deny
	* Priority: 290 (for this rule to take precedence over the other default rules)
5. Delete the denying rule you made in step 4 to make the firewall allow ping packets again.
6. Stop the ongoing ping by clicking inside PowerShell and pressing `Control-C` on your keyboard.

### SSH (Secure Shell) to remotely access a computer via its CLI (command line interface)
**Video version:** <https://youtu.be/fv7tPi7M1Uk>  
![Screenshot](https://i.imgur.com/xWiYLyE.png)  

1. In Wireshark, type `ssh` or `tcp.port == 22` to view related packets only.
2. In PowerShell, log into the Linux virtual machine by typing `ssh username@address`, where: (It will look something like `ssh myname@10.0.0.5`)
	* Username: What you chose in step 5 of the Setup section.
	* Address: The private IP address you copied in step 2 of the ICMP section.
3. Answer `yes` to continue connecting and enter the corresponding password.
4. Type commands like `uname -a` (which returns your username) and `pwd` (print working directory, which returns the current folder or directory you're currently in) which will generate SSH packets observable in Wireshark.
5. Type `exit` to close the SSH connection to the Linux virtual machine.

### DHCP (Dynamic Host Configuration Protocol) to assign an IP address to a computer
**Video version:** <https://youtu.be/OEvH3bHO8Q4>  
![Screenshot](https://i.imgur.com/CHF0gP9.png)  

1. In Wireshark, type `dhcp` or `udp.port == 67 || udp.port == 68` to view related packets only.
2. Open Notepad and type the below commands:  
`ipconfig /release`  
`ipconfig /renew`
3. Save what you just typed with as a batch file (a file that lets you save and run a bunch of commands in a given sequence) with:
	* File extension `.bat`. (It will look something like `script.bat`.)
	* Your user folder. Navigate there by clicking "This PC" in the left sidebar of Windows Explorer, then the C: drive, then the Users folder, then your own user folder. (It will look something like `C:¥Users¥username¥`.)
4. In PowerShell, run the batch file by typing `.¥` and then its name with no space in between. (It will look something like `.¥script.bat`.)
	* This will cause the virtual machine to disconnect (and release its current IP address) and reconnect (renew, that is, receive a new IP address).
	*  The packets for each step in the full DHCP sequence - discover, offer, request, and acknowledge - will then appear in Wireshark.

### DNS (Domain Name System) to assign human-readable names to IP addresses
**Video version:** <https://youtu.be/WrdQXBvzpJY>  
![Screenshot](https://i.imgur.com/0Rfs9R5.png)  

1. In Wireshark, type `dns` or `tcp.port == 53` to view related packets only.
2. In PowerShell, type the following commands to view the websites' corresponding IP addresses:  
`nslookup google.com`  
`nslookup disney.com`
	* DNS packets for other websites may appear in Wireshark due to the Web browser or operating system accessing resources that are out there on the Internet in the background.

### RDP (Remote Desktop Protocol) to remotely access a computer via its GUI (graphical user interface)
**Video version:** <https://youtu.be/V7hlqQTGKOQ>  
![Screenshot](https://i.imgur.com/eSyit5n.png)  

1. In Wireshark, type `tcp.port == 3389` to view RDP-related packets only.
	* RDP packets will appear regardless of any commands or keystrokes typed, because an active connection between your computer and the virtual machine in the cloud is being maintained.
	* More packets may appear when actively moving the cursor (mouse/trackpad) or typing.
