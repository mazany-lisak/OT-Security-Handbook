---
id: plantscada
title: "AVEVA Plant SCADA — Security Architecture and Engineering Reference"
category: Technologies
layer: "08-Technologies/AVEVA/PlantSCADA"
vendor: "AVEVA"
product: "Plant SCADA"
version: 2.0.1
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Security architecture and engineering reference for AVEVA Plant SCADA (formerly Citect):
  component and redundancy architecture, threat model, identity model (roles, privilege levels,
  areas, Windows/AD integration), Cicode and CtAPI governance, phased hardening, communications
  security, monitoring, backup and recovery.
keywords: [AVEVA, Plant SCADA, Citect, CitectSCADA, SCADA security, Cicode, CtAPI, privilege levels, redundancy, Access Anywhere, hardening]
---

# Purpose

This document provides a security architecture and engineering reference for **AVEVA Plant SCADA** (formerly Schneider Electric **Citect SCADA**) from the perspective of an OT Security Architect.

Plant SCADA is a supervisory control and data acquisition platform providing operator visualization, alarm management, trending, reporting and industrial connectivity at Level 2 of the Purdue Model. Because it sits directly between operators and controllers, its compromise offers an attacker both **process visibility** and — through command capability and its scripting engine — a degree of **process influence** second only to the engineering workstation.

This document covers what an architect must decide and verify when deploying, hardening and operating Plant SCADA. Configuration click-paths are intentionally excluded; consult the vendor documentation for the deployed version.

> **Verification note.** Capabilities, defaults and component ports vary by release (CitectSCADA 7.x → Citect SCADA 2016/2018 → Plant SCADA 2020 R2 and later). Statements below describe the platform family; always verify against the release notes and the *network communications / security* documentation of the version actually deployed.

---

# Related Documents

* [Purdue-Model.md](../../../02-Standards/Purdue-Model.md) — where Plant SCADA sits in the trust hierarchy.
* [IEC62443.md](../../../02-Standards/IEC62443.md) — zones, conduits and Security Levels applied around the SCADA zone.
* [OT-Protocols.md](../../../04-Network/OT-Protocols.md) — security of the southbound protocols the drivers speak.
* [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md), [Firewall-Design.md](../../../04-Network/Firewall-Design.md) — enforcing the SCADA zone boundary.
* [Identity-Management.md](../../../05-Identity/Identity-Management.md), [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md), [MFA.md](../../../05-Identity/MFA.md) — the identity layer this platform plugs into.
* [Monitoring.md](../../../09-Operations/Monitoring.md), [Logging.md](../../../09-Operations/Logging.md), [SIEM.md](../../../09-Operations/SIEM.md) — telemetry consumption.
* [Backup.md](../../../09-Operations/Backup.md), [Disaster-Recovery.md](../../../09-Operations/Disaster-Recovery.md), [Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md) — recoverability.

---

# Document Structure

1. Overview — lineage, components, architectures, Purdue placement
2. Security — threat model, defense in depth, IEC 62443 alignment
3. Identity and Access — roles, privilege levels, areas, AD integration, remote access
4. Hardening — phased hardening of servers, clients, project and APIs
5. Communications Security — component flows, encryption, drivers
6. Monitoring and Logging
7. Backup, Redundancy and Recovery
8. Best Practices and Common Mistakes

---

# 1 — Overview

## 1.1 Platform Role and Lineage

Plant SCADA originated as **Citect** (Ci Technologies, Australia), became **CitectSCADA**, was acquired by **Schneider Electric** (2006), moved with Schneider's industrial-software portfolio into **AVEVA**, and was renamed **AVEVA Plant SCADA** with the 2020 R2 release. The installed base therefore spans two decades of versions under three names — a fact with direct security consequences: many production systems still run releases that predate encrypted component communications, modern role management and supported operating systems.

Plant SCADA is a **plant-centric** SCADA: it assumes reasonably reliable plant networking and continuous connectivity to controllers. For geographically distributed telemetry over unreliable WAN links, the sibling product is [GeoSCADA.md](../../Schneider%20Electric/GeoSCADA/GeoSCADA.md) — the two solve different problems and have different trust models.

## 1.2 Component Architecture

A Plant SCADA system is a set of cooperating server roles plus clients, all defined in one compiled project:

| Component | Function | Security relevance |
|---|---|---|
| **I/O Server** | Communicates with PLCs/RTUs through protocol drivers; serves tag data | Holds controller connectivity and write capability; the highest-value server role |
| **Alarms Server** | Alarm detection, state, shelving, summary | Alarm suppression/shelving abuse hides process manipulation |
| **Trends Server** | Historical trend collection | Data integrity; feeds investigations |
| **Reports Server** | Scheduled/event reports, can execute Cicode | Scheduled code-execution path |
| **Control Client / View-only Client** | Operator HMI (full or read-only) | Operator workstations; command issuance |
| **Web Server + Web Client (legacy)** | Browser-based deployment (IIS-hosted) | Additional attack surface; legacy technology |
| **Plant SCADA Access Anywhere** | HTML5 remote-access gateway to clients | The controlled remote-access path; a conduit termination point |
| **CtAPI** | External programmatic API for tag read/write | Machine-to-machine access to live process data — must be governed |
| **Cicode / CitectVBA** | Built-in scripting executed by servers and clients | Arbitrary logic incl. file/OS interaction; a code-execution surface |

Servers can be combined on one host (small systems) or distributed and made redundant (see 7.2). Multiple **clusters** allow one project to supervise several plant areas or sites.

## 1.3 ISA-95 / Purdue Placement

* Servers and operator clients: **Level 2** (supervisory control).
* Engineering (Plant SCADA Studio, project compilation): a **separate engineering zone** — engineering is administrative activity, not operations ([Purdue-Model.md](../../../02-Standards/Purdue-Model.md) → Engineering ↔ Operations boundary).
* Northbound integration (AVEVA Historian replica, MES/ERP, reporting): through **Level 3 / the Industrial DMZ**, never directly from the SCADA servers to enterprise users.
* Access Anywhere gateway: terminates in the **IDMZ**, brokering browser access without exposing clients or servers.

Deterministic and safety-relevant control logic belongs in controllers, never in Cicode: Plant SCADA supervises the process, it must not *be* the process.

## 1.4 Typical Architectures

**Single server (small plant).** All server roles + client on one hardened host. Acceptable only where the availability requirement tolerates single-host failure; still segment it into its own zone.

**Redundant pair (standard).** Primary/standby pairs for I/O, Alarms, Trends and Reports roles; clients fail over automatically. This is the baseline for production plants and also enables **rolling patching** (patch standby → fail over → patch former primary).

**Distributed / multi-cluster.** Separate clusters per production area or site under one project, each cluster in its own zone with defined conduits. Cluster separation should mirror the zone model — do not let a single flat SCADA network span areas with different consequence levels.

**Virtualized.** Server roles as VMs is common and supported; apply hypervisor hardening, host anti-affinity for redundant pairs, and include the virtualization layer in the recovery plan.

## 1.5 Security-Relevant Capability Evolution

Qualitative guide (verify per release notes):

* **Legacy 7.x era** — Windows integrated security, areas and privilege levels present; component traffic essentially unencrypted; Web Client on IIS.
* **Citect SCADA 2018** — introduction of **encrypted client–server communications (TLS)** between components; certificate management for component trust.
* **Plant SCADA 2020 R2** — rebrand; continued TLS improvements; Access Anywhere as the strategic remote-access path.
* **Plant SCADA 2023 / 2023 R2** — current-generation OS support, OPC UA client connectivity, deeper AVEVA portfolio integration (Historian, operations-management visualization).

Architectural consequence: **on pre-2018 releases, confidentiality and integrity of SCADA traffic must be provided entirely by the network architecture** (zone isolation, IPsec where justified) because the platform cannot provide it itself. Treat this as a compensating-control situation ([Risk-Based-Security.md](../../../03-Architecture/Risk-Based-Security.md)).

---

# 2 — Security

## 2.1 Security Philosophy

Plant SCADA concentrates three things an attacker wants: **visibility** (every tag, alarm and trend), **command** (operator write capability through the I/O Server) and **code execution** (Cicode on servers and clients). The platform's protection is therefore less about the product's own switches and more about the architecture around it: a tightly bounded SCADA zone, a disciplined identity model, governed scripting and APIs, and monitored conduits.

## 2.2 Threat Model

Realistic adversary goals and paths, ordered by consequence:

