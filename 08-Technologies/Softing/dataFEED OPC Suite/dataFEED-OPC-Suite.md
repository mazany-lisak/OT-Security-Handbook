---
id: datafeed-opc-suite
title: "Softing dataFEED OPC Suite — Security Architecture and Engineering Reference"
category: Technologies
layer: "08-Technologies/Softing/dataFEED OPC Suite"
vendor: "Softing Industrial Automation"
product: "dataFEED OPC Suite"
version: 1.0.0
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Security architecture and engineering reference for the Softing dataFEED OPC Suite modular OPC
  connectivity platform: component architecture (OPC servers, OPC UA Tunnel, Redundancy Manager,
  Logger), the OPC UA Tunnel as a native DCOM-elimination pattern, southbound fieldbus/PROFINET
  heritage, redundancy security considerations, identity, phased hardening, monitoring and recovery.
keywords: [Softing, dataFEED, dataFEED OPC Suite, OPC server, OPC aggregator, OPC UA Tunnel, DCOM, Redundancy Manager, PROFIBUS, PROFINET, industrial connectivity]
---

# Purpose

This document provides a security architecture and engineering reference for the **Softing Industrial Automation dataFEED OPC Suite**, from the perspective of an OT Security Architect.

Like [Kepware-Server.md](../../PTC/Kepware%20Server/Kepware-Server.md), dataFEED OPC Suite is an **OPC aggregation platform** — the pattern [ISA95.md](../../../02-Standards/ISA95.md) documents explicitly as the correct way to terminate legacy OPC DA before it crosses a firewall boundary and to translate native industrial protocols into a unified OPC namespace for SCADA, historian and MES consumers. Architecturally the threat model is the same as any OPC aggregator: a **bidirectional conduit** sitting between many southbound devices and many northbound clients, whose compromise grants read/write reach across everything it connects.

Where dataFEED OPC Suite differs materially from a plugin-driver server is its **modular component architecture** — separate, independently deployable components for protocol-specific OPC servers, redundancy management, tunneling and logging — and, most significantly for security architecture, its **OPC UA Tunnel**: a purpose-built component that replaces DCOM-based OPC DA transport across network boundaries with an encrypted, certificate-authenticated OPC UA tunnel. This is a genuine, native answer to the DCOM problem documented at length in [Kepware-Server.md](../../PTC/Kepware%20Server/Kepware-Server.md) § 4.2, and it is the feature this document treats as the platform's defining security decision. Configuration procedures are excluded; consult the vendor documentation for the deployed release.

> **Verification note.** Softing markets dataFEED OPC Suite as a modular product line (individual OPC servers per fieldbus/protocol, the OPC UA Tunnel, the Redundancy Manager, and logging/diagnostic components), and Softing's broader industrial-networking heritage (PROFIBUS/PROFINET diagnostics and gateway hardware) informs its fieldbus-protocol depth. Confirm exact component set, supported protocols and security capabilities against the deployed release and licensed modules; treat this document as the pattern.

---

# Related Documents

* [Kepware-Server.md](../../PTC/Kepware%20Server/Kepware-Server.md) — the peer OPC aggregation platform in this handbook; read together for the shared bidirectional-conduit threat model, contrasted architectures (plugin-driver vs. modular components), and the OPC UA Tunnel vs. plugin-based DCOM containment approaches.
* [PI-System.md](../../AVEVA/PI%20System/PI-System.md) — a common northbound consumer of aggregated OPC data.
* [ISA95.md](../../../02-Standards/ISA95.md) — defines the OPC-aggregation-server conduit-termination pattern this document implements.
* [OT-Protocols.md](../../../04-Network/OT-Protocols.md) — southbound native-protocol, PROFIBUS/PROFINET and OPC DA/UA security detail (single source of truth).
* [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md), [Firewall-Design.md](../../../04-Network/Firewall-Design.md) — placing and bounding the aggregation server and its tunnel endpoints.
* [Identity-Management.md](../../../05-Identity/Identity-Management.md), [PKI.md](../../../05-Identity/PKI.md), [Certificates.md](../../../05-Identity/Certificates.md) — identity and certificate context for OPC UA and the Tunnel.
* [Monitoring.md](../../../09-Operations/Monitoring.md), [Backup.md](../../../09-Operations/Backup.md) — operations around the server.

---

# Document Structure

1. Overview — role, modular architecture, the OPC UA Tunnel, placement
2. Security — the bidirectional-conduit threat model, defense in depth, IEC 62443 alignment
3. Identity and Access — component-level authentication, OPC UA identity, service accounts
4. Communications Security — southbound fieldbus/native protocols, the OPC UA Tunnel, OPC DA/UA
5. Redundancy-Specific Security — the Redundancy Manager and failover trust
6. Hardening — phased hardening lifecycle
7. Monitoring and Logging
8. Backup and Recovery
9. Best Practices and Common Mistakes

---

# 1 — Overview

## 1.1 Platform Role

dataFEED OPC Suite performs the same fundamental function as any OPC aggregation platform: translate native industrial protocols into OPC (DA and/or UA) and serve that unified data model to SCADA, historian, MES and custom clients. Softing's product heritage in **fieldbus diagnostics and gateway hardware** (particularly PROFIBUS and PROFINET) gives the suite particular depth on those protocols alongside broader coverage (S7, Modbus, EtherNet/IP and others — see [OT-Protocols.md](../../../04-Network/OT-Protocols.md) for protocol-level detail regardless of vendor).

## 1.2 Modular Component Architecture

Rather than a single service with plugin drivers, dataFEED OPC Suite is composed of **separate, purpose-specific components**, typically licensed and deployed independently:

| Component | Function | Security relevance |
|---|---|---|
| **Protocol-specific OPC servers** (S7, PROFIBUS, PROFINET, Modbus, EtherNet/IP, etc.) | Southbound native-protocol collection, exposed as OPC DA/UA | Each is a protocol parser with its own patch cadence; enable only what is connected |
| **OPC UA Tunnel** | Encrypted, certificate-authenticated tunnel carrying OPC DA (or UA) traffic across network/firewall boundaries without DCOM | The platform's signature security feature — see 1.3 and § 4.2 |
| **Redundancy Manager** | Coordinates failover between redundant OPC servers/data sources | Availability mechanism with its own trust and failover-integrity considerations — see § 5 |
| **Logger / diagnostic components** | Data logging and connection diagnostics | Operational visibility; secure logged data like any historian output |

Deploying only the components actually required — rather than a monolithic install — is itself a security-relevant decision under this architecture: unused components should not be installed at all, not merely disabled.

## 1.3 The OPC UA Tunnel — Defining Feature

The **OPC UA Tunnel** is Softing's purpose-built answer to exactly the problem documented in [Kepware-Server.md](../../PTC/Kepware%20Server/Kepware-Server.md) § 4.2: classic OPC DA depends on COM/DCOM, which is notoriously difficult to secure across a firewall boundary (dynamic ports, complex ACLs, frequent "just open it up" commissioning shortcuts). The OPC UA Tunnel wraps OPC DA (or native OPC UA) traffic in an **OPC UA-based, TLS-secured, certificate-authenticated tunnel** between a server-side and client-side tunnel component, eliminating the need to open DCOM across the boundary at all.

This is a genuine architectural improvement **when correctly deployed** — but it is not automatic security: the tunnel's own certificate management, its endpoint placement, and what it is actually permitted to carry all require the same governance as any other conduit (§ 4.2, § 6).

## 1.4 ISA-95 / Purdue Placement

Identical to any OPC aggregation platform: positioned at the **Level 2/3 boundary**, south-facing into controller networks, north-facing to SCADA/historian/MES ([ISA95.md](../../../02-Standards/ISA95.md)). Where the OPC UA Tunnel crosses a zone or IDMZ boundary, treat the **tunnel endpoints themselves** as the documented conduit termination points ([Purdue-Model.md](../../../02-Standards/Purdue-Model.md)) — not the underlying OPC DA traffic they carry, which never itself traverses the boundary.

---

# 2 — Security

## 2.1 Security Philosophy

The core threat model is shared with every OPC aggregator in this handbook (see [Kepware-Server.md](../../PTC/Kepware%20Server/Kepware-Server.md) § 2.1): a single point aggregating bidirectional access across many protocols and many clients. What differs here is that Softing gives the architect a **native tool to solve the DCOM problem properly** rather than only a containment workaround — the question is whether that tool is actually deployed and correctly governed, or whether legacy DCOM paths persist alongside it out of habit.

## 2.2 Threat Model

