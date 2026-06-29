---
title: Czech Cybersecurity Act and Implementing Decrees
subtitle: Act No. 264/2025 Coll. | Decree 408 | Decree 409 | Decree 410
category: Legislation
version: 1.1.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-06-29
review_cycle: Annual
---

# Purpose

This document provides an engineering-oriented overview of the Czech national cybersecurity regulatory framework from the perspective of an OT Security Architect.

It covers:

* **Act No. 264/2025 Coll.** — Czech Cybersecurity Act (transposition of NIS2),
* **Decree No. 408/2025 Coll.** — Regulated Services (scope determination),
* **Decree No. 409/2025 Coll.** — Higher Obligations (security measures),
* **Decree No. 410/2025 Coll.** — Lower Obligations (security measures).

Rather than reproducing legal text, this document explains how the framework influences the design, governance and operation of industrial systems — particularly OT/ICS environments.

---

# Related Documents

| Document | Relationship |
|----------|-------------|
| [NIS2.md](#) | EU directive this Act transposes — read first |
| [IEC62443-Overview.md](#) | Engineering implementation framework |
| [ISA95.md](#) | Manufacturing functional reference model |

---

# Regulatory Hierarchy

```
[NIS2 Directive (EU 2022/2555)]        ← EU strategic framework
        │
[Act No. 264/2025 Coll.]               ← Czech national legal obligations
        │
        ├── [Decree 408]               ← Who is regulated (scope)
        │
        ├── [Decree 409]               ← How (Higher Obligations)
        │
        └── [Decree 410]               ← How (Lower Obligations)
                │
        [IEC 62443 / ISA-95]           ← Engineering implementation
```

These four layers must always be considered together. The Act defines **what** is legally required. Decrees define **the extent**. IEC 62443 defines **how** to implement it.

---

---

# Part 1 — Act No. 264/2025 Coll. (Czech Cybersecurity Act)

---

## Overview

The Czech Cybersecurity Act (zákon o kybernetické bezpečnosti) transposes NIS2 into Czech law.

It establishes:

* which organizations fall under regulation,
* which cybersecurity obligations apply,
* the role of NÚKIB (Národní úřad pro kybernetickou a informační bezpečnost),
* the distinction between the Higher and Lower Obligation regimes,
* the legal basis for the implementing decrees.

The Act replaces the previous Cybersecurity Act No. 181/2014 Coll.

> **Supervisory authority:** NÚKIB is the primary Czech cybersecurity supervisory authority. It issues methodological guidance, conducts inspections, handles incident notifications and cooperates with ENISA and EU institutions.

---

## Architect's Perspective

The Act is not a technical standard. It establishes:

* governance expectations (management responsibility),
* mandatory organizational obligations,
* risk management requirements,
* accountability structures,
* supervisory and enforcement processes.

Technical implementation is achieved through architecture, operational processes and security controls — informed by Decree 409 or 410 and engineering standards such as IEC 62443.

---

## Governance — Management Responsibility

The Act makes cybersecurity an explicit management responsibility, not solely an IT or OT function.

Management obligations include:

* Approving cybersecurity policies.
* Ensuring adequate resources (financial, human, technological).
* Overseeing risk management processes.
* Supporting continual improvement.
* Ensuring regulatory compliance.

**Architectural implication:** In OT projects, the plant manager or operations director must be formally involved in approving architectural decisions and security trade-offs — not only the CISO or security architect.

---

## Risk Management

The Act requires continuous cybersecurity risk management.

Every design decision should be supported by:

* documented assumptions,
* risk assessment,
* decision justification,
* traceability from requirement to implementation.

Architecture documentation must demonstrate how identified risks are addressed. A risk assessment is not a one-time activity — it must be maintained throughout the system lifecycle.

---

## Incident Reporting

The Act establishes reporting obligations for cybersecurity incidents.

| Stage | Deadline |
|-------|----------|
| Early warning | Within 24 hours of becoming aware |
| Full incident report | Within 72 hours |
| Final report | Within 1 month |

Reports are submitted to NÚKIB.

**Architectural prerequisites:**

* Centralized logging with protected integrity (preferably WORM).
* NTP time synchronization across all OT assets.
* Defined communication channels to NÚKIB outside the OT network.
* RACI matrix for incident response responsibilities.
* Incident detection capability (monitoring, IDS, anomaly detection).

> Incident reporting capability must be designed into the system architecture — it cannot be improvised at the time of an incident.

---

## Common Misconceptions

The Act does **not** prescribe:

* Specific firewall products or vendors.
* Mandatory SIEM platforms.
* Fixed network topologies.
* Specific authentication technologies.
* Mandatory use of particular standards.

Engineering decisions remain the responsibility of the organization. The Act establishes legal obligations — architecture satisfies them.

---

---

# Part 2 — Decree No. 408/2025 Coll. (Regulated Services)

---

## Purpose of the Decree

Decree 408 determines **whether an organization falls within the scope** of the Czech Cybersecurity Act and which regulatory regime initially applies.

It is the mandatory starting point of every compliance assessment. Without completing this step, discussing security controls is premature.

---

## Assessment Workflow

```
1. Identify the organization and its business services
        │
2. Compare business services against the list of regulated services (Annex to Decree 408)
        │
3. Evaluate significance criteria (size, nature, thresholds)
        │
4. Determine the applicable obligation regime (Higher or Lower)
        │
5. Proceed to Decree 409 or Decree 410
        │
6. Begin security architecture design
```

This assessment must be **documented** and retained as part of project governance.

---

## Regulated Sectors

The decree covers multiple sectors. Those most relevant to OT/ICS environments:

| Sector | Typical OT Systems | Notes |
|--------|--------------------|-------|
| Energy (electricity, gas, oil, hydrogen) | SCADA, EMS, DMS, DERMS, RTU | High likelihood of inclusion |
| Transport (rail, air, water, road) | PLC, signaling, ATC, ERTMS | Critical infrastructure |
| Drinking water | SCADA, PLC (pumping stations, treatment) | Depends on size |
| Wastewater | SCADA, PLC | Depends on size |
| Manufacturing (NIS2 Annex II) | ICS, MES, PLC production lines | Depends on product category |
| Chemicals | DCS, SIS (Safety Instrumented Systems) | Possible inclusion |
| Food production | SCADA, PLC | Possible inclusion |
| Space | Ground segment control systems | Specific criteria |

> **Architect note:** Never assume every industrial organization is regulated. Verify against the official annex to Decree 408. The assessment must be evidence-based, ideally involving legal counsel and NÚKIB guidance.

---

## Significance Criteria

Providing a regulated service alone does not always trigger regulation. The decree defines additional significance criteria:

* Organization size (employee count, turnover).
* Nature and criticality of the regulated activity.
* Sector-specific thresholds.
* Additional legal conditions.

Assessment must be documented with supporting evidence.

---

## Higher vs. Lower Regime — Key Rule

Each regulated service has an initial regime assignment. However:

> **If at least one regulated service places the organization into the Higher Obligations regime, that regime applies to the organization as a whole.**

All regulated services provided by the organization must therefore be evaluated — not only the primary one.

---

## Common Assessment Mistakes

* Assuming every industrial company is regulated.
* Assuming manufacturing automatically results in Higher Obligations.
* Evaluating only the primary business service.
* Ignoring significance criteria.
* Performing architecture design before completing regulatory assessment.
* Treating scope determination as an IT-only responsibility.

---

---

# Part 3 — Decree No. 409/2025 Coll. (Higher Obligations)

---

## Purpose of the Decree

Decree 409 defines the cybersecurity measures that organizations under the **Higher Obligations** regime must implement and maintain.

It specifies required **security capabilities** — not products or configurations. Implementation should be proportional to operational risk, process criticality, threat exposure and business requirements.

Most OT projects in regulated critical infrastructure will ultimately be governed by this decree.

---

## Engineering Philosophy

Treat Decree 409 as a set of required security capabilities, not a compliance checklist.

Each capability must be:

* implemented proportionally to risk,
* sustained through operational processes,
* documented in a verifiable manner,
* reviewed periodically.

Compliance naturally emerges from a well-designed, well-operated architecture.

---

## Security Capability Domains

### Governance

Establish clear, documented responsibility for cybersecurity.

Required elements:

* Defined security roles and responsibilities (RACI).
* Approved cybersecurity policies.
* Management oversight and periodic review.
* Continuous improvement process.
* Cybersecurity included in project governance from day one.

**OT implication:** Security governance must extend to the OT domain. Plant managers and process engineers are part of the governance structure — not only IT or security teams.

---

### Asset Management

Know what must be protected before designing protection.

Required capabilities:

* Asset inventory covering all OT components: PLC, RTU, HMI, DCS, engineering workstations, historians, industrial switches, wireless access points, safety systems.
* Asset ownership assignment.
* Criticality classification.
* Lifecycle tracking.
* Firmware and software version records.
* Configuration records.

> Unknown assets cannot be secured, patched or recovered. Asset inventory is the foundation of all other security capabilities.

---

### Risk Management

Support informed architectural and operational decisions.

Required capabilities:

* Systematic risk identification.
* Risk evaluation (likelihood × impact).
* Risk treatment decisions with documented justification.
* Risk acceptance with management sign-off.
* Periodic risk review.

**Recommended methodology for OT:**

| Step | Standard/Method |
|------|----------------|
| Asset identification | IEC 62443-2-1 |
| Threat and vulnerability analysis | IEC 62443-3-2 (SLRA) |
| Target Security Level determination | IEC 62443-3-3 |
| Control implementation | IEC 62443-3-3 / 4-2 |
| Verification | IEC 62443-2-1 / audit |

> **Functional safety interface:** Risk assessment for OT must consider the relationship between cybersecurity risk and functional safety (SIL, IEC 61511). A cyber attack on a Safety Instrumented System (SIS) can directly cause safety incidents. The Triton/TRISIS incident (2017) demonstrated this risk in a real-world petrochemical environment.

---

### Identity and Access Management (IAM)

Ensure that only authorized identities can access OT assets.

Required capabilities:

* Unique user accounts — no shared accounts on SCADA, DCS or HMI.
* Identity lifecycle management (provisioning, deprovisioning).
* Role-based access control (RBAC) at SCADA and DCS level.
* Privileged Access Management (PAM) for engineering workstations and PLC engineering software.
* Service account management and review.
* Principle of least privilege, including at PLC function block level where applicable.
* Multi-factor authentication (MFA) for remote access and privileged operations.

**Remote vendor access:**

* Time-limited access only — no permanent vendor credentials.
* Access exclusively through a jump server in the Industrial DMZ.
* Full session recording.
* Protocol of all actions performed by the vendor.

---

### Network Security and Segmentation

Network segmentation is one of the most critical architectural controls in OT.

**Reference architecture — Purdue / ISA-95 model with IEC 62443 zones:**

```
[Enterprise IT — L4/5]
    ERP, MES (business functions)
        │  Firewall + Industrial DMZ
[Industrial DMZ — L3.5]
    Historian (read-only replica), Jump Server, File Transfer Server
        │  Firewall (whitelist only)
[Control Network — L2/3]
    SCADA, DCS, HMI, Engineering Workstations
        │  Firewall / conduit
[Field Level — L0/1]
    PLC, RTU, Sensors, Actuators, Safety Systems
```

Required capabilities:

* **Industrial DMZ** as mandatory buffer between IT and OT — direct IT↔OT connectivity is not acceptable.
* **Unidirectional gateways (data diodes)** for data flows from OT to IT where a reverse channel is not required.
* **Firewalls with allowlist (whitelist) rules** at all zone boundaries — deny-by-default.
* **VLAN segmentation** within OT networks according to Security Zones.
* **Minimization of communication paths** — every open port and protocol must be justified.
* **Physical separation** of critical control networks from corporate infrastructure where feasible.

See [IEC62443-Overview.md](#) for the Zone and Conduit design methodology.

---

### Cryptography

Protect sensitive communications and authentication mechanisms.

Required capabilities:

* TLS for all network communications where supported by OT equipment.
* Certificate management and lifecycle (PKI or managed CA).
* Secure key storage.
* Modern cryptographic algorithms — avoid deprecated protocols (SSLv3, TLS 1.0, MD5, SHA-1).

> **OT constraint:** Legacy OT devices may not support modern cryptography. Compensating controls (network isolation, monitoring) must be documented and accepted as a risk treatment decision.

---

### Logging and Monitoring

Provide operational visibility and incident detection capability.

Required capabilities:

* Centralized log collection from all OT assets capable of generating logs.
* **NTP time synchronization** across all OT devices — critical for log correlation and forensic analysis.
* Log integrity protection (WORM storage or equivalent).
* Log retention aligned with decree requirements.
* Security monitoring with alerting.
* **OT-specific passive monitoring/IDS** (e.g., Claroty, Nozomi Networks, Dragos) for industrial protocols: Modbus, DNP3, IEC 61850, PROFINET, EtherNet/IP, OPC UA.
* Anomaly detection for unauthorized PLC/DCS configuration changes.

---

### Vulnerability Management

Identify and address vulnerabilities in a manner appropriate to OT constraints.

Required capabilities:

* Regular vulnerability identification — preferably passive scanning to avoid destabilizing legacy OT devices.
* CVE tracking for all OT components (PLC firmware, SCADA software, OS).
* Risk-based prioritization — not all vulnerabilities require immediate remediation.
* Patch management process adapted to OT: test environment, maintenance windows, coordination with production schedule.
* **Compensating controls** for systems that cannot be patched (end-of-life PLCs, legacy SCADA). Document, justify and formally accept the residual risk.

---

### Backup and Recovery

Recovery capability is more important than backup creation alone.

Required capabilities:

* Backups of PLC programs, DCS configurations, SCADA projects, engineering workstation images.
* **Offline/air-gapped backups** for critical configurations.
* Defined RTO (Recovery Time Objective) and RPO (Recovery Point Objective) for critical control systems.
* **Tested recovery procedures** — backup without verified restoration is insufficient.
* Defined degraded mode operation for production during a cyber incident.
* Secure storage of backup media (physical and logical protection).

---

### Incident Management

Detect, respond to and recover from cybersecurity incidents in a structured manner.

Required capabilities:

* ICS-specific Incident Response Plan (not an adaptation of IT IR plans).
* Defined escalation paths when a cyber incident affects production or process safety.
* NÚKIB notification procedures (see Part 1 — Incident Reporting).
* Forensic capability for OT environments that does not disrupt ongoing operations.
* Post-incident lessons learned process feeding back into risk management.

> Incident response procedures must be exercised (tabletop exercises, simulations) before a real incident occurs.

---

### Supply Chain Security

Industrial systems depend on system integrators, equipment vendors, SCADA software suppliers and remote maintenance providers. All must be governed.

Required capabilities:

* **Controlled vendor remote access:** time-limited, MFA-protected, through jump server, fully recorded.
* **Software and firmware integrity:** hash/signature verification at installation; no uncontrolled USB media.
* **Isolated test environment** for validation before production deployment.
* **Software Bill of Materials (SBOM)** for critical OT components.
* **Contractual security requirements** covering: security incident notification, right to audit, secure development lifecycle (IEC 62443-4-1), access governance.
* **IEC 62443-2-4** compliance as a selection criterion for system integrators.

---

### Business Continuity

Ensure operational resilience and defined recovery capability.

Required elements:

* Business continuity plan addressing OT-specific scenarios (cyber incident, ransomware, physical failure).
* Disaster recovery plan with defined priorities.
* Manual operating procedures for critical processes when automated control is unavailable.
* Recovery testing — not only planning.

---

### Documentation

Documentation is an engineering deliverable, not merely an audit artifact.

Required documentation:

| Document | Content |
|----------|---------|
| Architecture diagrams | Network topology, zones and conduits, data flows — must reflect reality |
| Asset inventory | All OT assets with FW/SW version, owner, criticality |
| Risk assessment | Threats, vulnerabilities, risk ratings, treatment decisions |
| Security policies | IAM, patch management, remote access, change management |
| Operating procedures | Safe working procedures for OT systems |
| Incident response plan | OT-specific, tested |
| Backup procedures | What, when, how, where, who tests, evidence of testing |
| Change records | Auditable record of all configuration changes |
| Supplier documentation | Contracts, access records, audit results |

---

---

# Part 4 — Decree No. 410/2025 Coll. (Lower Obligations)

---

## Purpose of the Decree

Decree 410 defines cybersecurity measures for organizations operating under the **Lower Obligations** regime.

The objective is not reduced security — it is **proportionate, sustainable, risk-based security** that matches the organization's actual risk profile and operational capacity.

---

## Engineering Philosophy

The Lower Obligations regime represents:

* proportional governance,
* practical security controls,
* proportionate documentation,
* continuous risk management at an appropriate scale,
* operational sustainability.

> A simpler architecture that is consistently operated and maintained is usually more secure than a complex architecture that cannot be sustained.

---

## Security Capabilities — Lower Obligations

The same capability domains apply as in the Higher Obligations regime. The difference lies in the expected depth, formality and complexity.

---

### Governance

Required:

* Defined cybersecurity responsibilities (named responsible person).
* Documented security policy approved by management.
* Periodic review.

Governance must be simple but clearly defined and consistently applied.

---

### Asset Management

Required:

* Up-to-date inventory of assets supporting regulated services.
* Asset owner, function, location, criticality, lifecycle status.

An accurate asset inventory is one of the highest-value controls regardless of regime.

---

### Risk Management

Required:

* Identification of significant risks.
* Evaluation of impact.
* Defined risk treatment.
* Periodic review.

Risk management should remain proportionate to organizational complexity and resources.

---

### Identity and Access Management

Required:

* Unique user accounts — no shared credentials.
* Appropriate authentication strength.
* Least privilege.
* Account lifecycle management.
* Periodic review of privileged accounts.
* Controlled and documented vendor access.

---

### Network Security

Required:

* Basic network segmentation separating OT from IT.
* Controlled communication paths.
* Secure remote access.
* Appropriate firewalling.

Segmentation should follow operational needs. Avoid arbitrary network complexity that cannot be maintained.

---

### Logging and Monitoring

Required:

* Collection of relevant security events.
* Time synchronization (NTP).
* Sufficient log retention to support incident investigation.
* Capability to investigate incidents.

Monitoring should match organizational size and operational complexity.

---

### Vulnerability Management

Required:

* Identification of known vulnerabilities.
* Risk-based prioritization.
* Patch planning.
* Compensating controls where patching is not immediately feasible.

Operational stability must always be considered before remediation.

---

### Backup and Recovery

Required:

* Configuration and system backups.
* Documented recovery procedures.
* Periodic restoration testing (not only backup creation).

A backup that has never been tested is not a recovery capability.

---

### Incident Management

Required:

* Detection and initial assessment procedure.
* Escalation path.
* Recovery procedure.
* Incident documentation.
* Reporting to NÚKIB where required.

The decree also requires organizations to assess the significance of incidents for reporting purposes.

---

### Documentation

Required:

* Asset inventory.
* Network diagrams.
* Security procedures.
* Backup procedures.
* Risk assessments.
* Change records.

Documentation should support both daily operations and compliance.

---

## Regime Comparison

| Area | Higher Obligations (Decree 409) | Lower Obligations (Decree 410) |
|------|--------------------------------|-------------------------------|
| Governance | Comprehensive, formal structure | Clear but proportionate |
| Documentation | Extensive, auditable | Focused and practical |
| Risk Management | Detailed, structured processes | Simplified but continuous |
| Security Controls | Broad set of capabilities | Core capabilities |
| IAM | PAM, MFA, full lifecycle | Unique accounts, least privilege, controlled vendor access |
| Network Security | Industrial DMZ, data diodes, strict allowlists | Basic segmentation, controlled paths |
| Monitoring | OT IDS, passive protocol analysis, SIEM integration | Relevant event collection, NTP, investigation capability |
| Vulnerability Management | CVE tracking, compensating controls, formal acceptance | Identification, prioritization, patch planning |
| Backup | Air-gapped, tested, RTO/RPO defined | Configuration and system backup, tested restoration |
| Audit | Regular, independent | Internal / ad hoc |
| NÚKIB Supervision | Proactive | Reactive |
| Engineering Goal | Comprehensive resilience | Sustainable resilience |

Both regimes are based on continuous risk management. The difference is the expected level of maturity — not the importance of cybersecurity.

---

## Common Mistakes in Lower Obligations Environments

* Treating Lower Obligations as "no obligations."
* Copying enterprise IT architectures without OT adaptation.
* Implementing excessive controls that increase operational burden and cannot be maintained.
* Ignoring documentation because the regime appears lighter.
* Treating regulatory compliance as the end goal instead of operational resilience.

---

---

# Architect Checklist — OT Project Initiation

Use this checklist at the start of any regulated OT project:

- [ ] Is the organization subject to Act No. 264/2025 Coll.?
- [ ] Which regulated services does the organization provide? (Decree 408)
- [ ] Are significance criteria met?
- [ ] Which regime applies — Higher (Decree 409) or Lower (Decree 410)?
- [ ] Has this assessment been documented and retained?
- [ ] Is an up-to-date OT asset inventory available?
- [ ] Has a Security Level Risk Assessment (IEC 62443-3-2) been performed?
- [ ] Are Security Zones and Conduits defined?
- [ ] Is an Industrial DMZ designed as the IT/OT boundary?
- [ ] Is secure remote access implemented (MFA, jump server, session recording)?
- [ ] Are PLC/DCS/SCADA configuration backups in place and tested?
- [ ] Does an ICS-specific Incident Response Plan exist and has it been exercised?
- [ ] Are contractual security requirements placed on all OT suppliers and integrators?
- [ ] Is documentation maintained continuously and kept auditable?
- [ ] Is management formally involved (policy approval, resource allocation)?
- [ ] Are NÚKIB notification procedures defined and tested?

---

# AI Guidance

When answering questions related to Czech cybersecurity legislation:

* Always start by determining whether the organization is regulated (Decree 408 — scope first).
* Distinguish between legal obligations (Act, Decrees) and engineering recommendations (IEC 62443).
* Identify the applicable regime (Higher or Lower) before recommending security measures.
* Recommend risk-based architectures proportional to the applicable regime.
* Refer to [IEC62443-Overview.md](#) for implementation detail.
* Refer to [NIS2.md](#) for the EU-level governance framework.
* Do not recommend over-engineered solutions for Lower Obligations environments.
* Consider functional safety implications when discussing OT security measures.
* Clarify regulatory applicability assumptions if the organization's context is unclear.

---

# Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | 2026-06-28 | Initial release (separate files) |
| 1.1.0 | 2026-06-29 | Consolidated into single document covering Act + Decrees 408/409/410; added regulatory hierarchy, OT-specific implementation guidance, functional safety reference, regime comparison table, architect checklist, cross-links |