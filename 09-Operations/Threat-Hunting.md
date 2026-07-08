---
id: threat-hunting
title: "Threat Hunting"
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
  Proactive, hypothesis-driven investigation in OT telemetry beyond automated detection: hunting
  hypotheses grounded in ATT&CK for ICS and the ICS Kill Chain, data sources, cadence, a maturity
  model, and the relationship to Monitoring and Detection Engineering.
keywords: [threat hunting, proaktivní vyhledávání hrozeb, hypothesis-driven, ATT&CK for ICS, hunting maturity model, engineering workstation, OT telemetry]
---

# Purpose

Threat Hunting is the **proactive, human-led, hypothesis-driven** search for adversary activity that automated detection has not — and may never — surface. Where [Monitoring.md](Monitoring.md) is the continuous, largely automated validation of the trusted state, and [Detection-Engineering.md](Detection-Engineering.md) is the discipline of building and maintaining the rules that automate detection, Threat Hunting is what happens **between** those rules: an analyst forming a specific, testable hypothesis about how an adversary might be present, and searching the actual telemetry to confirm or refute it.

Threat Hunting exists because detection engineering can only automate what is already known to look for. In OT specifically — where attacker dwell time before physical consequence has historically been long, and where the highest-value assets (engineering workstations, safety systems) are exactly the ones an attacker studies carefully before acting — the gap between "what our rules catch" and "what is actually happening" is where the most consequential compromises hide.

---

# Scope

This document covers: hunting philosophy and hypothesis formation, OT-specific hunting hypotheses grounded in [MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md) and [ICS-Kill-Chain.md](../07-Threat-Intelligence/ICS-Kill-Chain.md), data sources, cadence, and a maturity model. It assumes [Logging.md](Logging.md) and [SIEM.md](SIEM.md) are already producing the evidence base a hunt searches through — threat hunting cannot compensate for missing telemetry, only for gaps in what existing telemetry has been asked to reveal.

---

# Threat Hunting vs. Monitoring vs. Detection Engineering

These three disciplines are frequently conflated; keep them distinct:

| Discipline | Question it answers | Trigger |
|---|---|---|
| **Monitoring** ([Monitoring.md](Monitoring.md)) | "Did a known-bad pattern occur?" | Continuous, automated, rule-driven |
| **Detection Engineering** ([Detection-Engineering.md](Detection-Engineering.md)) | "How do we automate detecting this pattern reliably?" | Building/tuning rules, ongoing discipline |
| **Threat Hunting** (this document) | "Is there something present that no rule would have caught?" | Periodic, analyst-initiated, hypothesis-driven |

A mature security programme needs all three. Hunting frequently *produces* the hypotheses that Detection Engineering later automates into a standing rule — a successful hunt that finds nothing this time still has value if it validates (or improves) future detection coverage.

---

# Hunting Philosophy

* **Assume presence, then look for evidence** — the hunting mindset inverts monitoring's "alert me if X happens" into "assume X may already be happening; go look." This is uncomfortable and is exactly the point — comfortable assumptions are what undetected compromises rely on.
* **Hypothesis-driven, not exploratory browsing** — an effective hunt starts with a specific, falsifiable statement ("if an adversary had engineering-workstation access, we would see X in Y telemetry over the last Z days") rather than open-ended log review, which does not scale and does not produce repeatable results.
* **Behavioural, not signature-based** — hunting looks for *patterns of activity* (unusual session timing, an unfamiliar sequence of otherwise-legitimate actions) rather than known-bad indicators, which is exactly what makes it complementary to signature/rule-based monitoring rather than redundant with it.

---

# OT-Specific Hunting Hypotheses

Ground hunts in the attacker behaviour models this handbook already documents rather than inventing hypotheses from scratch:

