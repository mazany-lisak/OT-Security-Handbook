---
id: windows-hardening
title: "Windows Hardening in OT"
category: Operations
layer: "09-Operations"
version: 1.0.0
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Hardening Windows hosts in OT (SCADA/historian/OPC servers, engineering workstations, jump
  hosts): OT domain architecture, service and RPC/DCOM minimisation, identity and privilege
  controls, PowerShell/logging, patching within OT constraints, EDR/allowlisting, and the DCOM
  containment this handbook's OPC DA guidance depends on.
keywords: [Windows hardening, zabezpečení Windows, DCOM, GPO, LAPS, PowerShell logging, engineering workstation, OT domain, application allowlisting, CIS Benchmark]
---

# Purpose

Windows is the operating system underneath a large share of the assets this handbook documents in detail elsewhere: SCADA/historian servers ([PlantSCADA.md](../08-Technologies/AVEVA/PlantSCADA/PlantSCADA.md), [GeoSCADA.md](../08-Technologies/Schneider%20Electric/GeoSCADA/GeoSCADA.md), [PI-System.md](../08-Technologies/AVEVA/PI%20System/PI-System.md)), OPC aggregation platforms ([Kepware-Server.md](../08-Technologies/PTC/Kepware%20Server/Kepware-Server.md), [dataFEED-OPC-Suite.md](../08-Technologies/Softing/dataFEED%20OPC%20Suite/dataFEED-OPC-Suite.md)), and — perhaps most consequentially — every **engineering workstation** referenced throughout `08-Technologies` as the single highest-value target in an OT estate. Every one of those documents says "harden the host per organisational Windows baseline" and points here. This document is that baseline.

Windows hardening in OT is not identical to Windows hardening in a standard enterprise IT estate. Availability constraints are stricter, patch windows are narrower and scarcer, some legacy protocols (OPC Classic/DCOM in particular) cannot simply be turned off without breaking production, and a compromised OT Windows host frequently sits one step away from physical consequence rather than data loss. This document adapts general-purpose Windows hardening guidance (principally the CIS Benchmarks) to those OT-specific realities.

---

# Scope

This document covers: OT domain/identity architecture, service and RPC/DCOM minimisation (the prerequisite for the OPC DA containment documented in [OT-Protocols.md](../04-Network/OT-Protocols.md)), local privilege and credential controls, remote access hardening (RDP/SMB), PowerShell and logging configuration, application allowlisting, patching within OT constraints, and endpoint protection considerations. It does not repeat general Windows administration guidance available from Microsoft and CIS — it states the OT-adapted baseline and the reasoning behind each adaptation.

---

# Security Philosophy

A Windows host in OT is rarely valuable to an attacker for the data stored on it — it is valuable for the **access it provides**: to an engineering toolchain, to an OPC/historian data path, to a network segment otherwise unreachable. Hardening should therefore prioritise controls that reduce lateral-movement and privilege-escalation potential (least privilege, service minimisation, RPC/DCOM containment) at least as highly as controls that protect data confidentiality — an inversion of typical enterprise-IT emphasis that architects should apply deliberately, not by default habit.

---

# OT Domain and Identity Architecture

* **A separate OT domain (or a hardened resource forest), never the enterprise domain directly.** OT hosts authenticating against the same Active Directory as general corporate IT means a corporate-side compromise is one hop from OT. Where full domain separation is impractical, use a resource forest with a one-way or tightly scoped trust, not a full bidirectional trust to the enterprise forest ([Identity-Management.md](../05-Identity/Identity-Management.md)).
* **No local administrator credential reuse across hosts.** The same local admin password on every OT Windows host turns compromise of one into compromise of all — use unique, managed local administrator credentials per host (a LAPS-equivalent mechanism) rather than a shared image-inherited password.
* **Named accounts, no shared logins**, consistent throughout this handbook's identity guidance ([Identity-Management.md](../05-Identity/Identity-Management.md), [Privileged-Access-Management.md](../05-Identity/Privileged-Access-Management.md)) — this applies as much to the underlying Windows host as to the application running on it.
* **MFA on every privileged and remote logon** ([MFA.md](../05-Identity/MFA.md)), with a documented, audited emergency-access exception for domain-controller/identity-infrastructure outage scenarios.

---

# Service and Feature Minimisation

* **Install only required roles and features.** A SCADA server, historian, or OPC aggregation host should run the minimum Windows Server role set needed for its function — every additional installed role/feature is attack surface with no operational benefit.
* **Disable every service not required by the specific application(s) running on the host.** Review the default service list against actual requirements; do not accept vendor-installer defaults uncritically.
* **No general-purpose use on single-function hosts.** A SCADA server or engineering workstation should not run email, general web browsing, or unrelated productivity software — this is stated throughout `08-Technologies` platform documents as a hard rule, and it is enforced at the Windows configuration level (application allowlisting, browser removal/restriction) rather than left as a policy expectation alone.

