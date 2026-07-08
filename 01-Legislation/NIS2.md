---
id: nis2
title: "NIS2 Directive (EU 2022/2555)"
category: Legislation
layer: "01-Legislation"
version: 1.2.2
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Engineering-oriented overview of the NIS2 Directive (EU 2022/2555): scope and entity categories,
  Article 20/21/23 obligations with OT-specific implementation, supervision, penalties and sector
  implications.
keywords: [NIS2, směrnice 2022/2555, essential entities, important entities, incident reporting, hlášení incidentů, Article 21, supply chain]
---

# Purpose

This document provides an engineering-oriented overview of the NIS2 Directive (Directive EU 2022/2555) from the perspective of an OT Security Architect.

Rather than reproducing the legal text, it explains how NIS2 influences the design, governance and operation of industrial systems — particularly those involving Operational Technology (OT), Industrial Control Systems (ICS), PLC, SCADA and DCS infrastructure.

**This document is the top of the regulatory hierarchy.** All national implementations and engineering standards derive from it. Read this document first; then read the national transposition ([Czech-Cybersecurity-Act.md](Czech-Cybersecurity-Act.md)) and the engineering implementation framework ([IEC62443.md](../02-Standards/IEC62443.md)).

This document covers:

* What NIS2 is and why it was introduced.
* Who is covered — entity categories, sectors, size thresholds.
* Key obligations — Article 20 (governance), Article 21 (risk management), Article 23 (incident reporting), Article 24 (supply chain), and related articles.
* How each NIS2 obligation translates into OT architectural decisions.
* Relationship with IEC 62443, ISA-95, and national legislation.
* Enforcement, penalties and supervisory mechanisms.
* Sector-specific OT implications.
* Checklists for practical compliance assessment.

---

# Related Documents

| Document | Relationship |
|----------|-------------|
| [Czech-Cybersecurity-Act.md](Czech-Cybersecurity-Act.md) | Czech national transposition of NIS2 — read for CZ-specific obligations |
| [IEC62443.md](../02-Standards/IEC62443.md) | Engineering implementation framework for OT security |
| [ISA95.md](../02-Standards/ISA95.md) | Manufacturing functional reference model — defines what must be secured |

---

# Regulatory Hierarchy

NIS2 sits at the top of a four-layer structure. All lower documents must be read in context of this hierarchy.

```
[NIS2 Directive (EU 2022/2555)]          ← Strategic objectives, EU-wide
        │                                   Defines: what must be achieved
        │
[National Transposition Law]             ← National legal obligations
│  Czech Republic: Act No. 264/2025      Defines: who is obligated, enforcement
│  Germany: NIS2UmsuCG (BSIG)
│  Other Member States: national acts
        │
[National Implementing Decrees]          ← Detailed security measures and scope
│  Czech: Decrees 408 / 409 / 410        Defines: extent of obligations
        │
[IEC 62443 / ISA-95]                    ← Engineering implementation
                                           Defines: how to build and operate
```

NIS2 defines **what** must be achieved. National legislation defines **who** is obligated and **what** the consequences of non-compliance are. Engineering standards define **how** to implement.

> **Critical point for multi-country organizations:** NIS2 is an EU directive, not directly applicable law. Each Member State transposes it into national law with varying interpretations and timelines. An organization operating in multiple EU countries must assess obligations under each national transposition — not only under NIS2 itself. The Czech transposition is Act No. 264/2025 Coll.

---

# What Is NIS2?

NIS2 (Network and Information Security Directive 2, formally Directive EU 2022/2555) was:

* **Proposed** by the European Commission on 16 December 2020.
* **Adopted** by the European Parliament and Council on 14 December 2022.
* **Entered into force** on 16 January 2023.
* **Transposition deadline** for Member States: 17 October 2024.

NIS2 replaces the original NIS Directive (EU 2016/1148, in force since 2016) and addresses its key shortcomings: inconsistent implementation across Member States, insufficient scope, weak enforcement, and inadequate supply chain security requirements.

## NIS1 vs NIS2 — Key Changes for OT Organizations

| Dimension | NIS1 (2016) | NIS2 (2022) | Impact on OT |
|-----------|-------------|-------------|-------------|
| Scope | Operators of Essential Services + DSPs | Much broader — 18 sectors, size-based criteria | More OT organizations are now in scope |
| Sector coverage | 7 sectors | 18 sectors including manufacturing | Manufacturing now explicitly in scope |
| Management accountability | Limited | Management liability, mandatory training | Board-level engagement required |
| Incident reporting | 72h only | 24h early warning + 72h full + 1 month final | Faster OT detection and reporting required |
| Supply chain | Not addressed | Explicit requirement | Vendor management formalized |
| Enforcement | Member State discretion | Harmonized minimum fines | Consistent EU-wide enforcement baseline |
| Supervisory approach | Reactive for OES | Proactive for essential, reactive for important | Regular inspections for higher-obligation OT operators |

## Primary Objectives

* Improve cybersecurity resilience across the EU — particularly in critical infrastructure.
* Increase management accountability — cybersecurity is a board-level responsibility.
* Harmonize requirements across Member States — reduce regulatory fragmentation.
* Strengthen incident reporting obligations — earlier warning, more information.
* Improve supply chain security — address vendor and integrator risk.
* Promote continuous risk management — ongoing, not point-in-time compliance.
* Establish supervisory and enforcement mechanisms — real consequences for non-compliance.

---

# Scope — Who Is Covered

## Entity Size Thresholds

NIS2 applies to entities that:
* Operate in a covered sector (Annex I or II), **AND**
* Meet size criteria or are identified as critical regardless of size.

| Size Category | Employees | Annual Turnover OR Annual Balance Sheet | NIS2 Status |
|--------------|-----------|----------------------------------------|-------------|
| Large enterprise | ≥ 250 | > €50M turnover OR > €43M balance | In scope (Annex I or II) |
| Medium enterprise | 50–249 | > €10M turnover AND ≤ €50M turnover | In scope (Annex I or II) |
| Small enterprise | < 50 | ≤ €10M | Generally out of scope |
| Micro enterprise | < 10 | ≤ €2M | Generally out of scope |

**Exceptions — in scope regardless of size:**
* Providers of electronic communications networks.
* Trust service providers.
* DNS service providers, TLD name registries.
* Entities designated as critical by Member State regardless of size.
* Sole providers of a service essential to maintain critical societal or economic activities.

> **OT architect note:** A small water utility serving a mid-sized city may be in scope regardless of employee count if it is the sole provider of an essential service. Size thresholds are a starting point, not the final answer. National legislation (Czech Decree 408) specifies exact criteria.

## Entity Categories

| NIS2 Category | Definition | Czech Equivalent | Supervisory Model |
|--------------|-----------|-----------------|------------------|
| **Essential entities** | Large enterprises in Annex I sectors; entities designated by Member State | Higher Obligations (Decree 409) | Proactive, ex-ante supervision — regular inspections regardless of incident |
| **Important entities** | Medium enterprises in Annex I or II sectors; large enterprises in Annex II sectors | Lower Obligations (Decree 410) | Reactive, ex-post supervision — triggered by incident or complaint |

## Covered Sectors

### Annex I — Highly Critical Sectors (Essential Entities)

| Sector | Subsectors | Typical OT Presence |
|--------|-----------|-------------------|
| **Energy** | Electricity, oil, gas, hydrogen | SCADA, EMS, DMS, RTU, IEC 61850 substations |
| **Transport** | Air, rail, water, road | ATC, railway signaling, port automation, traffic management |
| **Banking** | Credit institutions | Limited OT (facility management, ATM networks) |
| **Financial market infrastructure** | Trading venues, CCPs | Limited OT |
| **Healthcare** | Hospitals, EU reference labs, pharma R&D | Building automation, laboratory systems, medical devices |
| **Drinking water** | Providers | SCADA, PLC (treatment, pumping, distribution) |
| **Wastewater** | Collectors and treaters | SCADA, PLC (pumping stations, treatment) |
| **Digital infrastructure** | IXPs, DNS, TLD, cloud, data centers, CDN, TSP, NW providers | Data center BMS, SCADA (cooling, power) |
| **ICT service management (B2B)** | Managed service providers | IT/OT managed services |
| **Public administration** | Central and regional government | Facility automation, critical government systems |
| **Space** | Ground infrastructure operators | Ground segment control systems |

### Annex II — Other Critical Sectors (Important Entities)

| Sector | Subsectors | Typical OT Presence |
|--------|-----------|-------------------|
| **Postal and courier** | Universal and courier services | Sorting automation, facility management |
| **Waste management** | Waste collectors and processors | SCADA, PLC (incineration, sorting) |
| **Chemicals** | Manufacture and distribution of hazardous chemicals | DCS, SIS (Safety Instrumented Systems), SCADA |
| **Food** | Production, processing and distribution at scale | SCADA, PLC (food processing lines) |
| **Manufacturing** | Medical devices, electronics, machinery, motor vehicles, other transport equipment | ICS, robotics, PLC production lines, MES |
| **Digital providers** | Online marketplaces, search engines, social networks | Limited OT |
| **Research** | Research organizations | Laboratory equipment, computational infrastructure |

---

# Key Obligations — Article-by-Article for OT Architects

## Article 20 — Governance and Management Accountability

### What the Article Requires

Management bodies of essential and important entities must:

* **Approve** cybersecurity risk management measures.
* **Oversee** implementation of those measures.
* **Be held personally liable** for infringements by the entity.
* **Undergo cybersecurity training** to assess risks and management practices.
* **Offer equivalent training** to employees on a regular basis.

### Why This Is a Structural Change

Under NIS1, cybersecurity was primarily an IT/OT department responsibility. Management could approve a policy and consider the matter handled. NIS2 eliminates that distance: management is personally liable, must understand what has been approved, and must be able to demonstrate active oversight.

### OT-Specific Implications

**Who is "management" in an OT context?**

In industrial organizations, the relevant management bodies typically include:

* Board of directors or supervisory board.
* Chief Executive Officer and/or Chief Operations Officer.
* Plant Manager / Site Manager — directly responsible for OT operations.
* Chief Information Security Officer — if appointed.

The CISO or security architect cannot be the sole accountable party. The Plant Manager who approves operational compromises (e.g., delaying a security patch to avoid production downtime) shares accountability for the resulting risk.

**Practical implementation:**

* Cybersecurity must appear on board and management meeting agendas — not only in security committee reports.
* Management must formally approve the risk management framework, security architecture decisions and risk acceptance records.
* Management training must include OT-specific scenarios (what is a PLC? what happens when SCADA goes down? what is a ransomware attack on an OT network?).
* Evidence of management engagement must be available for inspection: meeting minutes with cybersecurity agenda items, signed risk acceptance records, training attendance records.

**Common failure pattern:** The security team builds a comprehensive OT security program. Management signs a policy document once a year. During an NÚKIB inspection, management cannot answer basic questions about the security architecture or explain any specific risk acceptance decision. This is non-compliance with Article 20 regardless of the quality of technical controls.

---

## Article 21 — Risk Management Measures

### What the Article Requires

Organizations must implement appropriate and proportionate technical, operational and organizational measures to manage cybersecurity risks, based on an all-hazards approach.

NIS2 specifies minimum measures (Article 21(2)):

| Minimum Measure | Article 21(2) Reference | OT Architectural Implication |
|----------------|------------------------|------------------------------|
| Risk analysis and information system security policies | (a) | Security policy framework covering OT; annual risk review |
| Incident handling | (b) | OT-specific Incident Response Plan |
| Business continuity — backup, disaster recovery, crisis management | (c) | PLC/DCS/SCADA backups; degraded mode procedures; RTO/RPO |
| Supply chain security — supplier relationships, product security | (d) | Vendor access management; IEC 62443-2-4 for integrators |
| Security in acquisition, development and maintenance — vulnerability handling | (e) | Secure procurement; patch management (IEC 62443-2-3) |
| Effectiveness assessment policies | (f) | Security metrics; audit; periodic SLRA review |
| Cyber hygiene practices and training | (g) | OT-specific training for operators, engineers, management |
| Cryptography and encryption policies | (h) | TLS, OPC UA security profiles; legacy device compensating controls |
| Human resources security, access control, asset management | (i) | IAM, PAM, OT asset inventory |
| MFA or continuous authentication | (j) | MFA for remote access; PAM for engineering workstations |

### Proportionality — The Core Principle

NIS2 explicitly requires that measures be **proportionate to the risk**. This is not a license to do less — it is a requirement to calibrate measures to actual risk rather than apply a one-size-fits-all standard.

For OT architects, proportionality means:

* A large electricity transmission operator faces higher threat sophistication and greater consequence of failure than a small water utility. Different Security Level Targets (IEC 62443) are appropriate.
* A legacy PLC from 2005 cannot support the same controls as a modern S7-1500. Compensating controls and documented residual risk acceptance are the proportionate response — not pretending the legacy device meets modern standards.
* A site operating under Lower Obligations (Decree 410) should not implement the same depth of controls as a Higher Obligations site — doing so consumes resources without proportionate risk reduction.

### OT-Specific Implementation of Each Minimum Measure

#### (a) Risk Analysis and Security Policies

**Risk analysis for OT:**
* Must cover OT assets, not only IT.
* Must use a methodology appropriate for industrial environments — IEC 62443-3-2 (SLRA) is the reference methodology.
* Must consider process safety consequences — a compromised SIS is categorically different from a compromised IT server.
* Must be updated when the environment changes — new assets, new integrations, new threat intelligence.
* Must produce documented outputs: risk register, Security Level Targets per zone, risk treatment plan, residual risk acceptance records.

**Security policies specific to OT:**

Existing IT security policies are insufficient. OT requires dedicated policies covering:

| Policy | OT-Specific Content Required |
|--------|------------------------------|
| Remote access policy | Who can access OT remotely; MFA requirement; jump server requirement; session recording; time-limited credentials; vendor-specific requirements |
| Change management policy | How PLC programs and DCS configurations are changed; testing in staging; rollback plan; approval workflow; post-change verification |
| Removable media policy | Prohibition on personal USB devices in OT; scanning station in DMZ; approved media only; chain of custody |
| Vendor access policy | Pre-access vetting; time-limited credentials; MFA; jump server; session recording; post-access credential revocation |
| Patch management policy | OT patch assessment process (IEC 62443-2-3); testing before deployment; maintenance windows; compensating controls for unpatchable systems |
| Asset management policy | Coverage of all OT assets; update frequency; ownership assignment; lifecycle tracking |
| Wireless access policy | Prohibition on personal wireless devices in OT; approved wireless zones with WPA3-Enterprise; monitoring |
| Engineering workstation policy | Security baseline; removable media control; prohibition on personal devices; version control for engineering projects |

#### (b) Incident Handling

NIS2 requires organizations to have systematic incident handling capability. For OT, this means an ICS-specific Incident Response Plan — not an adaptation of an IT IR plan.

Key OT-specific requirements:

* **Safety first:** Every OT IR process must begin with a safety assessment — before isolating or shutting down systems, determine whether doing so creates a safety hazard.
* **Production impact assessment:** Containment decisions in OT require coordination with operations — an operator cannot simply disconnect a PLC mid-cycle.
* **Specialized forensics:** OT forensic evidence collection must not disrupt running processes; OT-specific tools and expertise are required.
* **NÚKIB notification:** Early warning within 24 hours, full report within 72 hours, final report within 1 month (see Article 23 below).
* **Sector-specific notification:** Some sectors (energy, water) have additional reporting requirements to sector regulators beyond NÚKIB.

See [Czech-Cybersecurity-Act.md](Czech-Cybersecurity-Act.md) for NÚKIB notification procedures.

#### (c) Business Continuity — Backup, Disaster Recovery, Crisis Management

**Backup requirements for OT:**

| Asset Category | What to Back Up | Frequency | Storage |
|---------------|----------------|-----------|---------|
| PLC programs | Ladder/FBD/SCL program, CPU configuration, IP settings | After every change + scheduled | Offline/air-gapped copy mandatory |
| DCS configuration | Controller config, graphics, alarm lists, historian tags, recipes | After every change + scheduled | Offline/air-gapped copy mandatory |
| SCADA project | Tag database, graphics, scripts, runtime configuration | Daily (DB) + after changes (project) | Version-controlled |
| Engineering workstations | Full disk image including engineering software and licenses | Monthly + after changes | Offline copy |
| Industrial firewalls | Running config + startup config | After every change | Version-controlled |
| Industrial switches | Configuration | After every change | Version-controlled |
| Safety systems (SIS) | Program, configuration, proof test records | After every change | Separately from BPCS; physically secured |

**Recovery capability:**
* Backup without verified restoration is not a recovery capability.
* RTO (Recovery Time Objective) and RPO (Recovery Point Objective) must be defined for each critical control system.
* Recovery procedures must be documented, tested annually, and results recorded.
* Degraded mode operation procedures must exist for the period when SCADA/DCS is unavailable.

#### (d) Supply Chain Security

NIS2 Article 21(2)(d) requires organizations to address security in supplier relationships and product supply chains.

**Three dimensions of OT supply chain security:**

1. **Product security (what you buy):** PLC firmware, SCADA software, DCS, engineering tools — all carry inherent security characteristics. Require evidence of IEC 62443-4-1 compliance (secure development lifecycle) from product suppliers. Request SBOM (Software Bill of Materials) to enable rapid impact assessment when new CVEs are published.

2. **Integration security (how it is deployed):** System integrators introduce security risk through their access, tools and practices. Require IEC 62443-2-4 compliance evidence from integrators. Define contractual security obligations (incident notification, access governance, subcontracting restrictions, right to audit).

3. **Operational security (ongoing access):** Vendors and integrators retain remote access for maintenance and support. Govern this through: time-limited credentials, MFA, jump server via Industrial DMZ, full session recording, access log review.

#### (e) Security in Acquisition, Development and Maintenance

**Procurement security for OT:**

* Security requirements must be included in RFP/tender documents.
* Vendors must demonstrate product security capabilities before selection.
* Vulnerability disclosure processes (PSIRT) must be evaluated.
* Long-term security support commitments must be contractually established.
* End-of-life and end-of-security-support dates must be documented at procurement.

**Vulnerability management (IEC 62443-2-3):**

OT vulnerability management differs fundamentally from IT:

| Dimension | IT Approach | OT Approach |
|-----------|------------|-------------|
| Patch frequency | Monthly (Patch Tuesday) | Per maintenance window (monthly to annually) |
| Testing before deployment | Test environment | Mandatory OT staging/simulation environment |
| Production impact | Short restart acceptable | Coordination with production schedule required |
| Approval | IT change management | OT change management + operations approval |
| Unpatchable systems | Rare | Common — end-of-life PLCs and SCADA OS |

Compensating controls for unpatchable systems must be documented, technically implemented, and formally accepted by management.

#### (f) Effectiveness Assessment

NIS2 requires policies to assess the effectiveness of cybersecurity risk management measures. This means security cannot be assumed to be working — it must be verified.

**OT effectiveness assessment methods:**

| Method | OT Application | Frequency |
|--------|---------------|-----------|
| Security metrics review | Patch coverage, open vulnerabilities, access review completion, incident response drill results | Quarterly |
| Architecture review | Zone/conduit diagram vs. actual configuration; firewall rule review | Annually |
| Risk assessment review | SLRA update; new threats; changed environment | Annually or on change |
| Penetration testing | Carefully scoped OT pen test (passive preferred; active requires expert judgment) | Annually or biannually |
| Tabletop exercises | OT IR scenarios for management and operations | Annually |
| Third-party audit | Independent security assessment | For Higher Obligations environments |

#### (g) Cyber Hygiene and Training

**OT-specific training requirements:**

Different roles require different training content:

| Role | Training Content | Frequency |
|------|-----------------|-----------|
| Management (board, CEO, plant manager) | Cybersecurity governance, risk management, regulatory obligations, incident impact scenarios | Annual; OT-specific |
| SCADA/DCS operators | Recognizing suspicious behavior on HMI, alarm anomalies, social engineering, incident reporting | Annual |
| OT/automation engineers | Secure PLC programming practices, engineering WS security, USB/removable media policy, vendor access procedures | Annual + on process change |
| IT staff supporting OT | OT/IT differences, OT constraints, industrial protocols, risk of IT tools in OT | Before first OT engagement |
| Vendors/integrators | OT security policy, remote access procedures, acceptable behavior in OT environment | Before each site access |

**Cyber hygiene baselines for OT:**
* Default credential changes on all devices (documented and enforced).
* Disable unused network services on PLC, SCADA servers, industrial switches.
* Application whitelisting on SCADA servers and engineering workstations where technically feasible.
* NTP synchronization for all OT devices.
* Regular review of active user accounts and vendor access.

#### (h) Cryptography and Encryption

**Cryptography in OT contexts:**

Modern OT systems increasingly support cryptography, but a significant legacy installed base does not. The NIS2 requirement to have cryptography policies applies to both cases.

**Where cryptography should be applied in OT:**

| Use Case | Recommended Standard | Notes |
|----------|---------------------|-------|
| Remote access VPN | IPsec with IKEv2 or TLS 1.2/1.3 | MFA required alongside |
| Jump server sessions | TLS 1.3 | Session recording in addition |
| OPC UA inter-zone | Sign and Encrypt security profile | Mandatory for conduit-crossing connections |
| Engineering workstation to PLC | TLS where supported (S7-1500, M580, newer ControlLogix) | Legacy PLCs: compensating controls |
| Historian replication (OT→DMZ) | TLS 1.2+ | PI-to-PI replication with certificate auth |
| SCADA server web interfaces | TLS 1.2+, disable legacy cipher suites | Disable TLS 1.0/1.1, SSLv3 |
| Certificates | X.509, minimum RSA 2048 or ECDSA P-256 | Manage expiry proactively |

**Legacy OT and cryptography:**
Legacy devices (pre-2015 PLCs, older RTUs) typically have no cryptographic capability. This is not acceptable as a permanent state but may be managed through:
* Network isolation as compensating control (the device is not reachable from outside its zone).
* Documented residual risk and formal management acceptance.
* Inclusion in a modernization roadmap with defined timeline.

#### (i) Human Resources Security, Access Control, Asset Management

**Asset management for NIS2 compliance:**

An OT asset inventory is the prerequisite for almost every other NIS2 obligation:
* You cannot manage risk for assets you have not identified.
* You cannot assess incident scope without knowing what is in the environment.
* You cannot prioritize patching without knowing what software/firmware versions are deployed.
* You cannot define zones and conduits without knowing what systems exist and where they communicate.

Asset inventory must be structured by ISA-95 level (see [ISA95.md](../02-Standards/ISA95.md)), maintained continuously, and include metadata required for security decisions: vendor, model, firmware/software version, IP address, communication protocols, zone assignment, owner, criticality, lifecycle status.

**Access control:**

* **Unique accounts:** No shared credentials on SCADA, DCS, HMI — ever.
* **RBAC:** Role-based access control aligned to operational roles (operator, supervisor, engineer, administrator).
* **Least privilege:** Access limited to what is operationally necessary for each role.
* **Lifecycle management:** Account provisioning and deprovisioning tied to personnel changes; reviewed at minimum annually.
* **Privileged accounts:** PAM (Privileged Access Management) for engineering workstations, PLC engineering software, and administrative access to OT systems.
* **Vendor accounts:** Time-limited; MFA required; deprovisioned immediately after work completion.

**Human resources security:**
* Background checks for personnel with access to critical OT systems (commensurate with criticality).
* Security obligations included in employment contracts.
* Offboarding procedures including immediate access revocation.
* Insider threat awareness program.

#### (j) Multi-Factor Authentication (MFA)

NIS2 explicitly requires MFA or continuous authentication for network access and access to sensitive information.

**MFA application in OT:**

| Access Type | MFA Requirement | Implementation Notes |
|-------------|-----------------|---------------------|
| Remote access to OT network | Mandatory | Hardware token or authenticator app; SMS-based MFA should be avoided |
| Jump server / bastion host | Mandatory | All sessions through jump server require MFA |
| Engineering software access | Required for privileged operations | PAM integration where feasible |
| SCADA operator access (local, on-site) | Risk-based | Separate from remote access; physical access control compensating |
| Vendor remote access | Mandatory | Without exception |
| Emergency access (break-glass) | Monitored exception | Emergency accounts require enhanced logging and review |

---

## Article 23 — Incident Reporting

### Reporting Timeline and Content

NIS2 defines a three-stage reporting process:

| Stage | Deadline | Required Content |
|-------|----------|-----------------|
| **Early warning** | Within **24 hours** of becoming aware | Is it a significant incident? Was it caused by unlawful/malicious act? Does it have cross-border impact? |
| **Incident notification** | Within **72 hours** of becoming aware | Updated early warning; initial assessment (severity, impact, indicators); if known: cause |
| **Intermediate report** | On request from competent authority | Current status; containment measures taken |
| **Final report** | Within **1 month** of incident notification | Full description; type of threat; root cause; applied mitigations; cross-border impact if applicable |

### What Is a "Significant Incident"?

An incident is significant if it causes or is capable of causing:

* **Severe operational disruption** of the service provided.
* **Financial losses** for the entity.
* **Impact on other persons** through significant material or non-material damage.

**OT-specific significant incident examples:**

| Scenario | Significant? | Rationale |
|----------|-------------|-----------|
| Ransomware encrypting SCADA server — production halted | Yes | Severe operational disruption |
| Unauthorized modification of PLC program detected (process not yet affected) | Yes | Capable of causing disruption — the potential was realized |
| Anomalous Modbus write commands detected and blocked by IDS | Borderline | Report to NÚKIB for guidance if scope is unclear |
| Engineering workstation compromised with malware but isolated before OT network contact | Yes | High risk of escalation; malware on OT-adjacent system |
| Power outage affecting OT (non-cyber) | No | Not a cybersecurity incident unless caused by attack |
| DDoS against OT network management interface causing temporary disruption | Yes | Cyber-caused operational disruption |
| Supply chain software update contains malicious code (even if not yet activated) | Yes | Incident occurred at supply chain level |

### Architectural Prerequisites for Timely Reporting

The 24-hour early warning deadline is extremely tight for OT environments where detection capability is often limited. These architectural elements are prerequisites — not nice-to-haves:

* **OT monitoring / IDS:** Detection must be automated — waiting for an operator to notice something unusual on the HMI is too slow.
* **NTP synchronization:** Without synchronized time across all OT devices, log correlation for incident scope assessment is unreliable. This is a hard prerequisite for the 24-hour deadline.
* **Centralized logging:** Logs from SCADA, DCS, firewalls, engineering workstations, and OT monitoring must be accessible from a single point.
* **Out-of-band communication path to NÚKIB:** If the OT network itself is compromised, the communication path for incident reporting must exist outside it.
* **RACI matrix for incident response:** Who makes the decision to declare a significant incident? Who contacts NÚKIB? Who escalates to management? These must be defined and rehearsed before an incident occurs.
* **24/7 on-call coverage:** The 24-hour deadline does not pause for weekends or holidays.

### Sector-Specific Reporting Obligations

Beyond NIS2/NÚKIB reporting, OT operators in regulated sectors may have additional reporting requirements:

| Sector | Additional Reporting | Applicable Regulation |
|--------|---------------------|----------------------|
| Electricity (transmission, distribution) | ERÚ (Energy Regulatory Office) | Energy Act + sector regulation |
| Nuclear | SÚJB (State Office for Nuclear Safety) | Atomic Act |
| Aviation | CAA (Civil Aviation Authority) | EASA regulations |
| Rail | DRASC (Rail Safety Directorate) | Rail transport regulation |
| Water / Wastewater | Regional hygiene authority | Water Act |

Multi-level reporting obligations must be coordinated — the incident report to NÚKIB and the report to the sector regulator must be consistent.

---

## Article 21(2)(d) and Article 22 — Supply Chain Security

### What NIS2 Requires

Article 21(2)(d) requires organizations to address security of the supply chain including:
* Security aspects of relationships between the entity and its direct suppliers or service providers.
* The overall quality and security practices of suppliers' products.

Article 22 gives the European Commission authority to require specific supply chain security measures for certain product/service categories.

### OT Supply Chain Risk Landscape

| Supply Chain Component | Risk Scenario | Mitigation |
|-----------------------|--------------|-----------|
| PLC firmware | Malicious code in firmware update (analogous to SolarWinds for OT) | Verify firmware hash/signature; only official vendor sources; test before deployment |
| SCADA software | Backdoor in engineering tool or runtime | IEC 62443-4-1 compliance; SBOM; vendor PSIRT monitoring |
| Remote maintenance access | Vendor credential compromise used to access OT | Time-limited credentials; MFA; jump server; session recording |
| System integration | Malware introduced via integrator laptop or USB | Integrator IEC 62443-2-4 compliance; scanning station; approved devices only |
| Industrial network equipment | Compromised firmware in switches/firewalls | Trusted vendors; signature verification; change management |
| Cloud-connected OT services | Cloud platform compromise affecting OT telemetry or control | Evaluate cloud connectivity necessity; data diode for telemetry-only flows |

### SBOM (Software Bill of Materials) in OT Context

The SBOM concept — a machine-readable inventory of all software components in a product — is increasingly relevant for OT following high-profile supply chain incidents (Log4Shell 2021, 3CX supply chain attack 2023).

**OT SBOM use cases:**
* When a new CVE affecting a specific component (e.g., OpenSSL) is published, query SBOMs to identify which OT products are affected.
* During procurement: require vendors to provide SBOM to enable proactive vulnerability management.
* During incident response: rapidly determine which systems are potentially affected by a supply chain compromise.

Request SBOMs from PLC firmware suppliers, SCADA platform vendors, and industrial network equipment manufacturers. The availability of SBOM is a positive indicator of supply chain security maturity.

---

## Article 32 and 33 — Supervisory Measures

### Supervision of Essential Entities (Article 32)

Essential entities (Higher Obligations) are subject to proactive, ex-ante supervision:

* **On-site and off-site inspections** — scheduled and unannounced.
* **Targeted security audits** conducted by independent bodies.
* **Security scans** conducted or commissioned by the supervisory authority.
* **Requests for information** including documented evidence of security measures.
* **Access to data and documents** necessary to assess cybersecurity measures.

**What NÚKIB inspectors examine in OT environments:**
* Does the organization's documentation reflect its actual security posture? (Diagrammy vs. reality)
* Is the risk assessment current and based on actual threats?
* Are security policies specific enough to be operational (not just platitudes)?
* Can staff demonstrate that processes are followed in practice?
* Are vendor access records complete and are revocations timely?
* Are backup and recovery procedures documented and tested?
* Is incident response capability exercised?

### Supervision of Important Entities (Article 33)

Important entities (Lower Obligations) are subject to reactive, ex-post supervision:

* Supervision triggered by incident reports, complaints, or evidence of non-compliance.
* On-request provision of information to demonstrate compliance.
* On-site inspections following a trigger event.

---

## Article 34 — Enforcement and Penalties

### Administrative Fines

| Entity Category | Maximum Fine |
|----------------|-------------|
| **Essential entities** | €10 million OR 2% of total worldwide annual turnover (whichever is higher) |
| **Important entities** | €7 million OR 1.4% of total worldwide annual turnover (whichever is higher) |

**Czech Kč equivalents (approximate, based on current exchange rate):**

| Entity Category | Maximum Fine (CZK) |
|----------------|-------------------|
| Essential (Higher Obligations) | 250 000 000 Kč OR 2% of global turnover |
| Important (Lower Obligations) | 175 000 000 Kč OR 1.4% of global turnover |

### Non-Financial Sanctions

Beyond fines, NIS2 provides for:

* **Temporary ban** on exercising managerial functions for natural persons responsible for serious infringements.
* **Public disclosure** of the entity and the infringement (naming and shaming).
* **Binding instructions** to implement specific security measures with defined deadlines.
* **Temporary suspension** of certification or authorization for essential entities.
* **Court orders** requiring cessation of conduct that poses a cybersecurity risk.

### Business Case for Investment

The penalty structure provides a clear financial argument for cybersecurity investment:

* For a manufacturing company with €100M annual turnover, the maximum fine is €2M — before accounting for production loss, remediation costs, reputational damage, and potential personal liability for management.
* A ransomware attack on an OT network typically costs far more than the fine in production loss alone (Norsk Hydro 2019: estimated €50M+ in losses).
* Proactive investment in a well-architected OT security program — zones, conduits, monitoring, incident response — is economically rational compared to the alternative.

---

# Why NIS2 Matters for OT — Expanded Analysis

## The Convergence Problem

Industrial environments are increasingly interconnected:

* **Remote access** has become standard — vendor VPNs, remote monitoring, cloud connectivity.
* **IT/OT convergence** — ERP, MES, historian integration creates pathways between enterprise IT and OT networks.
* **Industrial IoT (IIoT)** — sensors, drives and controllers increasingly have native Ethernet connectivity.
* **Cloud-connected OT** — remote monitoring, digital twins, cloud SCADA deployments.

Each connection reduces isolation. Isolation was historically OT's primary security mechanism. As isolation erodes, systematic security engineering becomes mandatory.

## Real-World Incident Patterns in OT

The following incident categories have directly affected OT environments and demonstrate the concrete risk NIS2 addresses:

| Incident Pattern | Example | OT Impact |
|----------------|---------|-----------|
| Ransomware propagating IT→OT | Norsk Hydro (2019), Colonial Pipeline (2021) | Production shutdown, manual operations, recovery costs |
| Targeted OT attack via IT | Ukraine power grid (2015, 2016) | Deliberate power outage affecting consumers |
| Safety system attack | Triton/TRISIS (2017) | Attempted defeat of SIS safety protections |
| Supply chain compromise | SolarWinds (2020) | Potential OT pivot via monitoring software |
| Vendor access abuse | Multiple water utility incidents | Unauthorized changes via remote maintenance access |
| Ransomware on OT-adjacent systems | Multiple manufacturing incidents | Loss of SCADA/HMI access, production halt |

NIS2 directly targets the governance, architectural and process failures that enabled each of these incident patterns.

## The Architecture-First Principle

NIS2 is technology-neutral by design. It does not mandate specific products, vendors or configurations. This is intentional — the right security architecture depends on the specific environment, threat landscape and operational constraints.

The architect's task is to translate NIS2 obligations into architectural capabilities:

| NIS2 Obligation | Translates To | Engineering Standard |
|----------------|--------------|---------------------|
| Risk management | Security Level Risk Assessment | IEC 62443-3-2 |
| Asset management | OT asset inventory structured by ISA-95 level | [ISA95.md](../02-Standards/ISA95.md) |
| Network security | Zone and conduit model with Industrial DMZ | IEC 62443-3-3 |
| Access control | IAM + PAM + MFA; Engineering zone | IEC 62443-3-3 FR 1, FR 2 |
| Cryptography | TLS, OPC UA security profiles, PKI | IEC 62443-3-3 FR 3, FR 4 |
| Incident detection | OT IDS, passive monitoring, NTP-synced centralized logging | IEC 62443-3-3 FR 6 |
| Incident reporting | Detection → RACI → NÚKIB notification chain | Czech-Cybersecurity-Act.md |
| Business continuity | Offline backups, tested recovery, RTO/RPO | IEC 62443-3-3 FR 7 |
| Supply chain | Vendor governance, IEC 62443-2-4/-4-1/-4-2 | IEC 62443-2-4 |

---

# NIS2 vs. IEC 62443 — Detailed Comparison

| Dimension | NIS2 | IEC 62443 |
|-----------|------|-----------|
| Nature | EU legal directive | International engineering standard |
| Legal force | Binding via national transposition | Voluntary (but de facto reference for NIS2 compliance) |
| Answers | What must be achieved | How to design and operate secure IACS |
| Scope | Organization-wide governance | IACS-specific engineering |
| Audience | Management bodies, legal teams, architects | Engineers, integrators, product suppliers |
| Risk approach | Requires proportionate risk management | Provides Security Level methodology (IEC 62443-3-2) |
| Asset specificity | Technology-neutral | PLC/DCS/SCADA specific |
| Lifecycle | Does not specify | Full IACS lifecycle from concept to decommissioning |
| Supply chain | Requires addressing supply chain risk | IEC 62443-2-4 (integrators), 4-1/4-2 (products) |
| Verification | Supervisory authority inspections | SL-A verification process |

**The relationship in one sentence:** NIS2 creates the legal obligation; IEC 62443 provides the engineering methodology to satisfy it.

A mature OT security program uses NIS2 as its governance anchor, IEC 62443 as its engineering methodology, and ISA-95 as its functional architecture reference.

---

# NIS2 and ISA-95

ISA-95 is not a security standard, but it is essential for correct NIS2 compliance in OT environments.

ISA-95 provides the functional architecture analysis that enables:

* **Asset inventory** (required by Article 21(2)(i)) — structured by ISA-95 level.
* **Scope determination** — identifying which systems are in scope for regulatory obligations.
* **Risk assessment input** (required by Article 21(2)(a)) — understanding what exists and what flows where is the starting point for risk analysis.
* **Zone/conduit design** — ISA-95 functional boundaries translate into IEC 62443 security zone candidates.

See [ISA95.md](../02-Standards/ISA95.md) for full detail.

---

# Sector-Specific NIS2 Implications for OT

## Energy Sector

**Regulatory context:** The energy sector faces layered regulation — NIS2/national cybersecurity law plus sector-specific energy regulation. In the EU, the CER Directive (Critical Entities Resilience) also applies. Energy operators typically fall under Essential Entities (Higher Obligations).

**OT-specific considerations:**
* Electricity transmission and distribution SCADA (SCADA/EMS/DMS) are unambiguously in scope.
* IEC 61850 substation automation systems (protection relays, IEDs) are OT assets requiring inventory and zone design.
* Smart meter infrastructure (AMI) creates a new attack surface extending from field devices to back-end systems — the AMI head-end system must be isolated from the control network.
* ICCP (Inter-Control Center Communications Protocol) for energy exchange between utilities must terminate in Industrial DMZ.
* DERMS (Distributed Energy Resource Management Systems) for managing distributed generation creates new Level 3/2 integration points.

## Transport Sector

**OT-specific considerations:**
* Railway: ERTMS/ETCS (European Train Control System) represents the highest-stakes OT in the sector. Safety and cybersecurity requirements interact intensely.
* Aviation: ATC systems have extremely high availability requirements; security measures must be validated against operational continuity.
* Road: Tunnel automation, traffic management systems, bridge control — geographically distributed OT with cellular connectivity.
* Water transport: Port operational technology (crane automation, gate control, VTS) with increasing connectivity.

## Water and Wastewater Sector

**OT-specific considerations:**
* Geographically dispersed RTUs at pump stations connected via cellular or licensed radio.
* Online process analyzers (chlorine, pH, turbidity) directly influencing dosing control — integrity of these measurements is safety-critical.
* LIMS integration with SCADA (lab results affecting setpoints) creates a Level 3→Level 2 write path requiring careful conduit design.
* Small utilities may be sole providers and thus in scope regardless of size.

## Manufacturing Sector (Annex II — Important Entities)

**OT-specific considerations:**
* The NIS2 Annex II manufacturing categories are specific: medical devices, computers and electronics, machinery, motor vehicles, other transport equipment.
* Not all manufacturing is in scope — a food producer falls under the "Food" sector category, not "Manufacturing."
* Robotics (KUKA, ABB, Fanuc controllers) at Level 1 have both control and communication capabilities — they require zone placement and security assessment.
* MES integration (bi-directional Level 3↔Level 2) is particularly common in discrete manufacturing and requires careful conduit design.

## Healthcare Sector

**OT-specific considerations:**
* Medical devices with network connectivity (infusion pumps, imaging equipment, patient monitoring) sit at the intersection of OT security and patient safety.
* Building automation (HVAC, access control, power management for hospital facilities) represents OT within a clinical environment.
* Laboratory automation systems (robotic sample handling, analytical instruments) are OT assets.
* The convergence of clinical IT and medical device networks creates complex zone boundary decisions.

---

# Relationship with Other EU Cybersecurity Legislation

NIS2 does not exist in isolation. OT operators must understand how it interacts with related EU legislation.

| Regulation | Relevance to OT | Relationship with NIS2 |
|-----------|----------------|----------------------|
| **CER Directive (EU 2022/2557)** | Critical entity resilience — physical, cyber and organizational resilience for critical infrastructure | Parallel directive; applies to same sectors; physical resilience + cyber resilience together |
| **Cyber Resilience Act (CRA)** | Cybersecurity requirements for products with digital elements — applies to OT product vendors | Affects what OT products (PLCs, HMIs, switches) must comply with at manufacture; supports NIS2 supply chain requirements |
| **GDPR (EU 2016/679)** | Personal data protection | OT historian data may include personal data (operator actions, shift data); incident reporting may involve personal data |
| **Machinery Regulation (EU 2023/1230)** | Safety of machinery | Cybersecurity of machinery linked to safety; OT in manufacturing must consider both |
| **AI Act (EU 2024/1689)** | AI systems regulation | AI-based predictive maintenance or anomaly detection systems in OT may fall under AI Act |
| **Radio Equipment Directive (RED)** | Wireless devices | Industrial wireless devices (WirelessHART, ISA100, industrial WiFi) subject to cybersecurity requirements under RED |

---

# Common Misconceptions

NIS2 does **not** prescribe:

* Specific firewall vendors or products — engineering decisions remain with the organization.
* Mandatory authentication technologies — MFA is required, but the method is the organization's choice.
* Fixed network topologies — zone/conduit design follows risk, not a mandated blueprint.
* Particular SIEM platforms — the requirement is detection and monitoring capability, not a specific tool.
* Mandatory cloud adoption or avoidance — connectivity decisions must be risk-justified.
* ISO 27001 certification as mandatory — NIS2 requires risk management capability, not a specific certification framework.
* IEC 62443 certification as mandatory — but IEC 62443 is the de facto engineering standard for demonstrating compliance.

NIS2 defines governance obligations and minimum security capability categories. The architecture that satisfies them is the organization's engineering responsibility.

---

# Architect Notes

When reading NIS2, map every obligation to an architectural capability — not to a product or tool.

**The fundamental question for every NIS2 requirement:**

```
What is the CAPABILITY required?
        ↓
What ARCHITECTURAL ELEMENT provides it?
        ↓
What OPERATIONAL PROCESS sustains it?
        ↓
What EVIDENCE demonstrates it to an auditor?
```

**Example — Article 21(2)(j) MFA requirement:**

| Question | Answer |
|----------|--------|
| Capability required | Only authenticated users can access OT systems remotely |
| Architectural element | MFA gateway + jump server in Industrial DMZ; PAM for privileged access |
| Operational process | Credential issuance procedure; access review; deprovisioning procedure |
| Evidence | MFA logs; access request records; deprovisioning records; session recordings |

Compliance should emerge from a well-designed architecture — not be retrofitted as a layer of controls onto a poorly designed system.

---

# Checklists / Kontrolní seznamy

---

## Checklist 1: Posouzení aplikovatelnosti NIS2

Použij pro každou organizaci jako první krok před diskusí o bezpečnostních opatřeních.

### Česky

**Krok 1: Sektor**
- [ ] Identifikoval jsem všechny obchodní aktivity organizace
- [ ] Porovnal jsem je s přílohou I NIS2 (vysoce kritické sektory — essential)
- [ ] Porovnal jsem je s přílohou II NIS2 (jiné kritické sektory — important)
- [ ] Zahrnul jsem i nepřímé poskytování regulovaných služeb (subdodavatel kritické infrastruktury)
- [ ] Konzultoval jsem hraniční případy s právním zástupcem nebo NÚKIB

