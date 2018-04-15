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

![image001](https://user-images.githubusercontent.com/15791354/38782081-517d3d9e-40bc-11e8-9961-91d0c9d7f757.jpg)

![image002](https://user-images.githubusercontent.com/15791354/38782084-5ad7ca08-40bc-11e8-8e3c-d7c43223fe4e.jpg)

![image003](https://user-images.githubusercontent.com/15791354/38782085-5ae660e0-40bc-11e8-87c6-82a561636a4f.png)

![image004](https://user-images.githubusercontent.com/15791354/38782086-5af7ba5c-40bc-11e8-855c-d82a00bd4c16.jpg)

![image005](https://user-images.githubusercontent.com/15791354/38782087-5b072d66-40bc-11e8-9e94-7e179235637c.jpg)

![image006](https://user-images.githubusercontent.com/15791354/38782088-5b1eb86e-40bc-11e8-9fa0-550b36726786.jpg)

![image007](https://user-images.githubusercontent.com/15791354/38782089-5b2c772e-40bc-11e8-96e6-d0886c032ae3.jpg)

![image008](https://user-images.githubusercontent.com/15791354/38782090-5b3a7022-40bc-11e8-84cb-63d6413d11bf.jpg)

![image009](https://user-images.githubusercontent.com/15791354/38782091-5b498044-40bc-11e8-8648-ae62c33ecf22.jpg)

![image010](https://user-images.githubusercontent.com/15791354/38782092-5b5808ee-40bc-11e8-88e5-9d3358bee5fa.jpg)
