---
id: firewall-design
title: "OT Firewall Design — Enforcing Conduits in Industrial Control Systems"
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
  Enforcing conduits with OT firewalls: choosing the enforcement device (NGFW, industrial DPI,
  host-based, data diode), typical placements, default-deny rule design, industrial DPI, logging,
  HA fail behaviour and policy lifecycle.
keywords: [firewall, industrial firewall, default deny, DPI, conduit enforcement, rule design, pravidla firewallu]
---

> **What this document is.** A source-verified knowledge base on **firewall design for OT/ICS**, written as an **LLM skill / RAG** reference. A firewall is **not the architecture** — it is the *technical enforcement* of conduits defined during risk assessment and segmentation. This document is the enforcement companion to [Network-Segmentation.md](Network-Segmentation.md) (which defines *where* the boundaries are and *what* may cross them).
>
> **Deliberate scope limits.** *Where* trust boundaries go is decided in [Network-Segmentation.md](Network-Segmentation.md). **Zones & Conduits, Security Levels (SL) and the Security Level Risk Assessment (IEC 62443-3-2)** are referenced; see [IEC62443.md](../02-Standards/IEC62443.md). **Protocol semantics and DPI specifics** (function codes, ports, secure variants) are in [OT-Protocols.md](OT-Protocols.md). Vendor-specific firewall configuration is intentionally excluded.
>
> **Core thesis.** *Architecture precedes technology.* A firewall enforces a communication policy; if the policy is wrong, incomplete or overly permissive, the firewall simply enforces a poor architecture faithfully. The design order is always **Risk Assessment → Zones → Conduits → Firewall Policy → Monitoring → Review.**
>
> **Accuracy caveat for the model.** Firewall capabilities (DPI protocol coverage, HA modes, throughput, fail behaviour) are product- and firmware-specific. The model should give method and OT-specific design principles, not invent vendor features or rule syntax, and should defer regulatory specifics to the referenced documents.

---

## How to Use This Skill (Guidance for the LLM)

When answering firewall questions, the model should:

1. **Begin with zones and conduits, not rules.** Ask what boundary is being protected and what conduit specification it must enforce (from [Network-Segmentation.md](Network-Segmentation.md)).
2. **Default deny + explicit allow.** Communication is approved, never implicitly permitted; every rule is documented, justified, minimal, reviewable and auditable.
3. **Map one conduit → one documented ruleset.** A firewall rule is *documentation of a business requirement*, not just configuration.
4. **Distinguish Layer-3/4 filtering from industrial DPI** (protocol-aware inspection), and reference [OT-Protocols.md](OT-Protocols.md) for protocol detail rather than restating it.
5. **Respect OT constraints**: latency, determinism, availability and safety. A security control must never break a control loop, a deterministic bus (PROFINET IRT, POWERLINK) or a safety function.
6. **Treat the firewall as one layer of Defense in Depth**, never the primary or sole control.
7. **Recommend the right device for the boundary** — enterprise NGFW, industrial DPI firewall, host-based firewall, or a unidirectional gateway/data diode — and reference [Network-Segmentation.md](Network-Segmentation.md)/NIST SP 800-82 Rev. 3.

---

# 1. Purpose

Industrial firewalls are a primary technical control for enforcing communication policy between security zones in an IACS. A firewall should **never be considered the architecture itself** — it is the implementation of decisions made during risk assessment, zone definition, conduit definition and security-policy design. Well-designed policies reduce attack surface while preserving deterministic industrial communication.

# 2. Scope

Covers firewall architecture and placement, rule design, policy management, deep packet inspection (DPI), logging, monitoring, high availability, change management and OT-specific operational considerations. **Out of scope (referenced):** where boundaries belong ([Network-Segmentation.md](Network-Segmentation.md)), zone/conduit/SL theory ([IEC62443.md](../02-Standards/IEC62443.md)), protocol detail ([OT-Protocols.md](OT-Protocols.md)), and vendor configuration.

# 3. Security Philosophy

The most common misconception is *"installing a firewall makes the network secure."* It does not. A firewall **enforces a communication policy**; an incorrect or permissive policy is simply enforced faithfully. **Architecture precedes technology** — the firewall enforces architecture, it does not create it.

# 4. Firewall Objectives

Enforce trust boundaries; reduce attack surface; prevent unauthorized communication; support monitoring and auditability; simplify incident response; enforce least privilege. The firewall must not become a substitute for proper network architecture ([Network-Segmentation.md](Network-Segmentation.md)).

# 5. Relationship with Zones and Conduits

**Firewalls enforce conduits; they do not define them.** The design sequence is:

```text
Risk Assessment (IEC 62443-3-2 SL-RA)
        ↓
Security Zones
        ↓
Conduits (specified: owner, purpose, protocol/port, direction, auth, SL-T, monitoring)
        ↓
Firewall Policies  (one conduit → one documented ruleset)
        ↓
Monitoring
        ↓
Continuous Review
```

Designing rules before defining conduits produces inconsistent security. The conduit specification (see [Network-Segmentation.md](Network-Segmentation.md) §7.1) is the direct input to each ruleset. Firewall enforcement realizes IEC 62443 **FR5 — Restricted Data Flow** (and supports **FR1** access, **FR6** monitoring); see [IEC62443.md](../02-Standards/IEC62443.md).

# 6. Choosing the Right Enforcement Device

Different boundaries call for different devices (NIST SP 800-82 Rev. 3 treats firewalls, unidirectional gateways and DMZs as complementary):

| Device | Best for | Notes |
|---|---|---|
| **Enterprise NGFW** | Enterprise ↔ IDMZ (north of L3.5) | App-ID/IPS/URL filtering; usually *not* OT-protocol aware |
| **Industrial / DPI firewall** | IDMZ ↔ Operations, Engineering ↔ Control, Safety ↔ Control | OT-protocol aware (Modbus, S7comm, PROFINET, EtherNet/IP, DNP3); ruggedized; often transparent/"bump-in-the-wire" |
| **Host-based firewall** | Micro-segmentation within a zone (servers, EWS) | Complements network firewalls; east-west control |
| **Unidirectional gateway / data diode** | One-way high-assurance boundary (export from safety/critical zone; SOC feed) | Hardware-enforced one-way; precludes return traffic |

Avoid consumer-grade firewalls in critical industrial environments. Avoid using an IT NGFW as the sole control deep in the OT network if it cannot inspect OT protocols.

# 7. Typical Firewall Placements

- **Enterprise ↔ Industrial DMZ** — protects business networks from OT and vice versa; the IT/OT boundary is the critical one (NIST SP 800-82 Rev. 3).
- **Industrial DMZ ↔ Operations** — both sides terminate in the DMZ; nothing passes straight through (see [Network-Segmentation.md](Network-Segmentation.md) §8 and [ISA95.md](../02-Standards/ISA95.md) Industrial DMZ design rules).
- **Engineering Zone ↔ Control Zone** — restrict programming/engineering protocols to named stations.
- **Safety Zone ↔ Control Zone** — protect the SIS from unintended communication.
- **Vendor Access Gateway** — control and record remote maintenance sessions.
- **External Connectivity** — Internet connectivity must never terminate directly inside production control networks.

# 8. Rule Design Principles

Every rule must be **explicit, documented, justified, minimal, reviewable and auditable**. Avoid generic rules; each rule has a documented operational purpose.

## 8.1 Least Privilege
Permit only the required **source**, **destination**, **protocol**, **port**, **time window**, and **user/identity where applicable** — deny everything else.

## 8.2 Default Deny
Industrial firewalls follow **default deny**: communication is explicitly approved, not implicitly permitted. Exceptions are documented and periodically reviewed.

## 8.3 Conduit-to-Rule Mapping
Each conduit defined in segmentation becomes one **named, documented ruleset** with a clear direction. Bidirectional needs should be justified; prefer the narrowest direction. Tie each rule back to its conduit ID and owner for audit.

