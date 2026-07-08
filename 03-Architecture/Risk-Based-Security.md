---
id: risk-based-security
title: "Risk-Based Security"
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
  Applied strategy of selecting and prioritising controls by risk: control strength proportional
  to consequence and exposure, security-vs-operational-risk trade-offs, and vulnerability triage
  combining CVSS, EPSS, CISA KEV and context.
keywords: [risk-based security, prioritizace, vulnerability triage, CVSS, EPSS, CISA KEV, compensating controls, trade-offs]
---

# Purpose

Risk-Based Security is the architectural **strategy** of selecting, prioritising and tuning security controls according to risk—so that effort and control strength are concentrated where the consequence to safety and operations is greatest, and not applied uniformly.

It is one of the three complementary architectural strategies: [Defense-in-Depth.md](Defense-in-Depth.md) answers *how to layer* controls, [Zero-Trust-in-OT.md](Zero-Trust-in-OT.md) answers *which identities to trust and when*, and this document answers ***which controls are actually needed, and in what priority***.

> **Scope — applied strategy, not first principles.** This document assumes the risk **principles** defined in [Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md) (what risk is, the risk cycle, treatment options, residual risk) and the decision **method** in [Security-Decision-Framework.md](../00-Core/Security-Decision-Framework.md) (the eight-step sequence). It does **not** repeat them. It focuses on the *applied* strategy: risk-based control selection, the security-vs-operational-risk trade-off, and **vulnerability prioritisation** (CVSS / EPSS / CISA KEV) in an OT context. Consequence prioritisation follows the canonical hierarchy in [OT-Security-Philosophy.md](../00-Core/OT-Security-Philosophy.md).

---

# Scope

This document describes:

* Risk-based control **selection and prioritisation**
* Security vs operational risk trade-offs
* Vulnerability prioritisation and triage (CVSS, EPSS, CISA KEV, context)
* Applied worked examples

Risk fundamentals, the risk cycle and risk treatment are in [Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md). The decision sequence is in [Security-Decision-Framework.md](../00-Core/Security-Decision-Framework.md). Vendor-specific methodologies are intentionally excluded.

---

# Security Philosophy

Absolute security does not exist. Every architecture contains vulnerabilities, operational constraints, business limitations, human factors and unknown risks. Attempting to eliminate all risk usually creates unacceptable operational complexity. **Good architecture balances security with operational reality**—it reduces the risks that matter most, at acceptable operational cost.

---

# Why Risk-Based Security Matters in OT

Security decisions in OT may affect human safety, environmental protection, process stability, production continuity, regulatory compliance and financial performance. Because a control can itself harm safety or availability, controls must be chosen on the basis of **total organisational risk**, not cyber risk alone, and applied where they buy the most risk reduction per unit of operational impact.

---

# Risk-Based Control Selection and Prioritisation

The core of the strategy: **do not apply identical controls everywhere.** Concentrate the strongest controls on the highest-consequence assets and conduits, and accept lighter controls (or compensating controls) elsewhere.

* Match control strength to the asset's consequence and exposure (e.g. an SIS or a remotely reachable controller warrants stronger controls than an isolated lab HMI).
* Prefer the control that reduces the **greatest risk with the least operational impact**.
* Prioritise consequence in the canonical order (Human Safety → Environmental Protection → Process Stability → Availability → Integrity → Confidentiality → Operational Efficiency → Cost), per [OT-Security-Philosophy.md](../00-Core/OT-Security-Philosophy.md) and [Security-Decision-Framework.md](../00-Core/Security-Decision-Framework.md). **Cost alone should rarely determine security priorities.**
* Where an asset cannot be hardened (legacy), shift to **compensating controls** (segmentation, monitoring, restricted access) rather than forcing an unfeasible fix.

---

# Risk Is Context, Not a Score

A CVSS score alone does not determine operational risk.

Example: a vulnerability with a high CVSS score on an isolated engineering workstation may represent **lower** operational risk than a medium-severity vulnerability affecting a Safety Instrumented System (SIS). Exposure, exploitability and consequence—not severity alone—determine priority.

---

# Vulnerability Prioritisation and Triage

When many vulnerabilities compete for limited maintenance windows, prioritise using complementary signals rather than CVSS alone:

