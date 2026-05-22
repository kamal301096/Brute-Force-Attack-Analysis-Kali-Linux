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

### Task 4 — SSH Brute Force Detection & Mitigation

This task demonstrates a full before-and-after analysis of an SSH brute force attack, showing the system's vulnerability without protection and the effectiveness of Fail2Ban as a mitigation control.

---

#### 🔴 Before Mitigation

The SSH service was started with no brute-force protection in place. Hydra was used to launch a dictionary attack using the rockyou.txt wordlist. With nothing blocking repeated attempts, thousands of password tries were sent continuously to the SSH service.

**Step 1 — Start SSH Service**

```bash
sudo service ssh start
sudo service ssh status
```

SSH service started successfully and is confirmed active on port 22.

*Figure 1: SSH service started and confirmed active on port 22*

<img width="871" height="400" alt="Screenshot 2026-05-22 190909" src="https://github.com/user-attachments/assets/3fe0f4bc-3e11-4427-a8c3-9c1bf362dd3f" />


---

**Step 2 — Launch Hydra Brute-Force Attack (No Protection)**

```bash
hydra -l red -P /usr/share/wordlists/rockyou.txt ssh://127.0.0.1 -t 4 -V
```

Hydra launched 14,344,399 login attempts using 4 parallel threads. With no Fail2Ban active, every attempt reached the SSH service without being blocked or throttled.

*Figure 2: Hydra sending unrestricted brute-force attempts — no protection active*

<img width="1655" height="661" alt="Screenshot 2026-05-22 191027" src="https://github.com/user-attachments/assets/964dcc7b-2c7d-4558-9984-240b019115d1" />


---

**Step 3 — Failed Attempts Logged in auth.log**

```bash
grep "Failed password" /var/log/auth.log | tail -20
```

The auth.log file recorded a continuous stream of failed password attempts from 127.0.0.1 every 1–3 seconds, confirming the attack was reaching the SSH service with no intervention.

*Figure 3: auth.log showing rapid failed SSH login attempts with no banning*

<img width="949" height="374" alt="Screenshot 2026-05-22 191235" src="https://github.com/user-attachments/assets/b66a6a31-ad7a-4af0-b00a-143dc37552ee" />


---

#### 🟢 Applying Mitigation — Installing and Configuring Fail2Ban

**Step 4 — Install Fail2Ban**

```bash
sudo apt install fail2ban -y
```

Fail2Ban installed successfully along with its dependency python3-systemd.

*Figure 4: Fail2Ban installed via apt package manager*
<img width="1665" height="709" alt="Screenshot 2026-05-22 191609" src="https://github.com/user-attachments/assets/d718ef73-3208-4629-b123-b01d64aaefde" />


---

**Step 5 — Configure the SSH Jail**

The Fail2Ban SSH jail was configured with the following settings:

```
[sshd]
enabled = true
port = 22
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 600
findtime = 600
backend = auto
ignoreself = false
```

Setting `ignoreself = false` ensures the loopback address (127.0.0.1) is not exempt from banning, which is critical for this local simulation.

*Figure 5: Fail2Ban SSH jail configuration in nano*

<img width="591" height="424" alt="Screenshot 2026-05-22 200558" src="https://github.com/user-attachments/assets/2acc3a00-46d6-4465-9f39-fa2253bce21d" />


---

**Step 6 — Start Fail2Ban and Verify Baseline Status**

```bash
sudo service fail2ban start
sudo service fail2ban status
sudo fail2ban-client status sshd
```

Fail2Ban started successfully and confirmed active. The initial jail status showed zero failed attempts and no banned IPs, confirming a clean baseline before re-running the attack.

*Figure 6: Fail2Ban service active and running*

<img width="746" height="332" alt="Screenshot 2026-05-22 191710" src="https://github.com/user-attachments/assets/8aefee95-a32b-4d71-8af2-e8637496607e" />


*Figure 7: Fail2Ban sshd jail showing 0 failed attempts and empty banned IP list*

<img width="647" height="191" alt="Screenshot 2026-05-22 191852" src="https://github.com/user-attachments/assets/fae0ed3f-8e39-415b-afc6-9e36752f4565" />



---

#### ✅ After Mitigation

**Step 7 — Re-run Hydra Attack — Blocked and IP Banned**

```bash
hydra -l red -P /usr/share/wordlists/rockyou.txt ssh://127.0.0.1 -t 4 -V
sudo fail2ban-client status sshd
```

When Hydra was launched again, it immediately received a `Connection refused` error instead of attempting passwords. The Fail2Ban jail status confirmed 13 total failed attempts, 1 currently banned IP, and 127.0.0.1 in the banned IP list. The mitigation was fully effective.

*Figure 8: Hydra blocked with Connection refused; Fail2Ban showing 127.0.0.1 banned after 13 failed attempts*

<img width="1655" height="362" alt="Screenshot 2026-05-22 200223" src="https://github.com/user-attachments/assets/2588c8a6-5262-408c-b437-79251eac58a7" />


---

**Simulation Summary:**

| | Before Fail2Ban | After Fail2Ban |
|---|---|---|
| Hydra result | Unlimited attempts | Connection refused |
| auth.log | Flooded with failures | Attack stopped |
| Banned IPs | None | 127.0.0.1 |
| Total failed attempts | Thousands | 13 (then blocked) |
| Protection | ❌ None | ✅ Active |

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
<img width="1462" height="648" alt="Screenshot 2026-05-11 195128" src="https://github.com/user-attachments/assets/866043af-c8ad-4738-aee8-79414498444d" />

<img width="838" height="53" alt="Screenshot 2026-05-11 211816" src="https://github.com/user-attachments/assets/174d973f-f4a0-49e9-94f2-148bca49f32e" />

<img width="812" height="232" alt="Screenshot 2026-05-11 212032" src="https://github.com/user-attachments/assets/92e39fb8-6392-4187-b387-0062ba222219" />

<img width="926" height="756" alt="Screenshot 2026-05-11 214711" src="https://github.com/user-attachments/assets/3a0ca834-f883-468c-9722-b18cb4343b7d" />

<img width="895" height="487" alt="Screenshot 2026-05-11 214732" src="https://github.com/user-attachments/assets/8afdf1aa-3944-425d-b84c-b5ec2e3456e0" />

<img width="396" height="83" alt="Screenshot 2026-05-11 221404" src="https://github.com/user-attachments/assets/6ec3f702-9c21-4f30-a920-f135ab3ff3f3" />

<img width="955" height="240" alt="Screenshot 2026-05-12 190614" src="https://github.com/user-attachments/assets/3e05213a-6b2f-4fb7-89bb-5ad51afe8443" />

### Task 2 — User Audit
<img width="1595" height="873" alt="Screenshot 2026-05-13 201001" src="https://github.com/user-attachments/assets/0e2ae7a4-7f7e-42c7-ba41-5ab2102d5bc4" />

<img width="842" height="92" alt="Screenshot 2026-05-13 201601" src="https://github.com/user-attachments/assets/efb15f1c-40fe-4f75-9e31-8585cc3fa067" />

<img width="803" height="253" alt="Screenshot 2026-05-13 201947" src="https://github.com/user-attachments/assets/34dab391-9e8d-422e-8833-ab7ed06e5cbf" />

<img width="1621" height="143" alt="Screenshot 2026-05-13 202740" src="https://github.com/user-attachments/assets/5f87afaa-8c0f-4431-94d1-dcc733e54ee4" />

<img width="667" height="203" alt="Screenshot 2026-05-13 203207" src="https://github.com/user-attachments/assets/7a0d4a37-1d86-40ea-9865-2b41120a0001" />

<img width="653" height="93" alt="Screenshot 2026-05-13 203227" src="https://github.com/user-attachments/assets/2c6d330f-e339-42b9-afc9-7b55e7ba1de2" />

<img width="712" height="792" alt="Screenshot 2026-05-13 204810" src="https://github.com/user-attachments/assets/52aa5a9f-b0af-48f6-a9d1-0a78b77081ff" />

<img width="667" height="207" alt="Screenshot 2026-05-13 204831" src="https://github.com/user-attachments/assets/c9bfc9de-b534-4991-b349-32c2d17fa247" />

<img width="318" height="65" alt="Screenshot 2026-05-13 205711" src="https://github.com/user-attachments/assets/9234f497-a88c-42a5-81de-60539abfa3ec" />

<img width="307" height="382" alt="Screenshot 2026-05-13 205930" src="https://github.com/user-attachments/assets/8fff6c60-acc8-4f6d-a780-6f9dfb219421" />

<img width="383" height="52" alt="Screenshot 2026-05-13 210559" src="https://github.com/user-attachments/assets/d9f991d3-a906-4859-bc20-18a03a0bfd07" />

<img width="463" height="92" alt="Screenshot 2026-05-13 210636" src="https://github.com/user-attachments/assets/0fde9ec2-aa29-4cb3-8273-873559b1f9c5" />

<img width="590" height="132" alt="Screenshot 2026-05-13 211156" src="https://github.com/user-attachments/assets/792e5567-b66a-4b85-bce5-af8276b3a694" />

<img width="738" height="757" alt="Screenshot 2026-05-13 211342" src="https://github.com/user-attachments/assets/211cd872-6179-4118-a183-021770bfcb33" />

<img width="586" height="350" alt="Screenshot 2026-05-13 211355" src="https://github.com/user-attachments/assets/c1338aeb-7031-4014-a693-4c4c6ffa5a58" />

<img width="267" height="743" alt="Screenshot 2026-05-13 211848" src="https://github.com/user-attachments/assets/947bc893-b49a-4819-b1ee-14650b257adf" />

<img width="207" height="742" alt="Screenshot 2026-05-13 211920" src="https://github.com/user-attachments/assets/26c2e567-fdfc-4a1c-9495-c43c1dd9abf6" />

<img width="365" height="72" alt="Screenshot 2026-05-13 212133" src="https://github.com/user-attachments/assets/4a442fa3-d2dc-4a5a-a25b-c4dfbb676b3d" />

<img width="370" height="161" alt="Screenshot 2026-05-13 212953" src="https://github.com/user-attachments/assets/656fd8ee-d915-4ecf-bc44-fadc314e98ca" />


### Task 3 — Firewall
<img width="662" height="216" alt="Screenshot 2026-05-14 185731" src="https://github.com/user-attachments/assets/a59ef236-834e-4cb4-b074-0004c9305a9c" />

<img width="228" height="95" alt="Screenshot 2026-05-14 185916" src="https://github.com/user-attachments/assets/c098be65-e641-419b-8785-93bdacd1724e" />

<img width="1632" height="132" alt="Screenshot 2026-05-14 185940" src="https://github.com/user-attachments/assets/8b5f6781-78a1-4e16-bdfe-15d553c76183" />

<img width="816" height="207" alt="Screenshot 2026-05-14 190007" src="https://github.com/user-attachments/assets/8a83eb11-53b6-4da9-9074-adf7ca52e874" />

<img width="803" height="245" alt="Screenshot 2026-05-14 190038" src="https://github.com/user-attachments/assets/b0daa3d3-6b0a-4125-9666-68d452f75908" />

<img width="678" height="72" alt="Screenshot 2026-05-14 190046" src="https://github.com/user-attachments/assets/784eb8d8-feeb-4b37-8103-5d63cff2de6d" />

<img width="268" height="107" alt="Screenshot 2026-05-14 190115" src="https://github.com/user-attachments/assets/343ebddf-97e3-479d-bbab-8077bf9caf52" />

<img width="512" height="202" alt="Screenshot 2026-05-14 190144" src="https://github.com/user-attachments/assets/a7b53dea-7367-40cf-8697-796210c1b161" />


### Task 4 — Brute Force & Fail2Ban Mitigation

#### Before Mitigation

*Figure 1: SSH service started and confirmed active on port 22*
<img width="871" height="400" alt="SSH service started" src="https://github.com/user-attachments/assets/Screenshot_2026-05-22_190909" />

*Figure 2: Hydra sending unrestricted brute-force attempts — no protection active*
<img width="1655" height="661" alt="Hydra attack no protection" src="https://github.com/user-attachments/assets/Screenshot_2026-05-22_191027" />

*Figure 3: auth.log showing rapid failed SSH login attempts with no banning*
<img width="949" height="374" alt="auth log failed attempts" src="https://github.com/user-attachments/assets/Screenshot_2026-05-22_191235" />

#### Applying Mitigation

*Figure 4: Fail2Ban installed via apt package manager*
<img width="1665" height="709" alt="Fail2Ban install" src="https://github.com/user-attachments/assets/Screenshot_2026-05-22_191609" />

*Figure 5: Fail2Ban service active and running*
<img width="746" height="332" alt="Fail2Ban running" src="https://github.com/user-attachments/assets/Screenshot_2026-05-22_191710" />

*Figure 6: Fail2Ban sshd jail showing 0 failed attempts and empty banned IP list*
<img width="647" height="191" alt="Fail2Ban baseline status" src="https://github.com/user-attachments/assets/Screenshot_2026-05-22_191852" />

*Figure 7: Fail2Ban SSH jail configuration in nano*
<img width="591" height="424" alt="Fail2Ban config" src="https://github.com/user-attachments/assets/Screenshot_2026-05-22_200558" />

#### After Mitigation

*Figure 8: Hydra blocked with Connection refused; Fail2Ban showing 127.0.0.1 banned after 13 failed attempts*
<img width="1655" height="362" alt="After mitigation Hydra blocked" src="https://github.com/user-attachments/assets/Screenshot_2026-05-22_200223" />

---

**Original Task 4 Screenshots:**

<img width="232" height="60" alt="Screenshot 2026-05-14 202125" src="https://github.com/user-attachments/assets/fbe6cd8c-dc13-4ad6-b5cd-889a21fba9a2" />

<img width="745" height="332" alt="Screenshot 2026-05-14 202149" src="https://github.com/user-attachments/assets/b7d07de8-7e79-4452-add2-8f3051cdbbfc" />

<img width="790" height="173" alt="Screenshot 2026-05-14 202221" src="https://github.com/user-attachments/assets/517732c9-893e-4e0e-b746-bbf6f86b4d6f" />

<img width="1138" height="120" alt="Screenshot 2026-05-14 202519" src="https://github.com/user-attachments/assets/9962d0ad-3ef5-4991-844c-b0a5a3bedb6b" />

<img width="545" height="78" alt="Screenshot 2026-05-14 202603" src="https://github.com/user-attachments/assets/15f4b25f-6644-4407-8b5e-264130cd3e35" />

<img width="582" height="120" alt="Screenshot 2026-05-14 202739" src="https://github.com/user-attachments/assets/5284e54d-1707-4c52-8b0e-98cd04ec60c6" />

<img width="1547" height="707" alt="Screenshot 2026-05-14 202852" src="https://github.com/user-attachments/assets/63878e4d-96e3-484d-88dd-26631d5761ed" />

<img width="1166" height="403" alt="Screenshot 2026-05-14 202939" src="https://github.com/user-attachments/assets/7ced9453-d1a0-43b7-8605-6cb839cb2f21" />

<img width="1508" height="436" alt="Screenshot 2026-05-14 203056" src="https://github.com/user-attachments/assets/e2bba088-9f23-4f0e-96dd-6dd0f32b8003" />

<img width="1368" height="782" alt="Screenshot 2026-05-14 203125" src="https://github.com/user-attachments/assets/2739ec79-5ba9-4a2d-90c7-2100d02bdaf8" />

<img width="965" height="373" alt="Screenshot 2026-05-14 203148" src="https://github.com/user-attachments/assets/4f9725a4-ad81-4aa9-9cb0-e7900365c8cc" />

<img width="787" height="111" alt="Screenshot 2026-05-14 203212" src="https://github.com/user-attachments/assets/30532115-e16b-48b6-b543-6364414b6554" />

<img width="520" height="67" alt="Screenshot 2026-05-14 203236" src="https://github.com/user-attachments/assets/3c12d52e-eabb-4083-b62f-f6a7025448c8" />

<img width="1243" height="540" alt="Screenshot 2026-05-14 203410" src="https://github.com/user-attachments/assets/ca89c7d4-4f23-4be3-93d7-60813cc088f0" />


### Task 5 — Entropy
<img width="482" height="148" alt="Screenshot 2026-05-15 071843" src="https://github.com/user-attachments/assets/28f5e569-b24a-4567-998c-6c892ebdfb90" />

<img width="1050" height="345" alt="Screenshot 2026-05-15 072156" src="https://github.com/user-attachments/assets/64f64d45-e4d6-4879-9a9e-4dd929fcaa05" />

<img width="1198" height="328" alt="Screenshot 2026-05-15 072212" src="https://github.com/user-attachments/assets/75efd644-943b-4a55-899d-e4607cee74a3" />

<img width="765" height="463" alt="Screenshot 2026-05-15 072359" src="https://github.com/user-attachments/assets/00b5db47-6ac6-4b42-8aa1-55c9080512b3" />

<img width="572" height="377" alt="Screenshot 2026-05-15 072723" src="https://github.com/user-attachments/assets/d8b5bd82-ccba-43ee-8bfa-09e85ee504a7" />

<img width="470" height="72" alt="Screenshot 2026-05-15 072802" src="https://github.com/user-attachments/assets/aecabb5f-20e3-47e6-af96-d92fcb53976a" />

<img width="708" height="76" alt="Screenshot 2026-05-15 072847" src="https://github.com/user-attachments/assets/391e9bdb-e250-4800-a0e7-0ea0143af3a8" />

<img width="1641" height="502" alt="Screenshot 2026-05-15 073015" src="https://github.com/user-attachments/assets/6e76ebe2-c11d-4316-855a-927544a097fe" />

<img width="603" height="230" alt="Screenshot 2026-05-15 073108" src="https://github.com/user-attachments/assets/716f5af2-303e-402a-8ab8-df0b432c58b9" />


### Task 6 — Log Analysis
<img width="401" height="80" alt="Screenshot 2026-05-15 140722" src="https://github.com/user-attachments/assets/640dffda-f778-44ed-9b77-7b3174a535b5" />

<img width="746" height="57" alt="Screenshot 2026-05-15 140747" src="https://github.com/user-attachments/assets/1c608e09-0164-4732-a646-ab925dc9c0a9" />

<img width="663" height="357" alt="Screenshot 2026-05-15 140813" src="https://github.com/user-attachments/assets/f8996b36-60a0-4bcf-8785-e20f36ce6e7e" />

<img width="435" height="68" alt="Screenshot 2026-05-15 140845" src="https://github.com/user-attachments/assets/49cc8d66-4d95-4bba-9106-6170752c22b2" />

<img width="1116" height="150" alt="Screenshot 2026-05-15 140915" src="https://github.com/user-attachments/assets/d5bcf4db-2ecb-4287-9035-22b057b05050" />


### Task 7 — Wireshark
<img width="1697" height="552" alt="Screenshot 2026-05-15 152440" src="https://github.com/user-attachments/assets/8a8dc011-404f-44f9-bad0-5a6cd5404ea9" />

<img width="1707" height="551" alt="Screenshot 2026-05-15 152523" src="https://github.com/user-attachments/assets/8fc6139e-554c-43b9-bca2-17c1e947b735" />

<img width="1552" height="566" alt="Screenshot 2026-05-15 152603" src="https://github.com/user-attachments/assets/0479fe48-747e-4285-a260-fbc272468c92" />

<img width="1707" height="577" alt="Screenshot 2026-05-15 152641" src="https://github.com/user-attachments/assets/33985e65-6ab6-43fd-8924-7930af409730" />

<img width="1550" height="652" alt="Screenshot 2026-05-15 152713" src="https://github.com/user-attachments/assets/934ebdb8-a376-4d31-9f6c-5c30248b511c" />

<img width="1658" height="758" alt="Screenshot 2026-05-15 152744" src="https://github.com/user-attachments/assets/946d44ca-b237-4071-a439-001a0cc923a6" />

<img width="1617" height="597" alt="Screenshot 2026-05-15 152817" src="https://github.com/user-attachments/assets/148b7b29-ebed-460c-8c55-185b3089f872" />

<img width="1692" height="515" alt="Screenshot 2026-05-15 152837" src="https://github.com/user-attachments/assets/cd57e843-81ac-49f3-be41-789ff3747ea8" />


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