1. **Operator-client compromise** (phishing on a dual-homed workstation, USB, browser) → issue commands within the operator's privilege; observe the process.
2. **Engineering compromise** (project-source access) → modify graphics, alarms, Cicode; insert logic executed plant-wide at next deployment. Highest-consequence path; treat identically to PLC engineering ([ICS-Kill-Chain.md](../../../07-Threat-Intelligence/ICS-Kill-Chain.md) → Engineering Access).
3. **Cicode/CitectVBA abuse** — scripting can call OS-level functions and execute processes; malicious or careless code on a Reports Server runs unattended and scheduled.
4. **CtAPI abuse** — an external application (or attacker on the network) reading/writing tags programmatically, bypassing the operator UI and its audit trail.
5. **Alarm-path manipulation** — suppressing, shelving or re-prioritizing alarms to blind operators during process manipulation (ATT&CK for ICS: *Inhibit Response Function*; see [MITRE-ATTACK-ICS.md](../../../07-Threat-Intelligence/MITRE-ATTACK-ICS.md)).
6. **Driver/southbound spoofing** — unauthenticated southbound protocols (Modbus, legacy S7) allow response spoofing or direct controller writes from the SCADA segment ([OT-Protocols.md](../../../04-Network/OT-Protocols.md)).
7. **Unpatched platform vulnerabilities** — the product line has a public advisory history (from the classic CitectSCADA ODBC overflow onward); track AVEVA security advisories and CISA ICS advisories, prioritize via [Vulnerability-Management.md](../../../09-Operations/Vulnerability-Management.md).
8. **Legacy web deployment** — the IIS-hosted Web Client adds a web attack surface inside the SCADA zone.

## 2.3 Defense in Depth Applied

| Layer | Plant SCADA specifics |
|---|---|
| Physical | Control-room access; server room; no casual USB on clients |
| Network | Dedicated SCADA zone; conduits only to controller zone, engineering zone, IDMZ; no enterprise reachability |
| Identity | AD-integrated roles, named operators, privilege 1–8 + areas, MFA on remote/engineering paths |
| Application | TLS component comms; CtAPI restricted; Cicode governance; Web Client removed or isolated |
| Endpoint | Hardened Windows, allowlisting on servers and clients, no email/browsing on operator stations |
| Monitoring | Component/security logs to SIEM; alarm-shelving and command auditing |
| Recovery | Project version control, tested restores, redundant pairs, DR plan |

The layered model itself is defined in [Defense-in-Depth.md](../../../03-Architecture/Defense-in-Depth.md).

## 2.4 IEC 62443 Alignment

* Treat the Plant SCADA servers as one **zone** with an explicit Target Security Level from the [IEC62443.md](../../../02-Standards/IEC62443.md) risk assessment; operator clients may form a sub-zone.
* FR1/FR2 (identification, use control) → AD roles, privilege levels, named accounts, MFA on privileged paths.
* FR3/FR4 (integrity, confidentiality) → TLS component communications (2018+), controlled project deployment, protected trend/alarm data.
* FR5 (restricted data flow) → conduit design in 5.1.
* FR6 (timely response) → logging/detections in section 6.
* Where a deployed version cannot meet a requirement natively (legacy releases), document **compensating controls** rather than pretending the SL is met.

---

# 3 — Identity and Access

## 3.1 Identity Model

Plant SCADA authorization combines three constructs:

* **Roles** — map **Windows/AD groups** to platform permissions; the supported modern model. Native (project-defined) users exist for legacy/standalone cases and should be avoided in production.
* **Privilege levels 1–8** — each command, page or writable object requires a privilege; each role grants privileges.
* **Areas (up to 255)** — partition the plant so privileges apply per area; an operator can command area A and only view area B.

Design rules:

* Model roles on **operational reality** (operator line 1, shift lead, maintenance, engineer, viewer), not on org charts.
* Reserve the highest privilege levels for engineering/administrative functions and keep them out of shift-operator roles.
* Use areas to enforce **blast-radius limits**: a compromised operator session must not be able to command the whole plant.
* No shared operator accounts. Where a control-room console must stay logged in, use a **low-privilege view account** for the idle state and require per-operator elevation (re-authentication) for commands — do not solve console availability with a powerful always-on login.

## 3.2 Windows / AD Integration

