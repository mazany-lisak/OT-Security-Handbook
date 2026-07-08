---
id: detection-engineering
title: "Detection Engineering"
category: Operations
layer: "09-Operations"
version: 1.0.0
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Designing, testing, tuning and maintaining detection rules mapped to ATT&CK for ICS: the
  detection lifecycle, coverage mapping, false-positive management, detection-as-code and
  validation, and the feedback loop from Threat Hunting and Incident Response.
keywords: [detection engineering, detekční inženýrství, detection rule, ATT&CK for ICS coverage, false positive, purple team, detection-as-code, SIEM tuning]
---

# Purpose

Detection Engineering is the discipline of **designing, implementing, testing and maintaining** the rules, analytics and correlation logic that turn raw telemetry into reliable alerts. Where [Monitoring.md](Monitoring.md) is the practice of watching and analysing what those rules produce, and [Threat-Hunting.md](Threat-Hunting.md) is the human-led search for what no rule yet catches, Detection Engineering is the **engineering discipline that closes the gap** — converting a validated hunting hypothesis, an incident lesson-learned, or a threat-intelligence development into a durable, tuned, tested standing detection.

A detection rule written once and never revisited degrades: the environment changes, the false-positive rate drifts, and the technique it was meant to catch evolves. This document treats detections as engineered artifacts with a lifecycle — not one-off SIEM queries.

---

# Scope

This document covers: the detection lifecycle, coverage mapping against [MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md), rule quality and false-positive management, detection-as-code practice, testing/validation methods (including purple teaming), and the feedback loops to and from Threat Hunting and Incident Response. It assumes [Logging.md](Logging.md) and [SIEM.md](SIEM.md) already exist as the platform detections are built on.

---

# The Detection Lifecycle

```text
Identify a detection need
   (hunting finding, incident lesson-learned, new ATT&CK technique, new asset/platform)
        ↓
Map to technique(s)         — which ATT&CK for ICS technique(s) does this address?
        ↓
Design the logic            — what telemetry, what pattern, what threshold?
        ↓
Implement                   — write the rule/query in the SIEM
        ↓
Test                        — against known-good and known-bad data; simulate if possible
        ↓
Tune                        — reduce false positives without losing true-positive coverage
        ↓
Deploy                      — to production monitoring, with an owner and documentation
        ↓
Validate periodically       — still firing correctly? still relevant? still tuned?
        ↓
Retire or revise            — when the underlying technique, asset or environment changes
```

No detection should skip the "test" and "tune" stages — an untested rule deployed directly to production is exactly how alert fatigue and missed true positives both begin.

---

# Coverage Mapping

Track which **ATT&CK for ICS** tactics and techniques ([MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md)) the organisation has standing detection coverage for, which have partial coverage, and which have none. A simple coverage matrix — techniques down one axis, detection status across the other — turns "do we detect X" from a guess into a documented, reviewable answer, and directly informs where the next detection-engineering or threat-hunting effort should focus.

Prioritise coverage using the same consequence-first logic as the rest of this handbook: techniques that lead toward engineering access, controller manipulation, or safety-system interaction ([ICS-Kill-Chain.md](../07-Threat-Intelligence/ICS-Kill-Chain.md) phases 6–9) warrant detection coverage before lower-consequence reconnaissance-stage techniques, when resourcing forces a choice.

---

# Rule Quality and False-Positive Management

* **Specificity over volume.** A detection that fires constantly on legitimate activity trains analysts to ignore it — which defeats its purpose the moment it matters. Tune for actionable specificity, not maximum trigger count.
* **Document the intended true positive.** Every rule should have a written statement of exactly what behaviour it is meant to catch and why that behaviour is significant — this is what makes tuning possible later without guessing the original intent.
* **Track false-positive rate over time.** A rule whose false-positive rate climbs after an environment change (new legitimate integration, new asset, changed operational pattern) needs retuning, not silent disabling.
* **Context enrichment reduces false positives more than threshold tuning does.** A write from an unrecognised host is suspicious; the same write from the known, inventoried SCADA poller ([Asset-Management.md](Asset-Management.md)) is not — enrich detections with asset/identity context wherever the SIEM allows it, rather than relying on raw thresholds alone.
* **Every deployed rule has a named owner** responsible for its ongoing tuning and periodic validation — an orphaned rule is an unmaintained rule.

---

# Detection-as-Code

Where the SIEM/monitoring platform supports it, manage detection logic as **version-controlled code**, not ad hoc UI-configured rules:

* Store rule definitions in version control with change history, matching the discipline already applied to configuration baselines ([Configuration-Management.md](Configuration-Management.md)).
* Review changes to detection logic through the same change-control process as any other production change ([Change-Management.md](Change-Management.md)) — a detection rule is a security control, and disabling or weakening one should require the same scrutiny as disabling a firewall rule.
* Test rule changes against a representative telemetry sample before production deployment, exactly as code changes are tested before deployment elsewhere in the organisation.

This practice scales detection engineering beyond what any individual analyst can hold in memory, and makes "why did this rule change" an answerable question months later.

---

# Testing and Validation

* **Test against known-bad data** — historical incident data, threat-intelligence-derived samples, or simulated technique execution — to confirm a new rule actually fires on the behaviour it claims to detect.
* **Test against known-good data** — a representative sample of normal operational activity — to estimate false-positive rate before production deployment.
* **Purple teaming** — periodic collaborative exercises where a red/adversary-emulation activity is run specifically to validate that the corresponding detections actually fire, closing the loop between offensive testing and defensive coverage. In OT, scope any active technique emulation carefully against safety and availability impact — never test against live production process equipment without the same rigor applied to any other change ([Change-Management.md](Change-Management.md)); prefer representative test environments where available.
* **Periodic revalidation** — re-test standing rules against current telemetry on a scheduled cadence; an environment change (new platform, new integration, a firmware update that changes log format) can silently break a previously-working detection.

