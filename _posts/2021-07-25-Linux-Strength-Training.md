---
layout: post
title: Linux Strength Training (CheatSheet)
subtitle: Can you shortly how to use it?
thumbnail-img: /assets/img/linux-strength/linux-strength.png
# cover-img: 
# gh-badge: [star, fork, follow]
tags: [linux,tryhackme, writeup, cheatsheet]
# comments: true
---

This room is intended to further the understanding of basic Linux command line skills for beginners.

This room will help you to learn/reinforce linux command line skill.

## Here is a machine information

| Title | Linux Strength Training | 
| :------ |:--- | 
| Difficulty | Training | 
| Point | No point |
| Maker | DrXploiter and  wannabe12 |
| Infor | This is a free room, which means anyone can deploy virtual machines in the room.  | 

-----------------------------------------------------------------
# Introduction

This room is intended to further the understanding of basic Linux command line skills for beginners.

------------------------------------------------------------------

# Finding your way around linux - overview

This section will help you able to work with file/folder on the system based on various condition rangingg from, but not limieed to the following.

- Filename
- Size
- user/group
- Date Modified
- Date accessed
- Its keyword content

There is the cheat sheet with find command.

![find cheat sheet](/assets/img/linux-strength/find.png)

if you do not know already, typing **CTRL+L** allows you to clear the screen quicker rather than typing 'clear' all the time. Additionally, hitting the up arrow allows you to return to a previously typed command so you do not have to spend time retyping it again if you made an error. Cool. Finally, placing: **2>/dev/null** at the end of your find command can help filter your results to exclude files/directories that you do not have permission to.

## Task

1. What is the correct option for finding files based on group
    
    -group
2. What is format for finding a file with the user named Francis and with a size of 52 kilobytes in the directory /home/francis/

    ``` find /home/francis -user Francis -size 52k ```

3. SSH as topson using his password topson. Go to the /home/topson/chatlogs directory and type the following: grep -iRl 'keyword'. What is the name of the file that you found using this command?

    grep -iRl 'keyword' -> chatlogs/2019-10-11
4. What are the characters subsequent to the word you found?

    ``` 
    
    find /home/topson -type f -name "ReadMeIfStuck.txt" 2> /dev/null 
    
    find /home/topson/ -type f -name "additionalHIN" 2> /dev/null 

    find /home/topson/ -type d -name "telephone number" 2> /dev/null

    find /home/topson/workflows -type f -newermt 2016–09–11 ! -newermt 2016–09–13

    less /home/topson/workflows/xft/eBQRhHvx
    
    ```

    To search in the less we use /. Then we type /Flag and got it flag. 

#  Working with files

You should be somewhat familiar already with working with files. Similar to windows, we can do the following:

- copy files and folders
- move files and folders
- rename files and folders
- create files and folders


there is a cheat sheet to work with files: 

![find cheat sheet](/assets/img/linux-strength/workwithfile.png)

A few additional things to remember is that occasionally you may encounter files/folders with special characters such as - (dash). Just remember that if you try to copy or move these files you will encounter errors because Linux interprets the - as a type of argument, therefore you will have to place -- just before the filename. For example: cp -- -filename.txt /home/folderExample.

## Task

1. Hypothetically, you find yourself in a directory with many files and want to move all these files to the directory of /home/francis/logs. What is the correct command to do this?

    ```mv * /home/francis/logs ```

2. Hypothetically, you want to transfer a file from your /home/james/Desktop/ with the name script.py to the remote machine (192.168.10.5) directory of /home/john/scripts using the username of john. What would be the full command to do this?

    ``` scp  /home/james/Desktop/script.py  john@192.168.100.123:/home/john/script ```

3. How would you rename a folder named -logs to -newlogs

    ``` mv -- -logs -newlogs ```

4.  How would you copy the file named **encryption keys** to the directory of /home/john/logs

    ``` cp "encryption keys" /home/john/logs ```

5. Find a file named readME_hint.txt inside topson's directory and read it. Using the instructions it gives you, get the second flag.
    
    ``` 
        find . -type f -name readME_hint.txt

        mv -- "-MoveMe.txt" "-march folder"

        cd -- "-march folder"

        ./"-runME.sh" 
    ```

# Hashing - introduction

## What is hashing?

Next we will talk about hashing, which is important for any Linux security researcher. Hashing refers to taking any data input, such as a password and calculating its hash equivalent. The hash equivalent is a long string which cannot be reversed since the act of hashing is known as a one-way function. For example, if you visit: https://www.md5hashgenerator.com/ and type the following: mypassword123 or any other password, you will see how the hash algorithm known as MD5 will calculate and output a MD5 hash equivalent. Therefore, 'mypassword123' would have the MD5 hash equivalent of '9c87baa223f464954940f859bcf2e233'.


## Why is hashing important?


There are several reasons why hashing is important and I would encourage you to conduct some personal research after this, but the single most important use case you should concern yourself with in this room is integrity checking. For example, when you log into a website, your password must be checked with the local database to verify whether you should be allowed access. But think critically, if companies stored the username and password in plain text on the database, it would make it very easy for a successful hacker to be able to compromise every account. Therefore, it makes more sense to store the hash equivalent instead of storing the plain text credentials. So, when you send over your username and password to the database, the system will input these separately into a hash algorithm and if the output turns out to be equal to the hash equivalent stored in the database then it will allow you access. 

## Task

1.  Download the hash file attached to this task and attempt to crack the MD5 hash. What is the password?

    5d7845ac6ee7cfffafc5fe5f35cf666d using https://crackstation.net -> secret123

