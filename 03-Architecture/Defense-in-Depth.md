---
id: defense-in-depth
title: "Defense in Depth"
category: Architecture
layer: "03-Architecture"
version: 1.1.1
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Authoritative deep-dive on layered security in OT: independent control layers from physical
  security to backup and recovery, design criteria for independence, and illustrative mapping to
  ATT&CK for ICS.
keywords: [defense in depth, vrstvená obrana, layered security, independent controls, security layers]
---

# Purpose

Defense in Depth is a foundational cybersecurity strategy that reduces risk by implementing multiple independent and complementary security controls.

Rather than relying on a single protective mechanism, Defense in Depth assumes that every individual control may eventually fail.

The objective is therefore not to prevent every attack, but to ensure that no single failure results in complete compromise of the industrial process.

Within Operational Technology (OT), Defense in Depth must always support:

* Human safety
* Process availability
* Operational continuity
* Cyber resilience

> **Principle vs. deep-dive.** Defense in Depth is stated as an architectural *principle* in [OT-Architecture-Principles.md](../00-Core/OT-Architecture-Principles.md) (Principle 5) and [OT-Security-Philosophy.md](../00-Core/OT-Security-Philosophy.md). **This document is the authoritative deep-dive**: the layered model and the mapping of layers to attacker techniques. It is one of three complementary strategies alongside [Zero-Trust-in-OT.md](Zero-Trust-in-OT.md) and [Risk-Based-Security.md](Risk-Based-Security.md).

---

# Scope

This document describes:

* Defense in Depth principles
* Layered security
* OT-specific implementation
* Relationship with IEC 62443
* Architectural design
* Operational resilience
* Detection strategy
* Recovery considerations

Vendor-specific technologies are intentionally excluded.

---

# Security Philosophy

No security control is perfect.

Every security mechanism has limitations.

Examples include:

* Firewalls may contain misconfigurations.
* Credentials may be stolen.
* Software vulnerabilities may remain undiscovered.
* Users may make mistakes.
* Vendors may introduce insecure updates.

Defense in Depth assumes these failures will occur and designs the architecture accordingly.

---

# Why Defense in Depth Matters

Industrial systems protect physical processes rather than information alone.

Consequences of compromise may include:

* Personnel injury
* Equipment damage
* Environmental impact
* Production interruption
* Regulatory consequences
* Financial loss

Multiple independent security layers reduce the probability that a single attack results in physical impact.

---

# Core Principles

Effective Defense in Depth follows several principles:

* Independent controls
* Multiple trust boundaries
* Risk-based design
* Least Privilege
* Segmentation
* Continuous monitoring
* Secure recovery
* Operational resilience

No individual technology should become a single point of security.

---

# Security Layers

Defense in Depth can be visualized as multiple architectural layers.

```text
Physical Security
        ↓
Personnel & Governance
        ↓
Identity Management
        ↓
Network Segmentation
        ↓
Zones & Conduits
        ↓
Firewalls
        ↓
Secure Protocols
        ↓
Endpoint Protection
        ↓
Monitoring & Logging
        ↓
Incident Response
        ↓
Backup & Recovery
```

Each layer compensates for weaknesses in other layers.

---

# Physical Security

Physical access often bypasses many logical controls.

Recommended measures include:

* Controlled access
* CCTV
* Locked control cabinets
* Visitor management
* Tamper detection
* Secure equipment rooms

Physical security remains the foundation of OT protection.

---

# Governance Layer

Organizational controls include:

* Security policies
* Roles and responsibilities
* Supplier management
* Security awareness
* Change management
* Risk management
* Asset ownership

Technology cannot compensate for ineffective governance.

---

# Identity Layer

Identity is one of the strongest security boundaries.

Recommended controls include:

* Unique identities
* MFA
* Least Privilege
* Role-Based Access Control (RBAC)
* Privileged Access Management (PAM)
* Certificate-based authentication where appropriate

Shared administrative accounts should be avoided. See [Identity-Management.md](../05-Identity/Identity-Management.md) and [Zero-Trust-in-OT.md](Zero-Trust-in-OT.md).

---

# Network Layer

Network architecture should support:

* Zones
* Conduits
* Industrial DMZ
* Segmentation
* Trust boundaries

Flat industrial networks significantly reduce the effectiveness of Defense in Depth. See [Network-Segmentation.md](../04-Network/Network-Segmentation.md), [Firewall-Design.md](../04-Network/Firewall-Design.md) and the zone/conduit model in [IEC62443.md](../02-Standards/IEC62443.md).

---

# Communication Layer

Communication should be:

* Explicitly permitted
* Authenticated where possible
* Encrypted where appropriate
* Monitored
* Documented

Every communication path should have a business justification.

---

# Endpoint Layer

Critical assets include:

* Engineering workstations
* SCADA servers
* Historians
* Domain Controllers
* Jump Servers
* Operator workstations

Recommended controls include:

* Hardening
* Application allowlisting
* Patch management
* Anti-malware (where operationally appropriate)
* Secure configuration

---

# Monitoring Layer

Detection capabilities should include:

* Firewall events
* VPN activity
* Identity events
* PLC programming
* Configuration changes
* Certificate events
* Engineering sessions
* Protocol anomalies

Early detection significantly improves resilience.

---

# Response Layer

Preparation is as important as prevention.

Organizations should maintain:

* Incident Response Plans
* Escalation procedures
* Communication plans
* Recovery procedures
* Technical playbooks

Prepared organizations recover significantly faster.

---

# Recovery Layer

Recovery capabilities should include:

* Offline backups
* Tested restoration procedures
* PLC project backups
* Configuration management
* Disaster recovery testing
* Business continuity planning

Recovery planning should begin during system design.

---

# Relationship with IEC 62443

Defense in Depth is a fundamental architectural principle throughout the IEC 62443 family.

It supports:

* Zones & Conduits (FR5)
* Security Levels
* Least Privilege (FR2)
* Identification & Authentication (FR1)
* System Integrity (FR3)
* Secure Communications (FR4)

IEC 62443 emphasizes that no single security control should be solely relied upon. See [IEC62443.md](../02-Standards/IEC62443.md).

---

# Relationship with NIS2

NIS2 does not explicitly mandate Defense in Depth.

However, its risk management philosophy strongly aligns with layered security.

Organizations implementing Defense in Depth generally satisfy multiple NIS2 expectations including:

* Risk management
* Incident detection
* Business continuity
* Supply chain security
* Monitoring

See [NIS2.md](../01-Legislation/NIS2.md).

---

# Relationship with MITRE ATT&CK for ICS

Every layer interrupts different attacker techniques.

The following mapping is **illustrative**, not a formal control-to-technique matrix. MITRE ATT&CK for ICS defines its own tactics (e.g. *Initial Access*, *Lateral Movement*, *Privilege Escalation*, *Discovery*, *Inhibit Response Function*, *Impair Process Control*, *Impact*); consult the ATT&CK for ICS matrix and [MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md) for authoritative mappings.

| Security Layer       | Illustrative ATT&CK for ICS relevance |
| -------------------- | ------------------------------------- |
| MFA                  | Initial Access                        |
| Network Segmentation | Lateral Movement                      |
| PAM                  | Privilege Escalation                  |
| Monitoring           | Discovery & Persistence               |
| Change Management    | Impair Process Control / manipulation |
| Backup & Recovery    | Impact                                |

Layered controls reduce attacker freedom throughout the attack lifecycle.

---

# Common Design Mistakes

Typical mistakes include:

* Relying on a single firewall.
* Believing antivirus alone provides security.
* Ignoring engineering workstations.
* Treating network segmentation as the only control.
* No monitoring.
* No recovery planning.
* Shared administrative identities.
* Security technologies without governance.

---

# Architect Notes

One of the most common misconceptions is that Defense in Depth means "buying more security products."

It does not.

Effective Defense in Depth is achieved when:

* controls are independent,
* controls complement each other,
* failures remain contained,
* recovery is possible.

Adding multiple technologies that all fail for the same reason does not improve resilience.

Architects should optimize for independence rather than quantity.

---

# Decision Support

Before recommending a new security control, determine:

1. Which existing layer does it strengthen?
2. Does it duplicate another control?
3. What happens if it fails?
4. Which attack techniques does it interrupt?
5. Does it introduce operational risk?
6. Can it be monitored?
7. Does it improve recovery?
8. Is it independent of existing controls?

---

# AI Guidance

When discussing Defense in Depth:

* Explain that it is an architectural strategy rather than a product.
* Recommend independent layers rather than multiple controls of the same type.
* Prioritize safety and operational continuity.
* Map recommendations to Zones & Conduits, Identity Management and Risk-Based Security.
* Explain how individual controls reduce attacker progression rather than promising complete protection.
* Consider prevention, detection, response and recovery equally important.

---

# Sources / Grounding

* **NIST SP 800-82 Rev. 3** — *Guide to OT Security* (2023): explicitly advocates a defense-in-depth strategy for OT (policies, architecture, layered technical and procedural controls).
* **ISA/IEC 62443** — defense in depth as a foundational principle; zones and conduits, foundational requirements. See [IEC62443.md](../02-Standards/IEC62443.md).
* **CISA** — layered-defense / defense-in-depth guidance for industrial control systems; MITRE **ATT&CK for ICS** for attacker-technique context.

> Defense in Depth is an architectural strategy; normative requirements live in the standards documents.

---

# Related Documents

* [OT-Security-Philosophy.md](../00-Core/OT-Security-Philosophy.md)
* [OT-Architecture-Principles.md](../00-Core/OT-Architecture-Principles.md)
* [Zero-Trust-in-OT.md](Zero-Trust-in-OT.md)
* [Risk-Based-Security.md](Risk-Based-Security.md)
* [Identity-Management.md](../05-Identity/Identity-Management.md)
* [Network-Segmentation.md](../04-Network/Network-Segmentation.md)
* [Firewall-Design.md](../04-Network/Firewall-Design.md)
* [MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md)
* [ICS-Kill-Chain.md](../07-Threat-Intelligence/ICS-Kill-Chain.md)
* [Incident-Response.md](../09-Operations/Incident-Response.md)
* [Monitoring.md](../09-Operations/Monitoring.md)
* [Backup.md](../09-Operations/Backup.md)
* [IEC62443.md](../02-Standards/IEC62443.md)
* [NIS2.md](../01-Legislation/NIS2.md)

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
| 1.1.0   | 2026-07-01 | Converted to YAML front matter (category: Architecture) and semver; corrected links (`IEC62443-Overview.md` → `IEC62443.md`) and removed the retired `Zones-and-Conduits.md` reference; added principle-vs-deep-dive scope note delegating the DiD principle to OT-Architecture-Principles.md; labelled the MITRE ATT&CK for ICS table as illustrative and noted the real ATT&CK for ICS tactics; added Sources/Grounding (NIST SP 800-82 Rev. 3, IEC 62443, CISA); added strategy-trio and Core cross-links; converted Related Documents to markdown links |
| 1.1.1 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings |
