---
id: geoscada
title: "Schneider Electric EcoStruxure Geo SCADA Expert — Security Architecture and Engineering Reference"
category: Technologies
layer: "08-Technologies/Schneider Electric/GeoSCADA"
vendor: "Schneider Electric"
product: "EcoStruxure Geo SCADA Expert"
version: 1.0.1
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Security architecture and engineering reference for EcoStruxure Geo SCADA Expert (formerly
  ClearSCADA), the wide-area telemetry SCADA: integrated-server architecture, WAN/field trust
  model, threat model for distributed RTU estates, identity and per-object permissions,
  telemetry-protocol security (DNP3 SA, IEC 60870-5-104), phased hardening, monitoring, backup
  and disaster recovery.
keywords: [Geo SCADA, ClearSCADA, EcoStruxure, wide-area SCADA, telemetry, DNP3, Secure Authentication, IEC 60870-5-104, SCADAPack, RTU, ViewX, WebX, water, pipelines]
---

# Purpose

This document provides a security architecture and engineering reference for **Schneider Electric EcoStruxure Geo SCADA Expert** (formerly **ClearSCADA**) from the perspective of an OT Security Architect.

Geo SCADA Expert is a **wide-area telemetry SCADA**: it supervises geographically distributed infrastructure — water and wastewater networks, oil and gas gathering and pipelines, power distribution, district utilities — where hundreds or thousands of remote sites communicate with a central control room over WAN links (cellular, licensed radio, satellite, leased lines, legacy dial-up). This changes the security problem fundamentally compared with plant SCADA: **the conduits themselves cross untrusted or semi-trusted territory**, remote sites are physically exposed, and the platform is designed to tolerate — and mask — communication loss, which an attacker can exploit as cover.

This document covers the architecture, threat model, identity design, protocol security, hardening and recoverability of a Geo SCADA deployment. Configuration procedures are excluded; consult the vendor documentation for the deployed release.

> **Verification note.** Product capabilities, defaults and ports vary by release (ClearSCADA → EcoStruxure Geo SCADA Expert from the 2019 release onward). The client–server port (commonly TCP 5481 by default) and all other specifics are configurable and must be confirmed against the deployed version's documentation, never assumed. Statements below describe the platform family.

---

# Related Documents

* [PlantSCADA.md](../../AVEVA/PlantSCADA/PlantSCADA.md) — the plant-centric sibling; different problem, different trust model.
* [OT-Protocols.md](../../../04-Network/OT-Protocols.md) — DNP3, IEC 60870-5-104, Modbus security detail (single source of truth).
* [Secure-Remote-Access.md](../../../04-Network/Secure-Remote-Access.md) — the brokered chain into the control-room zone.
* [ICR-2734.md](../../Advantech/ICR-Series/ICR-2734.md), [WebAccess-DMP.md](../../Advantech/WebAccess-DMP/WebAccess-DMP.md) — a representative secure edge-gateway pattern for field-site WAN transport (VPN-wrapped telemetry, managed fleet).
* [IEC62443.md](../../../02-Standards/IEC62443.md), [Purdue-Model.md](../../../02-Standards/Purdue-Model.md) — zones/conduits and trust placement.
* [Identity-Management.md](../../../05-Identity/Identity-Management.md), [MFA.md](../../../05-Identity/MFA.md), [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md) — the identity layer.
* [Backup.md](../../../09-Operations/Backup.md), [Disaster-Recovery.md](../../../09-Operations/Disaster-Recovery.md), [Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md) — recoverability.

---

# Document Structure

1. Overview — role, integrated architecture, clients, redundancy, placement
2. Security — the wide-area trust model, threat model, defense in depth, IEC 62443 / regulatory context
3. Identity and Access — user model, per-object permissions, AD integration, remote access
4. Communications Security — telemetry protocols, WAN transport, client and web access
5. Hardening — phased hardening from control room to field
6. Field-Site and RTU Considerations
7. Monitoring and Logging
8. Backup, Redundancy and Disaster Recovery
9. Best Practices and Common Mistakes

---

# 1 — Overview

## 1.1 Platform Role and Lineage

Geo SCADA Expert descends from **ClearSCADA**, developed by Control Microsystems (also the origin of the **SCADAPack** RTU family) and later acquired by Schneider Electric; the product was renamed **EcoStruxure Geo SCADA Expert** with the 2019 release. Its design centre is telemetry: efficient polling and event collection from many small, remote, intermittently connected outstations, with the control room as the point of integration.

## 1.2 Integrated-Server Architecture

Unlike component-distributed plant SCADA, Geo SCADA runs as an **integrated server**: real-time database, historian, alarm engine, communications drivers and web server operate as one server instance around a single **object-oriented configuration database**. Key constructs:

| Element | Function | Security relevance |
|---|---|---|
| **Configuration database (objects, groups, templates/instances)** | Every point, outstation, display, user and permission is a database object; templates instantiate per site | One database defines the whole estate — its integrity is everything; templates propagate both fixes and mistakes fleet-wide |
| **Integrated historian** | Time-series storage incl. **backfilled** field events | Backfill accepts *past-dated* data from outstations (see 2.2) |
| **Alarm engine** | Alarm state, disabling/suppression | Alarm suppression at scale blinds a dispersed estate |
| **Drivers** | DNP3 (incl. SCADAPack), IEC 60870-5-101/104, Modbus, OPC and others | Southbound trust; per-protocol security in [OT-Protocols.md](../../../04-Network/OT-Protocols.md) |
| **Logic** | IEC 61131-3 Structured Text programs and scripts attached to database objects | Server-side code execution surface; needs governance like Cicode in Plant SCADA |
| **ViewX** | Thick desktop client (operations + engineering configuration) | The same client can *configure* the system — privilege design is critical |
| **WebX / Virtual ViewX** | Browser-based access (HTML5) | Web attack surface; must be HTTPS-only, ideally IDMZ-brokered |
| **APIs (ODBC, .NET/automation interfaces)** | Programmatic data and configuration access | Machine access bypassing the UI; inventory and restrict |

## 1.3 Redundancy

Geo SCADA supports **hot-standby server redundancy** (Main/Standby, with an additional geographically separated standby for DR in larger estates): the standby replicates configuration and data and takes over on failure. As with any replication, it propagates compromise and corruption as faithfully as legitimate data — redundancy is an availability control, not a recovery control (section 8).

## 1.4 Purdue Placement — the Wide-Area Reading

The classical hierarchy needs interpretation for wide-area SCADA:

* **Control room:** Geo SCADA servers at Level 2/3 of the *central* site; engineering (ViewX in configuration mode) in a separate engineering zone; enterprise access via the IDMZ.
* **Field sites:** each remote site is its own small zone (RTU/PLC + local instrumentation at L1/L0), often with minimal physical protection.
* **The WAN in between is a conduit crossing untrusted territory** — cellular, radio, third-party carriers. In IEC 62443 terms this is the defining conduit of the architecture and deserves the strongest treatment: authenticated protocols and/or VPN encapsulation (section 4.2). Where the rigid level model fits poorly (it does, here), fall back to zone-and-conduit analysis per [Purdue-Model.md](../../../02-Standards/Purdue-Model.md) and [IEC62443.md](../../../02-Standards/IEC62443.md).

---

# 2 — Security

## 2.1 Security Philosophy

Plant SCADA security leans on the assumption that the network between server and controller is *yours*. Wide-area SCADA cannot assume that: links traverse carriers and airspace, sites sit unmanned behind a padlock, and the platform is deliberately tolerant of outstations disappearing for hours. Geo SCADA security therefore stands on three legs: **authenticate the telemetry** (or wrap it), **assume field sites can be physically reached by an adversary**, and **treat communication anomalies as security signals**, not just maintenance tickets.

## 2.2 Threat Model

1. **WAN telemetry manipulation** — unauthenticated DNP3/IEC 104/Modbus on exposed links permits command injection (open a valve, trip a breaker, change a setpoint) and measurement spoofing. This is the signature risk of the product category; mitigations in 4.1–4.2.
2. **Field-site physical access** — an attacker at a remote cabinet reaches the RTU serial/Ethernet ports, the WAN device and the wiring; from one trusted outstation they speak *inward* toward the control room. One compromised site must not become a pivot to the estate (6).
3. **Engineering/ViewX configuration compromise** — configuration capability inside the operational client means a stolen high-privilege session can re-template the entire estate; the template/instance mechanism is a fleet-wide propagation channel.
4. **Server-side logic abuse** — ST programs/scripts attached to database objects execute on the server; ungoverned logic is a persistence and manipulation mechanism.
5. **Alarm and communication-state abuse** — disabling alarms or exploiting the platform's tolerance of comms loss to create cover ("the site was just offline again") while manipulating it locally (ATT&CK for ICS: *Inhibit Response Function*; [MITRE-ATTACK-ICS.md](../../../07-Threat-Intelligence/MITRE-ATTACK-ICS.md)).
6. **Backfill poisoning** — historic backfill ingests outstation-timestamped past events; a compromised outstation can rewrite the recorded past, corrupting investigations and regulatory records. Treat backfilled data provenance as an integrity question.
7. **WebX/API exposure** — browser and ODBC/automation interfaces reachable beyond the control-room zone; historical ClearSCADA advisories (CISA ICS advisories exist for the product line) underline patch and exposure discipline via [Vulnerability-Management.md](../../../09-Operations/Vulnerability-Management.md).
8. **Sector consequence** — the typical estates (water, energy, pipelines) are essential services under [NIS2.md](../../../01-Legislation/NIS2.md) and the Czech framework ([Czech-Cybersecurity-Act.md](../../../01-Legislation/Czech-Cybersecurity-Act.md)); consequence evaluation is safety- and environment-weighted per [Risk-Management-Principles.md](../../../00-Core/Risk-Management-Principles.md).

## 2.3 Defense in Depth Applied

| Layer | Geo SCADA specifics |
|---|---|
| Physical | Control-room and server protection; field cabinets alarmed/monitored (6) |
| Network | Control-room zone; per-site field zones; WAN as authenticated/encrypted conduits; WebX via IDMZ |
| Identity | Named users, per-object-group permissions, AD integration, MFA on remote and engineering paths |
| Application | ST/script governance, template change control, API inventory, DNP3 Secure Authentication where supported |
| Endpoint | Hardened Windows server and ViewX hosts; allowlisting |
| Monitoring | Comms-state analytics, command auditing, config-change and alarm-disable detection to SIEM |
| Recovery | Config export + database backup off-server, DR standby, rehearsed restores |

## 2.4 IEC 62443 Alignment

Zone the estate honestly: control-room server zone, engineering zone, IDMZ, and *each field site* as a zone with the WAN link as its conduit. Assign Target Security Levels per consequence — a pipeline block-valve site is not a rain gauge. FR1/FR2 map to the user/permission model (3), FR3/FR4 to protocol authentication and transport encryption (4), FR5 to the conduit design, FR6 to section 7. For legacy outstations that cannot authenticate, document compensating controls (VPN wrap, monitoring, physical hardening) per [Risk-Based-Security.md](../../../03-Architecture/Risk-Based-Security.md).

---

# 3 — Identity and Access

## 3.1 User Model and Per-Object Permissions

Geo SCADA authorizes **per user/role against the object hierarchy**: permissions (view, control, acknowledge, configure, security administration) are granted on database groups and inherited down the tree. Design rules:

* Build the object tree so that **permission boundaries follow operational and consequence boundaries** (region, system, criticality) — the tree *is* the authorization model; retrofitting it later is painful.
* Separate **operate** from **configure**: dispatcher roles get control/acknowledge on their region only; configuration permissions live in engineering roles used from the engineering zone.
* **Security administration** (users, permissions) is its own role, held by almost no one, audited always.
* Named accounts only; integrate with **Windows/AD** in the OT domain ([Identity-Management.md](../../../05-Identity/Identity-Management.md)); avoid local platform accounts except a documented, sealed emergency account. Explicitly disable/remove any legacy guest-type access — historically a real-world finding on this product line.

## 3.2 Control-Room Consoles and 24/7 Reality

Dispatch consoles run continuously. Use low-privilege view sessions as the resting state and per-dispatcher re-authentication for control actions; log the identity with every command. Do not let 24/7 convenience decay into one shared, always-elevated login.

## 3.3 Engineering and Remote Access

* ViewX-in-configuration-mode and the Server Configuration tools belong on **dedicated engineering workstations** under PAM (MFA, JIT elevation, session recording; [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md)); template changes go through [Change-Management.md](../../../09-Operations/Change-Management.md) — one template edit can reconfigure a thousand sites.
* Remote operational access (on-call dispatchers, vendors) goes through the brokered chain of [Secure-Remote-Access.md](../../../04-Network/Secure-Remote-Access.md): MFA at the IDMZ, jump host or published client, recording, time limits. WebX/Virtual ViewX, if used remotely, is published **only** via the IDMZ over HTTPS — never the server directly.

---

# 4 — Communications Security

## 4.1 Telemetry Protocol Security

Per-protocol depth is owned by [OT-Protocols.md](../../../04-Network/OT-Protocols.md); the Geo SCADA-relevant decisions:

* **DNP3** — enable **Secure Authentication (SAv5)** wherever master and outstations (e.g. SCADAPack generations that support it) allow: it authenticates critical operations end-to-end, surviving carrier networks the way transport-layer wrapping alone cannot. Roll out key management deliberately (per-outstation keys, rotation procedure).
* **IEC 60870-5-104** — natively unauthenticated; IEC 62351 extensions exist but field adoption is inconsistent. In practice: VPN-wrap it (4.2) and monitor command ASDUs.
* **Modbus** — no native security; confine to inside the field-site zone or wrap entirely.
* **Serial legacy (DNP3/101 over radio, dial-up)** — no transport security; compensate with physical protection, dial-back/allowlisting where applicable, and anomaly monitoring; plan migration.

## 4.2 WAN Transport

Default pattern: **encrypt and mutually authenticate the WAN conduit regardless of protocol**, using IPsec or WireGuard between field gateways and the control-room edge — a managed industrial-router fleet is the natural implementation (see [ICR-2734.md](../../Advantech/ICR-Series/ICR-2734.md) and its management plane [WebAccess-DMP.md](../../Advantech/WebAccess-DMP/WebAccess-DMP.md); this pairing is a proven Geo SCADA field-transport pattern). On cellular, use a **private APN** — but treat it as carrier-provided segmentation, not authentication: the VPN stays. Protocol authentication (DNP3 SA) and transport encryption are complementary, not alternatives — SA protects against on-path manipulation *including inside* the tunnel endpoints' blast radius.

## 4.3 Client, Web and API Access

* ViewX ↔ server: enable the platform's client–server encryption; restrict the server port (commonly TCP 5481 by default — verify per release) to client and engineering zones at the firewall ([Firewall-Design.md](../../../04-Network/Firewall-Design.md)).
* WebX/Virtual ViewX: HTTPS only, certificates from the internal PKI ([PKI.md](../../../05-Identity/PKI.md)), published solely via the IDMZ; disable if unused.
* ODBC/automation APIs: inventory every consumer, dedicated least-privilege accounts, source-host allowlists, log all writes; disable interfaces without a documented consumer.
* NTP across servers, clients and (where supported) outstations — backfill and event forensics depend on time integrity ([Logging.md](../../../09-Operations/Logging.md)).

---

# 5 — Hardening

Phased; each phase ends with validation and a baseline capture ([Configuration-Management.md](../../../09-Operations/Configuration-Management.md)).

**Phase 1 — Platform baseline.** Hardened Windows Server for Main/Standby ([Windows-Hardening.md](../../../09-Operations/Windows-Hardening.md)); minimal roles; allowlisting; EDR where vendor-supported; ViewX hosts hardened like operator stations.

**Phase 2 — Least functionality.** Install/enable only required drivers and interfaces; remove demo/sample database content; disable WebX, ODBC and automation interfaces without documented consumers; remove or seal legacy accounts (incl. any guest access).

**Phase 3 — Identity implementation.** Object tree aligned to permission boundaries; AD-integrated roles; operate/configure/security-admin separation; emergency account sealed and audited (section 3).

**Phase 4 — Communications.** Client–server encryption on; WebX HTTPS-only via IDMZ; DNP3 SA enabled per supported outstation population; VPN transport for all WAN conduits; firewall default-deny around the server zone ([Network-Segmentation.md](../../../04-Network/Network-Segmentation.md)).

**Phase 5 — Logic and template governance.** Review all ST programs/scripts; prohibit unreviewed server-side OS interaction; template changes only through change control with staged rollout (pilot sites → estate).

**Phase 6 — Validation.** Test the permission matrix per role; verify SA/VPN actually negotiated (capture, don't trust the checkbox); confirm API reachability matches the inventory; capture the configuration baseline and export it to version control.

---

# 6 — Field-Site and RTU Considerations

* **Assume reachable.** Design each site so its compromise is contained: the WAN device authenticates the site to the centre (per-site credentials/certificates — no shared PSK across the estate), and the centre's firewall admits only that site's telemetry flows. One padlock must not equal estate-wide trust.
* **Physical layer**: locked, alarmed cabinets (door contacts as SCADA points are cheap and effective), tamper-evident practice for service visits, no exposed spare Ethernet ports patched into the RTU segment.
* **RTU hygiene**: change default credentials, disable unused services and local engineering ports, track firmware per [Patch-Management.md](../../../09-Operations/Patch-Management.md) with field-visit realities in the plan; RTUs and gateways belong in the asset inventory with criticality and support status ([Asset-Management.md](../../../09-Operations/Asset-Management.md)).
* **Local engineering access** at sites (laptop to RTU) is privileged access without the control room watching — govern it procedurally (authorization before visit, log after) and prefer routing engineering through the central chain where the link allows.

---

# 7 — Monitoring and Logging

Forward the platform event journal and Windows logs to the SIEM ([SIEM.md](../../../09-Operations/SIEM.md), [Logging.md](../../../09-Operations/Logging.md)); high-value detections ([Monitoring.md](../../../09-Operations/Monitoring.md)):

* **Operator control actions** (who/what/where) — the command audit across the estate.
* **Alarm disabling/suppression** and alarm-configuration changes.
* **Configuration and template changes**, security-administration events, new/changed users and permissions.
* **Communication-state analytics**: outages are routine in telemetry — the *signal* is pattern deviation (site offline outside weather/carrier correlation, offline exactly during anomalous local events, repeated brief drops on one site). Make "offline" a security-relevant datum, not noise.
* **DNP3 SA authentication failures**, unexpected command function codes / ASDUs on WAN conduits (OT IDS with telemetry-protocol awareness earns its keep here).
* **Backfill anomalies** — unusually large or past-dated backfills from a site.
* API/ODBC connections and writes; new source hosts.
* Field-site door contacts and tamper points as first-class alarm inputs.

---

# 8 — Backup, Redundancy and Disaster Recovery

* **Back up**: the configuration database (and periodic configuration exports into version control — diffable evidence of what changed), historian data per retention, server settings, certificates and DNP3 SA key material custody records, license files, and the object-tree/permission documentation ([Backup.md](../../../09-Operations/Backup.md)).
* **Redundancy vs recovery**: Main/Standby (and a DR standby at a second site) cover availability and site loss; they replicate malicious configuration instantly. Offline, versioned configuration copies are the actual recovery asset.
* **DR specifics of wide-area SCADA**: the DR site needs not just the server but the **WAN head-end** (VPN concentrators, carrier terminations, radio interfaces) — rehearse outstation re-homing to the DR centre, not only server restoration ([Disaster-Recovery-Plan.md](../../../09-Operations/Disaster-Recovery-Plan.md)).
* Recovery is complete at a validated Trusted Operational State: permissions verified, telemetry authenticated end-to-end, alarms live, backfill reconciled ([Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md), [Disaster-Recovery.md](../../../09-Operations/Disaster-Recovery.md)).

---

# 9 — Best Practices and Common Mistakes

**Practices that pay off:** object tree designed around permission and consequence boundaries from day one; DNP3 SA plus VPN as the WAN default; templates under change control with staged rollout; comms-state deviations wired into detections; configuration exports in version control; DR exercise that re-homes real outstations.

**Common mistakes:**

* Trusting the carrier: telemetry in cleartext because "it's a private APN".
* One shared PSK or credential across every field site.
* Dispatcher accounts with configuration permissions because ViewX made it convenient.
* The object tree grown organically, making least privilege unimplementable later.
* Alarm disabling unrestricted and unlogged across the estate.
* "Site offline" normalized into noise — the attacker's favourite blind spot.
* WebX or ODBC reachable from the enterprise network directly.
* Legacy/guest access never removed after commissioning.
* Backfill trusted blindly as historical truth.
* DR plan that restores the server but forgets the WAN head-end and outstation re-homing.

---

# Architect Notes

Geo SCADA estates live or die on two design acts performed early: the **object tree** (which silently becomes your authorization model and your blast-radius map) and the **WAN conduit standard** (authenticated protocol + encrypted transport, applied uniformly, with per-site identity). Everything else — hardening, monitoring, DR — builds on those two. And respect the product's defining virtue as its defining risk: a system engineered to shrug off communication loss will also shrug off an attacker who arranges one; teach your monitoring to tell the difference.

---

# Decision Support

1. Is each field site a zone with its own identity, and the WAN link a designed conduit — or is the estate one implicit trust domain?
2. Which outstations support DNP3 SA, and what compensates where they do not?
3. Is every WAN conduit VPN-wrapped with per-site credentials/certificates (no estate-wide shared secrets)?
4. Does the object tree let you grant a dispatcher exactly their region and nothing more?
5. Are configure and security-administration permissions separated from operations and held under PAM?
6. Are template/logic changes under change control with staged rollout — and would an unauthorized one be detected?
7. Do detections treat comms-state deviations, alarm disabling and backfill anomalies as security signals?
8. Can you demonstrate DR including outstation re-homing to the standby centre, within the BIA-derived RTO?

---

# AI Guidance

When discussing EcoStruxure Geo SCADA Expert:

* Frame it as wide-area telemetry SCADA whose defining risk is the WAN conduit and the physically exposed field estate — distinct from plant SCADA (redirect plant-floor questions to PlantSCADA.md).
* Recommend authenticated telemetry (DNP3 SAv5 where supported) *and* encrypted transport (per-site VPN) as complementary defaults; never present a private APN as sufficient security.
* Anchor authorization advice in the object tree and per-object permissions; insist on operate/configure/security-admin separation and named dispatcher identities.
* Treat templates and server-side ST logic as fleet-wide propagation channels requiring change control and review.
* Elevate communication-loss patterns, alarm disabling and backfill anomalies to security signals in monitoring guidance.
* Delegate protocol depth to OT-Protocols.md, edge-transport implementation to ICR-2734.md/WebAccess-DMP.md, and recovery process to the 09-Operations layer; verify release-specific capabilities and ports against the deployed version's documentation rather than asserting them.

---

# Sources / Grounding

* **Schneider Electric EcoStruxure Geo SCADA Expert documentation** — architecture, security configuration, drivers, redundancy (authoritative for the deployed release).
* **Schneider Electric / CISA ICS security advisories** for ClearSCADA / Geo SCADA Expert — vulnerability history and patch guidance.
* **IEEE 1815 (DNP3) Secure Authentication v5** and **IEC 62351** — telemetry-protocol security referenced in section 4; see [OT-Protocols.md](../../../04-Network/OT-Protocols.md).
* **ISA/IEC 62443** — zone/conduit design for distributed estates; see [IEC62443.md](../../../02-Standards/IEC62443.md).
* **NIST SP 800-82 Rev. 3** — OT/SCADA security context.

> Foundational references; the vendor documentation of the deployed release is authoritative for product specifics.

---

# Related Documents

* [PlantSCADA.md](../../AVEVA/PlantSCADA/PlantSCADA.md)
* [ICR-2734.md](../../Advantech/ICR-Series/ICR-2734.md)
* [WebAccess-DMP.md](../../Advantech/WebAccess-DMP/WebAccess-DMP.md)
* [Purdue-Model.md](../../../02-Standards/Purdue-Model.md)
* [IEC62443.md](../../../02-Standards/IEC62443.md)
* [ISA95.md](../../../02-Standards/ISA95.md)
* [NIS2.md](../../../01-Legislation/NIS2.md)
* [Czech-Cybersecurity-Act.md](../../../01-Legislation/Czech-Cybersecurity-Act.md)
* [OT-Protocols.md](../../../04-Network/OT-Protocols.md)
* [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md)
* [Firewall-Design.md](../../../04-Network/Firewall-Design.md)
* [Secure-Remote-Access.md](../../../04-Network/Secure-Remote-Access.md)
* [Zero-Trust-in-OT.md](../../../03-Architecture/Zero-Trust-in-OT.md)
* [Risk-Based-Security.md](../../../03-Architecture/Risk-Based-Security.md)
* [Identity-Management.md](../../../05-Identity/Identity-Management.md)
* [MFA.md](../../../05-Identity/MFA.md)
* [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md)
* [PKI.md](../../../05-Identity/PKI.md)
* [Certificates.md](../../../05-Identity/Certificates.md)
* [Asset-Management.md](../../../09-Operations/Asset-Management.md)
* [Configuration-Management.md](../../../09-Operations/Configuration-Management.md)
* [Change-Management.md](../../../09-Operations/Change-Management.md)
* [Patch-Management.md](../../../09-Operations/Patch-Management.md)
* [Monitoring.md](../../../09-Operations/Monitoring.md)
* [Logging.md](../../../09-Operations/Logging.md)
* [SIEM.md](../../../09-Operations/SIEM.md)
* [Backup.md](../../../09-Operations/Backup.md)
* [Disaster-Recovery.md](../../../09-Operations/Disaster-Recovery.md)
* [Disaster-Recovery-Plan.md](../../../09-Operations/Disaster-Recovery-Plan.md)
* [Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md)
* [Vulnerability-Management.md](../../../09-Operations/Vulnerability-Management.md)
* [MITRE-ATTACK-ICS.md](../../../07-Threat-Intelligence/MITRE-ATTACK-ICS.md)
* [ICS-Kill-Chain.md](../../../07-Threat-Intelligence/ICS-Kill-Chain.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-07 | Initial release — new 08-Technologies platform security reference for EcoStruxure Geo SCADA Expert (formerly ClearSCADA), written to the S7-1500/M580/Plant SCADA document standard: integrated-server architecture and object database, wide-area trust model (field-site zones, WAN conduits), threat model incl. telemetry manipulation, backfill poisoning and comms-loss cover, per-object permission design, DNP3 SAv5 / IEC 60870-5-104 / VPN transport guidance, phased hardening, field-site/RTU controls, telemetry-aware monitoring, and DR incl. outstation re-homing; resolves the Planned entry from the corpus manifest |
| 1.0.1 | 2026-07-07 | Resolved Windows-Hardening.md reference now that the document exists |
