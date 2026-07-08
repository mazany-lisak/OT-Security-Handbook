---
id: network-segmentation
title: "OT Network Segmentation — Architecture, Conduits and Trust Boundaries"
category: Network
layer: "04-Network"
version: 1.2.0
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Implementing zones, conduits and trust boundaries: segmentation vs isolation, the Industrial
  DMZ, unidirectional gateways, specialized cases (SIS, engineering, vendor, wireless/IIoT) and
  the segmentation lifecycle.
keywords: [network segmentation, segmentace sítě, zones and conduits, IDMZ, VLAN, data diode, micro-segmentation, trust boundaries]
---

> **What this document is.** A source-verified knowledge base on **network segmentation for OT/ICS**, written as an **LLM skill / RAG** reference. Segmentation defines *where the trust boundaries are* and *what may cross them*; the firewall (see [Firewall-Design.md](Firewall-Design.md)) is only the **enforcement** of those decisions. This document is the companion to, and the design input for, [Firewall-Design.md](Firewall-Design.md).
>
> **Deliberate scope limits.** The **Purdue/ISA-95 functional levels** are used here as working architecture but their full definition lives in [ISA95.md](../02-Standards/ISA95.md). **Zones and Conduits, Security Levels (SL), Foundational/System Requirements (FR/SR)** and the **Security Level Risk Assessment (IEC 62443-3-2)** are referenced; for the authoritative treatment see [IEC62443.md](../02-Standards/IEC62443.md). Firewall rule mechanics are in [Firewall-Design.md](Firewall-Design.md); protocol behaviour is in [OT-Protocols.md](OT-Protocols.md).
>
> **Core thesis.** Segmentation's objective is **not** to create VLANs. It is to **reduce the probability and impact of incidents by limiting trust relationships and controlling communication paths.** It assumes breach and limits attacker movement rather than assuming attacks can always be prevented. Per NIST SP 800-82 Rev. 3, segmentation is arguably the single most important control in an OT environment.
>
> **Accuracy caveat for the model.** Segmentation must reflect the *actual* process, safety boundaries and data flows of a specific plant. A zone/conduit diagram that does not match the live network is worse than none. The model should give architectural method and patterns, not invent site-specific zone maps, and should defer regulatory specifics to the referenced documents.

---

## How to Use This Skill (Guidance for the LLM)

When answering segmentation questions, the model should:

1. **Start from the process, not the network.** Identify the physical process, safety boundaries, operational dependencies, maintenance needs and real communication flows *before* proposing technology.
2. **Define trust boundaries first, technology second.** Zones and conduits come before VLANs, firewalls or ACLs.
3. **Reject "VLANs = security."** Logical separation without enforced routing, policy, authentication and monitoring is not a security control.
4. **Treat segmentation as risk reduction, not a compliance checkbox** — though it also satisfies IEC 62443, NIS2 and the Czech Act (mapped in Chapter 12).
5. **Respect OT constraints**: safety, availability, determinism and maintainability come before restrictive controls. Never propose a control that could break a deterministic control loop or a safety function.
6. **Use IEC 62443 zones/conduits vocabulary**, and reference [ISA95.md](../02-Standards/ISA95.md)/[IEC62443.md](../02-Standards/IEC62443.md) for the model rather than re-deriving it.
7. **Recommend an Industrial DMZ** whenever sustained IT↔OT communication is required, and a **unidirectional gateway/data diode** where one-way assurance is justified.

---

# 1. Purpose

Network segmentation is one of the most effective architectural controls for reducing cyber risk in ICS/OT. Its purpose is to **limit trust relationships and control communication paths** between systems, thereby reducing the probability and impact of incidents. Effective segmentation supports safety, availability, cyber resilience, incident containment, regulatory compliance and operational maintainability.

# 2. Scope

