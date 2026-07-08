---
id: incident-response
title: "Incident Response"
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
  OT incident response with safety-first priorities: the IR lifecycle, detection indicators,
  containment trade-offs against process stability, eradication, recovery with operational
  validation and lessons learned.
keywords: [incident response, reakce na incidenty, containment, eradication, evidence preservation, NIST SP 800-61, safety first]
---

# Purpose

Incident Response (IR) is the structured process of preparing for, detecting, analyzing, containing, eradicating and recovering from cybersecurity incidents.

Within Operational Technology (OT), Incident Response extends beyond cybersecurity.

Every response activity must also preserve:

* Human safety
* Process stability
* Environmental protection
* Operational continuity
* Regulatory compliance

The objective is not simply to remove the attacker, but to restore the industrial process to a safe and trusted operational state.

---

# Scope

This document describes:

* Incident Response principles
* OT-specific response considerations
* Response lifecycle
* Roles and responsibilities
* Recovery
* Lessons learned
* Continuous improvement

Detailed forensic procedures are outside the scope of this document.

---

# Security Philosophy

Cyber incidents in OT are operational incidents.

Every response decision may affect:

* Personnel safety
* Equipment integrity
* Environmental protection
* Product quality
* Production continuity

Security actions should therefore be coordinated with operational personnel rather than performed in isolation.

---

# Response Priorities

The recommended priority order is:

1. Human Safety
2. Environmental Protection
3. Process Stability
4. Operational Continuity
5. Cybersecurity Containment
6. Evidence Preservation
7. System Recovery

Cybersecurity objectives should never compromise life safety.

---

# Incident Response Lifecycle

Recommended lifecycle:

```text id="irlifecycle"
Preparation
        ↓
Detection
        ↓
Analysis
        ↓
Containment
        ↓
Eradication
        ↓
Recovery
        ↓
Lessons Learned
        ↓
Continuous Improvement
```

Incident Response should be viewed as a continuous operational capability rather than an isolated activity.

---

# Preparation

Preparation includes:

* Incident Response Plan
* Roles and responsibilities
* Contact lists
* Escalation procedures
* Communication plans
* Backup validation
* Recovery testing
* Tabletop exercises

Preparation largely determines response effectiveness.

---

# Detection

Potential indicators include:

* Unexpected PLC programming
* Unauthorized engineering sessions
* New administrator accounts
* Unexpected firmware changes
* Network anomalies
* VPN misuse
* Firewall alerts
* OPC UA certificate changes
* Unexpected configuration changes

Detection should combine multiple telemetry sources.

---

# Analysis

Before taking containment actions, determine:

* What happened?
* Which systems are affected?
* Is safety impacted?
* Is the process stable?
* Is the attacker active?
* What operational dependencies exist?

Technical analysis should always include operational context.

---

# Containment

Containment should minimize both cyber and operational risk.

Possible actions include:

* Restrict network communication
* Disable compromised accounts
* Block malicious VPN sessions
* Isolate affected engineering workstations
* Increase monitoring
* Restrict vendor access

Immediate shutdown of industrial processes should occur only after evaluating safety and operational consequences.

---

# Eradication

After containment:

* Remove malicious software
* Remove unauthorized accounts
* Restore trusted configurations
* Patch vulnerabilities where appropriate
* Rotate credentials
* Reissue compromised certificates if necessary

Eradication should remove both the attacker and the conditions enabling compromise.

---

# Recovery

Recovery includes:

* Restoring systems
* Restoring configurations
* Restoring PLC logic
* Restoring communications
* Validating operational functionality
* Confirming process stability

Recovery is complete only after operational verification.

---

# Operational Validation

Recovery should confirm:

* Process integrity
* Alarm functionality
* Communications
* Historian operation
* Safety systems
* Operator interfaces
* Industrial protocols
* Security controls

Operational validation should involve engineering and operations personnel.

---

# Roles and Responsibilities

Typical participants include:

* Operations
* Automation Engineers
* OT Security
* SOC
* IT
* Management
* Safety Personnel
* Vendors (where appropriate)

Responsibilities should be defined before incidents occur.

---

# Communication

Communication plans should define:

* Escalation paths
* Decision authority
* Regulatory notification
* Vendor communication
* Executive reporting
* Internal updates

Communication should remain structured and documented throughout the incident.

---

# Evidence Preservation

Organizations should preserve:

* Logs
* Configuration snapshots
* Memory captures (where appropriate)
* Network captures
* PLC diagnostics
* Session recordings
* System timelines

Evidence preservation supports forensic analysis and legal requirements.

---

# Lessons Learned

Every incident should conclude with a structured review.

Recommended topics include:

* Root cause
* Detection effectiveness
* Response effectiveness
* Communication
* Recovery performance
* Architecture improvements
* Policy updates
* Training needs

Lessons learned should improve future resilience.

---

# Relationship with Backup

Backup enables:

