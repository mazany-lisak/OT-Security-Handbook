---
title: OT Security Philosophy
category: Core
version: 1.1.1
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-07-01
review_cycle: Annual
---

# Purpose

This document defines the engineering philosophy used throughout the OT Security Handbook.

Rather than describing specific technologies, vendors or regulations, it explains the principles that guide architectural decisions in Operational Technology (OT) environments.

All subsequent documents assume the concepts described here.

---

# Why OT Security Is Different

Operational Technology exists to control physical processes.

Unlike enterprise IT, the primary objective is not protecting information—it is ensuring that industrial processes remain safe, reliable and available.

An OT security architecture must therefore balance cybersecurity with operational continuity and functional safety.

---

# Security Priorities

Typical IT security is based on the CIA triad:

* Confidentiality
* Integrity
* Availability

In OT environments the priorities are usually different.

A common engineering hierarchy is:

1. Safety
2. Availability
3. Integrity
4. Confidentiality

Every recommendation in this handbook should be evaluated against this order.

> **Canonical priority hierarchy.** This Safety → Availability → Integrity → Confidentiality order is the **single source of truth** for prioritisation across the handbook. The [Security-Decision-Framework.md](Security-Decision-Framework.md) expands it into an operational eight-level ordering (adding environmental protection, process stability, operational efficiency and cost) for trade-off decisions; that expansion refines—but never reorders—the four priorities above. This inversion of the IT CIA triad is consistent with NIST SP 800-82 Rev. 3, whose OT consequence categories are safety, environmental release, production impact and equipment damage.

---

# Risk-Based Security

Absolute security does not exist.

Security controls should always be selected according to risk.

A risk assessment should consider:

* Process criticality
* Safety impact
* Operational impact
* Threat likelihood
* Existing controls
* Recovery capability

The objective is to reduce risk to an acceptable level—not eliminate all risk. See [Risk-Management-Principles.md](Risk-Management-Principles.md) for the underlying risk principles and [Risk-Based-Security.md](Risk-Based-Security.md) for the applied control-selection strategy.

---

# Architecture Before Technology

Technology should never be the starting point.

Recommended decision sequence:

1. Understand the industrial process.
2. Identify critical assets.
3. Understand operational constraints.
4. Assess risks.
5. Design the architecture.
6. Select technologies.
7. Validate the solution.

Choosing products before understanding the process often leads to unnecessary complexity. The structured method is defined in [Security-Decision-Framework.md](Security-Decision-Framework.md), and the architectural principles that shape step 5 are in [OT-Architecture-Principles.md](OT-Architecture-Principles.md).

---

# Defense in Depth

No single security control is sufficient.

A secure OT architecture combines multiple independent layers, including:

* Physical security
* Network segmentation
* Identity and access management
* Secure remote access
* Monitoring and logging
* Backup and recovery
* Operational procedures
* Security awareness

The failure of one layer should not compromise the entire system. See [Defense-in-Depth.md](Defense-in-Depth.md) for the layered model.

---

# Keep It Simple

Complex systems are difficult to secure and maintain.

Whenever possible:

* Prefer simple architectures.
* Minimize trust relationships.
* Reduce unnecessary services.
* Remove unused components.
* Document design decisions.

Simplicity improves both security and maintainability.

---

# Safety vs Security

Cybersecurity should never reduce operational safety.

Before implementing a security control, consider:

* Will this affect production?
* Could this delay emergency actions?
* Is vendor support maintained?
* Can the system still be recovered?

Security must support the process—not obstruct it.

---

# Vendor Neutrality

This handbook promotes engineering principles rather than products.

Recommendations should focus on capabilities.

Example:

✔ Industrial Firewall

instead of

✘ Vendor X Firewall

Specific products may be mentioned as examples, not requirements.

---

# Architect Mindset

Experienced architects usually begin with questions rather than solutions.

Typical questions include:

* What process are we protecting?
* Which assets are critical?
* What happens if this system fails?
* What is the acceptable operational risk?
* Which controls provide the greatest benefit?

Technology selection is the final step.

---

# AI Guidance

When answering OT-related questions:

* Prioritize safety before cybersecurity.
* Explain architectural reasoning.
* Distinguish legal requirements from engineering recommendations.
* State assumptions when context is missing.
* Recommend clarification before proposing detailed solutions.

---

# Sources / Grounding

This philosophy is consistent with, and draws on, recognised OT security doctrine:

* **NIST SP 800-82 Rev. 3** — *Guide to Operational Technology (OT) Security* (2023): safety-first posture; OT consequence categories (safety, environmental release, production impact, equipment damage).
* **ISA/IEC 62443** — the IACS security framework (defense in depth; zones and conduits; foundational requirements). See [IEC62443.md](IEC62443.md).
* **ISO 31000** — risk-management principles underpinning the risk-based approach. See [Risk-Management-Principles.md](Risk-Management-Principles.md).

> These are foundational references; specific normative requirements live in the standards documents. Defer to the current standard texts for authoritative detail.

---

# Related Documents

* [OT-Architecture-Principles.md](OT-Architecture-Principles.md)
* [Security-Decision-Framework.md](Security-Decision-Framework.md)
* [Risk-Management-Principles.md](Risk-Management-Principles.md)
* [OT-Lifecycle.md](OT-Lifecycle.md)
* [Zero-Trust-in-OT.md](Zero-Trust-in-OT.md)
* [Defense-in-Depth.md](Defense-in-Depth.md)
* [Risk-Based-Security.md](Risk-Based-Security.md)
* [NIS2.md](NIS2.md)
* [Czech-Cybersecurity-Act.md](Czech-Cybersecurity-Act.md)
* [IEC62443.md](IEC62443.md)
* [Risk-Assessment.md](Risk-Assessment.md)
* [Network-Segmentation.md](Network-Segmentation.md)

---

# Revision History

| Version | Date       | Description     |
| ------- | ---------- | --------------- |
| 1.0.0   | 2026-06-28 | Initial release |
| 1.1.0   | 2026-07-01 | Resolved committed git merge conflict and de-duplicated content; fixed malformed YAML front matter; corrected `IEC62443-Overview.md` → `IEC62443.md`; designated the Safety→Availability→Integrity→Confidentiality hierarchy as the canonical single source of truth (referenced by Security-Decision-Framework.md); added Sources/Grounding (NIST SP 800-82 Rev. 3, IEC 62443, ISO 31000); added Core cross-links (OT-Architecture-Principles.md, Security-Decision-Framework.md, Risk-Management-Principles.md, OT-Lifecycle.md) |
| 1.1.1   | 2026-07-01 | Added reciprocal cross-links to the 03-Architecture strategy documents (Defense-in-Depth.md, Risk-Based-Security.md, Zero-Trust-in-OT.md) in the relevant sections and Related Documents |
