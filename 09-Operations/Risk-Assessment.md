---
id: risk-assessment
title: "Risk Assessment"
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
  Operational execution of OT risk assessment per IEC 62443-2-1/-3-2: the SLRA process from
  zone/conduit scoping through Cybersecurity Requirements Specification, the risk register,
  reassessment triggers, roles, and a worked example — the practice this handbook's risk
  principles and risk-based strategy are applied through.
keywords: [risk assessment, SLRA, řízení rizik, risk register, Target Security Level, Cybersecurity Requirements Specification, CRS, IEC 62443-3-2, reassessment]
---

# Purpose

Risk Assessment is the **operational execution** of the risk principles and strategy defined elsewhere in this handbook: it is the recurring, structured activity of actually evaluating risk for real zones, conduits and assets, and producing the artifacts — Target Security Levels, a Cybersecurity Requirements Specification, a maintained risk register — that downstream architecture and control decisions depend on.

This document deliberately does not repeat what a risk *is*, how the risk cycle works conceptually, or how residual risk should be governed — that is [Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md). It does not repeat how to prioritise and select controls once risk is known — that is [Risk-Based-Security.md](../03-Architecture/Risk-Based-Security.md). This document is the **process**: who runs an assessment, in what sequence, producing what artifacts, reviewed how often, using which method — the IEC 62443-3-2 System Level Risk Assessment (SLRA) as practised.

---

# Scope

This document describes: the SLRA process sequence, roles and responsibilities, the risk register as a living artifact, reassessment triggers, and a worked example. It assumes familiarity with [Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md) (the risk cycle, consequence categories, treatment options) and [Security-Decision-Framework.md](../00-Core/Security-Decision-Framework.md) (where this activity sits in the overall decision sequence — Step 4). Vendor-specific risk-assessment tooling is intentionally excluded.

---

# Why a Dedicated Process Matters

A risk assessment performed once, informally, at project kickoff and never revisited is not risk management — it is a snapshot that ages the moment the environment changes. IEC 62443-2-1 requires risk assessment as an ongoing element of the IACS security programme, and IEC 62443-3-2 defines the specific method (the SLRA) for deriving zones, conduits and Target Security Levels from that assessment. Treating this as a formal, repeatable, documented process — rather than an occasional workshop — is what makes every downstream architecture decision in this handbook traceable back to an actual risk basis rather than an assumption.

---

# The SLRA Process (IEC 62443-3-2)

```text
1. Identify the System Under Consideration (SUC) and its boundary
        ↓
2. Perform an initial (high-level) risk assessment
        ↓
3. Partition the SUC into zones and conduits
        ↓
4. Perform a detailed risk assessment per zone/conduit
        ↓
5. Compare unmitigated risk against tolerable risk
        ↓
6. Assign Target Security Levels (SL-T) per zone/conduit
        ↓
7. Document the Cybersecurity Requirements Specification (CRS)
        ↓
8. Feed SL-T/CRS into architecture and control selection
```

**Step 1 — System Under Consideration.** Define scope explicitly: which assets, networks and physical boundaries are in scope for this assessment. An undefined boundary produces an assessment that quietly misses whatever falls outside it.

**Step 2 — Initial assessment.** A high-level pass across the whole SUC to identify where consequence is high enough to warrant detailed analysis, and where it clearly is not — this triages effort rather than treating every asset with equal depth.

**Step 3 — Zone and conduit partitioning.** Group assets by shared security requirements into zones, and identify the conduits between them, per [IEC62443.md](../02-Standards/IEC62443.md). This step is where Purdue-level trust-boundary thinking ([Purdue-Model.md](../02-Standards/Purdue-Model.md)) becomes the formal zone/conduit structure the rest of the assessment operates on.

**Step 4 — Detailed assessment per zone/conduit.** For each zone/conduit: identify threats, vulnerabilities, likelihood and consequence (using the OT consequence categories — safety, environmental, production, equipment — from [Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md)), and calculate unmitigated risk.

**Step 5 — Compare against tolerable risk.** Determine whether unmitigated risk for each zone/conduit exceeds what the organisation considers tolerable — this comparison, not intuition, is what justifies the Target SL assigned next.

**Step 6 — Assign Target Security Levels.** Each zone/conduit receives an **SL-T** (Target Security Level, SL 0–4) reflecting the protection needed to bring risk to a tolerable level. This is the output every architecture document in this handbook (Network, Identity, and every 08-Technologies platform reference) expects as an input.

**Step 7 — Cybersecurity Requirements Specification (CRS).** Document the derived requirements per zone/conduit — this is the formal handoff artifact from risk assessment to architecture design ([Security-Decision-Framework.md](../00-Core/Security-Decision-Framework.md) Step 6).

**Step 8 — Feed forward.** Architecture, network segmentation, identity design and control selection all consume the SL-T/CRS output — risk assessment is not the end of the process, it is what makes the rest of the process risk-based rather than assumption-based.

---

# The Risk Register

A **risk register** is the living record of identified risks, their assessed level, assigned treatment (per [Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md) — reduce/transfer/avoid/accept), owner, and current status. For each entry, record at minimum: the asset/zone/conduit affected, the threat and vulnerability, unmitigated and residual risk, the treatment decision and its rationale, the accountable owner, and the review date.

**Residual risk entries require a named accepting authority** — an entry with no owner is not actually accepted, it is merely unaddressed. Review the register on the cadence below, not only when someone remembers to look at it.

---

# Reassessment Triggers

Risk is not static ([Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md)). Reassess — for the affected zone/conduit at minimum, for the whole SUC where the change is structural — when:

* New equipment is introduced or existing equipment is replaced/upgraded.
* Firmware or major software versions change materially.
* Network architecture changes — new conduits, new zones, new external connections.
* A new supplier or vendor gains access (remote or on-site).
* The production process changes in a way that alters consequence.
* A significant vulnerability, incident, or near-miss occurs, on this system or a comparable one elsewhere.
* On a defined periodic cycle regardless of other triggers (typically annually for the full SUC, more frequently for high-consequence zones) — treat this as a floor, not a ceiling.

---

# Roles and Responsibilities