---

# The Feedback Loop

Detection Engineering does not operate in isolation:

* **From Threat Hunting** ([Threat-Hunting.md](Threat-Hunting.md)) — a hunting hypothesis confirmed as a real gap becomes a detection-engineering backlog item; the goal is to never manually hunt for the same pattern twice.
* **From Incident Response** ([Incident-Response.md](Incident-Response.md)) — every incident's lessons-learned review should explicitly ask "would a detection have caught this earlier, and if not, can one be built now."
* **From Vulnerability Management** ([Vulnerability-Management.md](Vulnerability-Management.md)) — a newly disclosed, actively exploited vulnerability ([CISA-KEV.md](../06-Vulnerability-Management/CISA-KEV.md)) in deployed technology often has known exploitation indicators worth a dedicated detection, independent of and prior to patching.
* **To Monitoring** ([Monitoring.md](Monitoring.md)) — every validated, tuned rule becomes part of the standing monitoring capability that discipline operates day to day.

---

# Relationships

* **[Monitoring.md](Monitoring.md)** — consumes the detections this discipline builds; the operational, day-to-day analysis practice.
* **[Threat-Hunting.md](Threat-Hunting.md)** — the source of many new detection requirements; read together as complementary halves of proactive/reactive coverage.
* **[Logging.md](Logging.md)**, **[SIEM.md](SIEM.md)** — the telemetry and platform every detection is built on.
* **[MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md)** — the coverage-mapping framework.
* **[Incident-Response.md](Incident-Response.md)** — the consumer of triggered detections and the source of lessons-learned-driven new ones.
* **[Configuration-Management.md](Configuration-Management.md)**, **[Change-Management.md](Change-Management.md)** — the governance discipline detection-as-code borrows.

---

# Common Mistakes

* Rules copied from generic IT threat-detection content with no adaptation to OT-specific behaviour or ATT&CK for ICS techniques.
* No coverage mapping — nobody can answer "do we detect technique X" with confidence.
* Rules deployed directly to production with no testing, producing either alert-fatigue false positives or silent gaps.
* No named owner per rule, so tuning and validation never happen after initial deployment.
* Detection logic changes made informally, with no version control or change record.
* Purple-team/validation exercises never performed, so coverage claims are untested assumptions.
* Threat-hunting findings and incident lessons-learned never converted into standing detections — the same gap rediscovered repeatedly.

---

# Decision Support

1. Is there a documented mapping of detection coverage against ATT&CK for ICS techniques, prioritised by consequence?
2. Does every deployed rule have a documented intended true positive and a named owner?
3. Are new rules tested against both known-bad and known-good data before production deployment?
4. Is detection logic managed as version-controlled, change-controlled code where the platform supports it?
5. Are purple-team or equivalent validation exercises performed periodically to confirm claimed coverage actually fires?
6. Do confirmed threat-hunting findings and incident lessons-learned reliably convert into new standing detections?
7. Are existing rules periodically revalidated against current telemetry and environment state?

---

# AI Guidance

* Treat Detection Engineering as the discipline that builds and maintains what Monitoring runs day to day and Threat Hunting searches beyond — always distinguish the three clearly rather than using them interchangeably.
* Recommend mapping detection coverage explicitly to ATT&CK for ICS techniques, prioritised by the consequence-weighted phases in the ICS Kill Chain (engineering access, controller manipulation, safety interaction ahead of early reconnaissance).
* Insist every rule has a documented intended true positive and a named owner responsible for tuning.
* Recommend detection-as-code (version control, change review, pre-deployment testing) wherever the platform supports it, treating a detection rule as a security control subject to the same change discipline as any other.
* Recommend purple-teaming or equivalent validation to confirm claimed coverage actually fires, rather than trusting an untested rule inventory.
* Recommend explicit feedback loops: threat-hunting findings and incident lessons-learned should be tracked to a resulting detection, not left as one-off observations.

---

# Sources / Grounding

* **MITRE ATT&CK for ICS** — the technique-coverage framework this document's mapping practice is built on. See [MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md).
* **NIST SP 800-61** — incident-handling context informing the feedback loop from Incident Response.
* **NIST SP 800-82 Rev. 3** — OT security monitoring context.

> Foundational references; defer to the organisation's SIEM/detection-platform documentation and current threat-intelligence sources for operational detail.

---

# Related Documents

* [Monitoring.md](Monitoring.md)
* [Threat-Hunting.md](Threat-Hunting.md)
* [Logging.md](Logging.md)
* [SIEM.md](SIEM.md)
* [Incident-Response.md](Incident-Response.md)
* [Vulnerability-Management.md](Vulnerability-Management.md)
* [MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md)
* [ICS-Kill-Chain.md](../07-Threat-Intelligence/ICS-Kill-Chain.md)
* [Configuration-Management.md](Configuration-Management.md)
* [Change-Management.md](Change-Management.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-07 | Initial release — new 09-Operations document resolving the dangling `Detection-Engineering.md` reference from Logging.md; defines the detection lifecycle, ATT&CK for ICS coverage mapping prioritised by ICS Kill Chain consequence, rule quality and false-positive management, detection-as-code practice, testing/validation incl. purple teaming, and the feedback loops to/from Threat Hunting and Incident Response |
