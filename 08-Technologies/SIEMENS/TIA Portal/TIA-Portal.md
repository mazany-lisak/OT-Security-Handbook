---
title: Siemens TIA Portal — Security Architecture and Engineering Governance
category: Engineering Platform
version: 1.1.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-06-29
review_cycle: Annual
---

# Purpose

This document provides a comprehensive architectural and security reference for Siemens Totally Integrated Automation (TIA) Portal from the perspective of an OT Security Architect.

It consolidates guidance across the complete TIA Portal security domain, covering the engineering platform itself, the workstation it runs on, the projects it manages, and the controller environments it configures and maintains.

TIA Portal must be understood as the **Root of Trust** for Siemens automation systems. Compromise of the engineering platform is operationally equivalent to compromise of the controllers it manages — including any PLC, HMI, drive or safety system within its scope.

This document is intended for: OT Security Architects, Automation Engineers, System Integrators, OT Administrators, and Disaster Recovery Teams.

> **Regulatory context:** TIA Portal security directly supports compliance with IEC 62443 (Series 2 and 3), NIS2 Article 21 obligations, and the Czech Cybersecurity Act (Decrees 409/410). The engineering workstation and its governance processes are auditable assets — not merely tools.

---

# Related Documents

| Document | Relationship |
|----------|-------------|
| [IEC62443-Overview.md](#) | Engineering security framework — TIA Portal implementation satisfies IEC 62443 system and component requirements |
| [NIS2.md](#) | EU governance framework — TIA Portal governance supports Article 21 risk management obligations |
| [Czech-Cybersecurity-Act.md](#) | Czech national obligations — engineering platform security is within scope of Decree 409/410 |
| [ISA95.md](#) | Functional reference model — TIA Portal operates at ISA-95 Level 2 (control) with interfaces to Level 1 (controllers) |

---

# Document Structure

| Chapter | Content |
|---------|---------|
| [1 — Overview](#1--overview) | Platform architecture, role, supported technologies |
| [2 — Identity and Access](#2--identity-and-access) | IAM, UMAC, UMC, MFA, PAM, vendor access |
| [3 — Security Architecture](#3--security-architecture) | Threat model, defense in depth, security zones |
| [4 — Hardening](#4--hardening) | OS hardening, application hardening, removable media, monitoring |
| [5 — Project Protection](#5--project-protection) | Know-how protection, copy protection, access control, IP |
| [6 — Version Control](#6--version-control) | Baseline governance, branching, release management |
| [7 — Certificate Management](#7--certificate-management) | PKI, OPC UA certificates, Secure PG/PC, lifecycle |
| [8 — Backup and Recovery](#8--backup-and-recovery) | Backup scope, recovery scenarios, DR integration |
| [9 — Patch Management](#9--patch-management) | Vulnerability management, TIA Portal updates, firmware |
| [10 — Best Practices](#10--best-practices) | Reference architecture, checklists, common mistakes |

---

---

# 1 — Overview

---

## 1.1 Platform Role

Siemens TIA Portal (Totally Integrated Automation Portal) is the unified engineering platform for the Siemens SIMATIC automation ecosystem. It provides a single integrated environment for engineering, configuration, commissioning, diagnostics and lifecycle management of Siemens controllers, HMIs, drives and safety systems.

From a security perspective, TIA Portal must not be regarded as merely a PLC programming application. It is the **authoritative engineering authority** and **primary security configuration platform** for every Siemens automation system it manages.

The engineering project created in TIA Portal — not the running PLC — is the authoritative source of truth for the automation system. The engineering workstation — not the controller — is the Root of Trust.

**Implications of this framing:**

* If TIA Portal is compromised, the integrity of every controller it manages is in question.
* Unauthorized modification of a TIA Portal project is operationally equivalent to unauthorized modification of the physical control system.
* The security of the automation system is bounded by the security of its engineering environment.

## 1.2 Supported Platforms

TIA Portal supports engineering for the following Siemens product families:

**Controllers (SIMATIC):**

| Product Family | Notes |
|---------------|-------|
| S7-1200 | Compact controller; current generation; supports TLS, access levels |
| S7-1500 | High-end controller; full security feature set (Secure PG/PC, OPC UA, HTTPS, integrity protection) |
| S7-1500 Software Controller | PC-based runtime; same security model as S7-1500 hardware |
| ET 200SP Open Controller | Distributed controller with S7-1500 security capabilities |
| S7-300 / S7-400 | Legacy; minimal native security; end of mainstream support; reliant on network isolation |
| WinAC (legacy) | Software PLC; Windows-based; end of life |
| SIMATIC Drive Controller | S7-1500 core with integrated SINAMICS drive |

**HMI Platforms:**

| Platform | Notes |
|---------|-------|
| Basic Panels | Entry-level; limited security features |
| Comfort Panels | Full TIA Portal integration; HTTPS; user management |
| Unified Comfort Panels | WinCC Unified Runtime; modern security architecture |
| WinCC Unified PC Runtime | PC-based; Windows; full security hardening applicable |

**Drives:**

| Platform | Notes |
|---------|-------|
| SINAMICS G/S series | Via Startdrive integration; drive security configuration |
| SIMOTION | Motion control platform |

**Safety:**

| Platform | Notes |
|---------|-------|
| F-CPU (SIMATIC Safety) | Fail-safe CPU integrated in S7-1500 or ET 200SP |
| Fail-safe I/O | Distributed safety I/O modules |
| Safety Integrated | Drive safety integration |

## 1.3 TIA Portal Versions and Lifecycle

TIA Portal releases are versioned by year (e.g., V18, V19, V20). Each version supports specific hardware and firmware combinations. Maintaining version compatibility is an engineering governance requirement.

**Key lifecycle considerations:**
* Siemens publishes a compatibility matrix — TIA Portal version must match hardware generation and firmware version.
* Running an unsupported TIA Portal version on a production workstation creates vulnerability exposure.
* Version upgrades require project migration — governed through Change Management.
* Multiple TIA Portal versions may coexist on a workstation if legacy project support is required (with associated security trade-offs — larger attack surface).

## 1.4 Engineering Workflow

```
Requirements Analysis
        ↓
System Architecture (Hardware, Network, Safety, Security)
        ↓
Hardware Configuration (Device catalog, I/O assignment, network topology)
        ↓
Application Development (Ladder, FBD, SCL, SFC programs; HMI screens)
        ↓
Simulation (PLCSIM Advanced, SIMIT)
        ↓
Security Configuration (UMAC, UMC, Secure PG/PC, OPC UA security)
        ↓
Peer Review + Security Review
        ↓
Testing (FAT, SAT)
        ↓
Approval + Release Baseline
        ↓
Commissioning (Online → Compare → Download)
        ↓
Production Operation
        ↓
Maintenance (Changes via Change Management process)
        ↓
Archive / Decommissioning
```

Every lifecycle phase should produce auditable engineering artefacts. Security review is not a final step — it must be integrated throughout.

## 1.5 Security Role of TIA Portal

TIA Portal is responsible for configuring and maintaining numerous security functions across the automation system:

| Function | TIA Portal Role |
|---------|----------------|
| User Management & Access Control (UMAC) | Defines user roles, rights, and passwords for each controller |
| Central User Management (UMC) | Integrates enterprise identity into controller access management |
| Access Protection Levels | Configures PLC access levels (No restriction, HMI access only, Read access, Full access, PUT/GET protection) |
| OPC UA security configuration | Configures OPC UA server security mode (None / Sign / Sign and Encrypt) and certificate trust |
| Secure PG/PC Communication | Configures certificate-based mutual authentication between engineering workstation and S7-1500 |
| HTTPS / web server configuration | Configures TLS for integrated web servers on S7-1500 and HMI |
| Know-how Protection / Copy Protection | Applies IP protection to PLC program blocks |
| Firmware management | Downloads controller firmware via the engineering environment |
| Secure Boot configuration | Configures Integrity Monitoring features on supported S7-1500 hardware |

The security of all of these functions is only as strong as the security of TIA Portal itself.

## 1.6 ISA-95 Context

In the ISA-95 functional hierarchy, TIA Portal and its workstation operate at:

* **ISA-95 Level 2** — Engineering workstations and SCADA/DCS supervisory platforms.
* **ISA-95 Level 1 interface** — Direct connectivity to PLCs, DCS controllers, drives and HMIs.

This dual-level footprint makes TIA Portal a particularly sensitive asset: it spans the boundary between supervisory control and field control, with write access to Level 1 devices. In IEC 62443 terms, the engineering workstation must be placed in a **dedicated Engineering Zone** with its own Security Level Target (typically SL 2), separate from both the Control Zone (SCADA) and the Field Zone (PLCs).

See [ISA95.md](#) and [IEC62443-Overview.md](#) for context.

---

---

# 2 — Identity and Access

---

## 2.1 Identity Philosophy

Identity is the primary control for the engineering environment. Every engineering action — project modification, controller download, firmware update, online change — must be attributable to a specific, authenticated individual.

**Shared engineering accounts are never acceptable.** They defeat audit trails, enable undetected insider actions, and make incident forensics impossible.

## 2.2 Account Architecture

**Recommended account layers for TIA Portal environments:**

| Layer | Mechanism | Purpose |
|-------|-----------|---------|
| Enterprise identity | Active Directory (or equivalent LDAP) | Authentication foundation; centrally managed |
| Engineering workstation accounts | AD-integrated local accounts | OS-level access; MFA enforced |
| TIA Portal UMAC | TIA Portal User Management | Per-project role assignments |
| UMC (Central User Management) | TIA Portal + SIMATIC S7-1500 | Enterprise identity into controller |
| Controller access levels | S7-1500 access protection | Fine-grained controller operation control |
| Vendor/contractor accounts | Temporary, time-limited AD accounts | Governed third-party access |

## 2.3 User Management and Access Control (UMAC)

UMAC is TIA Portal's built-in user management system. It defines who can perform which engineering operations within TIA Portal and, where supported, on connected controllers.

**UMAC roles — recommended structure:**

| Role | Typical Rights | Notes |
|------|---------------|-------|
| Viewer | Read-only access to project; no downloads | For audit, review, monitoring personnel |
| Operator | HMI interaction only | Cannot modify engineering configuration |
| Engineer | Program development, testing in PLCSIM | Cannot download to production controllers |
| Senior Engineer | Download to non-production environments; configuration changes | Requires review before production |
| Release Engineer | Download to production controllers; firmware management | Restricted pool; PAM integration recommended |
| Administrator | Full UMAC management; security configuration | Maximum two persons; break-glass procedure |

**Key UMAC configuration requirements:**
* All accounts must be individually named — no role-based shared accounts (e.g., no "engineer1" shared among multiple people).
* Password complexity and rotation policies must be enforced within UMAC (or better: governed at AD level with UMAC integrated).
* UMAC access logs should be exported to centralized logging.

## 2.4 Central User Management (UMC)

UMC extends identity management from TIA Portal into supported SIMATIC S7-1500 controllers. With UMC, an S7-1500 can authenticate users against an enterprise identity store, enabling consistent role-based access control across the engineering platform and the controller itself.

**UMC architecture:**

```
[Active Directory / LDAP]
        │
[UMC Server (SINEC Security Monitor or standalone)]
        │
[TIA Portal — UMC integration]
        │
[S7-1500 — authenticates user via UMC]
```

**Benefits of UMC vs. standalone UMAC:**
* Centralized account lifecycle management — deprovisioning in AD immediately affects controller access.
* Consistent identity governance across workstation, TIA Portal and controllers.
* Audit trail linking user identity to specific controller operations.
* Eliminates controller-local accounts that persist after personnel changes.

**UMC implementation notes:**
* UMC requires S7-1500 firmware ≥ V2.9 and TIA Portal V17+.
* UMC server requires a separate Windows host (can be virtualized); must be within the Engineering Zone.
* UMC server must be highly available if controllers depend on it for authentication.
* Consider fallback local accounts for emergency access (break-glass procedure) — document and protect with PAM.

## 2.5 Controller Access Levels (S7-1500)

The S7-1500 implements four access protection levels, configured within TIA Portal:

| Level | What Is Allowed Without Password | Security Implication |
|-------|----------------------------------|---------------------|
| No restriction | All access | Never use in production |
| HMI access | Read process values, acknowledge alarms via HMI | Minimum for any connected controller |
| Read access | Online monitoring, read memory | Suitable where monitoring-only access is required |
| Full access | All online operations including write and download | Default with password protection; recommended for most production systems |
| Full access + PUT/GET protection | As above, plus PUT/GET communication blocked | Highest protection; blocks S7comm legacy read/write |

**Recommendation:** All production S7-1500 controllers must be configured with Full access protection or Full access + PUT/GET protection. The "No restriction" level must never be used on any networked production controller.

**S7-300/S7-400 access limitation:**
Legacy S7-300 and S7-400 PLCs do not support UMAC, UMC or modern access levels. Their access protection is a simple CPU password that provides minimal resistance to anyone with network access to the controller. Primary security for these devices is network isolation — they must be in a firewalled field zone with no direct access from engineering networks other than through an approved conduit.

## 2.6 Multi-Factor Authentication (MFA)

MFA must be enforced for:

* All engineering workstation logins (OS level).
* All remote access to the engineering workstation or engineering zone network.
* All privileged administrative operations (PAM-integrated where possible).

**MFA options for OT environments:**

| Method | Suitability | Notes |
|--------|------------|-------|
| Hardware token (FIDO2, YubiKey) | Preferred | Phishing-resistant; works offline; no mobile dependency |
| Authenticator app (TOTP) | Acceptable | Requires mobile device; time-sync dependency |
| Smart card / PIV | Preferred for high-security environments | AD integration; requires card reader on workstation |
| SMS-based OTP | Not recommended | SIM-swap risk; mobile network dependency; avoid |

**MFA for vendor/contractor access:**
Remote vendor access to the engineering environment must always require MFA — without exception. Time-limited credentials plus MFA is the minimum control for any vendor remote session. See Section 2.7.

## 2.7 Privileged Access Management (PAM)

Engineering workstations host privileged access to industrial controllers. The engineering role — particularly "Release Engineer" or "Administrator" — is a high-privilege role that must be governed through PAM.

**PAM capabilities for TIA Portal environments:**

| Capability | Implementation | Benefit |
|-----------|---------------|---------|
| Session recording | Record all privileged TIA Portal sessions (video + keystroke) | Forensic evidence; accountability |
| Credential vaulting | PAM vault issues session credentials; engineer never sees password | Prevents credential sharing and exfiltration |
| Just-in-time (JIT) access | Elevated access granted only for approved time window | Reduces standing privilege exposure |
| Approval workflow | Download-to-production requires approval in PAM system | Four-eyes principle for controller downloads |
| Session termination | PAM can terminate active sessions on demand | Incident response capability |

**PAM integration options for TIA Portal:**
* CyberArk, Delinea (Thycotic), BeyondTrust, or equivalent enterprise PAM solutions.
* Jump server in Engineering Zone with PAM-mediated access.
* For remote vendor access: PAM-managed time-limited credentials issued per session.

## 2.8 Vendor and Contractor Access

Vendor remote access to TIA Portal is a significant attack vector — vendor credential theft, vendor-side compromise, and unauthorized access during a live session are all realistic threats.

**Required controls for vendor remote access:**

| Control | Requirement | Notes |
|---------|-------------|-------|
| Time-limited credentials | Maximum duration = estimated task + buffer | Auto-expire; no permanent vendor accounts |
| MFA | Mandatory without exception | Hardware token strongly preferred |
| Approved device | Vendor must use an approved, managed device | No personal laptops; enforce via network access control where possible |
| Jump server | All access via jump server in Engineering Zone DMZ | No direct VPN access to engineering LAN |
| Session recording | Full video + keystroke recording | Stored outside OT network; retained minimum 12 months |
| Monitored session | Internal coordinator available to monitor live for critical systems | Risk-based: always for SIS, on-demand for standard |
| Access scope | Network access limited to specific target systems only | Not the entire engineering network |
| Post-session | Credentials revoked immediately; PLC program integrity verified | Automated revocation where possible |
| Contractual obligations | Incident reporting; approved device; no subcontracting without approval | In every supplier contract |

---

---

# 3 — Security Architecture

---

## 3.1 Threat Model

Understanding the threat landscape for TIA Portal is the prerequisite for designing proportionate controls.

**Threat actors and their relevance to the engineering environment:**

| Threat Actor | Attack Path | Target | Realistic Probability |
|-------------|------------|--------|----------------------|
| External attacker — opportunistic | Ransomware via phishing → engineering WS | Encrypt engineering files; disrupt engineering capability | High — general ransomware campaigns regularly hit OT support systems |
| External attacker — targeted | Spearphishing engineer → TIA Portal compromise | Malicious PLC program deployment | Medium — nation-state and criminal groups actively target OT |
| Compromised vendor | Vendor VPN credential → engineering WS → controller | Unauthorized firmware or program change | Medium — documented in multiple ICS incidents |
| Insider — malicious | Direct TIA Portal access → controller download | Sabotage, IP theft, unauthorized change | Low-Medium — access controls and audit trails are primary mitigations |
| Supply chain — software | Trojanized TIA Portal update or add-in | Persistent malware in engineering environment | Low but critical impact if realized |
| USB malware | USB device on engineering WS → SCADA/DCS | Malware introduction bypassing network controls | High — most common malware vector in OT environments |

**Attack consequence for each target:**

| Attack | Consequence |
|--------|-------------|
| Unauthorized PLC program download | Production disruption, physical damage, safety incident |
| Ransomware on engineering WS | Engineering capability loss; potentially weeks of recovery |
| Credential theft from engineering WS | Persistent attacker presence; delayed discovery |
| Certificate theft from engineering WS | Impersonation of trusted engineering workstation |
| Malicious firmware deployment | Controller compromise surviving OS reinstall |
| Know-how theft via project exfiltration | Intellectual property loss; competitive damage |

## 3.2 Security Architecture — Layered Model

Defense in depth for TIA Portal environments consists of nine layers. A compromise of any single layer must not result in compromise of the automation system.

```
LAYER 1: Physical Security
├── Engineering workstation in locked room or restricted area
├── Access badge readers for engineering areas
├── Screen lock on unattended workstation (automatic timeout)
├── Locked cabinet for portable engineering laptops when not in use
└── USB port physical blockers (where removable media policy requires)

LAYER 2: Network Isolation (Engineering Zone)
├── Dedicated Engineering Zone — separate from Control Zone and IT
├── Firewall with allowlist rules between Engineering Zone and Control Zone
├── No direct Engineering Zone → IT connectivity
├── Engineering Zone → Internet: blocked (updates via DMZ staging server)
└── Remote access: only via jump server in DMZ; MFA mandatory

LAYER 3: Operating System Hardening
├── Windows Server/Pro (supported, current patch level)
├── CIS Benchmark baseline (OT-adapted)
├── Windows Firewall enabled (secondary control behind network firewall)
├── Application whitelisting (Windows AppLocker or Sophos, Carbon Black)
├── BitLocker disk encryption
└── Disable: AutoRun, AutoPlay, unused Windows features, IIS, SMBv1

LAYER 4: Identity and Authentication
├── Named individual accounts only — no shared accounts
├── Active Directory integration
├── MFA for all logins (hardware token preferred)
├── UMAC configured for role-based TIA Portal access
├── UMC integration for S7-1500 controller identity where supported
└── PAM for privileged operations (Release Engineer, Administrator)

LAYER 5: Application Hardening
├── TIA Portal version consistent with approved baseline
├── Only required TIA Portal optional packages installed
├── Siemens add-ins from trusted sources only; inventoried
├── PLCSIM Advanced isolated from production controller network
├── Engineering software launched under standard user account; elevation only when needed
└── Secure PG/PC Communication enabled on all S7-1500 controllers

LAYER 6: Project and Configuration Integrity
├── Version control repository (Git/GitLab/Azure DevOps)
├── Approved baselines before any controller download
├── Mandatory offline vs. online compare before download
├── Change Management process for every modification
├── Know-how Protection on sensitive PLC blocks
└── Project stored in repository — never only on workstation

LAYER 7: Certificate Management
├── Enterprise PKI (not self-signed in production)
├── Secure PG/PC Communication certificates (engineering WS ↔ S7-1500)
├── OPC UA server/client certificates (Sign and Encrypt profile)
├── Certificate expiry monitoring
└── Private keys protected (Windows CNG / TPM where available)

LAYER 8: Monitoring and Audit
├── TIA Portal user actions logged and forwarded to SIEM
├── Windows Security Event Log forwarded (logon, privilege use, process)
├── OT IDS passive monitoring of engineering network traffic
├── Alerts: off-hours engineering login, unexpected controller download
└── NTP synchronized time on engineering workstation

LAYER 9: Backup and Recovery
├── Engineering project version control repository (continuous)
├── Workstation disk image (monthly + after major changes)
├── Offline/air-gapped project backup (after every production release)
├── Certificate and trust store backup (after issuance/renewal/revocation)
└── Tested recovery procedures (annual workstation rebuild test)
```

## 3.3 Engineering Zone Architecture

The engineering workstation must reside in a dedicated **Engineering Zone**, separate from the SCADA/HMI Control Zone and from IT networks.

```
[IT Enterprise Network]
        │
        │ NO DIRECT CONNECTION
        │
[Control Zone]                     [Engineering Zone]
  SCADA Server                       Engineering WS 1
  DCS Workstations                   Engineering WS 2
  HMI Servers                        PLCSIM Advanced
  OPC Server                         UMC Server
        │                                   │
        ├───────────── Conduit ─────────────┤
        │    (Allowlist: TIA Portal →       │
        │    S7-1500 on specific ports;     │
        │    S7comm-Plus, HTTPS, OPC UA)    │
        │
[Field Zone]
  S7-1500 / S7-1200 PLCs
  ET 200SP
  Safety F-CPUs
  HMI Panels
```

**Engineering Zone conduit rules (allowlist):**

| Source | Destination | Protocol / Port | Purpose |
|--------|------------|-----------------|---------|
| Engineering WS | S7-1500 | S7comm-Plus (TCP 102) | TIA Portal online access; Secure PG/PC Communication |
| Engineering WS | S7-1500 | HTTPS (TCP 443) | Web server access; certificate management |
| Engineering WS | S7-1500 | OPC UA (TCP 4840) | OPC UA client for diagnostics (if needed) |
| Engineering WS | HMI Panel | HTTPS (TCP 443) | HMI project download |
| Engineering WS | UMC Server | HTTPS (TCP 443) | UMC communication |
| Engineering WS | PLCSIM Advanced | Local only | Simulation — no network access to production controllers |
| Engineering WS | Patch staging server (DMZ) | HTTPS (TCP 443) | Software update download |
| All others | Engineering WS | DENY | Default deny |

**What must never be permitted:**
* Direct connectivity between Engineering Zone and enterprise IT (email, internet browsing, file sharing).
* SCADA server direct connection to S7-1500 using S7comm-Plus on the same port as TIA Portal without separate conduit rule.
* Engineering WS accessing controllers across zone boundaries not defined in the conduit.

## 3.4 Secure PG/PC Communication

Secure PG/PC Communication is the mutual certificate-based authentication mechanism between TIA Portal engineering workstations and S7-1500 controllers. It replaces the implicit trust model (any workstation can connect to any controller on the network) with explicit certificate-based identity verification.

**How it works:**
1. TIA Portal generates or imports an engineering certificate (issued by enterprise PKI or Siemens-managed CA).
2. The S7-1500 is configured with a list of trusted engineering certificates.
3. Before any online access (including downloads, diagnostics, online changes), the S7-1500 verifies the workstation's certificate.
4. An unrecognized engineering workstation is rejected — it cannot connect even if it has network access.

**Configuration requirements:**
* Must be explicitly enabled in S7-1500 protection settings within TIA Portal.
* Requires TIA Portal V16+ and S7-1500 firmware V2.9+.
* Engineering certificates must be backed up — loss of certificate breaks engineering access to the controller.
* Certificate trust must be established before first online access (commissioning procedure).

**Security implication:** With Secure PG/PC Communication enabled, physical or network access to the S7-1500 is insufficient for unauthorized engineering access. The attacker also needs the engineering certificate and its private key.

---

---

# 4 — Hardening

---

## 4.1 Hardening Philosophy

The engineering workstation is the Root of Trust. Every hardening measure reduces the probability that this trust is violated. Hardening is not a one-time installation activity — it is a continuous operational discipline requiring periodic review and validation.

The guiding principle: **every enabled service, account, interface, protocol, and certificate must have a documented operational justification.** Anything without justification must be disabled or removed.

## 4.2 Operating System Hardening

**Baseline requirements:**

| Area | Requirement | Implementation |
|------|-------------|---------------|
| OS version | Windows 10/11 Pro or Windows Server 2019/2022 (current support) | Validated with TIA Portal version compatibility matrix |
| Updates | Security updates applied within defined patch window | Via DMZ-staged WSUS or SCCM; not direct internet |
| Unused features | Remove: IIS, Hyper-V (unless needed for PLCSIM), Telnet, FTP server, Remote Registry | Windows Features control panel / PowerShell |
| SMB | Disable SMBv1; restrict SMBv2/v3 to required shares only | Registry + Group Policy |
| Remote Desktop | Disable or restrict to engineering jump server only; NLA mandatory | Group Policy |
| Windows Firewall | Enable; configure rules allowing only engineering traffic | Secondary to network firewall |
| Disk encryption | BitLocker on all drives (especially portable engineering laptops) | TPM-backed; recovery key in PKI/PAM |
| AutoRun/AutoPlay | Disabled system-wide | Group Policy |
| Screensaver / lock | 5-minute idle lock; requires MFA to unlock | Group Policy |
| Time sync | NTP configured to organization's NTP server | Group Policy; verified |
| Audit policy | Logon events, privilege use, process creation, policy change | Advanced Audit Policy Configuration |

**Application allowlisting (critical for engineering workstations):**

Application allowlisting permits only pre-approved executables to run — this is one of the most effective controls against malware on engineering workstations.

Implementation options:
* **Windows AppLocker** (built-in, free) — publisher rules for Siemens signed executables; hash rules for unsigned components.
* **Microsoft WDAC** (Windows Defender Application Control) — policy-based; recommended for new deployments.
* **Sophos Device Control / Carbon Black App Control** — commercial; richer management interface.

**Caution:** Application allowlisting must be carefully tuned for TIA Portal. TIA Portal installs many components across multiple directories; hash rules must cover all legitimate executables. Test extensively in a staging environment before production deployment — a misconfigured allowlist can prevent TIA Portal from launching.

## 4.3 TIA Portal Application Hardening

| Area | Requirement | Notes |
|------|-------------|-------|
| Installed version | Only approved TIA Portal version(s) installed | Remove legacy versions when no longer needed for active projects |
| Optional packages | Install only required optional packages (Startdrive, Safety, Energy) | Review installed packages list; remove unused |
| Add-ins | Only vetted, business-justified add-ins from trusted sources | Maintain add-in inventory; verify publisher signature |
| Project directory | Engineering projects stored on secured network path or encrypted local drive; not on Desktop | ACL-protected; backed up |
| Temporary files | TIA Portal temporary directory on encrypted volume | Temporary project files may contain sensitive engineering data |
| PLCSIM Advanced | Installed only if simulation is operationally required | PLCSIM instances must not have network access to production controllers |

## 4.4 Removable Media Control

USB and other removable media represent the most common malware introduction vector in OT environments, particularly for engineering workstations that are otherwise well-isolated from internet.

**Control hierarchy:**

| Level | Control | Notes |
|-------|---------|-------|
| Policy | Written policy prohibiting personal USB devices | Enforceable foundation |
| Technical | USB port blocking via Windows policy (Device control) or physical USB blockers | Group Policy → Device Manager → Device Installation restrictions |
| Process | Approved engineering USB media only; defined procurement process | Labelled, tracked, used only on engineering systems |
| Scanning | Mandatory malware scan before first use of any USB on OT-adjacent system | Dedicated scanning workstation in DMZ; not on production engineering WS |
| Alternatives | Use DMZ file transfer server for software distribution; USB-free where operationally feasible | Preferred over physical media |

**Siemens-specific USB considerations:**
* Hardware Support Packages (HSP) and GSD/GSDML files are often distributed on physical media by vendors — these must pass through the scanning station before use.
* SIMATIC Memory Cards (for PLC program storage) are not standard USB media but should be treated with equivalent caution.
* License dongles (USB) for TIA Portal are an exception — approved by policy, cannot be scanned in the traditional sense, must be from official Siemens procurement.

## 4.5 Network Hardening for Engineering Zone

| Control | Requirement |
|---------|-------------|
| VLAN separation | Engineering Zone on dedicated VLAN; no trunk to IT VLANs |
| Firewall | Dedicated firewall separating Engineering Zone from Control Zone and IT; allowlist rules only |
| Wireless | No wireless access in Engineering Zone; if required: WPA3-Enterprise, isolated SSID, separate VLAN |
| DNS | Engineering WS resolves via internal DNS only; no direct internet DNS |
| NTP | Engineering WS syncs to internal NTP server; not to public internet NTP |
| Network monitoring | OT IDS/NDR (Claroty, Nozomi, Dragos) passively monitors engineering network traffic |

## 4.6 Endpoint Protection

**Antimalware for engineering workstations:**

OT environments have historically been cautious about antimalware due to compatibility concerns and performance impact. Modern endpoint protection can be deployed safely with appropriate configuration:

* Use enterprise endpoint protection validated for compatibility with TIA Portal (check Siemens compatibility list).
* Configure exclusions for TIA Portal runtime directories (reduce scan interference), while maintaining real-time protection on download directories and USB mount points.
* Signature updates via DMZ-staged update mirror — engineering workstation must not pull updates directly from internet.
* Behavioral detection (EDR) may require tuning to avoid false positives from TIA Portal's legitimate process injection and memory operations.

**Siemens compatibility note:** Siemens publishes a compatibility matrix for antivirus/EDR products with TIA Portal and SIMATIC software. Always verify before deployment.

## 4.7 Hardening Validation

After initial hardening and after significant changes, validate:

- [ ] All hardening settings verified against baseline (Group Policy, registry, local security policy)
- [ ] Application allowlist tested: TIA Portal launches and operates normally
- [ ] MFA tested: workstation login requires second factor
- [ ] USB policy tested: unauthorized USB device blocked
- [ ] Network isolation tested: engineering WS cannot reach internet or IT network directly
- [ ] Antimalware running and updating from DMZ mirror
- [ ] NTP synchronization verified
- [ ] Audit logging active and events reaching SIEM

---

---

# 5 — Project Protection

---

## 5.1 The Engineering Project as Critical Asset

A TIA Portal project is not a file — it is the **complete digital definition of an automation system**. It contains:

* Hardware configuration — every device, every module, every parameter.
* PLC programs — the logic that controls physical processes.
* HMI configuration — operator interfaces and alarm logic.
* Safety configuration — fail-safe logic (for F-CPU projects).
* Network topology — communication configuration, IP addresses, protocols.
* Security configuration — UMAC roles, access levels, OPC UA settings, Secure PG/PC certificates.
* Library references — dependencies on reusable function blocks.
* Documentation — embedded engineering knowledge.

Unauthorized access to this project enables an attacker to:
* Understand the process in detail (reconnaissance).
* Identify safety interlocks and understand how to defeat them.
* Develop targeted malicious modifications.
* Create a counterfeit project that appears legitimate.

Unauthorized modification of this project enables an attacker to:
* Deploy malicious control logic to the controller.
* Disable safety functions.
* Create persistent backdoors in the automation system.

Project protection is therefore both an IP protection measure and a safety-critical security control.

## 5.2 Access Protection

**Controlling who can open and modify the project:**

* Projects stored in version control repository (see Section 6) with repository-level access control.
* UMAC enforces what authenticated users can do within TIA Portal.
* File system ACLs on project storage directories restrict access at OS level.
* Project export should be logged and restricted to approved personnel.

## 5.3 Know-how Protection

Know-how Protection (KHP) prevents unauthorized users from viewing the contents of protected PLC program blocks. The block can be used (called, included in the project), but its internal logic cannot be inspected or copied without the correct password.

**Appropriate use cases:**
* Vendor-supplied function libraries — vendor IP protection.
* Proprietary process algorithms — asset owner IP protection.
* Safety-critical logic that must not be accidentally modified — protection against inadvertent changes.
* Security-relevant logic blocks — reducing reconnaissance value if project file is exfiltrated.

**Know-how Protection limitations:**
* KHP is not encryption of the program — it is an access control mechanism within TIA Portal.
* A sufficiently sophisticated attacker with access to the project file and reverse engineering tools may be able to extract protected content.
* KHP protects against casual inspection and accidental modification — it is not a substitute for protecting the project file itself.

**Password governance for KHP:**
* KHP passwords must be documented and stored in the organization's password vault (PAM).
* Loss of KHP password means permanent loss of ability to modify that block — recovery requires deletion and recreation.
* KHP passwords must be changed when personnel with knowledge of them leave the organization.

## 5.4 Copy Protection

Copy Protection binds engineering components or entire projects to specific controller hardware (identified by serial number). A copy-protected program can only run on the specific hardware it was bound to.

**Use cases:**
* Preventing unauthorized duplication of licensed applications.
* Ensuring that engineering releases are deployed only to approved hardware.
* Intellectual property protection for machine builders (OEMs) delivering systems to end customers.

**Operational trade-off:**
* Copy Protection creates a dependency on specific hardware — if the controller fails and is replaced with an identical model, the protected program will not run on the replacement unit without reauthorization.
* Document all Copy Protection bindings in the engineering project documentation and PAM.
* Test recovery procedures including Copy Protection reauthorization before production deployment.

## 5.5 Confidential Engineering Data

Some information within TIA Portal projects requires additional protection because it has direct security implications:

| Data Type | Protection Required | Notes |
|-----------|--------------------|----|
| UMAC user accounts and passwords | Never export to unprotected files | Audit access to security configuration tabs |
| OPC UA certificate trust stores | Backup with encryption; access control | Compromise enables impersonation |
| Secure PG/PC certificate references | Protect private key; encrypted backup | Loss breaks engineering access to controller |
| PLC access level passwords | Store in PAM; not in plain-text documentation | Rotation required on personnel changes |
| Network passwords (PROFINET device names, etc.) | Treat as security-sensitive configuration | Included in project change management scope |

## 5.6 Library Protection

Engineering libraries (Global Libraries, Local Libraries) contain reusable function blocks, data types, faceplates and templates. They are intellectual property and operational dependencies.

**Library governance requirements:**
* Libraries stored in version control independently from projects (separate repository or dedicated directory with separate permissions).
* Library releases approved through the same Change Management process as project releases.
* Library consumers document which library version their project depends on (version pinning).
* Library changes trigger assessment of impact on all dependent projects.
* Know-how Protection applied to library blocks containing proprietary logic.

## 5.7 Project Lifecycle Governance

Every project modification should pass through a defined lifecycle:

```
Change Request (documented business justification)
        ↓
Risk Assessment (what could go wrong? what is the impact?)
        ↓
Technical Design (what will change? how?)
        ↓
Development (in development branch; PLCSIM validation)
        ↓
Peer Review (another engineer reviews changes)
        ↓
Security Review (security engineer reviews for security implications)
        ↓
Approval (authorized approver signs off)
        ↓
Release Baseline (tagged in version control; release package created)
        ↓
Staging Deployment (FAT/SAT environment where available)
        ↓
Production Download (approved workstation; PAM-mediated; recorded session)
        ↓
Online Verification (offline compare after download)
        ↓
Documentation Update
```

The approval → release → download sequence must be documented and auditable. An untracked change to a production PLC is a compliance finding and a potential safety issue.

---

---

# 6 — Version Control

---

## 6.1 Version Control Philosophy

Without controlled version governance, it is impossible to determine:

* Which project version is currently deployed in each controller.
* Who approved the last production download.
* What changed between the current and previous version.
* Why a change was made.
* Which version should be restored after an incident.
* Whether the running controller matches the approved engineering baseline.

Version control is not a software development convenience — it is an engineering governance requirement with direct safety and regulatory implications.

## 6.2 Repository Architecture

**Recommended repository structure:**

```
tia-portal-engineering/
├── projects/
│   ├── PlantA/
│   │   ├── Line1/            ← Individual TIA Portal project
│   │   └── Line2/
│   └── PlantB/
├── libraries/
│   ├── GlobalLib-PowerMgmt/  ← Versioned independently
│   └── GlobalLib-Safety/
├── hsps/                     ← Hardware Support Packages (version-tracked)
├── gsds/                     ← GSD/GSDML files (version-tracked)
├── certificates/             ← Certificate metadata (NOT private keys)
├── documentation/
│   ├── architecture/
│   ├── release-notes/
│   └── approval-records/
└── release-packages/
    └── PlantA-Line1-v4.3.7/  ← Complete release snapshots
        ├── project/
        ├── libraries/
        ├── firmware-matrix.md
        ├── release-notes.md
        └── approval-record.pdf
```

**Platform options:**

| Platform | Suitability | Notes |
|---------|------------|-------|
| Git + GitLab CE (self-hosted) | Preferred | Full control; on-premise; branch protection; CI/CD capability |
| Git + Gitea | Suitable | Lightweight; on-premise |
| Azure DevOps Server | Suitable | On-premise; enterprise integration |
| Siemens Teamcenter | Suitable for large enterprises | PLM integration; higher complexity |
| Shared network drive + manual versioning | Not recommended | No audit trail; error-prone; common but problematic |

**Repository hosting:**
* The repository must be hosted within the Engineering Zone or a dedicated, access-controlled engineering server — not on general IT file servers.
* Repository must be included in backup and DR procedures (see Section 8).
* Repository access must be governed by the same identity controls as TIA Portal (AD integration, MFA for repository access).

## 6.3 Version Numbering

**Recommended format: MAJOR.MINOR.PATCH**

| Component | Trigger | Example |
|-----------|---------|---------|
| MAJOR | Architectural change, hardware change, new safety function, scope expansion | 4.0.0 → 5.0.0 |
| MINOR | New feature, new device, significant logic addition | 4.2.0 → 4.3.0 |
| PATCH | Bug fix, parameter correction, security fix, minor logic change | 4.3.6 → 4.3.7 |

Version numbers must be embedded in:
* TIA Portal project properties (comment field, project info).
* Release notes document.
* Deployment record.
* Version control tag (e.g., `git tag v4.3.7`).

## 6.4 Branching Strategy

**Recommended branching model:**

```
main (protected)
 │   ← Only receives merges from release/* branches
 │   ← All merges require approval
 │   ← Tagged with release version on each merge
 │
 ├── develop
 │    │   ← Integration branch; merges from feature/* and hotfix/*
 │    │   ← Requires passing review before merge to release/*
 │    │
 │    ├── feature/CHANGE-REF-motor-protection-upgrade
 │    ├── feature/CHANGE-REF-hmi-alarm-update
 │    └── feature/CHANGE-REF-opc-ua-config
 │
 ├── release/4.3.x
 │    │   ← Release preparation; final testing; release notes
 │    │   ← Only merges to main when approved for production
 │    │
 │    └── (merge → main → tag v4.3.7)
 │
 └── hotfix/CHANGE-REF-emergency-valve-fix
      │   ← Emergency production fix; bypasses develop
      └── (merge → main → tag v4.3.8; backport to develop)
```

**Branch protection rules (enforced in repository):**
* `main` branch: no direct pushes; requires pull request with minimum one reviewer approval; requires passing CI checks if available.
* Release branches: require approver with Release Engineer role.
* All merges to `main` must have associated approval record.

## 6.5 Engineering Baselines

An engineering baseline is an immutable, approved snapshot of the complete engineering state at a specific point in time. It is the reference against which production controllers are verified.

**Baseline components:**

| Component | Required | Notes |
|-----------|----------|-------|
| TIA Portal project (exported archive) | Mandatory | `.zap17` or equivalent |
| Referenced Global Libraries (versions) | Mandatory | Pinned version identifiers |
| Hardware Support Package versions | Mandatory | HSP version determines hardware catalog entries |
| GSD/GSDML file versions | Mandatory | Device descriptions used in project |
| Firmware compatibility matrix | Mandatory | Which firmware version corresponds to this project release |
| Security configuration record | Mandatory | UMAC roles, access levels, certificate fingerprints |
| Release notes | Mandatory | What changed from previous version |
| Approval record | Mandatory | Who approved; date; change request reference |
| Deployment record template | Mandatory | Blank form for download recording |

Once created, a baseline must be **immutable** — no modifications, only a new baseline version.

## 6.6 Online vs. Offline Comparison

Before any production controller download, the offline project (in version control) must be compared against the online controller state using TIA Portal's comparison function.

**Comparison procedure:**

1. Open approved project version from repository (verified tag).
2. Connect to production controller (Secure PG/PC communication required).
3. Execute TIA Portal → Online → Compare → Online/Offline.
4. Review all differences:
   * **No differences:** Proceed with download if download is the intent; or confirm no changes needed.
   * **Differences found:** Investigate each difference before proceeding. Differences may indicate unauthorized online changes, configuration drift, or incomplete previous downloads.
5. Document comparison result in deployment record.

**Configuration drift investigation:**
If online vs. offline differences are found and no approved change explains them, this is a **security incident**:
* Isolate the controller (disconnect from network if possible without safety impact).
* Preserve forensic state (document differences; capture memory dump if feasible).
* Initiate incident response procedure.
* Do not proceed with download until root cause is established.

## 6.7 Release Package

Each production release must be packaged as a self-contained artifact:

```
release-PlantA-Line1-v4.3.7/
├── project/
│   └── PlantA-Line1-v4.3.7.zap17    ← Exported TIA Portal project
├── libraries/
│   ├── GlobalLib-PowerMgmt-v2.1.zip
│   └── GlobalLib-Safety-v1.4.zip
├── firmware-matrix.md               ← Required firmware per device
├── deployment-instructions.md       ← Step-by-step download procedure
├── rollback-procedure.md            ← How to restore previous version
├── release-notes.md                 ← What changed; change request refs
├── approval-record.pdf              ← Signed approval document
└── SHA256SUMS.txt                   ← Checksums for all package files
```

The SHA256 checksums enable integrity verification of the release package at download time — confirm files have not been modified since packaging.

---

---

# 7 — Certificate Management

---

## 7.1 Certificate Architecture

Certificates in a TIA Portal environment serve multiple distinct trust purposes:

```
[Enterprise PKI]
  Root CA
  └── Issuing CA (Engineering)
       ├── Engineering Workstation Certificates (one per WS)
       │    └── Used for: Secure PG/PC Communication
       ├── User Certificates (where applicable)
       └── Service Certificates
            └── Used for: UMC server HTTPS, internal services

[Controller Certificates]
  S7-1500 self-generated or PKI-issued
  ├── Device Identity Certificate
  │    └── Used for: Secure PG/PC Communication (server side)
  ├── OPC UA Server Certificate
  │    └── Used for: OPC UA server authentication
  └── Web Server Certificate (HTTPS)
       └── Used for: S7-1500 integrated web server

[OPC UA Trust Relationships]
  OPC UA Client (TIA Portal, SCADA, historian)
  └── Trust: OPC UA Server Certificate (on S7-1500)
  S7-1500 OPC UA Server
  └── Trust: OPC UA Client Certificate
```

## 7.2 PKI Requirements

**Enterprise PKI is strongly preferred** over self-signed certificates for production environments.

Self-signed certificates:
* Have no revocation mechanism.
* Create individual trust decisions on each workstation — error-prone and unscalable.
* Cannot be audited centrally.
* Are acceptable only in isolated test environments.

**Minimum PKI requirements for TIA Portal environments:**

| Requirement | Details |
|-------------|---------|
| Root CA | Offline root CA (HSM-protected where possible); certificate validity ≤ 20 years |
| Issuing CA | Online issuing CA; validity ≤ 5 years; accessible from Engineering Zone |
| Certificate validity | Engineering WS certificates: 2 years; device certificates: 3 years |
| Key length | RSA 2048 minimum; RSA 4096 or ECDSA P-256 preferred |
| Hash algorithm | SHA-256 minimum |
| CRL / OCSP | CRL published and accessible from Engineering Zone; OCSP optional |
| Key storage | Engineering WS private keys protected by Windows CNG; TPM-backed preferred |

## 7.3 Certificate Types and Lifecycle

### Engineering Workstation Certificate

**Purpose:** Identifies the engineering workstation to S7-1500 controllers using Secure PG/PC Communication.

**Lifecycle:**
* Issued by enterprise issuing CA to individual workstation.
* Private key generated on workstation; never exported.
* Certificate enrolled to TIA Portal certificate store.
* Validity: 2 years; renewal automated where possible.
* Installed in S7-1500 trusted client list during commissioning.
* Revoked immediately if workstation is compromised or retired.

**Recovery risk:** If the private key is lost (workstation failure without backup), a new certificate must be issued and installed in every S7-1500 that trusts the old certificate. This requires physical or alternative remote access to each controller. **Back up the certificate store with private key protection.**

### OPC UA Server Certificate (S7-1500)

**Purpose:** Authenticates the S7-1500 OPC UA server to clients (SCADA historian, TIA Portal diagnostics, third-party OPC UA clients).

**Lifecycle:**
* Generated by S7-1500 internally or issued by PKI.
* Installed in OPC UA client trust stores.
* Validity: 3 years; renewal must be coordinated (update all client trust stores).
* Security profile must be: **Sign and Encrypt** — never None in production.
* Any client attempting to connect with an untrusted certificate must be rejected.

### OPC UA Client Certificate

**Purpose:** Authenticates OPC UA clients (SCADA server, historian, TIA Portal) to the S7-1500 OPC UA server.

**Lifecycle:**
* Generated by each OPC UA client application.
* Must be added to S7-1500 trusted client list before connection is accepted.
* Validity: 3 years.
* Revoke from S7-1500 trust list when client is decommissioned.

### S7-1500 Web Server Certificate

**Purpose:** Enables HTTPS for the S7-1500 integrated web server.

**Lifecycle:**
* Issued by PKI or self-signed (test only).
* Validity: 2 years.
* Required for HTTPS management interface access.

## 7.4 Certificate Monitoring

Certificate expiry events in OT environments cause operational disruptions that are often more severe than in IT:

* Expired Secure PG/PC certificate → engineering workstation cannot access controller → no maintenance possible.
* Expired OPC UA server certificate → historian stops collecting data → process visibility lost.
* Expired HMI certificate → HTTPS connections fail → depending on configuration, HMI may become inaccessible.

**Minimum monitoring requirements:**

| Event | Alert Threshold | Action |
|-------|----------------|--------|
| Certificate expiry approaching | 90 days, 30 days, 7 days | Initiate renewal process |
| Certificate expired | Immediately | Emergency renewal; check operational impact |
| New certificate installed | Any time | Alert for unauthorized certificate installation |
| Trust store modification | Any time | Alert; verify authorization |
| Certificate revocation | Any time | Update trust stores; operational impact assessment |

Certificate monitoring should be integrated with the organization's SIEM or PKI management platform.

## 7.5 Private Key Protection

The private key is the security-critical component of any certificate. Compromise of a private key compromises the identity it represents.

**Protection requirements:**

| Key Type | Protection Method | Notes |
|----------|------------------|-------|
| Engineering WS certificate private key | Windows CNG / TPM-backed | Never export unless for encrypted backup |
| TIA Portal certificate store | Windows-protected; workstation user-bound | Encrypted with workstation credentials |
| UMC server certificate | Windows CNG on UMC server; TPM-backed | High-value server; HSM for high-security environments |
| CA private keys (issuing CA) | HSM in FIPS 140-2 Level 3 device (for issuing CA) | Root CA: offline, HSM-protected, air-gapped storage |

**Certificate backup:**
Private keys must be backed up — the backup must be encrypted and access-controlled at least as strictly as the original. Recommended approach:
* Export certificate with private key to PKCS#12 (.p12) format with strong password.
* Store encrypted export in PAM vault, not on file server.
* Document the backup location and decryption procedure in the DR plan.

---

---

# 8 — Backup and Recovery

---

## 8.1 Backup Philosophy

A backup is not the goal — **recovery is the goal**. A backup that cannot be used for successful recovery is a false sense of security.

The objective is to restore a **trusted engineering environment** — not merely engineering files. A recovered TIA Portal project without the matching TIA Portal version, compatible HSPs, correct library versions, valid certificates and functional workstation cannot be trusted and cannot be used for production engineering.

## 8.2 Backup Scope

**Complete backup scope for TIA Portal environments:**

| Component | Backup Method | Frequency | Storage |
|-----------|--------------|-----------|---------|
| TIA Portal project | Version control + periodic archive export | Continuous (VCS) + after each release | Repository + offline copy |
| Global Libraries | Version control | After modification | Repository |
| Local Libraries | Version control | After modification | Repository |
| Hardware Support Packages (HSP) | File backup | After installation/update | Backup repository |
| GSD/GSDML files | File backup (part of project environment) | After addition | Backup repository |
| Startdrive configurations | File backup | After modification | Backup repository |
| PLCSIM Advanced configurations | File backup (if used) | After modification | Backup repository |
| WinCC Unified resources | File backup | After modification | Backup repository |
| Engineering workstation image | Full disk image | Monthly + after major changes | Backup repository |
| TIA Portal installer (approved version) | File backup | After version approval | Software repository |
| TIA Portal license files | File backup | After installation | Backup repository + offline |
| Engineering certificates + private keys | Encrypted PKCS#12 export | After issuance/renewal/revocation | PAM vault |
| Certificate trust stores | File backup | After modification | Backup repository |
| Root CA / Issuing CA materials | HSM backup (if applicable) | After any CA operation | Offline + secure offsite |
| UMAC/UMC configuration export | Configuration export | After modification | Backup repository |
| Documentation | File backup + VCS | After modification | Repository + backup |
| Release packages | File backup | After each release | Backup repository + offline |
| Deployment records | File backup | After each deployment | Backup repository |
| Version control repository | Repository backup (bare clone) | Daily | Secondary repository location |

**What is NOT sufficient:**
* Backing up only the `.zap17` project export file — insufficient without matching software, libraries and certificates.
* Keeping projects only on the engineering workstation's local drive — single point of failure; not protected against ransomware.
* "I know where everything is" — implicit knowledge without documented procedure is not a backup strategy.

## 8.3 Backup Security

Backups are high-value targets for attackers — a ransomware group that encrypts backups alongside production data eliminates recovery options.

| Security Control | Requirement |
|----------------|-------------|
| Encryption at rest | AES-256 for all backup storage |
| Encryption in transit | TLS 1.2+ for all backup transfers |
| Access control | RBAC; minimum access principle; MFA for backup system access |
| Immutability | WORM storage or backup solution with immutability feature for critical backups |
| Offline copy | At least one copy of each critical backup offline/air-gapped — not reachable from network |
| Integrity verification | Checksum verification after backup; periodic restore test |
| Audit logging | All backup and restore operations logged |
| Ransomware isolation | Backup storage on separate network segment; different credentials from engineering WS |

## 8.4 Recovery Scenarios

### Scenario A: Engineering Workstation Hardware Failure (Non-Cyber)

**Trigger:** Hardware failure (disk, motherboard), OS corruption.
**Recovery time target:** Hours to one working day.

**Recovery procedure:**
1. Deploy approved workstation image to replacement hardware (or bare metal restore from image backup).
2. Verify OS version and patch level match baseline.
3. Install approved TIA Portal version from software repository.
4. Install all Hardware Support Packages from HSP backup.
5. Install GSD/GSDML files.
6. Configure Active Directory domain join and MFA.
7. Restore certificates and trust stores from encrypted backup.
8. Clone version control repository.
9. Restore libraries from repository.
10. Configure UMAC settings.
11. Validate: launch TIA Portal; open project; attempt online connection to test controller.
12. Document recovery in change record.

### Scenario B: Project Corruption or Accidental Deletion

**Trigger:** Accidental project modification, file system corruption, mistaken deletion.
**Recovery time target:** Minutes to hours.

**Recovery procedure:**
1. Identify the last approved production baseline (version tag in repository).
2. Clone or restore tagged version from repository.
3. Verify project checksums from release package SHA256SUMS.
4. Verify library versions match release package manifest.
5. Verify certificate and firmware compatibility.
6. Review release notes to confirm this is the correct production version.
7. Proceed with validation before any controller download.

### Scenario C: Ransomware or Cyber Incident on Engineering Workstation

**Trigger:** Ransomware encryption, malware discovery, suspected compromise.
**Recovery time target:** Determined by forensic requirements; typically 1–3 days including forensics.

**Recovery procedure:**
1. **Isolate immediately:** Disconnect workstation from Engineering Zone network.
2. **Preserve forensic state:** Do not power off (memory evidence); engage OT forensic team; image disk before remediation.
3. **Notify:** Initiate incident response; notify NÚKIB within 24 hours if organization is regulated; notify management.
4. **Assess scope:** Determine whether the attacker accessed the engineering repository, controller connections, or deployed any downloads to production controllers.
5. **Controller integrity check:** For every controller the workstation could access, perform online vs. offline comparison to detect unauthorized modifications. If differences found: treat as additional incident scope.
6. **Rebuild workstation from trusted image:** Do NOT attempt to clean the malware from the compromised OS — rebuild from scratch.
7. **Install approved software from repository.**
8. **Restore certificates — DO NOT reuse certificates from the compromised workstation.** Generate new engineering workstation certificate; update S7-1500 trusted client lists.
9. **Rotate credentials:** All passwords known to or stored on the compromised workstation; PAM credential rotation.
10. **Validate before returning to production engineering.**
11. **Post-incident review:** Root cause analysis; architecture improvements; update IR plan.

### Scenario D: Version Control Repository Failure

**Trigger:** Repository server failure, corruption, ransomware.
**Recovery time target:** Hours to one day.

**Recovery procedure:**
1. Restore repository from most recent daily bare clone backup.
2. Verify repository integrity (git fsck or equivalent).
3. Compare restored repository HEAD against known production release tags.
4. Investigate any discrepancy — missing commits may indicate incomplete backup or potential tampering.
5. Restore repository server to Engineering Zone.
6. Re-verify access controls and authentication configuration.

## 8.5 Recovery Validation

Recovery is complete only after validation:

| Validation Check | Method |
|----------------|--------|
| TIA Portal version correct | Check Help → About |
| Project version correct | Compare version tag in project properties vs. expected |
| Library versions correct | Verify library version references match release package manifest |
| HSP versions correct | Check installed HSPs in TIA Portal → Options → Support Packages |
| Certificate valid | Check certificate validity in TIA Portal certificate store; verify fingerprint against backup record |
| Secure PG/PC connectivity | Attempt online connection to a test controller; verify certificate-based authentication |
| UMAC configuration correct | Verify user roles and rights match documented baseline |
| Backup verified | Confirm new backup of recovered workstation taken after validation |

## 8.6 RTO and RPO for Engineering Environments

Define and document RTO and RPO for each engineering environment:

| Scenario | Suggested RTO | Suggested RPO | Rationale |
|----------|--------------|--------------|-----------|
| Workstation hardware failure | 4–8 hours | 0 (VCS is continuous) | Fast rebuild from image + VCS clone |
| Ransomware on workstation | 24–72 hours | Last production release | Forensics required before rebuild |
| VCS repository failure | 4–8 hours | ≤ 24 hours (daily backup) | Repository restore from backup |
| Certificate expiry causing outage | 2–4 hours | N/A (no data loss) | Certificate renewal procedure |

RTO and RPO must be reviewed with operations management — engineering downtime may directly impact production if emergency engineering changes are required during recovery.

---

---

# 9 — Patch Management

---

## 9.1 TIA Portal Patch Management Philosophy

Patch management for the TIA Portal engineering environment differs from IT patch management in critical ways:

| Dimension | IT Approach | TIA Portal / OT Approach |
|-----------|------------|--------------------------|
| Frequency | Monthly (Patch Tuesday) | Risk-based; per approved maintenance window |
| Testing | Test environment | Mandatory staging validation |
| Version lock | Rolling updates acceptable | TIA Portal version must match project/hardware compatibility matrix |
| Impact on production | Short service interruption | Engineering WS downtime may block emergency changes to production |
| Rollback | Generally straightforward | TIA Portal version rollback requires project migration reversal |
| Vendor involvement | IT handles | Siemens support may be required for major version upgrades |

## 9.2 Patch Categories

| Category | Scope | Governance |
|----------|-------|-----------|
| Windows OS security patches | Engineering workstation OS | Monthly; via DMZ-staged WSUS; high-severity within 7 days |
| TIA Portal minor updates / hotfixes | TIA Portal application | After staging validation; per Change Management |
| TIA Portal major version upgrades | TIA Portal application | Major project; full regression testing; Change Management |
| Hardware Support Packages (HSP) | Device support in TIA Portal | When new hardware requires it; staging validation |
| Siemens security advisories | Any Siemens component | Priority-based; within defined SLA per severity |
| Third-party dependencies | Runtime libraries, .NET, Visual C++ | After vendor release; staging validation |
| Antimalware signatures | Endpoint protection | Automated via DMZ-staged mirror; near real-time |
| Engineering workstation firmware | BIOS/UEFI | Annual or on security advisory; Change Management |

## 9.3 Vulnerability Intelligence

Monitor the following sources for TIA Portal-relevant vulnerabilities:

| Source | Content | Access |
|--------|---------|--------|
| Siemens ProductCERT | Official Siemens security advisories; CVEs for all Siemens products | https://cert.siemens.com |
| CISA ICS-CERT Advisories | ICS-specific CVEs; includes Siemens advisories | https://www.cisa.gov/ics-advisories |
| NVD (National Vulnerability Database) | CVEs by product (search "Siemens TIA Portal") | https://nvd.nist.gov |
| NÚKIB Varování | Czech-market relevant advisories | https://nukib.cz |
| Siemens Security Patch Day | Monthly security advisory releases | Second Tuesday of month (analogous to Microsoft Patch Tuesday) |

**Subscription recommendation:** Subscribe to Siemens ProductCERT email alerts — this is the most reliable source for TIA Portal-specific security advisories.

## 9.4 TIA Portal Update Process

```
Siemens releases update / advisory
        ↓
Security team reviews: Does it apply to our TIA Portal version?
        ↓
Risk assessment: Severity × Exploitability × Our exposure
        ↓
Decision: Priority (Critical/High/Medium/Low) → Target date
        ↓
Staging validation:
  - Install on staging engineering WS
  - Test: TIA Portal launches; project opens; PLCSIM validation
  - Test: Compatibility with project hardware/firmware versions
  - Test: Antimalware compatibility
  - Test: Application allowlist (new executables may require allowlist update)
        ↓
Change Management approval
        ↓
Production deployment (maintenance window)
        ↓
Validation on production engineering WS
        ↓
Update backup (new workstation image after successful update)
        ↓
Document update in asset inventory and change record
```

## 9.5 TIA Portal Version Compatibility Management

This is the most TIA Portal-specific challenge in patch management: a TIA Portal version upgrade may require project migration, which is a significant engineering change in itself.

**Version compatibility matrix considerations:**

* TIA Portal V15.1 projects are not directly compatible with TIA Portal V19 — migration required.
* Project migration is a one-way process — a migrated project cannot be opened in the previous TIA Portal version without re-migration.
* Hardware Support Packages must match the TIA Portal version.
* Controller firmware versions have minimum TIA Portal version requirements.

**Version upgrade procedure:**
1. Assess all active projects: which TIA Portal version do they require?
2. If multiple projects require different versions, document coexistence plan (multiple TIA Portal versions on one workstation, or separate workstations).
3. Migrate project copies to new TIA Portal version in staging environment.
4. Full regression test of migrated project in staging.
5. Approve migration via Change Management.
6. Deploy new TIA Portal version to production workstation.
7. Open migrated project; perform online comparison to verify no unintended changes.
8. Retire old TIA Portal version from workstation when all projects are migrated.

## 9.6 Firmware Patch Management

TIA Portal is the mechanism through which controller firmware is updated. Firmware patch management therefore falls partially within the TIA Portal governance domain.

**Firmware update process:**

1. Siemens releases firmware update for S7-1500 (published on support.industry.siemens.com).
2. Download from official Siemens source; verify checksum before installation.
3. Review firmware release notes for: security fixes, compatibility changes, functional changes.
4. Test in staging/FAT environment.
5. Assess production impact: does firmware update require any project changes?
6. Plan maintenance window (firmware update requires controller restart).
7. Approve via Change Management.
8. Deploy via TIA Portal → Online → Firmware Update.
9. Verify: controller comes online; process operates normally.
10. Document: update record in asset inventory (FW version updated).

**Compensating controls for unpatched firmware:**
If a known vulnerability in controller firmware cannot be patched (e.g., vendor patch not yet available, or operational constraints prevent restart):
* Document the vulnerability in the risk register.
* Implement compensating controls: restrict network access to the controller, enhance monitoring for exploitation indicators, review access controls.
* Formally accept residual risk (management sign-off).
* Define review date (when will patching become possible?).

---

---

# 10 — Best Practices

---

## 10.1 Core Engineering Principles

The following principles underpin a secure and governable TIA Portal engineering environment:

1. **Trust before deployment** — every controller download must originate from an approved, verified baseline.
2. **Identity before access** — every engineering action is attributed to a named, authenticated individual.
3. **Governance before configuration** — Change Management precedes every modification.
4. **Least Privilege** — every account has only the access required for its defined role.
5. **Defense in Depth** — no single control is the sole barrier; assume each layer may be breached.
6. **Controlled Change Management** — every change is documented, reviewed and approved.
7. **Continuous monitoring** — engineering activity is logged and anomalies are detected.
8. **Recovery by design** — the ability to recover must be designed and tested, not assumed.

## 10.2 Reference Architecture Summary

```
╔══════════════════════════════════════════════════════════════════════╗
║  ENTERPRISE IT                                                       ║
║  Active Directory, MFA, PKI, PAM, SIEM                              ║
╠══════════════════════╦═══════════════════════════════════════════════╣
║  ENGINEERING DMZ     ║   ENGINEERING ZONE                           ║
║  Jump Server         ║   Engineering WS 1 (TIA Portal, UMAC, certs) ║
║  Patch Staging       ║   Engineering WS 2 (TIA Portal, UMAC, certs) ║
║  AV Update Mirror    ║   UMC Server                                 ║
║  Log Relay to SIEM   ║   PLCSIM Advanced (isolated)                 ║
║  Remote Access GW    ║   Git Repository Server                      ║
╠══════════════════════╩═══════════════════════════════════════════════╣
║  CONDUIT: Allowlist (S7comm-Plus TCP 102, HTTPS 443, OPC UA 4840)   ║
╠══════════════════════════════════════════════════════════════════════╣
║  CONTROL ZONE                                                        ║
║  SCADA Servers, DCS Workstations, HMI Servers                       ║
╠══════════════════════════════════════════════════════════════════════╣
║  CONDUIT: Allowlist (Modbus, EtherNet/IP, PROFINET, OPC UA)         ║
╠══════════════════════════════════════════════════════════════════════╣
║  FIELD ZONE                                                          ║
║  S7-1500, S7-1200, ET 200SP, S7-300 (legacy), HMI Panels, Drives   ║
╠══════════════════════════════════════════════════════════════════════╣
║  SAFETY ZONE (physically separated)                                  ║
║  F-CPU, Safety I/O — No network path to Engineering Zone except      ║
║  dedicated, approved engineering connection during planned access     ║
╚══════════════════════════════════════════════════════════════════════╝
```

## 10.3 Common Mistakes — Complete Reference

The following mistakes are observed in real OT engineering environments:

| Mistake | Risk | Correct Practice |
|---------|------|----------------|
| Engineering WS used for office email / browsing | Phishing → malware → controller compromise | Dedicated workstation; no general-purpose use |
| Shared engineering accounts ("engineer1", "siemens") | No attribution; cannot detect insider; forensics impossible | Individual named accounts; UMAC; UMC |
| No MFA on engineering WS | Single credential compromise = full engineering access | MFA mandatory; hardware token preferred |
| Projects stored only on local workstation C: drive | Single point of failure; no version history; ransomware destroys all copies | Version control repository; off-WS storage |
| No version control; manual folder copies ("v2_FINAL_final") | No audit trail; cannot reproduce; cannot detect changes | Git repository; formal versioning |
| Download to production without offline comparison | Configuration drift undetected; unauthorized changes missed | Mandatory offline/online compare before every download |
| Uncontrolled online changes ("Quick Fix") | Undocumented modification; version control diverges from running config | All online changes through Change Management; sync to offline project |
| No Change Management for PLC modifications | No accountability; no rollback plan; no review | Formal CM for every change |
| S7-1500 access level: No restriction | Any device on the network can write to PLC | Full access + PUT/GET protection minimum |
| Secure PG/PC Communication not enabled | Any TIA Portal on the network can connect to controllers | Enable on all S7-1500 in production |
| Self-signed OPC UA certificates, None security profile | No authentication or encryption on OPC UA | PKI certificates; Sign and Encrypt profile |
| No certificate expiry monitoring | Silent operational failure when certificate expires | Monitoring; alerts at 90/30/7 days |
| Vendor permanent VPN access ("they always need it") | Persistent attack surface; credential theft | Time-limited credentials; revoke after each session |
| No session recording for vendor access | Cannot audit what vendor did during access | Full session recording; stored off-WS |
| HSP and GSD files not backed up | Workstation rebuild cannot open legacy project | Back up all engineering environment components |
| Backup not tested | Recovery impossible when needed; false confidence | Annual workstation rebuild test |
| No antimalware or signature updates via internet | Missing protection; or direct internet from OT-adjacent WS | Enterprise AV; DMZ-staged update mirror |
| Engineering WS in same zone as SCADA | Compromise of one system = access to other | Separate Engineering Zone |
| No patch process for TIA Portal itself | Known vulnerabilities exploited | Vulnerability monitoring; staged testing; CM-governed updates |

## 10.4 IEC 62443 Alignment

The TIA Portal engineering environment directly corresponds to IEC 62443 requirements:

| IEC 62443 Requirement | TIA Portal Implementation |
|----------------------|--------------------------|
| FR 1 — Identification and Authentication (SR 1.1, 1.2, 1.3) | UMAC, UMC, enterprise AD, MFA, named accounts |
| FR 2 — Use Control (SR 2.1, 2.8) | RBAC in UMAC, least privilege, audit logging |
| FR 3 — System Integrity (SR 3.2, 3.4) | Application allowlisting, version control, integrity verification |
| FR 4 — Data Confidentiality (SR 4.1) | Know-how Protection, BitLocker, TLS for repository |
| FR 5 — Restricted Data Flow (SR 5.1, 5.2) | Engineering Zone, firewall allowlist, Secure PG/PC |
| FR 6 — Timely Response to Events (SR 6.1) | Centralized logging, SIEM integration, alerting |
| FR 7 — Resource Availability (SR 7.3) | Backup strategy, recovery procedures, tested restoration |

See [IEC62443-Overview.md](#) for full FR/SR context.

## 10.5 NIS2 and Czech Cybersecurity Act Alignment

Engineering platform security directly supports NIS2 Article 21 obligations:

| NIS2 / Decree 409 Obligation | TIA Portal Governance Element |
|-----------------------------|------------------------------|
| Risk management (Art. 21(2)(a)) | SLRA for Engineering Zone; threat model; risk register |
| Access control, asset management (Art. 21(2)(i)) | UMAC, UMC, MFA, PAM; engineering WS asset inventory |
| MFA (Art. 21(2)(j)) | MFA on all engineering WS and remote access |
| Supply chain security (Art. 21(2)(d)) | Vendor access management; IEC 62443-2-4 for integrators |
| Backup and continuity (Art. 21(2)(c)) | Complete backup scope; tested recovery; RTO/RPO |
| Cryptography (Art. 21(2)(h)) | Secure PG/PC; OPC UA Sign+Encrypt; PKI |
| Vulnerability management (Art. 21(2)(e)) | TIA Portal patch process; Siemens ProductCERT monitoring |
| Incident handling (Art. 21(2)(b)) | Incident Scenario C in Section 8; IR plan; NÚKIB notification |

---

---

# Checklists / Kontrolní seznamy

---

## Checklist 1: Zprovoznění Engineering Workstation — bezpečnostní baseline

Použij při nasazení nové nebo obnovené engineering workstation.

### Česky

**Hardwarová a softwarová příprava**
- [ ] Workstation je dedikovaná výhradně pro engineering — žádné kancelářské použití
- [ ] Je nainstalován podporovaný OS (Windows 10/11 Pro nebo Server 2019/2022)
- [ ] Jsou aplikovány aktuální bezpečnostní záplaty OS (přes DMZ staging server, ne přímo z internetu)
- [ ] Je nainstalována schválená verze TIA Portal (dle kompatibilní matice pro aktivní projekty)
- [ ] Jsou nainstalovány pouze vyžadované optional packages TIA Portal
- [ ] Jsou nainstalovány Hardware Support Packages z bezpečného repository
- [ ] Je nainstalován a nakonfigurován podnikový endpoint protection (antimalware)

**Identita a přístup**
- [ ] Workstation je zapojena do Active Directory
- [ ] Je nakonfigurováno MFA pro přihlášení k OS (hardware token nebo authenticator app)
- [ ] Jsou vytvořeny pouze individuální pojmenované uživatelské účty — žádné sdílené účty
- [ ] Jsou nakonfigurovány UMAC role odpovídající rolím konkrétních uživatelů
- [ ] Místní administrátorský účet je přejmenován a heslo je uloženo v PAM

**Síťová konfigurace**
- [ ] Workstation je ve správné VLAN (Engineering Zone)
- [ ] Není přímé připojení do IT sítě ani na internet
- [ ] Firewall pravidla ověřena: TIA Portal → S7-1500 povolen; ostatní deny
- [ ] NTP synchronizace nakonfigurována na interní NTP server (ověřit správný čas)
- [ ] DNS nakonfigurováno na interní DNS (ne 8.8.8.8 nebo podobné)

**Bezpečnostní konfigurace**
- [ ] Windows Firewall povolen a nakonfigurován
- [ ] AutoRun a AutoPlay deaktivovány (Group Policy)
- [ ] SMBv1 deaktivován
- [ ] BitLocker aktivován (klíč uchováván v AD nebo PAM)
- [ ] Application allowlisting nakonfigurováno a otestováno (TIA Portal spouštění ověřeno)
- [ ] USB port management nakonfigurován (povolena pouze schválená zařízení)
- [ ] Automatický zámek obrazovky po 5 minutách nečinnosti

**Certifikáty a komunikace**
- [ ] Engineering workstation certifikát vydán z enterprise PKI a nainstalován v TIA Portal
- [ ] Secure PG/PC Communication nakonfigurováno a otestováno na testovacím S7-1500
- [ ] Trust store obsahuje pouze certifikáty schválených CA

**Monitoring a logging**
- [ ] Windows Security Event Log forwarding nakonfigurováno na SIEM nebo log relay
- [ ] TIA Portal audit log forwarding nakonfigurováno (kde je podporováno)
- [ ] Workstation zahrnuta v inventáři OT aktiv

**Zálohy a recovery**
- [ ] Počáteční disk image workstation vytvořen a uložen do backup repository
- [ ] Git repository naklonován na workstation
- [ ] Recovery postup zdokumentován a uložen mimo workstation

---

## Checklist 2: Schválení a nasazení projektu do výroby

Použij před každým stažením projektu do produkčního řídicího systému.

### Česky

**Příprava projektu**
- [ ] Projekt je verzován dle schématu MAJOR.MINOR.PATCH
- [ ] Verze projektu odpovídá schválenému release tagu v repository
- [ ] Release package je kompletní (projekt, knihovny, firmware matice, release notes, schválení)
- [ ] SHA256 checksums release package ověřeny
- [ ] Projekt otevřen z repository (ne z lokální záložní kopie nebo starší složky)

**Review a schválení**
- [ ] Proběhl peer review změn (jiný inženýr než autor změny)
- [ ] Proběhl security review (bezpečnostní implikace změn posouzeny)
- [ ] Změna je schválena v Change Management systému
- [ ] Approval record je podepsán a uložen

**Kompatibilita**
- [ ] Verze TIA Portal na engineering workstation odpovídá projektu
- [ ] Verze firmware v řídicím systému je kompatibilní s projektem (dle firmware matice)
- [ ] Verze knihoven odpovídají release package manifestu
- [ ] Verze HSP jsou kompatibilní s hardwarem v projektu

**Online ověření před stažením**
- [ ] Proveden online vs. offline compare (TIA Portal → Online → Porovnání)
- [ ] Výsledek compare zdokumentován (žádné rozdíly / nalezené rozdíly: vysvětlení)
- [ ] Pokud nalezeny neočekávané rozdíly: ZASTAVIT — zahájit incident investigation

**Nasazení**
- [ ] Nasazení probíhá z PAM-schválené session (Release Engineer role)
- [ ] Session recording aktivní po celou dobu nasazení
- [ ] Nasazení provedeno v schváleném maintenance window
- [ ] Rollback postup dostupný a otestován před nasazením

**Po nasazení**
- [ ] Proveden post-download online compare — ověření, že download proběhl správně
- [ ] Vyplněn deployment record (datum, verze, engineering WS, operátor, schvalovatel)
- [ ] Deployment record uložen do repository nebo DMS
- [ ] Inventář aktiv aktualizován (verze projektu v řídicím systému)

---

## Checklist 3: Správa přístupu dodavatelů

Použij před každou remote session dodavatele do engineering prostředí.

### Česky

**Před přístupem**
- [ ] Je zahájena změnová žádost nebo servisní požadavek (zdůvodnění přístupu zdokumentováno)
- [ ] Identita technika dodavatele ověřena (jméno, firma, kontakt — předem)
- [ ] Vydány časově omezené přihlašovací údaje (maximálně na dobu úkonu + buffer)
- [ ] MFA nakonfigurováno pro vydané přihlašovací údaje
- [ ] Technikovi sdělena bezpečnostní politika (co smí a nesmí dělat v engineering prostředí)
- [ ] Schválení přístupu zdokumentováno (kdo schválil, pro jaký účel, na jak dlouho)

**Průběh přístupu**
- [ ] Přístup pouze přes jump server v Engineering DMZ (žádný přímý VPN do Engineering Zone)
- [ ] Session recording aktivní (video + keystroke)
- [ ] Přístup omezen na specifické cílové systémy (ne celou Engineering Zone)
- [ ] Interní koordinátor dostupný pro monitoring (povinný pro SIS nebo kritické systémy)
- [ ] Žádné přenosy souborů mimo schválené kanály (žádný nekontrolovaný USB, cloud upload)

**Po přístupu**
- [ ] Přihlašovací údaje okamžitě zrušeny po dokončení úkonu
- [ ] Session recording uložen a označen (datum, dodavatel, účel) — retention min. 12 měsíců
- [ ] Protokol provedených prací od dodavatele obdržen a uložen
- [ ] Integrity check provedena na řídicích systémech, ke kterým měl dodavatel přístup (online vs. offline compare)
- [ ] Pokud byly provedeny změny: zahájit standard Change Management pro zdokumentování

---

## Checklist 4: Průběžná bezpečnostní provozní připravenost engineering prostředí

Použij čtvrtletně nebo jako roční review.

### Česky

**Inventář a verze**
- [ ] Je inventář engineering workstations aktuální (HW, OS verze, TIA Portal verze)?
- [ ] Jsou verze projektů v repository shodné s verzemi v produkčních řídicích systémech (online vs. offline compare pro klíčové systémy)?
- [ ] Jsou zaznamenány verze firmware všech řídicích systémů?
- [ ] Jsou identifikovány řídicí systémy s end-of-life firmware nebo TIA Portal projekty na nepodporovaných verzích?

**Správa identit a přístupu**
- [ ] Byl proveden přezkum UMAC účtů — jsou odstraněny účty odcházejících zaměstnanců nebo dodavatelů?
- [ ] Jsou všechny aktivní vendorské přístupy stále provozně nezbytné?
- [ ] Jsou hesla administrátorských účtů v PAM aktuální (rotována dle politiky)?
- [ ] Jsou certifikáty engineering workstations platné a blíží se nějaký k expiraci (< 90 dnů)?

**Bezpečnostní záplaty**
- [ ] Jsou OS bezpečnostní záplaty aplikovány dle politiky?
- [ ] Jsou Siemens ProductCERT advisories za uplynulé čtvrtletí přezkoumány?
- [ ] Jsou nalezené zranitelnosti ve TIA Portal nebo S7-1500 firmware zdokumentovány v risk registru?
- [ ] Jsou kompenzační opatření pro dosud neopatchované systémy platná a zdokumentovaná?

**Zálohy a recovery**
- [ ] Jsou zálohy prováděny dle plánu a úspěšné?
- [ ] Byl proveden test obnovy workstation ze zálohy v uplynulém roce?
- [ ] Je repository server zálohován a obnova testována?
- [ ] Jsou offline zálohy release packages aktuální?

**Monitoring**
- [ ] Je SIEM nebo log systém přijímající engineering logy funkční?
- [ ] Jsou alerty na off-hours přihlášení nebo neočekávané controller downloads aktivní?
- [ ] Byly přezkoumány bezpečnostní události za uplynulé období — zjištěny anomálie?

---

# AI Guidance

When answering questions about Siemens TIA Portal security:

* Treat TIA Portal as the **Root of Trust** for Siemens automation systems — not merely a PLC programming application. Compromise of TIA Portal is equivalent to compromise of the controllers it manages.
* Always recommend dedicated engineering workstations — never shared with office tasks.
* Recommend individual named accounts, enterprise AD integration, MFA (hardware token preferred), UMAC, UMC (for S7-1500 environments), and PAM for privileged operations.
* Secure PG/PC Communication must be enabled on all production S7-1500 controllers — emphasize this as a critical control.
* S7-1500 access protection level must be configured to Full access or Full access + PUT/GET protection — No restriction is never acceptable in production.
* Recommend version control (Git/GitLab) for engineering projects — not shared drives or local-only storage.
* Mandatory offline vs. online comparison before every production download — configuration drift is a security incident.
* Recommend enterprise PKI over self-signed certificates for production environments.
* OPC UA must use Sign and Encrypt security profile for all inter-zone connections — None profile is not acceptable in production.
* Vendor remote access: time-limited credentials, MFA, jump server, session recording, immediate revocation — all mandatory, no exceptions.
* Recovery must restore the complete engineering environment (TIA Portal version, HSPs, libraries, certificates, documentation) — not just the project file.
* Align recommendations to IEC 62443 (Foundational Requirements, Security Levels, Engineering Zone design), NIS2 Article 21, and Czech Cybersecurity Act Decree 409/410.
* When discussing patch management, always note that TIA Portal version upgrades require project compatibility assessment and Change Management — not routine automatic updates.
* Reference [IEC62443-Overview.md](#), [NIS2.md](#), [Czech-Cybersecurity-Act.md](#), and [ISA95.md](#) for broader regulatory and architectural context.

---

# Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | 2026-06 | Initial release (8 separate documents) |
| 1.1.0 | 2026-06-29 | Consolidated into single document; major expansion: full threat model with attack consequence analysis; 9-layer defense-in-depth model; Engineering Zone architecture with conduit allowlist table; Secure PG/PC Communication deep-dive; UMAC role table with descriptions; UMC architecture diagram; S7-1500 access level table; PAM capability table; vendor access control requirements table; OS hardening baseline table; application allowlisting guidance for TIA Portal; removable media control hierarchy; certificate type lifecycle table; PKI requirements; certificate monitoring alert thresholds; private key protection table; complete backup scope table with frequency and storage; 4 recovery scenarios with detailed procedures; RTO/RPO guidance; patch management process flow; TIA Portal version compatibility management; firmware update process; vulnerability intelligence sources; IEC 62443 FR alignment table; NIS2/Decree 409 alignment table; 4× checklist in Czech language |