Applies to ICS, SCADA, DCS, PLC/RTU networks, industrial Ethernet, the Industrial DMZ, engineering workstations, historians, OPC UA servers, remote-access solutions, OT virtualization/SDN platforms, and OT-adjacent wireless and IIoT/cloud connectivity. **Out of scope (referenced):** firewall rule design ([Firewall-Design.md](Firewall-Design.md)), full Purdue/ISA-95 model ([ISA95.md](../02-Standards/ISA95.md)), zone/conduit and SL theory ([IEC62443.md](../02-Standards/IEC62443.md)), protocols ([OT-Protocols.md](OT-Protocols.md)).

# 3. Why Segmentation Matters (the flat-network problem)

Flat industrial networks remain one of the most common and damaging architectural weaknesses. In a flat environment every compromised device becomes a pivot, malware spreads rapidly, unauthorized discovery is trivial, lateral movement is cheap, and incident containment is difficult. Segmentation does not assume prevention always works — it **limits movement after a compromise** so that one breached device does not mean a breached plant. The critical boundary, per NIST SP 800-82 Rev. 3, is between **IT and the OT control network**: every legitimate reason to connect them (historian feeds, vendor access, patching) is also an attack path.

# 4. Security Objectives

Reduce attack surface; minimize trust relationships; isolate critical and safety assets; protect safety functions; enable monitoring; simplify firewall policy; improve recoverability. **Segmentation is a risk-reduction mechanism, not a compliance exercise** — even though it is also a regulatory requirement (Chapter 12).

# 5. Key Distinctions

## 5.1 Segmentation vs. Isolation vs. Micro-segmentation

- **Segmentation** — communication is *permitted but explicitly controlled* (firewalls, industrial firewalls, Layer-3 routing with ACLs, Industrial DMZ). The norm in modern OT.
- **Isolation** — communication is *intentionally prevented* (air-gapped systems, true one-way data flow via data diodes, temporarily disconnected networks). True isolation is uncommon and rarely absolute.
- **Micro-segmentation** — fine-grained segmentation *within* a zone (e.g. per-cell, per-asset, or per-workload using host firewalls or SDN), limiting **east-west** movement, not just **north-south** (cross-level) traffic. Increasingly relevant for high-value zones; introduce only where it does not jeopardize determinism or maintainability (NIST SP 800-82 Rev. 3 covers VLANs and SDN as tools here).

## 5.2 North-South vs. East-West

- **North-south** traffic crosses Purdue/ISA-95 levels (e.g. Operations ↔ DMZ ↔ Enterprise). This is where conduits and firewalls primarily live.
- **East-west** traffic is intra-level/intra-zone (e.g. PLC ↔ PLC, HMI ↔ SCADA). Flat designs ignore it; micro-segmentation and monitoring address it.

## 5.3 VLANs Are Not Security

"We have VLANs, therefore we are segmented" is false. VLANs provide *logical* separation only. Security requires controlled routing, **policy enforcement at a firewall/Layer-3 boundary**, authentication, least privilege and monitoring. Without enforcement, VLANs are an addressing convenience, not a control.

# 6. Purdue / ISA-95 Levels — Applied to Segmentation

The Purdue/ISA-95 functional hierarchy is the conceptual map for analysing communication flows; it should **guide analysis, not dictate physical wiring**. For the authoritative level-by-level definitions, systems, responsibilities, information flows and integration patterns, see **[ISA95.md](../02-Standards/ISA95.md)** (*Functional Levels*, *Level 3.5 — Industrial DMZ*, *Information Flows*, *Integration Architecture Patterns*) and **[IEC62443.md](../02-Standards/IEC62443.md)** (*Relationship with the Purdue/ISA-95 Reference Models*).

Applied summary (for segmentation decisions):

