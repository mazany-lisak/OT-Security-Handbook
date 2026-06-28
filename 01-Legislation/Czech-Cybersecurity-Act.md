---

title: Czech Cybersecurity Act (Act No. 264/2025 Coll.)

category: Legislation

version: 1.0.0

status: Stable

author: OT Security Handbook Project

classification: Public

last_reviewed: 2026-06-28

## review_cycle: Annual

# Purpose

This document provides an engineering-oriented overview of the Czech Cybersecurity Act (Act No. 264/2025 Coll.) from the perspective of an Operational Technology (OT) Security Architect.

Rather than describing legal provisions in detail, it explains how the Act influences the design, governance and operation of industrial systems.

This document complements **NIS2.md** by focusing on the Czech national implementation.

---

# Scope

The Czech Cybersecurity Act transposes the NIS2 Directive into Czech law.

It establishes:

* which organizations fall under regulation,
* which cybersecurity obligations apply,
* the role of the National Cyber and Information Security Agency (NÚKIB),
* the distinction between higher and lower obligation regimes,
* the legal basis for implementing decrees.

The Act replaces the previous Cybersecurity Act No. 181/2014 Coll.

---

# Architect's Perspective

The Act should not be viewed as a technical standard.

Instead, it defines:

* governance expectations,
* mandatory organizational responsibilities,
* risk management obligations,
* accountability,
* supervisory processes.

The technical implementation is achieved through architecture, operational procedures and security controls.

---

# Relationship with NIS2

NIS2 establishes a common European framework.

The Czech Cybersecurity Act implements that framework within the Czech Republic.

For architects:

* NIS2 defines strategic objectives.
* The Czech Act defines national legal obligations.
* The implementing decrees define detailed security measures.
* IEC 62443 provides engineering guidance.

These four layers should always be considered together.

---

# Regulated Services

The Act applies to providers of regulated services.

Whether an organization is subject to the Act depends on:

* sector,
* service provided,
* organizational characteristics,
* legal criteria defined by the Act and implementing legislation.

Architects should verify regulatory scope during the earliest project phases.

Never assume that every industrial organization is regulated.

---

# Higher and Lower Obligation Regimes

One of the key architectural implications is the distinction between:

* Higher Obligations
* Lower Obligations

This distinction influences:

* required governance,
* documentation,
* security processes,
* technical controls,
* audit expectations.

Architectural complexity should remain proportional to the applicable regime.

---

# Governance

The Act makes cybersecurity a management responsibility.

Typical management responsibilities include:

* approving cybersecurity policies,
* ensuring adequate resources,
* overseeing risk management,
* supporting continual improvement,
* ensuring regulatory compliance.

Cybersecurity is therefore an organizational responsibility rather than solely an IT or OT function.

---

# Risk Management

The Act requires organizations to manage cybersecurity risks continuously.

For architects this means that every design decision should be supported by:

* documented assumptions,
* risk assessment,
* justification,
* traceability.

Architecture documentation should demonstrate how identified risks are addressed.

---

# Security Measures

The Act establishes the legal requirement to implement security measures.

The detailed requirements are specified in the implementing decrees.

Typical architectural capabilities include:

* asset management,
* identity and access management,
* network segmentation,
* secure remote access,
* logging,
* monitoring,
* vulnerability management,
* backup and recovery,
* incident response,
* supplier security.

The exact implementation should always be risk-based.

---

# Incident Reporting

The Act establishes obligations related to cybersecurity incident reporting.

Architectural implications include:

* centralized logging,
* reliable time synchronization,
* evidence preservation,
* incident response procedures,
* communication channels,
* clearly defined responsibilities.

Incident reporting should be considered during system design rather than after deployment.

---

# Supply Chain Security

Industrial systems depend heavily on suppliers.

Architects should therefore design for:

* controlled vendor access,
* authenticated remote maintenance,
* software integrity,
* firmware authenticity,
* contractual security requirements,
* supplier accountability.

Supplier access should be treated as part of the architecture rather than an operational exception.

---

# Documentation

The Act significantly increases the importance of documentation.

Typical documentation includes:

* architecture diagrams,
* asset inventory,
* risk assessments,
* operating procedures,
* incident response plans,
* backup procedures,
* change records,
* supplier documentation.

Documentation is an engineering deliverable—not merely an audit artifact.

---

# Common Misconceptions

The Act does **not** prescribe:

* specific firewall products,
* mandatory vendors,
* exact network topologies,
* specific authentication technologies,
* mandatory SIEM platforms.

It establishes legal obligations, while engineering decisions remain the responsibility of the organization.

---

# Architect Notes

Successful compliance is achieved through good engineering rather than isolated security controls.

When designing an OT architecture, always ask:

* Which legal requirement is addressed?
* Which architectural capability satisfies it?
* Which operational process supports it?
* Which evidence will demonstrate compliance?

Compliance should emerge naturally from a well-designed architecture.

---

# AI Guidance

When answering questions related to the Czech Cybersecurity Act:

* Distinguish legal obligations from implementation guidance.
* Refer to the implementing decrees for detailed security measures.
* Recommend risk-based architectures.
* Explain management responsibilities where relevant.
* Clarify assumptions if regulatory applicability is unknown.

Avoid interpreting legal text beyond its documented meaning.

---

# Related Documents

* NIS2.md
* Decree-408-Regulated-Services.md
* Decree-409-Higher-Obligations.md
* Decree-410-Lower-Obligations.md
* IEC62443-Overview.md
* OT-Architecture-Principles.md
* Security-Decision-Framework.md

---

# Revision History

| Version | Date       | Description     |
| ------- | ---------- | --------------- |
| 1.0.0   | 2026-06-28 | Initial release |
