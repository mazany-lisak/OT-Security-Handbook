---

title: NIS2 Directive

category: Legislation

version: 1.0.0

status: Stable

author: OT Security Handbook Project

classification: Public

last_reviewed: 2026-06-28

## review_cycle: Annual

# Purpose

This document provides an engineering-oriented overview of the NIS2 Directive from the perspective of an Operational Technology (OT) Security Architect.

It explains how NIS2 influences the design, operation and governance of industrial systems rather than reproducing the legal text.

This document should be read together with the national implementation of NIS2 and relevant industrial cybersecurity standards such as IEC 62443.

---

# What is NIS2?

The NIS2 Directive establishes a common cybersecurity framework across the European Union.

Its primary objectives are to:

* Improve cybersecurity resilience.
* Increase management accountability.
* Harmonize cybersecurity requirements across Member States.
* Improve incident reporting.
* Strengthen supply chain security.
* Promote continuous risk management.

Unlike technical standards, NIS2 defines **what organizations must achieve**, not **how they must implement it**.

---

# Why NIS2 Matters for OT

Industrial organizations increasingly rely on interconnected Operational Technology.

As a result:

* Production systems have become more exposed.
* Supply chains have become more interconnected.
* Remote access has become commonplace.
* Cyber incidents can directly affect safety, production and the environment.

For many organizations, cybersecurity is now a legal obligation rather than a voluntary improvement initiative.

---

# The Architect's Perspective

NIS2 should be viewed as a governance framework.

It establishes expectations for:

* Risk management.
* Organizational responsibilities.
* Incident handling.
* Supply chain security.
* Business continuity.
* Accountability.

The technical implementation of these requirements is typically achieved through architecture, operational processes and engineering controls.

---

# Key Architectural Implications

Although NIS2 is technology-neutral, it has significant architectural consequences.

Typical design areas affected include:

* Asset inventory
* Identity and Access Management
* Network segmentation
* Secure remote access
* Logging and monitoring
* Vulnerability management
* Backup and recovery
* Supplier access
* Incident response

An architect should ensure that these capabilities are considered from the beginning of the project.

---

# Governance and Accountability

One of the most significant changes introduced by NIS2 is the increased responsibility of management.

Management is expected to:

* Approve cybersecurity measures.
* Allocate appropriate resources.
* Oversee risk management.
* Ensure continual improvement.

Cybersecurity is therefore no longer solely an IT or OT responsibility.

Architectural decisions should support governance by providing visibility, traceability and clear ownership.

---

# Risk-Based Approach

NIS2 promotes risk-based cybersecurity rather than prescriptive technical controls.

This means that security measures should be selected according to:

* Business impact.
* Operational impact.
* Threat landscape.
* Asset criticality.
* Existing safeguards.

Organizations with different operational environments may therefore implement different technical solutions while remaining compliant.

---

# Supply Chain Security

Industrial environments depend heavily on external vendors and service providers.

Architects should therefore consider:

* Vendor remote access.
* Third-party maintenance.
* Software supply chains.
* Firmware integrity.
* Trusted update mechanisms.
* Contractual security requirements.

Supplier security should be integrated into the overall architecture rather than treated as a separate process.

---

# Incident Management

NIS2 emphasizes timely detection, reporting and handling of cybersecurity incidents.

From an architectural perspective, this requires:

* Centralized logging.
* Time synchronization.
* Security monitoring.
* Incident response procedures.
* Evidence preservation.
* Recovery planning.

Incident response should be designed as part of the architecture—not added later.

---

# Business Continuity

Cybersecurity must support operational continuity.

Architectures should therefore include:

* Backup strategies.
* Disaster recovery planning.
* Recovery testing.
* Redundant critical services where appropriate.
* Manual operating procedures where required.

Availability remains one of the primary objectives in industrial environments.

---

# Relationship with IEC 62443

NIS2 answers the question:

> **"What should an organization achieve?"**

IEC 62443 primarily answers:

> **"How can industrial systems be designed and operated to achieve those objectives?"**

The two are complementary rather than competing.

A mature OT cybersecurity program typically uses NIS2 for governance and IEC 62443 for engineering implementation.

---

# Relationship with National Legislation

NIS2 is implemented through national legislation.

Architects should always verify:

* Whether the organization falls within scope.
* Which national obligations apply.
* Which regulatory authority is responsible.
* Which reporting requirements exist.

This handbook discusses Czech implementation separately in:

* Czech-Cybersecurity-Act.md
* Decree-408-Regulated-Services.md
* Decree-409-Higher-Obligations.md
* Decree-410-Lower-Obligations.md

---

# Common Misconceptions

NIS2 does **not** prescribe:

* Specific firewall vendors.
* Specific authentication methods.
* Mandatory network topologies.
* Particular SIEM products.
* Mandatory cloud adoption.

NIS2 defines objectives rather than technologies.

---

# Architect Notes

When reading NIS2, focus on capabilities rather than products.

Ask:

* Which architectural capability satisfies this requirement?
* Which operational process supports it?
* Which evidence demonstrates compliance?

Successful compliance is usually the result of good architecture rather than isolated security controls.

---

# AI Guidance

When answering questions related to NIS2:

* Distinguish legal requirements from engineering recommendations.
* Explain how governance influences architecture.
* Recommend risk-based rather than prescriptive controls.
* Refer to IEC 62443 when implementation guidance is required.
* Clarify assumptions if the regulatory scope is unknown.

Avoid presenting NIS2 as a technical implementation guide.

---

# Related Documents

* OT-Security-Philosophy.md
* OT-Architecture-Principles.md
* Security-Decision-Framework.md
* Risk-Management-Principles.md
* Czech-Cybersecurity-Act.md
* IEC62443-Overview.md
* Risk-Assessment.md

---

# Revision History

| Version | Date       | Description     |
| ------- | ---------- | --------------- |
| 1.0.0   | 2026-06-28 | Initial release |
