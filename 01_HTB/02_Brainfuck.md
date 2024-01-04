# Brainfuck
**Machine ip:** 

## Enumeration
+ `sudo masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.3 -e tun0 > ports`

+ `ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')`
+ `sudo nmap -Pn -sV -sC -p$ports 10.10.10.3`


+ Check scan results, and see subdomains.
+ Control TLS certificate.
+ Subdomains and `orestis@brainfuck.htb` mail address.


+ Add subdomains into `/etc/hosts` file.




+ Navigate to `www.brainfuck.htb`
+ There is a Wordpress site.
+ SMTP integration and `orestis@brainfuck.htb` mail address.
+ Go to `/wp-admin` , and see Wordpress login page.
+ Instead of brute force trial and errors, use wpscan: `wpscan --url https://example.com -e at -e ap -e u --random-user-agent`
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
