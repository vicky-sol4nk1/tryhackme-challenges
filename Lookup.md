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
nmap -sC -sV -oN nmap.txt <TARGET-IP>
```

**Findings:**

* Open ports discovered
* Services running on the target

> Enumeration is the most important phase because all attacks depend on collected information.

---

## 🌐 Web Enumeration

* Visited `http://<TARGET-IP>`
* Found a login page
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