1. **Server/component compromise as a multi-protocol, bidirectional pivot** — identical in kind to any OPC aggregator: reach across every connected controller through whichever protocol-specific server component handles it.
2. **Residual DCOM exposure where the Tunnel is not used** — sites that install dataFEED OPC Suite but continue using classic DCOM-based OPC DA connectivity (rather than adopting the OPC UA Tunnel) inherit the full DCOM risk documented in [Kepware-Server.md](../../PTC/Kepware%20Server/Kepware-Server.md) § 4.2, gaining none of the platform's differentiating benefit.
3. **OPC UA Tunnel misconfiguration** — an improperly scoped or certificate-mismanaged tunnel can itself become the exposure it was meant to close: accepting untrusted tunnel-client certificates, or carrying more traffic/tags than the intended integration requires.
4. **Fieldbus-server exposure** — the PROFIBUS/PROFINET/S7/Modbus protocol-specific OPC servers inherit the security (or lack thereof) of their native protocol exactly as documented in [OT-Protocols.md](../../../04-Network/OT-Protocols.md); the OPC-layer wrapper does not retroactively authenticate an unauthenticated fieldbus.
5. **Redundancy Manager failover abuse** — an attacker able to force spurious failover events, or to target a standby data source while attention is on the primary, can degrade availability or create a monitoring blind spot during transition (see § 5).
6. **Overprivileged OPC clients** — as with any aggregator, a client granted broader read/write scope than required amplifies the impact of that client's own compromise.
7. **Unmonitored component sprawl** — because components are modular and independently deployable, an estate can accumulate protocol-server instances over time with no central inventory of what is running where — an asset-management gap specific to this architecture.
8. **Platform vulnerabilities** — track **Softing PSIRT/security advisories**; prioritise via [Vulnerability-Management.md](../../../09-Operations/Vulnerability-Management.md).

## 2.3 Defense in Depth Applied

| Layer | dataFEED OPC Suite specifics |
|---|---|
| Physical | Server-room protection for hosts running suite components |
| Network | Aggregation components at the L2/L3 boundary; OPC UA Tunnel as the boundary-crossing mechanism, correctly scoped; no residual DCOM across the boundary |
| Identity | Component-level authentication; OPC UA/Tunnel certificate-based auth; least-privilege client scoping |
| Application | Only required protocol-server components deployed; Redundancy Manager failover governed and monitored |
| Endpoint | Hardened Windows hosts for each component; allowlisting; patched per component |
| Monitoring | Tunnel session events, tag-write events, Redundancy Manager failover events, to SIEM |
| Recovery | Component configuration backup, tested restore including redundancy re-establishment |

See [Defense-in-Depth.md](../../../03-Architecture/Defense-in-Depth.md).

## 2.4 IEC 62443 Alignment

* Treat each protocol-specific OPC server as a component with its own Target SL assessment tied to the zone it serves, and the **OPC UA Tunnel endpoints** as the formal conduit-termination points where a boundary is crossed ([IEC62443.md](../../../02-Standards/IEC62443.md)).
* FR1/FR2 → component-level and OPC UA/Tunnel certificate authentication, least-privilege client scoping.
* FR3/FR4 → the OPC UA Tunnel's TLS/certificate transport directly satisfies integrity/confidentiality for boundary-crossing traffic **when used**; residual DCOM paths do not, and should be documented as a compensating-control gap if they must temporarily remain ([Risk-Based-Security.md](../../../03-Architecture/Risk-Based-Security.md)).
* FR7 (resource availability) → the Redundancy Manager's role, with its own integrity considerations documented in § 5.

---

# 3 — Identity and Access

## 3.1 Component-Level Authentication

Each protocol-specific OPC server and the Tunnel components authenticate connecting OPC clients; scope every client to the minimum tag namespace and read/write capability it requires, exactly as for any OPC aggregator ([Identity-Management.md](../../../05-Identity/Identity-Management.md)). Avoid one broad shared client credential serving every SCADA/MES integration.

## 3.2 OPC UA and Tunnel Identity

Enable **certificate-based mutual authentication** on both the OPC UA server interface and the **OPC UA Tunnel endpoints**, with a managed trust list — issue certificates from the internal PKI where practical ([PKI.md](../../../05-Identity/PKI.md), [Certificates.md](../../../05-Identity/Certificates.md)). The Tunnel's value depends entirely on disciplined certificate management at both ends; an expired or over-broadly-trusted tunnel certificate reopens the exact exposure the Tunnel exists to close.

## 3.3 Service Accounts

Host each suite component under a least-privileged, non-interactive service account; inventory southbound protocol credentials where the fieldbus/protocol supports them; review periodically ([Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md)).

---

# 4 — Communications Security

