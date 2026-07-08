---
id: pi-system
title: "AVEVA PI System (formerly OSIsoft PI) — Security Architecture and Engineering Reference"
category: Technologies
layer: "08-Technologies/AVEVA/PI System"
vendor: "AVEVA"
product: "PI System"
version: 1.0.2
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Security architecture and engineering reference for the AVEVA PI System (formerly OSIsoft PI):
  Data Archive, Asset Framework, PI Interfaces/Connectors and buffering, PI Vision and PI Web API,
  the estate-wide-aggregation threat model, PI Trusts vs Identity-based authentication, IDMZ
  placement and PI-to-PI replication, phased hardening, monitoring, backup and cloud integration.
keywords: [PI System, OSIsoft, AVEVA, historian, PI Data Archive, Asset Framework, AF, PI Vision, PI Web API, PI Interfaces, PI Trusts, buffering, IDMZ, time-series]
---

# Purpose

This document provides a security architecture and engineering reference for the **AVEVA PI System** (formerly **OSIsoft PI**), from the perspective of an OT Security Architect.

The PI System is the industry's most widely deployed **process historian and time-series data platform**: it collects, contextualises, stores and serves operational data from across an entire industrial estate — often every PLC, SCADA system, DCS and sub-historian an organisation owns — and makes it available to engineers, analysts, business intelligence tools and, increasingly, cloud services. That role gives the PI System a threat model unlike any controller or SCADA platform in this handbook: it rarely **controls** anything, but it routinely holds **read access to everything**, making it one of the highest-value reconnaissance and lateral-planning targets in the estate, and one of the most consequential single points to get right at the **IT/OT boundary**.

This document covers the architecture, threat model, identity model, communications security, hardening and recoverability of a PI System deployment. Configuration procedures are excluded; consult the vendor documentation for the deployed release.

> **Verification note.** The PI System has evolved substantially across its OSIsoft-era and AVEVA-era releases, including a significant shift in recommended authentication (see 3.2 — PI Trusts vs Identity-based mappings) and expanding cloud integration (PI Cloud Connect, AVEVA Data Hub, AVEVA Connect). Confirm capabilities, defaults and component versions against the deployed release; statements below describe the platform family. AVEVA maintains an active PSIRT with a real advisory history across PI Data Archive, PI Vision and PI Web API — track it.

---

# Related Documents

* [Kepware-Server.md](../../PTC/Kepware%20Server/Kepware-Server.md) — a common upstream OPC aggregation source feeding PI Interfaces.
* [dataFEED-OPC-Suite.md](../../Softing/dataFEED%20OPC%20Suite/dataFEED-OPC-Suite.md) — the peer OPC aggregation platform.
* [PlantSCADA.md](../PlantSCADA/PlantSCADA.md) — a common southbound source feeding the PI System; different platform class (supervisory control vs data aggregation).
* [ISA95.md](../../../02-Standards/ISA95.md) — the process-historian function at Level 3 in the functional hierarchy; the PI System is the family's canonical example.
* [Purdue-Model.md](../../../02-Standards/Purdue-Model.md), [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md) — the IDMZ historian-replication pattern this document assumes throughout.
* [OT-Protocols.md](../../../04-Network/OT-Protocols.md) — OPC UA/DA and other southbound protocols PI Interfaces speak.
* [Identity-Management.md](../../../05-Identity/Identity-Management.md), [MFA.md](../../../05-Identity/MFA.md), [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md), [PKI.md](../../../05-Identity/PKI.md) — the identity layer this platform must plug into.
* [Monitoring.md](../../../09-Operations/Monitoring.md), [Logging.md](../../../09-Operations/Logging.md), [SIEM.md](../../../09-Operations/SIEM.md) — telemetry consumption.
* [Backup.md](../../../09-Operations/Backup.md), [Disaster-Recovery.md](../../../09-Operations/Disaster-Recovery.md), [Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md) — recoverability.

---

# Document Structure