* Put SCADA hosts in the **OT domain** (or a hardened resource forest), never the enterprise domain ([Identity-Management.md](../../../05-Identity/Identity-Management.md)).
* Service accounts for server roles: least privilege, no interactive logon, managed rotation.
* Define authentication behaviour for domain-controller outage (documented, audited local emergency account — see [MFA.md](../../../05-Identity/MFA.md) → Emergency Access).

## 3.3 Engineering Access

Plant SCADA Studio and project sources belong on **dedicated engineering workstations** in the engineering zone, under [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md): MFA, JIT elevation for deployment, session recording, and change control for every compile/deploy ([Change-Management.md](../../../09-Operations/Change-Management.md)). Deployment to production servers is a privileged operation, not a routine save.

## 3.4 Remote Access

* The supported pattern is **Access Anywhere in the IDMZ**: browser → HTTPS → gateway → published client. Enforce MFA at or before the gateway, record sessions, time-limit vendor access ([Secure-Remote-Access.md](../../../04-Network/Secure-Remote-Access.md)).
* Never publish Control Clients, servers, RDP to servers, or the legacy Web Server directly to enterprise or Internet reachability.

---

# 4 — Hardening

Phased, in dependency order; each phase concludes with validation and a configuration-baseline capture ([Configuration-Management.md](../../../09-Operations/Configuration-Management.md)).

## 4.1 Phase 1 — Platform (OS) Baseline

Windows servers/clients hardened per organizational baseline (see [Windows-Hardening.md](../../../09-Operations/Windows-Hardening.md), planned): minimal roles/features, application allowlisting on servers and operator clients, removable-media policy, EDR where vendor-supported, no browsing/email capability on operator stations.

## 4.2 Phase 2 — Installation Minimization

Install only the required server roles per host; do not install Studio (engineering) on servers or operator clients; skip the legacy Web Server/Web Client entirely unless a documented requirement exists — Access Anywhere supersedes it.

## 4.3 Phase 3 — Component Communications

On 2018+ releases, **enable encrypted (TLS) client–server communications** and deploy component certificates from the internal PKI ([PKI.md](../../../05-Identity/PKI.md), [Certificates.md](../../../05-Identity/Certificates.md)); plan certificate renewal as an operational process. On earlier releases, document the gap and compensate at the network level.

## 4.4 Phase 4 — Identity Configuration

Implement section 3: AD roles, privilege/area matrix, removal of default/native accounts, service-account least privilege. Produce the **role → privilege → area matrix** as a controlled document; auditors and incident responders will need it.

## 4.5 Phase 5 — Scripting and API Governance

* **Cicode/CitectVBA**: mandatory code review before deployment; prohibit OS-command execution and file-system access in runtime code unless justified and documented; keep scheduled Cicode on the Reports Server under change control.
* **CtAPI**: inventory every consumer; dedicated least-privilege account per integration; restrict source hosts at the firewall; encrypt where the version supports it; log all CtAPI writes.
* Remove sample projects, demo code and unused included projects from production.

## 4.6 Phase 6 — Network Hardening

Place servers in a dedicated zone; allow only: clients→servers, servers→controller zone (driver protocols), engineering→servers (deployment window only), servers→IDMZ (historian replication, Access Anywhere, time, logging). Default-deny everything else ([Firewall-Design.md](../../../04-Network/Firewall-Design.md)). Southbound, prefer OPC UA or secured native drivers where controllers support them; treat unauthenticated protocols as zone-internal only ([OT-Protocols.md](../../../04-Network/OT-Protocols.md)).

## 4.7 Hardening Validation

Verify: the role matrix behaves as designed (test per role); TLS is actually negotiated between components; CtAPI is reachable only from inventoried hosts; alarm shelving is restricted and audited; deployment is possible only from engineering identities; the baseline is captured.

---

# 5 — Communications Security

## 5.1 Component Flows and Conduit Design

Design the conduit table from actual flows. Component ports are configurable and version-specific — take them from the deployed version's network-communications documentation, never from memory:

| Flow | Direction | Notes |
|---|---|---|
| Client ↔ I/O / Alarms / Trends / Reports Servers | client→server | TLS on 2018+; the core operational traffic |
| Server ↔ redundant partner | bidirectional | Synchronization; keep inside the SCADA zone |
| I/O Server ↔ controllers | server→controller zone | Driver protocols; the most consequence-laden conduit |
| Engineering → servers | eng zone→SCADA zone | Deployment only; open per change window where feasible |
| Servers → IDMZ | outbound | Historian replication, logging, NTP; terminate in the IDMZ |
| Browser → Access Anywhere | enterprise→IDMZ | HTTPS + MFA; gateway → published client only |
| CtAPI consumers → I/O Server | per inventory | Explicit allowlist per integration |

