---
id: backup
title: "Backup"
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
  OT backup strategy: what to back up (PLC programs, SCADA projects, infrastructure and security
  assets, documentation), RPO/RTO alignment, offline and immutable copies, validation and
  restoration testing.
keywords: [backup, zálohování, RPO, RTO, offline backup, immutable, PLC projects, restoration testing]
---

# Purpose

Backup is the controlled process of creating, protecting and maintaining recoverable copies of critical data, configurations, software and operational assets.

Within Operational Technology (OT), the objective of Backup is not simply data preservation, but ensuring that industrial processes can be restored safely, consistently and within acceptable recovery objectives following hardware failure, software defects, cyber incidents or human error.

Backup is therefore a fundamental component of Operational Resilience.

---

# Scope

This document describes:

* Backup principles
* OT-specific backup requirements
* Backup lifecycle
* Backup strategy
* Recovery objectives
* Backup validation
* Governance
* Operational best practices

Detailed disaster recovery planning is covered separately in **[Disaster-Recovery.md](Disaster-Recovery.md)**.

---

# Security Philosophy

Every industrial system will eventually experience failure.

Failures may result from:

* Hardware faults
* Software defects
* Human error
* Cyber attacks
* Ransomware
* Power failures
* Configuration mistakes
* Vendor defects

The objective of Backup is to minimize recovery time, operational disruption and business impact.

A backup has value only if it can be successfully restored.

---

# Why Backup Matters

Industrial environments contain assets that cannot easily be recreated.

Examples include:

* PLC programs
* HMI configurations
* SCADA projects
* Historian databases
* Engineering projects
* Industrial recipes
* Firewall configurations
* Network device configurations
* Certificates
* License files

Loss of these assets may significantly delay recovery.

---

# Backup Objectives

A mature backup strategy should:

* Preserve critical information
* Support rapid recovery
* Protect against ransomware
* Maintain integrity
* Support regulatory requirements
* Enable disaster recovery
* Minimize downtime

Backups should support business continuity rather than merely storage.

---

# What Should Be Backed Up?

Typical OT assets include:

## Controllers

* PLC programs
* Safety PLC logic
* Firmware versions
* Hardware configuration
* Device parameters

---

## SCADA Systems

* Projects
* Databases
* Alarm configuration
* Trends
* Graphics
* Scripts

---

## Engineering Workstations

* Engineering projects
* Configuration files
* Libraries
* Templates
* Driver configurations

---

## Infrastructure

* Firewall configurations
* Switch configurations
* Router configurations
* VPN configurations
* Hypervisor configurations
* Active Directory backups
* DNS
* DHCP

---

## Security Assets

* Certificates
* Private keys
* PKI databases
* MFA configuration
* PAM configuration
* Identity configuration

---

## Documentation

* Network diagrams
* Asset inventory
* Procedures
* FAT/SAT documentation
* Risk assessments
* Configuration baselines

Documentation should be treated as a recoverable operational asset.

---

# Backup Lifecycle

Recommended lifecycle:

```text id="backuplife"
Identify Assets
        ↓
Classify Criticality
        ↓
Create Backup
        ↓
Protect Backup
        ↓
Verify Integrity
        ↓
Test Restoration
        ↓
Review
        ↓
Retire
```

Backup management is a continuous operational process.

---

# Backup Strategy

A comprehensive strategy should define:

* Scope
* Frequency
* Retention
* Storage locations
* Encryption
* Access control
* Recovery procedures
* Validation

Backup policies should reflect operational risk.

---

# Recovery Objectives

Organizations should define:

## Recovery Point Objective (RPO)

The maximum acceptable amount of data loss.

---

## Recovery Time Objective (RTO)

The maximum acceptable recovery duration.

Different assets may require different objectives.

---

# Backup Storage

Recommended practices include:

* Multiple storage locations
* Offline copies
* Immutable backups where practical
* Encryption
* Physical protection
* Access control

A backup that can be modified by ransomware provides limited protection.

---

# Backup Validation

Creating backups is insufficient.

Organizations should periodically verify:

* Backup integrity
* File completeness
* Readability
* Restoration procedures
* Recovery time
* Configuration consistency

Untested backups should not be assumed recoverable.

---

# Backup Testing

Recovery testing should include:

* PLC restoration
* SCADA restoration
* Database restoration
* Certificate recovery
* Virtual machine recovery
* Configuration recovery

Testing should be documented and periodically repeated.

---

# Change Management

Backups should be integrated with Change Management.

Before significant changes:

* Create backup
* Validate backup
* Document baseline
* Approve change

After successful deployment:

* Update baseline
* Create new backup
* Verify recoverability

---

# Relationship with Patch Management

Patch Management changes systems.

Backup protects recovery.

Every significant patch should be preceded by:

* Configuration backup
* Project backup
* Recovery validation

Rollback capability should always exist.

---

# Relationship with Incident Response

During cyber incidents, backups support:

* System restoration
* Data recovery
* Configuration recovery
* Service recovery
* Business continuity

Incident response planning should include backup recovery procedures.

---

# Relationship with Disaster Recovery

Backup preserves information.

Disaster Recovery restores operations.

Backup is therefore one component of a broader Disaster Recovery strategy.

---

# Relationship with IEC 62443

Backup supports several IEC 62443 concepts including:

* System Integrity
* Availability
* Recovery
* Configuration Management
* Security Maintenance

Backup contributes directly to cyber resilience.

---

# Monitoring

Recommended monitoring includes:

* Backup completion
* Failed backup jobs
* Backup integrity
* Storage utilization
* Expiring media
* Unauthorized backup access
* Backup restoration testing
* Configuration changes without corresponding backups

Monitoring should include both backup creation and recovery capability.

---

# Common Design Mistakes

Typical mistakes include:

* Never testing restoration.
* Backing up only databases.
* Ignoring PLC programs.
* Ignoring certificates and private keys.
* Storing backups on the same system being protected.
* No offline copies.
* No recovery documentation.
* Assuming backup success equals recoverability.

---

# Architect Notes

One of the most common misconceptions is:

> "We have backups, therefore we can recover."

Recovery depends on:

* Backup integrity
* Recovery procedures
* Documentation
* Testing
* Personnel
* Infrastructure availability

A backup strategy should be evaluated by restoration success—not by the number of backup files created.

---

# Decision Support

Before defining a backup strategy, determine:

1. Which assets are critical to operations?
2. What are the required RPO and RTO?
3. How frequently should backups be created?
4. Where should backups be stored?
5. How will backup integrity be verified?
6. How often will restoration be tested?
7. Who owns backup procedures?
8. How will backups support cyber incident recovery?

---

# Practical Examples

### Example 1 – PLC

Recommended backup:

* PLC program
* Hardware configuration
* Firmware version
* Device parameters
* Project documentation

Validate restoration on representative hardware where practical.

---

### Example 2 – SCADA Server

Recommended backup:

* Project files
* Database
* Alarm configuration
* Historical data (according to retention policy)
* User configuration
* Certificates
* Configuration files

Perform periodic restoration testing.

---

### Example 3 – Engineering Workstation

Recommended backup:

* Engineering projects
* Libraries
* Templates
* Driver configuration
* License files
* User documentation

Protect backups using encryption and access control.

---

# AI Guidance

When discussing Backup:

* Explain that Backup is an operational resilience capability rather than a storage function.
* Distinguish backup creation from successful recovery.
* Recommend backing up configurations, engineering projects, certificates and documentation in addition to application data.
* Encourage offline or immutable backup copies where operationally appropriate.
* Recommend periodic restoration testing rather than relying solely on successful backup jobs.
* Integrate Backup with Patch Management, Incident Response, Disaster Recovery and Change Management.
* Prioritize recovery objectives (RPO and RTO) according to operational criticality.

---

# Sources / Grounding

* **ISO 22301** (BCMS), **NIST SP 800-34** (Contingency Planning), **IEC 62443** (recovery, system integrity).
* RTO/RPO are defined by [Business-Impact-Analysis.md](Business-Impact-Analysis.md).

> These are foundational references; defer to the current standard/catalogue for authoritative, up-to-date detail.

---

# Related Documents

* [Disaster-Recovery.md](Disaster-Recovery.md)
* [Incident-Response.md](Incident-Response.md)
* [Patch-Management.md](Patch-Management.md)
* [Change-Management.md](Change-Management.md)
* [Asset-Management.md](Asset-Management.md)
* [Risk-Based-Security.md](../03-Architecture/Risk-Based-Security.md)
* [Defense-in-Depth.md](../03-Architecture/Defense-in-Depth.md)
* [Identity-Management.md](../05-Identity/Identity-Management.md)
* [PKI.md](../05-Identity/PKI.md)
* [Certificates.md](../05-Identity/Certificates.md)
* [IEC62443.md](../02-Standards/IEC62443.md)
* [NIS2.md](../01-Legislation/NIS2.md)

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
| 1.1.0   | 2026-07-01 | Converted to YAML front matter (category: Operations) and semver; fixed links (IEC62443-Overview.md → IEC62443.md); added Sources/Grounding; markdown Related Documents |
| 1.1.1 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings |
