# Brainfuck
**Machine ip:** 

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.17 -e tun0 > ports`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2131bd03-d3bc-47ad-b0e0-5386ecbaaed1)

+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.17`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/79e9437c-d796-444d-bd1b-4baead8ea885)

+ Check scan results, and see subdomains.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/ce226c16-5d40-40ca-956a-1d2105d79691)

+ Navigate to `https://10.10.10.17`
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/e76efcd5-a6e6-4c52-85d6-2bf7e53ab1c6)

+ Control TLS certificate.
+ Subdomains and `orestis@brainfuck.htb` mail address.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/c11338d1-dd89-4813-a8b0-c65bd48adffa)

+ Add subdomains into `/etc/hosts` file.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/b7b15b39-ace4-4dd3-acf5-f0e3de926e54)

+ Navigate to `https://brainfuck.htb` or `https://www.brainfuck.htb` as they're the same.
+ There is a Wordpress site.
+ admin account username, SMTP integration and `orestis@brainfuck.htb` mail address.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/13aa2d1f-c897-4bfa-8a00-e6b4857f4843)

+ Go to `/wp-admin` , and see Wordpress login page. You can try default username:passwords.
![image](https://github.com/h4md153v63n/CTFs/assets/5091265/a4bb3416-457e-4aad-af23-4e967edf01ac)

+ Instead of brute force trial and errors, use wpscan: `wpscan --url https://brainfuck.htb/ --disable-tls-checks`
```

```

+ Navigate to `sup3rs3cr3t.brainfuck.htb`
+ It's a forum.


## Findings
+ nmap results.
+ TLS certificate.
+ 1 domain and 1 subdomain.
+ Wordpress site.
+ wpscan results.
+ Forum.


## Exploits
+ Review wpscan results, and there is a


+ 


## Tools
+ masscan
+ nmap
+ wpscan


## References:
+ https://medium.com/@v1per/brainfuck-hackthebox-writeup-9a326bcb426a
+ https://4st1nus.gitbook.io/hackthebox/htb/hack-the-box-brainfuck-walkthrough-without-metasploit
+ https://0xdf.gitlab.io/2022/05/16/htb-brainfuck.html
+ https://medium.com/@toneemarqus/brainfuck-htb-manual-walkthrough-2023-oscp-prep-47858603b2d6
+ https://benheater.com/hackthebox-brainfuck/