## 5.2 Time Synchronization

All servers and clients on the OT NTP hierarchy; alarm/trend forensics and SIEM correlation are worthless without it ([Logging.md](../../../09-Operations/Logging.md) → Time Synchronization).

---

# 6 — Monitoring and Logging

Forward to the SIEM ([SIEM.md](../../../09-Operations/SIEM.md)) and treat as high-value detections ([Monitoring.md](../../../09-Operations/Monitoring.md)):

* Authentication successes/failures on servers, clients and Access Anywhere; use of emergency accounts.
* **Operator command writes** (who, what tag, which client, which area) — the platform's command audit is the process-manipulation record.
* **Alarm shelving/suppression and alarm-configuration changes** — blinding precedes manipulation.
* Project **deployment/compile events** and any runtime file change on servers outside a change window (drift → [Configuration-Management.md](../../../09-Operations/Configuration-Management.md)).
* CtAPI connections and writes; new CtAPI source hosts.
* Driver/communication failures and redundancy failovers (also an availability signal).
* Windows security logs of all SCADA hosts.

---

# 7 — Backup, Redundancy and Recovery

## 7.1 What to Back Up

Compiled runtime **and** project sources (version-controlled), alarm/trend databases per retention policy, component certificates and PKI dependencies, license files, driver configurations, the role/privilege/area matrix, and the documented server topology. Follow [Backup.md](../../../09-Operations/Backup.md); validate restores per [Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md).

## 7.2 Redundancy ≠ Recovery

Redundant pairs protect availability against single-host failure and enable rolling patching; they replicate **corruption and compromise just as faithfully as data**. Ransomware or a malicious deployment reaches both partners. Offline backups and a rehearsed [Disaster-Recovery-Plan.md](../../../09-Operations/Disaster-Recovery-Plan.md) remain mandatory; recovery is complete only at a validated Trusted Operational State (alarms, trends, commands, certificates and southbound communications all verified).

## 7.3 Patching Strategy

Use the redundant architecture: patch standby → verify → controlled failover → patch former primary — within change management and with a pre-change backup ([Patch-Management.md](../../../09-Operations/Patch-Management.md)). Confirm AVEVA-supported OS/patch combinations before deployment.

---

# 8 — Best Practices and Common Mistakes

**Practices that pay off:** the role/privilege/area matrix as a governed artifact; engineering fully separated from operations; Cicode review as a hard gate; Access Anywhere as the only remote path; command and alarm-shelving audits wired into detections; rolling patching rehearsed.

**Common mistakes:**

* Studio installed on operator clients "for convenience" — engineering capability on the plant floor.
* One powerful shared operator login on every console.
* Privilege 8 granted broadly because area/privilege design was skipped.
* Cicode with OS-execution calls deployed without review.
* CtAPI open to the whole subnet with a shared credential.
* The legacy Web Server exposed because it "was already there".
* A pre-2018 release with cleartext component traffic on a flat network shared with the enterprise.
* Redundancy mistaken for backup; restores never tested.
* Alarm shelving unrestricted and unlogged.

---

# Architect Notes

Plant SCADA rewards architectural discipline more than product tuning. The three decisions that dominate its real-world security posture are: **where the zone boundary sits and what crosses it**, **how engineering is separated and governed**, and **whether the scripting/API surfaces (Cicode, CtAPI) are inventoried and reviewed**. A current, TLS-enabled release on a flat network with ungoverned Cicode is less secure than a legacy release inside a disciplined architecture — though the correct target is, of course, both.

---

# Decision Support

1. Which release is deployed, and does it support encrypted component communications? If not, what compensates?
2. Is the role → privilege → area matrix designed, documented and tested?
3. Where do engineering workstations sit, and how is deployment to production controlled?
4. What is the complete CtAPI/Cicode inventory, and who reviewed the code?
5. Which southbound protocols cross the controller conduit, and which of them support authentication?
6. Is Access Anywhere (IDMZ, MFA, recording) the only remote path?
7. Are operator commands and alarm shelving audited and forwarded to the SIEM?
8. When was a restore of the full SCADA stack last demonstrated, and against which RTO?

