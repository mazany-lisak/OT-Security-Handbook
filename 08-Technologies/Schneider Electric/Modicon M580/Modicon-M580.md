---
title: Schneider Electric Modicon M580 ePAC — Security Architecture and Engineering Reference
category: Platform Reference
version: 1.1.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-06-29
review_cycle: Annual
---

# Purpose

This document provides a comprehensive security architecture and engineering reference for the Schneider Electric Modicon M580 ePAC (Ethernet Programmable Automation Controller) from the perspective of an OT Security Architect.

It consolidates guidance across the complete M580 security domain: platform architecture, security capabilities, hardening, communication protocols, identity management, certificates, firmware governance, diagnostics, monitoring, backup, patch management, high availability, distributed I/O, communication modules, derived function blocks and safety systems.

The M580 must be understood as a **cyber-physical control node** — not merely a programmable controller. Compromise of an M580 is operationally equivalent to losing control of the physical process it governs, with potential consequences for production, safety and the environment.

**Intended audience:** OT Security Architects, Automation Engineers, System Integrators, OT Administrators, AI knowledge base, RAG systems.

> **Regulatory context:** M580 security directly supports compliance with IEC 62443-3-3 (System Security Requirements), IEC 62443-4-2 (Component Security Requirements), NIS2 Article 21, and Czech Cybersecurity Act Decrees 409/410. Schneider Electric positions the M580 as a Secure-by-Design platform targeting IEC 62443-4-2 Security Level 2 compliance.

> **Platform note:** The Modicon M580 is a Schneider Electric (formerly Modicon) platform. Chapters covering PROFINET, S7Comm and S7Comm Plus are Siemens-specific protocols and do not apply to this platform. Equivalent M580 chapters cover EtherNet/IP, Modbus TCP, and Ethernet RIO — the native communication technologies of this platform.

---

# Related Documents

