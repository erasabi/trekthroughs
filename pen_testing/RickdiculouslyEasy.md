
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

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782090-5b3a7022-40bc-11e8-84cb-63d6413d11bf.jpg">
<br>Okay, got it and left ftp. Now the file should be right in the ```home(~)``` directory of my Kali box</p>

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782091-5b498044-40bc-11e8-8648-ae62c33ecf22.jpg">
<br>Using cat you can see what it says in FLAG.txt and that I now have 10/130 points!</p>

3.2 Port 22 (SSH)
SSH gives me access to the target’s terminal with the privileges of whichever user I are, including root, which is just as good as being the target. If I find usernames and passwords throughout this enumeration phase. Using them to SSH into the target will give me access to everything they can

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782092-5b5808ee-40bc-11e8-88e5-9d3358bee5fa.jpg">
<br>SSH is refusing all attempts at remote connection. It seems even if I get the credentials the target has prevented me from signing in with them. Yikes.</p>

3.3 Port 80 (HTTP/Web)
Since I know that port 80 is for web traffic, I’ll plug my target address into my browser search and see where it takes me.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782093-5b663856-40bc-11e8-84ee-600d3d889883.jpg">
<br></p>
Okay. So it takes me to this unfinished website. I used chrome developer tools to see if I could find anything suspicious but there wasn’t really much there, so let’s use some Kali tools and see if they can find any hidden files. 

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782094-5b725924-40bc-11e8-93e2-539ac7e71414.jpg"></p>
So I ran a tool called Dirb, which basically does a dictionary attack on this site using a list of popular filenames that might be associated with content that may be associated with vulnerabilities. Dirb showed me two files I think are especially interesting (robots.txt and passwords). 

a) /passwords
<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782095-5b7ee98c-40bc-11e8-9944-f25e127aacee.jpg">
<br>FLAG.txt reveals another 10pt flag for 20/130pt</p>


<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782096-5b8ac680-40bc-11e8-8030-b9749d24c06c.png">
<br></p>
Opening up the html file looked like Rick was just yelling at Morty, but opening it up in developer tools showed me this comment in green titled password.

b) /robots.txt
Website owners use /robots.txt to give instructions about their site to web bots, like what files they do/don’t want these bots to access. Therefore, checking robots.txt might tell me about hidden files holding possibly sensitive information.
<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782097-5b997d56-40bc-11e8-9788-915a4eb80af4.jpg">
<br>/robots.txt reveals to me several file paths within /cgi-bin directory that I could check out.</p>

Visiting /root_shell.cgi reveals a site under construction. Inspecting the elements shows hidden comments suggesting I have been trolled by Rick and there is nothing to find here. Checking /tracertool.cgi brings me to a page that runs a tracertool, which basically guesses how many hops are needed for a packet to reach its destination. Here’s what I got after typing in the targets IP address.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782098-5ba86d8e-40bc-11e8-9843-fb3db7c443a4.jpg">
</p>
I know this tracertool application is running from my target machine, which is why when using its own IP address to run a trace, it recognized the address as localhost (itself). My first thought after seeing this is to attempt command line injection. This is when an application is allowing me to write commands directly to the operating system of a target through a web interface. So I want to see if I can write some linux commands directly into the targets terminal.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782099-5bb81b62-40bc-11e8-9a9c-476a45e65095.jpg">
</p>
It worked. Typing ```;``` so I can pass a fresh command followed by ```ls –l``` which lists any contents inside the terminals current folder output the proper files just as it would on my own terminal. Now let’s see how I can use this to my advantage! The passwd file located in /etc/passwd holds the names of all existing users on a linux machine. If I can read that file, I can then get all the usernames on this machine and test that with any known passwords. 

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782100-5bc40d50-40bc-11e8-95c5-38e345664ca2.jpg">
</p>
Okay so using the cat command shows me a picture of a cat. Obviously, another troll by Rick telling me he has replaced that command with something else so I can’t use it to read files. What other command can I use to read this file? Well the command tail will do the same thing as cat except it will output exactly N lines of text starting from the bottom of the file. 

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782101-5bd17882-40bc-11e8-800d-0b9f1139d605.jpg">
<br>Tail command successfully output the usernames I was looking for: RickSanchez, Morty, Summer, and apache being the user identification for the apache web server.</p>