## 8.4 Specificity
Prefer specific host endpoints over subnets where practical; avoid `any` in source, destination, service or port. Order rules to avoid shadowing; remove duplicates and unused rules.

# 9. Industrial Protocol Awareness & Deep Packet Inspection (DPI)

Modern industrial firewalls inspect **protocol semantics**, not just Layer-3/4 headers. Typical OT protocol awareness: OPC UA, Modbus TCP, PROFINET, EtherNet/IP, DNP3, S7 Communication (see [OT-Protocols.md](OT-Protocols.md) for protocol-by-protocol detail and ports). DPI capabilities include **function-code validation**, protocol anomaly detection, **unauthorized write-request blocking**, restriction of engineering/programming operations, and protocol-compliance verification.

DPI provides far greater visibility than port filtering — but it **must be validated for latency and device compatibility**, and should **not** be inserted into hard-real-time segments (PROFINET IRT, POWERLINK) where it could break determinism. Place DPI at north-south conduits, not inside deterministic field networks.

# 10. Logging

Every firewall must generate security logs and forward them to centralized logging/SIEM. Recommended events: **allowed** connections (for baseline), **denied** connections, rule changes, administrative logins, firmware updates, VPN sessions, certificate failures, and high-severity protocol/DPI events. Firewall logs are an architectural prerequisite for NIS2 Article 23 timely incident reporting and the Czech Act logging/monitoring domain (Chapter 16).

# 11. Monitoring & Rule Hygiene

Monitor for policy violations, unexpected communication, new endpoints, **configuration drift**, high connection rates, failed VPN authentication, rule modifications and threat-detection alerts. Monitoring validates that policies still reflect operational reality. Maintain **rule hygiene**: periodically review for shadowed/duplicated/overly-broad/expired rules and undocumented exceptions; reconcile the live ruleset against the conduit inventory in [Network-Segmentation.md](Network-Segmentation.md).

# 12. High Availability and OT Fail Behaviour

Firewall availability can be as important as the security it provides. Consider redundant firewall pairs, automatic failover, state synchronization, defined maintenance procedures, configuration backup and tested recovery. 

**OT-specific decision — fail-open vs. fail-closed.** Unlike IT, OT must *consciously* decide what a firewall does on failure: fail-closed maximizes security but can interrupt a process or safety-relevant flow; fail-open preserves availability but drops enforcement. This is a **risk- and safety-based decision** made with process and safety engineers — never a silent default. Define availability and fail behaviour during architecture design, not after an outage.

# 13. Performance Considerations

OT environments may require low latency, deterministic communication, high throughput and redundant paths. Validate firewall performance (and DPI overhead) under realistic operational conditions. **Security controls must not compromise process safety or determinism.** Transparent/bump-in-the-wire deployment can ease insertion into existing flat segments during migration.

# 14. Change Management

Firewall rules follow formal change management: business justification, risk assessment, testing, approval, documentation and a rollback plan. **Temporary rules must carry an expiration date** and be removed when no longer required. Uncontrolled rule accumulation ("rule sprawl") is a leading cause of OT firewall failures and audit findings.

# 15. Firewall as a Hardened Asset

The firewall is itself a high-value target and must be hardened: restrict and authenticate the management plane (separate management network/VLAN, MFA where supported), disable unused services, keep firmware current via change control, back up and version configurations, and monitor administrative access. A compromised enforcement device defeats every conduit it protects.

# 16. Regulatory Verification & Audit Mapping

| Firewall concern | IEC 62443 ([IEC62443.md](../02-Standards/IEC62443.md)) | NIS2 ([NIS2.md](../01-Legislation/NIS2.md)) | Czech Act 264/2025 ([Czech-Cybersecurity-Act.md](../01-Legislation/Czech-Cybersecurity-Act.md)) |
|---|---|---|---|
| Enforce conduits / restrict data flow | FR5 (RDF); Conduits; Defense in Depth | Art. 21 — network security | Domain 5 — Network security & segmentation |
| Least-privilege access across boundaries | FR1; SL-T per conduit | Art. 21 — access control | Domain 4 — IAM |
| Log allow/deny + rule changes to SIEM | FR6 (Timely Response) | Art. 21 — monitoring; Art. 23 — reporting | Domain 7 — Logging & monitoring |
| Change management & rule review | Secure Lifecycle (Operation/Modification) | Art. 21 — risk management | Domain 5 + Domain 1 (Governance) |
| HA / recovery of enforcement devices | FR7 (Resource Availability) | Art. 21 — business continuity | Domain 12 — Business continuity |

> **Audit-defensible artifact:** a **rule base that maps 1:1 to the documented conduits**, each rule justified, owned, dated and reviewed, with logging to the SIEM. "Any→Any" rules, undocumented exceptions and rules with no owner are classic IEC 62443 SL-RA and NÚKIB/NIS2 inspection findings.

# 17. Firewall Policy Lifecycle

```text
Business Requirement → Risk Assessment → Zone Definition → Conduit Definition →
Firewall Rule Design → Testing → Deployment → Monitoring → Periodic Review →
Removal (when no longer required)
```

Policies evolve with the industrial process; review after any process, asset or connectivity change.

# 18. Relationship with IEC 62443 (referenced)

Firewall deployment supports IEC 62443 **Zones and Conduits**, **FR5 Restricted Data Flow**, **Least Privilege**, **Defense in Depth**, **Secure Communications**, **FR6 monitoring** and **FR1 access control**. IEC 62443 deliberately does **not** prescribe firewall vendors or products. See [IEC62443.md](../02-Standards/IEC62443.md) for the requirements; this document covers how to *implement* them.

# 19. Common Design Mistakes

`Any → Any` rules; excessive rule complexity; undocumented exceptions; no periodic review; placing a firewall inside a flat network without real segmentation; permanent vendor access; missing logging; transplanting IT policy models without OT adaptation; using consumer-grade firewalls in critical environments; inserting DPI into hard-real-time segments; treating the firewall as the primary/sole control; no defined fail behaviour; temporary rules that never expire.

# 20. Decision Support

1. Which trust boundary requires protection (from [Network-Segmentation.md](Network-Segmentation.md))?
2. Which communication is operationally required — and can it be reduced?
3. Which industrial protocols cross the boundary (see [OT-Protocols.md](OT-Protocols.md))?
4. Is DPI required, supported, and safe for the segment's latency budget?
5. What are the availability requirements and the chosen fail behaviour?
6. How will policy changes be approved, tested and rolled back?
7. How will the firewall (and its logs) be monitored?
8. Is the boundary better served by a data diode than a stateful firewall?

# 21. Architect Notes

A frequent architectural failure is treating the firewall as the primary security control. In mature OT architectures it is one layer among many, working with network segmentation, identity management, secure remote access, asset management, monitoring, logging and change management. **The firewall enforces architecture — it does not create it.** The quality of a firewall deployment is determined upstream, in the segmentation and risk-assessment work, not in the rule editor.

# 22. AI Guidance (summary for the model)

- Start with zones and conduits (from [Network-Segmentation.md](Network-Segmentation.md)), not firewall rules.
- Recommend **default deny + explicit, documented, least-privilege allow**, one ruleset per conduit.
- Explain the difference between Layer-3/4 filtering and **industrial DPI**, and reference [OT-Protocols.md](OT-Protocols.md) for protocol specifics.
- Match the device to the boundary (NGFW vs industrial DPI firewall vs host-based vs data diode).
- Respect latency, determinism, availability and safety; make **fail-open/closed** a conscious, risk-based decision.
- Treat firewall rules as documentation of business requirements; require change management, expiry on temporary rules, logging to SIEM, and periodic rule hygiene.
- Treat the firewall as a hardened asset and as **one layer** of Defense in Depth.
- Map enforcement to IEC 62443 FR5/Conduits/Defense-in-Depth, NIS2 Art. 21/23, and Czech-Act Domain 5 (and 4/7/12) for verification and audit.