1. Overview — role, components, data flow, deployment models, placement
2. Security — the estate-aggregation threat model, defense in depth, IEC 62443 / regulatory context
3. Identity and Access — PI Identities/Mappings, the PI Trusts legacy, database security
4. Data Collection Security — PI Interfaces/Connectors, buffering, southbound trust
5. Communications Security — component flows, the IDMZ pattern, PI-to-PI replication
6. Web, API and Cloud Exposure — PI Vision, PI Web API, cloud integration
7. Hardening — phased hardening across the estate
8. Monitoring and Logging
9. Backup, High Availability and Recovery
10. Best Practices and Common Mistakes

---

# 1 — Overview

## 1.1 Platform Role

The PI System's job is **aggregation**: pull time-series data from every source that matters (PLCs, SCADA, DCS, sub-historians, lab systems, third-party historians), contextualise it against an asset model, retain it for the life of the plant, and serve it to everyone who needs it — control-room engineers, process engineers, corporate analytics, regulatory reporting and, increasingly, cloud analytics platforms. It is infrastructure that **crosses every zone boundary in the enterprise by design**, which is exactly why its security matters disproportionately to its size.

## 1.2 Core Components

| Component | Function | Security relevance |
|---|---|---|
| **PI Data Archive** | The time-series database engine; stores and serves all collected values | The crown jewel — years of process history for the entire estate; read access here is estate-wide reconnaissance |
| **PI Asset Framework (AF)** | Hierarchical asset model, attributes, event frames, analytics | Defines *what the data means* and *how systems relate*; a valuable target for understanding plant structure |
| **PI Interfaces (UniInt-based) / PI Connectors** | Southbound collectors — OPC DA/UA, native protocol interfaces, files, other historians | The reach into OT: interface nodes routinely have live connectivity into control-system networks |
| **PI Buffering (PIBufSS / bufserv)** | Local queue on interface nodes caching data during Data Archive unavailability | An availability control, not a security control — but its buffer/store integrity matters |
| **PI Vision** | Web-based visualisation and dashboards | Browser-facing surface; the platform's most likely web-exposed component |
| **PI Web API** | REST API onto Data Archive and AF | Programmatic access; a common integration and automation surface, including to the cloud |
| **PI Integrator / PI Cloud Connect / AVEVA Data Hub** | Northbound integration to BI platforms and AVEVA/third-party cloud services | The IT/cloud boundary; governs what leaves the OT estate |
| **PI SDK / AF SDK / PI System Explorer / Management tools** | Engineering and administrative access to configuration | Administrative capability — treat like any engineering toolchain |

## 1.3 Data Flow

```text
PLC / SCADA / DCS / sub-historian
        │  (OPC UA/DA, native protocol, file)
PI Interface / PI Connector node  ──(local buffer)──►
        │  (PI Net Manager protocol, TLS on current releases)
PI Data Archive  ◄──────────────────────────────────────  PI AF (context)
        │
PI Vision (web) / PI Web API (REST) / PI Integrator (BI/cloud)
        │
Engineers, analysts, business systems, cloud services
```

The **direction of the arrows is the point**: data flows from OT toward IT and, often, the cloud — the PI System is frequently the largest legitimate, designed IT/OT data conduit in the enterprise, which is precisely why it must be the most deliberately governed one.

## 1.4 Deployment Models

* **On-premises, single estate** — Data Archive and AF hosted within the organisation's own infrastructure, typically at Level 3.
* **Distributed / multi-site with PI-to-PI replication** — site-level Data Archives replicate to a central or corporate PI System, classically **through the Industrial DMZ** (see 5.2) rather than direct site-to-corporate connectivity.
* **Cloud-extended** — on-premises PI System plus **PI Cloud Connect** or **AVEVA Data Hub/Connect** pushing selected data to cloud services for analytics, remote monitoring or enterprise reporting.
* **Collective / High Availability** — multiple Data Archive servers forming a collective for redundancy (see 9.2).

## 1.5 Purdue Placement

