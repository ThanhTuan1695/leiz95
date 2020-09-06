---
layout: post
title: Lian_Yu writeup
subtitle: A beginner level security challenge
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/alianYu/logo.png
share-img: /assets/img/path.jpg
tags: [ctf,tryhackme, writeup, Lian_Yu, gobuster]
---

Welcome to Lian_YU, this Arrowverse themed beginner CTF box! Capture the flags and have fun.


## Here is a machine information

| Title | Lian_Yu | 
| :------ |:--- | 
| Difficulty | Easy | 
| Point | 200 |
| Maker | Deamon |
| Infor | This is a free room, which means anyone can deploy virtual machines in the room  | 


## RUST SCAN and Enumeration
Normally, i will use the rustscan as scan the host and gathering machine information


``` rustscan -b 500 machine-ip ```
<em>

-b : the batch size for port scanning, it increases or slows the speed of scanning. Depends on the open file limit of your OS.  If you do 65535 it will do every port at the same time. Although, your OS may not support this [default:4500]
</em>

It will take few time to scan: 

![gs1](/assets/img/alianYu/l1.png)

We got 4 open ports recently. Going to port 80:

![gs1](/assets/img/alianYu/l2.png)

Also no more interesting thing at port 21:

![gs1](/assets/img/alianYu/l3.png)

Let try another the way to gathering as much as possible. Let's use dirsearch to check what the file is hiden or not.


```

python3 dirsearch.py -u http://machine-ip -e *

```

i am using the dirsearch. You can find it here: [dirsearch](https://github.com/maurosoria/dirsearch.git)

After running with common directory. i did not find anything new. Then i use  *directory-list-2.3-medium.txt* of seclist as another the wordlist. To add wordlist for dirsearch, using *-w*. Finally, i found *island* directory.

![gs1](/assets/img/alianYu/l4.png)

We have the code word now. I am stuck for the while. But i still do the dirsearch. But in the image below. it looks like */island/index.html*. let's go dirsearch for this as well.

Finally we get another directory in island. In */2100*,

![gs1](/assets/img/alianYu/5.png)

View page as source, there is look like the new hint. 

![gs1](/assets/img/alianYu/6.png)

Let do the dirsearch with .ticket as extension.

```
python3 dirsearch.py -u http://machine-ip -w wordlist -e ticket
```

Finally we got this:


![gs1](/assets/img/alianYu/7.png)


The String look like encoded string. let go to [CyberChef](https://gchq.github.io/CyberChef/).

From base58, we got the password.


![gs1](/assets/img/alianYu/8.png)

Now let try to access ftp or ssh with credentials if we can. 

In FTP,

![gs1](/assets/img/alianYu/9.png)

In ftp we have some file and let try to download all the file but run

```
ls  (this is show all file)
get filename 
```

In order to get all files.

```
mget .
```

I use steghide to check what it hide in all the picture.

In aa.jpg

![gs1](/assets/img/alianYu/10.png)


After check Leave_me_alone.png. There is wrong signiture of file. I change the signitue file and got the password.

I am ussing hexeditor to change the signniture of file.

![gs1](/assets/img/alianYu/11.png)

Save and closed.

![gs1](/assets/img/alianYu/12.png)

Now we got the password to open file *aa.jpg*.

Check the password is right or not and extract it.

![gs1](/assets/img/alianYu/13.png)

Upzip file and take the another ssh password. For the username to access ssh. At FTP, we are change to root directory and list all the users of system

![gs1](/assets/img/alianYu/14.png)

Accessing to machine host. We got the user flag.

## Privilege Escalation

As normally, i try to run
```
sudo -l
```
![gs1](/assets/img/alianYu/15.png)

Here we got:

``` (root) PASSWD: /usr/bin/pkexec ```

Let check it on the [GTFOBINS](https://gtfobins.github.io/). We have exploit cmd.

```
sudo pkexec /bin/sh
```

Yeah!!! got root now.

![gs1](/assets/img/alianYu/16.png)

Finally, we got the root flag!!!.