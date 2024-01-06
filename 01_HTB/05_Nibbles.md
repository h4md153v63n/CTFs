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

+ Navigateto `http://10.10.10.75/`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4a6e4c26-b14f-4560-b51a-c856039e7d9e)

+ Directory fuzzing `http://10.10.10.75/` and there is no found.
```
feroxbuster -u http://10.10.10.75 -x html,php,json,js,sh,cgi,pl,docx,pdf,txt -w /usr/share/wordlists/dirb/common.txt --depth 2 -s 200 301 302 -r --extract-links
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b32bc692-6942-47ab-b62f-1a2bb8798b4f)

+ View page source and see `/nibbleblog`. 
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/013e1861-d697-4345-9459-3b118743b547)

+ Navigate`to `http://10.10.10.75/nibbleblog`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/22e15b9a-8762-4518-b97c-4aa3162c1736)

+ Directory fuzzing `http://10.10.10.75/nibbleblog`
```
feroxbuster -u http://10.10.10.75/nibbleblog -x html,php,json,js,sh,cgi,pl,docx,pdf,txt -w /usr/share/wordlists/dirb/common.txt --depth 2 -s 200 301 302 -N 287,288,305,326 -r --extract-links
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/fb2eb882-0f65-4ab2-9d9a-24373c6d2303)








# References & Alternatives:
+ 

