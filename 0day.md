I started by running an Nmap scan to see what services were exposed on the target machine. This helps understand the attack surface before diving deeper.

First, I ran a standard scan with default scripts and version detection:

```bash
mrbunny $ sudo nmap -sS -p- 10.80.160.139 -oN port-scan.txt -T5
[sudo] password for greatkey: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-01-04 21:49 EST
Stats: 0:00:01 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 0.46% done
Stats: 0:00:40 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 56.47% done; ETC: 21:50 (0:00:32 remaining)
Stats: 0:00:42 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 60.39% done; ETC: 21:50 (0:00:28 remaining)
Nmap scan report for 10.80.160.139
Host is up (0.15s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 55.52 seconds



The scan revealed only two open ports:

22/tcp – SSH

80/tcp – HTTP

Since SSH typically requires valid credentials, I decided to focus on the web service running on port 80, as web applications often provide an initial entry point.
