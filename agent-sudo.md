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
i try anonymous login on ftp server and i failled,that's mean it's not configure to login with anonymous ,we need to find valid user name and passwords 

<img width="1059" height="233" alt="image" src="https://github.com/user-attachments/assets/0111b51c-9051-4c43-86f7-0eaa537dcabb" />


here we see a three ports are open so lets see what running on port 80,

<img width="1911" height="577" alt="image" src="https://github.com/user-attachments/assets/8db544cd-4096-4403-a8f3-3dc60235e1f7" />

now the next challenge is a change a user-agent(user-agent is a header tell web server what kind of software is used for request to a web-server it's gives details like browser name,version number and also tells what kind of operation system is used to web-server so it's ) 

they are many ways to change user-agent we can use user-agent swithcter extenstion on our firefox browser,burpsuite,scriptiong,and also python,i try my custom user agent like bunny but it's not work that's mean we need to bruteforce the useragent,room also gives us hint like user agent start with c,

```bash
â”Œâ”€â”€(rootã‰¿prime)-[~/Desktop/challenges/agent-sudo]
â””â”€# curl -A "C"   http://10.80.185.245 -L
Attention chris, <br><br>

Do you still remember our deal? Please tell agent J about the stuff ASAP. Also, change your god damn password, is weak! <br><br>

From,<br>
Agent R 


```
now we have a username called chris and it' say a password is weak may be it's indicating on ftp server lets try bruteforce method using assuming chris is username

<img width="1914" height="351" alt="image" src="https://github.com/user-attachments/assets/548d11dd-ed77-455e-9ad2-af8f3b28fc8e" />

now we have username and password,after login successfully i donwload all files 

<img width="1912" height="768" alt="image" src="https://github.com/user-attachments/assets/a6385526-f0ce-47e8-b789-05c576a274cc" />

<img width="1893" height="290" alt="image" src="https://github.com/user-attachments/assets/30e5b4ec-aaab-413f-8ccc-2302f41352be" />

i think here data is hide into a cute-alien.jpg and cute.png, binwalk is best tool to check 

<img width="1689" height="500" alt="image" src="https://github.com/user-attachments/assets/61a87778-cc69-4097-a27d-0a4c48f0ee81" />

cutie.png has zip