## 4.1 Southbound (Fieldbus and Native Protocols)

Protocol-level depth — PROFIBUS, PROFINET, S7comm, Modbus and others — is owned by [OT-Protocols.md](../../../04-Network/OT-Protocols.md). Deploy only the protocol-specific server components actually connected to real devices; where the native protocol is unauthenticated (most fieldbus and legacy protocols), rely on network containment around the source device exactly as documented in that device's own reference (e.g. [S7-300.md](../../SIEMENS/S7-300/S7-300.md), [Modicon-Premium.md](../../Schneider%20Electric/Modicon%20Premium/Modicon-Premium.md)).

## 4.2 The OPC UA Tunnel in Practice

* **Use the Tunnel for every boundary-crossing OPC connection.** Wherever OPC DA connectivity must cross a zone or firewall boundary, deploy the OPC UA Tunnel rather than opening DCOM — this is the platform's core value proposition and should be treated as mandatory architecture, not an optional enhancement.
* **Scope the tunnel narrowly.** Configure it to carry only the specific OPC connections required for the integration it serves, not as a general-purpose open pipe between zones.
* **Manage tunnel certificates like any PKI-dependent conduit** — expiry monitoring, revocation on decommissioning, no self-signed "trust it once" shortcuts in production (§ 3.2).
* **Retire residual DCOM paths actively.** Auditing an estate for any OPC DA/DCOM connection that still crosses a boundary without the Tunnel is a standing action item, not a one-time migration.

## 4.3 OPC UA (Native)

Where clients connect via native OPC UA rather than through the Tunnel, apply the same certificate-based Sign/SignAndEncrypt guidance as for any OPC UA server ([OT-Protocols.md](../../../04-Network/OT-Protocols.md)).

## 4.4 Time Synchronization

NTP across all suite component hosts for reliable correlation with SCADA, historian and SIEM timestamps ([Logging.md](../../../09-Operations/Logging.md)).

---

# 5 — Redundancy-Specific Security

The **Redundancy Manager** coordinates failover between redundant OPC data sources/servers to maintain data continuity. As with every redundancy mechanism documented elsewhere in this handbook, keep two points sharply distinct:

* **Redundancy protects availability against failure — it is not a security control.** Both primary and standby paths individually require the same containment, authentication and monitoring as a standalone deployment; redundancy does not reduce the attack surface, it duplicates the number of paths that must be governed.
* **Failover events are a monitoring signal.** Unexpected or repeated failover, or failover correlated with unusual write activity, deserves the same scrutiny given to switchover events on redundant controllers (compare [S7-400.md](../../SIEMENS/S7-400/S7-400.md) § 4.2) — it can indicate a denial-of-availability attempt or an attempt to divert attention from concurrent tampering.
* **Configuration consistency between redundant paths** should be included in [Configuration-Management.md](../../../09-Operations/Configuration-Management.md) baselines; a standby data source silently configured differently from its primary undermines both reliability and security assumptions.

---

# 6 — Hardening

Phased; each phase ends with validation and a baseline capture ([Configuration-Management.md](../../../09-Operations/Configuration-Management.md)).

**Phase 1 — Platform baseline.** Hardened Windows hosts for each deployed component ([Windows-Hardening.md](../../../09-Operations/Windows-Hardening.md), planned); patched per vendor cadence.

**Phase 2 — Component minimisation.** Deploy only the protocol-specific OPC server components actually required by connected devices; maintain a central inventory of every deployed component instance and its host ([Asset-Management.md](../../../09-Operations/Asset-Management.md)) to close the sprawl gap noted in § 2.2.7.

**Phase 3 — Identity configuration.** Implement least-privilege, per-client authentication (§ 3.1); remove default/shared credentials; least-privilege service accounts (§ 3.3).

**Phase 4 — OPC UA and Tunnel hardening.** Enforce certificate-based mutual authentication and Sign/SignAndEncrypt on all OPC UA interfaces and Tunnel endpoints; deploy certificates from the internal PKI; scope every tunnel narrowly (§ 4.2).

**Phase 5 — DCOM elimination.** Audit for any remaining OPC DA/DCOM connection crossing a zone/firewall boundary; migrate every one to the OPC UA Tunnel; document and time-bound any that cannot yet migrate as a compensating-control exception.

**Phase 6 — Redundancy governance.** Confirm both primary and standby paths are independently hardened and monitored (§ 5); enable failover-event alerting.