* **PI Data Archive and AF:** Level 3 (Site Operations) as the standard placement; a corporate-consolidation PI System sits above Level 3, reached only via the IDMZ.
* **PI Interface/Connector nodes:** placed close to their sources — often Level 2/3 boundary — with their OT-facing leg inside the relevant zone and their Data-Archive-facing leg toward Level 3.
* **PI Vision / PI Web API:** if enterprise or remote users need access, **publish only through the IDMZ**, never by exposing the Data Archive/AF servers themselves.
* **PI-to-PI replication, PI Integrator, cloud connectors:** these are the enterprise↔OT and OT↔cloud conduits; they terminate in, or are brokered by, the **Industrial DMZ** ([Purdue-Model.md](../../../02-Standards/Purdue-Model.md), [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md) — historian replication is the IDMZ's textbook use case).

---

# 2 — Security

## 2.1 Security Philosophy

Most platforms in this handbook are secured by asking "what can this system *do* to the process." For the PI System the dominant question is "what can this system *tell* an attacker about the process" — and, secondarily, "what could a compromised PI System *inject* into engineering/business decisions via falsified history." Because it aggregates broadly and sits astride the IT/OT boundary by design, PI System security is fundamentally about **governing the boundary it exists to cross**: what data leaves OT, through which conduit, authenticated how, and visible to whom.

## 2.2 Threat Model

1. **Estate-wide reconnaissance via read access** — an attacker who reaches PI Vision, PI Web API, or the Data Archive with any meaningful read privilege can enumerate assets (via AF), understand plant topology, process ranges and normal operating behaviour across the *entire* connected estate — reconnaissance depth no single controller or SCADA system offers. This is the platform's defining risk.
2. **PI Trusts legacy exposure** — the historical **PI Trusts** authentication mechanism grants access based on IP address/hostname/application name rather than verified identity; a trust misconfigured too broadly (or exploitable via IP spoofing/host compromise on a trusted subnet) grants write/administrative access with no credential check (see 3.2).
3. **Interface-node compromise as an OT pivot** — PI Interface/Connector nodes routinely have live connectivity into control-system networks for collection; a compromised interface node is a ready-made bridge from the IT-adjacent PI environment into OT ([ICS-Kill-Chain.md](../../../07-Threat-Intelligence/ICS-Kill-Chain.md) → Lateral Movement, Discovery).
4. **PI Vision / PI Web API exposure** — unauthenticated or weakly authenticated web/API access exposed beyond the intended audience (or beyond the IDMZ) is a direct data-exfiltration and reconnaissance path.
5. **Data integrity / falsified history** — a compromise that can write to Data Archive or AF can alter historical records, corrupting investigations, regulatory reporting and downstream analytics/ML trained on that history — a subtler but real integrity risk distinct from real-time control manipulation.
6. **Uncontrolled cloud/BI egress** — PI Integrator, PI Cloud Connect or ad hoc export tooling pushing more data, or more sensitive data, to cloud/BI destinations than intended, without the governance applied to other IT/OT conduits.
7. **Credential/service-account sprawl** — Interfaces, Vision, Web API and integrations each typically need service accounts; sprawling, over-privileged, rarely reviewed service accounts are a common PI-estate finding.
8. **Platform vulnerabilities** — the product family has a real public advisory history (Data Archive, Vision and Web API components); track AVEVA PSIRT and prioritise via [Vulnerability-Management.md](../../../09-Operations/Vulnerability-Management.md).

## 2.3 Defense in Depth Applied

| Layer | PI System specifics |
|---|---|
| Physical | Server-room protection for Data Archive/AF hosts |
| Network | Data Archive/AF in a controlled Level 3 zone; interface nodes bounded to their source zones; Vision/Web API/replication brokered through the IDMZ only |
| Identity | Windows-integrated PI Identities/Mappings (not IP-based Trusts); MFA on Vision/Web API and administrative access; least-privilege service accounts per interface/integration |
| Application | Database Security (element-level access control in Data Archive/AF), Vision content permissions, Web API scope restriction |
| Endpoint | Hardened interface nodes and application servers; allowlisting; patched per vendor cadence |
| Monitoring | Authentication and Trust-usage events, AF/Data Archive write events, replication and export volume, forwarded to SIEM |
| Recovery | Archive/AF backups, collective/HA for availability, tested restore of the full stack |

See [Defense-in-Depth.md](../../../03-Architecture/Defense-in-Depth.md).

## 2.4 Standards and Regulatory Context

* **IEC 62443** — the PI System sits largely outside the IACS control zones proper but implements much of the **Level 3/IDMZ conduit discipline** the standard expects for enterprise-OT integration; treat Data Archive/AF as a zone with an explicit Target SL, and every replication/Vision/API path as a documented conduit. See [IEC62443.md](../../../02-Standards/IEC62443.md).
* **NIS2 / national law** — as the platform through which the largest legitimate volume of OT data typically leaves the control environment, PI System governance is directly relevant to the risk-management and incident-detection measures in [NIS2.md](../../../01-Legislation/NIS2.md) and the Czech capability domains ([Czech-Cybersecurity-Act.md](../../../01-Legislation/Czech-Cybersecurity-Act.md) explicitly lists historians as a critical asset category for reporting/policy purposes).
* **Data classification** — because PI data can reveal production rates, recipes, yields and process IP, treat estate-wide PI read access as a data-classification and confidentiality question, not merely an availability/integrity one — an inversion of the usual OT Safety→Availability→Integrity→**Confidentiality** ordering that architects should recognise explicitly, not silently override ([OT-Security-Philosophy.md](../../../00-Core/OT-Security-Philosophy.md)).

---

# 3 — Identity and Access

## 3.1 Identity Model

The PI System authorizes through **PI Identities**, which are **mapped** to Windows accounts/groups or to Windows-integrated security, and granted rights via **Database Security** on individual Data Archive/AF elements (points, attributes, databases). Design rules:

* Model PI Identities on **operational function** (read-only process engineer, AF administrator, interface service account, Vision viewer), not broad "PI Admins for everyone who asked."
* Use **element-level Database Security** to scope read access — not every identity needs estate-wide visibility; segment by plant/area/asset in AF and mirror that in Data Archive point security.
* Reserve **AF/Data Archive administrative rights** for a small, PAM-governed group; administrative capability here effectively means "can redefine what the entire organisation believes its historical process data says."

## 3.2 PI Trusts vs Identity-Based Mappings — the Critical Decision

PI Trusts are a **legacy authentication mechanism** that grants a connecting application access based on **IP address, hostname, application name or Windows domain/OU membership presented at connection time**, without independently verifying the identity of the connecting principal. This was historically the default and remains present for backward compatibility, but current AVEVA guidance is clear: **migrate to Windows-integrated Identities and Mappings**, and eliminate broad or default Trusts.

Practical guidance:

* **Audit every existing Trust.** Identify what each was created for, how broad its matching criteria are (an overly broad IP range or "any application" trust is a standing bypass), and whether it is still needed.
* **Migrate to Identity Mappings** (Windows-integrated security) wherever the connecting application supports it — this is the platform's actual credential check.
* **Retire unused/legacy Trusts** entirely rather than leaving them "just in case"; a forgotten Trust on a decommissioned subnet is exactly the kind of gap this handbook warns about elsewhere for forgotten devices.
* Where a Trust cannot yet be eliminated (older interface software), scope it as narrowly as the mechanism allows and document it as a compensating-control exception with a remediation date ([Risk-Based-Security.md](../../../03-Architecture/Risk-Based-Security.md)).

## 3.3 Service Accounts and Integrations

Every PI Interface, Connector, Vision instance and API integration typically runs under a service account. Inventory all of them; enforce least privilege (an interface account needs write access to its own points, not the whole Archive); disable interactive logon; rotate credentials; review periodically ([Identity-Management.md](../../../05-Identity/Identity-Management.md) → service identities, [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md)).

## 3.4 Human Access and MFA

* PI Vision and PI Web API access for humans should sit behind the organisation's standard identity provider with **MFA enforced**, especially for any access reachable beyond the trusted plant network ([MFA.md](../../../05-Identity/MFA.md)).
* Administrative access to Data Archive/AF/System Management Tools is privileged access: MFA, named accounts, session logging, JIT elevation where practical ([Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md)).

---

# 4 — Data Collection Security

## 4.1 PI Interfaces and Connectors

Each interface node is a **bridge between an OT source and the PI estate**. Treat it with the same discipline as any dual-homed asset: minimal installed interfaces per node, least-privilege source credentials (an OPC UA interface should use a scoped OPC UA identity, not a domain-admin-equivalent account), and network placement that keeps the OT-facing leg inside its proper zone.

## 4.2 Buffering

**PIBufSS/bufserv** locally queues collected data on the interface node when the Data Archive is unreachable, preventing data loss during network or server outages. This is an **availability mechanism**, not a security boundary — an attacker who compromises the interface node still has whatever access the node's credentials provide, buffering or not. Do not let buffering's reliability benefits create a false sense that the interface node is a low-value target; it remains a bridge into OT and must be hardened and monitored as such.

## 4.3 Southbound Trust

Interfaces speaking OPC UA should use certificate-based mutual authentication and a managed trust list ([Certificates.md](../../../05-Identity/Certificates.md), [OT-Protocols.md](../../../04-Network/OT-Protocols.md)); interfaces speaking legacy unauthenticated protocols (OPC DA, some native drivers) inherit that protocol's lack of security and must rely on network containment around the source, exactly as documented for the source platform itself (e.g. [PlantSCADA.md](../PlantSCADA/PlantSCADA.md), [Modicon-Premium.md](../../Schneider%20Electric/Modicon%20Premium/Modicon-Premium.md)).

---

# 5 — Communications Security

## 5.1 Component Flows

| Flow | Direction | Notes |
|---|---|---|
| Source (PLC/SCADA/DCS) → PI Interface/Connector | southbound | Per-protocol security as documented at the source; interface node bounded to source zone |
| PI Interface/Connector → PI Data Archive | interface→L3 | PI Net Manager protocol; TLS on current releases — verify and enable |
| PI AF ↔ Data Archive | L3 internal | Context/asset-model traffic |
| PI Vision / PI Web API → Data Archive/AF | web/API tier→L3 | Publish the web/API tier via the IDMZ for any non-plant-network access |
| Site PI System → Corporate PI System (PI-to-PI) | site→enterprise | **Terminate in the IDMZ**; do not connect site Data Archive directly to a corporate collective |
| PI Integrator / Cloud Connect → BI/cloud | L3→enterprise/cloud | Outbound; scope explicitly what data leaves and to where |

Design every one of these as a documented conduit with an explicit business justification, per [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md) and [Firewall-Design.md](../../../04-Network/Firewall-Design.md) — a PI estate with an undocumented flow is a PI estate with an unmanaged IT/OT bridge.

## 5.2 The IDMZ Pattern for PI Replication

The canonical secure architecture for multi-site/corporate PI: each site's Data Archive replicates (PI-to-PI) to a **DMZ-resident intermediate Data Archive**, and the corporate/enterprise PI System pulls from that intermediate archive — never directly from the site. This mirrors the general **historian-replica IDMZ pattern** already established in [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md) and [Purdue-Model.md](../../../02-Standards/Purdue-Model.md), and is the single most important architectural decision in a multi-site PI deployment. Direct site-to-corporate PI connectivity, bypassing the IDMZ "because replication is just data, not control," is a common and serious anti-pattern.

## 5.3 Time Synchronization

All Data Archive, AF and interface nodes on the organisational NTP hierarchy; time-series data and event correlation are meaningless without it ([Logging.md](../../../09-Operations/Logging.md)).

---

# 6 — Web, API and Cloud Exposure

* **PI Vision** — HTTPS only, behind the organisation's identity provider with MFA; publish beyond the plant network only via the IDMZ; scope display/content permissions per audience rather than granting broad visibility by default.
* **PI Web API** — treat as a full production API: authenticated, least-privilege-scoped per consumer, rate-limited, logged; inventory every consumer application and credential.
* **PI Integrator, PI Cloud Connect, AVEVA Data Hub/Connect** — define explicitly *what* data is permitted to leave the OT estate, to which destination, and under what data-classification decision (2.4); do not allow ad hoc expansion of cloud-forwarded tags without a review step.
* Disable/remove any web or API component not in active, documented use — the platform's breadth means unused surface accumulates quietly.

---

# 7 — Hardening

Phased, in dependency order; each phase concludes with validation and a baseline capture ([Configuration-Management.md](../../../09-Operations/Configuration-Management.md)).

**Phase 1 — Platform baseline.** Harden the OS of Data Archive, AF, interface and Vision/Web API hosts per organizational Windows baseline ([Windows-Hardening.md](../../../09-Operations/Windows-Hardening.md)); minimal roles per server; patched per vendor cadence.

**Phase 2 — Identity migration.** Audit and migrate PI Trusts to Identity Mappings (3.2); establish the PI Identity model (3.1); inventory and least-privilege every service account (3.3).

**Phase 3 — Database Security.** Apply element-level Database Security in Data Archive and AF reflecting the plant/area/asset segmentation decided in 3.1; remove default-broad access grants.

**Phase 4 — Component communications.** Enable TLS on PI Net Manager and inter-component traffic where the deployed release supports it; deploy certificates from the internal PKI ([PKI.md](../../../05-Identity/PKI.md)).

**Phase 5 — Web/API/Cloud governance.** Apply section 6 in full: MFA on Vision/Web API, IDMZ publication only, explicit cloud-data scoping.

**Phase 6 — Network hardening.** Zone Data Archive/AF at Level 3; bound interface nodes to their source zones; enforce the IDMZ replication pattern (5.2); default-deny at every boundary ([Firewall-Design.md](../../../04-Network/Firewall-Design.md)).

**Phase 7 — Validation.** Verify no unmanaged Trusts remain; confirm Database Security matches the intended segmentation by testing sample identities; confirm Vision/Web API unreachable except via IDMZ; confirm replication terminates in the IDMZ, not direct site-to-corporate; capture the baseline.

---

# 8 — Monitoring and Logging

Forward to the SIEM ([SIEM.md](../../../09-Operations/SIEM.md)) and treat as high-value detections ([Monitoring.md](../../../09-Operations/Monitoring.md)):

* **Trust-based connections** (any remaining PI Trust usage) — each occurrence is worth reviewing given 3.2; a spike or a connection from an unexpected source via a Trust is high-severity.
* Authentication failures and administrative logins to Data Archive/AF/System Management Tools.
* **Database Security changes** — new/modified access grants, especially broad or administrative ones.
* **AF structure changes** and **Data Archive point/value writes** outside expected interface behaviour — potential falsified-history activity (2.2 #5).
* New or changed PI Interface/Connector configurations; new source connections into OT-facing interface nodes.
* Vision/Web API authentication anomalies and unusual query/export volume (bulk extraction signal).
* PI-to-PI replication path changes; any direct site-to-corporate connection attempt bypassing the IDMZ.
* Cloud-connector configuration changes (what is now being sent, and where).

---

# 9 — Backup, High Availability and Recovery

## 9.1 What to Back Up

Data Archive archive files and configuration, the full AF database, PI Identity/Mapping and Database Security configuration, interface node configurations and buffering state, Vision display/content configuration, Web API and integration configuration, and certificates/PKI dependencies. Follow [Backup.md](../../../09-Operations/Backup.md); validate restores per [Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md).

## 9.2 High Availability ≠ Backup

A Data Archive **collective** provides redundancy against single-server failure and supports rolling maintenance; like any redundancy mechanism documented elsewhere in this handbook, it **replicates corruption and unauthorized change as faithfully as legitimate data**. It is not a substitute for offline, tested backups and a rehearsed [Disaster-Recovery-Plan.md](../../../09-Operations/Disaster-Recovery-Plan.md).

## 9.3 Recovery Scope

Recovery is complete only when Data Archive, AF, interface connectivity, Database Security/Identity mappings and Vision/Web API access have all been validated against a known-good baseline — a "server is back online" restoration that silently drops Database Security or Identity Mappings back to defaults reopens exactly the exposure this document exists to prevent.

---

# 10 — Best Practices and Common Mistakes

**Practices that pay off:** PI Trusts audited to zero (or a documented, dated exception list); Database Security modelled on real operational need, not "read access for everyone"; every replication/Vision/API path documented as a conduit; PI-to-PI replication always via the IDMZ; MFA on all Vision/Web API and administrative access; service accounts inventoried and least-privileged; cloud-forwarded data explicitly scoped and reviewed.

**Common mistakes:**

* Broad or default **PI Trusts** left in place for convenience — the platform's signature legacy weakness.
* Every engineer and analyst granted estate-wide read access "because it's just data."
* Direct site-to-corporate PI-to-PI replication bypassing the IDMZ.
* PI Vision or PI Web API published to the Internet/enterprise without MFA.
* Interface-node service accounts running with far more privilege than the single interface requires.
* No monitoring of Database Security changes or AF structure changes — a quiet privilege-escalation blind spot.
* Cloud connectors expanded ad hoc, with no review of what data now leaves the OT estate.
* Treating a Data Archive collective as sufficient backup; never testing full-stack restoration.

---

# Architect Notes

The PI System inverts the usual OT security intuition: the danger is rarely that someone will *control* something through it, and almost always that someone will *know* something they shouldn't, or that the organisation will come to *believe* something false about its own history. Budget accordingly — identity (kill the Trusts), Database Security segmentation, and disciplined IDMZ conduit governance for every replication/Vision/API path buy far more risk reduction here than any device-level tweak. Treat the PI System as what it actually is: the largest, most legitimate, most useful IT/OT data bridge most organisations will ever build — which is exactly why it deserves the most deliberate governance.

---

# Decision Support

1. Have all PI Trusts been audited, and is there a dated plan to eliminate the remainder in favour of Identity Mappings?
2. Does Database Security in Data Archive/AF reflect real least-privilege segmentation, or is access effectively estate-wide by default?
3. Is every replication, Vision, Web API and cloud-connector path documented as a conduit with an explicit business justification?
4. Does PI-to-PI replication terminate in the IDMZ, with no direct site-to-corporate connectivity?
5. Is MFA enforced for all Vision/Web API and administrative access?
6. Are interface-node and integration service accounts inventoried and least-privileged?
7. Is Database Security / AF-structure change monitored and forwarded to the SIEM?
8. Has full-stack recovery (Archive + AF + Database Security/Identity mappings + interface reconnection) been demonstrated, not just server restoration?

---

# AI Guidance

When discussing the AVEVA PI System:

* Frame it as estate-wide data-aggregation infrastructure whose primary risk is reconnaissance/confidentiality and IT/OT boundary governance, not process control — distinct from every controller/SCADA document in this handbook.
* Treat **PI Trusts** as the platform's signature legacy weakness; always recommend auditing and migrating to Identity Mappings, and flag any remaining Trust as a documented, dated exception rather than a permanent state.
* Insist that PI-to-PI replication, PI Vision and PI Web API terminate in or are brokered by the IDMZ — never direct site-to-corporate or direct-to-Internet connectivity.
* Recommend Database Security segmentation on real operational need; challenge "read access for everyone" as a default.
* Recognise that PI data confidentiality (production rates, recipes, process IP) can be a first-order concern even though it doesn't fit the usual Safety/Availability-first OT framing — call this out explicitly rather than silently reordering the canonical hierarchy.
* Delegate southbound protocol depth to OT-Protocols.md, IDMZ/segmentation detail to Network-Segmentation.md and Purdue-Model.md, and identity mechanics to the 05-Identity layer; verify release-specific capabilities and AVEVA PSIRT advisories against current vendor sources rather than asserting them.

---

# Sources / Grounding

* **AVEVA PI System documentation** (Data Archive, Asset Framework, PI Interfaces/Connectors, PI Vision, PI Web API) — architecture, security configuration and the current PI Trusts → Identity Mappings migration guidance (authoritative for the deployed release).
* **AVEVA PSIRT / CISA ICS advisories** for PI System components — the platform's vulnerability history and current patch guidance.
* **ISA/IEC 62443** — Level 3/IDMZ conduit discipline applied to historian/data-aggregation infrastructure. See [IEC62443.md](../../../02-Standards/IEC62443.md).
* **NIST SP 800-82 Rev. 3** — OT security-architecture context, incl. historian placement.

> Foundational references; the vendor documentation of the deployed release is authoritative for product specifics.

---

# Related Documents

* [PlantSCADA.md](../PlantSCADA/PlantSCADA.md)
* [GeoSCADA.md](../../Schneider%20Electric/GeoSCADA/GeoSCADA.md)
* [Purdue-Model.md](../../../02-Standards/Purdue-Model.md)
* [ISA95.md](../../../02-Standards/ISA95.md)
* [IEC62443.md](../../../02-Standards/IEC62443.md)
* [NIS2.md](../../../01-Legislation/NIS2.md)
* [Czech-Cybersecurity-Act.md](../../../01-Legislation/Czech-Cybersecurity-Act.md)
* [OT-Security-Philosophy.md](../../../00-Core/OT-Security-Philosophy.md)
* [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md)
* [Firewall-Design.md](../../../04-Network/Firewall-Design.md)
* [OT-Protocols.md](../../../04-Network/OT-Protocols.md)
* [Risk-Based-Security.md](../../../03-Architecture/Risk-Based-Security.md)
* [Identity-Management.md](../../../05-Identity/Identity-Management.md)
* [MFA.md](../../../05-Identity/MFA.md)
* [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md)
* [PKI.md](../../../05-Identity/PKI.md)
* [Certificates.md](../../../05-Identity/Certificates.md)
* [Configuration-Management.md](../../../09-Operations/Configuration-Management.md)
* [Monitoring.md](../../../09-Operations/Monitoring.md)
* [Logging.md](../../../09-Operations/Logging.md)
* [SIEM.md](../../../09-Operations/SIEM.md)
* [Backup.md](../../../09-Operations/Backup.md)
* [Disaster-Recovery.md](../../../09-Operations/Disaster-Recovery.md)
* [Disaster-Recovery-Plan.md](../../../09-Operations/Disaster-Recovery-Plan.md)
* [Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md)
* [Vulnerability-Management.md](../../../09-Operations/Vulnerability-Management.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-07 | Initial release — new 08-Technologies platform reference for the AVEVA PI System (formerly OSIsoft PI), written to the platform-document standard but framed around its distinct data-aggregation/IT-OT-boundary role: component architecture (Data Archive, AF, Interfaces/Connectors, buffering, Vision, Web API, cloud connectors), estate-wide-reconnaissance threat model, the PI Trusts-to-Identity-Mappings migration as the platform's signature identity decision, the IDMZ PI-to-PI replication pattern, Database Security segmentation, web/API/cloud governance, phased hardening, monitoring and HA-vs-backup distinction; resolves a Planned-equivalent manifest gap; first Historian-class platform document in 08-Technologies |
| 1.0.1 | 2026-07-07 | Added cross-links to Kepware-Server.md and dataFEED-OPC-Suite.md as typical upstream OPC aggregation sources |
| 1.0.2 | 2026-07-07 | Resolved Windows-Hardening.md reference now that the document exists |
