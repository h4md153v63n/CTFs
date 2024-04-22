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




## Enumeration



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

