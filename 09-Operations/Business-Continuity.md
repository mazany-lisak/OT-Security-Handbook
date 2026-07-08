---
id: business-continuity
title: "Business Continuity"
category: Operations
layer: "09-Operations"
version: 1.1.1
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Maintaining critical industrial operations during disruption: continuity strategies (manual,
  reduced-capacity, isolated, degraded modes), personnel and supplier dependencies, and
  coordination with DR and IR.
keywords: [business continuity, kontinuita provozu, BCP, manual operation, degraded mode, ISO 22301]
---

# Purpose

Business Continuity is the capability of an organization to continue delivering critical products and services at an acceptable predefined level following a disruptive event.

Within Operational Technology (OT), Business Continuity focuses on maintaining safe and essential industrial operations despite failures affecting people, facilities, technology, suppliers or cybersecurity.

The objective is not necessarily to restore every system immediately, but to sustain critical business processes until normal operations can be fully recovered.

Business Continuity is a business discipline supported by technology—not a technology discipline.

---

# Scope

This document describes:

* Business Continuity principles
* OT-specific continuity strategies
* Continuity planning
* Organizational resilience
* Recovery priorities
* Governance
* Operational best practices

Technical recovery procedures are described separately in **[Disaster-Recovery.md](Disaster-Recovery.md)** and **[Disaster-Recovery-Plan.md](Disaster-Recovery-Plan.md)**.

---

# Business Philosophy

Technology exists to support business.

When disruption occurs, organizations should prioritize maintaining critical business outcomes rather than restoring every technical component immediately.

Business Continuity therefore focuses on preserving operational capability rather than achieving complete technical recovery.

---

# Why Business Continuity Matters

Industrial organizations may experience disruption caused by:

* Cyber attacks
* Equipment failures
* Utility outages
* Natural disasters
* Human error
* Supplier failures
* Building evacuation
* Network failures

Business Continuity prepares the organization to continue operating despite these events.

---

# Business Continuity Objectives

A mature Business Continuity capability should:

* Protect human safety
* Maintain critical operations
* Minimize business disruption
* Support regulatory compliance
* Reduce financial loss
* Enable coordinated recovery
* Protect organizational reputation

Continuity planning should support strategic business objectives.

---

# Continuity Lifecycle

Recommended lifecycle:

```text id="bcplife"
Business Impact Analysis
        ↓
Continuity Strategy
        ↓
Continuity Plan
        ↓
Preparation
        ↓
Response
        ↓
Recovery
        ↓
Review
        ↓
Continuous Improvement
```

Business Continuity is an ongoing management process.

---

# Business Continuity Strategies

Organizations may employ one or more strategies.

Examples include:

* Manual operation
* Reduced production capacity
* Alternative production sites
* Redundant equipment
* Spare components
* Alternative communication methods
* Supplier substitution
* Temporary workarounds

Strategies should be selected based on business priorities.

---

# Critical Business Processes

Examples include:

* Production
* Water treatment
* Energy distribution
* Chemical processing
* Manufacturing
* Logistics
* Quality assurance

Business processes—not individual systems—should define continuity priorities.

---

# Operational Continuity

Operational continuity may include:

* Manual control
* Alternative operating procedures
* Local operator intervention
* Simplified production modes
* Deferred non-critical activities

Organizations should define acceptable operating modes before disruptions occur.

---

# Personnel Continuity

Business Continuity depends on people as well as technology.

Planning should consider:

* Key personnel
* Cross-training
* Shift coverage
* Emergency contacts
* Decision authority
* Succession planning

Single-person dependencies should be minimized.

---

# Communication

Continuity planning should define:

* Internal communication
* Executive communication
* Regulatory communication
* Supplier communication
* Customer communication
* Emergency contact procedures

Communication processes should remain operational during disruptions.

---

# Supplier Dependencies

Organizations should evaluate:

* Critical suppliers
* Spare part availability
* Vendor response capability
* Remote support
* Maintenance contracts

Supplier resilience directly affects operational resilience.

---

# Alternative Operating Modes

Examples include:

### Manual Operation

Operators temporarily control equipment without automation.

---

### Reduced Capacity

Production continues with limited throughput.

---

### Isolated Operation

The industrial process operates without enterprise connectivity.

---

### Graceful Degradation

Non-essential services are disabled while core operations continue.

Continuity strategies should be documented and exercised.

---

# Relationship with Business Impact Analysis

The Business Impact Analysis identifies:

* Critical processes
* Recovery priorities
* Recovery objectives

Business Continuity Planning defines how those priorities will be maintained during disruption.

---

# Relationship with Disaster Recovery