| Document | Relationship |
|----------|-------------|
| [IEC62443.md](#) | Engineering security framework |
| [NIS2.md](#) | EU governance framework |
| [Czech-Cybersecurity-Act.md](#) | Czech national obligations |
| [ISA95.md](#) | Functional reference model — M580 operates at ISA-95 Level 1 |

---

# Document Structure

| Chapter | Content |
|---------|---------|
| [1 — Overview](#1--overview) | Platform architecture, CPU families, Purdue placement, security features |
| [2 — Security](#2--security) | Security philosophy, threat model, defense in depth, IEC 62443 |
| [3 — Hardening](#3--hardening) | Attack surface reduction, phased hardening methodology |
| [4 — Best Practices](#4--best-practices) | Reference architecture, common mistakes, compliance mapping |
| [5 — Identity and Access](#5--identity-and-access) | Authentication, RBAC, ACL, identity lifecycle |
| [6 — Protection](#6--protection) | Secure Boot, firmware signing, application protection, write protection |
| [7 — Certificates](#7--certificates) | PKI, HTTPS certificates, IPsec, OPC UA, lifecycle |
| [8 — Firmware](#8--firmware) | Firmware governance, signed firmware, AES-256, update process |
| [9 — EtherNet/IP](#9--ethernetip) | CIP protocol, security limitations, M580 implementation |
| [10 — Modbus TCP](#10--modbus-tcp) | Protocol overview, security limitations, mitigations |
| [11 — Ethernet RIO](#11--ethernet-rio) | Remote I/O architecture, S-Ring, security |
| [12 — OPC UA](#12--opc-ua) | OPC UA on M580, security modes, certificates |
| [13 — IPsec](#13--ipsec) | IPsec on M580, tunnel mode, key management |
| [14 — Web Server](#14--web-server) | Integrated HTTPS server, hardening, authentication |
| [15 — Syslog](#15--syslog) | Event forwarding, transport, SIEM integration |
| [16 — SNMP](#16--snmp) | SNMP versions, security, SNMPv3 configuration |
| [17 — Diagnostics](#17--diagnostics) | Diagnostic capabilities, event logs, LED indicators |
| [18 — Monitoring](#18--monitoring) | Security monitoring, EcoStruxure Security Expert |
| [19 — Logging](#19--logging) | Audit trail, event categories, retention |
| [20 — Backup and Recovery](#20--backup-and-recovery) | Backup scope, SD card, recovery scenarios |
| [21 — Patch Management](#21--patch-management) | Firmware patches, Schneider advisories, process |
| [22 — High Availability](#22--high-availability) | Hot Standby, redundant RIO, S-Ring |
| [23 — X80 I/O and Ethernet RIO](#23--x80-io-and-ethernet-rio) | Distributed I/O architecture, security |
| [24 — NOC and Communication Modules](#24--noc-and-communication-modules) | Network Option Cards, serial, fieldbus |
| [25 — DFBs and EFBs](#25--dfbs-and-efbs) | Derived and Elementary Function Blocks, security |
| [26 — M580 Safety](#26--m580-safety) | Safety CPU, SIL, safety-security intersection |

---
---

# 1 — Overview

---

## 1.1 Platform Role

The Schneider Electric Modicon M580 is Schneider's current-generation Ethernet Programmable Automation Controller (ePAC), positioned as the successor to the Modicon Quantum and Premium PLC families. The "Ethernet-native" designation reflects that Ethernet is not an add-on communication option — it is the core backplane bus of the M580 architecture, enabling high-speed, deterministic communication across the rack and to distributed I/O.

Schneider Electric positions the M580 as a **Secure-by-Design** platform, with embedded cybersecurity capabilities including digitally signed and AES-256-encrypted firmware, Access Control Lists (ACL), IPsec, HTTPS, and Security Event Logging.

In an IACS context, the M580 occupies **ISA-95 Level 1** (Basic Control), with downward interfaces to Level 0 field devices (via X80 I/O and Ethernet RIO) and upward interfaces to Level 2 supervisory systems (SCADA, HMI). Direct communication to Level 3 or Level 4 must traverse defined security conduits.

**The M580 directly controls physical processes.** A compromised M580 can cause production disruption, equipment damage, environmental incidents, or — where safety systems are involved — injury or loss of life.

## 1.2 CPU Families

| CPU Family | Key Characteristics | Security Capability |
|-----------|--------------------|--------------------|
| **BMEP58•040 / 058 / 1020 / 2020 / 3020** | Standard M580 CPUs; Ethernet backplane; vary in task capacity | Full M580 security feature set |
| **BMEH582040 / 584040 / 586040** | Hot Standby CPUs (H suffix); primary/standby redundancy | Full security; dual-CPU management consideration |
| **BMEP58•040S / BMEH58•040S** | Safety-capable CPUs (S suffix); SIL 2/3; standard + safety program | Full security + safety application protection |
| **BMEP581020H / BMEP582020H** | High-performance standard CPUs | Full security |

> **Important:** Schneider Electric introduced a new generation of M580 CPUs (referred to internally as "gray" CPUs due to their casing color, in contrast to older "black" CPUs) with enhanced hardware-based security capabilities, including hardware-rooted Secure Boot and improved IEC 62443-4-2 SL 2 compliance. When assessing an M580 installation, always identify whether the CPU is a gray (new generation) or black (first generation) unit, as security feature availability differs.

## 1.3 ISA-95 / Purdue Placement

```
[ISA-95 Level 4]  ERP, SCM                          → Enterprise IT domain
[ISA-95 Level 3]  MES, GeoSCADA, Historian           → MOM zone
[ISA-95 L3.5]     Industrial DMZ                     → Boundary zone
[ISA-95 Level 2]  SCADA, HMI, Engineering WS         → Control zone
                             │
               ┌─────────────────────────┐
               │     Modicon M580 ePAC   │  ← ISA-95 Level 1
               │  (Level 1 — Basic       │
               │   Control)              │
               └─────────────────────────┘
                             │
[ISA-95 Level 0]  Sensors, Actuators, Drives         → Field zone
                  (via X80 I/O, Ethernet RIO)
```

## 1.4 Embedded Security Capabilities

| Feature | Availability | Notes |
|---------|-------------|-------|
| Access Control Lists (ACL) | All M580 CPUs | IP-based filtering; configurable per Ethernet service |
| Password-protected application | All M580 CPUs | Project-level write protection |
| HTTPS web server | All M580 CPUs | HTTP can be disabled |
| Secure Boot | Gray CPUs (new generation) | Hardware-rooted firmware integrity |
| Firmware digital signature | All M580 CPUs | Reject unsigned firmware |
| AES-256 firmware encryption | All M580 CPUs | Encrypted firmware binaries |
| SHA-256 integrity check | All M580 CPUs | Firmware integrity at startup |
| IPsec | All M580 CPUs | Tunnel-mode IPsec for WAN/remote connections |
| Syslog | All M580 CPUs | Security and operational event forwarding |
| Security Event Logging | All M580 CPUs | Audit trail for security-relevant events |
| SNMPv3 | All M580 CPUs | Secure network monitoring |
| OPC UA (via gateway/NOC) | Depends on architecture | Not native in all models |
| Service enable/disable | All M580 CPUs | Granular protocol enable/disable |

## 1.5 Typical System Architecture

```
[Engineering Zone]
  EcoStruxure Control Expert (Unity Pro) WS
        │ Modbus TCP / EtherNet/IP (engineering mode)
        │
[Industrial DMZ]
  Historian / OPC Server / SCADA Interface
  Jump Server
        │
[Control Zone — OT Network]
  ┌──────────────────────────────────────────┐
  │          Modicon M580 ePAC               │
  │  ┌──────────────────────────────────┐   │
  │  │  M580 CPU (e.g., BMEP584020)     │   │
  │  │  Ethernet Backplane (BMENOC)     │   │
  │  │  HTTPS Web Server                │   │
  │  │  Syslog Client                   │   │
  │  │  ACL Engine                      │   │
  │  │  IPsec Engine                    │   │
  │  └──────────────────────────────────┘   │
  │  ┌──────────────┐  ┌──────────────────┐ │
  │  │ X80 I/O       │  │ NOC Modules      │ │
  │  │ (local rack)  │  │ (BMENOC0301/11)  │ │
  │  └──────────────┘  └──────────────────┘ │
  └──────────────────────────────────────────┘
        │
  Ethernet RIO (S-Ring)
        │
[Field Zone]
  Remote X80 I/O Drops
  Drives (Altivar)
  Field instruments
```

---
---

# 2 — Security

---

## 2.1 Security Philosophy

The M580 should be understood as a **trusted industrial automation platform** — not merely a programmable controller. Security is achieved through multiple complementary layers. No single mechanism provides sufficient protection.

Schneider Electric's Secure-by-Design approach means security capabilities are integrated into the M580 at the hardware and firmware level — but these capabilities must be explicitly configured. Factory defaults are not equivalent to a hardened deployment.

**The fundamental security premise:** If an attacker can download a modified application to an M580, they can arbitrarily manipulate the physical process. This capability alone can cause production shutdown, equipment damage, environmental release, or physical harm. All security architecture decisions must start from this premise.

## 2.2 Threat Model

| Threat | Attack Path | Consequence | Probability |
|--------|-----------|-------------|------------|
| Unauthorized PLC programming | Engineering WS compromise → Control Expert → application download | Malicious control logic; process disruption; safety bypass | Medium-High |
| Ransomware on engineering WS | Phishing → malware → encrypt project files | Engineering capability loss; indirect OT impact | High |
| Credential theft / brute force | Weak passwords; default credentials; no lockout | Unauthorized administrative access | Medium |
| Modbus TCP exploitation | Any host on OT network → read/write PLC registers | Process data manipulation; register write | High (if no ACL) |
| EtherNet/IP exploitation | CIP explicit or implicit messages to M580 | Configuration changes; data access | Medium |
| Firmware manipulation | Unauthorized firmware upload → malicious firmware | Persistent controller compromise | Low-Medium |
| USB / removable media malware | USB device on engineering WS or SD card on M580 | Malware introduction; application replacement | Medium |
| ACL bypass via misconfiguration | Incorrectly configured ACL → unexpected host access | Unauthorized access to PLC services | Medium |
| IPsec key compromise | Weak PSK or poor key management → decrypted WAN traffic | WAN traffic interception; MITM attack | Low-Medium |
| Network reconnaissance | Port scan; Modbus device identification | Mapping for targeted attack | High (if flat network) |
| Physical access | Unlock cabinet → SD card extraction; application upload via local port | IP theft; malicious application | Low (physical controls) |
| Supply chain — firmware | Trojanized firmware or Control Expert update | Persistent compromise in engineering environment | Low but high impact |
| Insider — malicious | Legitimate Control Expert access → unauthorized change | Sabotage; configuration manipulation | Low-Medium |

## 2.3 Defense in Depth Model

```
LAYER 1: Physical Security
  Locked cabinets, physical access control, serial number inventory,
  SD card protection, tamper seals

LAYER 2: Network Segmentation (IEC 62443 Zone/Conduit)
  Dedicated OT VLAN, Industrial DMZ, firewall with allowlist rules,
  no direct IT↔OT, Engineering Zone separation from Control Zone

LAYER 3: Engineering Workstation Security
  Dedicated WS, MFA, PAM, application allowlisting, USB control,
  version control for projects, no internet access from OT WS

LAYER 4: Access Control Lists (ACL)
  IP-based filtering per Ethernet service (Modbus TCP, EtherNet/IP,
  HTTPS, SNMP, FTP), deny-by-default per service

LAYER 5: Application and Controller Protection
  Application write password, read password, project encryption,
  Secure Boot (gray CPUs), signed/encrypted firmware

LAYER 6: Communication Security
  HTTPS (no HTTP), IPsec for WAN/remote, OPC UA TLS (where used),
  SNMPv3 authPriv, Syslog/TLS

LAYER 7: Monitoring and Detection
  Syslog → SIEM, Security Event Logging, ACL deny logging,
  OT IDS passive monitoring, NTP-synchronized timestamps

LAYER 8: Backup and Recovery
  Control Expert project in version control, offline application backup,
  firmware baseline, tested recovery procedure, RTO/RPO defined
```

## 2.4 IEC 62443 Alignment

The M580 is Schneider Electric's reference platform for IEC 62443-4-2 compliance at Security Level 2. Key Foundational Requirement mappings:

| IEC 62443 FR | M580 Implementation |
|-------------|---------------------|
| FR 1 — Identification and Authentication | Password-protected application; ACL by source IP; HTTPS authentication; SNMPv3 |
| FR 2 — Use Control | Application read/write password separation; ACL per service; web server RBAC |
| FR 3 — System Integrity | Signed firmware; AES-256 encrypted firmware; SHA-256 integrity check; Secure Boot (gray); application password |
| FR 4 — Data Confidentiality | IPsec encryption; HTTPS; encrypted firmware |
| FR 5 — Restricted Data Flow | ACL (IP filtering per service); firewall at conduit; service enable/disable |
| FR 6 — Timely Response to Events | Syslog; Security Event Logging; ACL deny logging; diagnostic buffer |
| FR 7 — Resource Availability | Hot Standby redundancy; S-Ring RIO redundancy; backup/restore |

---
---

# 3 — Hardening

---

## 3.1 Hardening Philosophy

Hardening eliminates unnecessary risk by removing everything without a documented operational justification. The most secure M580 is the one exposing the minimum services, protocols and interfaces while still fulfilling its control function.

**Guiding principle:** Every enabled protocol, every open IP service, every active account, and every external interface increases the attack surface. Before enabling any capability, ask: *"Is this operationally required? If compromised, what is the impact?"*

The M580's granular service enable/disable controls and per-service ACL make it possible to achieve very fine-grained attack surface reduction — use these capabilities systematically.

## 3.2 Hardening Lifecycle

```
Factory Defaults (initial state)
        ↓
Physical installation (locked cabinet, tamper seals, SD card protection)
        ↓
Firmware baseline (approved version, validated, AES-256 signed)
        ↓
Initial security configuration (passwords, ACL, service enable/disable)
        ↓
Certificate deployment (HTTPS, IPsec, OPC UA where applicable)
        ↓
Communication hardening (disable unused services, configure ACL)
        ↓
Security baseline validation (verify configuration; offline compare)
        ↓
Production deployment
        ↓
Continuous monitoring (Syslog → SIEM; OT IDS)
        ↓
Periodic review (quarterly: accounts, certs, ACL; annually: full baseline)
        ↓
Recovery testing (annual)
```

## 3.3 Phase 1 — Physical Security

Physical access to an M580 must be treated as privileged access. Physical access enables:
* SD card removal/replacement (application theft or replacement).
* Direct USB/RS-232 connection to engineering port bypassing network controls.
* Hardware replacement (counterfeit CPU or I/O module).
* Power manipulation (forced restart; potential firmware recovery mode).

| Control | Requirement |
|---------|-------------|
| Cabinet locks | M580 installed in locked control cabinet; key management controlled |
| SD card | Protected within locked cabinet; consider removing if application stored only in flash |
| Serial/USB ports | Physical port blockers where local engineering access is not required |
| Asset inventory | Serial numbers, hardware part numbers, firmware versions documented |
| Access log | Physical access to OT cabinet recorded (badge reader or manual log) |
| Tamper seals | Cabinet door seals; intrusion detection where justified |

## 3.4 Phase 2 — Firmware Baseline

See Section 8 (Firmware) for full details. Minimum requirements:
* Deploy only Schneider Electric-approved firmware.
* Verify firmware integrity (SHA-256) before deployment.
* Test in staging environment — never directly in production.
* Maintain firmware inventory per device.
* Document baseline firmware version in asset inventory.

## 3.5 Phase 3 — Application and Controller Protection

| Protection | Configuration | Notes |
|-----------|--------------|-------|
| Application write password | Set strong password | Prevents unauthorized application download |
| Application read password | Set if IP protection required | Prevents application upload/reverse engineering |
| Project encryption | Enable in Control Expert | Encrypts project file at rest |
| Write protection (hardware) | Enable via Control Expert setting | Hardware-enforced write protection |
| Read protection | Set if IP is to be protected | Prevents source code extraction |

**Default state:** Factory M580 has no application password — any user of Control Expert with network access can download or upload the application. This must be changed before production deployment.

## 3.6 Phase 4 — Access Control Lists (ACL)

ACLs on the M580 provide IP-based filtering at the service level. This is one of the most powerful built-in security controls available on the platform.

**ACL architecture:**
* Configured via Control Expert or the embedded web server.
* Applied per Ethernet service (Modbus TCP, EtherNet/IP, HTTPS, SNMP, FTP, etc.).
* Source IP address or IP subnet based.
* Default rule: deny or allow — configure deny-by-default for maximum restriction.

**Recommended ACL configuration per service:**

| Service | Allowed Sources | Denied |
|---------|----------------|--------|
| Modbus TCP (port 502) | SCADA server IP(s), historian IP(s) only | All other sources |
| EtherNet/IP explicit (port 44818) | Engineering WS IP(s), SCADA IP(s) only | All other sources |
| HTTPS (port 443) | Engineering WS IP(s), jump server IP only | All other sources |
| HTTP (port 80) | **Disable entirely** | — |
| SNMP (UDP 161) | NMS / monitoring platform IP(s) only | All other sources |
| FTP | **Disable entirely** (use HTTPS upload) | — |
| NTP client | Internal NTP server IP | N/A (outbound only) |
| Syslog (outbound) | To Syslog server IP | N/A (outbound only) |

**ACL limitations:**
* ACL filters by source IP — it cannot authenticate users or enforce RBAC.
* IP spoofing on a flat network could bypass ACL (network segmentation is the complementary control).
* ACL does not encrypt traffic — it only restricts which sources can reach each service.
* ACL must be maintained when SCADA or engineering WS IP addresses change.

## 3.7 Phase 5 — Service Enable/Disable

M580 allows granular enable/disable per Ethernet service. Disable every service not required for operational function:

| Service | Action | Notes |
|---------|--------|-------|
| HTTP (port 80) | **Disable** | Replace with HTTPS only |
| FTP | **Disable** | No exception — use HTTPS or SFM for file transfer |
| Telnet | **Disable** | No exception — use HTTPS management only |
| SNMP v1/v2c | **Disable** | Replace with SNMPv3 |
| EtherNet/IP | Disable if not required by SCADA/DCS | Many deployments use only Modbus TCP |
| Modbus TCP write | Consider restricting via ACL if only read is needed | Cannot disable write selectively in protocol |
| UMAS (Modicon protocol) | Restrict via ACL to engineering WS only | Used by Control Expert; unrestricted access is high risk |

**UMAS (Unity Messaging Application Services):** The UMAS protocol is the proprietary Schneider/Modicon engineering protocol used by EcoStruxure Control Expert (formerly Unity Pro) for application download, upload, online modification and diagnostics. It operates over Modbus TCP (port 502 function codes) or dedicated UMAS port (port 27700). UMAS must be restricted via ACL to the engineering workstation IP address only — it should not be accessible from SCADA or historian systems.

## 3.8 Phase 6 — Communication Hardening

| Protocol | Action | Notes |
|----------|--------|-------|
| HTTPS | Enable | Replace default certificate |
| HTTP | **Disable** | No exceptions in production |
| IPsec | Enable for WAN/remote connections | Configure strong cipher suite |
| OPC UA (if used) | Sign and Encrypt | See Section 12 |
| Modbus TCP | Restrict via ACL; restrict to required hosts | No native authentication |
| EtherNet/IP | Restrict via ACL; disable if not required | CIP Security where supported |
| SNMPv3 | Enable if monitoring required; authPriv mode | Disable v1/v2c |
| Syslog/TLS | Enable | UDP Syslog not recommended |
| NTP | Authenticated NTP where supported | Time accuracy critical for logging |

## 3.9 Phase 7 — Network Hardening

Required network controls for M580 deployment:
* Dedicated OT VLAN for M580 and field devices — no IT systems on same VLAN.
* Separate Engineering VLAN — not the same VLAN as M580 control traffic.
* Industrial firewall with allowlist rules between Engineering VLAN and Control VLAN.
* Industrial DMZ between OT and IT — never direct IT↔M580 connectivity.
* OT IDS/NDR passive monitoring on the control network segment.
* NTP server accessible from OT network.

**Firewall allowlist rules for M580 (example):**

| Source | Destination | Port/Protocol | Purpose |
|--------|------------|---------------|---------|
| Engineering WS | M580 | TCP 502 (Modbus TCP / UMAS) | Control Expert programming |
| Engineering WS | M580 | TCP 44818 (EtherNet/IP) | Engineering access |
| Engineering WS | M580 | TCP 443 (HTTPS) | Web management |
| SCADA Server | M580 | TCP 502 (Modbus TCP) | Process data |
| NMS | M580 | UDP 161 (SNMPv3) | Monitoring |
| M580 | Syslog server | TCP 6514 (TLS) or TCP 514 | Security events |
| NTP Server | M580 | UDP 123 | Time sync |
| All others | M580 | ANY | DENY |

## 3.10 Hardening Validation

After initial hardening, verify:

- [ ] Firmware version matches approved baseline
- [ ] Application write password set (not empty)
- [ ] ACL configured: all services restricted to authorized source IPs
- [ ] HTTP disabled; HTTPS enabled with non-default certificate
- [ ] FTP disabled
- [ ] SNMP v1/v2c disabled; SNMPv3 configured if monitoring required
- [ ] Syslog/TLS configured; events reaching SIEM
- [ ] UMAS restricted via ACL to engineering WS IP only
- [ ] NTP synchronization active and accurate
- [ ] Unused Ethernet services disabled
- [ ] Physical cabinet locked; SD card protected
- [ ] Control Expert project version matches deployed application (offline compare)

---
---

# 4 — Best Practices

---

## 4.1 Core Principles

1. **Least functionality** — enable only protocols and services with documented operational justification.
2. **ACL for every service** — configure deny-by-default ACL for every enabled M580 Ethernet service.
3. **Least privilege** — every account has only the access required for its defined role.
4. **Defense in depth** — assume each security layer may be breached; design accordingly.
5. **Secure by default** — apply security configuration before first network connection; never rely on post-deployment hardening.
6. **Documented baselines** — every security configuration is documented and version-controlled.
7. **Regular validation** — compare running configuration against baseline; investigate drift.

## 4.2 Common Mistakes Reference

| Mistake | Risk | Correct Practice |
|---------|------|----------------|
| No application write password | Any Control Expert user with network access can download | Set write password before production; store in PAM |
| No ACL configured | Any host on OT network can access all M580 services | Configure deny-by-default ACL per service |
| UMAS accessible from SCADA/historian | SCADA compromise = PLC programming capability | ACL: UMAS to engineering WS IP only |
| HTTP enabled (web server) | Credentials in cleartext | HTTPS only; disable HTTP |
| FTP enabled | Cleartext file transfer; brute-forceable | Disable FTP; use HTTPS for file management |
| Self-signed HTTPS certificate (default) | No PKI governance; no revocation | Deploy PKI-issued certificate |
| SNMP v1/v2c enabled | Community strings in cleartext; device fingerprinting | SNMPv3 authPriv only; or disable SNMP |
| M580 on flat IT/OT network | Network breach = direct PLC access | Zone/conduit model; dedicated OT VLAN |
| No Syslog/TLS configured | Security events lost; no SIEM visibility | Syslog/TLS to centralized SIEM |
| Shared engineering accounts | No audit trail; insider threat detection impossible | Individual named accounts |
| No NTP synchronization | Syslog timestamps wrong; forensics impaired | NTP from trusted internal server |
| Control Expert on general-purpose office PC | Engineering WS compromise = PLC programming capability | Dedicated, hardened engineering WS |
| IPsec with weak PSK | Pre-shared key brute-force attacks | Certificate-based IPsec; strong PSK if PSK required |
| No firmware governance | Running vulnerable firmware indefinitely | Patch management; Schneider PSIRT monitoring |
| Remote vendor access permanent | Persistent attack surface | Time-limited; MFA on WS; jump server; revoke after session |
| Safety and standard application sharing engineering path | Safety bypass if standard path compromised | Separate engineering procedure for safety application |

## 4.3 NIS2 / IEC 62443 Compliance Mapping

| Regulatory Requirement | M580 Engineering Practice |
|-----------------------|--------------------------|
| NIS2 Art. 21(2)(i) — Asset management | Asset inventory: CPU model, serial number, firmware version, ACL baseline |
| NIS2 Art. 21(2)(i) — Access control | Application password; ACL; individual accounts; PAM for engineering WS |
| NIS2 Art. 21(2)(j) — MFA | MFA on engineering workstations; PAM for privileged sessions |
| NIS2 Art. 21(2)(h) — Cryptography | HTTPS; IPsec; AES-256 firmware; SNMPv3 encryption |
| NIS2 Art. 21(2)(e) — Vulnerability management | Firmware patch management; Schneider PSIRT monitoring |
| NIS2 Art. 21(2)(b) — Incident handling | Syslog → SIEM; Security Event Logging; ACL deny logging |
| NIS2 Art. 21(2)(c) — Backup/recovery | Control Expert project backup; SD card backup; tested recovery |
| IEC 62443-4-2 CR 1.1 | Password authentication for application access; ACL for service access |
| IEC 62443-4-2 CR 2.1 | ACL per service; application password restricts configuration changes |
| IEC 62443-4-2 CR 3.1 | Signed/encrypted firmware; SHA-256 integrity; Secure Boot (gray CPU) |
| IEC 62443-4-2 CR 6.1 | Syslog; Security Event Logging; ACL deny logging |
| IEC 62443-4-2 CR 7.3 | Application backup; SD card; tested recovery procedure |

---
---

# 5 — Identity and Access

---

## 5.1 Identity Philosophy

Every engineering action on an M580 must be attributable to a specific, identified individual or service account. This is both a security requirement and a regulatory compliance requirement (IEC 62443-4-2 CR 1.1; NIS2 Art. 21(2)(i)).

**The M580's native identity model is relatively simple compared to Siemens S7-1500 UMAC.** The primary native authentication is the application write/read password and HTTPS web server user accounts. Enterprise-grade RBAC at the controller level relies on complementary controls: ACL (restricts which hosts can access which services), engineering workstation authentication (MFA, PAM), and Control Expert user management.

**Shared engineering accounts are never acceptable.** They defeat audit trails, prevent insider threat detection, and make incident forensics impossible.

## 5.2 Application-Level Protection

The M580 application (Control Expert project) is protected by:

| Protection | Granularity | Purpose |
|-----------|------------|---------|
| Write password | Application level | Prevents unauthorized application download |
| Read password | Application level | Prevents application upload/source code extraction |
| Section password | Program section level | Protects specific code sections within the application |
| Project encryption | Project file | Encrypts .stu/.zef project file at rest |

**Password governance:**
* Write and read passwords must be stored in PAM — loss of the write password means inability to modify the application without clearing it.
* Different password for write vs. read access — operators/maintenance may need read access; only engineers need write access.
* Section passwords are appropriate for protecting third-party library sections or sensitive control algorithms.

## 5.3 Web Server Authentication

The M580 embedded web server provides authentication via user accounts defined in the controller.

**Recommended configuration:**
* Create individual named user accounts — not "admin" shared among multiple people.
* Define user roles per the web server RBAC (administrator, user, guest).
* Disable or delete the default "USER" account (no password by default).
* Configure automatic session timeout.
* HTTPS only — authentication over HTTP is cleartext.

**Web server RBAC (typical roles):**

| Role | Access | Assignment |
|------|--------|-----------|
| Administrator | Full configuration access | Security administrator; PAM-governed |
| User | Read and limited control | Maintenance engineers; operators |
| Guest | Read only | Monitoring; no configuration |

## 5.4 Access Control Lists (ACL) as Primary Access Control

Because the M580 does not have a native per-user engineering authentication mechanism equivalent to Siemens UMAC, **ACL is the primary mechanism restricting which systems can access M580 engineering services**.

ACL restricts at the network level:
* Only the engineering workstation (specific IP) can access UMAS (Control Expert engineering protocol).
* Only SCADA and historian IPs can access Modbus TCP read services.
* Only the NMS can access SNMP.
* Only the jump server IP can access HTTPS management (for remote access scenarios).

This makes the security of the **engineering workstation** critically important — compromise of the engineering WS grants all ACL permissions that the WS has.

## 5.5 EcoStruxure Control Expert User Management

Control Expert (formerly Unity Pro) has its own user management system that governs who can open, modify, and download projects within the engineering tool. This is separate from controller-level authentication.

**Control Expert user management:**
* Supports multiple user profiles with different access rights.
* Rights can be assigned per action (project modification, download, online change, simulation).
* Integrates with Windows user accounts on the engineering workstation.
* Application write password is separate from Control Expert user management — both are required for production download.

**Multi-layer authentication for production download:**
1. Engineering WS login (Windows + MFA).
2. Control Expert user authentication (user account in CE).
3. Application write password (controller-level).
4. PAM approval if implemented for privileged download sessions.

## 5.6 OPC UA Identity

If OPC UA is used (via gateway or NOC module), OPC UA sessions use separate authentication:
* **Anonymous:** Disable in production.
* **Username/password:** Service account in OPC UA server configuration.
* **Certificate-based:** Preferred for automated SCADA/historian connections.

OPC UA service accounts must be separate from Control Expert engineering accounts. See Section 12 for OPC UA security detail.

## 5.7 Identity Lifecycle

| Phase | Action | Responsibility | Frequency |
|-------|--------|---------------|-----------|
| Provisioning | Create named web server account; assign role | Security admin | On role change |
| Control Expert | Create CE user profile; assign rights | Engineering manager | On role change |
| Application password | Set write/read passwords in PAM | Engineering manager | At commissioning; on change |
| Periodic review | Verify accounts still needed; correct roles | Security team | Quarterly |
| Deprovisioning | Delete web server account; revoke CE access; rotate application password | Security admin | Immediately on departure |
| Vendor access | Time-limited WS account; restrict ACL if needed | Engineering manager | Per service event |

---
---

# 6 — Protection

---

## 6.1 Application Write Protection

The application write password prevents unauthorized users from downloading a new or modified application to the M580. This is the single most important native protection control on the platform.

**Configuration in Control Expert:**
* Project Properties → Security → Set write protection password.
* Password required for every application download.
* Password required for online modification (online change of application).
* Password must be stored in PAM — loss requires application clear and full re-download.

## 6.2 Application Read Protection

The read password prevents Control Expert from uploading (reading back) the application from the controller. This protects intellectual property and prevents reconnaissance by reverse-engineering the deployed application.

**Considerations:**
* Read protection also prevents recovery of the application from a backup-less controller — always maintain project backups in version control independently.
* Section-level read passwords allow protecting specific function blocks while leaving others readable (e.g., protect proprietary process algorithms; leave standard logic visible for maintenance).

## 6.3 Secure Boot (Gray CPUs)

New-generation M580 CPUs (gray casing) implement hardware-rooted Secure Boot:
* CPU verifies the integrity and authenticity of firmware at boot time.
* Firmware that fails signature verification is rejected.
* Protects against firmware-level persistent compromise.
* Secure Boot is a hardware-firmware capability — not configurable via Control Expert (it is always active on supported hardware).

## 6.4 Firmware Integrity at Runtime

All M580 CPUs (including black first-generation) verify firmware integrity using SHA-256 at startup:
* If firmware is modified or corrupted, the CPU detects this during the integrity check.
* Integrity failure causes the CPU to refuse to start.
* AES-256 encrypted firmware binaries protect against offline analysis of firmware content.

## 6.5 Write Protection Hardware Mode

M580 supports a hardware-enforced write protection mode, settable via the controller's operating mode selector or via software configuration:
* When hardware write protection is active, the application cannot be modified even with the correct write password.
* Use for production systems where no in-service modification should be possible (only modification during planned maintenance windows).
* Requires physical intervention to disable — provides strong protection against unauthorized online changes.

## 6.6 Application Encryption

Control Expert supports encrypting the project file (.stu / .zef) at rest:
* Project encryption uses a password-derived key.
* Encrypted project cannot be opened without the password.
* Protects against IP theft if project files are exfiltrated from the engineering workstation or version control repository.
* **Encryption password must be in PAM** — lost password means project file is permanently inaccessible.

---
---

# 7 — Certificates

---

## 7.1 Certificate Architecture

M580 uses X.509 certificates for:

| Certificate Type | Purpose | Where Configured |
|----------------|---------|-----------------|
| HTTPS server certificate | Identifies M580 web server; enables TLS | Control Expert → Services → HTTPS |
| IPsec certificates | Device authentication for IPsec tunnel | Control Expert → IPsec configuration |
| OPC UA server certificate | If OPC UA via gateway; server authentication | OPC UA gateway/NOC configuration |
| CA certificates (trust anchors) | Root/Intermediate CA certificates | Configured per service |

## 7.2 HTTPS Certificate

The M580 ships with a factory self-signed certificate for HTTPS. **The factory certificate must be replaced before production deployment.**

**Requirements for production HTTPS certificate:**
* Issued by enterprise PKI (not self-signed in production).
* Subject CN or SAN matches the M580 hostname or IP address.
* Key algorithm: RSA 2048 minimum; RSA 4096 or ECDSA P-256 preferred.
* Validity: 2 years maximum; monitor expiry.
* CA certificate must be in the trust store of management workstations accessing the web server.

**Certificate deployment process:**
1. Generate CSR in Control Expert or via external tool.
2. Submit CSR to enterprise CA.
3. Install issued certificate in Control Expert HTTPS configuration.
4. Download updated configuration to M580.
5. Verify HTTPS connection from management WS.
6. Record expiry date in certificate management system.

## 7.3 IPsec Certificates

When using certificate-based IPsec (preferred over PSK for production WAN connectivity):
* Each M580 requires a device certificate for IKEv2 authentication.
* Certificate issued by enterprise PKI to M580 device identity.
* Remote VPN peer (concentrator, firewall) must trust the M580's CA.
* M580 must trust the remote peer's CA.
* Certificate lifecycle must be managed — expired IPsec certificate causes tunnel failure.

## 7.4 Certificate Expiry Monitoring

Certificate expiry in OT environments causes operational disruption:
* Expired HTTPS certificate → web management inaccessible from modern browsers.
* Expired IPsec certificate → VPN tunnel failure; remote sites lose communication.
* Expired OPC UA certificate (if used) → historian data collection stops.

**Monitor expiry for every certificate:**
* 90 days before expiry: initiate renewal.
* 30 days: escalate if renewal not complete.
* 7 days: emergency escalation.

---
---

# 8 — Firmware

---

## 8.1 Firmware Security Architecture

The M580 firmware security architecture is one of the strongest available in the PLC market:

* **Digital signature:** All M580 firmware is digitally signed by Schneider Electric. The CPU verifies the signature before applying an update — unsigned or tampered firmware is rejected.
* **AES-256 encryption:** Firmware binaries are encrypted — the firmware file cannot be analyzed offline without the decryption key.
* **SHA-256 integrity:** The CPU verifies SHA-256 integrity of firmware at every startup.
* **Secure Boot (gray CPUs):** Hardware-rooted boot chain verification.

These controls make firmware-level attacks significantly harder than on platforms without signed/encrypted firmware.

## 8.2 Firmware Sources and Authenticity

* **Official source:** Schneider Electric's Software Update portal (su.schneider-electric.com or EcoStruxure platform).
* **Verify:** SHA-256 checksum provided by Schneider Electric for each firmware package.
* **Never use firmware from:** third-party sites, USB drives of unknown origin, email attachments, or unofficial repositories.

## 8.3 Firmware Update Process

```
Schneider Electric publishes firmware update
        ↓
Security team reviews: PSIRT advisory? Known CVE? Functional change?
        ↓
Download from official Schneider source; verify SHA-256 checksum
        ↓
Review release notes: compatibility; known issues; dependencies
        ↓
Verify compatibility: Control Expert version; project migration required?
        ↓
Test in staging environment (same hardware model):
  - Firmware installs and CPU starts normally
  - Application loads and executes correctly
  - Modbus TCP, HTTPS, Syslog still function
  - ACL configuration preserved
  - Security configuration preserved
        ↓
Change Management approval (change request, risk assessment, approval)
        ↓
Schedule maintenance window (CPU restart required)
        ↓
Deploy via Control Expert → PLC → Update Firmware
  OR via HTTPS web interface (firmware upload)
        ↓
Verify: CPU online; application running; communications restored
        ↓
Update asset inventory (firmware version updated)
        ↓
Take new backup (updated firmware baseline documented)
```

## 8.4 Firmware Compatibility with Control Expert

M580 firmware versions have compatibility dependencies with EcoStruxure Control Expert versions. Before updating firmware:
* Check the Schneider Electric compatibility matrix (available on Schneider Industry support portal).
* Determine whether a Control Expert upgrade is required alongside the firmware update.
* Assess whether project migration is needed (project file format may change between CE versions).
* Test the complete chain (CE version + firmware version + project) in staging.

## 8.5 Schneider Electric PSIRT

Schneider Electric's Product Security Incident Response Team (PSIRT) publishes security advisories for all Schneider Electric products, including the Modicon M580.

* **Subscribe:** https://www.se.com/us/en/work/support/cybersecurity/security-notifications.jsp
* **Advisory format:** Includes affected products, affected firmware versions, CVE references, CVSS scores, impact description and mitigation measures.
* **Also published via:** CISA ICS-CERT (https://www.cisa.gov/ics-advisories) — often the fastest external notification for critical vulnerabilities.
* **Additional resource:** Schneider Electric's Cybersecurity Center of Excellence resources (https://www.se.com/ww/en/work/solutions/cybersecurity/).

---
---

# 9 — EtherNet/IP

---

## 9.1 Protocol Overview

EtherNet/IP (Ethernet Industrial Protocol) is an ODVA-defined industrial protocol based on the Common Industrial Protocol (CIP) running over standard Ethernet/TCP/IP and UDP/IP. The Modicon M580 supports EtherNet/IP as one of its standard communication options.

**EtherNet/IP on M580:**
* Supported as both EtherNet/IP adapter (server) and scanner (client/master).
* Used for communication with EtherNet/IP-capable drives (Altivar), remote I/O, and third-party devices.
* Explicit messaging (TCP port 44818): configuration, parameter read/write, connected messaging.
* Implicit messaging (UDP port 2222): I/O data exchange (real-time cyclic).
* EPATH (CIP path) addressing for specific objects within the M580.

## 9.2 Security Limitations

EtherNet/IP/CIP (without CIP Security extensions) has limited native security:
* **No authentication:** Any EtherNet/IP scanner on the network can communicate with the M580 as an adapter.
* **No encryption:** Explicit and implicit messages transmitted in cleartext.
* **No integrity protection:** Messages can potentially be replayed or modified.

**CIP Security (EtherNet/IP Security Extensions, ODVA CIP Security v1.0+):**
ODVA published CIP Security specifications that add TLS authentication and encryption to CIP explicit messaging. M580 CIP Security support depends on the firmware version and CPU model — consult Schneider Electric compatibility documentation for current status.

## 9.3 Mitigations for EtherNet/IP on M580

When EtherNet/IP is used without CIP Security:
* **ACL:** Configure M580 ACL to allow EtherNet/IP (port 44818 TCP; port 2222 UDP) from authorized source IPs only.
* **Firewall:** Restrict EtherNet/IP ports at the network perimeter — no EtherNet/IP traffic from engineering zone or IT network unless explicitly required.
* **Disable if not required:** If the M580 deployment only uses Modbus TCP for SCADA communication, disable EtherNet/IP in Control Expert to eliminate this attack surface.
* **Monitor:** OT IDS tools (Claroty, Nozomi, Dragos) can parse EtherNet/IP/CIP and detect unauthorized explicit messages or anomalous I/O data patterns.

## 9.4 EtherNet/IP vs. Modbus TCP Choice

In M580 deployments, both EtherNet/IP and Modbus TCP may be available for SCADA communication:

| Factor | EtherNet/IP | Modbus TCP |
|--------|------------|-----------|
| Protocol origin | ODVA (Allen-Bradley ecosystem) | Modicon (Schneider ecosystem) |
| SCADA support | Wide (Wonderware, GE iFIX, Ignition) | Universal |
| Security (native) | Low (without CIP Security) | Low |
| Overhead | Higher (CIP encapsulation) | Lower |
| M580 native | Supported | Native (M580 is Modicon) |
| Recommendation | Use where EtherNet/IP-native devices require it | Preferred for SCADA on M580 |

---
---

# 10 — Modbus TCP

---

## 10.1 Protocol Overview

Modbus TCP is the native protocol of the Modicon platform (Modbus was developed by Modicon in 1979). The M580 includes a Modbus TCP server as a core feature, providing access to:
* Input coils (Function Code 2 — read discrete inputs).
* Output coils (FC 1, 5, 15 — read/write discrete outputs).
* Input registers (FC 4 — read input registers).
* Holding registers (FC 3, 6, 16 — read/write holding registers).
* Extended memory (%MW, %M, %I, %Q) via Modbus address mapping.

Modbus TCP operates on TCP port 502 by default.

## 10.2 UMAS Protocol (Control Expert Engineering)

Within the Modbus TCP framework, Schneider Electric's proprietary UMAS (Unity Messaging Application Services) protocol is used by EcoStruxure Control Expert for:
* Application download and upload.
* Online modification (application change while running).
* Real-time monitoring (watch window, forcing I/O).
* Diagnostics.

UMAS uses Modbus TCP port 502 with specific Modbus function codes and a proprietary sub-protocol. **UMAS must be treated as the highest-risk protocol on the M580** — unrestricted UMAS access is equivalent to unrestricted programming access.

**Critical ACL rule:** UMAS (TCP 502 from Control Expert) must be restricted to the engineering workstation IP address. SCADA systems and historians should not have UMAS capability — they access only standard Modbus registers.

## 10.3 Security Limitations

Modbus TCP has no native security features:
* **No authentication:** Any Modbus TCP client can read or write any register without credentials.
* **No encryption:** All data in cleartext.
* **No integrity:** Messages can be replayed or modified in transit.
* **Write access:** Modbus FC 6 and FC 16 allow writing to holding registers — direct manipulation of PLC output values and setpoints.
* **UMAS access:** Any host with TCP 502 access can potentially use UMAS to download arbitrary applications if no application write password is set.

## 10.4 Mitigations

| Mitigation | Implementation | Effect |
|-----------|---------------|--------|
| ACL — restrict Modbus TCP | Allow TCP 502 from SCADA/historian IPs only; deny all others | Prevents unauthorized Modbus access |
| ACL — restrict UMAS | Allow TCP 502 from Engineering WS IP only (separate from SCADA) | Prevents unauthorized programming access |
| Application write password | Set in Control Expert; store in PAM | Prevents application download even with Modbus access |
| Network segmentation | OT VLAN; no IT systems with Modbus TCP access | Reduces attacker population |
| OT IDS monitoring | Claroty, Nozomi, Dragos — detect FC 6/16 writes from unexpected sources | Detection of unauthorized register writes |
| Migrate to OPC UA | For SCADA/historian connectivity where possible | Adds authentication and encryption |

## 10.5 Modbus TCP Address Space Security

The M580 exposes its entire address space (within the configured limits) via Modbus TCP. Consider:
* **Limit exposed registers:** Configure the maximum Modbus address accessible via Modbus TCP in the M580 configuration — expose only what SCADA actually needs.
* **Separate read and write access:** Where possible, configure SCADA to use read-only Modbus connections; write access only from engineering WS.
* **Monitor write operations:** OT IDS can detect FC 6/16 write operations from unexpected sources or outside maintenance windows.

---
---

# 11 — Ethernet RIO

---

## 11.1 Ethernet RIO Architecture

Ethernet RIO (Remote I/O) is the Modicon M580's native distributed I/O technology. It uses standard 100 Mbps Ethernet to connect X80 I/O drops to the M580 CPU over distances of up to 100 m per segment (copper) or longer with fiber.

**Key characteristics:**
* Deterministic real-time I/O exchange over Ethernet.
* S-Ring topology provides network redundancy.
* Remote I/O drops contain Ethernet Adapter modules (BM•CRA) plus X80 I/O modules.
* The M580 CPU scans Ethernet RIO drops cyclically — similar to PROFINET IO Controller-Device model.
* Communication is proprietary Schneider Electric protocol — not Modbus TCP or EtherNet/IP.

## 11.2 S-Ring Topology and Media Redundancy

S-Ring is Modicon's Ethernet RIO ring topology providing network redundancy:
* Ethernet RIO drops connected in a ring.
* S-Ring recovery time: < 1 ms (transparent redundancy at Layer 2).
* Requires ring master (typically the BMENOC module or CPU) and ring members (CRA adapter modules).
* If a single cable break occurs, the ring reorganizes and I/O exchange continues.

**Security implications for S-Ring:**
* S-Ring traffic is on the field zone network — physically separate from supervisory and IT networks.
* Any device connected to the S-Ring Ethernet can potentially intercept or inject RIO traffic.
* Physical access control to RIO ring infrastructure (cables, junction boxes, terminal boxes) is the primary security control.
* Monitor for unexpected devices appearing on the S-Ring network via OT IDS or SINEC-equivalent for Schneider platforms (EcoStruxure Security Expert).

## 11.3 Security Considerations for Ethernet RIO

| Area | Consideration | Control |
|------|--------------|---------|
| Physical access | RIO drops often in distributed locations | Locked sub-panels; physical access control; tamper seals |
| Network access | RIO protocol is not firewallable in the traditional sense | Dedicated RIO VLAN; physical separation from other OT traffic |
| Module integrity | I/O modules can be replaced physically without software protection | Physical locks; asset inventory; serial number tracking |
| RIO firmware | BM•CRA adapter modules have firmware | Firmware management extends to adapter modules |
| Unauthorized device | Rogue device on RIO network can receive/inject RIO frames | Physical cable protection; dedicated RIO network |

## 11.4 Ethernet RIO vs. DIO (Distributed I/O over standard LAN)

The M580 supports both:
* **Ethernet RIO** (dedicated, real-time, ring topology) — deterministic; separate network from supervisory traffic.
* **DIO (Distributed I/O)** over standard LAN — less common; shares Ethernet with other traffic.

For security, **Ethernet RIO on a dedicated ring is preferred** — it provides better isolation, lower latency, and hardware redundancy. DIO over shared LAN introduces additional attack surface because I/O data is on the same network as supervisory and potentially engineering traffic.

---
---

# 12 — OPC UA

---

## 12.1 OPC UA on M580 — Architecture

The Modicon M580 CPU does not include a native built-in OPC UA server in the same way that the Siemens S7-1500 does. OPC UA connectivity for M580 is typically provided by:

1. **EcoStruxure OPC UA Module (BMENUA0100):** A dedicated hardware module that inserts into the M580 backplane and provides an OPC UA server exposing M580 data. Available for M580 "gray" CPUs.
2. **AVEVA (Wonderware) InTouch / Historian OPC UA integration:** SCADA platforms connecting to M580 via their OPC DA/UA servers using Modbus TCP as the underlying connection to the PLC.
3. **Third-party OPC UA gateways:** Standalone gateways (e.g., Kepware KEPServerEX, Matrikon OPC Server) that connect to M580 via Modbus TCP and expose data via OPC UA to SCADA/historian.

## 12.2 BMENUA0100 OPC UA Module

The BMENUA0100 provides native OPC UA server capability within the M580 backplane:
* Exposes M580 process variables as OPC UA nodes.
* Supports OPC UA security modes: None, Sign, Sign and Encrypt.
* Certificate-based authentication for OPC UA clients.
* Supports OPC UA subscriptions and data change notifications.
* Configuration via dedicated web interface or via Control Expert.

## 12.3 OPC UA Security Configuration

| Security Mode | Description | Production Use |
|--------------|-------------|----------------|
| None | No authentication or encryption | **Never in production** |
| Sign | Message integrity only; no encryption | Not recommended in production |
| Sign and Encrypt | Integrity + TLS encryption | **Required in production** |

**Configure Sign and Encrypt as the only permitted security mode** in production.

**Authentication:**
* **Anonymous:** Disable in production.
* **Username/password:** Service account; stored in PAM.
* **Certificate-based:** Preferred for automated connections (SCADA, historian).

## 12.4 OPC UA Certificate Management for BMENUA0100

Certificate requirements for OPC UA:
* OPC UA server certificate: issued by enterprise PKI; installed on BMENUA0100.
* OPC UA client certificates: from SCADA/historian; added to trusted client list on BMENUA0100.
* Monitor certificate expiry — expired OPC UA server certificate causes historian data collection failure.

---
---

# 13 — IPsec

---

## 13.1 IPsec Overview

The Modicon M580 includes an integrated IPsec engine supporting tunnel-mode IPsec for securing WAN communications. This is a significant security capability not commonly found in PLC platforms.

**Typical IPsec use cases for M580:**
* Remote site communication — M580 at a remote pump station or substation communicating to a central SCADA over a WAN or telemetry link.
* MPLS VPN integration — M580 participating in an enterprise VPN where Ethernet RIO or Modbus TCP traffic traverses WAN links.
* Secure maintenance access — encrypted tunnel for remote engineering access over WAN.

**IPsec is not typically required** when the M580 communicates only within a well-segmented OT LAN. Its primary value is for WAN-connected remote deployments where network-level encryption protects traffic traversing untrusted network infrastructure.

## 13.2 IPsec Configuration

M580 supports IPsec in tunnel mode with IKEv1 and IKEv2:

| Parameter | Recommended Configuration |
|-----------|--------------------------|
| IKE version | IKEv2 (preferred over IKEv1) |
| Authentication | Certificate-based (preferred over PSK) |
| Encryption | AES-256-GCM or AES-256-CBC |
| Integrity | SHA-256 or SHA-384 |
| DH group | Group 14 (2048-bit) minimum; Group 20 (384-bit ECC) preferred |
| PSK (if used) | Minimum 32 characters; random; unique per M580 instance; stored in PAM |
| Certificate | Device certificate from enterprise PKI |

**Avoid:**
* IKEv1 aggressive mode.
* DES, 3DES, RC4 encryption.
* MD5 integrity.
* Diffie-Hellman groups < 14.
* Shared PSK across multiple devices.

## 13.3 IPsec and Network Architecture

When using IPsec on M580:
* IPsec tunnel terminates at the M580 CPU — traffic within the OT LAN is still plaintext.
* IPsec protects only the WAN segment between the M580 and the VPN concentrator/firewall.
* The VPN concentrator must be in a secured zone (preferably the Industrial DMZ or dedicated VPN segment).
* IPsec does not replace network segmentation on the OT LAN side.

---
---

# 14 — Web Server

---

## 14.1 M580 Integrated Web Server

The M580 includes an embedded web server accessible via HTTPS (TCP 443) or HTTP (TCP 80). The web server provides:
* CPU status (RUN/STOP, error, operating mode).
* I/O configuration overview.
* Modbus diagnostic information.
* Variable monitoring (real-time tag values).
* Firmware upload (some models/configurations).
* ACL configuration.
* IPsec configuration.
* Certificate management.
* User account management.
* Syslog configuration.
* SNMP configuration.

## 14.2 Hardening the Web Server

| Configuration | Requirement |
|--------------|-------------|
| HTTPS | **Enable** |
| HTTP | **Disable** — no exceptions in production |
| Default certificate | **Replace** with PKI-issued certificate |
| Default credentials | **Change** — default password must not remain |
| Authentication | **Enable** — no anonymous access to management functions |
| ACL | Restrict TCP 443 to engineering WS / jump server IP only |
| Session timeout | Enable automatic session expiry (e.g., 15 minutes idle) |
| TLS version | Minimum TLS 1.2; TLS 1.3 where supported by firmware |

## 14.3 When to Disable the Web Server

If the web server is not required for operational management:
* **Disable it.** An unused but enabled web server is unnecessary attack surface.
* Disabling: Control Expert → Services → Web Server → Disable.
* Note: The web server may be the only management interface for certain configuration parameters (ACL, IPsec, certificates) — verify that alternative configuration paths exist before disabling.

## 14.4 Web Server vs. Control Expert for Configuration

Most M580 security configuration (ACL, HTTPS certificates, Syslog, SNMP, IPsec) can be performed either via:
* **Control Expert (engineering tool):** Preferred — configuration is part of the project and version-controlled.
* **Web Server:** Useful for runtime adjustments or when Control Expert is unavailable — but changes made via web server may not be reflected in the project file (create configuration drift).

**Best practice:** Configure all security settings via Control Expert and include them in the project. Use the web server only for diagnostic/monitoring access, not for permanent configuration changes.

---
---

# 15 — Syslog

---

## 15.1 Syslog Overview

The M580 supports forwarding security and operational events to Syslog servers. This enables integration with enterprise SIEM platforms and centralized OT monitoring infrastructure.

**Syslog transport options:**
* UDP Syslog (port 514): Available; no transport security.
* TCP Syslog: Available on some firmware versions.
* Syslog/TLS (RFC 5425, port 6514): Available on current firmware; recommended.

## 15.2 Transport Security

| Transport | Security | Recommendation |
|-----------|---------|---------------|
| UDP Syslog (port 514) | None — spoofable; no delivery guarantee | **Not recommended** for security events |
| TCP Syslog (port 514) | No encryption; connection-oriented | Acceptable in isolated, trusted OT network only |
| Syslog/TLS (RFC 5425) | TLS encryption; certificate auth | **Required** for production security event forwarding |

Configure Syslog/TLS for all production M580 deployments. The M580 acts as a TLS client connecting to the Syslog server — configure the Syslog server's certificate (or CA) in the M580 trusted certificate store.

## 15.3 Security-Relevant Events Forwarded via Syslog

| Event Category | Examples |
|---------------|---------|
| Authentication events | Successful login (web server), failed login |
| Engineering access | Control Expert connection; application download |
| Application changes | Online change; application download |
| Firmware events | Firmware update started; firmware update completed |
| ACL events | ACL deny (blocked connection attempt) |
| CPU operational events | Mode change (RUN→STOP); restart |
| Configuration changes | ACL modification; Syslog configuration change; password change |
| Diagnostic events | Hardware fault; communication error |
| IPsec events | Tunnel established; tunnel failed |
| Certificate events | Certificate expiry warning |

**ACL deny logging is particularly valuable** — it reveals which unauthorized sources are attempting to access the M580. A pattern of repeated ACL denies from an unexpected IP may indicate reconnaissance or attempted attack.

## 15.4 SIEM Integration

* **NTP synchronization is mandatory** — Syslog timestamps are only reliable with synchronized time. Configure NTP on M580 before enabling Syslog.
* **Syslog message format:** M580 Syslog follows RFC 5424 / CEF. Verify SIEM has appropriate parser.
* **Alert rules:** Define alerts for: failed authentication (repeated = brute force), application download, ACL deny pattern (reconnaissance), mode change (unexpected STOP), firmware update.
* **Retention:** Minimum 1 year; 3 years for higher-obligation environments.

---
---

# 16 — SNMP

---

## 16.1 SNMP Overview

M580 supports SNMP for monitoring by network management systems (NMS) and OT monitoring platforms.

**SNMP on M580 provides:**
* Device identification (sysName, sysDescr, sysLocation, sysUpTime).
* Interface statistics (ifTable — packet counts, link status, errors).
* CPU status and performance data.
* Modicon-specific MIB entries (CPU operating state, task status).

## 16.2 SNMP Version Security

| Version | Authentication | Encryption | Recommendation |
|---------|---------------|-----------|----------------|
| SNMPv1 | Community string (cleartext) | None | **Disable** |
| SNMPv2c | Community string (cleartext) | None | **Disable** |
| SNMPv3 (noAuthNoPriv) | None | None | Do not use |
| SNMPv3 (authNoPriv) | HMAC-SHA | None | Acceptable if encryption not possible |
| SNMPv3 (authPriv) | HMAC-SHA-256 | AES-128 or AES-256 | **Required** |

**Disable SNMPv1 and SNMPv2c.** Factory default community strings ("public", "private") are well-known and transmitted in cleartext — they enable device fingerprinting, configuration enumeration, and potentially SNMP write access.

## 16.3 SNMPv3 Configuration for M580

* Authentication protocol: SHA-256 (preferred) or SHA-1 (minimum).
* Privacy protocol: AES-256 (preferred) or AES-128.
* Unique username/authKey/privKey per NMS instance — do not share credentials.
* Read-only access for monitoring — no SNMP Write capability unless explicitly required.
* ACL: Restrict UDP 161 to NMS IP only.

## 16.4 If SNMP Is Not Required

* **Disable SNMP entirely** in Control Expert → Services → SNMP → Disable.
* This eliminates SNMP as a device fingerprinting and attack vector.
* M580 can still be monitored via the web server and Syslog without SNMP.

---
---

# 17 — Diagnostics

---

## 17.1 M580 Diagnostic Capabilities

The M580 provides comprehensive diagnostic capabilities accessible through Control Expert (online), the embedded web server, and system function blocks within the application.

## 17.2 Diagnostic Buffer and Event Log

The M580 maintains an internal diagnostic event log recording:
* CPU operational events (start, stop, restart, watchdog, mode change).
* I/O module faults (X80 I/O module errors, Ethernet RIO drops).
* Communication faults (Ethernet link loss, RIO communication failure).
* Configuration faults (project download errors, parameter faults).
* Security events (authentication failures, ACL denies, application write attempts) — on supported firmware.
* User-defined events (application-level diagnostic entries).

**Access methods:**
* Control Expert → PLC → Diagnostic → Event Log (online).
* Web Server → Diagnostics → Event Log.
* System function blocks (READ_EVT, PUT_CLCK) in application.

## 17.3 LED Indicators

M580 CPUs have front-panel LEDs for rapid status assessment:

| LED | Color/State | Meaning |
|-----|------------|---------|
| RUN | Green (steady) | CPU executing application normally |
| RUN | Flashing green | CPU in HALT or startup |
| ERR | Red (steady) | Hardware or software fault; check event log |
| ERR | Flashing red | Application-level error or watchdog event |
| I/O | Yellow | Local I/O bus active |
| ETH1 / ETH2 | Green | Ethernet link active |
| MEMORY | Yellow/Green | SD card activity |

**Security note:** Unexpected ERR LED during production requires investigation of the event log. A CPU STOP during production without operator action is an operational incident — check for unauthorized mode change in Syslog and event log.

## 17.4 CPU Diagnostic Function Blocks

Within the Control Expert application, diagnostic system function blocks provide programmatic access to:
* `READ_RTC` — read real-time clock.
* `READ_EVT` — read event buffer.
* `SEND_MSG` — diagnostic messaging.
* `PUT_CLCK` / `GET_CLCK` — set/read CPU clock.
* Various `DIAG_*` function blocks for I/O diagnostics.

Diagnostic data can be exposed via Modbus TCP registers or OPC UA nodes to SCADA/historian for operational monitoring.

## 17.5 Remote Diagnostics via Control Expert Online

Online diagnostics in Control Expert allow viewing the application state, variable values, force table, and event log without local physical access.

**Security controls for remote diagnostics:**
* Requires Control Expert connection → application password authentication.
* Restrict UMAS (Control Expert protocol) to engineering WS via ACL.
* Log Control Expert online sessions (visible in Syslog if Control Expert connection events are logged).
* Session should be over a PAM-mediated connection for critical production systems.

---
---

# 18 — Monitoring

---

## 18.1 Monitoring Philosophy

Monitoring for M580 must address:
1. **Operational monitoring:** CPU running? Ethernet RIO dropping? I/O faults?
2. **Security monitoring:** Unauthorized Modbus writes? ACL denies? Unexpected Control Expert connections? Configuration changes?

Both are required. Most M580 deployments have operational monitoring (SCADA alarms, historian trends) but weak or absent security monitoring.

## 18.2 EcoStruxure Security Expert

Schneider Electric's EcoStruxure Security Expert is a monitoring platform designed for Schneider Electric industrial environments. For M580 fleets, it provides:
* **Asset discovery and inventory:** Identifies M580 CPUs and X80 I/O modules.
* **Firmware version monitoring:** Compares discovered firmware against approved baselines.
* **Security event aggregation:** Collects Security Event Logs from M580 systems.
* **ACL monitoring:** Tracks ACL configuration across the fleet.
* **Vulnerability assessment:** Cross-references device versions against Schneider PSIRT advisories.

## 18.3 OT IDS / NDR Integration

Passive OT network monitoring tools (Claroty, Nozomi Networks, Dragos, Microsoft Defender for IoT) provide protocol-aware monitoring of M580 communication:

| Monitoring Capability | What It Detects |
|----------------------|----------------|
| Modbus TCP monitoring | FC 6/16 write operations from unexpected sources; UMAS from non-engineering WS |
| EtherNet/IP monitoring | Unexpected CIP explicit message senders |
| Engineering protocol monitoring | Unexpected Control Expert connections; off-hours downloads |
| ACL deny monitoring | Via Syslog integration with SIEM |
| Application download detection | Visible in Modbus protocol as specific UMAS sequences |
| Anomaly detection | Unusual communication patterns; new devices on OT network |
| Behavioral baseline | Alert when M580 communication deviates from baseline |

## 18.4 Monitoring Checklist

Minimum monitoring for each M580 production system:

- [ ] Syslog/TLS → SIEM: ACL deny events, authentication events, application download events, mode changes
- [ ] NTP synchronized: Syslog timestamps reliable
- [ ] OT IDS passively monitoring OT network segment (Modbus TCP, EtherNet/IP)
- [ ] Certificate expiry monitored (90/30/7 day alerts)
- [ ] Firmware version tracked in asset inventory; alert on unauthorized change
- [ ] Offline vs. online comparison performed periodically (Control Expert project vs. deployed application)
- [ ] SCADA alarms for unexpected CPU STOP or ERR events

---
---

# 19 — Logging

---

## 19.1 Audit Trail Requirements

Every security-relevant action on or affecting the M580 must be logged to support:
* Incident response and forensic analysis.
* Regulatory compliance (NIS2 Art. 21; IEC 62443 SR 6.1).
* Change management verification.
* Access review support.

## 19.2 Event Categories and Priority

| Event Category | Source | Priority |
|---------------|--------|----------|
| Authentication — success (web server) | Web server log | High |
| Authentication — failure (web server) | Web server log | **Critical** (repeated = brute force) |
| Application download | Security Event Log / Syslog | **Critical** |
| Online modification (online change) | Security Event Log / Syslog | **Critical** |
| Firmware update | Security Event Log / Syslog | High |
| CPU mode change (RUN/STOP) | Security Event Log / Syslog / SCADA | High |
| ACL deny (blocked connection) | Syslog | High |
| Configuration change (ACL, IPsec, etc.) | Security Event Log / Syslog | High |
| IPsec tunnel events | Syslog | Medium |
| Certificate events (expiry, install) | Syslog | High |
| Ethernet RIO communication fault | Event log / SCADA | Medium (operational) |
| Hardware fault | Event log / SCADA | Medium (operational) |

## 19.3 Log Retention

* **Minimum retention:** 1 year for security events.
* **Recommended retention:** 3 years for higher-obligation environments (NIS2 / Decree 409).
* M580 internal event log is limited in capacity — **Syslog/TLS to SIEM is mandatory** for long-term retention.
* SIEM storage must be sized for expected Syslog volume from all M580 systems.

## 19.4 Log Integrity Controls

* Use Syslog/TLS for transport security — prevents spoofing and ensures delivery confirmation.
* Monitor for Syslog session gaps (loss of events may indicate tampering or connectivity failure).
* WORM storage for archived security logs where regulatory requirements apply.
* NTP synchronization is mandatory — without it, log timestamps are unreliable for forensics.

---
---

# 20 — Backup and Recovery

---

## 20.1 Backup Philosophy

Recovery is the objective — not backup creation. A backup that cannot support successful, timely restoration of a trusted operational state is insufficient.

For M580, "trusted operational state" means:
* Correct firmware version running.
* Approved application executing correctly.
* Correct hardware configuration.
* Valid security configuration (passwords, ACL, certificates).
* Communications operational (Modbus TCP, Ethernet RIO, SCADA).

## 20.2 Backup Scope

| Component | Backup Method | Frequency | Storage |
|-----------|--------------|-----------|---------|
| Control Expert project (.stu/.zef) | Version control (Git) + archive export | After every approved change | Repository + offline copy |
| Deployed application (uploaded from CPU) | Control Expert → PLC → Upload to PC → Save | After every production download | Backup repository |
| Hardware configuration | Part of Control Expert project | After every change | Repository |
| ACL configuration | Part of Control Expert project | After every change | Repository |
| IPsec configuration | Part of Control Expert project | After every change | Repository |
| HTTPS/OPC UA certificates | Export from web server / BMENUA0100 | After issuance/renewal | Encrypted backup |
| SD card contents | Spare SD card with identical content | After every application change | Offline, secured |
| Firmware version record | Asset inventory update | After firmware change | Asset management |

## 20.3 SD Card Backup

The M580 uses an SD card as non-volatile storage for the application and configuration. The SD card provides a hardware backup mechanism:
* If the CPU is replaced with a new unit, the SD card from the old unit can be installed to restore the application.
* A spare SD card with the current approved application (written periodically) serves as a hardware recovery option.

**Security considerations for SD card:**
* The SD card contains the complete application — physical access to the card = IP access to the application.
* Protect physically within the locked cabinet.
* Application read password still protects source code on the card (readable via SD card reader, but password required to open in Control Expert).
* When decommissioning a CPU, securely erase or physically destroy the SD card.

## 20.4 Recovery Scenarios

### Scenario A: CPU Hardware Failure (Non-Cyber)

**Recovery procedure:**
1. Obtain replacement M580 CPU (same model number; identical hardware revision preferred).
2. Verify firmware version on replacement unit — update to match baseline if needed.
3. Install spare SD card (with current approved application) into replacement CPU.
4. OR: Download from Control Expert project (version-controlled; verified release tag).
5. Restore ACL, IPsec, certificate configuration.
6. Verify: CPU running; Ethernet RIO communicating; SCADA connectivity restored.
7. Update asset inventory (new serial number).

### Scenario B: Application Corruption or Unauthorized Modification (Suspected)

**Recovery procedure:**
1. **Do not immediately overwrite.** Upload current (potentially compromised) application from CPU — preserve as evidence.
2. Compare uploaded application against last approved version (Control Expert project comparison tool).
3. If differences found: initiate incident response; investigate before restoring.
4. Restore approved application from version control (verified release tag).
5. Download via Control Expert with write password authentication.
6. Verify deployed application matches approved project (offline/online comparison).
7. Document incident in change record.

### Scenario C: Cyber Incident (Ransomware, Unauthorized Access)

**Recovery procedure:**
1. **Isolate:** Disconnect affected engineering workstations from OT network.
2. **Assess:** Which M580 systems were accessible? Which had applications downloaded during the incident window?
3. **Application integrity:** Upload application from potentially affected M580 CPUs; compare to approved version.
4. **Do not trust potentially compromised applications.** Restore from known-good backup.
5. **Rebuild engineering WS** from trusted image before any PLC engineering.
6. **Change all passwords:** Application write/read passwords; web server accounts; rotate and document in PAM.
7. **ACL review:** Verify ACL configuration has not been modified.
8. **NÚKIB notification** if organization is regulated (Czech-Cybersecurity-Act.md).
9. Resume production only after full verification and management sign-off.

### Scenario D: SD Card Failure

**Recovery procedure:**
1. Obtain replacement SD card (same capacity and type as specified in M580 documentation).
2. Format replacement card per Schneider Electric SD card formatting procedure.
3. Download approved application from Control Expert to replacement card via CPU.
4. Verify application running correctly on new SD card.

## 20.5 RTO and RPO

Define and document for each M580 deployment:

| Scenario | Suggested RTO | Suggested RPO |
|----------|--------------|--------------|
| CPU hardware failure | 4–8 hours | 0 (version control is continuous) |
| Application corruption | 2–4 hours | Last approved release |
| Cyber incident | 24–72 hours (investigation required) | Last approved release |
| SD card failure | 2–4 hours | Last application download |

---
---

# 21 — Patch Management

---

## 21.1 Patch Management Principles

M580 firmware patch management requires coordination between security team, automation engineering and production operations:
* **CPU restart required** for firmware update — production downtime must be planned.
* **Control Expert compatibility** — firmware versions may have dependencies on Control Expert versions.
* **Safety application consideration** — for M580 Safety CPUs, safety application re-validation may be required after firmware update (consult IEC 61511 requirements).

## 21.2 Vulnerability Intelligence Sources

| Source | Content | Access |
|--------|---------|--------|
| Schneider Electric PSIRT | Official Schneider advisories; all products including M580 | https://www.se.com/ww/en/work/support/cybersecurity/security-notifications.jsp |
| CISA ICS-CERT | ICS vendor advisories; includes Schneider | https://www.cisa.gov/ics-advisories |
| NVD (National Vulnerability Database) | CVE details; search "Modicon M580" | https://nvd.nist.gov |
| NÚKIB varování | Czech-market relevant advisories | https://nukib.cz |
| Schneider SE Industry support | Firmware releases; compatibility matrices | https://www.se.com/us/en/work/support/ |

Subscribe to Schneider Electric PSIRT notifications — this is the primary source for M580-specific security advisories.

## 21.3 Patch Assessment Process

For each Schneider Electric PSIRT advisory affecting M580:

```
Advisory received (PSIRT / CISA ICS-CERT)
        ↓
Determine: Does it affect our firmware version?
        ↓
If yes: Assess CVSS score and attack vector
  - Network-accessible vulnerability (AV:N): higher priority
  - Local/physical access only: lower priority
        ↓
Determine: Is our architecture exposed?
  - Is the vulnerable service reachable from attacker's position?
  - Does ACL or network segmentation compensate?
        ↓
Assign priority:
  - Critical (CVSS ≥ 9.0, network accessible): patch within 30 days
  - High (CVSS 7.0–8.9): patch within 90 days
  - Medium: patch at next planned maintenance window
  - Low: patch at next annual firmware review
        ↓
If immediate patch not possible: implement compensating controls;
  document in risk register; formal risk acceptance with management sign-off
```

## 21.4 Compensating Controls for Unpatched Systems

* Document vulnerability in risk register (CVE, CVSS, affected firmware version, planned patch date).
* Implement ACL restriction for the vulnerable service/port.
* Enhance Syslog monitoring for exploitation indicators.
* OT IDS rule for known exploitation patterns (if available).
* Management sign-off on residual risk acceptance.
* Review monthly until patched.

---
---

# 22 — High Availability

---

## 22.1 M580 Hot Standby Architecture

The Modicon M580 Hot Standby (BMEH58•040) provides hardware redundancy with a primary and standby CPU:

**How Hot Standby works:**
* Two identical M580 Hot Standby CPUs connected by a synchronization link (typically fiber or Ethernet).
* **Primary** CPU is ACTIVE — executing the application and controlling I/O.
* **Standby** CPU is in hot standby — synchronized with the primary; ready to take control.
* On primary CPU failure (hardware fault, application error, communication loss), the standby becomes ACTIVE automatically.
* Switchover time: typically < 1 scan cycle (deterministic, application-dependent).

**Hardware requirements:**
* Both CPUs must be the same model (BMEH58•040, BMEH584040, BMEH586040).
* Both CPUs must run the same firmware version.
* Synchronization link (BMXUSB018 USB cable for legacy; Ethernet for current generation).
* Redundant power supplies recommended.

## 22.2 Security Implications for Hot Standby

| Area | Consideration | Control |
|------|--------------|---------|
| Password synchronization | Application write password must be identical on both CPUs | Set same password; included in Control Expert project |
| ACL synchronization | ACL configuration must be identical on both CPUs | Include ACL in Control Expert project; download to both |
| Certificate synchronization | HTTPS and IPsec certificates on both CPUs must match | Manage as paired deployment; same certificate on both |
| Firmware updates | Both CPUs must run same firmware | Update one at a time using Schneider Hot Standby update procedure |
| Asset inventory | Both CPUs have separate serial numbers | Document both in asset inventory as a paired unit |
| Syslog from both | Primary and standby both generate Syslog events | Configure Syslog on both; SIEM must handle events from both IPs |
| Engineering access | During switchover, engineering connection may need to reconnect to new primary IP | Document expected behavior in engineering procedures |

**Firmware updates in Hot Standby:**
Schneider Electric provides a specific Hot Standby firmware update procedure that maintains availability by updating one CPU while the other remains active:
1. Update standby CPU firmware.
2. Initiate manual switchover (standby becomes primary).
3. Update (now standby) CPU firmware.
4. System returns to normal redundant operation.
Do not update both CPUs simultaneously — this removes all redundancy.

## 22.3 Ethernet RIO Redundancy

In Hot Standby configurations, Ethernet RIO drops are connected to both the primary and standby CPU via redundant network paths:
* Each remote I/O drop has two network connections — one to each CPU.
* S-Ring provides additional ring-level redundancy within each path.
* On CPU switchover, the I/O drops switch communication to the new primary CPU.
* Total I/O communication interruption during switchover: typically < 1 scan cycle.

---
---

# 23 — X80 I/O and Ethernet RIO

---

## 23.1 X80 I/O System Overview

X80 I/O is Schneider Electric's modular I/O system for the M580 platform. It replaces the older Quantum I/O and Premium I/O module families.

**X80 I/O can be deployed in two ways:**
* **Local rack (backplane I/O):** X80 I/O modules in the same rack as the M580 CPU, connected via the Ethernet backplane.
* **Remote rack (Ethernet RIO):** X80 I/O modules in a remote rack, connected to the M580 via dedicated Ethernet RIO (see Section 11).

**X80 module categories:**

| Category | Examples | Description |
|----------|---------|-------------|
| Digital Input | BMX DDI 1602, BMXDDI3202K | Discrete sensor inputs |
| Digital Output | BMX DDO 1602, BMXDDO3202K | Discrete actuator outputs |
| Analog Input | BMX AMI 0410, BMXAMI0810 | Analog measurement (voltage, current, temperature) |
| Analog Output | BMX AMO 0210 | Analog control outputs |
| Counter/Frequency | BMXEHC0200 | High-speed counting, encoder input |
| Communication | BMX NOM 0200 (serial) | Serial interface for legacy devices |

## 23.2 Security Considerations for X80 I/O

| Area | Consideration | Control |
|------|--------------|---------|
| Physical access | X80 I/O modules can be replaced without software authentication | Physical cabinet locks; asset inventory; module tamper seals |
| Firmware | Some X80 modules (especially communication modules) have firmware | Firmware management extends to I/O module firmware |
| Power over Ethernet | Some newer X80 modules support PoE — introduces network path | Assess network segmentation implications |
| Module diagnostics | X80 modules report diagnostics to CPU and Syslog | Monitor for unexpected module removal/insertion events |

## 23.3 Quantum I/O Migration to X80

Many M580 deployments involve migration from Quantum I/O to X80 I/O. Security considerations for migration:
* Quantum I/O modules in an M580 rack require a Quantum/Premium backplane adapter — assess whether this introduces backward compatibility that reduces security posture.
* Where possible, replace Quantum I/O with native X80 modules during M580 migration to benefit from current X80 security improvements.
* Document all migrated modules in the asset inventory.

---
---

# 24 — NOC and Communication Modules

---

## 24.1 Network Option Cards (NOC)

The Modicon M580 supports Network Option Cards (NOC) inserted into the M580 backplane to provide additional communication interfaces.

**Available NOC modules:**

| Module | Interface | Protocol | Use Case |
|--------|-----------|---------|---------|
| BMENOC0301 | Gigabit Ethernet (1x) | PROFIBUS DP master (via PC-based engineering) | PROFIBUS DPV1 field devices on M580 |
| BMENOC0311 | Gigabit Ethernet (1x) | EtherNet/IP Scanner, OPC UA | EtherNet/IP I/O scanning; OPC UA connectivity |
| BMENUA0100 | Ethernet | OPC UA Server | Native OPC UA server for M580 |
| BMXNOC0401 | 4x Ethernet | Modbus TCP, EtherNet/IP | Quad-port Ethernet for complex topologies |
| BMXNRP0200 / 0201 | Fiber/copper | Redundant Ethernet RIO | Ethernet RIO with fiber option |

## 24.2 Security Considerations for NOC Modules

Each NOC module adds additional network interfaces and protocol capabilities — each also adds attack surface.

| Consideration | Control |
|--------------|---------|
| Each NOC has its own IP address | ACL must be configured on each NOC interface separately |
| Each NOC may support different protocol capabilities | Disable unused protocols per NOC module |
| BMENOC0311 EtherNet/IP Scanner | ACL on EtherNet/IP ports; limit to required device IPs |
| BMENUA0100 OPC UA | Configure Sign and Encrypt; certificate-based client auth |
| Multiple Ethernet ports | Each port must be in the correct zone; routing between ports must be assessed |

## 24.3 Serial Communication Modules (BMX NOM)

The BMX NOM 0200 provides RS-232/RS-485 serial communication:
* Used for legacy field devices (drives, analyzers, smart meters) supporting Modbus RTU, ASCII, or proprietary serial protocols.
* Serial protocols have no authentication or encryption.
* Physical access to serial cable = potential eavesdropping or injection.

**Controls:**
* Physical cable protection; locked terminal boxes.
* Limit UMAS/Modbus access to the COM port configuration to the engineering WS.
* Document all serial-connected devices in the asset inventory.

## 24.4 PROFIBUS DP via BMENOC0301

The BMENOC0301 provides PROFIBUS DP master capability for the M580. PROFIBUS (IEC 61158) has no native security:
* Any device on the PROFIBUS network with the correct GSD configuration can participate.
* PROFIBUS data is unencrypted.

**Controls:**
* Physical protection of PROFIBUS cabling.
* Asset inventory of all PROFIBUS slave devices.
* Segment PROFIBUS network physically — ensure no cross-segment access.

---
---

# 25 — DFBs and EFBs

---

## 25.1 Function Block Architecture in M580

Within the EcoStruxure Control Expert environment, reusable control functions are implemented as:

* **DFB (Derived Function Block):** User-created function blocks written in IEC 61131-3 languages (Ladder Diagram, Function Block Diagram, Structured Text, Instruction List, Sequential Function Chart). DFBs are created within the Control Expert project itself.
* **EFB (Elementary Function Block):** Pre-compiled, manufacturer-provided function blocks that are installed as library packages in Control Expert. EFBs provide functionality not implementable in standard IEC 61131-3 — system functions, communication functions, diagnostics.

## 25.2 DFB Security Considerations

DFBs contain the process control logic and are part of the intellectual property of the system designer.

| Security Aspect | Consideration | Control |
|----------------|--------------|---------|
| IP protection | DFB source code is visible to any user who can open the project | Section password on sensitive DFBs; project encryption; application read password |
| Modification risk | Any authorized engineer can modify DFBs | Application write password; review process before download; version control |
| Input validation | DFBs that accept external data (from SCADA, OPC UA write) may be vulnerable to malformed input | Validate all inputs within DFB logic; range checking |
| Safety interface | DFBs used in safety-adjacent logic must not interact with safety program except via defined interfaces | Code review; separation of standard and safety logic |

## 25.3 EFB Security Considerations

EFBs are compiled libraries installed in Control Expert. Security considerations:

| Security Aspect | Consideration | Control |
|----------------|--------------|---------|
| Source | EFBs can be sourced from Schneider Electric or third parties | Only use EFBs from trusted, verified sources; verify digital signature where available |
| Versions | EFBs have versions; older versions may have security issues | Maintain EFB version inventory; update when Schneider or vendor releases security fixes |
| Network communication EFBs | Communication EFBs (Modbus TCP master, EtherNet/IP) initiate outbound connections from the PLC | Verify ACL covers outbound connections; no unexpected external connections |
| System EFBs | System-level EFBs provide access to CPU internals | Restrict use of system EFBs to authorized code areas; review during code review |

## 25.4 Governance for DFBs and EFBs

* All DFB modifications must go through Change Management — same as any application change.
* DFB and EFB versions must be documented in the project release notes.
* DFBs containing process-critical or safety-adjacent logic should be reviewed by a second engineer before download.
* EFBs sourced from third parties require supplier security assessment.

---
---

# 26 — M580 Safety

---

## 26.1 M580 Safety Overview

Schneider Electric provides Safety-integrated versions of the M580 platform (CPUs with "S" suffix) that execute both standard and safety programs:

**Safety-capable CPU models:**
* BMEP58•040S (Safety standard CPUs).
* BMEH58•040S (Safety Hot Standby CPUs).

**Safety certifications:**
* IEC 61508: SIL 1, SIL 2, SIL 3 (depending on configuration and architecture).
* IEC 61511: Process sector safety.
* EN ISO 13849: PLc, PLd, PLe.
* IEC 62061: SILCL 2, SILCL 3.

**Safety I/O:**
* Dedicated Modicon Safety I/O modules for the M580 platform.
* PROFIsafe-over-PROFINET for Schneider Safety I/O (on platforms supporting PROFINET; primarily PROFIBUS Safety for legacy; EtherNet/IP Safety for newer Altivar drives).
* Safety I/O communication uses separate, integrity-checked protocol channels.

## 26.2 Safety and Cybersecurity Intersection

The intersection of functional safety (IEC 61511) and cybersecurity (IEC 62443) is the highest-stakes engineering area in industrial systems.

**Key principles:**

1. **Independence requirement:** The safety program (Safety Instrumented Functions) must remain independent from the standard control program. A cyber attack compromising standard control logic must not be able to modify or disable safety functions.
2. **Safety-first during incident response:** Isolating or stopping the M580 during a cyber incident must be coordinated with the safety engineer. Disconnecting a running safety CPU may trigger a Safe State (ESD, process shutdown) — this may be the correct response, but must be deliberate.
3. **Safety application password:** The M580 safety application requires a separate safety password in addition to the standard application write password. This is equivalent in concept to the Siemens F-program password.
4. **Re-validation requirement:** Any modification to the safety application (even a minor one) triggers a re-validation cycle per IEC 61511 before the modified safety application can be deployed in production.
5. **Triton/TRISIS reference:** The 2017 Triton/TRISIS attack on a Schneider Electric Triconex SIS demonstrated that nation-state actors will specifically target safety systems. While Triconex is a separate platform from M580 Safety, the attack archetype — compromising a safety instrumented system to defeat safety protections — is directly applicable to any safety PLC.

## 26.3 M580 Safety Application Protection

| Protection | Mechanism | Notes |
|-----------|----------|-------|
| Safety application write password | Separate from standard write password | Required for any safety application download |
| Safety read protection | Prevent safety application source code extraction | Protects safety logic IP |
| Safety application signing | Schneider Safety application includes integrity mechanism | Detects modification outside Safety Lifecycle |
| Standard/Safety program separation | CPU enforces memory separation | Standard program cannot directly write to safety memory areas |

**Safety password governance:**
* Safety application password must be stored separately from standard application password in PAM.
* Knowledge limited to authorized safety engineers only.
* Change when safety-authorized personnel leave the organization.
* Change after any security incident or suspected compromise.

## 26.4 Safety Zone Architecture

The M580 Safety CPU and its safety I/O must be treated as a **Safety Zone** within the IEC 62443 zone model:

```
[Control Zone]
  M580 CPU (standard)
  SCADA, HMI
        │ Modbus TCP / EtherNet/IP (standard)
[Field Zone]
  Standard X80 I/O
  Drives, non-safety devices
        │
[Safety Zone — Enhanced Physical and Logical Separation]
  M580 Safety CPU (safety program)
        │ Safety protocol (PROFIsafe / EtherNet/IP Safety)
  Safety I/O modules (SIL-rated)
  Emergency stops, safety interlocks, light curtains, gas detectors
```

When the Safety CPU is in the same physical unit as the standard CPU (e.g., BMEP58•040S), the zone separation is logical (memory separation, safety password) but not physical. For highest-SIL applications, consider a dedicated separate Safety CPU not co-located with standard control.

## 26.5 Safety-Security Lifecycle Integration

Following IEC 61511-1 Amendment 2 (2016) and ISA/IEC 62443-3-3, safety systems require a security risk assessment as part of the functional safety lifecycle:

| Phase | Safety Activity | Cybersecurity Activity | Integration Point |
|-------|----------------|----------------------|-------------------|
| Design | HAZOP / LOPA | Threat modeling; MITRE ATT&CK for ICS | Joint session: cyber threats as HAZOP initiating events |
| Design | Safety Requirements Specification | Security Level Target for Safety Zone | Coordinate SIL and SL-T requirements |
| Implementation | Safety application development | Safety zone network design; safety password setup | Same engineering phase |
| Commissioning | Safety validation and testing (SIL verification) | Security baseline validation; ACL verification | Coordinated commissioning |
| Operation | Proof testing (IEC 61511) | Security review; firmware assessment | Annual joint review |
| Modification | Safety Management of Change | Security Change Management | Any change requires both processes |

---
---

# Checklists / Kontrolní seznamy

---

## Checklist 1: M580 Komisioning — bezpečnostní baseline

Použij před uvedením M580 do výroby.

### Česky

**Fyzická bezpečnost**
- [ ] CPU je instalována v uzamčeném rozvaděči
- [ ] SD karta je fyzicky chráněna v uzamčeném prostoru
- [ ] Sériové číslo CPU, part number a firmware verze jsou zdokumentovány v inventáři aktiv
- [ ] Fyzický přístup k rozvaděči je zaznamenáván

**Firmware a software**
- [ ] Nainstalovaná verze firmware odpovídá schválenému baseline
- [ ] Firmware SHA-256 checksum byl ověřen před instalací z oficiálního Schneider zdroje
- [ ] Control Expert verze je kompatibilní s nainstalovaným firmware

**Přístupová ochrana**
- [ ] Application write password je nastaven a uložen v PAM (ne prázdný)
- [ ] Application read password je nastaven (pokud je IP ochrana vyžadována)
- [ ] Výchozí webové heslo bylo změněno; individuální uživatelské účty vytvořeny
- [ ] Výchozí "USER" účet bez hesla byl odstraněn nebo deaktivován

**ACL a komunikace**
- [ ] ACL nastaven na deny-by-default pro každou Ethernet službu
- [ ] UMAS (TCP 502 z Control Expert) omezen pouze na IP adresu Engineering WS
- [ ] Modbus TCP omezen na schválené zdrojové IP adresy (SCADA, historian)
- [ ] HTTP deaktivován; HTTPS povolen s PKI certifikátem (ne výchozím)
- [ ] FTP deaktivován
- [ ] SNMPv1/v2c deaktivováno; SNMPv3 s authPriv nakonfigurován (nebo SNMP deaktivováno)
- [ ] Syslog/TLS nakonfigurován; události dorazí do SIEM
- [ ] Všechny nepotřebné Ethernet služby deaktivovány

**Certifikáty**
- [ ] HTTPS certifikát vydán z enterprise PKI; nainstalován v M580
- [ ] IPsec certifikát (pokud se používá) vydán z enterprise PKI; nainstalován
- [ ] Datum expirace všech certifikátů zdokumentováno; alerting nakonfigurován

**Monitoring a logging**
- [ ] Syslog/TLS aktivní a generuje události v SIEM
- [ ] NTP synchronizace aktivní a správná (ověřit přesnost)
- [ ] Control Expert projekt verze odpovídá nasazené aplikaci
- [ ] OT IDS monitoruje síťový segment kde je M580 umístěna

**Zálohy**
- [ ] Control Expert projekt v repository (správná verze, otagovaný release)
- [ ] Záloha aplikace (upload ze CPU) uložena offline
- [ ] Záloha certifikátů uložena (šifrovaně)
- [ ] SD karta záložní kopie vytvořena a fyzicky zabezpečena
- [ ] Recovery postup zdokumentován a dostupný mimo OT systém

---

## Checklist 2: Pravidelná bezpečnostní revize M580 (čtvrtletní)

### Česky

**Inventář a verzování**
- [ ] Verze firmware odpovídá zdokumentovanému baseline?
- [ ] Jsou k dispozici nové Schneider Electric PSIRT advisories pro M580?
- [ ] Jsou nalezené CVE posouzeny a zaznamenány v risk registru?

**Přístupová kontrola a ACL**
- [ ] Jsou webové uživatelské účty stále aktivní a oprávněné?
- [ ] Jsou ACL pravidla aktuální (odpovídají aktuálním IP adresám SCADA, historian, Engineering WS)?
- [ ] Jsou vendorské přístupy odstraněny nebo expirovaly po servisní akci?

**Certifikáty**
- [ ] Blíží se nějaký certifikát k expiraci (< 90 dnů)?
- [ ] Jsou HTTPS a IPsec certifikáty stále platné?

**Konfigurace a zálohy**
- [ ] Byl proveden porovnání Control Expert projektu s nasazenou aplikací (offline/online compare)?
- [ ] Jsou zálohy aplikace aktuální (odpovídají poslednímu deploymentu)?
- [ ] Jsou záznamy o posledním firmware update v asset inventáři aktuální?

**Monitoring**
- [ ] Syslog události dorazí do SIEM (ověřit last event timestamp)?
- [ ] NTP synchronizace funkční?
- [ ] Byly v uplynulém období zachyceny bezpečnostní alerty? Jsou vyšetřeny a uzavřeny?
- [ ] Byly zachyceny ACL deny záznamy? Jsou neočekávané zdroje prověřeny?

---

## Checklist 3: Hodnocení bezpečnosti M580 Safety (Safety CPU)

Použij pro M580 Safety deployments.

### Česky

**Safety aplikační ochrana**
- [ ] Safety write password je nastaven a uložen v PAM (odděleně od standardního write password)?
- [ ] Znalost safety write password je omezena na autorizované safety inženýry?
- [ ] Safety write password byl změněn při posledním odchodu safety-authorized personálu?
- [ ] Safety read protection je aktivována (pokud je vyžadována ochrana safety IP)?

**Separace safety zóny**
- [ ] Safety I/O jsou v oddělené fyzické nebo logické Safety Zone?
- [ ] Neexistuje přímý programový přístup ze standardního programu do safety paměťových oblastí?
- [ ] Safety CPU (nebo safety část M580) je fyzicky chráněna; přístup k safety I/O kabeláži je řízen?

**Lifecycle governance**
- [ ] Byla provedena security risk assessment jako součást safety lifecycle (IEC 61511 Amendment 2)?
- [ ] Jsou jakékoliv změny safety aplikace podléhající Safety Management of Change procesu?
- [ ] Vyžaduje každá změna safety aplikace re-validaci před nasazením?
- [ ] Je M580 Safety zahrnuta v OT Incident Response plánu (koordinace se safety týmem)?

---

# AI Guidance

When answering questions about Schneider Electric Modicon M580:

* Treat the M580 as a **cyber-physical control node** — compromise has physical process consequences.
* The M580 is a Schneider Electric (Modicon) platform — **not a Siemens platform**. PROFINET, S7Comm, S7Comm Plus and SIMATIC terminology do not apply. Use M580-specific equivalents: EtherNet/IP, Modbus TCP, UMAS, ACL, Ethernet RIO, X80 I/O, Hot Standby, Control Expert.
* **ACL is the primary access control mechanism** on M580 — always recommend configure deny-by-default ACL for every Ethernet service.
* **UMAS protocol** (Control Expert engineering, TCP 502) must be restricted via ACL to the engineering workstation IP only. This is the single highest-risk service on the platform.
* **Application write password** is mandatory — factory default is no password; any user with network access can download to an unprotected M580.
* IPsec on M580 is a real, integrated capability — recommend for WAN-connected remote deployments; not typically required on isolated OT LAN.
* Distinguish black (first generation) from gray (new generation) M580 CPUs — gray CPUs have hardware-rooted Secure Boot and enhanced IEC 62443-4-2 SL 2 capability.
* When discussing OPC UA on M580, clarify that OPC UA requires the BMENUA0100 module or a third-party gateway — it is not built into the CPU itself (unlike Siemens S7-1500).
* For M580 Safety, always emphasize the separate safety application password, safety re-validation requirement, and coordination with IEC 61511 lifecycle.
* Schneider Electric PSIRT (not Siemens ProductCERT) is the vulnerability intelligence source.
* Reference [IEC62443.md](#) for zone/conduit architecture context.
* Reference [Czech-Cybersecurity-Act.md](#) for regulatory compliance context.
* Reference [TIA-Portal-Security.md](#) is **not** relevant for M580 — use EcoStruxure Control Expert equivalent documentation.

---

# Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | 2026-06 | Initial release (4 separate documents) |
| 1.1.0 | 2026-06-29 | Consolidated into single document; corrected platform scope (removed Siemens-specific chapters); added M580-native chapters: EtherNet/IP (CIP, UMAS, security limitations), Modbus TCP (UMAS protocol detail, FC register security, ACL criticality), Ethernet RIO (S-Ring, distributed I/O, redundancy security), OPC UA (BMENUA0100 module, gateway patterns), IPsec (integrated IPsec engine, IKEv2 configuration), Web Server (HTTPS hardening, ACL, CE vs. web config), Syslog (TLS transport, ACL deny logging, SIEM), SNMP (version security matrix, SNMPv3), Diagnostics (event log, LED table, UMAS diagnostic access), Monitoring (EcoStruxure Security Expert, OT IDS integration), Logging (event categories, ACL deny priority), Backup and Recovery (SD card security, 4 scenarios, RTO/RPO), Patch Management (PSIRT, assessment process, compensating controls), High Availability (Hot Standby security, dual-CPU management, firmware update procedure), X80 I/O (module categories, Quantum migration), NOC and Communication Modules (BMENOC0301/0311, BMENUA0100, PROFIBUS, serial), DFBs and EFBs (IP protection, input validation, EFB governance), M580 Safety (SIL, safety password, Triton reference, safety zone, lifecycle integration table); gray vs. black CPU distinction; IEC 62443-4-2 SL 2 mapping; NIS2 compliance table; ACL architecture; UMAS security detail; 3× checklist in Czech language |