2.  What is the hash type stored in the file hashA.txt

    ``` 
        find -type f -name hashA.txt 2>/dev/null

        f9d4049dd6a4dc35d40e5265954b2a46 -> admin

    ````
 3.  What is the hash type stored in the file hashB.txt

    ``` 
        find -type f -name hashB.txt 2>/dev/null

        b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3 -> letmein

    ````

 4.   Find a wordlist  with the file extention of '.mnf' and use it to crack the hash with the filename hashC.txt. What is the password?

    ``` 
        find -type f -name *.mnf 2>/dev/null

        find -type f -name hashC.txt 2>/dev/null

        john --format=raw-sha256 --wordlist=ww.mnt hashC.txt -> unacvaolipatnuggi 

    ````

# Decoding base64

This is very simple 

## Task

1. what is the name of the tool which allows us to decode base64 strings?

    base64

2.  find a file called encoded.txt. What is the special answer?

    john

# Encryption/Decryption using gpg


## What is encryption/decryption

Encryption refers to the process of concealing sensitive data by converting it to an unintelligible format. The only way to reverse the process is to use a key; this is known as decryption. For further explanation please visit:https://www.cisco.com/c/en/us/products/security/encryption-explained.html but in short, encryption is just a way to protect data using a private key. For example, the following string 'secret data' can be converted to 'QFnvZbCSffGzrauFXx9icxsN9UHHuU+sCL0sGcUCPGKyRquc9ldAfFIpVI+m8mc/' using a key derived from the password 'pass'. It is also important to note that there are many different types of encryption schemes, known as algorithms such as AES-256/128, 3DES, Blowfish, ect. Among these, AES is considered to be the reccomended encryption algorithm to use due to the fact that it has been tested and proven to be a strong scheme. Furthermore, there are two main types of encryption methods, aysmmetric and symmetric. However, in this room we will be focusing on symmetric encryption. If you are interested in knowing the difference or more on encryption please visit: https://www.thesslstore.com/blog/types-of-encryption-encryption-algorithms-how-to-choose-the-right-one/


## Task

1. You wish to encrypt a file called history_logs.txt using the AES-128 scheme. What is the full command to do this?

    
    ``` gpg --cipher-algo AES-128 --symetric history_logs.txt ```

2.  What is the command to decrypt the file you just encrypted?

    
    ``` gpg history_logs.txt.gpg ```

3. Find an encrypted file called layer4.txt, its password is bob. Use this to locate the flag. What is the flag?

    ``` 
        find  -type f -name layer4.txt 2>/dev/null

        gpg --decrypt layer4.txt > out.txt

        gpg --decrypt /home/sarah/oldLogs/2014-02-15/layer3.txt > out.txt

        gpg --decrypt /home/sarah/oldLogs/settings/layer2.txt > out.txt

        gpg --decrypt /home/sarah/logs/zmn/layer1.txt > out.txt

    ```

#  Cracking encrypted gpg files

## How to crack encrypted files using john the ripper
you can check it out. 

## Task

1.  Find an encrypted file called personal.txt.gpg and find a wordlist called data.txt. Use tac to reverse the wordlist before brute-forcing it against the encrypted file. What is the password to the encrypted file?

    ```
        find /home/sarah -type f -name personal.txt.gpg 2>/dev/null

        find /home/sarah -type f -name data.txt 2>/dev/null
        
        copy wordlist to attack Machine

        john /home/sarah/oldLogs/units/personal.txt.gpg -w wordrev.txt --format gpg

        gpg /home/sarah/oldLogs/units/personal.txt.gpg

         cat /home/sarah/oldLogs/units/personal.txt

    ```

#  Reading SQL databases

You can following the cmd to solve the thing.

## Task

```
    find /home/sarah -type f -name employees.sql 2>/dev/null

    cd /home/sarah/serverLx/

    mysql -p

    source /home/sarah/serverLx/employees.sql


```

#  Final challenge 

You can following the cmd to solve the thing.

## Task

```
   cd /home/shared/chatlogs

   cat  LpnQ

   grep -iRl Sameer /home 2>/dev/null

   cat /home/shared/chatlogs/Pqmr

   cat /home/shared/chatlogs/KfnP ->> you can got the sameer password 

   find /home/shared/sql/ -type f -size 50M

   head /home/shared/sql/conf/JKpN

   echo  aG9tZS9zYW1lZXIvSGlzdG9yeSBMQi9sYWJtaW5kL2xhdGVzdEJ1aWxkL2NvbmZpZ0JEQgo= | base64 -d

   get wordlist

   grep -iRlE '^ebq' '/home/sameer/History LB/labmind/latestBuild/configBDB'

   grep -iRhE '^ebq' '/home/sameer/History LB/labmind/latestBuild/configBDB'

   cp to attack Vm

   scp sameer@10.10.61.194:/home/shared/sql/2020-08-13.zip.gpg .

   gpg2john 2020-08-13.zip.gpg 2020-08-13.zip.gpg.hash

   gpg2john doesn't work because the file is too big:

   https://github.com/felipesi/gpg-crack/blob/master/crackgpg.sh

   ./crackgpg.sh 2020-08-13.zip.gpg wordlist.txt

   unzip 2020-08-13.zip


   find only for james with grep

   grep -ri james 2020-08-13 -- you got the password.

   then switch to james.

   Then you easyly to got the root.

```


This room quite long. but at the end. you can understand about the linux.
