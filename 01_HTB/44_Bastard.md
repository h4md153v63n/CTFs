# Bastard 

**OS:** Windows

**Level:** Medium

**Links:** [1](https://www.hackthebox.com/machines/Bastard)  [2](https://app.hackthebox.com/machines/Bastard)

**Machine ip:** 10.10.10.9

![Bastard](https://github.com/h4md153v63n/CTFs/assets/5091265/84d54684-632c-4ac5-ac8e-146a4b2d7267)


## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.9 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.9 --open
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3af46416-e08d-4b5c-bdfb-0d8a16c4e1fb)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c9205d7a-b825-43c2-b71b-15b0456a3bb3)


## Enumeration
Visit **http://10.10.10.9**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/cf5880f4-a25e-4c05-9b77-86ee83268cc1)

Check **http://10.10.10.9/CHANGELOG.txt**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5163edd8-e04c-472a-bd4e-45e64c7c4083)

Run searchsploit:

```
searchsploit drupal 7.x

searchsploit -m 41564
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d11fa67b-28a5-4a61-a50f-57a97b5b080c)

```
gobuster dir -e -w /usr/share/wordlists/dirb/common.txt -u http://10.10.10.9/ -k -n -r -t 10 --exclude-length 1233
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0ccab42b-9061-43f5-9c20-7b7c06453c46)

Test **http://10.10.10.9/rest** on the browser or using **curl**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/575bf492-5f0d-4771-b1b2-9a4d444580b6)

Modify the below lines in the exploit code:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4da43bf7-5ca8-4fdf-ab20-f738bcc18380)

Run the exploit: `php 41564.php`

In case of **PHP Fatal error:  Uncaught Error: Call to undefined function curl_init()** error, run `sudo apt install php-curl` to install **php-curl**:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/293cb05e-6870-46cc-812d-1185ac050fa2)

Rerun the exploit: `php 41564.php`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c9ce7b40-75f7-4047-bd2f-bfcc56c55d26)

It creates 2 files: **session.json** and **user.json**. View the content of **session.json**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e77c653b-928b-4d12-84a1-3cff4f22c232)

**session.json** file gives a valid session cookie for admin user.

Add the cookie with the Cookie Editor plugin on the browser:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4210ab1b-d35e-4598-b99c-08e18a850221)

Refresh the browser:






## Exploitation & Gaining Access



## Privilege Escalation



# References & Alternatives
+ https://vvmlist.github.io/#Bastard
+ 


## CVE Scripting
+ 


## Tools
+  


## Technical Knowledge
+  


## Problems Solution
+ **PHP Fatal error:  Uncaught Error: Call to undefined function curl_init():** 
  + https://stackoverflow.com/questions/6382539/call-to-undefined-function-curl-init
  + https://enginetemplates.com/call-to-undefined-function-curl_init/


## For More
+ 