* **CVSS (severity)** — describes *technical severity* under assumed conditions; it is **not** a measure of the likelihood of exploitation or of operational risk in *your* environment.
* **EPSS (exploit probability)** — an empirical probability that a vulnerability will be exploited in the wild in the near term; helps rank the large volume of high-CVSS items.
* **CISA KEV (Known Exploited Vulnerabilities)** — a catalogue of vulnerabilities *actually being exploited*; presence here strongly elevates priority.
* **Operational context** — exposure (Internet/remote-reachable vs. isolated), asset criticality/consequence (SIS vs. lab), existing compensating controls, and the operational cost/risk of patching (downtime, revalidation).

The output is a **prioritised, context-aware remediation plan**, not a raw CVSS-ordered list. Patch timing must respect maintenance windows and change management ([Change-Management.md](../09-Operations/Change-Management.md)); where immediate patching is infeasible, apply compensating controls and document the residual risk (per [Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md)).

---

# Security vs Operational Risk

Security controls may themselves introduce risk. Examples:

* Firewalls or DPI increasing latency on deterministic networks.
* Anti-malware disrupting PLC engineering tools.
* Authentication delaying emergency operator response.
* Patching causing production downtime or invalidating vendor certification.

Architectural decisions should reduce **total** risk (cyber *plus* operational *plus* safety), not cyber risk alone. A control that raises operational or safety risk more than it lowers cyber risk is a net loss.

---

# Relationship with Defense in Depth and Zero Trust

The three strategies are complementary:

* **Risk-Based Security** decides *which* controls are needed and in what priority.
* **Defense in Depth** ([Defense-in-Depth.md](Defense-in-Depth.md)) decides *how* controls are layered so no single failure is catastrophic.
* **Zero Trust** ([Zero-Trust-in-OT.md](Zero-Trust-in-OT.md)) decides *which identities* are trusted, when and under what conditions.

Risk determines priorities; Defense in Depth and Zero Trust shape implementation.

---

# Relationship with Standards and Regulation

* **IEC 62443** is fundamentally risk-based (Security Levels, zones & conduits, the 62443-3-2 risk assessment). Controls should be selected per identified risk, not applied uniformly. See [IEC62443.md](../02-Standards/IEC62443.md).
* **NIS2** requires risk-management measures *appropriate to the organisation's circumstances*—it deliberately avoids prescribing identical controls for everyone; Risk-Based Security is the methodology that satisfies this. See [NIS2.md](../01-Legislation/NIS2.md) and [Czech-Cybersecurity-Act.md](../01-Legislation/Czech-Cybersecurity-Act.md).
* **MITRE ATT&CK** describes attacker behaviour; risk assessment determines whether those techniques are relevant, which assets they threaten and which controls are justified. Threat intelligence informs—but does not replace—risk assessment.

Compliance **supports** risk management; it does not replace it.

---

# Practical Examples

### Example 1 – Legacy PLC

**Situation:** A PLC does not support encrypted communications.

**Poor recommendation:** Replace the PLC immediately.

**Risk-based recommendation:**

* Assess operational criticality and vendor support.
* Implement network segmentation and restrict engineering access.
* Monitor communications.
* Plan phased modernisation.

---

### Example 2 – Remote Vendor Access

**Situation:** A vendor requires remote maintenance.

**Poor recommendation:** Disable remote access permanently.

**Risk-based recommendation:**

* VPN with MFA, restricted to maintenance windows.
* Require approval; record sessions; monitor activity; review access periodically.

(See [Secure-Remote-Access.md](../04-Network/Secure-Remote-Access.md) and [Zero-Trust-in-OT.md](Zero-Trust-in-OT.md).)

---

### Example 3 – High CVSS Vulnerability

**Situation:** A historian contains a vulnerability with a CVSS score of 9.8.

**Questions before deciding:**

* Is the historian Internet- or remote-accessible?
* Is exploitation realistic (EPSS)?
* Is it listed in CISA KEV?
* What operational impact would patching have (downtime, revalidation)?

The appropriate response depends on **context, not solely the CVSS score.**

---

# Common Design Mistakes