---

# RPC and DCOM — the OPC DA Prerequisite

This section exists because [OT-Protocols.md](../04-Network/OT-Protocols.md) § 13 and every reference to legacy OPC DA throughout `08-Technologies` depend on it: **DCOM's dynamic RPC port allocation and complex permission model is the single most common source of both operational fragility and security misconfiguration in OT Windows environments.**

* **Restrict the dynamic RPC port range** to a defined, firewallable band rather than the full ephemeral range, wherever the applications involved support it — this is what makes it possible to write a meaningful firewall rule around DCOM traffic at all.
* **Configure DCOM launch and access permissions explicitly**, per application, rather than accepting the common (and dangerous) commissioning-time shortcut of granting Everyone/Anonymous DCOM access to "make it connect." Every OPC DA deployment problem traced to DCOM in practice originates here.
* **Never bridge DCOM/OPC DA traffic across a firewall boundary.** This handbook's standing architectural rule — terminate OPC DA on an OPC aggregation server on the OT side of the conduit ([ISA95.md](../02-Standards/ISA95.md), [Kepware-Server.md](../08-Technologies/PTC/Kepware%20Server/Kepware-Server.md) § 4.2, [dataFEED-OPC-Suite.md](../08-Technologies/Softing/dataFEED%20OPC%20Suite/dataFEED-OPC-Suite.md) § 4.2) — is only enforceable if the Windows-level DCOM configuration on that aggregation host is itself correctly scoped.
* **Actively plan migration off DCOM-dependent OPC DA** toward OPC UA ([OPC-UA.md](../04-Network/OPC-UA.md)) — DCOM hardening reduces risk, it does not eliminate the underlying legacy-protocol exposure.

---

# Local Privilege and Credential Controls

* **No routine administrative use.** Engineers and operators use standard, least-privileged accounts for daily work; administrative rights are elevated Just-In-Time through PAM ([Privileged-Access-Management.md](../05-Identity/Privileged-Access-Management.md)), not held standing on the primary working account.
* **Credential Guard / protected credential storage** where the Windows edition and hardware support it, to reduce the value of credential-dumping techniques against a compromised host.
* **Restrict interactive logon rights** on server-class OT hosts (SCADA, historian, OPC aggregation servers) to the specific accounts that require them — most operational access to these systems should be through the application, not an interactive desktop session.
* **Disable or tightly restrict cached domain credentials** on hosts where offline logon is not operationally required, reducing what a stolen device yields.

---

# Remote Access Hardening (RDP / SMB)

* **RDP restricted to jump hosts / PAM-brokered sessions only** — no direct RDP from general networks to SCADA, historian, engineering, or OPC-aggregation hosts, consistent with [Secure-Remote-Access.md](../04-Network/Secure-Remote-Access.md). Where RDP is used, enforce Network Level Authentication and current TLS settings.
* **SMB hardening** — disable SMBv1 unconditionally (it has no place in a current OT Windows estate); enable SMB signing; restrict SMB reachability to the specific hosts that require file-share access rather than leaving it open estate-wide.
* **Disable unused remote-management protocols** (WinRM, legacy remote registry, etc.) on hosts that do not require them for their specific function.

---

# PowerShell and Scripting

* **Enable PowerShell logging** — Script Block Logging and Module Logging at minimum, forwarded to the SIEM ([Logging.md](../09-Operations/Logging.md), [SIEM.md](../09-Operations/SIEM.md)). PowerShell is a common living-off-the-land technique surface, and its absence from logging scope is a common, avoidable blind spot.
* **Constrained Language Mode** on hosts where full PowerShell scripting capability is not an operational requirement (most SCADA/historian/OPC servers), restricting what an attacker who gains code execution can actually do with the shell.
* **Application allowlisting governs script execution too** — a scripting engine is only as dangerous as what it's permitted to invoke; pair PowerShell restriction with the allowlisting policy below rather than relying on either alone.

---

# Application Allowlisting

Application allowlisting (only explicitly approved executables/scripts may run) is referenced as a control throughout this handbook's `08-Technologies` documents and is one of the highest-value controls available for single-function OT Windows hosts specifically because those hosts have a small, well-defined, rarely-changing set of legitimate executables:

* Deploy allowlisting on every SCADA/historian/OPC-aggregation server and every engineering workstation.
* Baseline the allowlist against the as-built application set (tie this to [Configuration-Management.md](../09-Operations/Configuration-Management.md) — allowlist changes should follow the same change-control discipline as any configuration baseline change).
* Treat an allowlist-blocked execution attempt as a monitored security event, not a silent nuisance ([Monitoring.md](../09-Operations/Monitoring.md)).

---

