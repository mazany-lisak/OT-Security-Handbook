---
title: Configuration Management
category: Operations
version: 1.0.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-07-01
review_cycle: Annual
---

# Purpose

Configuration Management is the discipline of establishing, recording, controlling and verifying the configurations of an Industrial Automation and Control System (IACS) and its components against a known, trusted **baseline**.

Within Operational Technology (OT), configuration management provides the "known-good" reference that makes drift detectable, recovery trustworthy and change auditable. It is the record-keeping and enforcement counterpart to [Change-Management.md](Change-Management.md).

---

# Scope

This document covers baselines, versioning, drift detection and remediation, the configuration record (CMDB), and OT-specific considerations for controllers, firmware and network devices. Authorisation of changes is in [Change-Management.md](Change-Management.md); preservation of configurations is in [Backup.md](Backup.md).

---

# Security Philosophy

You cannot detect drift, restore trust or prove integrity without a defined baseline. Configuration Management assumes the environment *will* drift — through change, maintenance, vendor activity or compromise — and provides the reference against which deviation is measured and corrected.

---

# Baselines

A configuration baseline is the approved, documented "golden" state of a system: hardware and firmware versions, software and patch levels, security settings (hardening), network configuration, accounts and services, and PLC/engineering project versions. A baseline is established at commissioning and **re-baselined only through approved change** (see [Change-Management.md](Change-Management.md)).

---

# Versioning

Configurations and industrial projects (PLC logic, HMI/SCADA projects, network device configs) should be **version-controlled** — with author, timestamp, approval reference and the ability to diff against the previous version. Versioning turns "what changed, when and by whom" from guesswork into evidence.

---

# Drift Detection and Remediation

```text
Baseline  →  Periodic/continuous comparison  →  Drift detected
                                                     ↓
                                    Investigate (authorised change? incident?)
                                                     ↓
                        Remediate: restore baseline OR re-baseline via change control
```

Unexplained drift is a security signal — it may indicate an unauthorised change or a compromise. Drift alerts should feed [Monitoring.md](Monitoring.md) / [SIEM.md](SIEM.md).

---

# OT-Specific Considerations

* **Controllers & firmware.** Track PLC/RTU firmware and hardware configuration; unexpected firmware change is high-signal.
* **Vendor tooling.** Many configs live in vendor engineering tools; capture exports as version-controlled artefacts.
* **Network devices.** Firewall, switch, router and VPN configurations are part of the baseline.
* **Change-safe capture.** Prefer passive/export-based capture that does not disturb running processes.

---

# Relationships

* **[Change-Management.md](Change-Management.md)** — authorises change; Configuration Management records the resulting baseline and detects deviations from it.
* **[Backup.md](Backup.md)** — configurations and projects in the baseline are preserved as recoverable backups.
* **[Disaster-Recovery.md](Disaster-Recovery.md)** / **[Recovery-Testing.md](Recovery-Testing.md)** — recovery restores systems to a trusted baseline; testing validates that it can.
* **[Monitoring.md](Monitoring.md)** / **[SIEM.md](SIEM.md)** — consume drift and configuration-change events.
* **[Asset-Management.md](Asset-Management.md)** — the asset inventory and the configuration record are complementary (assets vs their state).

---

# Common Mistakes

* No defined baseline; "the running system is the documentation".
* Configurations not version-controlled; no diff/audit trail.
* Drift never checked, or checked but never remediated.
* Baselines updated informally, outside change control.
* Omitting network-device and firmware configurations from the baseline.

---

# Decision Support

1. Is there an approved baseline for each critical asset?
2. Are configurations and projects version-controlled with an audit trail?
3. How is drift detected, and how quickly?
4. Is drift investigated as a potential security event?
5. Are baselines updated only through [Change-Management.md](Change-Management.md)?

---

# AI Guidance

* Explain Configuration Management as baseline definition, versioning and drift detection — the record/enforcement counterpart to Change Management.
* Treat unexplained drift as a security signal routed to monitoring/SIEM.
* Require baselines to change only through approved change control.
* Include controllers, firmware and network devices, captured in a change-safe way.

---

# Sources / Grounding

* **NIST SP 800-128** (security-focused configuration management) and **NIST SP 800-82 Rev. 3** (OT).
* **IEC 62443** (configuration management, system integrity).
* **ISO/IEC 27001** (configuration/asset control) as a general-IT reference, adapted to OT.

> Foundational references; defer to the current standards for authoritative detail.

---

# Related Documents

* [Change-Management.md](Change-Management.md)
* [Backup.md](Backup.md)
* [Disaster-Recovery.md](Disaster-Recovery.md)
* [Recovery-Testing.md](Recovery-Testing.md)
* [Monitoring.md](Monitoring.md)
* [SIEM.md](SIEM.md)
* [Asset-Management.md](Asset-Management.md)
* [Patch-Management.md](Patch-Management.md)
* [IEC62443.md](IEC62443.md)
* [NIS2.md](NIS2.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-01 | Initial release — new 09-Operations document resolving the dangling `Configuration-Management.md` reference from DR/DRP/Recovery-Testing; defines baselines, versioning and drift detection as the record/enforcement counterpart to Change-Management.md; grounded in NIST SP 800-128/800-82 Rev. 3, IEC 62443 and ISO/IEC 27001 |