**Phase 7 — Network hardening.** Place components at the documented L2/L3 boundary; default-deny boundary firewall; permit only the specific southbound and northbound flows required, with Tunnel endpoints as the only boundary-crossing OPC path ([Firewall-Design.md](../../../04-Network/Firewall-Design.md)).

**Phase 8 — Validation.** Confirm no DCOM traffic crosses any monitored boundary; confirm Tunnel and OPC UA reject untrusted certificates; confirm client accounts see only intended tag scope; confirm failover behaves as designed and is alerted; capture the baseline.

---

# 7 — Monitoring and Logging

Forward to the SIEM ([SIEM.md](../../../09-Operations/SIEM.md)) and treat as high-value detections ([Monitoring.md](../../../09-Operations/Monitoring.md)):

* **OPC UA Tunnel session establishment and certificate events** — failures, new/unexpected endpoints, certificate expiry approaching.
* **Tag writes**, correlated with client identity, across every deployed protocol-server component.
* **Any OPC DA/DCOM connection attempt crossing a monitored boundary** — should not occur post-hardening; treat as high-severity if seen.
* **Redundancy Manager failover and re-synchronisation events**, per § 5.
* Component-level authentication failures and configuration changes.
* Southbound protocol-server connection failures (availability signal).
* Windows security logs of every component host.

---

# 8 — Backup and Recovery

* **Back up** every component's configuration (protocol-server settings, Tunnel configuration and certificates, Redundancy Manager configuration, client/authentication settings), stored offline and version-controlled ([Backup.md](../../../09-Operations/Backup.md)).
* **Test restoration** across the full component set, confirming Tunnel certificate trust, southbound reconnection, and — for redundant deployments — that failover re-establishes correctly rather than merely that one path comes back online ([Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md)).
* Maintain a **component-and-host inventory** as part of the backup record, given the modular architecture's tendency toward sprawl (§ 2.2.7) — recovery planning requires knowing what was actually deployed, not just what the "main" server held.

---

# 9 — Best Practices and Common Mistakes

**Practices that pay off:** the OPC UA Tunnel deployed for every boundary-crossing connection, with disciplined certificate management; only required protocol-server components deployed, centrally inventoried; per-client least-privilege authentication; redundant paths independently hardened and failover-monitored; DCOM actively audited to zero across boundaries; component configuration backed up and restoration tested end-to-end.

**Common mistakes:**

* Installing dataFEED OPC Suite but continuing to use classic DCOM-based OPC DA across a boundary — buying the solution and not deploying its defining feature.
* Tunnel certificates self-signed, unmanaged, or trusted broadly "to get it working."
* Protocol-server components accumulating over time with no central inventory of what runs where.
* Treating Redundancy Manager failover as a security control rather than an availability one, leaving the standby path less hardened than the primary.
* No monitoring of failover events, missing a slow-burn availability or diversion pattern.
* Every OPC client sharing one broad, unscoped credential.
* Configuration backed up for only the "main" component, leaving Tunnel/Redundancy Manager/protocol-server settings unrecoverable.

---

# Architect Notes

dataFEED OPC Suite hands the architect a real, native solution to the OPC DA/DCOM problem that most of this handbook's other aggregation-platform guidance can only work around — the OPC UA Tunnel is a genuinely good answer, *if deployed*. The single most valuable question to ask of any dataFEED OPC Suite estate is therefore blunt: is the Tunnel actually carrying every boundary-crossing OPC connection, or is DCOM still quietly doing that job because migrating the last few clients was never finished? Everything else — component minimisation, redundancy governance, certificate discipline — is standard OT aggregation-platform hygiene shared with Kepware and every other conduit-termination component in this handbook; the Tunnel decision is what makes this platform distinct.

---

# Decision Support

1. Is the OPC UA Tunnel deployed for every OPC connection that crosses a zone or firewall boundary — with zero residual DCOM in that role?
2. Are Tunnel and OPC UA certificates managed through the internal PKI, with expiry monitoring and no broad/self-signed trust?
3. Is there a central inventory of every deployed protocol-server, Tunnel and Redundancy Manager component instance and its host?
4. Are OPC client accounts individually scoped to the minimum tag namespace they require?
5. Are both primary and standby paths in any redundant deployment independently hardened, not just the primary?
6. Are failover/re-synchronisation events monitored and alerted?
7. Is component configuration (including Tunnel and Redundancy Manager) backed up and restoration tested end-to-end?
8. Is a Softing PSIRT-tracking process in place covering every deployed component, not just the base platform?

---

# AI Guidance

When discussing Softing dataFEED OPC Suite:

* Frame it as an OPC aggregation platform sharing Kepware's bidirectional-conduit threat model, but differentiated by its modular component architecture and, above all, its native **OPC UA Tunnel** answer to the DCOM problem — make the Tunnel-deployment question the first thing to check.
* Never present the Tunnel's presence in the product as sufficient by itself; confirm it is actually the path carrying boundary-crossing traffic, with disciplined certificate management, before treating DCOM risk as addressed.
* Recommend central inventory of deployed components given the modular architecture's sprawl tendency, distinct from Kepware's single-service model.
* Treat the Redundancy Manager as an availability mechanism only; insist both primary and standby paths are independently secured and that failover events are monitored.
* Delegate southbound protocol depth (including PROFIBUS/PROFINET) to OT-Protocols.md, and cross-reference Kepware-Server.md for the shared aggregator threat model rather than repeating it; verify release-specific component capabilities and Softing security advisories against current vendor sources rather than asserting them.

---

# Sources / Grounding

* **Softing Industrial Automation dataFEED OPC Suite documentation** — component architecture, OPC UA Tunnel, Redundancy Manager configuration (authoritative for the deployed release and licensed modules).
* **Softing security advisories / PSIRT guidance** — dataFEED OPC Suite vulnerability history and current guidance.
* **OPC Foundation OPC UA specifications** — security model underlying the Tunnel and native OPC UA interfaces; see [OT-Protocols.md](../../../04-Network/OT-Protocols.md).
* **ISA/IEC 62443** — the OPC-aggregation-server conduit-termination pattern this document implements; also documented in [ISA95.md](../../../02-Standards/ISA95.md). See [IEC62443.md](../../../02-Standards/IEC62443.md).
* **NIST SP 800-82 Rev. 3** — OT security-architecture context.

> Foundational references; the vendor documentation of the deployed release is authoritative for product specifics.

---

# Related Documents

* [Kepware-Server.md](../../PTC/Kepware%20Server/Kepware-Server.md)
* [PI-System.md](../../AVEVA/PI%20System/PI-System.md)
* [PlantSCADA.md](../../AVEVA/PlantSCADA/PlantSCADA.md)
* [ISA95.md](../../../02-Standards/ISA95.md)
* [Purdue-Model.md](../../../02-Standards/Purdue-Model.md)
* [IEC62443.md](../../../02-Standards/IEC62443.md)
* [OT-Protocols.md](../../../04-Network/OT-Protocols.md)
* [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md)
* [Firewall-Design.md](../../../04-Network/Firewall-Design.md)
* [Risk-Based-Security.md](../../../03-Architecture/Risk-Based-Security.md)
* [S7-300.md](../../SIEMENS/S7-300/S7-300.md)
* [S7-400.md](../../SIEMENS/S7-400/S7-400.md)
* [Modicon-Premium.md](../../Schneider%20Electric/Modicon%20Premium/Modicon-Premium.md)
* [Identity-Management.md](../../../05-Identity/Identity-Management.md)
* [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md)
* [PKI.md](../../../05-Identity/PKI.md)
* [Certificates.md](../../../05-Identity/Certificates.md)
* [Configuration-Management.md](../../../09-Operations/Configuration-Management.md)
* [Asset-Management.md](../../../09-Operations/Asset-Management.md)
* [Monitoring.md](../../../09-Operations/Monitoring.md)
* [Logging.md](../../../09-Operations/Logging.md)
* [SIEM.md](../../../09-Operations/SIEM.md)
* [Backup.md](../../../09-Operations/Backup.md)
* [Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md)
* [Vulnerability-Management.md](../../../09-Operations/Vulnerability-Management.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-07 | Initial release — new 08-Technologies platform reference for Softing dataFEED OPC Suite, implementing the "OPC aggregation server" conduit-termination pattern from ISA95.md with a modular-component architecture distinct from Kepware's plugin-driver model: the OPC UA Tunnel as a native DCOM-elimination pattern (deployment, certificate governance, and the "is it actually carrying the traffic" audit question), fieldbus/PROFIBUS-PROFINET southbound heritage, Redundancy Manager availability-vs-security framing and failover monitoring, component-sprawl asset-management guidance specific to the modular architecture, phased hardening, monitoring and recovery; first Softing vendor branch; cross-referenced throughout with Kepware-Server.md as the peer aggregation platform |