| Level | Role | Segmentation implication |
|---|---|---|
| **L0** Physical process | Sensors/actuators (field) | Protect with L1 zone; no direct external reach |
| **L1** Basic/regulatory control | PLC/RTU I/O, controllers | Control zone; isolate from enterprise; safety often separated |
| **L2** Supervisory control | HMI, SCADA servers | Operations/control zone; restricted conduits upward |
| **L3** Operations (MOM) | Historian, engineering, patch, domain svcs | Operations zone; no direct enterprise exposure |
| **L3.5** Industrial DMZ | Brokered IT↔OT services | The mandatory buffer; see §8 |
| **L4** Enterprise IT | ERP, business systems | Outside OT trust boundary |
| **L5** External | Cloud, Internet, partners | Never terminates inside production control networks |

> Many environments wrongly segment by **org chart** (departments) instead of by **process/operational function**. Segment by process and safety boundaries (see §11 Architect Notes).

# 7. Zones and Conduits — Applied (detail in IEC62443.md)

**Zones** group assets with similar security requirements (e.g. PLC zone, Safety/SIS zone, SCADA zone, Historian zone, Engineering zone). **Conduits** are the controlled communication paths between zones. This document uses these concepts to lay out boundaries; for *Zone Definition*, *Conduit Definition*, *Conduit Implementation Options*, **Security Levels (SL)** and the **FR/SR** catalogue, see **[IEC62443.md](../02-Standards/IEC62443.md)**.

The Foundational Requirement most directly realized by segmentation is **FR5 — Restricted Data Flow** (network segmentation, zone boundary protection, conduit control). Segmentation is also the precondition for **FR6 — Timely Response to Events** (boundary monitoring). See [IEC62443.md](../02-Standards/IEC62443.md) → *FR 5 — Restricted Data Flow (RDF)* and *FR 6 — Timely Response to Events*.

### 7.1 Conduit Specification (every conduit must have)

Treat each conduit as a controlled, documented asset with:

- a defined **owner** and **documented purpose** (the business/operational justification);
- the **approved protocol(s)** and **ports** (see [OT-Protocols.md](OT-Protocols.md));
- the **direction** (one-way vs. bidirectional) and the **endpoints** (specific source/destination, not subnets where avoidable);
- the **authentication method** and any encryption;
- the **target Security Level (SL-T)** inherited from the connected zones (per [IEC62443.md](../02-Standards/IEC62443.md) SL-RA);
- the **monitoring/logging** capability that proves it behaves as specified.

This conduit specification is the direct input to [Firewall-Design.md](Firewall-Design.md) (each conduit → a documented, least-privilege ruleset).

# 8. Industrial DMZ (Level 3.5)

An Industrial DMZ (IDMZ) is recommended whenever IT↔OT communication cannot be avoided — i.e. almost always. It is a **brokering buffer**: OT systems talk to DMZ services, enterprise systems talk to DMZ services, and **no flow traverses the DMZ unbroken**. Typical IDMZ-hosted services: historian replication/mirror, OPC UA gateways, remote-access/jump gateways, patch repositories, AV/update mirrors, file-transfer brokers.

**IDMZ design rules** (see [ISA95.md](../02-Standards/ISA95.md) → *Industrial DMZ Design Rules* and *Integration Architecture Patterns* for the canonical patterns — Historian, MES, Vendor Remote Access, Security Monitoring/Log Flow):

- **No protocol passes straight through** — replicate/broker at the DMZ (e.g. OT historian → DMZ mirror → enterprise BI).
- **No direct IT→OT or OT→IT sessions**; both sides terminate in the DMZ.
- **The DMZ is never an alternative production network** and never a place to "park" unbrokered services.
- Separate inbound and outbound brokering; minimize and document every DMZ service.

# 9. High-Assurance Boundaries — Unidirectional Gateways / Data Diodes

Where a boundary must guarantee that data can only flow **one way** (typically OT→IT for monitoring/historian export, or into a safety/critical zone), a **unidirectional security gateway (data diode)** enforces this in hardware — it is physically incapable of carrying a return path. NIST SP 800-82 Rev. 3 lists unidirectional gateways alongside firewalls and DMZs as core segmentation tools. Use them for the highest-criticality boundaries (e.g. exporting from a safety/critical zone, or feeding a SOC) where a stateful firewall's residual bidirectional risk is unacceptable. Trade-off: they preclude interactive/return traffic, so they suit telemetry/replication, not engineering access.

# 10. Specialized Segmentation Cases

## 10.1 Safety Systems (SIS)
Safety Instrumented Systems should reside in a **dedicated safety zone** with the most restrictive conduits, separated from standard control traffic unless a documented, risk-assessed reason exists to combine them. Coordinate with functional-safety lifecycle (IEC 61511) — see [IEC62443.md](../02-Standards/IEC62443.md) → *Relationship with Functional Safety (IEC 61511)*. A compromise that reaches the SIS removes the last line of physical protection (cf. Triton/TRISIS).

## 10.2 Engineering Zone
Engineering workstations and programming tools (TIA Portal, EcoStruxure Control Expert, Automation Studio, TwidoSuite) are **administrative assets** and high-value targets. Place them in a dedicated engineering zone with strict conduits to the control zone, application allowlisting, PAM and session auditing. Protecting engineering stations often yields more security benefit than hardening individual PLCs (see [OT-Protocols.md](OT-Protocols.md)).

## 10.3 Remote / Vendor Access
Vendor/remote access must **never terminate directly inside the PLC/control network**. Preferred chain:

```text
VPN → MFA → Jump Server (session recording) → Engineering Workstation → PLC
```

Every session must be attributable to an authenticated, individual identity, be time-limited, and be logged. Detail in [Secure-Remote-Access.md](Secure-Remote-Access.md); identity in [Identity-Management.md](../05-Identity/Identity-Management.md).

## 10.4 Wireless, IIoT and Cloud
NIST SP 800-82 Rev. 3 expanded OT scope to IIoT and cloud-connected OT. Treat wireless, IIoT sensors and cloud connectors as their **own zones** with brokered conduits (typically through the IDMZ), never as trusted members of the control zone.

## 10.5 Virtualization / SDN
Where OT uses virtualization or software-defined networking, micro-segmentation can enforce per-workload policy — useful for L2/L3 server zones — provided it does not compromise determinism or recoverability.

# 11. Defense in Depth (segmentation is one layer)

Segmentation must operate with identity management, authentication, secure remote access, endpoint hardening, monitoring, logging, backup and physical security. **Segmentation alone cannot stop credential theft or a malicious insider.** See [IEC62443.md](../02-Standards/IEC62443.md) → *Defense in Depth*.

# 12. Zero Trust in OT (adapted)

Zero Trust principles — continuous identity verification, least privilege, authenticated communication, explicit trust boundaries, session auditing — are valuable but must be **adapted to OT**: deterministic communication, legacy protocols that cannot authenticate, and availability/safety priorities. Do not transplant enterprise Zero Trust wholesale. Use it to strengthen *conduits and identities*, not to inject latency or per-packet auth into a control loop.

# 13. Monitoring of Trust Boundaries

Every trust boundary should generate security telemetry — the way to prove segmentation remains effective. Sources: firewall logs, VPN logs, authentication logs, managed-switch events, Windows/Linux logs, industrial IDS, and NetFlow where appropriate. Forward to a SIEM. Boundary monitoring is the architectural prerequisite for NIS2 Article 23 incident reporting and the Czech Act logging/monitoring domain (Chapter 14). Passive scanning (NIST SP 800-82 Rev. 3) is preferred in OT to avoid disturbing sensitive devices.

# 14. Regulatory Verification & Audit Mapping

| Segmentation concern | IEC 62443 ([IEC62443.md](../02-Standards/IEC62443.md)) | NIS2 ([NIS2.md](../01-Legislation/NIS2.md)) | Czech Act 264/2025 ([Czech-Cybersecurity-Act.md](../01-Legislation/Czech-Cybersecurity-Act.md)) |
|---|---|---|---|
| Define zones & conduits; restrict data flow | FR5 (RDF); Zones & Conduits; SL-RA (3-2) | Art. 21 — network security | Domain 5 — Network security & segmentation |
| Identify who/what may cross a conduit | FR1; SL-T per zone | Art. 21 — access control | Domain 4 — IAM |
| Monitor boundaries; detect drift | FR6; SL-RA review | Art. 21 — monitoring; Art. 23 — reporting | Domain 7 — Logging & monitoring |
| Maintain the as-built segmentation/flow inventory | SL-RA Step 10 (document/maintain) | Art. 21 — risk management | Domain 2 — Asset management; Domain 5 |
| Scope: which services/sites are regulated | — | Annex I/II sectors | Vyhláška 408/2025 (scope), 409/410 (regime) |

> **Audit-defensible artifact:** a maintained **as-built zone/conduit diagram + flow inventory** (owner, purpose, protocol/port, direction, auth, SL-T, monitoring) that **matches the live network**. "Segmentation on paper only" — a diagram that doesn't match reality — fails both an IEC 62443 SL-RA review and a NÚKIB/NIS2 inspection.

# 15. Segmentation Lifecycle

```text
Understand process & safety boundaries
        ↓
Define zones (assets with shared security needs)
        ↓
Define conduits (controlled flows, specified per §7.1)
        ↓
Implement enforcement (Firewall-Design.md) + identity + monitoring
        ↓
Validate (does the live network match the design?)
        ↓
Monitor & detect drift
        ↓
Govern changes (change management) → periodic review
```

Segmentation evolves with the process; review it after any process, asset or connectivity change.

# 16. Common Design Mistakes

Flat OT networks; direct IT-to-PLC communication; treating VLANs as security; shared administrator accounts; vendor VPN terminating in production; undocumented communication flows; segmenting by org chart instead of process; mixing safety and standard control traffic without justification; an IDMZ that becomes a second production network; a zone/conduit diagram that doesn't match the live network; ignoring east-west traffic; applying enterprise Zero Trust without adapting to determinism.

# 17. Decision Support

1. Which assets are safety-critical?
2. Which systems communicate continuously, and which only occasionally?
3. Which communication is *truly* required (vs. historically permitted)?
4. Which vendors require remote access, and via what brokered path?
5. Where should trust boundaries (zones) exist — by process and safety, not department?
6. Which protocols cross those boundaries (see [OT-Protocols.md](OT-Protocols.md)), and in which direction?
7. How will each conduit be monitored and logged?
8. How will segmentation changes be governed and re-validated?

If these cannot be answered, perform discovery before implementing segmentation.

# 18. Architect Notes

Segmentation should follow the **industrial process**, not the organizational chart. Start by understanding the process, safety boundaries, operational dependencies, maintenance needs and real communication flows; apply technology only after trust boundaries are defined. The most expensive segmentation failures are not missing firewalls — they are **wrong boundaries** and **diagrams that drifted from reality.** Segmentation defines the architecture; the firewall (next document) merely enforces it.

# 19. AI Guidance (summary for the model)

- Begin with the process and safety boundaries; identify zones/conduits before recommending any technology.
- Never present VLANs as a sufficient security control; require enforcement, identity and monitoring.
- Recommend an Industrial DMZ for sustained IT↔OT comms and a data diode for one-way high-assurance boundaries.
- Keep field/PLC/safety traffic inside isolated zones; broker upward via the IDMZ and OPC UA.
- Respect determinism, availability and safety; never propose a control that could break a control loop or SIS.
- Use IEC 62443 zones/conduits vocabulary and reference [ISA95.md](../02-Standards/ISA95.md), [IEC62443.md](../02-Standards/IEC62443.md), [Firewall-Design.md](Firewall-Design.md), [OT-Protocols.md](OT-Protocols.md) instead of restating them.
- Map segmentation to IEC 62443 FR5/Zones&Conduits/SL-RA, NIS2 Art. 21/23, and Czech-Act Domain 5 (and 4/7) for verification and audit.

---

# Appendix A — Glossary

