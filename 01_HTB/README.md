# <img src="https://avatars.githubusercontent.com/u/67481186?v=4" width="25"> Hack-The-Box-Writeups


# Easy
|S.No| Machine          | Link | Tags                                                 | CVE                                                  |
|----|------------------|------|------------------------------------------------------|------------------------------------------------------|
|1   |Lame              |[Lame](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/01_Lame.md)|unix,smb,samba,smbmap,smbclient,distccd,process,udev|CVE-2007-2447,CVE-2004-2687,CVE-2009-1185|
|2   |Shocker           |[Shocker](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/03_Shocker.md)|unix,cgi-*,cgi-bin,user.sh,shellshock,gtfobins,sudo,RCE,reverse shell|CVE-2014-6271|
|3   |Bashed            |[Bashed](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/04_Bashed.md)|unix,sudo,cronjob/scheduled task,RCE,reverse shell|-|
|4   |Nibbles           |[Nibbles](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/05_Nibbles.md)|unix,sudo,nibbleblog,kernel,RCE,default creds,source code inspection|CVE-2015-6967,CVE-2017-16995|
|5   |Beep              |[Beep](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/06_Beep.md)|unix,sudo,Elastix,PBX,LFI,svwar,webmin,shellshock,cgi-*,smtp,pop3,email,vTiger CRM,reverse shell,ssh|CVE:N/A [1](https://www.exploit-db.com/exploits/37637),CVE-2012-4869,CVE-2014-6271,CVE-2012-4867,CVE-2016-1713,CVE-2015-6000,CVE-2013-3214,CVE-2013-3215|
|6   |Sense             |[Sense](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/09_Sense.md)|unix,default creds,rce,stored creds,pfsense|CVE-2014-4688,CVE-2016-10709|
|7   |Valentine         |[Valentine](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/12_Valentine.md)|unix,heartbleed ssl/tsl,sslyze,bruteforce/decrypt/decode,hash/encrypted/encoded,ssh,openssl,kernel exploit,dirtycow,system binary exploit,tmux session|CVE-2014-0160,CVE-2014-0346,CVE-2016-5195|
|8   |Sunday            |[Sunday](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/14_Sunday.md)|unix,bruteforce/decrypt/decode,finger,hydra,hash/encrypted/encoded,ssh,stored creds,sudo,suid,system/config/backup file,shadow.backup,hashid,john,hashcat,wget,gtfobins,openssl,passwd,wget --post-file,Overwrite Different SUID Binary,Overwrite shadow,Overwrite sudoers,/etc/sudoers,pspy,solaris,powershell|-|
|9   |Irked             |[Irked](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/16_Irked.md)|unix,hidden file,rce,reverse shell,stego,steghide,stored creds,suid,IRC,UnrealIRCd,Irssi,hexchat,Exim,ltrace|CVE-2010-2075,CVE-2016-1531,CVE-2018-6789|
|10  |FriendZone        |[FriendZone](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/17_FriendZone.md)|unix,bruteforce/decrypt/decode,burp,directory traversal,dns,hash/encrypted/encoded,library hijack,local file inclusion,rce,reverse shell,smb,source code inspection,stored creds,system/config/backup file|-|


# Medium
|S.No| Machine          | Link | Tags                                                 | CVE                                                  |
|----|------------------|------|------------------------------------------------------|------------------------------------------------------|
|1   |Cronos            |[Cronos](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/07_Cronos.md)|unix,burp,cronjob/scheduled task,DNS,DNS zone transfer,subdomain,sqli,RCE,reverse shell,KERNEL,Laravel|CVE-2017-16995,CVE-2018-15133|
|2   |Nineveh           |[Nineveh](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/08_Nineveh.md)|unix,info.php,bruteforce/decrypt/decode,hydra,php login bypass,php comparisons error exploit,type juggling,php login bypass type juggling,LFI,rce,phpliteadmin,reverse shell,pspy,cronjob/scheduled task,chkrootkit,strings,ssh,stego,binwalk,system binary exploit,system/config/backup file,mail,port knock,knockd,chisel,ssh authorized keys,public SSH keystring,private SSH keystring,kernel|CVE:N/A [1](https://www.exploit-db.com/exploits/24044),CVE-2014-0476,CVE-2017-16995|
|3   |SolidState        |[SolidState](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/10_SolidState.md)|unix,Apache James Mail Server,smtp,pop3,email,mutt,ssh,sshpass,authenticated rce,cron/scheduled tasks,pspy,rbash(restricted bash shell)/restricted shell,smtp/pop/imap,ssh,default creds,telnet,/etc/bash_completion.d,RSIP|CVE:N/A [1](https://www.exploit-db.com/exploits/35513) [2](https://www.exploit-db.com/exploits/50347)|
|4   |Node              |[Node](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/11_Node.md)|unix,Express Node.js,API,hadoop,big data,crackstation.net,MongoDB,NoSQL,base64,unzip,fcrackzip,zip2john,john,ssh,cron/scheduled tasks,kernel exploit,SUID,binary analysis,ltrace,unzip,7z,libc buffer overflow|CVE-2017-16995|
|5   |Poison            |[Poison](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/13_Poison.md)|unix,bruteforce/decrypt/decode,hash/encrypted/encoded,LFI,local file inclusion,log poisoning,rce,reverse shell,ssh,ssh tunnelling,proxychains,port forwarding,stored creds,system binary exploit,system/config/backup file,VNC,vncviewer,scp,phpinfo.php,phpinfolfi.py|-|
|6   |TartarSauce       |[TartarSauce](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/15_TartarSauce.md)|unix,robots.txt,monstra cms,wordpress,wpscan,gwolle-gb,cron/scheduled tasks,pspy,systemctl list-timers,RFI,remote file inclusion,reverse shell,source code inspection,sudo,system binary exploit,gtfobins,tar,--checkpoint-action,--to-command,SUID,symbolic link|CVE:N/A [1](https://www.exploit-db.com/exploits/43348),CVE-2015-8351|


# Hard
|S.No| Machine          | Link | Tags                                                 | CVE                                                  |
|----|------------------|------|------------------------------------------------------|------------------------------------------------------|
|1   |                  |      |                                                      |                                                      |


# Insane
|S.No| Machine          | Link | Tags                                                 | CVE                                                  |
|----|------------------|------|------------------------------------------------------|------------------------------------------------------|
|1   |Brainfuck         |[Brainfuck](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/02_Brainfuck.md)|smtp,pop3,email,mutt,ssh,id_rsa,ssh2john,john,tls,subdomain,wordpress,vigenere,RSA,lxd,lxc,ssh|CVE:N/A [1](https://www.exploit-db.com/exploits/41006)  [2](https://www.exploit-db.com/exploits/46978)|

