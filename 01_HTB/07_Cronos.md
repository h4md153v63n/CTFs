# Cronos

**Machine ip:** 10.10.10.13

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.13 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.13`
+ `sudo nmap -Pn -sV -sC -sU -p$ports 10.10.10.13`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/91f25735-459a-4a75-a798-2c419bd4a7c1)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e7764b71-bd50-4af5-ab88-80b1f69cbd8b)

+ dns enumeration: `nslookup`
```
└─$ nslookup
> server 10.10.10.13
Default server: 10.10.10.13
Address: 10.10.10.13#53
> 10.10.10.13
13.10.10.10.in-addr.arpa	name = ns1.cronos.htb.

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/8878f5ed-c712-45c6-aa14-0b093ff5139c)

+ dns enumeration: `nslookup 10.10.10.13 10.10.10.13`
```
└─$ nslookup 10.10.10.13 10.10.10.13
13.10.10.10.in-addr.arpa	name = ns1.cronos.htb.

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/08c7ad57-ee86-4e75-8e75-e12dd33f9c64)

+ Zone transfer: `host -l cronos.htb 10.10.10.13`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/85af127e-788f-4388-960a-f43543698b49)


+ Zoner transfer: `dig axfr @10.10.10.13 cronos.htb`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ac1ace97-a6ec-4bb8-958d-5d740aaf4e59)

+ `dig any @10.10.10.13 cronos.htb`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/cc415b9e-da19-489d-a4b8-8ca809bf085b)

+ Add domains into `/etc/hosts` file
```
echo "10.10.10.13 cronos.htb admin.cronos.htb ns1.cronos.htb www.cronos.htb" >> /etc/hosts
```

+ Enumerate subdomain:
```
gobuster dns -d cronos.htb -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt


gobuster vhost -u http://cronos.htb/ -w /usr/share/wordlists/seclists/Discovery/DNS/namelist.txt
```



# References & Alternatives:
+ https://vvmlist.github.io/#cronos
+ https://0xdf.gitlab.io/2020/04/14/htb-cronos.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/cronos-writeup-w-o-metasploit
+ https://systemweakness.com/htb-cronos-walkthrough-3f669386b681