* **Engineering-workstation anomalies** — logins outside expected hours or from unexpected source hosts; engineering-tool sessions with no corresponding approved change record; new or modified project files with no matching change ticket ([ICS-Kill-Chain.md](../07-Threat-Intelligence/ICS-Kill-Chain.md) → Engineering Access; every `08-Technologies` platform document's engineering-governance sections describe what "normal" looks like to hunt against).
* **Process-understanding behaviour** — unusual read-heavy activity against controller variables, PLC project downloads with no corresponding change window, or historian/OPC queries covering an unusually broad tag scope for the requesting identity ([ICS-Kill-Chain.md](../07-Threat-Intelligence/ICS-Kill-Chain.md) → Process Understanding; compare [PI-System.md](../08-Technologies/AVEVA/PI%20System/PI-System.md) § 2.2's estate-wide reconnaissance risk).
* **Lateral movement toward OT** — authentication activity, especially privileged or service-account activity, moving from IT-adjacent systems toward the Industrial DMZ or OT zones with no corresponding approved access request.
* **Persistence mechanisms** — new scheduled tasks, new services, new local/service accounts on engineering workstations, jump hosts, or OPC aggregation servers ([Kepware-Server.md](../08-Technologies/PTC/Kepware%20Server/Kepware-Server.md), [dataFEED-OPC-Suite.md](../08-Technologies/Softing/dataFEED%20OPC%20Suite/dataFEED-OPC-Suite.md)) that were not provisioned through [Identity-Management.md](../05-Identity/Identity-Management.md) processes.
* **Communication-loss patterns as cover** — for wide-area/telemetry systems specifically, patterns of "site offline" that correlate with anomalous local events rather than genuine WAN/carrier issues ([GeoSCADA.md](../08-Technologies/Schneider%20Electric/GeoSCADA/GeoSCADA.md) § 2.2, § 7 — a hunting hypothesis this handbook's own platform documentation explicitly motivates).
* **Alarm and safety-adjacent manipulation** — alarm suppression/shelving patterns, or configuration changes near safety-relevant boundaries, with no matching Management of Change record ([Change-Management.md](Change-Management.md) → MoC; every SCADA/historian platform document's monitoring section names alarm-path manipulation as a detection priority — hunting validates that the priority is actually covered).
* **OPC/protocol-level anomalies** — unexpected client certificates, security-policy downgrade attempts, or new source hosts against OPC UA/DA servers ([OPC-UA.md](../04-Network/OPC-UA.md) § 11) that a standing rule may not yet cover for a specific server.

---

# Data Sources

A hunt is only as good as the telemetry available to search. Prioritise, consistent with [Logging.md](Logging.md) and [Monitoring.md](Monitoring.md):

* Engineering-workstation authentication and session logs; PLC/controller programming and mode-change events.
* Identity provider logs (authentication, privilege changes, new accounts) across both IT-adjacent and OT-domain identity infrastructure.
* Network flow data and firewall logs at every documented conduit ([Network-Segmentation.md](../04-Network/Network-Segmentation.md)).
* OPC UA/DA server logs (certificate events, session activity — [OPC-UA.md](../04-Network/OPC-UA.md)).
* Historian/SCADA command and configuration-change audit trails.
* Endpoint telemetry (EDR where deployed) on engineering workstations, jump hosts, and OPC/data-aggregation servers.

Where a hunting hypothesis cannot be tested because the required telemetry does not exist, that gap is itself a finding — feed it back into [Logging.md](Logging.md)/[Monitoring.md](Monitoring.md) as a coverage improvement, not a reason to abandon the hypothesis.

---

# Cadence

Threat hunting is periodic and deliberate, not continuous (that is monitoring's job). Recommended cadence:

* **Scheduled hunts** — a regular cycle (commonly monthly or quarterly, scaled to organisational maturity and resourcing) covering a rotating set of hypotheses across the estate.
* **Triggered hunts** — following a relevant industry incident, a new [CISA-KEV.md](../06-Vulnerability-Management/CISA-KEV.md)/threat-intelligence development affecting deployed technology, a significant [Change-Management.md](Change-Management.md) event, or a near-miss/incident elsewhere in the organisation.
* **Hunts informed by [Vulnerability-Management.md](Vulnerability-Management.md)** — a newly disclosed vulnerability in deployed technology warrants a targeted hunt for exploitation indicators, independent of and prior to patching.

---

# Hunting Maturity Model

A useful way to assess and grow a hunting programme:

1. **Ad hoc** — hunts happen occasionally, reactively, with no defined hypothesis or documented method.
2. **Hypothesis-led, manual** — defined hypotheses (as in the section above), executed through manual log/data review.
3. **Tooled and repeatable** — hunts use saved queries/playbooks against the SIEM ([SIEM.md](SIEM.md)), are documented, and are repeatable by more than one analyst.
4. **Intelligence-driven and measured** — hunts are systematically derived from current threat intelligence and [MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md) technique coverage gaps, findings are tracked to remediation, and hunt results feed [Detection-Engineering.md](Detection-Engineering.md) to close coverage gaps permanently.

Most OT security programmes should aim for level 3 as a baseline, with level 4 as the target for high-consequence estates.

---

# Relationships

* **[Monitoring.md](Monitoring.md)** — the continuous, automated counterpart; hunting covers what monitoring's existing rules do not.
* **[Detection-Engineering.md](Detection-Engineering.md)** — successful hunts frequently become new detection rules, closing the gap permanently rather than repeating the same manual hunt indefinitely.
* **[Logging.md](Logging.md)**, **[SIEM.md](SIEM.md)** — the evidence base every hunt depends on.
* **[MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md)**, **[ICS-Kill-Chain.md](../07-Threat-Intelligence/ICS-Kill-Chain.md)** — the behavioural models hunting hypotheses should be grounded in.
* **[Incident-Response.md](Incident-Response.md)** — a confirmed hunt finding transitions directly into the incident-response process.
* **[Vulnerability-Management.md](Vulnerability-Management.md)** — new disclosures motivate targeted hunts independent of patch timelines.

---

# Common Mistakes

* Open-ended "let's look through the logs" sessions with no defined hypothesis — this does not scale and produces inconsistent results.
* Hunting only IT-style hypotheses (credential theft, malware signatures) while ignoring OT-specific behaviours (engineering access patterns, alarm manipulation, comms-loss cover).
* No documentation of what was hunted, what was found, and what telemetry gaps were discovered — every hunt's institutional value is lost.
* Treating a "found nothing" hunt as wasted effort, rather than as validated coverage (or a prompt to improve telemetry where the hypothesis genuinely couldn't be tested).
* Confusing threat hunting with routine monitoring/alert triage — they are complementary but distinct disciplines with different cadence and mindset.
* Hunting findings never fed back into Detection Engineering, so the same gap is manually re-discovered on every future hunt.

---

# Decision Support

1. Does the hunting programme use defined, falsifiable hypotheses rather than open-ended log review?
2. Are hypotheses grounded in OT-specific attacker behaviour (ATT&CK for ICS, the ICS Kill Chain), not only generic IT threat patterns?
3. Is there sufficient telemetry to actually test each hypothesis — and are gaps fed back into logging/monitoring improvement?
4. Is hunting cadence defined (scheduled plus triggered by intelligence/incidents), not purely ad hoc?
5. Are hunt results documented and tracked, including "found nothing but validated coverage" outcomes?
6. Do confirmed findings flow into Incident Response, and do recurring gaps flow into Detection Engineering as new standing rules?
7. Where does the organisation sit on the hunting maturity model, and is there a plan to advance it?

---

# AI Guidance

* Distinguish Threat Hunting clearly from Monitoring (continuous/automated) and Detection Engineering (building the rules); explain hunting as the hypothesis-driven human search for what neither of those currently catches.
* Ground every suggested hunting hypothesis in this handbook's own OT-specific attacker-behaviour material (ATT&CK for ICS, the ICS Kill Chain, and the threat-model sections of relevant platform documents) rather than generic IT hunting playbooks.
* Recommend documenting hypotheses, methods and results, including telemetry-gap findings, so hunts are repeatable and institutionally cumulative.
* Recommend that successful/recurring hunt findings be converted into standing Detection Engineering rules rather than re-hunted manually every cycle.
* Frame "found nothing" as a valid, useful outcome (validated coverage) rather than wasted effort, provided the hypothesis was genuinely testable against available telemetry.

---

# Sources / Grounding

* **MITRE ATT&CK for ICS** — the behavioural technique model hunting hypotheses should map to. See [MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md).
* **SANS ICS Cyber Kill Chain** — attack-lifecycle phases informing OT-specific hunting hypotheses. See [ICS-Kill-Chain.md](../07-Threat-Intelligence/ICS-Kill-Chain.md).
* **NIST SP 800-82 Rev. 3** and **NIST SP 800-61** — OT security monitoring and incident-handling context.

> Foundational references; defer to current threat-intelligence sources and the organisation's own hunting playbooks for operational detail.

---

# Related Documents

* [Monitoring.md](Monitoring.md)
* [Detection-Engineering.md](Detection-Engineering.md)
* [Logging.md](Logging.md)
* [SIEM.md](SIEM.md)
* [Incident-Response.md](Incident-Response.md)
* [Vulnerability-Management.md](Vulnerability-Management.md)
* [MITRE-ATTACK-ICS.md](../07-Threat-Intelligence/MITRE-ATTACK-ICS.md)
* [ICS-Kill-Chain.md](../07-Threat-Intelligence/ICS-Kill-Chain.md)
* [Identity-Management.md](../05-Identity/Identity-Management.md)
* [OPC-UA.md](../04-Network/OPC-UA.md)
* [Network-Segmentation.md](../04-Network/Network-Segmentation.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-07 | Initial release — new 09-Operations document resolving the dangling `Threat-Hunting.md` reference from Logging.md; defines hunting philosophy and its distinction from Monitoring and Detection Engineering, OT-specific hunting hypotheses grounded in ATT&CK for ICS/the ICS Kill Chain and this corpus's own platform-document threat models (engineering access, process understanding, comms-loss-as-cover, alarm manipulation), data sources, cadence and a four-level hunting maturity model |
