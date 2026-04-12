ctf:bounty hacker

<img width="1899" height="882" alt="image" src="https://github.com/user-attachments/assets/b256f217-cdda-4cd6-b5b2-462412c5fede" />

i see port 80 is also open and i do directory bruteforce for finding hidden directory







```
nmap -sS -p- 10.49.144.83 -T4 -oN open-ports.txt
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-12 09:34 +0530
Nmap scan report for 10.49.144.83
Host is up (0.14s latency).
Not shown: 1951 filtered tcp ports (no-response), 46 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 30.38 seconds


```

i try anonymous login on ftp server and i successfully logini and get two intresting files and donwload on my local machine and see what it's hase

```
[9:35] root@prime ~/ctf/bounty-hacker # ftp 10.49.144.83                                                     
Connected to 10.49.144.83.
220 (vsFTPd 3.0.5)
Name (10.49.144.83:root): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
550 Permission denied.
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-rw-r--    1 ftp      ftp           418 Jun 07  2020 locks.txt
-rw-rw-r--    1 ftp      ftp            68 Jun 07  2020 task.txt
226 Directory send OK.
ftp> get locks.txt
local: locks.txt remote: locks.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for locks.txt (418 bytes).
100% |******************************************************************************************|   418        2.03 MiB/s    00:00 ETA
226 Transfer complete.
418 bytes received in 00:00 (3.47 KiB/s)
ftp> get task.txt
local: task.txt remote: task.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for task.txt (68 bytes).
100% |******************************************************************************************|    68        0.18 KiB/s    00:00 ETA
226 Transfer complete.
68 bytes received in 00:00 (0.15 KiB/s)
ftp> 
```

```
[9:40] root@prime ~/ctf/bounty-hacker # ls
locks.txt  open-ports.txt  task.txt
                                                                                                                                                                                             
[9:40] root@prime ~/ctf/bounty-hacker # cat task.txt              
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin
                                                                                                                                                                                             
[9:40] root@prime ~/ctf/bounty-hacker # cat locks.txt      
rEddrAGON
ReDdr4g0nSynd!cat3
Dr@gOn$yn9icat3
R3DDr46ONSYndIC@Te
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
....                                                                                                                                                                             
[9:41] root@prime ~/ctf/bounty-hacker # 
```