---

# AI Guidance

When discussing AVEVA Plant SCADA:

* Treat it as a Level 2 supervisory platform whose compromise grants visibility, command and code execution; anchor recommendations in zone/conduit design, identity (roles/privilege/areas) and Cicode/CtAPI governance.
* Distinguish version generations — do not assume encrypted component communications or modern role management on legacy Citect releases; recommend compensating network controls there.
* Keep deterministic and safety logic in controllers; flag Cicode-implemented control as an anti-pattern.
* Recommend Access Anywhere via the IDMZ with MFA as the only remote path; advise against the legacy Web Client.
* Never quote component port numbers from memory; direct users to the deployed version's network documentation.
* Delegate protocol detail to OT-Protocols.md, identity depth to the 05-Identity layer and recovery process to the 09-Operations layer.
* For geographically distributed telemetry use cases, redirect to GeoSCADA.md rather than stretching Plant SCADA.

---

# Sources / Grounding

* **AVEVA Plant SCADA product documentation** — installation/architecture, network communications, security and Access Anywhere guides for the deployed release (authoritative for ports, defaults and capabilities).
* **AVEVA security advisories / CISA ICS advisories** — the product line's vulnerability history and current patch guidance.
* **ISA/IEC 62443** — zone/conduit design and FR/SL framing around the SCADA zone. See [IEC62443.md](../../../02-Standards/IEC62443.md).
* **NIST SP 800-82 Rev. 3** — OT security-architecture context.

> Foundational references; the vendor documentation of the deployed version is authoritative for product specifics.

---

# Related Documents

* [Purdue-Model.md](../../../02-Standards/Purdue-Model.md)
* [IEC62443.md](../../../02-Standards/IEC62443.md)
* [ISA95.md](../../../02-Standards/ISA95.md)
* [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md)
* [Firewall-Design.md](../../../04-Network/Firewall-Design.md)
* [OT-Protocols.md](../../../04-Network/OT-Protocols.md)
* [Secure-Remote-Access.md](../../../04-Network/Secure-Remote-Access.md)
* [Identity-Management.md](../../../05-Identity/Identity-Management.md)
* [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md)
* [MFA.md](../../../05-Identity/MFA.md)
* [PKI.md](../../../05-Identity/PKI.md)
* [Certificates.md](../../../05-Identity/Certificates.md)
* [Monitoring.md](../../../09-Operations/Monitoring.md)
* [Logging.md](../../../09-Operations/Logging.md)
* [SIEM.md](../../../09-Operations/SIEM.md)
* [Backup.md](../../../09-Operations/Backup.md)
* [Disaster-Recovery.md](../../../09-Operations/Disaster-Recovery.md)
* [Disaster-Recovery-Plan.md](../../../09-Operations/Disaster-Recovery-Plan.md)
* [Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md)
* [Change-Management.md](../../../09-Operations/Change-Management.md)
* [Configuration-Management.md](../../../09-Operations/Configuration-Management.md)
* [Patch-Management.md](../../../09-Operations/Patch-Management.md)
* [Vulnerability-Management.md](../../../09-Operations/Vulnerability-Management.md)
* [PI-System.md](../PI%20System/PI-System.md) — the typical downstream historian consuming Plant SCADA data.
* [GeoSCADA.md](../../Schneider%20Electric/GeoSCADA/GeoSCADA.md)
* [MITRE-ATTACK-ICS.md](../../../07-Threat-Intelligence/MITRE-ATTACK-ICS.md)
* [ICS-Kill-Chain.md](../../../07-Threat-Intelligence/ICS-Kill-Chain.md)

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release (architecture overview) |
| 1.1.0 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings; migrated from draft header (no front matter) to canonical schema |
| 2.0.0 | 2026-07-07 | Expanded from architecture overview to a full platform security reference matching the S7-1500/M580 document standard: lineage and version-capability evolution (Citect → Plant SCADA; TLS from 2018), component table with security relevance, threat model (engineering, Cicode/CtAPI, alarm path, southbound spoofing), IEC 62443 alignment, identity model (roles / privilege 1–8 / areas / AD), phased hardening, conduit-flow table, monitoring detections, redundancy-vs-recovery and rolling patching; cross-linked to GeoSCADA.md |
| 2.0.1 | 2026-07-07 | Added cross-link to PI-System.md as the typical downstream historian |
