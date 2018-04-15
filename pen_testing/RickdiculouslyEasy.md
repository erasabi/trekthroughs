
				Penetration Testing w/Rick and Morty Walkthrough
						by Elan Rasabi

Okay so I’ve got my attack machine (Kali Linux) in range to communicate directly with my target machine (Fedora Linux). My assignment is simple, find my target on the network and get any all information I can on it to reach the end goal of getting root (administrator privileges) essentially owning the target. There will be flags located in inside the target Virtual Machine (VM) to help me know that I am going down the right rabbit holes. So I will break this Penetration Test down as best as I can into the fundamental phases of the job, which is best represented by this flow diagram I found.

[brute force](http://hmarco.org/data/Preventing_brute_force_attacks_against_stack_canary_protection_on_networking_servers.pdf)

#1 Recon: 		Collect information on anything that help you understand the target.
#2 Scanning: 		Discover operating systems, open ports, running services, vulnerabilities
#3 Enumeration (often falls under scanning):  	
Gathering information that is more active. Establishing connections and interacting with the target. You can use what you have learned from scanning to enumerate more intelligently.
#4 Gaining Access: 	Getting usernames, passwords, and other credentials to gain ownership of anything
#5 Maintain Access:	Establishing persistence; Making sure you will not lose what access you have gained.
#6 Covering Tracks: 	Changing logs, closing backdoors, hiding any traces you were ever there.

**Test virtual machines do not usually involve certain phases of real life hacking such as:
#1 Heavy Recon: 
-The target doesn’t have a company with a hierarchy and history to research
-No employees to gather information on via social media and email
       	#5 Maintaining Access: No IT department to defend the vulnerable network from attackers or fix issues
	#6 Cover Tracks: 	No one cares if you break into my target. In fact that’s the point.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782081-517d3d9e-40bc-11e8-9961-91d0c9d7f757.jpg"></p>

1 Recon: 	Collect information on anything that will help understand the target. 

**An example of internet recon that would come in handy for this VM would be to watch the show Rick and Morty and read up on the character Wikis. In this portion I make my recon work to find the IP address of my target rather than just giving it to you. Just remember your target IP is assigned randomly and will likely be different from mine in this doc.

Well, I know I am in the same subnetwork as my target. Without getting into the nitty gritty of network addressing and subnetting, a good way to think of this is by imagining my Kali box (computer) and the target box as if they were both under a home router on the same local network. This means my device can communicate with the target device directly without having to go through other devices outside the local network or across the internet. I just need a way to identify the target, like its IP address. 

I’ll open up my Kali using Terminator which is just like the regular terminal in Linux, only with added features.

select:  ```Activities (upper left)>Terminator (red icon)``` 
type: ```ifconfig```

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782084-5ad7ca08-40bc-11e8-8e3c-d7c43223fe4e.jpg"></p>

The ifconfig command shows me the details of my Ethernet connection. In blue is my ip address and in red is my netmask, which tells me the range of possible ip addresses that devices in my subnetwork can use. Because 255 fills the first three segments of the netmask, this tells me the range of ip addresses in my local network can be between 192.168.241.0-255. So I know that one of these options with the exception of my own address, must be my target. So let’s scan this range and find out what other live ip addresses there are. There are many tools that can perform scans, but I’m going to use nmap.

type: ```nmap 182.168.241.0-255 in the terminal and press enter```

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782085-5ae660e0-40bc-11e8-87c6-82a561636a4f.png">
<br>Nmap shows the only other viable live address to be 192.168.241.160 aka my target :)
</p>

**There will be another target up on your network for more advanced kids. You can differentiate between the two using steps later in this doc, but for now just know your ip address will have a port open running a service called “zeus admin”.

2 Scanning: 		Discover operating systems, open ports, running services, vulnerabilities 
2.1 Operating System:
Finding out the OS of the target can be done using a technique called banner grabbing. The process of communicating with the target device and using the way in which it responds to determine what type of OS it is. ```nmap –A <targetIP>``` is a common way to do this:

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782086-5af7ba5c-40bc-11e8-855c-d82a00bd4c16.jpg">
<br>The banner grab tells me the OS is a flavor of Unix known as Linux 3.2 - 4.0
</p>

2.2 Open Ports:
Ports are needed to allow hardware and software on a device to communicate and share resources without interfering with each other. Running nmap by default, I was able to discover open ports as well. But I will add the ```-p-``` flag to ensure all 65,557 possible ports are scanned.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782087-5b072d66-40bc-11e8-9e94-7e179235637c.jpg">
</p>
Open ports detected:
	Port 21: File Transfer Protocol (ftp) allows for remote access to the file system 
	Port 22: Secure Shell (ssh) allows remote access to the terminal (aka shell)
             Port 80: Hypertext Transfer Protocol (http) is the default port for a webserver
             Port 9090: Zeus-Admin which quick google search shows to be a webserver
             The other ports are not on dedicated and running services my scan cannot ID 

2.3 Service and vulnerability detection 
A great way to find vulnerabilities is to discover what services are running on each port and check for known vulnerabilities with that service. Running nmap with the ```–A``` flag is also good for service detection and can even reveal existing vulnerabilities, coupled with ```-p``` so I can specify the ports to scan.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782088-5b1eb86e-40bc-11e8-9fa0-550b36726786.jpg"></p>
Discovered Services and possible vulnerabilities:
Port 21: ftp version is vsftpd 3.0.3, vulnerability: anonymous login allowed
             Port 80: Webserver is an Apache 2.4.27 running on Fedora (Linux) and serving the page Morty’s Website 
             Port 22222: Service is OpenSSH 7.5 and the ssh-hostkeys are shown below
	All other ports: No helpful information has been found 

3 Enumeration:  	Gathering information that is more active. Establishing connections and interacting with the target. I can what I have learned from my scanning to enumerate more intelligently.

3.1 Port 21 (FTP)
Although vsftpd has exploits available, my scans showed that anonymous ftp login is allowed. So let’s try logging into ftp as anonymous. 

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782089-5b2c772e-40bc-11e8-96e6-d0886c032ae3.jpg">
<br>Worked. Now I’ll grab that Flag.txt I saw from my scan…</p>

3.2 Port 22 (SSH)
SSH gives me access to the target’s terminal with the privileges of whichever user I are, including root, which is just as good as being the target. If I find usernames and passwords throughout this enumeration phase. Using them to SSH into the target will give me access to everything they can

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782090-5b3a7022-40bc-11e8-84cb-63d6413d11bf.jpg">
<br>Okay, got it and left ftp. Now the file should be right in the ```home(~)``` directory of my Kali box</p>

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782091-5b498044-40bc-11e8-8648-ae62c33ecf22.jpg">
<br>Using cat you can see what it says in FLAG.txt and that I now have 10/130 points!</p>

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782092-5b5808ee-40bc-11e8-88e5-9d3358bee5fa.jpg"></p>

