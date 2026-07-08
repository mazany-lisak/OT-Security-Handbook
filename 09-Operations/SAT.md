---
id: sat
title: "Site Acceptance Test (SAT)"
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
  Site Acceptance Test: on-site functional and security validation after network integration and
  before handover to operations — real conduits, real identity infrastructure, real operating
  conditions, and the formal transition into the operational security baseline.
keywords: [SAT, Site Acceptance Test, testovací akceptace, integration testing, network validation, handover, go-live, FAT]
---

# Purpose

The Site Acceptance Test (SAT) is the structured validation of a system performed **at its final installation site**, after network integration, before formal handover to operations. Where [FAT.md](FAT.md) confirms a system was **built correctly** in isolation, SAT confirms it is **integrated correctly** — connected to the real network, the real identity infrastructure, the real Industrial DMZ, alongside the real neighbouring systems it will operate beside for the rest of its life.

Within [OT-Lifecycle.md](../00-Core/OT-Lifecycle.md), SAT is the last checkpoint before a system moves from the Testing phase into Operation — the last point at which a security gap can be corrected before the system is live, connected, and subject to the full operational risk this handbook exists to manage.

---

# Scope

This document covers: what SAT validates that FAT structurally cannot, the security-relevant SAT checklist, go-live/handover criteria, roles, and documentation. It assumes FAT has already been completed for the system ([FAT.md](FAT.md)) and treats FAT's punch-list resolution as an input, not a repeat of FAT's own checklist.

---

# Why SAT Matters for Security

Every security property that depends on the system's *context* — its real conduits, its real firewall rules, its real identity provider, its real physical placement — can only be validated once that context exists. FAT necessarily tests a system in isolation; SAT is where the architecture documented throughout this handbook (zones, conduits, segmentation, brokered remote access, centralized identity) is verified as **actually implemented**, not merely designed. A system that passed FAT with an exemplary hardening baseline can still fail SAT if the site network was cabled differently than designed, if a firewall rule was left overly permissive during commissioning, or if the identity integration was never actually completed.

SAT is also the formal boundary at which security operations begins: monitoring, logging, backup and incident-response readiness should all be **verified working**, not merely planned, before the system is handed to operations.

---

# What SAT Validates (Security-Relevant Scope)

* **Network integration matches design.** The actual deployed zones, conduits and firewall rules match the approved architecture — verify by testing, not by reading the firewall configuration alone (attempt connections that should be blocked; confirm they are).
* **Identity integration is live and correct.** Accounts resolve against the intended identity provider (not local fallback accounts left enabled "just in case"); MFA enforced where required actually challenges; role/permission assignments match design ([Identity-Management.md](Identity-Management.md is in 05-Identity — see Related Documents), [MFA.md](../05-Identity/MFA.md)).
* **Remote access path, if any, is the approved brokered path.** No standing vendor VPN, no direct-to-device access left open from commissioning — only the documented Industrial DMZ / jump-host chain functions ([Secure-Remote-Access.md](../04-Network/Secure-Remote-Access.md)).
* **Monitoring and logging are live.** The system's security-relevant events actually reach the SIEM/monitoring platform — verify with a test event, not just a configuration check ([Monitoring.md](Monitoring.md), [SIEM.md](SIEM.md), [Logging.md](Logging.md)).
* **Backup is functioning, not merely configured.** A backup has actually been taken since final site configuration, and — where the schedule allows — a restoration has been test-validated ([Backup.md](Backup.md)).
* **No commissioning residue remains.** Temporary firewall rules opened for commissioning convenience, temporary accounts, temporary debug/diagnostic access — all identified and closed. This is one of the most common and most dangerous SAT gaps: commissioning shortcuts that were never reverted.
* **Safety function integrity confirmed post-integration.** Any safety-relevant function is verified unaffected by the now-complete network and security integration — never assumed carried over unchanged from FAT.
* **Documentation reconciled.** As-built network diagrams, firewall rule sets, and the asset inventory reflect the actual site installation, superseding the FAT-stage documentation where site reality differed from the original design.

---

# Go-Live / Handover Criteria

SAT should conclude with an explicit, checked decision — not an implicit "it's been running for a few days, so it must be fine." Recommended criteria before formal handover to operations:

* All SAT checklist items passed, or open items have a documented, risk-accepted remediation plan with an owner and date.
* Monitoring, logging and backup demonstrated working, not just configured.
* Operations personnel have received the documentation and any required training for the system's operational security procedures.
* A named individual or committee formally signs off that the system is accepted into operation.

Only after this handover should the system be treated as fully subject to routine [Change-Management.md](Change-Management.md), [Patch-Management.md](Patch-Management.md) and [Monitoring.md](Monitoring.md) operational processes — commissioning-phase exceptions end here.

---

# Roles

* **Integrator/vendor** — supports integration testing, resolves any integration-stage findings.
* **OT security representative** — runs or reviews the security-specific checklist, independent from the integration team wherever practical.
* **Site network/identity team (asset owner)** — confirms integration with real site infrastructure (firewalls, identity provider, SIEM) matches design.
* **Operations** — confirms operational readiness and accepts handover.
* **Sign-off authority** — formally accepts the system into operation.