# Patching Within OT Constraints

Windows patching in OT follows [Patch-Management.md](../09-Operations/Patch-Management.md) in full; the OT-specific realities worth restating here:

* **Maintenance windows are scarcer and narrower than in IT** — plan Windows update cadence around them explicitly rather than IT's default patching schedule.
* **Vendor certification matters** — SCADA/historian/engineering-tool vendors frequently certify specific Windows update levels against their software; verify vendor guidance before deploying a Windows update to a host running vendor OT applications, rather than assuming general Microsoft guidance alone is sufficient.
* **Compensating controls when patching must wait** — network containment, allowlisting and monitoring (this document's other sections) are exactly the compensating controls [Risk-Based-Security.md](../03-Architecture/Risk-Based-Security.md) recommends for the gap between disclosure and a validated, deployable patch.

---

# Endpoint Protection (EDR/AV)

* **Deploy endpoint protection compatible with the vendor OT application stack** — verify vendor support/certification before deploying EDR/AV to a SCADA, historian, or controller-adjacent host; an incompatible agent can itself cause an operational incident.
* **Exclude only what vendor guidance specifically requires**, and document every exclusion — broad, undocumented exclusions quietly reopen the exposure the agent was deployed to close.
* **Forward EDR telemetry to the SIEM** ([SIEM.md](../09-Operations/SIEM.md)) rather than treating the agent's local console as the only place its findings are ever reviewed.

---

# Physical and Media Controls

* **Removable media policy** — disable or tightly control USB mass-storage on SCADA/historian/OPC/engineering hosts; where removable media is operationally required (e.g. certain PLC firmware transfer workflows), require a documented, logged exception process rather than open access.
* **BitLocker / disk encryption** where hardware and operational constraints allow, particularly for engineering laptops that leave a controlled facility — a stolen unencrypted engineering laptop is both a data-confidentiality and a credential/project-exposure incident.
* **Secure Boot** where the hardware and OS support it without breaking vendor application compatibility — verify before enabling on any host running specialised OT vendor software.

---

# Logging and Time Synchronisation

* Enable and forward **Windows Security Event Logs**, **Sysmon** (or an equivalent enhanced-telemetry agent) where operationally supportable, and the PowerShell logs above, to the SIEM ([Logging.md](../09-Operations/Logging.md), [SIEM.md](../09-Operations/SIEM.md)).
* **NTP synchronisation** across every OT Windows host — event correlation, certificate validity ([Certificates.md](../05-Identity/Certificates.md)), and forensic timelines all depend on it ([Logging.md](../09-Operations/Logging.md) § Time Synchronization).

---

# Baseline and Benchmark Reference

Use the **CIS Benchmarks for Windows Server / Windows** as the general-purpose starting baseline, then apply the OT-specific adaptations in this document on top — CIS provides the comprehensive general hardening checklist; this document provides the OT-specific reasoning for where and how to deviate from a pure IT baseline (availability-first change cadence, DCOM containment rather than blanket removal, allowlisting prioritised for single-function hosts, vendor-certification gating on patch/EDR deployment). Capture the applied baseline in [Configuration-Management.md](../09-Operations/Configuration-Management.md) as the host's configuration of record, and validate it — through FAT/SAT for new systems ([FAT.md](FAT.md), [SAT.md](SAT.md)) and through periodic drift checks thereafter.

---

# Relationships

* **[OT-Protocols.md](../04-Network/OT-Protocols.md)** — this document's DCOM/RPC hardening is the prerequisite for the OPC DA containment guidance there.
* **Every `08-Technologies` platform document** that references a Windows-hosted component (SCADA, historian, OPC aggregation, engineering workstations) points here for the underlying OS baseline.
* **[Patch-Management.md](Patch-Management.md)** — the update-deployment process this document's patching section operates within.
* **[Identity-Management.md](../05-Identity/Identity-Management.md)**, **[Privileged-Access-Management.md](../05-Identity/Privileged-Access-Management.md)**, **[MFA.md](../05-Identity/MFA.md)** — the identity layer this document's account/privilege controls implement at the host level.
* **[Configuration-Management.md](Configuration-Management.md)** — the applied hardening baseline becomes the configuration of record.
* **[Monitoring.md](Monitoring.md)**, **[SIEM.md](SIEM.md)**, **[Logging.md](Logging.md)** — consume the telemetry this document's logging section enables.

---

# Common Mistakes

* DCOM/RPC left at defaults (Everyone/Anonymous access, full dynamic port range) because it was the fastest way to get OPC DA connected during commissioning.
* Shared local administrator credentials across every OT Windows host.
* Engineering workstations used for general email/web browsing alongside engineering software — the single most common path malware reaches an OT engineering toolchain.
* PowerShell logging disabled or never configured, leaving a major living-off-the-land technique surface invisible.
* Application allowlisting deployed on general IT endpoints but skipped on single-function OT servers where it would be highest-value and lowest-friction to maintain.
* Windows updates deployed without checking vendor certification, causing an avoidable operational incident that then discourages future timely patching.
* EDR/AV deployed with broad, undocumented exclusions that quietly negate its value.
* RDP reachable directly from general networks instead of only through a jump host/PAM-brokered path.

---

# Decision Support

1. Do OT Windows hosts authenticate against a separate OT domain or a tightly scoped resource forest, never the enterprise domain directly?
2. Are DCOM launch/access permissions explicitly scoped per application, with no Everyone/Anonymous access remaining?
3. Is application allowlisting deployed on every SCADA/historian/OPC-aggregation server and engineering workstation?
4. Is PowerShell logging enabled and forwarded to the SIEM, with Constrained Language Mode applied where full scripting isn't required?
5. Is RDP restricted to jump-host/PAM-brokered access only, with SMBv1 disabled and SMB signing enabled?
6. Are Windows updates checked against vendor certification before deployment to hosts running OT vendor applications?
7. Is EDR/AV deployment vendor-verified for compatibility, with every exclusion documented?
8. Is the applied hardening baseline captured in Configuration Management and validated at FAT/SAT?

---

# AI Guidance

* Treat this document as the Windows OS baseline every `08-Technologies` platform document assumes when it says "harden the host" — apply it explicitly rather than leaving Windows hardening as an unstated assumption.
* Emphasise the DCOM/RPC section specifically whenever OPC DA, DCOM, or legacy OPC connectivity is discussed elsewhere in the corpus — this document is the prerequisite that makes the OPC DA containment rule in OT-Protocols.md/ISA95.md actually enforceable.
* Adapt general Windows hardening guidance to OT constraints explicitly: narrower patch windows, vendor-certification gating, availability-first change cadence — do not recommend a pure IT-style hardening cadence without this adaptation.
* Recommend application allowlisting as a priority control for single-function OT Windows hosts specifically, given their small and stable legitimate-executable set.
* Recommend CIS Benchmarks as the general starting baseline, with this document's OT-specific deviations layered on top, not as a replacement for CIS.
* Delegate identity/PAM/MFA mechanics to the 05-Identity layer, patch process mechanics to Patch-Management.md, and platform-specific hardening detail to the relevant 08-Technologies document; verify OS-version-specific settings against current Microsoft and CIS guidance rather than asserting them from memory.

---

# Sources / Grounding

* **CIS Benchmarks for Microsoft Windows Server / Windows** — the general-purpose hardening baseline this document adapts for OT.
* **Microsoft Security Baselines** — vendor-published hardening guidance for supported Windows versions.
* **NIST SP 800-82 Rev. 3** — OT security architecture context, including Windows-hosted OT system guidance.
* **ISA/IEC 62443** — endpoint hardening as a Defense in Depth layer. See [IEC62443.md](../02-Standards/IEC62443.md), [Defense-in-Depth.md](../03-Architecture/Defense-in-Depth.md).

> Foundational references; defer to current CIS Benchmark and Microsoft Security Baseline releases, and to OT vendor certification guidance, for authoritative, version-specific detail.

---

# Related Documents

* [OT-Protocols.md](../04-Network/OT-Protocols.md)
* [OPC-UA.md](../04-Network/OPC-UA.md)
* [Secure-Remote-Access.md](../04-Network/Secure-Remote-Access.md)
* [Identity-Management.md](../05-Identity/Identity-Management.md)
* [Privileged-Access-Management.md](../05-Identity/Privileged-Access-Management.md)
* [MFA.md](../05-Identity/MFA.md)
* [Certificates.md](../05-Identity/Certificates.md)
* [Patch-Management.md](Patch-Management.md)
* [Configuration-Management.md](Configuration-Management.md)
* [Change-Management.md](Change-Management.md)
* [Monitoring.md](Monitoring.md)
* [Logging.md](Logging.md)
* [SIEM.md](SIEM.md)
* [FAT.md](FAT.md)
* [SAT.md](SAT.md)
* [Defense-in-Depth.md](../03-Architecture/Defense-in-Depth.md)
* [IEC62443.md](../02-Standards/IEC62443.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-07 | Initial release — new 09-Operations document resolving the widely-referenced dangling `Windows-Hardening.md` reference from OT-Protocols.md and multiple 08-Technologies platform documents; defines OT domain/identity architecture, service and RPC/DCOM minimisation (the explicit prerequisite for this handbook's OPC DA containment rule), privilege/credential controls, RDP/SMB hardening, PowerShell logging and Constrained Language Mode, application allowlisting, vendor-certification-gated patching, EDR/AV and media controls, and CIS Benchmark baseline reference adapted to OT constraints |
