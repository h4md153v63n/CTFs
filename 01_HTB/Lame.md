# Lame
Machine IP : 10.10.10.3

## Enumeration

### nmap
`masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.3 -e tun0 > ports`
```
Starting masscan 1.3.2 (http://bit.ly/14GZzcT) at 2024-01-02 13:58:37 GMT
Initiating SYN Stealth Scan
Scanning 1 hosts [131070 ports/host]
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a34eabd7-9f22-4c5d-a498-106bac766611)

`ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
```
 
```

`sudo nmap -Pn -sV -sC -p$ports 10.10.10.3`
```
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


