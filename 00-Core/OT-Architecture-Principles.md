---
title: OT Architecture Principles
category: Core
version: 1.0.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-07-01
review_cycle: Annual
---

# Purpose

This document defines the **architectural principles** for designing secure Operational Technology (OT) systems.

It sits between the *mindset* ([OT-Security-Philosophy.md](OT-Security-Philosophy.md)) and the *implementation* documents ([Network-Segmentation.md](Network-Segmentation.md), [Firewall-Design.md](Firewall-Design.md), [Identity-Management.md](Identity-Management.md)). The philosophy explains **why**; this document states the **principles** an architecture must embody; the implementation documents explain **how**; and the [Security-Decision-Framework.md](Security-Decision-Framework.md) sequences the decisions.

These principles are technology- and vendor-neutral and apply throughout the system lifecycle ([OT-Lifecycle.md](OT-Lifecycle.md)).

---

# Position in the Core Layer

```
OT-Security-Philosophy        → how to think (mindset & priorities)
OT-Architecture-Principles    → what a good OT architecture embodies (this document)
Security-Decision-Framework   → how to sequence the decisions
Risk-Management-Principles    → how to weigh risk
OT-Lifecycle                  → how it evolves over time
```

The principles below are the criteria an architecture is judged against, regardless of the products chosen to realise it.

---

# The Principles

## 1. Architecture Before Technology

Understand the process, assets, constraints and risks before selecting any product. Technology is the outcome of architecture, never its starting point. (See [Security-Decision-Framework.md](Security-Decision-Framework.md).)

## 2. Safety and Availability First

Design decisions follow the canonical OT priority hierarchy — **Safety → Availability → Integrity → Confidentiality** ([OT-Security-Philosophy.md](OT-Security-Philosophy.md)). A control must never compromise a safety function or a deterministic control loop.

## 3. Risk-Based Design

Apply controls in proportion to risk; there is no single "secure architecture" for every site. Consequence is weighed in safety and operational terms, not just financial terms. (See [Risk-Management-Principles.md](Risk-Management-Principles.md).)

## 4. Explicit Trust Boundaries

Trust is never implicit. Group assets by shared security requirements into **zones**, and permit only defined, documented **conduits** between them. The concepts are owned by [IEC62443.md](IEC62443.md); the "where" is modelled by [Purdue-Model.md](Purdue-Model.md); the implementation is in [Network-Segmentation.md](Network-Segmentation.md).

## 5. Defense in Depth

Combine multiple independent layers (physical, network, identity, remote access, monitoring, backup, procedures, awareness). The failure of one layer must not compromise the whole. No single control — including the firewall — is the architecture.

## 6. Least Privilege

Grant the minimum access, connectivity and functionality required for the operational task — per user, per host, per protocol, per conduit — and deny the rest by default.

## 7. Identity Before Connectivity

Establish *who* (or *what*) before granting a path. Connectivity does not imply authorisation; a permanent tunnel is not permanent access. (See [Identity-Management.md](Identity-Management.md), [Secure-Remote-Access.md](Secure-Remote-Access.md).)

## 8. Secure by Design and by Default

Security is designed in from concept, not added at commissioning. Delivered systems start from a hardened, least-functionality default state rather than an open one.

## 9. Minimize the Attack Surface (Simplicity)

Prefer the simplest architecture that meets the requirement. Every extra service, port, protocol, account and component is attack surface and maintenance burden. Simplicity is a security property.

## 10. Segment, Don't Flatten

Flat OT networks are a primary weakness. Segment by process and safety function so that a single compromise cannot traverse the plant. VLANs alone are not segmentation — enforcement, identity and monitoring are required. (See [Network-Segmentation.md](Network-Segmentation.md).)

## 11. Design for Recoverability

Assume failure and compromise will occur. Ensure configuration, firmware, credentials and data can be restored to a *trusted* operational state, and that recovery is tested — not assumed. (See [Backup-and-Recovery.md](Backup-and-Recovery.md), [Recovery-Testing.md](Recovery-Testing.md).)

## 12. Design for Observability

An architecture must be monitorable. Every trust boundary should produce security telemetry to a central point (SIEM), enabling detection, response and proof that controls remain effective. (See [Monitoring.md](Monitoring.md), [Logging.md](Logging.md).)

## 13. Design for the Full Lifecycle

IACS run for decades. Favour designs that can be operated, maintained, patched, audited and modernised over 15–30 years, and that accommodate **compensating controls** for legacy components that cannot meet modern requirements. (See [OT-Lifecycle.md](OT-Lifecycle.md).)

