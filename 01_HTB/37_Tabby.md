# Tabby 

**OS:** Linux

**Level:** Easy

**Links:** [1](https://www.hackthebox.com/machines/Tabby)  [2](https://app.hackthebox.com/machines/Tabby)

**Machine ip:** 10.10.10.194

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/2227c005-63ba-4836-9185-d50d6423d8a4)


## Solutions
I solved the machine, but I won't publish a write-up.

https://www.hackthebox.com/achievement/machine/184261/259

![image](https://github.com/h4md153v63n/CTFs/assets/5091265/680622a9-6627-4d8c-8ed2-c7ae4a5e1813)

I share alternative solutions, and different approaches links in the below:
+ https://vvmlist.github.io/#tabby
+ https://0xdf.gitlab.io/2020/11/07/htb-tabby.html
+ https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/tabby-writeup-w-o-metasploit
+ https://chr0x6eos.github.io/2020/11/07/htb-Tabby.html
  + https://snowscan.io/htb-writeup-tabby/#
  + https://ethicalhacs.com/tabby-hackthebox-walkthrough/
  + https://ivanitlearning.wordpress.com/2021/02/18/hackthebox-tabby/
+ https://www.hackingarticles.in/tabby-hackthebox-walkthrough/
  + https://offs3cg33k.medium.com/tabby-htb-walkthrough-97b61b5c2a98
+ https://github.com/python4004/Tabby-HTB/blob/master/tabby.md
  + https://medium.com/@v1per/tabby-hackthebox-writeup-67d56026282c
  + https://netosec.com/tabby-hackthebox-writeup/
  + https://amirr0r.github.io/posts/htb-tabby/


## CVE Scripting
+ CVE: N/A = https://www.exploit-db.com/exploits/46978


## Tools
+ **What is WAR?:** https://0xdf.gitlab.io/2020/11/07/htb-tabby.html#beyond-root---what-is-war
+ **Deploy A New Application Archive (WAR) Remotely:** https://tomcat.apache.org/tomcat-9.0-doc/manager-howto.html#Deploy_A_New_Application_Archive_(WAR)_Remotely
  + https://stackoverflow.com/questions/4432684/tomcat-manager-remote-deploy-script
+ https://github.com/tennc/webshell
	+ https://github.com/tennc/webshell/blob/master/jsp/cmdjsp.jsp
+ **LXD Alpina Linux image builder:** https://github.com/saghul/lxd-alpine-builder


## Technical Knowledge
+ **File Inclusion / LFI:** https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion/Intruders
+ **LXC Exploitation:** https://github.com/h4md153v63n/CTFs/blob/main/01_HTB/02_Brainfuck.md#privilege-escalation
  + https://0xdf.gitlab.io/2020/11/07/htb-tabby.html#lxc-exploitation
  + https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/linux-boxes/tabby-writeup-w-o-metasploit#id-5805
+ **lxd/lxc Group - Privilege escalation:** https://book.hacktricks.xyz/linux-hardening/privilege-escalation/interesting-groups-linux-pe/lxd-privilege-escalation
  + https://www.hackingarticles.in/lxd-privilege-escalation/
  + https://reboare.github.io/lxd/lxd-escape.html
  + https://materials.rangeforce.com/tutorial/2019/12/07/Privilege-Escalation-Docker-LXD-Groups/
+ **Better LXC Root:** https://blog.m0noc.com/2018/10/lxc-container-privilege-escalation-in.html
+ **Apache Tomcat:** https://tomcat.apache.org
+ **Multiple Ways to Exploit Tomcat Manager:** https://www.hackingarticles.in/multiple-ways-to-exploit-tomcat-manager/
  + https://medium.com/@cyb0rgs/exploiting-apache-tomcat-manager-script-role-974e4307cd00
+ **Tomcat exploit variant : host-manager:** https://www.certilience.fr/2019/03/tomcat-exploit-variant-host-manager/
+ **Text-based manager:** https://tomcat.apache.org/tomcat-9.0-doc/manager-howto.html#Supported_Manager_Commands


## Problems Solution
+ **Failed container creation: No storage pool found. Please create a new storage pool.:** https://techoverflow.net/2018/05/03/how-to-fix-lxd-failed-container-creation-no-storage-pool-found-please-create-a-new-storage-pool/


## For More
+ **Apache Tomcat Default Credentials:** https://github.com/netbiosX/Default-Credentials/blob/master/Apache-Tomcat-Default-Passwords.mdown
+ **pentesting-web tomcat:** https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/tomcat
+ **Example hashes:** https://hashcat.net/wiki/doku.php?id=example_hashes
+ **penglab:** https://github.com/rvrsh3ll/penglab