3.4 Port 9090: Zeus-admin Webserver

Since Zeus is a web server, the first place I would expect to reach it would be over the internet like with Morty’s Website. Although 9090 is not the dedicated port for HTTP or HTTPS, that does not mean that another port cannot be using them instead. In this case, the port is 9090, so I’ll try to access the target from my browser and specify ```:9090```.   
<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782102-5bde6ef2-40bc-11e8-9fbc-d167be07726f.jpg">
<br>This takes me to a web server login, revealing my next flag for 30/130 pts. Although the login and functionality seems not to be working.</p>

3.5 Port 13337: unknown service

Since some kind of custom service is running on this port that scanning tools weren’t able to identify. My best option is to connect to this service and try to interact with it and find out for myself. 

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782103-5bed0a8e-40bc-11e8-9a51-1e8dc6bdeb8b.jpg">
</p>

Connecting to this service simply responded to me with a flag for 40/130 pts. Pretty easy but this port would not have been found if I had not made sure to scan more than just the default nmap port range.

3.6 Port 22222: unknown service

Looks like I have another date with netcat :)

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782104-5bfac99e-40bc-11e8-878b-a443d8115b13.jpg">
<br>OpenSSH is the open source SSH service. Might I actually have the ability to ssh in after all?</p>

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782105-5c0842f4-40bc-11e8-82b4-0b81af616a4f.jpg">
<br>I guess so!</p>

3.7 Port 60000: unknown service

You know the drill by now people…

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782106-5c158392-40bc-11e8-91f1-266ed6c077ae.jpg">
</p>

Netcatting into 60000 has given me access to a reverse shell, which is basically when a shell (terminal) is communicated from the target –> attack machine rather than the other way around.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782107-5c2438a6-40bc-11e8-91b5-ec5972083f27.jpg">
<br>The commands I could execute in this shell/terminal were really limited, but I was able to find this flag and read it for 50/130 pts.</p>

4 Gaining Access: Getting usernames, passwords, and other credentials to gain ownership of anything

4.1 Summer:
I’ll try and login with one of the usernames I found, using the only password I have so far (winter).

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782108-5c3341c0-40bc-11e8-828b-174b231d016a.jpg">
</p>

I’m in the target machine as user: Summer, whose password is winter. Listing what’s in the directory reveals the flag for 60/130. Now I have access to move all around the file system of my target, but this doesn’t mean it’s over because even though I can see what’s in these directories I might not have the permission to read/write/execute everything. This is why getting root is everything, because it lets you do exactly that by being the user with the greatest privileges.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782109-5c40a1bc-40bc-11e8-84b8-93b91f79b7c2.jpg">
<br>The commands I could execute in this shell/terminal were really limited, but I was able to find this flag and read it for 50/130 pts</p>

Checking where I am in the file system shows the location /home in the directory (folder) /Summer. Cool. So I backed up into the home directory and listed the contents there to find two more dirs (directories) that weren’t Summer: Morty and RickSanchez; the other usernames I found. I’ll look at Morty first…

4.2 Morty

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782111-5c5e3362-40bc-11e8-8fac-fd90a1c88c69.png">
<br></p>

Using the ```–l``` flag allowed me to see what permissions (blue) my user has with Morty’s files, not just their names (red). These permissions show that Summer is allowed only to read(r) the files, but not allowed to write(w) or execute(x). The first file is a zipped text file that asks for a password to unzip it. But even if I had it, my permissions won’t allow me to actually decompress the file. The second is a JPG image file, which I can’t visually see in a terminal because it doesn’t have a graphic interface. Luckily, read permission still allows me to copy the files to my attack machine where I have the highest permissions. This can be done using a number of tools like netcat and SCP, but I’m going to copy the files to Summer’s directory and use FTP to send it to my Kali. 

Copying:

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782110-5c4f25ac-40bc-11e8-9c6d-5fbbe95c2429.png">
</p>

Sending back to Kali w/FTP: 

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782112-5c6c201c-40bc-11e8-99a0-6f8f06587002.jpg">
</p>

After grabbing the two files, I opened the JPG to see if the picture would show me something.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782113-5c8064d2-40bc-11e8-9c21-ef1f8be63943.jpg">
</p>

Worthless. My next guess is to check for Steganography, which is a term for hidden data inside a file, be it pictures, music, etc. An easy tool that checks any file time for human readable words is called strings.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782114-5c919c8e-40bc-11e8-8104-6b2ad5d1ef0e.jpg">
<br></p>

Most of what strings output can only be described as pure garbage. But from the first few results was an entire message telling me the password to my journal zip file (Meeseek). Applying this password gives me journal.txt.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782115-5c9f90b4-40bc-11e8-81b2-f13ca0cfca68.jpg">
</p>

Reading this file gives me some kind of hint about a password and the flag{131333} for 80/130 pts. Now I’ll SSH back into the target as Summer and check out RickSanchez’s directory.

4.3 RickSanchez

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782116-5cac47be-40bc-11e8-8ee8-b41660ec8cf1.png">
<br>So Rick has two directories inside of his own. One that claims not to have flags, which I checked out anyway…more cats. And the other, RICKS_SAFE.</p>

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782117-5cbba9e8-40bc-11e8-8695-94cef23b8fa0.jpg">
</p>

In here I find a file called safe, which I can read but no more. Trying to read this file, however, will produce complete garbage. Why? Because it is not a text file, but a binary file (i.e. program/executable). Permission do not allow me to run this program, but I already explained how to get around this.



<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782118-5cc972f8-40bc-11e8-8df6-0f5a75de2085.jpg">
<br></p>

Root still was not given privileges to run this program, so I need to change those permissions using the ```chmod``` command to allow that. 

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782119-5cd7b53e-40bc-11e8-8b25-13269c23ccb6.jpg">
<br></p>
**Unrelated to Challenge: if you still get an error including the word “libmcrypt” run this command…

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782120-5ce55400-40bc-11e8-9ad0-86effb3cfa32.jpg">
<br></p>
Running the program shows me a conversation between past and present Rick telling future Rick to use Command Line Arguments. Most commands in linux like ls or cat have a similar format that goes like this:

		Format: <command_name> <preference flags> <argument1> <argument n+1> …

So this time when I ran ./safe I passed the argument “idk” just to see what happens.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782121-5cf42a02-40bc-11e8-9a95-f36561062e4e.jpg">
<br></p>

The program output the word “decrypt” followed by nonsense, which hints to me that this program decrypts its contents when passed the right argument, like a key. Attempting to reverse engineer the binary showed me that the binary used a modern encryption scheme too complex to expect to recover the key this way. I could attempt to brute force (try all possible inputs till I get the correct one), but without any constraints to narrow possible options this process could be endless. I am more likely to find the key somewhere hidden in the target machine, if I have not already found it. Which brings me back to my hint about a safe-password/password-safe from the last flag.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782122-5d02da02-40bc-11e8-9c60-b0846d93b053.jpg">
<br></p>

Going back to that I realized I was given the flag as the key to Rick’s safe. Using the number in that flag decrypted the message to give me a hint for user RickSanchez’s password and another flag for 100/130 pts.
The hint is that the flag starts with an uppercase character, followed by a single digit, then one of the words from the name of Rick’s old band. What I need to do here is create a list of all possibilities that meet these constraints (known in Pen Testing as a wordlist or dictionary) and attempt logging in with each one until I get the correct password. Doing either of those manually would be painstaking so I’m going to use Kali tools to do that for me. Also assuming I actually did Recon, I would know that Rick’s old band name was called, The Flesh Curtains.

a) Create a Wordlist/Dictionary:

You can create a custom wordlist with several different tools or write some code in python or another language to do it for you. I decided to go with the command line tool ```crunch``` made specifically for this task. 

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782123-5d124cee-40bc-11e8-9a6b-97d13223e237.png">
<br></p>