Business Continuity answers:

> **How do we continue operating?**

Disaster Recovery answers:

> **How do we restore technology?**

Both disciplines are complementary.

---

# Relationship with Incident Response

Incident Response manages the disruptive event.

Business Continuity maintains operational capability during that event.

The two activities should be coordinated.

---

# Relationship with Backup

Backups support recovery.

Business Continuity determines:

* When recovery is required.
* Which systems require priority restoration.
* Which workarounds are acceptable until recovery completes.

---

# Relationship with IEC 62443

Business Continuity supports multiple IEC 62443 objectives including:

* Operational resilience
* Availability
* Risk management
* Security program maturity

Business Continuity strengthens the organization's ability to withstand cyber and operational disruptions.

---

# Monitoring

Organizations should periodically review:

* Business process changes
* New dependencies
* Personnel changes
* Supplier changes
* Recovery objectives
* Alternative operating procedures
* Continuity exercise results

Business Continuity plans should evolve with the organization.

---

# Common Design Mistakes

Typical mistakes include:

* Equating Business Continuity with Disaster Recovery.
* Focusing exclusively on technology.
* Ignoring manual operating procedures.
* Ignoring personnel availability.
* Assuming suppliers will always be available.
* Never exercising continuity plans.
* Failing to update continuity documentation.

---

# Architect Notes

One of the most common misconceptions is:

> "Business Continuity means restoring all systems."

It does not.

Business Continuity seeks to maintain acceptable business operations, even if some systems remain unavailable.

In many industrial environments, temporary manual operation or reduced production may represent a successful continuity strategy until full recovery is possible.

---

# Decision Support

Before developing a Business Continuity strategy, determine:

1. Which business processes are essential?
2. Which disruptions are most credible?
3. Which activities can continue manually?
4. Which functions require automation?
5. Which suppliers are critical?
6. Which personnel are essential?
7. Which communication methods remain available during disruption?
8. How will continuity plans be validated and exercised?

---

# Practical Examples

### Example 1 – Production Line

Disruption:

SCADA unavailable.

Continuity strategy:

* Continue production using predefined manual operating procedures.
* Increase operator supervision.
* Record process values manually.
* Restore SCADA through Disaster Recovery procedures while production continues safely.

---

### Example 2 – Engineering Network Failure

Disruption:

Engineering workstations unavailable.

Continuity strategy:

* Suspend non-essential engineering changes.
* Continue stable production.
* Use approved emergency engineering workstation if required.
* Restore engineering infrastructure according to the Disaster Recovery Plan.

---

### Example 3 – Internet Connectivity Loss

Disruption:

External connectivity unavailable.

Continuity strategy:

* Continue isolated plant operation.
* Suspend vendor remote access.
* Use local authentication and operational procedures.
* Restore connectivity after risk assessment.

---

# AI Guidance

When discussing Business Continuity:

* Explain that Business Continuity focuses on maintaining business operations rather than restoring technology.
* Distinguish Business Continuity from Disaster Recovery and Incident Response.
* Recommend identifying critical business processes before selecting continuity strategies.
* Consider manual operation, reduced-capacity operation and alternative procedures where appropriate.
* Encourage regular continuity exercises involving operations, engineering and management.
* Integrate Business Continuity with Business Impact Analysis, Backup, Disaster Recovery and Incident Response.
* Prioritize human safety, operational continuity and regulatory compliance throughout planning.

---

# Sources / Grounding

* **ISO 22301** (Business Continuity Management Systems), **ISO/IEC 27031** (ICT readiness for business continuity), **NIST SP 800-34**.

> These are foundational references; defer to the current standard/catalogue for authoritative, up-to-date detail.

---

# Related Documents

* [Business-Impact-Analysis.md](Business-Impact-Analysis.md)
* [Disaster-Recovery.md](Disaster-Recovery.md)
* [Disaster-Recovery-Plan.md](Disaster-Recovery-Plan.md)
* [Backup.md](Backup.md)
* [Incident-Response.md](Incident-Response.md)
* [Recovery-Testing.md](Recovery-Testing.md)
* [Change-Management.md](Change-Management.md)
* [Risk-Based-Security.md](../03-Architecture/Risk-Based-Security.md)
* [Asset-Management.md](Asset-Management.md)
* [IEC62443.md](../02-Standards/IEC62443.md)
* [NIS2.md](../01-Legislation/NIS2.md)

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
| 1.1.0   | 2026-07-01 | Converted to YAML front matter (category: Operations) and semver; fixed links (IEC62443-Overview.md → IEC62443.md); added Sources/Grounding; markdown Related Documents |
| 1.1.1 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings |
