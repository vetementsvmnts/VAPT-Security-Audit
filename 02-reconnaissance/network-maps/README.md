# 🗺️ 02 — Reconnaissance: Network Maps

> **Phase:** Reconnaissance | **Module:** Network Mapping & Host Discovery  
> **Tooling:** Nmap · PowerShell · Python  
> **Methodology:** PTES (Penetration Testing Execution Standard) · MITRE ATT&CK® T1046 — Network Service Discovery

---

## 📌 Overview

This module documents the **network discovery and topology mapping** phase of the VAPT engagement. Before any vulnerability can be assessed or exploited, the attacker's surface must be fully enumerated. This phase answers three critical questions:

- **What hosts are alive on the network?**
- **What ports and services are exposed?**
- **What does the network topology look like?**

All scans were conducted in a **controlled, authorised lab environment**. No live production systems were targeted.

---

## 🖼️ Screenshots

### 1. Network Discovery Scan
![Nmap Network Discovery](Nmap%20network%20discovery.png)

> **What you're seeing:** An Nmap host discovery sweep (`-sn` ping scan) across the target subnet. Each responding host is listed with its IP address, MAC address, and vendor fingerprint. This sweep establishes the **live host inventory** — the foundation of every subsequent phase. Hosts that respond here become candidates for deep port scanning and service enumeration.

---

### 2. VAPT Nmap Reconnaissance
![VAPT Nmap Reconnaissance](VAPT%20Nmap%20Reconnaissance.png)

> **What you're seeing:** A full **service version detection** scan (`-sV -sC`) against identified live hosts. Nmap probes each open port to fingerprint the running service, version number, and operating system. This output directly informs which CVEs and exploits are applicable — the bridge between *discovery* and *vulnerability assessment*.

---

## 🛠️ Commands Used

### Stage 1 — Host Discovery (Ping Sweep)
```bash
# Discover all live hosts on the /24 subnet without triggering port scan IDS signatures
nmap -sn 192.168.1.0/24 -oN host_discovery.txt
```
| Flag | Purpose |
|------|---------|
| `-sn` | Ping scan only — no port scan |
| `192.168.1.0/24` | Target subnet range |
| `-oN` | Output in normal readable format |

---

### Stage 2 — Port & Service Enumeration
```bash
# Deep scan against live hosts: all ports, version detection, default scripts
nmap -sV -sC -T4 -p- 192.168.1.0/24 -oA recon_output
```
| Flag | Purpose |
|------|---------|
| `-sV` | Probe open ports to determine service/version |
| `-sC` | Run default NSE scripts (banner grab, SSH hostkeys, etc.) |
| `-T4` | Aggressive timing — faster scan |
| `-p-` | Scan all 65,535 ports, not just top 1000 |
| `-oA` | Output in all formats (normal, XML, grepable) |

---

### Stage 3 — OS Fingerprinting
```bash
# Requires root/admin — attempts OS detection via TCP/IP stack behaviour
nmap -O --osscan-guess 192.168.1.0/24
```

---

## 📊 Findings Summary

| Host | Open Ports | Services Detected | Risk Level |
|------|-----------|------------------|------------|
| 192.168.1.1 | 22, 80, 443 | SSH, HTTP, HTTPS | 🟡 Medium |
| 192.168.1.10 | 21, 23, 3389 | FTP, Telnet, RDP | 🔴 High |
| 192.168.1.20 | 8080, 8443 | HTTP-Alt, HTTPS-Alt | 🟡 Medium |
| 192.168.1.50 | 445, 139 | SMB, NetBIOS | 🔴 High |

> ⚠️ *Hostnames and IPs are representative of a lab environment. Real engagement findings are redacted per rules of engagement.*

---

## 🗂️ Files in this Module

| File | Description |
|------|-------------|
| `Nmap network discovery.png` | Screenshot — live host discovery sweep output |
| `VAPT Nmap Reconnaissance.png` | Screenshot — full port/service version scan output |
| `README.md` | This document |

---

## 🔗 MITRE ATT&CK® Mapping

| Technique | ID | Description |
|-----------|-----|-------------|
| Network Service Discovery | T1046 | Enumerating open ports and services |
| Active Scanning | T1595 | Host discovery via ICMP/TCP probes |
| OS Fingerprinting | T1592.001 | Identifying OS via stack behaviour |

---

## ➡️ Next Phase

Once the network map is complete, findings feed into:

**`03-vulnerability-assessment/`** — Where open services are cross-referenced against CVE databases and scanned with tools such as OpenVAS / Nessus.

---

## ⚠️ Legal Disclaimer

All reconnaissance activities documented here were performed on **authorised, isolated lab infrastructure** for educational and portfolio purposes only. Performing these scans against systems you do not own or have explicit written permission to test is **illegal** and violates computer fraud laws in most jurisdictions.

---

*Part of the [VAPT-Security-Audit](../../README.md) portfolio — a structured penetration testing documentation project following industry-standard methodology.*
