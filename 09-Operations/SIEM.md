---
title: SIEM
category: Operations
version: 1.0.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-07-01
review_cycle: Annual
---

# Purpose

A Security Information and Event Management (SIEM) platform collects, normalises, correlates and analyses log and event data to detect threats, support investigations and provide accountability across an Industrial Automation and Control System (IACS) and its supporting IT.

Within Operational Technology (OT), a SIEM is the analytical layer that turns the raw evidence produced by [Logging.md](Logging.md) into actionable detections that support [Monitoring.md](Monitoring.md) and [Incident-Response.md](Incident-Response.md).

---

# Scope

This document covers SIEM concepts, OT-specific data sources and constraints, correlation and detection, and integration with logging, monitoring and incident response. Log generation is in [Logging.md](Logging.md); analysis strategy and detection priorities are in [Monitoring.md](Monitoring.md).

---

# Where the SIEM Fits

```text
Logging      → generates trustworthy evidence (the raw events)
        ↓
SIEM         → collects, normalises, correlates, detects (this document)
        ↓
Monitoring   → human + automated analysis, triage, threat hunting
        ↓
Incident Response → containment, eradication, recovery
```

The SIEM does not replace logging or monitoring — it connects them. Poor-quality logs and unsynchronised clocks undermine any SIEM.

---

# Core Functions

* **Collection** — ingest logs from IT, OT and security sources.
* **Normalisation** — parse heterogeneous formats into a common schema.
* **Correlation** — combine events across sources to reveal multi-step activity.
* **Detection** — signatures, correlation rules, behavioural analytics and threat-intel matching.
* **Alerting & triage** — prioritised, deduplicated, context-enriched alerts.
* **Investigation & reporting** — search, timelines, dashboards, audit evidence.

---

# OT-Specific Considerations

* **Passive-first ingestion.** Prefer log forwarding, taps and span ports; avoid active polling that could disturb deterministic control traffic.
* **Industrial data sources.** Industrial firewalls, OT IDS, PLC/controller diagnostics, engineering workstations, SCADA/HMI, historians, OPC UA security events, network flows.
* **Protocol awareness.** Value comes from OT-aware parsing (Modbus, PROFINET, EtherNet/IP, DNP3, IEC 61850, OPC UA) — see [OT-Protocols.md](OT-Protocols.md).
* **Data residency & connectivity.** Cloud SIEM may conflict with isolation or regulatory constraints; consider on-prem collection with controlled forwarding, or an OT-sited collector.
* **Time synchronisation.** Reliable NTP and consistent time zones are prerequisites for correlation.
* **Volume vs value.** Tune for high-value OT events (PLC programming, firmware changes, engineering sessions, privilege use) rather than maximum ingestion.

---

# High-Value Detections

* PLC programming / logic downloads / firmware changes
* New privileged accounts; failed/anomalous MFA
* Engineering-workstation logins and sessions
* Vendor / remote-access sessions crossing the IT↔OT boundary
* Firewall policy changes; new communication paths
* Certificate replacement; OPC UA secure-channel failures
* Configuration drift ([Configuration-Management.md](Configuration-Management.md))

Detections should map to attacker techniques — see [MITRE-ATTACK-ICS.md](MITRE-ATTACK-ICS.md).

---

# Relationships

* **[Logging.md](Logging.md)** — supplies the evidence the SIEM consumes.
* **[Monitoring.md](Monitoring.md)** — the analytical and threat-hunting practice the SIEM enables.
* **[Incident-Response.md](Incident-Response.md)** — the SIEM provides detection, timelines and evidence.
* **[MITRE-ATTACK-ICS.md](MITRE-ATTACK-ICS.md)** / **[ICS-Kill-Chain.md](ICS-Kill-Chain.md)** — detection engineering targets.

---

# Common Mistakes

* Deploying a SIEM without an asset inventory or a logging strategy.
* Ingesting only IT logs; omitting engineering workstations and OT sources.
* Alert overload with no tuning, triage or ownership.
* No time synchronisation; unreliable correlation.
* Active collection methods that disturb control networks.
* Treating the SIEM as a compliance checkbox rather than a detection capability.

---

# Decision Support

1. Which high-value OT events must be detected?
2. Which sources already produce them ([Logging.md](Logging.md))?
3. Passive vs active collection — any control-network impact?
4. On-prem, OT-sited or cloud — given isolation and regulation?
5. Which detections map to realistic attacker techniques?
6. How are alerts triaged, owned and improved?

---

# AI Guidance

* Explain the SIEM as the correlation/detection layer between Logging (evidence) and Monitoring (analysis).
* Prioritise passive, OT-aware collection; protect determinism and availability.
* Emphasise engineering workstations, privileged identities and controller activity as key sources.
* Map detections to MITRE ATT&CK for ICS; integrate with Incident Response.
* Optimise for actionable detections, not ingestion volume.

---

# Sources / Grounding

* **NIST SP 800-92** (log management) and **NIST SP 800-82 Rev. 3** (OT security monitoring).
* **IEC 62443** (security monitoring, accountability).
* **MITRE ATT&CK for ICS** — detection-engineering reference.

> Foundational references; defer to current guidance for authoritative detail.

---

# Related Documents

* [Logging.md](Logging.md)
* [Monitoring.md](Monitoring.md)
* [Incident-Response.md](Incident-Response.md)
* [MITRE-ATTACK-ICS.md](MITRE-ATTACK-ICS.md)
* [ICS-Kill-Chain.md](ICS-Kill-Chain.md)
* [Asset-Management.md](Asset-Management.md)
* [Configuration-Management.md](Configuration-Management.md)
* [OT-Protocols.md](OT-Protocols.md)
* [IEC62443.md](IEC62443.md)
* [NIS2.md](NIS2.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-01 | Initial release — new 09-Operations document resolving the dangling `SIEM.md` reference from Logging.md and Monitoring.md; defines the collection/normalisation/correlation/detection layer, OT-specific (passive, protocol-aware, data-residency) considerations and high-value detections; grounded in NIST SP 800-92/800-82 Rev. 3, IEC 62443 and MITRE ATT&CK for ICS |