## 14. Manageability and Maintainability

A control that cannot be sustained is not a control. Prefer architectures the organisation can actually operate, document and change under formal change management. (See [Change-Management.md](Change-Management.md).)

## 15. Vendor Neutrality

Design to **capabilities**, not products (e.g. "industrial firewall with protocol-aware inspection", not a specific brand). Products are chosen last, to realise the architecture.

---

# How the Principles Are Applied

These principles are the design criteria used inside **Step 6 (Design the Architecture)** of the [Security-Decision-Framework.md](Security-Decision-Framework.md), and the acceptance criteria used at **Step 8 (Validate)**. During the lifecycle they are applied at the **Architecture** phase and re-checked at every **Modernization** ([OT-Lifecycle.md](OT-Lifecycle.md)). When principles conflict (e.g. availability vs. least privilege), resolve using the priority hierarchy and a documented, risk-based decision.

---

# Common Anti-Patterns

Avoid:

* Starting from a product or an enterprise-IT reference design.
* Flat networks with implicit trust; VLANs treated as security.
* The firewall (or any single control) treated as "the architecture".
* Connectivity granted without identity; permanent vendor access.
* Maximal security that harms safety, availability or maintainability.
* Designs that cannot be monitored, backed up or recovered.
* Architectures no one can sustain over the system's life.

---

# Decision Support

Before accepting an architecture, confirm:

1. Does it follow the Safety → Availability → Integrity → Confidentiality hierarchy?
2. Are trust boundaries (zones/conduits) explicit, documented and owned?
3. Is defense in depth present — no single point of security failure?
4. Is least privilege enforced across users, hosts, protocols and conduits?
5. Is identity established before connectivity?
6. Is the attack surface minimised and the design as simple as the requirement allows?
7. Can it be monitored, backed up and recovered to a trusted state?
8. Can the organisation operate and maintain it for the system's full life?

---

# Architect Notes

A strong OT architecture is judged less by the number of security features it contains and more by how few assumptions of trust it makes, how well it contains failure, and how sustainably it can be operated for decades. These principles are stable; the products that implement them are not.

---

# AI Guidance

When answering architecture questions:

* State the relevant principle(s) explicitly and tie recommendations to them.
* Lead with trust boundaries, least privilege and defense in depth; keep field/PLC/safety assets inside isolated zones.
* Never recommend a control that could compromise safety, determinism or recoverability.
* Recommend **capabilities before products**; defer implementation detail to the Network/Firewall/Identity documents and standards detail to [IEC62443.md](IEC62443.md).
* When principles conflict, resolve via the priority hierarchy and a documented risk-based decision, not a default preference.

---

# Sources / Grounding

* **ISA/IEC 62443** — zones and conduits, foundational requirements, defense in depth, secure lifecycle. See [IEC62443.md](IEC62443.md).
* **NIST SP 800-82 Rev. 3** — *Guide to OT Security*: segmentation, layered defense, safety-first consequence framing, lifecycle approach.
* **ISO 31000** — risk-based decision-making underpinning proportionate control selection.

> Foundational references only; normative detail lives in the standards documents.

---

# Related Documents

* [OT-Security-Philosophy.md](OT-Security-Philosophy.md)
* [Security-Decision-Framework.md](Security-Decision-Framework.md)
* [Risk-Management-Principles.md](Risk-Management-Principles.md)
* [OT-Lifecycle.md](OT-Lifecycle.md)
* [IEC62443.md](IEC62443.md)
* [Purdue-Model.md](Purdue-Model.md)
* [Network-Segmentation.md](Network-Segmentation.md)
* [Firewall-Design.md](Firewall-Design.md)
* [Identity-Management.md](Identity-Management.md)
* [Secure-Remote-Access.md](Secure-Remote-Access.md)
* [Monitoring.md](Monitoring.md)
* [Backup-and-Recovery.md](Backup-and-Recovery.md)

---

# Revision History

| Version | Date       | Description     |
| ------- | ---------- | --------------- |
| 1.0.0   | 2026-07-01 | Initial release — new Core keystone resolving the dangling `OT-Architecture-Principles.md` reference across the Core layer; defines 15 vendor-neutral OT architecture principles bridging OT-Security-Philosophy.md (mindset) and the implementation documents; grounded in ISA/IEC 62443, NIST SP 800-82 Rev. 3 and ISO 31000 |
