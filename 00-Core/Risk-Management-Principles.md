---
title: Risk Management Principles
category: Core
version: 1.1.1
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-07-01
review_cycle: Annual
---

# Purpose

This document defines the fundamental principles of cybersecurity risk management for Operational Technology (OT) environments.

It provides an engineering perspective rather than a compliance methodology.

The objective is to support consistent architectural decision-making throughout the lifecycle of industrial systems.

> **Scope note (Core, not Operations).** This document covers the **principles** of risk management. The **operational execution** — running risk assessments, maintaining a risk register, and periodic reassessment — is an Operate/Maintain activity documented separately (see [Risk-Assessment.md](Risk-Assessment.md)) and governed by IEC 62443-2-1 / 62443-3-2. The **applied strategy** of selecting and prioritising security controls by risk (including vulnerability triage) is in [Risk-Based-Security.md](Risk-Based-Security.md).

---

# Why Risk Management Matters

Cybersecurity is not about eliminating every possible threat.

It is about understanding which risks are significant, selecting appropriate controls and reducing risk to an acceptable level while maintaining safe and reliable operations.

Every architecture is ultimately a series of risk management decisions.

---

# Core Principle

Risk management is a continuous engineering process.

It should support operational objectives—not compete with them.

Good risk management enables informed decisions.

Poor risk management produces unnecessary controls, excessive complexity or unacceptable exposure.

---

# The Risk Management Cycle

Risk management is iterative.

```mermaid
flowchart LR

A[Identify Assets]
--> B[Identify Threats]
--> C[Identify Vulnerabilities]
--> D[Evaluate Risk]
--> E[Select Controls]
--> F[Implement]
--> G[Monitor]
--> H[Review]
--> A
```

The process never truly ends.

Industrial systems evolve, and so do risks.

---

# Understand the Asset Before the Risk

Risk cannot be evaluated without understanding the asset.

For each asset determine:

* Business purpose
* Operational function
* Safety impact
* Availability requirements
* Dependencies
* Owner
* Recovery requirements

Unknown assets cannot be effectively protected.

---

# Risk Is Context-Dependent

The same vulnerability may represent different levels of risk depending on the environment.

Example:

A legacy PLC running unsupported firmware may represent:

* High risk in a remotely accessible production network.
* Moderate risk in an isolated laboratory.
* Acceptable risk in a physically protected offline environment.

Risk should never be evaluated without context.

---

# Consequence Is Evaluated in Safety and Operational Terms

In OT, consequence is **not** primarily financial. A control-system compromise may cause physical harm, environmental release, production loss or equipment damage. Evaluate impact using OT consequence categories—**safety (injury/death), environmental release, production impact, equipment damage**—and have the **process-safety team validate** what a given manipulation actually means for the physical process. This safety-weighted view of consequence is what distinguishes OT risk assessment from IT risk assessment.

---

# Security Controls Have Costs

Every control introduces costs.

Examples include:

* Increased operational complexity
* Additional maintenance effort
* Reduced system flexibility
* Performance impact
* Training requirements
* Procurement costs

The objective is to maximize risk reduction while minimizing negative operational impact.

---

# Accept That Residual Risk Exists

No architecture eliminates all risk.

After implementing appropriate controls, some residual risk will remain.

Residual risk should be:

* Understood
* Documented
* Accepted by the appropriate authority
* Periodically reviewed

Ignoring residual risk is poor governance.

Ignoring that it exists is unrealistic.

---

# Risk Is Not Static

Industrial environments change continuously.

Risk should be reviewed when:

* New equipment is introduced.
* Firmware is upgraded.
* Network architecture changes.
* New suppliers gain access.
* Production processes change.
* New threats emerge.
* Significant incidents occur.

Periodic reviews should also be part of normal governance.

---

# Balance Security and Operations

The most secure solution is not always the best solution.

For example:

* Disabling all remote access may improve security but reduce operational resilience.
* Applying patches immediately may introduce production instability.
* Restricting engineering access excessively may delay incident recovery.

Risk management requires balancing competing objectives.

---