* Prioritising CVSS scores without operational context (ignoring EPSS/KEV/exposure).
* Treating compliance as risk management.
* Ignoring safety impacts.
* Using identical security controls everywhere.
* Letting a control's own operational risk go unaccounted.
* Ignoring residual risk.

---

# Architect Notes

Experienced architects rarely begin with "What security product should we buy?" They ask: *What are we protecting? Why does it matter? What could realistically happen? Which consequence is unacceptable? Which control reduces the greatest risk with the least operational impact?* Good architecture is the result of informed trade-offs—not maximal security.

---

# Decision Support

Before selecting or prioritising a control, determine:

1. Which asset/consequence is being protected, and how exposed is it?
2. Is the threat realistic (threat intel, EPSS, KEV)?
3. Does the control reduce more risk than it introduces (operational + safety)?
4. Is a lighter compensating control sufficient given existing layers?
5. What residual risk remains, and who accepts it?
6. Does the priority follow the canonical consequence hierarchy?

(For the full assessment sequence see [Security-Decision-Framework.md](../00-Core/Security-Decision-Framework.md); for principles see [Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md).)

---

# AI Guidance

When applying Risk-Based Security:

* Assume the risk principles ([Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md)) and decision method ([Security-Decision-Framework.md](../00-Core/Security-Decision-Framework.md)); focus on applied control selection and prioritisation.
* Distinguish cyber risk from operational risk; never recommend controls without understanding the process.
* For vulnerabilities, combine CVSS with **EPSS, CISA KEV and operational context**—never rank by CVSS alone.
* Explain trade-offs between security, availability and safety; encourage documenting residual risk.
* Explain that compliance supports—but does not replace—risk management.

---

# Sources / Grounding

* **ISO 31000** — risk-management principles and process (foundational; see [Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md)).
* **IEC 62443-3-2** — IACS security risk assessment (zones/conduits, Target Security Levels). See [IEC62443.md](../02-Standards/IEC62443.md).
* **NIST SP 800-30 / SP 800-82 Rev. 3** — risk assessment and OT risk management (consequence-based).
* **CVSS** (FIRST) — technical severity; **EPSS** (FIRST) — exploit-probability scoring; **CISA KEV** — known-exploited-vulnerabilities catalogue. Used together for context-aware prioritisation.

> Risk-Based Security is an architectural strategy; the underlying principles, process and normative requirements live in the referenced documents and standards.

---

# Related Documents

* [OT-Security-Philosophy.md](../00-Core/OT-Security-Philosophy.md)
* [OT-Architecture-Principles.md](../00-Core/OT-Architecture-Principles.md)
* [Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md)
* [Security-Decision-Framework.md](../00-Core/Security-Decision-Framework.md)
* [Defense-in-Depth.md](Defense-in-Depth.md)
* [Zero-Trust-in-OT.md](Zero-Trust-in-OT.md)
* [Risk-Assessment.md](../09-Operations/Risk-Assessment.md)
* [IEC62443.md](../02-Standards/IEC62443.md)
* [NIS2.md](../01-Legislation/NIS2.md)
* [Czech-Cybersecurity-Act.md](../01-Legislation/Czech-Cybersecurity-Act.md)
* [MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md)
* [CVSS.md](../06-Vulnerability-Management/CVSS.md)
* [EPSS.md](../06-Vulnerability-Management/EPSS.md)
* [CISA-KEV.md](../06-Vulnerability-Management/CISA-KEV.md)
* [Change-Management.md](../09-Operations/Change-Management.md)

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
| 1.1.0   | 2026-07-01 | Refactored into the applied risk-based **strategy** for the 03-Architecture layer: delegated risk fundamentals/treatment/residual-risk to Risk-Management-Principles.md and the decision sequence to Security-Decision-Framework.md (removed duplicated first-principles content); sharpened and retained the unique content — risk-based control selection & prioritisation, security-vs-operational-risk trade-off, and **vulnerability triage (CVSS + EPSS + CISA KEV + context)** with worked examples; converted to YAML front matter (category: Architecture) and semver; corrected links (`IEC62443-Overview.md` → `IEC62443.md`, removed retired `Zones-and-Conduits.md`); added Sources/Grounding and strategy-trio cross-links; markdown Related Documents |
| 1.1.1 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings |
