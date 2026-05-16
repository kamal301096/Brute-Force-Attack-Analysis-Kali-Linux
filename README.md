# 🔐 Brute Force Attack Analysis and 
# Mitigation Using Kali Linux

![Status](https://img.shields.io/badge/Status-Completed-success)
![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-red)
![Institute](https://img.shields.io/badge/Institute-IIT%20Madras-orange)
![Tools](https://img.shields.io/badge/Tools-Hydra%20%7C%20Wireshark%20%7C%20iptables-blue)

---

## 👤 Author

**Kamalpreet Singh**
AI-Powered Cybersecurity Mastery — IIT Madras
[GitHub](https://github.com/kamal301096)

---

## 📌 Overview

This project investigates and mitigates
brute force attack patterns across system
and network layers using Kali Linux.

Completed as part of IIT Madras
AI-Powered Cybersecurity Mastery Program
Course 2 — Designing Secure Systems
Networks and Devices.

---

## 🏢 Real World Scenario

**Company:** SecureCore Technologies

A mid-sized enterprise detected surge in
failed login attempts on critical servers.
Despite active security systems deeper
log analysis revealed ongoing brute force
attacks targeting weak credentials and
misconfigured policies.

---

## 🎯 Objective

Investigate and mitigate brute force
attack patterns by:

- Analyzing system logs
- Auditing user accounts
- Reviewing firewall rules
- Analyzing encrypted files
- Identifying unauthorized access

---

## 🛠️ Tools Used

| Tool | Purpose |
|------|---------|
| Kali Linux | Investigation platform |
| Hydra | Brute force simulation |
| Wireshark | Packet analysis |
| iptables | Firewall inspection |
| rsyslog | Log capture |
| GPG | Encryption analysis |
| ent | Entropy analysis |

---

## ✅ Tasks Completed

### Task 1 — Baseline System Check
Established system baseline including
OS version kernel network configuration
running services and disk usage.

**Commands:**
```bash
sudo apt update && sudo apt upgrade -y
uname -a
ip addr
ss -tulpn
df -h
free -h
```

---

### Task 2 — User Account Audit
Investigated accounts and permissions
to identify weak credentials and
unauthorized access points.

**Key Findings:**
- postgres had interactive bash shell
- kali-trusted had NOPASSWD sudo access
- No unauthorized UID 0 accounts
- No passwordless accounts detected

---

### Task 3 — Firewall Analysis
Examined firewall configuration open
ports and active network connections.

**Critical Finding:**
All iptables chains set to ACCEPT
with zero filtering rules active.
Completely open firewall detected.

---

### Task 4 — SSH Brute Force Detection
Monitored SSH logs to identify
brute force attack patterns.

**Simulation Details:**
- Tool: Hydra
- Target: red user account
- Wordlist: rockyou.txt 134MB
- Threads: 4 parallel
- Result: 20 failed attempts logged
- Outcome: No successful login

---

### Task 5 — Entropy Analysis
Scanned for encrypted files using
entropy analysis to detect hidden data.

**Results:**

| File | Entropy | Conclusion |
|------|---------|------------|
| normal.txt | 3.6 bits/byte | Plain text |
| secret.txt.gpg | 6.24 bits/byte | Encrypted |

---

### Task 6 — Log Analysis
Deep analysis of authentication logs
to uncover complete attack picture.

**Findings:**
- 20 failed attempts recorded
- Attack every 1-3 seconds
- Confirms automated tool
- No successful login
- Started 14/05/2026 at 20:30

---

### Task 7 — Wireshark Analysis
Analyzed two packet capture files
to detect network intrusion attempts.

**File 1 — NmapScanANDDoS.pcapng:**
- Nmap SYN stealth scan detected
- Fingerprint Win=1024 MSS=1460
- SYN flood DoS identified
- Attacker: 192.168.12.131

**File 2 — MITM.pcapng:**
- ARP cache poisoning detected
- Man in middle attack confirmed
- ICMP redirects identified

---

## 🔍 Security Findings

| Finding | Risk | Mitigated |
|---------|------|-----------|
| Open firewall | Critical | ✅ Yes |
| postgres bash shell | High | ✅ Yes |
| kali-trusted NOPASSWD | High | ✅ Yes |
| SSH brute force | High | ✅ Yes |
| ARP poisoning | Critical | ✅ Detected |
| Nmap SYN scan | Medium | ✅ Detected |

---

## 🛡️ Mitigations Applied

### Firewall
```bash
sudo iptables -A INPUT -m state \
--state ESTABLISHED,RELATED -j ACCEPT

sudo iptables -A INPUT -p tcp \
--dport 22 -s 192.168.64.0/24 -j ACCEPT

sudo iptables -P INPUT DROP

sudo iptables-save > \
/etc/iptables/rules.v4
```

### SSH Hardening
```bash
sudo apt install fail2ban -y
sudo service fail2ban start

# Set MaxAuthTries 3
# Set PermitRootLogin no
# Set PasswordAuthentication no
sudo service ssh restart
```

### File Security
```bash
chmod 700 /home/red/.ssh
chmod 700 /home/red/.gnupg
md5sum /home/red/* > baseline.txt
```

---

## 💻 Lab Environment

| Component | Detail |
|-----------|--------|
| Platform | VMware Workstation Pro |
| OS | Kali Linux |
| RAM | 8GB |
| CPU | 2 Cores |
| Network | NAT Mode |

---

## 📸 Screenshots

### Task 1 — Baseline Check
![Task 1](screenshots/task1/)

### Task 2 — User Audit
![Task 2](screenshots/task2/)

### Task 3 — Firewall
![Task 3](screenshots/task3/)

### Task 4 — Brute Force
![Task 4](screenshots/task4/)

### Task 5 — Entropy
![Task 5](screenshots/task5/)

### Task 6 — Log Analysis
![Task 6](screenshots/task6/)

### Task 7 — Wireshark
![Task 7](screenshots/task7/)

---

## 📚 What I Learned

- How brute force attacks work in real time
- Detecting attacks through log analysis
- Hardening SSH and firewall configurations
- Using entropy to identify encrypted data
- How ARP poisoning enables MITM attacks
- How Nmap SYN scans fingerprint targets
- Applying practical mitigation techniques

---

## 🎓 Certification

**Program:** AI-Powered Cybersecurity Mastery
**Institute:** IIT Madras
**Course:** Designing Secure Systems
Networks and Devices
**Status:** Completed ✅

---

## 📬 Connect

**GitHub:** [kamal301096](https://github.com/kamal301096)

---

*Completed in isolated lab environment*
*for educational purposes only*
