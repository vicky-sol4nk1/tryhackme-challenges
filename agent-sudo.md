 # agent sudo
 In this room we learn about user-agent,ftp bruteforc and magic of stenography,privledge escalaton through outdated software 

# task 1: enumuration
```bashnmap -sS --top-ports 5000  10.80.185.245  -sV -oN scan.report
# Nmap 7.95 scan initiated Thu Feb  5 22:12:30 2026 as: /usr/lib/nmap/nmap --top-ports 5000 -sV -T4 -oN scan.report 10.81.156.219
Nmap scan report for 10.81.156.219
Host is up (0.16s latency).
Not shown: 4997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Feb  5 22:12:55 2026 -- 1 IP address (1 host up) scanned in 25.48 seconds

```
