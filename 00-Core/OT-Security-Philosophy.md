---

title: OT Security Philosophy

category: Core

version: 1.0.0

status: Stable

author: OT Security Handbook Project

classification: Public

last_reviewed: 2026-06-28

## review_cycle: Annual

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

The objective is to reduce risk to an acceptable level—not eliminate all risk.

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

Choosing products before understanding the process often leads to unnecessary complexity.

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

The failure of one layer should not compromise the entire system.

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

# Related Documents

* NIS2.md
* Czech-Cybersecurity-Act.md
* IEC62443-Overview.md
* Risk-Assessment.md
* Network-Segmentation.md

---

# Revision History

| Version | Date       | Description     |
| ------- | ---------- | --------------- |
| 1.0.0   | 2026-06-28 | Initial release |