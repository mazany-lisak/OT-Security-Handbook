---
title: NIS2 Directive (EU 2022/2555)
category: Legislation
version: 1.1.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-06-29
review_cycle: Annual
---

# Purpose

This document provides an engineering-oriented overview of the NIS2 Directive (Directive EU 2022/2555) from the perspective of an OT Security Architect.

Rather than reproducing the legal text, it explains how NIS2 influences the design, governance and operation of industrial systems — particularly those involving Operational Technology (OT), Industrial Control Systems (ICS) and SCADA infrastructure.

**This document is the top of the regulatory hierarchy.** All national implementations derive from it.

---

# Related Documents

| Document | Relationship |
|----------|-------------|
| [Czech-Cybersecurity-Act.md](#) | Czech national transposition of NIS2 |
| [Decree-408-Regulated-Services.md](#) | Czech scope determination |
| [Decree-409-Higher-Obligations.md](#) | Czech higher-regime security measures |
| [Decree-410-Lower-Obligations.md](#) | Czech lower-regime security measures |
| [IEC62443-Overview.md](#) | Engineering implementation framework |
| [ISA95.md](#) | Manufacturing functional reference model |

---

# Regulatory Hierarchy

NIS2 sits at the top of a four-layer structure. All lower documents must be read in this context.

```
[NIS2 Directive (EU 2022/2555)]        ← Strategic objectives, EU-wide
        │
[Czech Cybersecurity Act 264/2025]     ← National legal obligations
        │
[Decrees 408 / 409 / 410]             ← Detailed security measures and scope
        │
[IEC 62443 / ISA-95]                  ← Engineering implementation
```

NIS2 defines **what** must be achieved. National legislation, decrees and engineering standards define **how**.

---

# What Is NIS2?

NIS2 (Network and Information Security Directive 2) was adopted by the European Parliament and Council on 14 December 2022 and entered into force on 16 January 2023. Member States were required to transpose it into national law by 17 October 2024.

NIS2 replaces the original NIS Directive (EU 2016/1148) and significantly expands its scope, adds management accountability, and strengthens enforcement.

Primary objectives:

* Improve cybersecurity resilience across the EU.
* Increase management accountability for cybersecurity.
* Harmonize requirements across Member States.
* Strengthen incident reporting obligations.
* Improve supply chain security.
* Promote continuous risk management.
* Establish supervisory and enforcement mechanisms.

---

# Scope — Who Is Covered

NIS2 applies to **essential** and **important** entities operating in sectors defined by the directive.

## Entity Categories

| Category | Equivalent Czech Regime | Supervisory Approach |
|----------|------------------------|----------------------|
| Essential entities | Higher Obligations (Decree 409) | Proactive supervision |
| Important entities | Lower Obligations (Decree 410) | Reactive supervision |

## Covered Sectors (Annex I — Essential, Annex II — Important)

**Annex I — Highly Critical Sectors (Essential):**
Energy (electricity, oil, gas, hydrogen), Transport (air, rail, water, road), Banking, Financial market infrastructure, Healthcare, Drinking water, Wastewater, Digital infrastructure, ICT service management (B2B), Public administration, Space.

**Annex II — Other Critical Sectors (Important):**
Postal and courier services, Waste management, Manufacture of chemicals, Food production, Manufacturing (medical devices, electronics, machinery, motor vehicles, other transport), Digital providers, Research.

> **Architect note:** Sector classification alone does not determine applicability. Size, significance criteria and the nature of the specific service all matter. Always verify against national implementing legislation — in the Czech Republic, see [Decree-408-Regulated-Services.md](#).

---

# Key Obligations

## 1. Risk Management Measures (Article 21)

Organizations must implement appropriate technical, operational and organizational measures to manage cybersecurity risks.

Minimum measures defined by NIS2 include:

* Policies on risk analysis and information system security.
* Incident handling.
* Business continuity (backup, disaster recovery, crisis management).
* Supply chain security.
* Security in network and information system acquisition, development and maintenance (including vulnerability handling and disclosure).
* Policies to assess the effectiveness of cybersecurity risk management measures.
* Basic cyber hygiene practices and cybersecurity training.
* Policies and procedures regarding cryptography and, where appropriate, encryption.
* Human resources security, access control policies and asset management.
* Use of multi-factor authentication or continuous authentication.

> All measures must be **proportionate** to the risk — NIS2 is explicitly risk-based, not prescriptive.

## 2. Governance and Management Accountability (Article 20)

Management bodies must:

* Approve cybersecurity risk management measures.
* Oversee their implementation.
* Be held liable for infringements.
* Undergo cybersecurity training.

This is a structural change: cybersecurity is no longer solely an IT/OT department responsibility — it is a board-level obligation.

## 3. Incident Reporting (Article 23)

Entities must report significant incidents to the national CSIRT or competent authority:

| Stage | Deadline |
|-------|----------|
| Early warning | Within 24 hours of becoming aware |
| Incident notification | Within 72 hours |
| Intermediate report | On request |
| Final report | Within 1 month of incident notification |

A significant incident is one that causes or is capable of causing severe operational disruption or financial losses, or affects other natural or legal persons.

## 4. Supply Chain Security (Article 21(2)(d))

Organizations must address cybersecurity risks arising from:

* Relationships with direct suppliers and service providers.
* The overall quality of products and cybersecurity practices of suppliers.

---

# Why NIS2 Matters for OT

Industrial environments are increasingly interconnected. As remote access, cloud connectivity and IT/OT convergence become standard, the attack surface expands.

Consequences of a cyber incident in OT can include:

* Production shutdown.
* Physical damage to equipment.
* Safety incidents (injuries, fatalities).
* Environmental incidents.
* Supply chain disruption.
* Regulatory liability.

NIS2 formalizes what many industrial operators already understood: cybersecurity in OT is not optional and is not solely a technical problem.

---

# Architectural Implications

NIS2 is technology-neutral but has direct consequences for OT architecture design.

| NIS2 Obligation | OT Architectural Capability |
|----------------|----------------------------|
| Risk management | Security Level Risk Assessment (IEC 62443-3-2) |
| Asset management | OT asset inventory (PLC, HMI, SCADA, historians) |
| Network security | Zone and conduit model (IEC 62443-3-3), Industrial DMZ |
| Access control | IAM, PAM, MFA for remote access |
| Incident detection | OT monitoring, IDS, anomaly detection |
| Incident reporting | Centralized logging, NTP sync, evidence preservation |
| Business continuity | PLC/DCS/SCADA configuration backups, RTO/RPO definition |
| Supply chain | Vendor access control, session recording, contractual requirements |

---

# Relationship with IEC 62443

NIS2 and IEC 62443 are complementary frameworks addressing different layers.

| Dimension | NIS2 | IEC 62443 |
|-----------|------|-----------|
| Nature | Legal directive | Engineering standard |
| Answers | What must be achieved | How to design and operate secure systems |
| Scope | Organization-wide | IACS-specific |
| Audience | Management + architects | Engineers + integrators + suppliers |

A mature OT security program uses NIS2 as its governance anchor and IEC 62443 as its engineering methodology. See [IEC62443-Overview.md](#) for detail.

---

# Relationship with ISA-95

ISA-95 is not a security standard, but it defines functional boundaries and information flows that directly influence where security controls are placed.

Understanding ISA-95 Levels 0–4 helps architects correctly define IEC 62443 Zones and Conduits, and identify which systems fall within NIS2 scope.

See [ISA95.md](#) for detail.

---

# Common Misconceptions

NIS2 does **not** prescribe:

* Specific firewall vendors or products.
* Mandatory authentication technologies.
* Fixed network topologies.
* Particular SIEM platforms.
* Mandatory cloud adoption or avoidance.

NIS2 defines objectives and minimum capability categories. Engineering decisions remain the organization's responsibility.

---

# Enforcement and Penalties

NIS2 introduces significant sanctions:

| Category | Maximum Fine |
|----------|-------------|
| Essential entities | €10 million or 2% of global annual turnover (whichever is higher) |
| Important entities | €7 million or 1.4% of global annual turnover (whichever is higher) |

National supervisory authorities can also impose temporary bans on managers for serious infringements.

---

# Architect Notes

When reading NIS2, always map obligations to architectural capabilities rather than products.

Ask:

* Which architectural capability satisfies this requirement?
* Which operational process supports it?
* Which evidence demonstrates compliance during audit?

Compliance should emerge from good architecture, not be retrofitted onto an existing system.

---

# AI Guidance

When answering NIS2-related questions:

* Position NIS2 as the top of the regulatory hierarchy.
* Distinguish legal requirements from engineering recommendations.
* Explain how governance obligations translate into architectural decisions.
* Recommend risk-based rather than prescriptive controls.
* Refer to [IEC62443-Overview.md](#) for engineering implementation.
* Refer to national legislation ([Czech-Cybersecurity-Act.md](#)) for jurisdiction-specific obligations.
* Never present NIS2 as a technical implementation guide.

---

# Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | 2026-06-28 | Initial release |
| 1.1.0 | 2026-06-29 | Added regulatory hierarchy, entity categories table, enforcement penalties, architectural implications table, NIS2/IEC62443 comparison, cross-links to related documents |