* Configuration restoration
* PLC recovery
* SCADA recovery
* Database restoration
* Certificate recovery

Backup supports Incident Response but does not replace it.

---

# Relationship with Disaster Recovery

Incident Response manages the incident.

Disaster Recovery restores operational capability.

The two disciplines should be coordinated.

---

# Relationship with MITRE ATT&CK

MITRE ATT&CK helps responders:

* Understand attacker behavior.
* Predict likely next actions.
* Prioritize containment.
* Improve detection.

Behavioral analysis supports effective response.

---

# Relationship with IEC 62443

Incident Response supports several IEC 62443 concepts including:

* Security Monitoring
* System Integrity
* Recovery
* Continuous Improvement
* Operational Security

Incident Response should be integrated into the organization's cybersecurity management system.

---

# Monitoring

Recommended monitoring includes:

* Incident timelines
* Detection quality
* Response times
* Containment effectiveness
* Recovery duration
* Recurring incidents
* Lessons learned implementation
* Improvement actions

Response metrics support continual improvement.

---

# Common Design Mistakes

Typical mistakes include:

* Disconnecting systems without understanding process impact.
* Ignoring safety considerations.
* No Incident Response Plan.
* No recovery validation.
* No evidence preservation.
* No communication plan.
* No tabletop exercises.
* Treating OT incidents as standard IT incidents.

---

# Architect Notes

One of the most common misconceptions is:

> "The fastest containment is always the best containment."

In OT, uncontrolled containment may create greater operational risk than the cyber incident itself.

Architects should instead ask:

* Will containment affect safety?
* Will containment destabilize the process?
* Can monitoring temporarily replace isolation?
* Is the attacker actively manipulating the process?
* Which action minimizes total organizational risk?

Successful Incident Response balances cybersecurity with safe industrial operation.

---

# Decision Support

Before initiating containment, determine:

1. Is human safety affected?
2. Is the industrial process stable?
3. Which assets are compromised?
4. Which systems can safely be isolated?
5. Are backups available and validated?
6. Which stakeholders must be informed?
7. How will recovery be validated?
8. Which improvements should follow the incident?

---

# Practical Examples

### Example 1 – Compromised Engineering Workstation

Recommended approach:

* Disable privileged account.
* Preserve forensic evidence.
* Isolate the workstation if operationally safe.
* Validate PLC integrity.
* Monitor engineering communications.
* Restore from a trusted baseline if required.

---

### Example 2 – Suspected Ransomware on SCADA Server

Recommended approach:

* Assess operational impact.
* Verify process stability.
* Prevent lateral movement.
* Preserve logs.
* Prepare validated backups.
* Restore services following approval and verification.

---

### Example 3 – Unauthorized PLC Logic Modification

Recommended approach:

* Confirm operator safety.
* Verify current process state.
* Compare logic with approved baseline.
* Restore approved program if appropriate.
* Document all actions.
* Review access controls and engineering authentication.

---

# AI Guidance

When discussing Incident Response:

* Explain that OT Incident Response prioritizes safety, environmental protection and operational continuity before cybersecurity containment.
* Recommend structured preparation, defined roles and tested procedures.
* Encourage coordination between operations, engineering, cybersecurity and management.
* Distinguish containment from eradication and recovery.
* Recommend preserving evidence whenever operationally feasible.
* Integrate Backup, Disaster Recovery, MITRE ATT&CK and Risk-Based Security into response planning.
* Avoid recommending disruptive containment actions without considering their effect on industrial processes.

---

# Sources / Grounding

* **NIST SP 800-61** (Computer Security Incident Handling Guide) — IR lifecycle.
* **IEC 62443-2-1** (IACS security programme) and **NIST SP 800-82 Rev. 3** (OT-specific incident response).

> These are foundational references; defer to the current standard/catalogue for authoritative, up-to-date detail.

---

# Related Documents

* [Risk-Based-Security.md](../03-Architecture/Risk-Based-Security.md)
* [Defense-in-Depth.md](../03-Architecture/Defense-in-Depth.md)
* [Backup.md](Backup.md)
* [Disaster-Recovery.md](Disaster-Recovery.md)
* [Change-Management.md](Change-Management.md)
* [MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md)
* [ICS-Kill-Chain.md](../07-Threat-Intelligence/ICS-Kill-Chain.md)
* [Monitoring.md](Monitoring.md)
* [Logging.md](Logging.md)
* [Asset-Management.md](Asset-Management.md)
* [IEC62443.md](../02-Standards/IEC62443.md)
* [NIS2.md](../01-Legislation/NIS2.md)

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
| 1.1.0   | 2026-07-01 | Converted to YAML front matter (category: Operations) and semver; fixed links (IEC62443-Overview.md → IEC62443.md); added Sources/Grounding (NIST SP 800-61, IEC 62443-2-1); markdown Related Documents |
| 1.1.1 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings |
