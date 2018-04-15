---
author: elan rasabi
comments: true
layout: post
date: 04/15/2018
trekthrough: 1
title: RickdiculouslyEasy
---
				Penetration Testing w/Rick and Morty Walkthrough

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

</t>![image001](https://user-images.githubusercontent.com/15791354/38782081-517d3d9e-40bc-11e8-9961-91d0c9d7f757.jpg)

1 Recon: 	Collect information on anything that will help understand the target. 

**An example of internet recon that would come in handy for this VM would be to watch the show Rick and Morty and read up on the character Wikis. In this portion I make my recon work to find the IP address of my target rather than just giving it to you. Just remember your target IP is assigned randomly and will likely be different from mine in this doc.

Well, I know I am in the same subnetwork as my target. Without getting into the nitty gritty of network addressing and subnetting, a good way to think of this is by imagining my Kali box (computer) and the target box as if they were both under a home router on the same local network. This means my device can communicate with the target device directly without having to go through other devices outside the local network or across the internet. I just need a way to identify the target, like its IP address. 

I’ll open up my Kali using Terminator which is just like the regular terminal in Linux, only with added features.

select:  ```Activities (upper left)>Terminator (red icon)``` 
type: ```ifconfig```

<img style="float: right;" src="https://www.superdrug.com/medias/sys_master/front-prd/front-prd/h6a/h00/9237434761246/Garnier-Olia-660-Intense-Red-Permanent-Hair-Dye-537874.jpg")>

<blockquote class="imgur-embed-pub" lang="en" data-id="kalrMu5"><a href="//imgur.com/kalrMu5">&quot;I&#39;ll take the stairs!&quot;</a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>
![image003](https://user-images.githubusercontent.com/15791354/38782085-5ae660e0-40bc-11e8-87c6-82a561636a4f.png)

![image004](https://user-images.githubusercontent.com/15791354/38782086-5af7ba5c-40bc-11e8-855c-d82a00bd4c16.jpg)

![image005](https://user-images.githubusercontent.com/15791354/38782087-5b072d66-40bc-11e8-9e94-7e179235637c.jpg)

![image006](https://user-images.githubusercontent.com/15791354/38782088-5b1eb86e-40bc-11e8-9fa0-550b36726786.jpg)

![image007](https://user-images.githubusercontent.com/15791354/38782089-5b2c772e-40bc-11e8-96e6-d0886c032ae3.jpg)

![image008](https://user-images.githubusercontent.com/15791354/38782090-5b3a7022-40bc-11e8-84cb-63d6413d11bf.jpg)

![image009](https://user-images.githubusercontent.com/15791354/38782091-5b498044-40bc-11e8-8648-ae62c33ecf22.jpg)

![image010](https://user-images.githubusercontent.com/15791354/38782092-5b5808ee-40bc-11e8-88e5-9d3358bee5fa.jpg)