# Typical Risk Treatment Options

For each identified risk, one or more treatment strategies may be appropriate.

Common approaches include:

* Reduce the risk by implementing controls.
* Transfer the risk through contracts or insurance.
* Avoid the activity creating the risk.
* Accept the residual risk after evaluation.

The selected approach should be documented together with its rationale. (These four options mirror the ISO 31000 risk-treatment set; "share" is sometimes distinguished from "transfer".)

---

# Questions Every Architect Should Ask

Before recommending any security control:

* What problem does this control solve?
* Which risk is being reduced?
* What new risks could it introduce?
* How does it affect operations?
* Is the control sustainable over the system lifecycle?
* Can the organization maintain it?

If these questions cannot be answered, more analysis is required.

---

# Common Risk Management Mistakes

Avoid:

* Treating compliance as risk management.
* Evaluating risks without operational input.
* Assuming every vulnerability requires immediate remediation.
* Ignoring maintenance requirements.
* Focusing only on technical threats.
* Forgetting supply chain risks.
* Documenting risks once and never reviewing them again.

---

# Architect Notes

Experienced architects do not attempt to remove all risk.

Instead, they focus on reducing the risks that matter most.

A well-designed architecture accepts that some residual risk will always remain, provided it is understood, documented and managed.

The goal is informed decision-making rather than absolute security.

---

# AI Guidance

When responding to risk-related questions:

* Encourage understanding of the operational context before recommending controls.
* Explain the relationship between threats, vulnerabilities and business impact.
* Distinguish between risk reduction and risk elimination.
* Explain trade-offs.
* Avoid recommending the same controls for every environment.

When information is incomplete, ask clarifying questions before assessing risk.

---

# Sources / Grounding

* **ISO 31000** — overarching risk-management principles and process; the risk-treatment options (reduce / transfer / avoid / accept, plus share).
* **ISO/IEC 27005** — information-security risk-management process. The 2024 revision of **ISA/IEC 62443-2-1** aligns IACS risk-assessment methodology with ISO/IEC 27005 and **NIST SP 800-30**.
* **IEC 62443-3-2** — IACS security risk assessment (zones/conduits, Target Security Levels, Cybersecurity Requirements Specification). See [IEC62443.md](IEC62443.md).
* **NIST SP 800-82 Rev. 3** (§4) — OT risk management; consequence categories (safety, environmental, production, equipment).

> These are foundational references; the operational risk process and normative requirements are detailed in the standards and in [Risk-Assessment.md](Risk-Assessment.md).

---

# Related Documents

* [OT-Security-Philosophy.md](OT-Security-Philosophy.md)
* [OT-Architecture-Principles.md](OT-Architecture-Principles.md)
* [Security-Decision-Framework.md](Security-Decision-Framework.md)
* [Risk-Based-Security.md](Risk-Based-Security.md)
* [OT-Lifecycle.md](OT-Lifecycle.md)
* [Risk-Assessment.md](Risk-Assessment.md)
* [IEC62443.md](IEC62443.md)
* [NIS2.md](NIS2.md)
* [Czech-Cybersecurity-Act.md](Czech-Cybersecurity-Act.md)

---

# Revision History

| Version | Date       | Description     |
| ------- | ---------- | --------------- |
| 1.0.0   | 2026-06-28 | Initial release |
| 1.1.0   | 2026-07-01 | Resolved committed git merge conflict and de-duplicated content; fixed malformed YAML front matter; corrected `IEC62443-Overview.md` → `IEC62443.md`; added a Core-vs-Operations scope note (principles here; execution in Risk-Assessment.md); added an OT-consequence (safety/operational) section; noted ISO 31000 lineage of risk-treatment options; added Sources/Grounding (ISO 31000, ISO/IEC 27005, IEC 62443-2-1/-3-2, NIST SP 800-30/800-82 Rev. 3); added Core cross-links |
| 1.1.1   | 2026-07-01 | Added reciprocal cross-link to the applied strategy [Risk-Based-Security.md](Risk-Based-Security.md) (scope note and Related Documents) |
