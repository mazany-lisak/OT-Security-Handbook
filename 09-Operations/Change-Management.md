---
id: change-management
title: "Change Management"
category: Operations
layer: "09-Operations"
version: 1.0.1
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Governance wrapper for OT changes: change types (standard/normal/emergency), the change
  lifecycle with backup-before-change and rollback, maintenance windows, vendor coordination and
  the Management-of-Change intersection with process safety.
keywords: [change management, řízení změn, rollback, maintenance window, Management of Change, MoC, CAB]
---

# Purpose

Change Management is the controlled process of requesting, assessing, approving, implementing, validating and documenting changes to an Industrial Automation and Control System (IACS) and its supporting infrastructure.

Within Operational Technology (OT), unmanaged change is one of the leading causes of outages, configuration drift and security incidents. Change Management exists to ensure that every change is deliberate, reversible, validated and traceable — without compromising safety, availability or operational continuity.

---

# Scope

This document covers change types, the change lifecycle, OT-specific controls (maintenance windows, rollback, backup-before-change, vendor coordination), and the relationship to configuration, patch, backup and risk management. It is referenced throughout the handbook as the governance wrapper around operational changes.

---

# Security Philosophy

Most OT disruptions are self-inflicted — a well-intended change with an unforeseen consequence. Change Management assumes that any change can fail, and therefore requires that changes be **assessed before** implementation and **reversible after**. Discipline here has a greater impact on resilience than most individual technologies.

---

# Change Types

* **Standard** — pre-approved, low-risk, repeatable (e.g. a routine, tested update via an approved procedure).
* **Normal** — assessed and approved through the full workflow.
* **Emergency** — expedited for active exploitation or imminent operational risk; still documented, approved and reviewed after the fact.

Classifying a change sets the level of assessment and approval required.

---

# The Change Lifecycle

```text
Request
        ↓
Risk & Impact Assessment   (safety, availability, dependencies, security)
        ↓
Approval                   (change authority / CAB)
        ↓
Backup & Rollback Plan     (config/project backup before change)
        ↓
Test                       (representative environment where practical)
        ↓
Implement                  (within an approved maintenance window)
        ↓
Validate                   (functional, process stability, security controls)
        ↓
Update Baseline & Document (new trusted configuration)
        ↓
Close & Review
```

No change should proceed without an assessment, an approval and a rollback path.

---

# OT-Specific Controls

* **Safety first.** Assess whether the change can affect a safety function, an interlock or a deterministic control loop before anything else.
* **Maintenance windows.** Schedule changes into approved windows; treat 24/7 processes and limited windows as hard constraints.
* **Backup before change.** Capture configuration, PLC/engineering projects and firmware versions *before* implementation (see [Backup.md](Backup.md)).
* **Rollback plan.** Every change carries a tested way back to the last trusted state.
* **Vendor coordination.** Confirm supported/certified combinations; unsupported configurations raise operational risk.
* **Operator notification.** Operations must know what is changing, when, and what to watch.

---

# Management of Change (Process Safety)

In many industries, cybersecurity change intersects **Management of Change (MoC)** obligations under process-safety regimes (e.g. IEC 61511 functional safety, OSHA PSM). A change touching safety-related systems should follow both the cybersecurity change process and the applicable MoC process, with safety and operations sign-off.

---

# Relationships

* **[Configuration-Management.md](Configuration-Management.md)** — Change Management authorises changes; Configuration Management records and enforces the resulting baselines and detects drift.
* **[Patch-Management.md](Patch-Management.md)** — patches are a change; they flow through this process (test, backup, approve, validate).
* **[Backup.md](Backup.md)** — backup-before-change and rollback depend on validated backups.
* **[Disaster-Recovery.md](Disaster-Recovery.md)** / **[Disaster-Recovery-Plan.md](Disaster-Recovery-Plan.md)** — recovered systems become the new baseline only after validation and approval.
* **[Risk-Based-Security.md](../03-Architecture/Risk-Based-Security.md)** — the impact assessment weighs total organisational risk.
* **[OT-Lifecycle.md](../00-Core/OT-Lifecycle.md)** — change runs through the Operate/Maintain/Modernise phases.

---

# Common Mistakes

* Undocumented or "temporary" changes that are never reversed.
* No risk/impact assessment; no rollback plan.
* Changing production without a configuration or project backup.
* Skipping validation after implementation.
* Ignoring vendor certification / support constraints.
* Treating emergency changes as exempt from later documentation and review.

---

# Decision Support

1. What exactly is changing, and why?
2. Could it affect safety, a control loop or availability?
3. Which assets and dependencies are impacted?
4. Has it been tested; is there a rollback plan and a current backup?
5. Is it approved, and scheduled into a maintenance window?
6. How will success be validated, and the baseline updated?

---

# AI Guidance

* Explain Change Management as the governance wrapper that makes changes deliberate, reversible and traceable.
* Always require impact assessment, approval, backup-before-change and rollback.
* Prioritise safety and availability; respect maintenance windows and vendor certification.
* Link changes to Configuration Management (baselines/drift), Patch Management and Backup.
* Note the intersection with process-safety Management of Change for safety-related systems.

---

# Sources / Grounding

* **IEC 62443-2-1** (IACS security programme — change/patch management) and **NIST SP 800-82 Rev. 3** (OT).
* **ISO/IEC 27001** (change control) and **ITIL** (change enablement) as general-IT references, adapted to OT.
* **IEC 61511** / process-safety **Management of Change** for safety-related changes.

> Foundational references; defer to the current standards for authoritative detail.

---

# Related Documents

* [Configuration-Management.md](Configuration-Management.md)
* [Patch-Management.md](Patch-Management.md)
* [Backup.md](Backup.md)
* [Disaster-Recovery.md](Disaster-Recovery.md)
* [Disaster-Recovery-Plan.md](Disaster-Recovery-Plan.md)
* [Recovery-Testing.md](Recovery-Testing.md)
* [Risk-Based-Security.md](../03-Architecture/Risk-Based-Security.md)
* [Asset-Management.md](Asset-Management.md)
* [OT-Lifecycle.md](../00-Core/OT-Lifecycle.md)
* [IEC62443.md](../02-Standards/IEC62443.md)
* [NIS2.md](../01-Legislation/NIS2.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-01 | Initial release — new 09-Operations document resolving the pervasive dangling `Change-Management.md` reference; defines change types, the change lifecycle, OT-specific controls (maintenance windows, backup-before-change, rollback, vendor coordination) and the Management-of-Change intersection with process safety; grounded in IEC 62443-2-1, NIST SP 800-82 Rev. 3, ISO/IEC 27001/ITIL and IEC 61511 |
| 1.0.1 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings |
