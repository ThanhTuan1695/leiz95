---
layout: post
title: BrookLyn Nine Nine writeup
subtitle: This room is aimed for beginner level hackers but anyone can try to hack this box. There are two main intended ways to root the box.
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/alianYu/logo.png
share-img: /assets/img/path.jpg
tags: [ctf,tryhackme, writeup, brooklyn-nine-nine, gobuster]
---

This room is aimed for beginner level hackers but anyone can try to hack this box. There are two main intended ways to root the box. If you find more dm me in discord at Fsociety2006.


## Here is a machine information

| Title | Blook Nine Nine | 
| :------ |:--- | 
| Difficulty | Easy | 
| Point | 120 |
| Maker | Fsociety2006 |
| Infor | This is a free room, which means anyone can deploy virtual machines in the room  | 


## RUST SCAN, Enumeration and Gathering Information
Normally, i will use the rustscan as scan the host and gathering machine information


``` rustscan -b 500 machine-ip -- -sv -sC -A```
<em>

-b : the batch size for port scanning, it increases or slows the speed of scanning. Depends on the open file limit of your OS.  If you do 65535 it will do every port at the same time. Although, your OS may not support this [default:4500]

-sV -sC -A: it is Nmap custom flag when rustscan run nmap.
</em>

It will take few time to scan: 

![gs1](/assets/img/brook/1.png)

We got 3 open ports recently includes 21, 22, 80. Namp expose *note_to_jake.txt* in FTP service.

![gs1](/assets/img/brook/2.png)

We knew that Jake's password to weak. Let's try to Crack it by Hydra.

```
hydra -l jake -P /rockyou.txt machine-ip ssh

```

I am using rockyou wordlists.

![gs1](/assets/img/brook/3.png)

Accessing into machine. Let's find user flag.

```
find / -name "user.txt" 2>/dev/null

```

![gs1](/assets/img/brook/4.png)

## Privilege Escalation

At Jake user. From sudoer it expose *(ALL) NOPASSWD: /usr/bin/less*

```
sudo -l
```

![gs1](/assets/img/brook/5.png)

Check less in the [GTFOBINS](https://gtfobins.github.io/). It has available exploitation. Just follow the SUDO part:

```
sudo less /etc/profile
!/bin/sh

```

Yeah!!, Now got the root user and we can find root flag with same find CMD above.

![gs1](/assets/img/brook/6.png)

## Anotther way gain to root flag
As you saw in the description of this machine. The creator told us that there are two main intented ways to root the box. I try to find another way to get into machine.

In the port 80, Access it on Firefox and View page source.

![gs1](/assets/img/brook/7.png)

There may some information was hiden in the image. Just save image to our host. Using the steghide tool to check information of image by run:

```
steghide info image-name
```
![gs1](/assets/img/brook/8.png)

It require passphrase to extract the file. Let's crack it. I am using stegcracker with rockyou.txt wordlist.

```
stegcracker image-name rockyou.txt
```

![gs1](/assets/img/brook/9.png)

After cracking have been done, we got new file with .out extention. Open this file, we got another user credentials.

![gs1](/assets/img/brook/10.png)

Now, we can access to machine with this credentials. From holt's home directory, we get user flag. Let go for high privilege.

Check sudo list. 

![gs1](/assets/img/brook/11.png)

Check nano in the [GTFOBINS](https://gtfobins.github.io/) against. It has available exploitation. Just follow the SUDO part:

```
sudo nano
^R^X
reset; sh 1>&0 2>&0
```

Finally, we got 2 ways to get inside the vm and all the flag.

Happy Hacking!!!