---

# Documentation

SAT should produce: the completed security checklist with pass/fail/remediation status; as-built network and configuration documentation reconciled against actual site installation; confirmation records for monitoring/logging/backup functionality (not configuration screenshots — evidence of an actual test event or restoration); a closed or tracked punch list; and formal handover sign-off. This becomes the system's baseline of record for [Configuration-Management.md](Configuration-Management.md) going forward.

---

# Relationships

* **[FAT.md](FAT.md)** — the factory-stage counterpart; SAT assumes FAT is complete and focuses on what only integration can reveal.
* **[OT-Lifecycle.md](../00-Core/OT-Lifecycle.md)** — SAT concludes the Testing phase (Phase 5) and gates entry into Operation (Phase 6).
* **[Network-Segmentation.md](../04-Network/Network-Segmentation.md)**, **[Firewall-Design.md](../04-Network/Firewall-Design.md)** — the architecture SAT verifies is actually implemented.
* **[Secure-Remote-Access.md](../04-Network/Secure-Remote-Access.md)** — the brokered access path SAT confirms is the only functioning one.
* **[Monitoring.md](Monitoring.md)**, **[SIEM.md](SIEM.md)**, **[Backup.md](Backup.md)** — the operational capabilities SAT confirms are live, not merely planned.
* **[Configuration-Management.md](Configuration-Management.md)** — SAT's reconciled documentation becomes the operational baseline.

---

# Common Mistakes

* Treating SAT as a repeat of the FAT checklist rather than testing what only integration can reveal.
* Verifying firewall/identity configuration by reading it rather than actively testing that blocked paths are actually blocked and required paths actually work.
* Commissioning-stage temporary access (firewall rules, accounts, debug interfaces) never closed out.
* Monitoring/logging/backup confirmed as "configured" without confirming they actually deliver working data/restoration.
* No explicit go-live decision — the system drifts into operational status without a formal handover.
* Safety function assumed unaffected by integration without active verification.

---

# Decision Support

1. Have all FAT punch-list items been resolved and verified before SAT begins?
2. Is network/firewall integration validated by active testing (attempted connections), not configuration review alone?
3. Is identity integration (accounts, MFA, roles) confirmed live against the real identity provider, with no local fallback accounts left enabled?
4. Is the remote-access path confirmed to be only the approved brokered chain, with any commissioning-stage direct access closed?
5. Are monitoring, logging and backup confirmed functioning through an actual test event or restoration, not just configuration?
6. Has all commissioning-stage temporary access been identified and removed?
7. Is there an explicit, documented go-live/handover decision with a named sign-off authority?

---

# AI Guidance

* Explain SAT as validating integration — real conduits, real identity, real monitoring — which FAT structurally cannot test; always distinguish it clearly from FAT.md.
* Recommend active testing (attempting blocked/allowed connections, triggering a test log event, validating a backup restoration) over passive configuration review as the SAT validation method.
* Flag commissioning-stage residue (temporary firewall rules, temporary accounts, debug interfaces left open) as the most common and highest-risk SAT gap.
* Insist on an explicit go-live/handover decision with named sign-off, rather than an implicit transition into operations.
* Frame SAT's reconciled documentation as the system's operational configuration-management baseline going forward.

---

# Sources / Grounding

* **IEC 62443-2-4** — security requirements for service providers/integrators, informing site-integration acceptance expectations.
* **IEC 62443-2-1** — IACS security programme requirements for the operate/maintain handover.
* **NIST SP 800-82 Rev. 3** — OT system lifecycle and integration-testing guidance.

> Foundational references; defer to project-specific acceptance-test specifications and the current standard text for normative detail.

---

# Related Documents

* [FAT.md](FAT.md)
* [OT-Lifecycle.md](../00-Core/OT-Lifecycle.md)
* [Network-Segmentation.md](../04-Network/Network-Segmentation.md)
* [Firewall-Design.md](../04-Network/Firewall-Design.md)
* [Secure-Remote-Access.md](../04-Network/Secure-Remote-Access.md)
* [Identity-Management.md](../05-Identity/Identity-Management.md)
* [MFA.md](../05-Identity/MFA.md)
* [Monitoring.md](Monitoring.md)
* [SIEM.md](SIEM.md)
* [Logging.md](Logging.md)
* [Backup.md](Backup.md)
* [Configuration-Management.md](Configuration-Management.md)
* [Change-Management.md](Change-Management.md)
* [IEC62443.md](../02-Standards/IEC62443.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-07 | Initial release — new 09-Operations document resolving the dangling `SAT.md` reference from OT-Lifecycle.md; defines the security-relevant SAT scope (live network/identity/remote-access integration testing, monitoring/logging/backup functional verification, commissioning-residue closeout), go-live/handover criteria, roles and documentation, explicitly distinguished from FAT.md's isolated pre-shipment validation |