**Krok 2: Velikost organizace**
- [ ] Ověřil jsem počet zaměstnanců (≥ 250 = velká, 50–249 = střední, < 50 = malá)
- [ ] Ověřil jsem roční obrat a bilanční sumu
- [ ] Posoudil jsem, zda se na organizaci vztahuje výjimka pro malé subjekty bez ohledu na velikost (sole provider, kritická role)

**Krok 3: Kategorie entity**
- [ ] Určil jsem, zda je organizace essential (vyšší povinnosti) nebo important (nižší povinnosti)
- [ ] Ověřil jsem toto zařazení vůči české Vyhlášce 408 — národní pravidla mohou být přísnější nebo specifičtější
- [ ] Zdokumentoval jsem výsledek posouzení s odůvodněním a podpůrnými důkazy

**Krok 4: Dokumentace**
- [ ] Posouzení aplikovatelnosti je zdokumentováno a schváleno managementem
- [ ] Je nastaven proces pro přehodnocení při změnách v organizaci nebo regulaci
- [ ] Je zaregistrována organizace u NÚKIB (pokud je regulována)

---

## Checklist 2: Governance a odpovědnost managementu (Článek 20)

Použij pro ověření splnění požadavků na zapojení managementu.

### Česky

**Schvalování a dohled**
- [ ] Management formálně schválil rámec řízení kybernetických rizik (podpis, zápis z jednání)
- [ ] Management formálně schvaluje výsledky risk assessmentu a přijímá zbytková rizika
- [ ] Kybernetická bezpečnost je pravidelnou položkou na programu jednání managementu (ne pouze výroční)
- [ ] Management aktivně dohlíží na implementaci bezpečnostních opatření — existují záznamy o tomto dohledu
- [ ] Jsou dostupné záznamy dokládající zapojení managementu (zápisy, schvalující e-maily, podpisy)

**Vzdělávání managementu**
- [ ] Management (board, CEO, plant manager, CISO) absolvoval školení kybernetické bezpečnosti
- [ ] Školení bylo OT-specifické (nejen obecné IT bezpečnostní školení)
- [ ] Existují záznamy o absolvování školení (datum, obsah, účastníci)
- [ ] Je plánováno pravidelné opakování (min. 1× ročně)
- [ ] Jsou zaměstnanci s přístupem do OT průběžně vzděláváni?

**Osobní odpovědnost**
- [ ] Je management informován o své osobní odpovědnosti za kybernetickou bezpečnost dle zákona?
- [ ] Jsou v pracovních smlouvách managementu explicitně uvedeny povinnosti v oblasti kybernetické bezpečnosti?

---

## Checklist 3: Opatření řízení rizik — OT implementace (Článek 21)

Použij pro ověření implementace minimálních opatření dle Článku 21(2).

### Česky

**21(2)(a) — Analýza rizik a bezpečnostní politiky**
- [ ] Byl proveden Security Level Risk Assessment (SLRA) dle IEC 62443-3-2 zahrnující OT prostředí
- [ ] Je risk assessment aktuální (ne starší než 12 měsíců nebo aktualizovaný při změnách)
- [ ] Existují OT-specifické bezpečnostní politiky (vzdálený přístup, přenosná média, vendor přístup, change management, patch management)
- [ ] Jsou politiky schváleny managementem a dostupné relevantním pracovníkům

**21(2)(b) — Řízení incidentů**
- [ ] Existuje ICS-specifický Incident Response plán (ne adaptace IT IR plánu)
- [ ] Jsou definovány eskalační cesty (safety team, management, NÚKIB, sektorový regulátor)
- [ ] Jsou definovány lhůty hlášení (24h early warning, 72h full report, 1 měsíc final report)
- [ ] Byl IR plán otestován (tabletop exercise min. 1× ročně)?
- [ ] Jsou záznamy o provedených cvičeních dostupné?

**21(2)(c) — Kontinuita provozu, zálohy, disaster recovery**
- [ ] Existují zálohy PLC programů, DCS konfigurací a SCADA projektů?
- [ ] Jsou offline/air-gapped zálohy kritických konfigurací fyzicky uloženy mimo produkční síť?
- [ ] Jsou definovány RTO a RPO pro kritické řídicí systémy?
- [ ] Byl proveden test obnovy ze zálohy (min. 1× ročně)?
- [ ] Existují postupy pro degradovaný provoz (manuální operace) po dobu výpadku SCADA/DCS?

**21(2)(d) — Bezpečnost dodavatelského řetězce**
- [ ] Jsou v smlouvách s integrátory zahrnuty bezpečnostní požadavky (IEC 62443-2-4, právo na audit)?
- [ ] Je přístup dodavatelů řízen (časově omezený, MFA, jump server, session recording)?
- [ ] Je ověřována integrita SW a FW před instalací?
- [ ] Je k dispozici SBOM pro kritické OT produkty?

**21(2)(e) — Bezpečnost při pořizování, vývoji a údržbě**
- [ ] Jsou bezpečnostní požadavky součástí procesu nákupu OT systémů (RFP, výběrová kritéria)?
- [ ] Existuje OT patch management proces (staging, maintenance windows, kompenzační opatření)?
- [ ] Jsou nepatchovatelné systémy zdokumentovány, kompenzačně ošetřeny a formálně akceptovány managementem?

**21(2)(f) — Hodnocení efektivity**
- [ ] Je efektivita bezpečnostních opatření pravidelně hodnocena (metriky, audit, penetrační test)?
- [ ] Jsou výsledky hodnocení předávány managementu?
- [ ] Jsou zjištění zapracovávána do aktualizace bezpečnostní architektury a risk registru?

**21(2)(g) — Kybernetická hygiena a vzdělávání**
- [ ] Absolvoval management OT-specifické školení kybernetické bezpečnosti?
- [ ] Absolvovali operátoři SCADA/DCS školení rozpoznávání anomálií a hlášení incidentů?
- [ ] Absolvovali OT/automation engineers školení bezpečných inženýrských postupů?
- [ ] Jsou záznamy o absolvování školení dostupné?

**21(2)(h) — Kryptografie**
- [ ] Je VPN pro vzdálený přístup šifrovaná (IPsec/TLS 1.2+)?
- [ ] Jsou OPC UA inter-zone komunikace konfigurované s profilem Sign and Encrypt?
- [ ] Jsou zakázány zastaralé protokoly (TLS 1.0/1.1, SSLv3, MD5, SHA-1)?
- [ ] Jsou legacy OT zařízení bez kryptografické podpory síťově izolována a riziko formálně akceptováno?

**21(2)(i) — Správa identit, přístupu a aktiv**
- [ ] Je k dispozici aktuální inventář OT aktiv strukturovaný dle ISA-95 úrovní?
- [ ] Jsou unikátní účty pro každého uživatele (žádné sdílené účty na SCADA/DCS/HMI)?
- [ ] Je implementováno RBAC pro OT role (operátor, supervizor, inženýr, administrátor)?
- [ ] Je implementováno PAM pro privilegovaný přístup (engineering workstations, admin přístupy)?
- [ ] Je lifecycle management účtů (provisioning, přezkum, deprovisioning) formalizován?

**21(2)(j) — MFA**
- [ ] Je MFA vyžadováno pro veškerý vzdálený přístup do OT sítě (bez výjimky)?
- [ ] Je MFA vyžadováno pro přístup přes jump server?
- [ ] Je MFA vyžadováno pro přístup dodavatelů?
- [ ] Je použité MFA odolné vůči phishingu (hardware token nebo authenticator app, ne SMS)?

---

## Checklist 4: Hlášení incidentů — připravenost (Článek 23)

Použij pro ověření schopnosti splnit lhůty hlášení incidentů.

### Česky

**Detekce — předpoklad pro 24h lhůtu**
- [ ] Existuje automatizovaný OT monitoring (IDS, anomaly detection, SCADA alerting) — ne pouze manuální pozorování operátorů
- [ ] Je NTP synchronizace funkční na všech OT zařízeních (ověřitelné, monitorované)?
- [ ] Jsou logy ze všech klíčových OT systémů centralizovány a prohledatelné?
- [ ] Je definováno, kdo přijímá bezpečnostní alerty 24/7 (včetně víkendů a svátků)?

**Rozhodovací proces — co je „významný incident"**
- [ ] Existuje definice „významného incidentu" přizpůsobená OT kontextu organizace?
- [ ] Je definováno, kdo rozhoduje o klasifikaci incidentu jako „významný" a musí kontaktovat NÚKIB?
- [ ] Je tento rozhodovací proces zdokumentován a znám příslušným pracovníkům?

**Komunikační cesta k NÚKIB**
- [ ] Je definován kontaktní bod NÚKIB (telefonní číslo, e-mail, portál)?
- [ ] Existuje záložní komunikační cesta k NÚKIB, která funguje i při kompromitaci OT sítě?
- [ ] Je kontakt na NÚKIB k dispozici fyzicky na místě (ne pouze v digitálním systému, který může být nedostupný)?

**Lhůty a obsah hlášení**
- [ ] Je tým schopen sestavit early warning do 24 hodin (kdo to dělá, jaký je obsah)?
- [ ] Je tým schopen sestavit full incident report do 72 hodin (kdo to dělá, jaký je obsah)?
- [ ] Existuje šablona pro NÚKIB hlášení obsahující požadované informace?
- [ ] Je definována koordinace hlášení na sektorový regulátor (ERÚ, SÚJB, ŘLP...) pro případ překryvu?

**RACI matice pro incident response**
- [ ] Je definováno, kdo detekuje a kdo klasifikuje incident?
- [ ] Je definováno, kdo eskaluje na management?
- [ ] Je definováno, kdo kontaktuje NÚKIB?
- [ ] Je definováno, kdo koordinuje komunikaci se sektorovým regulátorem?
- [ ] Je definováno, kdo komunikuje s médii nebo zákazníky (pokud je to relevantní)?
- [ ] Jsou výše uvedené role zdokumentovány a pravidelně přezkoumávány (změny personálu)?

**Testování**
- [ ] Byl proveden tabletop exercise zahrnující simulaci NÚKIB hlášení?
- [ ] Byly výsledky zdokumentovány a zjištění zapracována?

---

## Checklist 5: Dodavatelský řetězec — OT compliance (Článek 21(2)(d))

Použij pro ověření splnění požadavků na bezpečnost dodavatelského řetězce.

### Česky

**Posouzení dodavatelů**
- [ ] Byl proveden inventář všech dodavatelů s přístupem do OT prostředí (integrátoři, servisní firmy, vendoři)?
- [ ] Byl proveden inventář všech OT produktů a jejich výrobců (PLC, DCS, SCADA software, průmyslové sítě)?
- [ ] Byly posouzeny bezpečnostní praxe klíčových dodavatelů (IEC 62443-2-4 pro integrátory, IEC 62443-4-1 pro výrobce SW)?

**Smluvní požadavky**
- [ ] Jsou v smlouvách s integrátory zahrnuty bezpečnostní povinnosti (hlášení incidentů, právo na audit)?
- [ ] Jsou v smlouvách s integrátory zakázány sdílené přihlašovací údaje mezi techniky?
- [ ] Jsou v smlouvách podmínky pro subcontracting (schválení zákazníkem, pass-through bezpečnostní požadavky)?
- [ ] Je definovaná odpovědnost za bezpečnostní defekty zanesené integrátorem?
- [ ] Jsou v smlouvách s výrobci HW/SW požadavky na dlouhodobou bezpečnostní podporu (EoL datum)?

**Řízení přístupu dodavatelů**
- [ ] Jsou přihlašovací údaje vydávány jako časově omezené (automatická expirace)?
- [ ] Je MFA vyžadováno pro všechny vzdálené přístupy dodavatelů?
- [ ] Probíhá veškerý vzdálený přístup přes jump server v Industrial DMZ?
- [ ] Je prováděn full session recording všech vzdálených relací?
- [ ] Je přístup okamžitě odvolán po dokončení servisní práce?
- [ ] Existuje evidence všech provedených přístupů s datem, dobou trvání a provedenými akcemi?

**Softwarová a firmwarová integrita**
- [ ] Je firmware a SW instalován výhradně z ověřených zdrojů (vendor portál, ne třetí strany)?
- [ ] Je hash/digitální podpis ověřován před instalací?
- [ ] Je k dispozici SBOM pro kritické OT komponenty?
- [ ] Existuje proces pro rychlé posouzení dopadu nové CVE na základě SBOM?

---

# AI Guidance

When answering NIS2-related questions:

* Position NIS2 as the top of the regulatory hierarchy — all national implementations and engineering standards derive from it.
* Distinguish legal requirements from engineering recommendations — NIS2 defines what, IEC 62443 defines how.
* Always identify the entity category (essential vs. important) before discussing specific obligations — the supervisory model and proportionality expectation differ.
* Explain how each NIS2 obligation translates into a concrete OT architectural capability.
* Note that NIS2 is not directly applicable law — national transpositions apply. For Czech context, always refer to [Czech-Cybersecurity-Act.md](Czech-Cybersecurity-Act.md).
* Refer to [IEC62443.md](../02-Standards/IEC62443.md) for engineering implementation of security capabilities.
* Refer to [ISA95.md](../02-Standards/ISA95.md) for functional architecture analysis that underpins scope determination and zone design.
* When discussing incident reporting, emphasize that the 24-hour early warning deadline requires pre-built detection capability — it cannot be met reactively.
* When discussing supply chain, reference IEC 62443-2-4 (integrators), IEC 62443-4-1/4-2 (products), and SBOM.
* When discussing management accountability (Article 20), emphasize that personal liability for management is a significant change from NIS1.
* Do not present NIS2 as a technical implementation guide — it is a governance and objectives framework.
* When sector-specific questions arise, reference the sector-specific OT implications section.

---

# Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | 2026-06-28 | Initial release |
| 1.1.0 | 2026-06-29 | Added regulatory hierarchy, entity categories table, enforcement penalties, architectural implications table, NIS2/IEC62443 comparison, cross-links |
| 1.2.0 | 2026-06-29 | Major expansion: NIS1 vs NIS2 comparison table; size thresholds; detailed Annex I/II sector tables with OT presence; article-by-article analysis (Art. 20, 21, 22, 23, 32, 33, 34); OT policy framework per Article 21(2); OT backup table; OT vulnerability management IT vs OT comparison; OT training matrix by role; OT MFA application table; cryptography application table for OT; significant incident OT examples; real-world OT incident pattern table; NIS2 obligation → architecture → engineering standard mapping; sector-specific OT implications (energy, transport, water, manufacturing, healthcare); relationship with CER Directive, CRA, GDPR, Machinery Regulation; non-financial sanctions; business case analysis; 5× checklist in Czech language |
| 1.2.1 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings |
| 1.2.2 | 2026-07-07 | Fixed dangling placeholder anchors ([Document.md](#)) to real relative paths in Related Documents / inline references (12 links) |
