---
title: Schneider Electric EcoStruxure™ Control Expert — Security Architecture and Engineering Governance
category: Engineering Platform
version: 1.1.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-06-29
review_cycle: Annual
---

# Purpose

This document provides a comprehensive security architecture and engineering governance reference for Schneider Electric EcoStruxure™ Control Expert from the perspective of an OT Security Architect.

EcoStruxure Control Expert (formerly Unity Pro) is the authoritative engineering environment for Schneider Electric Modicon PAC platforms — including the Modicon M580, M340, Premium and Quantum families. It provides a unified environment for hardware configuration, controller programming, network topology, safety engineering, diagnostics, commissioning and lifecycle management.

**Control Expert must be understood as the Root of Trust for Modicon automation systems.** Compromise of the engineering platform is operationally equivalent to compromise of the controllers it manages — including any M580, M340 or other Modicon PAC within its scope.

**Intended audience:** OT Security Architects, Automation Engineers, System Integrators, OT Administrators, AI knowledge base, RAG systems.

> **Regulatory context:** Control Expert security directly supports compliance with IEC 62443-3-3 (System Security Requirements), IEC 62443-4-2 (Component Security Requirements), NIS2 Article 21, and Czech Cybersecurity Act Decrees 409/410. The engineering workstation and its governance processes are auditable assets — not merely tools.

> **Platform note:** EcoStruxure Control Expert is the Schneider Electric (Modicon) engineering platform, equivalent in role to Siemens TIA Portal. Key platform terminology: Control Expert (not TIA Portal), Modicon M580 (not S7-1500), UMAS protocol (not S7Comm+), ACL on M580 (not UMAC), X80 I/O and Ethernet RIO (not ET 200 and PROFINET).

---

# Related Documents

| Document | Relationship |
|----------|-------------|
| [Modicon-M580.md](#) | Primary controller platform — the target of Control Expert engineering |
| [IEC62443.md](#) | Engineering security framework |
| [NIS2.md](#) | EU governance framework |
| [Czech-Cybersecurity-Act.md](#) | Czech national obligations |
| [ISA95.md](#) | Functional reference model — Control Expert operates at ISA-95 Level 2 |

---

# Document Structure

| Chapter | Content |
|---------|---------|
| [1 — Overview](#1--overview) | Platform architecture, role, supported controllers, engineering workflow |
| [2 — Identity and Access](#2--identity-and-access) | Authentication, RBAC, CE user management, multi-layer auth |
| [3 — Security](#3--security) | Threat model, defense in depth, security architecture |
| [4 — Hardening](#4--hardening) | OS hardening, application hardening, network, USB, monitoring |
| [5 — Project Protection](#5--project-protection) | Write/read passwords, encryption, section protection, IP |
| [6 — Version Control](#6--version-control) | Repository, baselines, branching, release packages |
| [7 — Certificates](#7--certificates) | PKI for engineering, M580 HTTPS deployment, OPC UA |
| [8 — Backup and Recovery](#8--backup-and-recovery) | Backup scope, recovery scenarios, RTO/RPO |
| [9 — Patch Management](#9--patch-management) | CE updates, PSIRT, update process, compensating controls |
| [10 — Best Practices](#10--best-practices) | Reference architecture, common mistakes, compliance mapping, checklists |

---
---

# 1 — Overview

---

## 1.1 Platform Role

EcoStruxure Control Expert is the authoritative engineering platform for the Schneider Electric Modicon PAC ecosystem. It provides a single integrated environment for the complete engineering lifecycle: hardware configuration, PLC programming in all five IEC 61131-3 languages (LD, FBD, ST, IL, SFC), safety application development, network topology, diagnostics, commissioning and lifecycle management.

From a security perspective, Control Expert must not be regarded as merely a programming tool. It is the **authoritative engineering authority** for every Modicon controller it manages. The engineering project stored in Control Expert — not the running controller — is the authoritative source of truth for the automation system.

**The critical security premise:** If Control Expert is compromised, the integrity of every Modicon controller connected to it is in question. Unauthorized modification of a Control Expert project, followed by download to the controller, can arbitrarily manipulate the physical process — with consequences ranging from production disruption to safety incidents.

## 1.2 Supported Controller Platforms

| Controller Family | Generation | Notes |
|-------------------|-----------|-------|
| **Modicon M580 ePAC** | Current | Primary platform; native Ethernet backplane; full security feature set |
| **Modicon M340** | Previous generation | Modbus TCP/EtherNet/IP; fewer native security features than M580 |
| **Modicon Premium** | Legacy | PROFIBUS-based; PCMCIA I/O; limited security; end of active development |
| **Modicon Quantum** | Legacy | High-performance; Ethernet; predecessor to M580; active installed base |
| **Modicon M580 Safety** | Current | Safety-integrated M580; SIL 2/3; safety application in Control Expert |
| **Modicon M221/M241/M262** | Entry-level | Smaller machines; separate configuration tools (SoMachine/Machine Expert) |

> **Scope note:** This document focuses on the M580 as the primary current-generation platform. M340, Premium and Quantum guidance follows similar principles but differs in available security features. Always verify platform-specific capabilities in Schneider Electric product documentation.

## 1.3 Engineering Workflow

```
Requirements Analysis
        ↓
System Architecture (Hardware, Network, Safety, Security)
        ↓
Hardware Configuration (Device catalog, rack composition, I/O assignment)
        ↓
Network Topology (Ethernet RIO topology, communication configuration)
        ↓
Application Development (LD/FBD/ST/IL/SFC programs; DFBs; EFBs)
        ↓
Safety Application Development (if M580 Safety)
        ↓
Project Protection (Write/Read password; Section passwords; encryption)
        ↓
Security Configuration (M580 ACL, HTTPS, Syslog — configured via CE)
        ↓
Peer Review + Security Review
        ↓
Simulation / Testing (if staging environment available)
        ↓
Approval + Release Baseline (version control tag; release package)
        ↓
Commissioning (Online → Compare → Download)
        ↓
Production Operation
        ↓
Maintenance (Changes via formal Change Management process)
        ↓
Archive / Decommissioning
```

Every lifecycle phase should produce auditable artefacts. Security review is not a final step — it must be integrated throughout, particularly at the design and review phases.

## 1.4 Security Role of Control Expert

Control Expert is responsible for configuring and deploying the security functions of every Modicon M580 it manages:

| Security Function | Control Expert Role |
|-----------------|---------------------|
| Application write password | Configured in CE Project Properties; downloaded with project |
| Application read password | Configured in CE Project Properties; downloaded with project |
| Section passwords | Per-section protection configured in CE |
| M580 ACL (Access Control Lists) | Configured in CE → Services → Ethernet Services → ACL |
| M580 HTTPS configuration | Configured in CE → Services → Web Server |
| M580 Syslog configuration | Configured in CE → Services → Syslog |
| M580 SNMP configuration | Configured in CE → Services → SNMP |
| M580 IPsec configuration | Configured in CE → Services → IPsec |
| M580 NTP configuration | Configured in CE → Services → NTP |
| M580 service enable/disable | Configured in CE → Services → Ethernet Services |
| Firmware download to M580 | Performed via CE online → Firmware Update |

**The security of every M580 controller is ultimately determined by the security of its Control Expert project.** A compromised Control Expert environment means a compromised security configuration for all managed controllers.

## 1.5 ISA-95 Context

In the ISA-95 functional hierarchy, the Control Expert engineering workstation operates at:
* **ISA-95 Level 2** — Engineering workstations, supervisory systems.
* With direct interfaces to **ISA-95 Level 1** — Modicon M580 PLCs, drives, field devices.

This dual-level footprint makes the engineering workstation particularly sensitive: it spans the boundary between supervisory and field control, with write access to Level 1 devices. The engineering workstation must reside in a dedicated **Engineering Zone** — separate from the Control Zone (SCADA/HMI) and from enterprise IT networks.

---
---

# 2 — Identity and Access

---

## 2.1 Identity Philosophy

Every engineering action performed through Control Expert must be attributable to a specific, authenticated individual. This is both a security requirement (IEC 62443-4-2 CR 1.1) and a regulatory compliance requirement (NIS2 Art. 21(2)(i)).

**Shared engineering accounts are never acceptable.** They defeat audit trails, prevent insider threat detection, and make incident forensics impossible. "engineer1", "admin", "scada", "modicon" — all are examples of shared accounts that must not exist in a production engineering environment.

## 2.2 Authentication Architecture

Control Expert environments involve multiple authentication layers that must all be secured:

```
LAYER 1: Engineering Workstation OS Authentication
  Windows user account + MFA (hardware token, smart card, or authenticator app)
  Enforced via Active Directory + Group Policy

LAYER 2: Control Expert Application Authentication
  Control Expert user profile (CE user management)
  Rights assigned per user: project access, download, online modification, simulation

LAYER 3: Application-Level Protection (Controller-side)
  M580 application write password (required to download changes to controller)
  M580 application read password (required to upload/read back application)

LAYER 4: PAM (Privileged Access Management) — for critical operations
  PAM approval required for production downloads
  Session recording for download sessions
  Credential vaulting for application passwords
```

An attacker must breach all applicable layers to successfully deploy unauthorized logic to an M580 controller.

## 2.3 Control Expert User Management

Control Expert maintains an internal user management system governing who can access and modify projects within the engineering tool.

**User rights in Control Expert (typical structure):**

| Right | Description | Recommended Role |
|-------|-------------|-----------------|
| Project open (read) | View project; cannot modify | Auditor, Maintenance |
| Project modify | Edit program logic; cannot download | Junior Engineer |
| Project download | Download to controller (requires app password) | Senior Engineer |
| Online modification | Modify running application (requires app password) | Senior Engineer |
| Hardware configuration | Modify rack/module configuration | Engineering Lead |
| Simulation | Run PLCSIM-equivalent simulation | Engineer |
| Library management | Modify shared libraries | Engineering Lead |
| User management | Create/modify CE user accounts | Administrator |
| Safety application | Access safety program sections | Safety Engineer (separate) |

**Configuration:**
* CE user management is accessed via Control Expert → Tools → Users and Groups.
* Users are assigned to groups; groups have rights assigned.
* CE user database is stored locally on the engineering workstation — AD integration is at the OS level, not natively within CE itself.
* Back up the CE user database as part of workstation backup.

## 2.4 Multi-Factor Authentication

MFA must be enforced at the operating system level (Layer 1) since Control Expert does not natively support MFA within the application.

**MFA options for engineering workstations:**

| Method | Suitability | Notes |
|--------|------------|-------|
| Hardware security token (FIDO2, YubiKey) | **Preferred** | Phishing-resistant; offline capable; no mobile network dependency |
| Smart card / PIV card | **Preferred for high-security** | AD integration; certificate-based; requires card reader on WS |
| Authenticator app (TOTP) | Acceptable | Requires mobile device; TOTP codes expire |
| SMS OTP | **Not recommended** | SIM-swap risk; mobile network dependency |
| Windows Hello for Business | Acceptable | Biometric + PIN; AD integrated; hardware-backed |

MFA must be enforced for:
* All engineering workstation logins.
* All remote access to the engineering workstation or engineering zone network.
* All PAM-mediated privileged operations (production downloads, online modifications to critical systems).

## 2.5 Privileged Access Management (PAM)

The Control Expert engineering role — particularly project download to production controllers — is a high-privilege operation equivalent to direct PLC programming. It must be governed through PAM.

**PAM capabilities for Control Expert environments:**

| Capability | Implementation | Security Benefit |
|-----------|---------------|-----------------|
| Credential vaulting | PAM vault stores application write/read passwords | Engineers never see or share passwords; PAM issues for each session |
| Session recording | Video + keystroke recording of production download sessions | Forensic accountability; post-incident review |
| Just-in-time (JIT) access | Elevated rights granted only for approved time window | Reduces standing privilege exposure |
| Approval workflow | Production download requires approval from second authorized person (four-eyes) | Prevents single-person unauthorized download |
| Session termination | PAM can terminate active sessions on demand | Incident response capability |

**Application password management in PAM:**
* M580 application write password must be stored in PAM, not in a shared spreadsheet or email.
* Engineers request the password from PAM for each download session.
* PAM logs the credential access with timestamp and requester identity.
* Password rotation is triggered by personnel changes or security incidents.

## 2.6 Vendor and Contractor Access

Remote vendor access to Control Expert / M580 is a significant attack vector — see also [Modicon-M580.md](#).

**Required controls for vendor remote access to engineering environment:**

| Control | Requirement |
|---------|-------------|
| Time-limited credentials | OS account with automatic expiry after session window |
| MFA | Mandatory; hardware token preferred |
| Approved device | Vendor uses approved managed device, not personal laptop |
| Jump server | All remote access via jump server in Engineering DMZ |
| Session recording | Full video + keystroke recording; stored outside engineering WS |
| Scope restriction | Vendor can only access systems required for specific task |
| Post-session | Credentials revoked; M580 ACL reviewed; project integrity verified |
| Contractual | Incident reporting obligation; no subcontracting without approval |

## 2.7 Identity Lifecycle for Engineering Environments

| Phase | Action | Trigger | Responsibility |
|-------|--------|---------|---------------|
| Provisioning | Create OS account; create CE user profile; set initial password | New team member | Engineering manager |
| First access | Force password change; validate MFA enrollment | First login | User + IT admin |
| Periodic review | Verify accounts still needed; review CE rights | Quarterly | Security team |
| Role change | Update CE user group; update PAM rights | Role change | Engineering manager |
| Deprovisioning | Disable OS account; remove CE user; rotate M580 app passwords | Departure | Immediately; same day |
| Vendor lifecycle | Create time-limited account; set expiry; verify cleanup | Per service event | Engineering manager |

---
---

# 3 — Security

---

## 3.1 Security Philosophy

Control Expert must be understood as a **Tier-0 privileged asset** — equivalent in sensitivity to a domain controller in enterprise IT. A compromised Control Expert environment gives an attacker the ability to deploy arbitrary code to every Modicon M580 that the engineering workstation can reach.

Security is achieved through multiple complementary layers. No single control provides sufficient protection. The weakest layer determines the effective security of the entire engineering environment.

## 3.2 Threat Model

| Threat | Attack Path | Consequence | Probability |
|--------|-----------|-------------|------------|
| Ransomware encrypting projects | Phishing → malware → encrypt .stu/.zef project files | Engineering capability loss; production cannot be modified or recovered | High (most common OT support system attack) |
| Malware deploying modified controller logic | Compromised WS → modified project → download to M580 | Malicious control logic in production PLC; process disruption; safety impact | Medium-High |
| Credential theft | Shared accounts; weak passwords; keylogger on WS | Unauthorized CE access; unauthorized application download | Medium |
| USB malware | USB on WS → malware installation → project modification | Project infection; malware in engineering environment | High (primary OT vector) |
| Supply chain — CE update | Trojanized CE update or DFB library from unofficial source | Persistent malware in engineering environment | Low but critical impact |
| Insider — malicious | Legitimate CE access → unauthorized logic change → download | Sabotage; backdoor insertion | Low-Medium |
| Compromised vendor session | Vendor VPN → CE → M580 download | Unauthorized firmware or program change | Medium |
| Project exfiltration | WS compromise → steal .stu/.zef files → IP theft | Loss of proprietary process IP; reverse engineering for targeted attack | Medium |
| WS bridging (IT/OT) | General-purpose WS on both IT and OT networks → lateral movement | IT ransomware reaches OT network; attacker pivots from IT to OT | Medium-High (common when WS not dedicated) |

**Key insight for M580 environments:** Because M580 ACL is configured within the Control Expert project and downloaded with it, a compromised Control Expert project could potentially deploy a permissive ACL configuration that removes network-level access restrictions on the M580 — creating a multi-stage attack (compromise CE → modify ACL → deploy → exploit open M580 services). ACL configuration changes must be specifically monitored.

## 3.3 Security Architecture — Defense in Depth

```
LAYER 1: Physical Security
  Locked dedicated room or area for engineering workstations
  Access badge readers; no USB hubs/peripherals unless approved
  Screen lock on unattended workstations; Kensington lock on laptops
  Locked cabinets for engineering laptops when not in use

LAYER 2: Network Isolation (Engineering Zone)
  Dedicated Engineering Zone — separate from Control Zone and IT
  Firewall with allowlist rules between Engineering Zone and OT Control Zone
  No direct Engineering Zone → IT connectivity (no dual-homed WS)
  Remote access: only via jump server in Engineering DMZ; MFA mandatory
  No direct internet access from Engineering Zone

LAYER 3: Operating System Hardening
  Windows 10/11 Pro or Windows Server (supported; current patches)
  CIS Benchmark baseline (OT-adapted)
  Windows Firewall enabled
  Application allowlisting (Windows WDAC or AppLocker)
  BitLocker disk encryption
  Disable: AutoRun, AutoPlay, unused Windows features, SMBv1

LAYER 4: Identity and Authentication
  Individual named accounts only — no shared accounts
  Active Directory integration
  MFA for all logins (hardware token preferred)
  CE user management with role-based rights
  PAM for production download sessions

LAYER 5: Application Hardening
  Only approved CE version installed
  Only required CE optional components installed
  DFB/EFB libraries from trusted Schneider Electric sources only
  No internet-accessible tools on engineering WS
  CE launched under standard user account; elevation only when required

LAYER 6: Project and Configuration Integrity
  Version control repository (Git/GitLab/Azure DevOps)
  Approved project baselines before any controller download
  Mandatory offline vs. online compare before download
  Change Management process for every modification
  Application write password on every M580
  Project encryption enabled for sensitive projects

LAYER 7: Certificate Management
  Enterprise PKI for M580 HTTPS certificates
  IPsec certificates for WAN-connected M580 deployments
  OPC UA certificates (if BMENUA0100 OPC UA module used)
  Certificate expiry monitoring

LAYER 8: Monitoring and Audit
  CE user actions logged (OS audit log forwarded to SIEM)
  Windows Security Event Log forwarded (logon, privilege use, process creation)
  OT IDS passive monitoring of Engineering Zone network traffic
  Alerts: off-hours CE logins, unexpected M580 connections, project downloads
  NTP synchronized time on engineering workstation

LAYER 9: Backup and Recovery
  Control Expert project in version control (continuous)
  Workstation disk image (monthly + after major changes)
  Offline/air-gapped project backup (after every production release)
  Tested recovery procedures (annual workstation rebuild test)
```

## 3.4 Engineering Zone Network Architecture

```
[IT Enterprise Network]
        │
        │ NO DIRECT CONNECTION
        │
[Engineering DMZ]
  Jump Server (for remote engineering access)
  Patch Staging Server (updates pulled here; not from internet by WS)
  Log Relay (OT Syslog → SIEM without return path)
        │
        │ Allowlist Firewall
        │
[Engineering Zone — OT Network]
  Engineering WS 1 (Control Expert)
  Engineering WS 2 (Control Expert)
  CE Repository Server (Git/GitLab — local)
  [Optional] PLCSIM / Simulation environment
        │
        │ Allowlist Conduit (Modbus TCP/UMAS to specific M580 IPs)
        │
[Control Zone — OT Network]
  Modicon M580 PLCs
  SCADA Servers
  HMI Stations
```

**Engineering Zone firewall allowlist (example):**

| Source | Destination | Port/Protocol | Purpose |
|--------|------------|---------------|---------|
| Engineering WS | M580 CPU | TCP 502 (Modbus TCP/UMAS) | Control Expert programming |
| Engineering WS | M580 CPU | TCP 443 (HTTPS) | Web management |
| Engineering WS | CE Repo Server | TCP 443/22 (HTTPS/SSH) | Version control |
| Engineering WS | Patch Staging | TCP 443 (HTTPS) | Software updates |
| All others | Engineering WS | ANY | **DENY** |
| Engineering WS | Internet | ANY | **DENY** |
| Engineering WS | IT Network | ANY | **DENY** |

## 3.5 IEC 62443 Alignment

| IEC 62443 FR | Control Expert Implementation |
|-------------|------------------------------|
| FR 1 — Identification and Authentication | CE user management; MFA at OS; multi-layer auth (CE user + app password + PAM) |
| FR 2 — Use Control | CE RBAC rights per user/group; PAM for privileged downloads |
| FR 3 — System Integrity | Version control; offline/online compare; application write password; project encryption |
| FR 4 — Data Confidentiality | Project encryption (.stu/.zef); section passwords for IP; PKI for M580 HTTPS |
| FR 5 — Restricted Data Flow | Engineering Zone isolation; allowlist firewall; ACL on M580 configured via CE |
| FR 6 — Timely Response to Events | OS audit log → SIEM; CE activity logging; OT IDS on engineering network |
| FR 7 — Resource Availability | Version control backup; workstation image; offline project backup; tested recovery |

---
---

# 4 — Hardening

---

## 4.1 Hardening Philosophy

The engineering workstation is the Root of Trust for Modicon automation systems. Every hardening measure reduces the probability that this trust is violated. Hardening is not a one-time installation activity — it is a continuous operational discipline.

**Guiding principle:** Every enabled service, installed application, active account, and external connection increases the attack surface. Before enabling any capability on the engineering workstation, ask: *"Is this operationally required? If compromised, what is the impact?"*

## 4.2 Phase 1 — Dedicated Engineering Workstation

The most important single hardening decision is ensuring the engineering workstation is **exclusively dedicated to engineering** — no shared use with office tasks, email, internet browsing or general IT activities.

**Why this matters:**
* Phishing emails are the most common initial access vector for ransomware.
* A workstation that receives email can be compromised via a malicious attachment.
* A compromised workstation that also has Control Expert can deploy malicious logic to M580 controllers.
* Separation into dedicated workstations eliminates this entire attack class.

**Implementation:**
* Physical or virtual machines dedicated to OT engineering.
* No email client installed.
* No general-purpose web browser configured with accounts or bookmarks.
* No Microsoft 365, Google Workspace, or productivity suite beyond what CE requires.
* No IT service desk agents (use out-of-band management).
* Laptop: no personal use; secured in locked cabinet when not in use.

## 4.3 Phase 2 — Operating System Hardening

| Area | Requirement | Implementation |
|------|-------------|---------------|
| OS version | Windows 10/11 Pro or Windows Server 2019/2022 (supported) | Verified against CE compatibility matrix |
| Security updates | Applied within defined patch window | Via DMZ-staged patch server; not direct internet |
| Unnecessary features | Remove/disable: IIS, Hyper-V (unless simulation), Telnet, FTP server | Windows Features control panel / PowerShell |
| SMB | Disable SMBv1; restrict SMBv2/3 | Registry + Group Policy |
| Remote Desktop | Disable or restrict to jump server only; NLA mandatory | Group Policy |
| Windows Firewall | Enable; allowlist rules for CE and repository traffic | Secondary to network firewall |
| Disk encryption | BitLocker on all drives (especially portable laptops) | TPM-backed; recovery key in PKI/PAM |
| AutoRun/AutoPlay | Disabled system-wide | Group Policy |
| Screen lock | 5-minute idle lock; MFA required to unlock | Group Policy |
| Time sync | NTP configured to organizational NTP server | Group Policy |
| Audit policy | Logon, privilege use, process creation, policy change, object access | Advanced Audit Policy |

## 4.4 Phase 3 — Application Allowlisting

Application allowlisting is one of the most effective controls against malware on engineering workstations. Only pre-approved executables can run — all other code is blocked.

**Implementation options:**

| Tool | Notes |
|------|-------|
| Windows Defender Application Control (WDAC) | Microsoft built-in; policy-based; recommended for new deployments |
| Windows AppLocker | Built-in; rule-based (publisher, path, hash) |
| Carbon Black App Control | Commercial; richer management interface |

**Control Expert and allowlisting:**
Control Expert installs many components across multiple directories. Publisher-based rules (allow all executables signed by Schneider Electric) provide a good starting point. Hash-based rules require updating when CE is patched. Test the allowlist in a staging environment before production deployment — CE update failures due to allowlist blocks are disruptive.

## 4.5 Phase 4 — Control Expert Application Hardening

| Area | Requirement | Notes |
|------|-------------|-------|
| CE version | Only approved, current version installed | Verify against Schneider compatibility matrix |
| Optional components | Install only required components | Remove simulation environments not needed in production |
| DFB/EFB libraries | Only from Schneider Electric official sources | Verify authenticity; maintain library inventory |
| Project directory | Store projects on version-controlled network path; not Desktop | ACL-protected; backed up |
| Temporary files | CE temporary directory on encrypted volume | CE temp files may contain project content |
| CE user database | Back up CE user database as part of workstation backup | Contains user accounts and rights definitions |
| Serial/license dongles | Inventory and control | USB dongle = approved USB device exception in removable media policy |

## 4.6 Phase 5 — Removable Media Control

USB and other removable media represent the most common malware introduction vector in OT environments, particularly for engineering workstations that are isolated from the internet.

**Control hierarchy:**

| Level | Control | Notes |
|-------|---------|-------|
| Policy | Written policy prohibiting personal USB on engineering WS | Foundation |
| Technical | USB port blocking (Windows Device Control / Group Policy) | Allowlist by device class or specific serial number |
| Approved media | Organization-issued, labelled, tracked USB media only | Used exclusively on engineering systems |
| Scanning | Mandatory malware scan on dedicated scanning station before any USB use on OT-adjacent systems | Scanning station in Engineering DMZ |
| Alternatives | File transfer via DMZ file transfer server; pull from version control | Preferred over physical media |

**Exceptions management:**
* CE software installation media: scan before use; pull from official Schneider portal to staging server instead.
* License dongles (USB): approved by policy; inventory-tracked; do not share between workstations.
* Customer/vendor USB: never use directly on engineering WS; scan on isolated scanning station first.

## 4.7 Phase 6 — Network Hardening for Engineering Zone

| Control | Requirement |
|---------|-------------|
| VLAN separation | Engineering Zone on dedicated VLAN; no trunk to IT VLANs |
| Firewall | Dedicated firewall between Engineering Zone and OT Control Zone; allowlist only |
| No dual-homed workstations | Engineering WS has only one network interface — Engineering Zone | No second interface to IT LAN |
| Internet access | Blocked from Engineering Zone; updates via DMZ staging server |
| DNS | Internal DNS only; no public DNS resolvers |
| NTP | Internal NTP server; not direct internet NTP |
| Wireless | No wireless in Engineering Zone; if required: WPA3-Enterprise; isolated SSID |
| OT IDS | Passive monitoring of Engineering Zone network traffic |

## 4.8 Phase 7 — Endpoint Protection

**Antimalware for engineering workstations:**
* Use enterprise endpoint protection validated for compatibility with Control Expert (check Schneider compatibility list).
* Configure exclusions for CE runtime directories to reduce false-positive interference.
* Signature updates via DMZ-staged update mirror — engineering WS must not pull from internet.
* EDR (Endpoint Detection and Response) preferred over signature-only AV for behavioral detection.

**Compatibility note:** Schneider Electric maintains a compatibility list for endpoint protection software with Control Expert and Modicon products. Always verify before deployment — incompatible security software has caused CE communication failures in production environments.

## 4.9 Hardening Validation

After initial hardening and after significant changes, validate:

- [ ] Dedicated workstation: no email client; no general browser; no office productivity suite
- [ ] OS patching current; applied from staging server, not internet
- [ ] Unnecessary Windows features removed
- [ ] BitLocker enabled on all drives
- [ ] AutoRun/AutoPlay disabled
- [ ] USB policy enforced: unauthorized devices blocked
- [ ] Application allowlisting active: CE launches normally; non-approved executables blocked
- [ ] MFA active: workstation login requires second factor
- [ ] Audit logging active: events reaching SIEM
- [ ] Network: Engineering Zone VLAN; no IT connection; no internet
- [ ] Antimalware active and updating from DMZ mirror

---
---

# 5 — Project Protection

---

## 5.1 The Engineering Project as Critical Asset

A Control Expert project (.stu file for M580/M340/Quantum; .zef for M580 new format) is the **complete digital definition of the automation system**. It contains:

* Hardware configuration — rack composition, module parameters, I/O addresses.
* PLC program — all ladder, FBD, ST, IL, SFC logic.
* Safety application (if M580 Safety) — fail-safe logic and safety parameter sets.
* Network topology — Ethernet RIO topology, communication parameters.
* Security configuration — M580 ACL rules, HTTPS settings, Syslog configuration, SNMP settings.
* DFB/EFB library references — proprietary function blocks.
* Documentation — embedded comments, descriptions, revision notes.

**Unauthorized access to this project enables an attacker to:**
* Understand the process in detail (reconnaissance for targeted attack).
* Identify safety interlocks and understand how to defeat them.
* Develop precise malicious modifications.
* Create a counterfeit project that appears legitimate.

**Unauthorized modification enables:**
* Deployment of malicious control logic.
* Modification of M580 ACL to remove access restrictions.
* Disabling of Syslog forwarding (removes security visibility).
* Change of application passwords (locks out legitimate engineers).

Project protection is both an IP protection measure and a safety-critical security control.

## 5.2 Application Write Protection

The application write password prevents unauthorized users from downloading a new or modified application to the M580. **This is the single most important native protection control in the Control Expert / M580 ecosystem.**

**Configuration in Control Expert:**
* Project Properties → Protection → Write Protection → Set password.
* Password required for every application download (regardless of who performs it).
* Password required for online modification (modification of running application).
* Password required for Safety application download (separate safety password recommended).

**Factory default: no password.** Any Control Expert user with network access to an unprotected M580 can download arbitrary logic. This must be remediated before production deployment.

**Password governance:**
* Store in PAM — not in a shared document, spreadsheet, or email.
* Each M580 should have a unique write password (not shared across all PLCs in the facility).
* Rotate when personnel with knowledge of the password leave.
* Rotate after any security incident or suspected compromise.
* PAM logs every credential access with requester identity and timestamp.

## 5.3 Application Read Protection

The read password prevents Control Expert from uploading (reading back) the application from the M580. This protects IP and prevents reconnaissance by reverse-engineering the deployed application.

**Considerations:**
* Read protection prevents recovery of the application from the controller if no project backup exists — always maintain version-controlled project backups independently of the controller.
* Section-level read passwords allow selective protection: protect proprietary process algorithms while leaving standard logic visible for maintenance.
* Read protection does not affect runtime execution — the protected application continues to run normally.

**When to apply read protection:**
* Projects containing proprietary process algorithms (OEM machine builders).
* Projects containing competitive IP.
* High-security facilities where process knowledge must be tightly controlled.
* Safety applications (always apply read + write protection to safety sections).

## 5.4 Section-Level Password Protection

Control Expert supports applying passwords at the program section level — protecting individual LD, FBD, ST, IL, or SFC sections within the project.

**Use cases:**
* Third-party supplier libraries: protect supplier IP while leaving standard logic accessible.
* Safety logic: protect fail-safe sections with separate password from standard logic.
* Proprietary algorithms: protect specific control algorithms without restricting view of the overall project structure.

**Important:** Section passwords are cumulative with the project-level read password — a section protected by a section password requires both the project read password (to open the project) and the section password (to view that section).

## 5.5 Project Encryption

Control Expert supports encrypting the project file at rest:

**How it works:**
* Project encryption is applied via Project Properties → Protection → Encrypt project.
* Encrypted project file cannot be opened without the correct password.
* Encryption uses AES-based encryption (Schneider Electric implementation).
* Encrypted project can still be downloaded to an M580 from within CE (CE decrypts in memory).

**When to apply:**
* When project files are stored on file servers with broad access.
* When project files are transmitted (e.g., emailed to a vendor or contractor).
* When project files are stored on removable media.
* When version control repository has broad read access.

**Encryption password governance:**
* Store encryption password in PAM — loss makes the project permanently inaccessible.
* Separate from application write/read password.
* Document which projects are encrypted and with which password (password reference, not the password itself).

## 5.6 Project Governance — Change Management Integration

Every project modification must pass through a formal Change Management process:

```
Change Request (documented business justification; technical description)
        ↓
Risk Assessment (what could go wrong? impact on safety? regulatory impact?)
        ↓
Technical Design (what will change? which logic sections? which parameters?)
        ↓
Development (in feature branch; simulation/staging where available)
        ↓
Peer Review (second engineer reviews all changed logic; documents review)
        ↓
Security Review (ACL changes? Syslog changes? Application password changes?)
        ↓
Approval (authorized approver signs off; recorded in change system)
        ↓
Release Baseline (tagged in version control; release package created)
        ↓
Staging Deployment (if staging M580 or PLCSIM environment available)
        ↓
Production Download (PAM-mediated; application password; session recording)
        ↓
Online Verification (offline/online compare after download)
        ↓
Documentation Update (update asset inventory, release notes, architecture docs)
```

Any modification to production M580 controller logic without this process is a compliance finding and a potential safety issue.

## 5.7 IP Protection for DFBs and EFBs

Derived Function Blocks (DFBs) and Elementary Function Blocks (EFBs) containing proprietary algorithms require specific IP protection:

| Protection | Mechanism | Application |
|-----------|----------|-------------|
| DFB section password | Password per DFB | Vendor-supplied libraries; proprietary algorithms |
| DFB read protection | Part of project read protection | General IP protection |
| EFB distribution control | Control who has access to the EFB library file | Restrict EFB distribution outside organization |
| CE library access control | CE user rights limiting library modification | Prevent unauthorized library changes |

---
---

# 6 — Version Control

---

## 6.1 Version Control Philosophy

Without version control for Control Expert projects, it is impossible to determine:
* Which project version is deployed in each M580 controller.
* Who approved the last production download.
* What changed between the current and previous version.
* Why a change was made.
* Which version to restore after an incident.
* Whether the running controller matches the approved engineering baseline.

Version control is not a software development convenience — it is an engineering governance requirement with direct safety and regulatory implications. **The approved repository defines the intended state of every Modicon M580 under its governance.**

## 6.2 Repository Structure

**Recommended structure:**

```
control-expert-projects/
├── projects/
│   ├── PlantA/
│   │   ├── WaterTreatment/       ← Individual CE project
│   │   └── Dosing/
│   └── PlantB/
│       └── ProcessLine1/
├── libraries/
│   ├── DFB-ProcessControl/       ← Versioned independently
│   ├── DFB-Safety/
│   └── EFB-Custom/
├── documentation/
│   ├── architecture/
│   ├── release-notes/
│   └── approval-records/
└── release-packages/
    └── PlantA-WaterTreatment-v3.2.14/
        ├── project/
        │   └── WaterTreatment-v3.2.14.stu
        ├── libraries/
        │   └── DFB-ProcessControl-v2.1.zip
        ├── firmware-matrix.md
        ├── deployment-instructions.md
        ├── rollback-procedure.md
        ├── release-notes.md
        ├── approval-record.pdf
        └── SHA256SUMS.txt
```

## 6.3 Platform Options

| Platform | Suitability | Notes |
|---------|------------|-------|
| Git + GitLab CE (self-hosted) | **Preferred** | On-premise; branch protection; merge request approval; CI/CD capability |
| Git + Gitea | Suitable | Lightweight; on-premise |
| Azure DevOps Server (on-premise) | Suitable | Enterprise integration; work item linking |
| Git + GitHub Enterprise | Suitable | If organization uses GitHub Enterprise on-premise or private cloud |
| SVN (Subversion) | Acceptable | Simpler; centralized; no branching complexity |
| Shared network drive | **Not recommended** | No audit trail; no branching; error-prone; common but problematic |

**CE project files and Git:**
Control Expert project files (.stu, .zef) are binary files — they do not diff/merge well in Git. Use Git for version tracking and release tagging, not for line-by-line diffs. GitLab/Azure DevOps can store binary files effectively; set appropriate `.gitattributes` to treat .stu/.zef as binary.

## 6.4 Version Numbering

**Recommended format: MAJOR.MINOR.PATCH**

| Component | Trigger | Example |
|-----------|---------|---------|
| MAJOR | Hardware architecture change; new safety function; scope expansion; M580 CPU model change | 2.0.0 → 3.0.0 |
| MINOR | New feature; new device; significant logic addition; new DFB | 3.1.0 → 3.2.0 |
| PATCH | Bug fix; parameter correction; security fix; minor logic correction | 3.2.13 → 3.2.14 |

Version number must be embedded in:
* Control Expert project properties (comment/description field).
* Release notes document.
* Deployment record.
* Version control tag (e.g., `git tag v3.2.14`).
* Asset inventory entry for the M580 (which project version is running).

## 6.5 Branching Strategy

```
main (protected — approved production releases)
 │   Only receives merges from release/* branches
 │   All merges require approval; tagged with version on each merge
 │
 ├── develop
 │    │  Integration branch; merges from feature/* branches
 │    │
 │    ├── feature/CHANGE-123-valve-control-upgrade
 │    ├── feature/CHANGE-124-acl-hardening
 │    └── feature/CHANGE-125-syslog-configuration
 │
 ├── release/3.2.x
 │    │  Release preparation; final testing; release notes
 │    │  Only merges to main when approved for production
 │    └── (merge → main → tag v3.2.14)
 │
 └── hotfix/CHANGE-126-emergency-dosing-fix
      │  Emergency production fix; bypasses develop
      └── (merge → main → tag v3.2.15; backport to develop)
```

**Branch protection rules:**
* `main`: no direct pushes; merge request required; minimum one reviewer approval; tag immutable after creation.
* Release branches: require engineering manager approval.
* All merges to main must have associated approval record.

## 6.6 Engineering Baselines

A baseline is an immutable, approved snapshot of the complete engineering state at a specific point in time. Every production M580 controller corresponds to a specific baseline.

**Baseline components:**

| Component | Required | Notes |
|-----------|----------|-------|
| CE project file (.stu/.zef) | Mandatory | Exported from CE at time of approval |
| Referenced DFB libraries (versions) | Mandatory | Pinned version identifiers |
| M580 firmware version compatibility | Mandatory | Which firmware the project was validated against |
| M580 ACL configuration record | Mandatory | Document ACL rules deployed with this version |
| Security configuration record | Mandatory | App password reference (in PAM), protection settings |
| Release notes | Mandatory | What changed from previous version; change request refs |
| Approval record | Mandatory | Who approved; date; change request number |
| SHA-256 checksums | Mandatory | Hash of all release package files |
| Deployment record template | Mandatory | Blank form for recording download |

Once tagged as a release, the baseline is **immutable** — no modifications, only a new version.

## 6.7 Offline vs. Online Comparison (Control Expert)

Before any production M580 download, compare the offline project (in version control) against the online controller state using Control Expert's comparison function.

**Comparison procedure:**
1. Open approved project version from repository (verified release tag).
2. Connect to production M580 (application write password required for online access).
3. Execute: Control Expert → PLC → Compare → Online/Offline Comparison.
4. Review all differences:
   * **No differences:** Proceed; current controller state matches approved baseline.
   * **Differences found:** Investigate before proceeding.
5. Document comparison result in deployment record.

**If unexpected differences are found:**
This is a potential **security incident** — the running M580 may have been modified without authorization.
* Do not overwrite — preserve evidence.
* Document the specific differences.
* Determine whether any changes were made outside the Change Management process.
* Initiate incident response if unauthorized modification is suspected.
* Only restore approved baseline after incident investigation is complete.

---
---

# 7 — Certificates

---

## 7.1 Certificate Role in Control Expert Environments

Certificates in a Control Expert environment are used primarily for securing M580 services that are configured via Control Expert. The certificates themselves are deployed to M580 controllers as part of the project configuration, not to the Control Expert workstation itself.

| Certificate Type | Purpose | Configured In |
|----------------|---------|--------------|
| M580 HTTPS server certificate | TLS for M580 web server | CE → Services → Web Server → Certificate |
| M580 IPsec device certificate | IKEv2 authentication for IPsec tunnels | CE → Services → IPsec |
| M580 Syslog TLS certificate | TLS transport for Syslog | CE → Services → Syslog → TLS configuration |
| OPC UA server certificate (BMENUA0100) | OPC UA server authentication | Via BMENUA0100 management interface |
| CA certificates | Trust anchors for validating server certificates | CE → Global Security Settings |
| Engineering WS certificate | If mutual TLS is implemented for engineering sessions | OS-level; not in CE |

## 7.2 PKI Requirements for M580 Deployments

All certificates deployed to M580 controllers must meet minimum standards:

| Requirement | Details |
|-------------|---------|
| Root CA | Offline, air-gapped preferred; validity ≤ 20 years |
| Issuing CA | Online; validity ≤ 5 years; accessible from Engineering Zone |
| M580 HTTPS cert validity | 2 years; monitor expiry |
| M580 IPsec cert validity | 3 years; monitor expiry |
| Key algorithm | RSA 2048 minimum; RSA 4096 or ECDSA P-256 preferred |
| Hash algorithm | SHA-256 minimum |
| CRL/OCSP | CRL published and accessible from Engineering Zone |

**Avoid self-signed certificates in production.** The M580 ships with a factory self-signed certificate for HTTPS. This must be replaced with a PKI-issued certificate before production deployment.

## 7.3 Certificate Deployment Workflow (M580 via CE)

```
Generate CSR via CE (or external tool if CE CSR not supported)
        ↓
Submit CSR to enterprise PKI
        ↓
Receive issued certificate from PKI
        ↓
Import certificate into CE project (Services → Web Server → Certificate)
        ↓
Download updated CE project to M580
        ↓
Verify HTTPS connection from management WS (certificate accepted by browser)
        ↓
Record expiry date in certificate management system
        ↓
Configure monitoring alert (90/30/7 days before expiry)
```

## 7.4 Certificate Version Control

Certificate-related configuration in CE (HTTPS certificate, IPsec settings, Syslog TLS) is part of the CE project. Therefore:
* Certificate deployment is a **project change** — it must go through Change Management.
* The CE project version including the certificate configuration is version-controlled.
* The release notes for the version deploying a new certificate must include the certificate fingerprint and expiry date.
* The asset inventory entry for the M580 must reference the current certificate fingerprint and expiry.

## 7.5 Certificate Expiry Impact

Certificate expiry causes operational disruptions in M580 environments:

| Expired Certificate | Impact |
|--------------------|--------|
| M580 HTTPS certificate | Web management interface rejected by browsers; certificate error |
| M580 IPsec certificate | VPN tunnel failure; remote site loses communication |
| M580 Syslog TLS certificate | Syslog session fails; security events no longer forwarded to SIEM |
| OPC UA certificate (BMENUA0100) | OPC UA client connections rejected; historian data collection stops |

**Monitor all M580 certificate expiry dates with alerts at 90, 30, and 7 days before expiry.** Include this in the quarterly security review checklist.

---
---

# 8 — Backup and Recovery

---

## 8.1 Backup Philosophy

Recovery is the objective — not backup creation. A backup that cannot support successful, timely restoration of a trusted engineering environment is insufficient.

**Trusted engineering environment** means:
* Correct CE version installed and functional.
* Approved project accessible and verified.
* Application password known (in PAM).
* Libraries in correct versions.
* Certificates valid and deployed.
* Engineering workstation secure (hardening intact).
* Version control repository accessible.

## 8.2 Complete Backup Scope

| Component | Method | Frequency | Storage |
|-----------|--------|-----------|---------|
| CE project files (.stu/.zef) | Version control + periodic archive export | Continuous (VCS) + after each release | Repository + offline copy |
| DFB libraries | Version control | After modification | Repository |
| EFB library packages | File backup | After installation/update | Backup repository |
| CE user database | File backup (part of WS image) | Monthly + after user changes | WS image + separate backup |
| Application passwords | PAM vault (not file backup) | Managed in PAM | PAM system |
| M580 HTTPS/IPsec certificates (private key) | Encrypted PKCS#12 export | After issuance/renewal | PAM vault |
| CE project documentation | Version control | After modification | Repository |
| Release packages | File backup | After each production release | Repository + offline copy |
| Approval records | File backup | After each approval | Repository + document management |
| Engineering WS disk image | Full disk image | Monthly + after major changes | Backup server |
| CE installer (approved version) | File backup | After version approval | Software repository |
| CE license files | File backup | After installation | Backup repository |
| Version control repository | Repository backup (bare clone) | Daily | Secondary location |

**What is NOT sufficient:**
* Backing up only the .stu project file — insufficient without matching CE version, libraries, and certificates.
* Keeping projects only on the engineering workstation — single point of failure; ransomware target.
* "I remember where everything is" — not a backup strategy; unacceptable in regulated environments.

## 8.3 Backup Security

Engineering backups are high-value targets — a ransomware group that encrypts backups alongside production data eliminates recovery options.

| Control | Requirement |
|---------|-------------|
| Encryption at rest | AES-256 for all backup storage |
| Encryption in transit | TLS 1.2+ for all backup transfers |
| Access control | RBAC; MFA for backup system access |
| Immutability | WORM storage or backup solution with immutability for critical backups |
| Offline copy | At least one copy offline/air-gapped — not reachable from network |
| Integrity verification | Checksum after backup; periodic restore test |
| Ransomware isolation | Backup server on separate network segment; different credentials from engineering WS |
| Audit logging | All backup and restore operations logged |

## 8.4 Recovery Scenarios

### Scenario A: Engineering Workstation Hardware Failure (Non-Cyber)

**Recovery procedure:**
1. Deploy approved workstation image to replacement hardware (or bare metal restore from image backup).
2. Verify OS version and patch level match baseline.
3. Install approved CE version from software repository (same version as was installed).
4. Activate CE licenses.
5. Configure Active Directory domain join and MFA.
6. Clone/restore version control repository.
7. Restore CE user database (from WS image or separate backup).
8. Restore application passwords from PAM (no change needed — they are controller-side).
9. Validate: launch CE; open project; attempt connection to staging M580 (if available).
10. Document recovery in change record.

**Recovery time target:** 4–8 hours with pre-prepared workstation image.

### Scenario B: Project Corruption or Accidental Deletion

**Recovery procedure:**
1. Identify the last approved production baseline (release tag in version control).
2. Restore tagged version from repository.
3. Verify SHA-256 checksums from release package.
4. Verify library versions match release package manifest.
5. Verify CE version compatibility.
6. Review release notes to confirm this is the correct production version.
7. Proceed with validation before any M580 download.

**Recovery time target:** 15–60 minutes.

### Scenario C: Ransomware or Cyber Incident on Engineering Workstation

**Recovery procedure:**
1. **Isolate immediately:** Disconnect engineering WS from Engineering Zone network.
2. **Preserve forensic state:** Image the disk before remediation (forensic evidence).
3. **Notify:** Initiate incident response; notify NÚKIB within 24 hours if regulated; notify management.
4. **Scope assessment:** Determine whether attacker accessed the version control repository, connected to M580 controllers, or performed any downloads.
5. **M580 integrity check:** For every M580 the WS could access, perform offline/online comparison. Differences indicate potential unauthorized modification — treat as incident scope.
6. **Rebuild workstation from trusted image** — do NOT attempt to clean malware from compromised OS.
7. **Install CE from approved installer** (from software repository, not from compromised WS).
8. **Restore project from version control** (verify repository integrity first).
9. **New certificates:** Generate new engineering WS certificates if applicable; update M580 trusted lists.
10. **Rotate application passwords** for all M580 controllers the WS could access; update PAM.
11. **Validate before returning to production engineering.**
12. **Post-incident review:** Root cause; architecture improvements; update IR plan.

**Recovery time target:** 24–72 hours (forensic investigation required before rebuild).

### Scenario D: Version Control Repository Failure

**Recovery procedure:**
1. Restore repository from most recent daily bare clone backup.
2. Verify repository integrity (`git fsck` or equivalent).
3. Compare restored HEAD against known production release tags.
4. Investigate any discrepancy — missing commits may indicate incomplete backup.
5. Restore repository server to Engineering Zone.
6. Re-verify access controls and authentication configuration.

**Recovery time target:** 4–8 hours.

## 8.5 RTO and RPO

Define and document for each engineering environment:

| Scenario | Suggested RTO | Suggested RPO |
|----------|--------------|--------------|
| WS hardware failure | 4–8 hours | 0 (VCS is continuous) |
| Project corruption | 30–60 minutes | Last approved release |
| Ransomware / cyber incident | 24–72 hours | Last approved release |
| VCS repository failure | 4–8 hours | ≤ 24 hours (daily backup) |

RTO and RPO must be reviewed with operations management — engineering WS downtime during a production incident requiring immediate PLC modification is a critical business impact.

## 8.6 Recovery Testing

Recovery procedures that are not periodically tested cannot be relied upon when needed.

**Annual recovery test (minimum):**
1. Rebuild engineering workstation from image on spare hardware.
2. Install CE from software repository.
3. Restore project from version control (specific release tag).
4. Verify CE launches and project opens correctly.
5. Verify CE can connect to a test M580 (or PLCSIM simulation).
6. Verify application password authentication works (from PAM).
7. Document test results; update procedures based on findings.

---
---

# 9 — Patch Management

---

## 9.1 Patch Management Scope

Patch management for the Control Expert engineering environment covers:

| Component | Patch Source | Frequency |
|-----------|-------------|-----------|
| Windows OS | Microsoft; via DMZ-staged WSUS | Monthly; critical within 7 days |
| EcoStruxure Control Expert | Schneider Electric; via DMZ staging | Per Schneider release; after validation |
| CE optional packages (Safety, Drives) | Schneider Electric | With CE updates; compatibility verified |
| DFB/EFB libraries | Schneider Electric; third-party vendors | When vendor releases security fixes |
| Antimalware signatures | Vendor; via DMZ-staged mirror | Near real-time |
| Engineering WS firmware (BIOS/UEFI) | Hardware manufacturer | Annual or on security advisory |
| CE license server software (if separate) | Schneider Electric | With CE updates |

## 9.2 Vulnerability Intelligence Sources

| Source | Content | Access |
|--------|---------|--------|
| Schneider Electric PSIRT | Official SE advisories; all products including CE | https://www.se.com/ww/en/work/support/cybersecurity/security-notifications.jsp |
| CISA ICS-CERT | ICS vendor advisories; includes Schneider | https://www.cisa.gov/ics-advisories |
| NVD | CVE details; search "Control Expert" or "Unity Pro" | https://nvd.nist.gov |
| NÚKIB varování | Czech-market advisories | https://nukib.cz |
| Microsoft Security Updates | Windows OS patching | https://msrc.microsoft.com |

Subscribe to Schneider Electric PSIRT notifications — most reliable source for Control Expert-specific vulnerabilities.

## 9.3 Control Expert Update Process

```
Schneider Electric releases CE update / security advisory
        ↓
Security team reviews: Does it fix a vulnerability? Functional change? Compatibility impact?
        ↓
Download from official Schneider Electric source; verify SHA-256 checksum
        ↓
Review release notes: new features, security fixes, known issues, prerequisites
        ↓
Verify compatibility:
  - CE new version compatible with M580 firmware versions in use?
  - CE new version compatible with M340/Quantum projects if applicable?
  - CE new version requires Windows OS prerequisite changes?
  - Project migration required? (CE version changes sometimes require project migration)
        ↓
Test in staging environment:
  - CE installs without error
  - Existing projects open correctly
  - CE connects to test M580 (if available)
  - Application download/upload works correctly
  - Application allowlist (WDAC/AppLocker) updated for new CE executables
        ↓
Change Management approval
        ↓
Deploy to production engineering workstations
  (during engineering maintenance window if possible)
        ↓
Verify: CE launches; projects open; M580 connectivity confirmed
        ↓
Update software inventory (CE version updated)
        ↓
Take new workstation image backup
```

## 9.4 CE Version Compatibility with M580 Firmware

Control Expert and M580 firmware versions have interdependencies. Before updating CE:
* Check the Schneider Electric compatibility matrix (available on SE Industry Support portal).
* Determine whether M580 firmware must also be updated alongside CE.
* Assess whether project migration is required (CE version changes can alter project file format).
* Test the full chain (CE version + M580 firmware + project file) in staging.

**CE update without firmware update:** Usually possible for minor CE versions. Major CE version updates may require minimum M580 firmware versions.

**CE update requiring project migration:** Some CE version upgrades require migrating project files to a new format. Migration is typically automatic but:
* Verify migration in staging with actual project files before production.
* Keep old CE version available to open unmigrated backups (do not uninstall until migration is verified).
* Include project migration in the change record.

## 9.5 Windows OS Patch Management

Windows patching for engineering workstations follows the same process as any privileged workstation, with OT-specific considerations:

* Patches are pulled from DMZ-staged WSUS/SCCM — not from Microsoft Update directly.
* Test each Windows patch batch in a staging engineering WS before production deployment.
* Some Windows updates have affected CE functionality (particularly .NET framework and Visual C++ runtime updates). Monitor CE release notes for known OS patch conflicts.
* Critical security patches (CVSS ≥ 9.0, particularly if RCE) should be deployed within 7 days even without full staging test.

## 9.6 Compensating Controls for Unpatched Vulnerabilities

When CE or OS cannot be immediately patched (operational constraints, pending staging validation):
* Document the vulnerability in the risk register.
* Assess whether the vulnerability is exploitable given the engineering WS network isolation.
* Implement additional network controls if the vulnerability is network-accessible.
* Enhance monitoring for exploitation indicators (EDR behavioral detection).
* Formal risk acceptance with management signature.
* Review monthly until patched.

---
---

# 10 — Best Practices

---

## 10.1 Core Engineering Principles

1. **Engineering workstation is privileged infrastructure** — treat it as a Tier-0 administrative system.
2. **The project is the source of truth** — the running M580 implements the approved project; never the reverse.
3. **Identity before access** — every engineering action is attributed to a named individual.
4. **Governance before configuration** — Change Management precedes every modification.
5. **Least privilege** — every account has only the access required for its role.
6. **Defense in depth** — no single control is relied upon exclusively.
7. **Recovery by design** — backup and recovery capability is designed and tested, not assumed.
8. **Continuous monitoring** — engineering activity is logged; anomalies are detected and investigated.

## 10.2 Reference Architecture Summary

```
╔══════════════════════════════════════════════════════════════════════╗
║  ENTERPRISE IT                                                       ║
║  Active Directory, MFA, PKI, PAM, SIEM                              ║
╠══════════════════════╦═══════════════════════════════════════════════╣
║  ENGINEERING DMZ     ║   ENGINEERING ZONE                           ║
║  Jump Server         ║   Engineering WS 1 (CE; dedicated; MFA)      ║
║  Patch Staging       ║   Engineering WS 2 (CE; dedicated; MFA)      ║
║  AV Update Mirror    ║   CE Repository Server (GitLab on-premise)   ║
║  Log Relay → SIEM    ║   PLCSIM/Simulation (isolated; no prod PLC)  ║
║  Remote Access GW    ║                                               ║
╠══════════════════════╩═══════════════════════════════════════════════╣
║  CONDUIT: Allowlist (TCP 502 Modbus/UMAS, TCP 443 HTTPS)            ║
╠══════════════════════════════════════════════════════════════════════╣
║  CONTROL ZONE                                                        ║
║  SCADA Servers, HMI Stations, Historian                              ║
╠══════════════════════════════════════════════════════════════════════╣
║  CONDUIT: Allowlist (Modbus TCP, EtherNet/IP; ACL on M580)          ║
╠══════════════════════════════════════════════════════════════════════╣
║  FIELD ZONE                                                          ║
║  Modicon M580 PLCs, Ethernet RIO, X80 I/O, Altivar Drives          ║
╚══════════════════════════════════════════════════════════════════════╝
```

## 10.3 Common Mistakes — Complete Reference

| Mistake | Risk | Correct Practice |
|---------|------|----------------|
| Using engineering WS for email / internet | Phishing → ransomware → malicious M580 download | Dedicated WS; no email; no general browser |
| Shared engineering accounts ("admin", "unity") | No audit trail; insider threat undetectable | Named individual accounts; CE user management |
| No application write password on M580 | Any CE user with network access can download arbitrary logic | Set write password at commissioning; store in PAM |
| Projects stored only on WS local drive | Single point of failure; ransomware destroys all copies | Version control repository; off-WS storage |
| No version control; folder copies as "backup" | No audit trail; cannot detect unauthorized changes; cannot reproduce releases | Git repository; formal versioning; release tags |
| Download to production without offline compare | Configuration drift undetected; unauthorized M580 changes missed | Mandatory offline/online compare before every download |
| Online modifications ("quick fix") outside CM | Undocumented change; version control diverges from running M580 | All modifications through Change Management; commit to VCS |
| CE installed on office laptop (dual-purpose) | Office laptop compromise = M580 programming capability | Dedicated, hardened, monitored engineering WS |
| UMAS (CE engineering protocol) accessible from SCADA | SCADA compromise = M580 programming capability | ACL on M580: TCP 502 from engineering WS IP only |
| No PAM for production downloads | Single person can deploy unauthorized logic without accountability | PAM for download sessions; four-eyes for critical systems |
| Self-signed HTTPS certificate on M580 | No PKI governance; no revocation path | Enterprise PKI certificates on all M580 systems |
| CE update pulled directly from internet on WS | Engineering WS directly reaches internet | Updates via DMZ staging server; WS has no internet access |
| No CE user management; all users have full rights | Any CE user has all capabilities | CE RBAC; engineer ≠ administrator ≠ safety engineer |
| No application read password | Application uploaded from M580 by anyone → IP theft; process reconnaissance | Read password on all sensitive projects |
| Vendor remote access: permanent | Persistent attack surface | Time-limited; MFA; jump server; revoke after session |
| No monitoring of engineering WS | Security events undetected; no forensic evidence after incident | OS audit log → SIEM; OT IDS on engineering network |
| Recovery not tested | Recovery procedure fails when needed | Annual recovery test; documented results |

## 10.4 NIS2 / IEC 62443 Compliance Mapping

| Regulatory Requirement | Control Expert Engineering Practice |
|-----------------------|-------------------------------------|
| NIS2 Art. 21(2)(i) — Asset management | Engineering WS in asset inventory; CE version; project version; certificate expiry |
| NIS2 Art. 21(2)(i) — Access control | CE RBAC; MFA; PAM for downloads; named accounts |
| NIS2 Art. 21(2)(j) — MFA | MFA on all engineering WS and remote access sessions |
| NIS2 Art. 21(2)(h) — Cryptography | PKI for M580 HTTPS/IPsec certs; project encryption; BitLocker on WS |
| NIS2 Art. 21(2)(e) — Vulnerability management | CE patch management; Schneider PSIRT monitoring; Windows patching |
| NIS2 Art. 21(2)(b) — Incident handling | OS audit log → SIEM; incident response scenario for CE compromise |
| NIS2 Art. 21(2)(c) — Backup/recovery | Complete backup scope; annual recovery test; RTO/RPO defined |
| NIS2 Art. 21(2)(d) — Supply chain | CE from official Schneider sources; DFB library governance; vendor access controls |
| IEC 62443-3-3 SR 1.1 | CE user management + MFA + application write password = multi-layer authentication |
| IEC 62443-3-3 SR 2.1 | CE RBAC rights; PAM for privileged operations; application write password |
| IEC 62443-3-3 SR 3.1 | Version control; offline/online compare; project encryption |
| IEC 62443-3-3 SR 6.1 | OS audit log → SIEM; monitoring of CE sessions and downloads |
| IEC 62443-3-3 SR 7.3 | Version-controlled backups; annual recovery test; RTO/RPO |

---

# Checklists / Kontrolní seznamy

---

## Checklist 1: Zprovoznění Engineering Workstation — bezpečnostní baseline

Použij při nasazení nové nebo obnovené Control Expert engineering workstation.

### Česky

**Fyzická bezpečnost a dedikace**
- [ ] Workstation je dedikovaná výhradně pro OT engineering — žádné kancelářské využití
- [ ] Není nainstalován e-mailový klient, messenger ani kancelářský balíček (vyjma požadavků CE)
- [ ] Fyzický přístup k workstation je řízen; laptop je při nepoužívání zamčen

**OS hardening**
- [ ] Nainstalován podporovaný OS (Windows 10/11 Pro nebo Server); aktuální záplaty
- [ ] Záplaty aplikovány přes DMZ staging server, ne přímo z internetu
- [ ] Zbytečné Windows funkce odebrány; SMBv1 deaktivován
- [ ] BitLocker aktivován na všech discích
- [ ] AutoRun a AutoPlay deaktivovány
- [ ] Windows Firewall povolen a nakonfigurován
- [ ] Application allowlisting nakonfigurováno a otestováno (CE spouštění ověřeno)

**Identita a přístup**
- [ ] Workstation je zapojena do Active Directory
- [ ] MFA nakonfigurováno pro přihlášení k OS
- [ ] Individuální pojmenované uživatelské účty — žádné sdílené
- [ ] CE user management nakonfigurován; role odpovídají pracovním pozicím
- [ ] Application write password nastaven na každém spravovaném M580; uložen v PAM
- [ ] PAM nakonfigurován pro privilegované engineering sessions

**Síťová konfigurace**
- [ ] Workstation je ve správné VLAN (Engineering Zone)
- [ ] Není přímé propojení do IT sítě ani na internet
- [ ] Firewall pravidla ověřena: CE → M580 TCP 502 a 443 povoleno; ostatní deny
- [ ] NTP synchronizace nakonfigurována na interní NTP server

**Aplikace a media**
- [ ] Nainstalována schválená verze CE z oficiálního Schneider Electric zdroje
- [ ] Jsou nainstalovány pouze vyžadované CE komponenty
- [ ] USB port management nakonfigurován; neschválená zařízení blokována
- [ ] Antimalware nasazen a aktualizuje se z DMZ mirror

**Zálohy a VCS**
- [ ] Git repository naklonován na workstation nebo přístupný ze síťového repository serveru
- [ ] Počáteční disk image workstation vytvořen a uložen do backup repository
- [ ] Recovery postup zdokumentován a dostupný mimo workstation

**Monitoring**
- [ ] Windows Security Event Log forwarding nakonfigurováno na SIEM nebo log relay
- [ ] Workstation zahrnuta v inventáři OT aktiv (hardware, CE verze, assigned M580 controllers)

---

## Checklist 2: Schválení a nasazení projektu do výroby

Použij před každým stažením projektu do produkčního M580 řídicího systému.

### Česky

**Příprava projektu**
- [ ] Projekt je verzován dle schématu MAJOR.MINOR.PATCH
- [ ] Verze projektu odpovídá schválenému release tagu v repository
- [ ] Release package je kompletní (projekt, knihovny, firmware matice, release notes, schválení)
- [ ] SHA-256 checksums release package ověřeny
- [ ] Projekt otevřen z repository (ne z lokální zálohy nebo staré složky)

**Review a schválení**
- [ ] Peer review změn proběhl (jiný inženýr než autor)
- [ ] Security review proběhl (změny ACL? Syslog konfigurace? Hesla? Application protection?)
- [ ] Změna je schválena v Change Management systému (change request evidován)
- [ ] Approval record je podepsán a uložen v dokumentaci

**Kompatibilita**
- [ ] Verze CE na engineering WS je kompatibilní s projektem
- [ ] Verze firmware M580 je kompatibilní s projektem (dle firmware matice v release package)
- [ ] Verze DFB knihoven odpovídají release package manifestu

**Online ověření před stažením**
- [ ] Proveden offline vs. online compare (CE → PLC → Compare)
- [ ] Výsledek compare zdokumentován (nulové rozdíly nebo nalezené rozdíly: vysvětlení a schválení)
- [ ] Pokud nalezeny neočekávané rozdíly: ZASTAVIT — zahájit incident investigation

**Nasazení**
- [ ] Nasazení probíhá z PAM-schválené session (application password z PAM)
- [ ] Session recording aktivní (video + keystroke) pro kritické systémy
- [ ] Application write password zadán (ověřeno, že M580 je chráněn)
- [ ] Rollback postup dostupný a otestován před nasazením

**Po nasazení**
- [ ] Post-download offline/online compare proveden — ověření správnosti downloadu
- [ ] Deployment record vyplněn (datum, verze, engineering WS, operátor, schvalovatel, SHA-256)
- [ ] Deployment record uložen do repository nebo DMS
- [ ] Asset inventory aktualizován (verze projektu v M580 aktualizována)

---

## Checklist 3: Příprava na bezpečnostní incident — engineering prostředí

Použij pro ověření incident response připravenosti engineering prostředí.

### Česky

**Detekce a izolace**
- [ ] Jsou OS audit logy engineering WS přeposílány do SIEM v reálném čase?
- [ ] Je nakonfigurováno upozornění na off-hours CE přihlášení nebo neočekávané M580 připojení?
- [ ] Je definováno, kdo je kontaktován při podezření na kompromitaci engineering WS?
- [ ] Je postup okamžité síťové izolace engineering WS zdokumentován a znám?

**Scope assessment**
- [ ] Je seznam M580 řídicích systémů přístupných z každé engineering WS zdokumentován?
- [ ] Je postup offline/online compare pro rychlé ověření integrity M580 programů zdokumentován?
- [ ] Je postup zálohy (upload ze M580) pro zachování forenzních důkazů znám?

**Recovery**
- [ ] Existuje záloha engineering WS image (ne starší než 1 měsíc)?
- [ ] Je CE installer dostupný v software repository (ne na internetu)?
- [ ] Jsou application passwords všech spravovaných M580 v PAM (ne ztraceny spolu s WS)?
- [ ] Je postup rotace application passwords po incidentu zdokumentován?
- [ ] Byl proveden recovery test v posledních 12 měsících?

**NÚKIB notifikace**
- [ ] Je definováno, kdo kontaktuje NÚKIB (24h early warning, 72h full report)?
- [ ] Je kontakt na NÚKIB dostupný fyzicky mimo OT systémy?
- [ ] Je koordinace se sektorovým regulátorem (pokud relevantní) zdokumentována?

---

# AI Guidance

When answering questions about Schneider Electric EcoStruxure Control Expert:

* Treat Control Expert as the **Root of Trust for Modicon automation systems** — compromise of CE is equivalent to compromise of every M580 it manages.
* **Platform specificity:** Control Expert is the Schneider Electric engineering tool for Modicon M580, M340, Quantum and Premium. It is NOT equivalent to or interchangeable with Siemens TIA Portal in terms of features — they have different security architectures. CE has no equivalent to Siemens UMAC (controller-level user management via TIA Portal); the primary controller-level access control for M580 is the **application write/read password** plus **ACL (configured in CE)**.
* **The application write password is the single most critical native security control** — factory default is no password; any CE user with network access to an unprotected M580 can download arbitrary logic.
* **UMAS protocol** (Control Expert engineering protocol over TCP 502) must be restricted via M580 ACL to the engineering workstation IP address only — not accessible from SCADA or historian.
* The engineering workstation must be **dedicated** — no shared use with office tasks.
* **Version control is mandatory** — not optional. Production M580 controllers must always correspond to an approved, tagged baseline in version control.
* **Offline/online compare before every production download** is mandatory — unexpected differences are a potential security incident.
* Recommend PAM for all production download sessions — application passwords from PAM vault; session recording active.
* Reference [Modicon-M580.md](#) for M580 controller-specific security configuration (ACL, HTTPS, Syslog, firmware).
* Reference [IEC62443.md](#) for zone/conduit architecture context.
* Reference [Czech-Cybersecurity-Act.md](#) for regulatory compliance context.
* Schneider Electric PSIRT (not Siemens ProductCERT) is the vulnerability intelligence source for CE.

---

# Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | 2026-06 | Initial release (7 separate documents) |
| 1.1.0 | 2026-06-29 | Consolidated into single document; major expansion: CE supported controller matrix; security role of CE (what CE configures on M580); ISA-95 context; multi-layer authentication architecture (OS+CE+app password+PAM); CE user management rights table; PAM capabilities and application password vaulting; vendor access controls; identity lifecycle table; threat model with M580 ACL attack vector; 9-layer defense-in-depth model; Engineering Zone architecture diagram with firewall allowlist table; IEC 62443 FR mapping; hardening phases (dedicated WS, OS, allowlisting, CE application, removable media, network, endpoint protection); application write/read password governance; section-level password protection; project encryption; project governance change management flow; version control repository structure; CE binary file handling in Git; branching strategy diagram; offline/online comparison security incident procedure; release package structure; PKI requirements for M580 certificates; certificate deployment workflow; certificate version control; certificate expiry impact table; complete backup scope table; recovery scenarios (4 scenarios); RTO/RPO table; annual recovery test procedure; CE update process; CE/M580 compatibility considerations; compensating controls; NIS2/IEC 62443 compliance mapping table; common mistakes reference (18 items); reference architecture diagram; 3× checklist in Czech language |