Format: ```crunch <min> <max>  - <preference> <syntax in preference>```
min: minimum number of characters in my password 
max: maximum number of characters in my password
-preference: preference specifics for password generation
syntax: making a pattern to better specify what characters should be used where
**for more info type ```man crunch``` into terminal

In this example, I specify my password will be exactly ```10``` characters long and passwords should be generated in a pattern (-t) where the first character is some uppercase letter (,) the second is a digit (%) and the last three are always the word ```Curtains```. Then I funneled those results (>) into a text file called ```rick_wordlist.txt```. I ran crunch two more times to include the possibilities for ```Flesh``` and ```The``` but to keep the dictionary sizes small for the sake of speed I separated the results of each into their own list file.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782124-5d22fd5a-40bc-11e8-823a-affab2a43f92.jpg">
<br></p>

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782125-5d3122cc-40bc-11e8-9fec-99766749e516.jpg">
<br>This is what my dictionary looked like once I finished with crunch…</p>

b) Dictionary Attacking

Then I used the tool hydra, a network logon cracker to quickly attempt all the possible passwords for me.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782126-5d3e2eae-40bc-11e8-9c04-8004677ca083.jpg">
<br></p>
**Again, check out the man pages for hydra using ```man hydra```.

Specifying ```-l```ogin as RickSanchez using ```–P```asswords from rick_wordlist.txt, I attempted to SSH into my target using port ```-s``` 22222.  I used ```8 –t```hreads testing passwords to amount of login attempts per minute, even though hydra recommends you do not use more than 4 for SSH, and ```–f``` so that hydra would stop running when it got its first match. Hydra revealed ```P7Curtains``` as the password for RickSanchez.

4.4 Becoming Root(Administrator)

With the password to RickSanchez, I logged into the target machine. 
Then I used the tool ```hydra```, a network logon cracker to quickly attempt all the possible passwords for me.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782127-5d4c46ce-40bc-11e8-8ac2-455eb01ca279.jpg">
<br></p>

I know that when I was logged in to the target as Summer I was already able to move laterally through the target to obtain, and eventually use, all the content in both Rick and Mortys’ user accounts. So I know becoming Rick will not give me access to new files in Rick’s directory. Going back to Rick’s password hints found earlier, the last sentence makes a reference to sudo and uses the word wheely instead of really. 

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782128-5d59a1fc-40bc-11e8-9c68-acb8770b41dc.jpg">
<br></p>

Sudo is a command that allows users to perform actions they do not have the permissions for by either proving they have the password to root OR being given special permissions which would have to be stated in the sudoers file located in /etc/sudoers. 

The Wheel Group is a special user group used in Linux to control access to the sudo command. If users are in this group, they will be allowed to use any commands specified for wheel in the sudoers file as if they were the superuser (aka root).

While checking what groups RickSanchez is a member of shows he is, in fact, a member of the wheel group.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782129-5d668e58-40bc-11e8-8f23-4254f4c0d2ec.jpg">
<br></p>

I also checked for group associated with all users to see if Morty and/or Summer were members of any groups as well to find they were not.

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782130-5d758d54-40bc-11e8-865f-a38953c6b239.jpg">
<br></p>

Now that I know Rick can likely run commands as if logged in as root. I checked his sudo permissions to see exactly what commands I have this privileged access to. 

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782131-5d835ff6-40bc-11e8-803b-6734bc776290.jpg">
<br>This descriptions shows that, on (ALL) machines, Rick has access to use ALL possible commands as if root itself.</p>

This basically means that Rick can do anything root can do, including BE root, because he is allowed access to every command, which include one called su that allows a user to switch accounts to another user. Whereas other users would be required to know the password to the user they are switching to, Rick, like root, can switch into any account without needing a password. 

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782132-5d911786-40bc-11e8-8818-383f84257816.jpg">
<br></p>

And that is exactly how I became root user. From there I just navigated to the directory for root user where I finally had the permission to read the flag waiting for me there for 130/130 pts. 

<p align="center">
<img src="https://user-images.githubusercontent.com/15791354/38782133-5d9ffca6-40bc-11e8-9a86-10829f91bbc8.jpg">
<br></p>


