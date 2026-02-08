 # tryhackme room: agent sudo
 In this room we learn about user-agent,ftp bruteforc and magic of stenography,privledge escalaton through outdated software 


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

cutie.png file contain hidden zip files lets extract it and see what we can get interesting

<img width="1918" height="369" alt="image" src="https://github.com/user-attachments/assets/e4011ae5-7f0b-48a9-9d33-77da181950a3" />

zip file contain a another secret message but it's encrypted and asked for a password and this is another more time we need to bruteforc,first we use zip2john is a tool used to convert password protected file into hash formate so john can bruteforce it i alread crack it 

<img width="1061" height="619" alt="image" src="https://github.com/user-attachments/assets/8b744090-2df7-462b-af57-2463fda38c0e" />

now we have a password lets try to open 8702.zip and open the file 
<img width="646" height="505" alt="image" src="https://github.com/user-attachments/assets/43fc9ee3-054c-48ed-a16b-7685ac6b7cfd" />

this file is contain a base64 encoded text i decode it and get another password i don't know the this password where to belong, we get a another image from ftp,cute-alien.jpg

<img width="886" height="114" alt="image" src="https://github.com/user-attachments/assets/90147787-2c1f-49a0-98c2-c2d39979d415" />

i use steghide --extract -sf cute-alien.jpg it's asked for password and i try this passowrd and boom! we get new user name and passowrd,lets use for ssh login

<img width="963" height="570" alt="image" src="https://github.com/user-attachments/assets/d0d617e3-00a8-4758-b034-63ee1e24624f" />

in my frist though when author asked for cve number i think he asking for a kernal exploit cve number but i wrong it's actuly asked for sudo version 1.8.21p2, james user has a sudo permision to run /bin/bash with any usser permision expect root users, sudo has a vulnerability ,sudo versions before 1.8.28. At a high level, sudo becomes vulnerable because it doesn't properly check if a specified user ID actually exists or is valid when running commands with elevated privileges. Specifically, if a user has permission to run commands as "any user except root" (a common setup), they can trick sudo by using a special invalid user ID like -1. Sudo interprets -1 as equivalent to 0, which is root's ID, allowing the command to run with full root access despite the restrictions.

you can explor and read your self on this linke

https://www.exploit-db.com/exploits/47502

just type on termial:
```bash
sudo -u#-1 /bin/bash
```
<img width="1372" height="601" alt="image" src="https://github.com/user-attachments/assets/af59c464-b4ab-4267-913c-ad57fdcf9f3c" />

i hope you understood very well,keep learning and be creative!








