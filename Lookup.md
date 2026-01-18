# TryHackMe – Lookup Write-up

## 📌 Room Info

* **Platform:** TryHackMe
* **Room Name:** Lookup
* **Difficulty:** Easy
* **Objective:** Gain initial access and escalate privileges to root

---

## 🧠 Methodology

1. Enumeration and Scanning
2. Initial Access
3. Privilege Escalation

---

## 🔍 Enumeration and Scanning

### Nmap Scan

```bash
┌──(root㉿prime)-[~]
└─# nmap -sS -p- 10.80.158.100 -T5 -Pn
Starting Nmap 7.98 ( https://nmap.org ) at 2026-01-17 22:28 -0500
Stats: 0:00:41 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 15.58% done; ETC: 22:33 (0:03:42 remaining)
Stats: 0:01:11 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 29.35% done; ETC: 22:33 (0:02:53 remaining)
Nmap scan report for lookup.thm (10.80.158.100)
Host is up (0.20s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 222.36 seconds

```

**Findings:**

* Open ports discovered:80,22
* Services running on the target:http,22

> Enumeration is the most important phase because all attacks depend on collected information.

---

## 🌐 Web Enumeration

* Visited `http://10.80.158.100` and it's redirect to us on http://lookup.thm
  
  <img width="1753" height="702" alt="image" src="https://github.com/user-attachments/assets/d4db121e-39d1-4544-a874-63b8d1a8edfe" />

Reason: DNS Resolution Problem 🧠

our system works like this:

Browser sees lookup.thm

It asks DNS:
👉 “What is the IP address of lookup.thm?”

DNS says:
❌ “I don’t know this domain”

Because:

lookup.thm is not a real public domain

It exists only inside the TryHackMe lab


  so first we need to edit our /etc/hosts file ,so open it using
  ```bash
  sudo nano /etc/hosts
  ```
  
  ```bash
  sudo nano /etc/hosts

    GNU nano 8.7                                              /etc/hosts *                                                      
127.0.0.1       localhost
127.0.1.1       prime
10.80.158.100   lookup.thm
# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```
save using it's press ctrl+x and enter hit

now again try to open on browser:

<img width="1913" height="774" alt="image" src="https://github.com/user-attachments/assets/e809ea43-3818-4000-9c3d-a3e602ff4e80" />


* Suspected username enumeration vulnerability

Tools used:

* `gobuster`
* Browser manual testing

---

## 🚪 Initial Access

* Identified valid username
* Performed password brute-force
* Successfully logged in

**Result:** Shell access obtained as a low-privileged user

---

## ⬆ Privilege Escalation

* Checked sudo permissions
* Found misconfigured binary/script
* Exploited it to gain root access

```bash
sudo -l
```

**Result:** Root shell obtained 🎉

---

## 🏁 Conclusion

This room helped me understand:

* Importance of enumeration
* Web login vulnerabilities
* Basic Linux privilege escalation techniques

---

## 🛠 Tools Used

* Nmap
* Gobuster
* Linux enumeration commands

---

## 📚 Learning Outcome

This lab strengthened my practical penetration testing skills and improved my methodology for real-world assessments.

