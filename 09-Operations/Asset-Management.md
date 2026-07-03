---
title: Asset Management
category: Operations
version: 1.0.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-07-01
review_cycle: Annual
---

# Purpose

Asset Management is the process of discovering, inventorying, classifying and maintaining an accurate record of the assets that make up an Industrial Automation and Control System (IACS) and its supporting infrastructure.

Within Operational Technology (OT), the asset inventory is the **foundation of the security programme**: vulnerability management, patch management, monitoring, business impact analysis and incident response all depend on knowing what exists, how critical it is and how it is connected. You cannot protect what you cannot see.

---

# Scope

This document covers what to inventory, discovery methods (passive vs active), asset criticality classification, the asset lifecycle and the relationships that make asset data the backbone of other disciplines. Configuration *state* is covered in [Configuration-Management.md](Configuration-Management.md).

---

# Security Philosophy

Unknown assets cannot be assessed, patched, monitored or recovered. An inventory that is incomplete or stale silently undermines every downstream control. Asset Management therefore aims for an inventory that is **accurate, current and criticality-aware**, maintained as a living record rather than a one-time survey.

---

# What to Inventory

For each asset, capture: identity and location; hardware model; firmware and software versions; operating system; network addresses and connected protocols; zone/conduit placement; owner; **criticality**; vendor support / end-of-life status; and dependencies. Metadata (criticality, owner, zone, support status) is what makes the inventory *useful*, not just a device list.

---

# Discovery: Passive First

* **Passive discovery** (traffic analysis, span/tap, log/flow data) is **preferred in OT** — it does not risk disturbing sensitive or legacy devices.
* **Active discovery / scanning** must be used with great caution: many OT devices react badly to unexpected probes. Where used, it should be OT-aware, rate-limited and scheduled with operations.
* **Vendor and engineering records, and configuration exports**, supplement automated discovery.

No single method is complete; combine them.

---

# Asset Criticality

Classify assets (e.g. Critical / High / Medium / Low) using safety impact, production impact, regulatory impact, and dependency relationships — not acquisition cost or technical complexity. Criticality is derived with, and feeds, the [Business-Impact-Analysis.md](Business-Impact-Analysis.md), and it drives prioritisation in vulnerability, patch and monitoring decisions.

---

# Asset Lifecycle

Asset records span the full system lifecycle — procurement, commissioning, operation, modification, and decommissioning — mirroring [OT-Lifecycle.md](OT-Lifecycle.md). Decommissioning must update the inventory and remove credentials/certificates; stale "ghost" assets are a common security gap.

---

# Relationships

* **[Vulnerability-Management.md](Vulnerability-Management.md)** / **[Patch-Management.md](Patch-Management.md)** — prioritisation is impossible without knowing affected, criticality-rated, exposed assets.
* **[Business-Impact-Analysis.md](Business-Impact-Analysis.md)** — shares and refines asset criticality and dependencies.
* **[Monitoring.md](Monitoring.md)** / **[SIEM.md](SIEM.md)** — asset context enriches detections; unknown assets are blind spots.
* **[Configuration-Management.md](Configuration-Management.md)** — assets vs their configuration *state* are complementary records.
* **[Risk-Based-Security.md](Risk-Based-Security.md)** — criticality and exposure feed risk-based control selection.
* **[IEC62443.md](IEC62443.md)** — assets are grouped into zones and conduits.

---

# Common Mistakes

* No inventory, or a spreadsheet that is never updated.
* Recording devices without criticality, owner or zone metadata.
* Active scanning that disrupts fragile OT devices.
* Ignoring firmware/version data needed for vulnerability matching.
* Leaving decommissioned "ghost" assets (and their credentials) in place.
* Treating asset management as an IT-only exercise.

---

# Decision Support

1. Do we have a current, complete inventory of OT assets?
2. Does each asset carry criticality, owner, zone and support-status metadata?
3. Is discovery passive-first and OT-safe?
4. Are firmware/software versions captured for vulnerability matching?
5. Is the inventory updated through change and decommissioning?
6. Does asset criticality align with the [Business-Impact-Analysis.md](Business-Impact-Analysis.md)?

---

# AI Guidance

* Explain the asset inventory as the foundation on which vulnerability, patch, monitoring, BIA and incident response depend.
* Recommend passive-first, OT-safe discovery; caution against active scanning of fragile devices.
* Emphasise metadata (criticality, owner, zone, versions, support status), not just a device list.
* Tie criticality to the BIA and to risk-based prioritisation; keep the inventory current through change and decommissioning.

---

# Sources / Grounding

* **IEC 62443-2-1** (IACS security programme — asset inventory) and **NIST SP 800-82 Rev. 3** (OT asset management).
* **CISA** guidance on OT/ICS asset inventory and visibility.
* Criticality aligns with **ISO 22301** / **NIST SP 800-34** business-impact analysis.

> Foundational references; defer to the current standards for authoritative detail.

---

# Related Documents

* [Vulnerability-Management.md](Vulnerability-Management.md)
* [Patch-Management.md](Patch-Management.md)
* [Business-Impact-Analysis.md](Business-Impact-Analysis.md)
* [Configuration-Management.md](Configuration-Management.md)
* [Monitoring.md](Monitoring.md)
* [SIEM.md](SIEM.md)
* [Risk-Based-Security.md](Risk-Based-Security.md)
* [OT-Lifecycle.md](OT-Lifecycle.md)
* [ISA95.md](ISA95.md)
* [IEC62443.md](IEC62443.md)
* [NIS2.md](NIS2.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-01 | Initial release — new 09-Operations document resolving the pervasive dangling `Asset-Management.md` reference; defines what to inventory, passive-first OT-safe discovery, criticality classification and the asset lifecycle as the foundation for vulnerability/patch/monitoring/BIA/IR; grounded in IEC 62443-2-1, NIST SP 800-82 Rev. 3, CISA guidance and ISO 22301/NIST SP 800-34 |
