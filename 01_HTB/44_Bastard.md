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
+ 


## For More
+ 