---

# Appendix A — Glossary

- **Bump-in-the-wire / transparent firewall** — Layer-2 inline firewall requiring no IP re-addressing; eases insertion into existing segments.
- **Conduit** — controlled communication path between zones (IEC 62443); enforced by firewall rules.
- **Data diode / unidirectional gateway** — hardware-enforced one-way data path.
- **Default deny** — implicit deny of all traffic not explicitly allowed.
- **DPI** — Deep Packet Inspection (protocol-aware).
- **Fail-open / fail-closed** — firewall behaviour on failure: pass traffic vs. block traffic.
- **FR5 (RDF)** — IEC 62443 Foundational Requirement: Restricted Data Flow.
- **NGFW** — Next-Generation Firewall (enterprise; app/IPS aware, usually not OT-protocol aware).
- **Rule sprawl** — uncontrolled accumulation of firewall rules over time.
- **SL-T** — Security Level Target (per zone/conduit; see IEC62443.md).

# Appendix B — Sources (verified)

- **NIST SP 800-82 Rev. 3**, *Guide to Operational Technology (OT) Security* (September 2023; csrc.nist.gov/pubs/sp/800/82/r3/final) — firewalls, unidirectional gateways and DMZs as complementary segmentation tools; industrial-grade firewalls that understand OT protocols (Modbus, DNP3, EtherNet/IP) enforcing least privilege at zone boundaries; IT/OT boundary as critical.
- **ISA/IEC 62443** family — conduits, FR5 Restricted Data Flow, Defense in Depth, Security Level Risk Assessment (62443-3-2); standard intentionally vendor-neutral on firewall products.
- Engineering best practice for OT firewall HA, fail behaviour and DPI latency constraints.

> Treat external versions/sections as a snapshot; for authoritative requirements follow the referenced documents and current standard texts.

# Appendix C — Related Documents

- [Network-Segmentation.md](Network-Segmentation.md) — defines the zones/conduits this document enforces.
- [IEC62443.md](../02-Standards/IEC62443.md) — Zones & Conduits, SL, FR/SR (esp. FR5/FR6/FR7), SL Risk Assessment (3-2), Defense in Depth, Secure Lifecycle.
- [OT-Protocols.md](OT-Protocols.md) — protocol semantics, ports and DPI considerations.
- [ISA95.md](../02-Standards/ISA95.md) — Industrial DMZ design rules and integration patterns.
- [NIS2.md](../01-Legislation/NIS2.md) — Article 21 (network security/access control), Article 23 (incident reporting).
- [Czech-Cybersecurity-Act.md](../01-Legislation/Czech-Cybersecurity-Act.md) — Zákon 264/2025 Sb.; Domain 5 (segmentation), Domain 4 (IAM), Domain 7 (logging/monitoring), Domain 12 (business continuity); Vyhl. 408/409/410.
- [Secure-Remote-Access.md](Secure-Remote-Access.md), [Identity-Management.md](../05-Identity/Identity-Management.md), [Monitoring.md](../09-Operations/Monitoring.md), [Logging.md](../09-Operations/Logging.md).

# Revision History

| Version | Date | Description |
|---|---|---|
| 1.0 | 2026-06 | Initial production release |
| **1.1.0** | **2026-06** | Revised and expanded as RAG/LLM skill: added enforcement-device selection (NGFW vs industrial DPI firewall vs host-based vs data diode), conduit-to-rule mapping, rule hygiene/anti-sprawl, OT fail-open/closed decision, firewall-as-hardened-asset, expanded DPI/latency guidance referencing OT-Protocols.md, and a regulatory verification & audit mapping to IEC62443.md, NIS2.md and Czech-Cybersecurity-Act.md (Zákon 264/2025 Sb.); scoped zone/conduit/SL theory to references; aligned with Network-Segmentation.md (NIST SP 800-82 Rev. 3 grounding) |
| 1.2.0 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings; migrated from skill-style front matter |