* **Process/automation engineering** — process knowledge, consequence validation (a manipulation's real-world physical effect must be validated by people who understand the process, not assumed by security staff alone).
* **OT security/cybersecurity function** — threat and vulnerability analysis, facilitation of the SLRA method, risk register maintenance.
* **Operations** — operational-impact input, validation that proposed treatments are workable in practice.
* **Safety engineering** — mandatory input wherever safety consequence categories are involved; safety impact assessment should not be estimated by non-safety personnel.
* **Accountable risk owner (management)** — accepts residual risk, approves the SL-T/CRS, owns the register entries assigned to them.

A risk assessment run entirely by one discipline in isolation — security staff with no process-engineering input, or vice versa — reliably produces an incomplete result; the SLRA process requires cross-functional participation by design.

---

# Worked Example (Abbreviated)

**System Under Consideration:** A water-treatment chemical-dosing skid, comprising a legacy PLC ([Modicon-Premium.md](../08-Technologies/Schneider%20Electric/Modicon%20Premium/Modicon-Premium.md)), a local HMI, and a Modbus TCP link to the site SCADA.

1. **Initial assessment:** dosing-rate manipulation could cause a public-health consequence (over/under-dosing) — flagged for detailed assessment.
2. **Zone/conduit partitioning:** the skid is its own zone; the SCADA conduit is the sole external path.
3. **Detailed assessment:** the PLC has no native authentication (per [Modicon-Premium.md](../08-Technologies/Schneider%20Electric/Modicon%20Premium/Modicon-Premium.md) § 1.4); any host reachable on the SCADA conduit can write dosing setpoints; consequence category is Safety/Environmental (public health); likelihood is assessed as moderate given the conduit is not Internet-reachable but is reachable from the broader plant network.
4. **Comparison:** unmitigated risk exceeds tolerable risk given the consequence category.
5. **Target SL:** SL-T 2 assigned to the zone, reflecting protection against a moderately resourced, intentional adversary.
6. **CRS:** requires conduit-level Modbus function-code restriction, a firewall/DPI boundary, monitored writes, and a documented compensating-control gap for the PLC's own lack of authentication (per [Risk-Based-Security.md](../03-Architecture/Risk-Based-Security.md)).
7. **Register entry:** residual risk (PLC has no native authentication even after compensating controls) accepted by the named plant engineering manager, with a migration commitment logged and a 12-month reassessment date.

This is the pattern every zone/conduit in a real SUC goes through — abbreviated here for illustration; a full SLRA documents each step with the actual evidence and calculations behind it.

---

# Relationships

* **[Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md)** — the principles this process operationalises; read first.
* **[Risk-Based-Security.md](../03-Architecture/Risk-Based-Security.md)** — consumes SL-T/CRS output to select and prioritise controls.
* **[Security-Decision-Framework.md](../00-Core/Security-Decision-Framework.md)** — this process is Step 4 of that sequence.
* **[IEC62443.md](../02-Standards/IEC62443.md)** — the zone/conduit and Security Level constructs this process produces.
* **[Business-Impact-Analysis.md](Business-Impact-Analysis.md)** — shares and refines consequence/criticality data.
* **[Asset-Management.md](Asset-Management.md)** — supplies the asset inventory a risk assessment cannot proceed without.
* **[Vulnerability-Management.md](Vulnerability-Management.md)** — supplies technical vulnerability input; risk assessment supplies the operational-consequence context that turns a CVE into an actual risk figure.

---

# Common Mistakes

* Treating risk assessment as a one-time project-kickoff exercise, never repeated.
* No defined SUC boundary, so scope silently drifts or gaps go unnoticed.
* Skipping zone/conduit partitioning and assessing "the whole plant" as one undifferentiated risk.
* Consequence estimated by security staff alone, without process/safety engineering validation.
* A risk register that exists but is never reviewed or updated.
* Residual risk left with no named accepting owner.
* Reassessment triggers ignored — major changes deployed with no corresponding reassessment.
* Confusing this operational process with the principles ([Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md)) or the applied control-selection strategy ([Risk-Based-Security.md](../03-Architecture/Risk-Based-Security.md)) — all three are distinct and complementary.

---

# Decision Support

1. Is the System Under Consideration boundary explicitly defined for this assessment?
2. Has the SUC been partitioned into zones and conduits before detailed assessment begins?
3. Does the detailed assessment use the OT consequence categories, with safety/process engineering validating consequence?
4. Has a Target Security Level been assigned per zone/conduit, with a documented Cybersecurity Requirements Specification?
5. Is there a maintained risk register with named owners for every residual-risk entry?
6. Are reassessment triggers defined and actually monitored, not just documented?
7. Is reassessment cadence at least annual for the full SUC, more frequent for high-consequence zones?

---

# AI Guidance

* Treat this document as the operational **process** — distinguish it clearly from Risk-Management-Principles.md (concepts/principles) and Risk-Based-Security.md (applied control-selection strategy); recommend all three together but never conflate them.
* Walk through the SLRA sequence (SUC → initial assessment → zone/conduit partitioning → detailed assessment → SL-T → CRS) when a user asks how to actually run a risk assessment, rather than restating risk principles.
* Insist that consequence assessment involves process/safety engineering input, not security staff working in isolation.
* Recommend a maintained risk register with named owners for every residual-risk acceptance — an unowned entry is not actually accepted.
* Recommend reassessment triggered by defined events (new equipment, network changes, incidents) in addition to a periodic cycle, not periodic review alone.

---

# Sources / Grounding

* **IEC 62443-3-2** — Security risk assessment for system design; the SLRA method, zones/conduits, Target Security Levels, and the Cybersecurity Requirements Specification this document operationalises.
* **IEC 62443-2-1** — IACS security programme requirements for ongoing risk assessment as an organisational process.
* **ISO 31000** and **ISO/IEC 27005** — general risk-management process lineage; see [Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md).
* **NIST SP 800-30** — risk-assessment methodology referenced alongside IEC 62443-2-1's 2024 revision alignment.

> Foundational references; defer to the current standard text for authoritative, normative detail.

---

# Related Documents

* [Risk-Management-Principles.md](../00-Core/Risk-Management-Principles.md)
* [Risk-Based-Security.md](../03-Architecture/Risk-Based-Security.md)
* [Security-Decision-Framework.md](../00-Core/Security-Decision-Framework.md)
* [OT-Security-Philosophy.md](../00-Core/OT-Security-Philosophy.md)
* [IEC62443.md](../02-Standards/IEC62443.md)
* [Purdue-Model.md](../02-Standards/Purdue-Model.md)
* [Business-Impact-Analysis.md](Business-Impact-Analysis.md)
* [Asset-Management.md](Asset-Management.md)
* [Vulnerability-Management.md](Vulnerability-Management.md)
* [Change-Management.md](Change-Management.md)
* [NIS2.md](../01-Legislation/NIS2.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-07 | Initial release — new 09-Operations document resolving the pervasive dangling `Risk-Assessment.md` reference; defines the IEC 62443-3-2 SLRA process (SUC → initial assessment → zone/conduit partitioning → detailed assessment → Target SL → Cybersecurity Requirements Specification), the risk register, reassessment triggers, roles and a worked example, explicitly distinguished from Risk-Management-Principles.md (principles) and Risk-Based-Security.md (applied strategy); resolves the Planned manifest entry referenced by OT-Security-Philosophy.md, Risk-Management-Principles.md, Security-Decision-Framework.md and Risk-Based-Security.md |
