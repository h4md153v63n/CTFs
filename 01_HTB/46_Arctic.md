# Arctic

**OS:** Windows

**Level:** Easy

**Links:** [1](https://www.hackthebox.com/machines/Arctic)  [2](https://app.hackthebox.com/machines/Arctic)

**Machine ip:** 10.10.10.11

![Arctic](https://github.com/h4md153v63n/CTFs/assets/5091265/d397feae-2a4e-407d-b89c-8be5e16dbd0c)


# Sections
+ [Reconnaissance](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/46_Arctic.md#reconnaissance)
+ x
+ [Links](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/46_Arctic.md#links)


## Reconnaissance
```
sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.11 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

sudo nmap -Pn -n -sV -sC -O -p$ports 10.10.10.11 --open
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2a63e599-4daa-4749-9bce-3fdd108a0fb8)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1fb88333-4181-436f-afa1-c5b07366bca2)


## Enumeration
Visit **http://10.10.10.11:8500/**

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2d1d0c15-3bc9-4a1e-b45f-2bbfc5ab816f)

Check consecutively **http://10.10.10.11:8500/CFIDE/** -> **http://10.10.10.11:8500/CFIDE/administrator/**

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3ffa0ee5-63da-4a34-802b-d94c1505c0b2)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/dc394261-f2e2-4ff1-8097-2bff1c424d5e)


## Exploitation & Gaining Access

### Method 1: 




## Privilege Escalation

### Method 1: 


---

# Links

# References & Alternatives
+ https://vvmlist.github.io/#Arctic
+ https://0xdf.gitlab.io/2020/05/19/htb-arctic.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/arctic-writeup-w-o-metasploit
+ 


## CVE Scripting
+ x


## Tools
+ x 


## Technical Knowledge
+ x 


## Problems Solution
+ x 


## For More
+ x 