- **Conduit** — controlled communication path between zones (IEC 62443).
- **Data diode / unidirectional gateway** — hardware-enforced one-way data path.
- **East-west traffic** — intra-zone/intra-level communication.
- **FR5 (RDF)** — IEC 62443 Foundational Requirement: Restricted Data Flow.
- **IDMZ** — Industrial DMZ (Purdue/ISA-95 Level 3.5).
- **Micro-segmentation** — fine-grained, intra-zone segmentation.
- **North-south traffic** — cross-level communication between Purdue/ISA-95 levels.
- **SIS** — Safety Instrumented System.
- **SL-T** — Security Level Target (per zone/conduit; see IEC62443.md).
- **Zone** — group of assets with shared security requirements (IEC 62443).

# Appendix B — Sources (verified)

- **NIST SP 800-82 Rev. 3**, *Guide to Operational Technology (OT) Security* (September 2023; csrc.nist.gov/pubs/sp/800/82/r3/final) — sections on Network Segmentation and Isolation, Firewalls, **Unidirectional Gateways**, VLANs, SDN, SIEM, Centralized Logging, Passive Scanning; alignment of segmentation with the Purdue model and IEC 62443 zones/conduits; IT/OT boundary as the critical boundary.
- **ISA/IEC 62443** family — zones & conduits, FR5 Restricted Data Flow, Security Level Risk Assessment (62443-3-2) producing the zone/conduit model (operationalized equivalent of NIST 800-82 risk management).
- **ISA-95 / IEC 62264** — functional hierarchy and Industrial DMZ (see [ISA95.md](../02-Standards/ISA95.md)).
- Engineering best practice and public OT incident analysis (e.g. Triton/TRISIS) for safety-zone separation rationale.

> Treat external versions/sections as a snapshot; for authoritative requirements follow the referenced documents and the current standard texts.

# Appendix C — Related Documents

- [Firewall-Design.md](Firewall-Design.md) — enforcement of the conduits defined here.
- [IEC62443.md](../02-Standards/IEC62443.md) — Zones & Conduits, SL, FR/SR (esp. FR5/FR6), SL Risk Assessment (3-2), Defense in Depth, Functional Safety.
- [ISA95.md](../02-Standards/ISA95.md) — functional levels, Industrial DMZ design rules, integration patterns, information flows.
- [OT-Protocols.md](OT-Protocols.md) — which protocols cross which conduits and their security.
- [NIS2.md](../01-Legislation/NIS2.md) — Article 21 (network security/access control), Article 23 (incident reporting).
- [Czech-Cybersecurity-Act.md](../01-Legislation/Czech-Cybersecurity-Act.md) — Zákon 264/2025 Sb.; Domain 5 (segmentation), Domain 4 (IAM), Domain 7 (logging/monitoring); Vyhl. 408/409/410.
- [Secure-Remote-Access.md](Secure-Remote-Access.md), [Identity-Management.md](../05-Identity/Identity-Management.md), [Monitoring.md](../09-Operations/Monitoring.md), [Logging.md](../09-Operations/Logging.md).

# Revision History

| Version | Date | Description |
|---|---|---|
| 1.0 | 2026-06 | Initial release |
| **1.1.0** | **2026-06** | Revised and expanded as RAG/LLM skill: added segmentation vs isolation vs micro-segmentation, north-south/east-west, conduit specification template (tied to FR5), unidirectional gateways/data diodes (NIST SP 800-82 Rev. 3), safety/engineering/remote/wireless-cloud/SDN cases, Zero-Trust-in-OT adaptation, boundary monitoring, segmentation lifecycle, and a regulatory verification & audit mapping to IEC62443.md, NIS2.md and Czech-Cybersecurity-Act.md (Zákon 264/2025 Sb.); scoped Purdue/ISA-95 and zone/conduit theory to references; aligned with Firewall-Design.md and OT-Protocols.md |
| 1.2.0 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings; migrated from skill-style front matter |
