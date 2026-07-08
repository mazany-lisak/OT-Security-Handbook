---
id: kepware-server
title: "PTC Kepware Server (KEPServerEX) — Security Architecture and Engineering Reference"
category: Technologies
layer: "08-Technologies/PTC/Kepware Server"
vendor: "PTC"
product: "Kepware Server (KEPServerEX)"
version: 1.0.1
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Security architecture and engineering reference for the PTC Kepware Server (KEPServerEX) OPC
  aggregation platform: plugin-driver architecture, the OPC DA/DCOM legacy problem, OPC UA and
  IoT Gateway (MQTT/REST/ThingWorx) northbound paths, the bidirectional-write threat model, user
  management, phased hardening, monitoring, backup and IEC 62443 conduit placement.
keywords: [Kepware, KEPServerEX, PTC, OPC server, OPC aggregator, protocol gateway, OPC DA, DCOM, OPC UA, IoT Gateway, MQTT, ThingWorx, connectivity plugin]
---

# Purpose

This document provides a security architecture and engineering reference for the **PTC Kepware Server** (commonly known by its long-standing product name **KEPServerEX**), from the perspective of an OT Security Architect.

Kepware occupies a role distinct from every platform documented so far in this handbook: it is not a controller, not a SCADA system, and not primarily a historian — it is an **OPC aggregation server / protocol gateway**. It speaks dozens of native industrial protocols southbound to controllers of every major vendor through a **plugin-driver architecture**, and exposes that aggregated data northbound as **OPC DA, OPC UA, and, via the IoT Gateway, MQTT/REST/ThingWorx**. [ISA95.md](../../../02-Standards/ISA95.md) already identifies this role explicitly — Kepware KEPServerEX is its canonical worked example of the **"OPC aggregation server"** pattern used to terminate legacy OPC DA before it crosses a firewall boundary.

That aggregation role is precisely what makes Kepware security-critical: unlike a historian, which mostly *reads*, an OPC aggregation server routinely sits **directly in the bidirectional control-data path** — a SCADA or MES writing a setpoint typically writes *through* Kepware to the controller. Its compromise is closer to "a pivot with write reach into every connected device across every connected protocol" than to a pure reconnaissance risk. This document covers the architecture, threat model, identity model, communications security, hardening and recoverability of a Kepware Server deployment. Configuration procedures are excluded; consult the vendor documentation for the deployed release.

> **Verification note.** KEPServerEX capabilities, supported drivers, and security features (OPC UA security policies, User Manager granularity, IoT Gateway protocol support) vary by release. PTC maintains an active PSIRT with a real advisory history across specific device-driver plugins and core services — track it and confirm capabilities against the deployed version rather than assuming them.

---

# Related Documents

* [dataFEED-OPC-Suite.md](../../Softing/dataFEED%20OPC%20Suite/dataFEED-OPC-Suite.md) — the peer OPC aggregation platform in this handbook; comparable threat model, different architecture (modular components, OPC UA Tunnel vs. plugin drivers).
* [PI-System.md](../../AVEVA/PI%20System/PI-System.md) — a common northbound consumer of Kepware-aggregated data; contrast its read-mostly aggregation model against Kepware's bidirectional control-path role.
* [OPC-UA.md](../../../04-Network/OPC-UA.md) — the OPC UA security deep-dive for Kepware's preferred northbound interface.
* [ISA95.md](../../../02-Standards/ISA95.md) — defines the OPC-aggregation-server pattern this document implements in detail; read together, especially the OPC DA→firewall termination rule.
* [OT-Protocols.md](../../../04-Network/OT-Protocols.md) — southbound native-protocol and OPC DA/UA security detail (single source of truth).
* [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md), [Firewall-Design.md](../../../04-Network/Firewall-Design.md) — placing and bounding the aggregation server as a conduit termination point.
* [Identity-Management.md](../../../05-Identity/Identity-Management.md), [PKI.md](../../../05-Identity/PKI.md), [Certificates.md](../../../05-Identity/Certificates.md) — identity and certificate context for OPC UA and IoT Gateway.
* [Monitoring.md](../../../09-Operations/Monitoring.md), [Backup.md](../../../09-Operations/Backup.md) — operations around the server.

---

# Document Structure

1. Overview — role, plugin architecture, northbound paths, placement
2. Security — the bidirectional-conduit threat model, defense in depth, IEC 62443 alignment
3. Identity and Access — User Manager, project security, OPC UA identity, service accounts
4. Communications Security — southbound drivers, OPC DA/DCOM, OPC UA, IoT Gateway/cloud
5. Hardening — phased hardening lifecycle
6. Monitoring and Logging
7. Backup and Recovery
8. Best Practices and Common Mistakes

---

# 1 — Overview

## 1.1 Platform Role

Kepware's job is **protocol translation and aggregation**: a single Windows-hosted service maintains live connections to potentially hundreds of controllers across many vendors and protocols, and exposes their data through a **unified OPC namespace** to every client that needs it — SCADA, HMI, historian, MES, custom applications. This is what makes it valuable, and it is exactly the property that makes it a **high-value single point of compromise**: one server, many trust relationships, in both directions.

## 1.2 Plugin-Driver Architecture

Kepware's defining architectural feature is its **driver/plugin model**: connectivity to each device family (Allen-Bradley, Siemens, Modbus, Schneider, GE, Omron, and many others, alongside the vendor-neutral protocols in [OT-Protocols.md](../../../04-Network/OT-Protocols.md)) is provided by a separate, independently updatable plugin. Security consequences:

* **Attack-surface breadth scales with installed drivers.** Every enabled plugin is a protocol parser with its own historical vulnerability record; install and enable only the drivers actually required for the connected estate.
* **Driver-specific advisories are common.** PTC PSIRT advisories frequently target individual driver plugins rather than the core server — patch discipline must track drivers, not just the base product (section 7).
* **Native southbound protocol security is inherited, not added.** Where a driver speaks an unauthenticated legacy protocol (Modbus, S7comm, etc.), Kepware does not retroactively secure it — the same containment principles documented for the source controller apply on that leg (e.g. [Modicon-Premium.md](../../Schneider%20Electric/Modicon%20Premium/Modicon-Premium.md), [S7-300.md](../../SIEMENS/S7-300/S7-300.md)).

## 1.3 Northbound Interfaces

* **OPC DA** — the legacy, COM/DCOM-based interface; still common where older SCADA/HMI clients require it (section 4.2).
* **OPC UA** — the modern, security-capable interface; Kepware's OPC UA server supports certificate-based mutual authentication and Sign/SignAndEncrypt security policies (section 3.3).
* **IoT Gateway** — an add-on module publishing selected tags via **MQTT, REST, and PTC's ThingWorx** platform, extending the aggregation northbound toward IIoT/cloud analytics (section 4.4).
* **Native OPC UA/DA clients and custom applications** connecting directly for SCADA/MES integration.

## 1.4 Typical Architecture

```text
Controllers (multi-vendor: Ethernet/IP, Modbus, S7comm, PROFINET, etc.)
        │  native protocols via driver plugins
Kepware Server (KEPServerEX)
        │           │                    │
   OPC DA (legacy)  OPC UA (preferred)   IoT Gateway (MQTT/REST/ThingWorx)
        │           │                    │
   SCADA/HMI    SCADA/Historian/MES   Cloud / IIoT analytics
```

The server is deliberately positioned to be reached from **both directions** — many controllers south, many consuming applications north — which is the architectural fact every security decision in this document follows from.

## 1.5 ISA-95 / Purdue Placement

Kepware sits at the **Level 2/3 boundary**, exactly as [ISA95.md](../../../02-Standards/ISA95.md) describes the OPC-aggregation-server role: south-facing connections reach into **Level 1/2** controller networks; north-facing OPC UA/IoT Gateway connections serve **Level 2/3** SCADA, historian and MES consumers. Where legacy OPC DA must remain in use, **terminate it on the Kepware server itself, on the OT side of any firewall boundary** — never bridge OPC DA/DCOM traffic through a firewall via dynamic port mapping ([ISA95.md](../../../02-Standards/ISA95.md) states this explicitly as a design rule). Any IoT Gateway/cloud path is an enterprise↔OT conduit and must be brokered through the **Industrial DMZ** ([Purdue-Model.md](../../../02-Standards/Purdue-Model.md)).

---

# 2 — Security

## 2.1 Security Philosophy

Most platforms in this handbook are secured by asking either "what can this system control" or "what can this system reveal." Kepware requires **both** questions simultaneously, because it is architected to do both at once, for many devices, through many protocols, at the same time. The governing question is: **for every southbound driver and every northbound interface, who can reach it, with what privilege, and is that privilege actually needed?**

## 2.2 Threat Model

1. **Server compromise as a multi-protocol, bidirectional pivot** — an attacker controlling the Kepware host gains read/write reach across every connected controller, through whichever native protocol each driver speaks, regardless of that protocol's own security. This is the platform's defining risk and has no equivalent single point of concentration elsewhere in a typical OT network short of the SCADA server itself.
2. **OPC DA / DCOM exposure** — DCOM's dynamic port allocation, complex ACL model, and historical tendency toward "just disable DCOM security to make it work" during commissioning make it one of the most persistently misconfigured legacy technologies in OT. A DCOM misconfiguration exposed across a network boundary is a well-documented, high-impact finding (section 4.2).
3. **Plugin/driver vulnerabilities** — protocol parsers processing untrusted network input are a classic vulnerability class; a flaw in one driver can potentially be reached by any host that can talk that protocol to the server (2.2.1 above), not merely by hosts with legitimate OPC client access.
4. **Overprivileged OPC/IoT Gateway clients** — a client application (SCADA, MES, custom integration) granted broader tag read/write scope than its function requires becomes an amplifier for any compromise of *that* client.
5. **IoT Gateway / cloud bridge misconfiguration** — MQTT/REST/ThingWorx publishing more tags, or more sensitive tags, than intended to a cloud or IIoT destination, without the same governance applied to other IT/OT conduits (mirrors the PI System cloud-connector risk — see [PI-System.md](../../AVEVA/PI%20System/PI-System.md) § 2.2, § 6).
6. **Project file exposure** — the Kepware project (channel/device/tag configuration) reveals the entire connected estate's topology; protect it like any sensitive engineering artifact, not a convenience export.
7. **Service-account over-privilege** — the Kepware Windows service and any configured southbound credentials frequently accumulate more access than required over time; periodic review is essential.
8. **Platform/plugin vulnerabilities** — track **PTC PSIRT** advisories; several historical advisories affect specific drivers or the IoT Gateway component specifically, not the whole product uniformly ([Vulnerability-Management.md](../../../09-Operations/Vulnerability-Management.md)).

## 2.3 Defense in Depth Applied

| Layer | Kepware Server specifics |
|---|---|
| Physical | Server-room protection for the host |
| Network | Server at the L2/L3 boundary; OPC DA/DCOM terminated on-server, never bridged through a firewall; IoT Gateway/cloud paths via the IDMZ only |
| Identity | User Manager roles least-privileged per client/integration; OPC UA certificate-based auth; service accounts minimally scoped |
| Application | Only required driver plugins installed/enabled; project file protected; tag-level permissions applied |
| Endpoint | Hardened Windows host; allowlisting; patched per vendor and per-driver advisory cadence |
| Monitoring | OPC session/tag-write events, DCOM connection anomalies, IoT Gateway publish volume, to SIEM |
| Recovery | Project backup, driver/version inventory, tested restore |

See [Defense-in-Depth.md](../../../03-Architecture/Defense-in-Depth.md).

## 2.4 IEC 62443 Alignment

* Treat Kepware as a **conduit-termination component**: it is precisely the "OPC aggregation server" IEC 62443-informed architecture expects at the boundary between zones of differing trust ([IEC62443.md](../../../02-Standards/IEC62443.md), and [ISA95.md](../../../02-Standards/ISA95.md) which names this pattern explicitly).
* FR1/FR2 (identification, use control) → User Manager roles, OPC UA user/certificate authentication, least-privilege client scoping.
* FR3/FR4 (integrity, confidentiality) → OPC UA Sign/SignAndEncrypt northbound; southbound integrity is bounded by what each native protocol/driver can offer — document the gap for unauthenticated legacy protocols as a compensating-control case, exactly as for the source controller itself.
* FR5 (restricted data flow) → conduit design in section 4; OPC DA never crossing a firewall boundary undissected.
* Where southbound drivers connect to legacy controllers with no native security, the **zone containing both the controller and its Kepware connection** is the unit of Target-SL assessment — not the Kepware server in isolation ([Risk-Based-Security.md](../../../03-Architecture/Risk-Based-Security.md)).

---

# 3 — Identity and Access

## 3.1 User Manager

Kepware's **User Manager** defines named users/groups and scopes their permissions across administrative functions (project editing, driver configuration) and **runtime OPC/IoT Gateway access** (per-interface, and in some configurations per-tag-group). Design rules:

* Separate **administrative** access (project configuration, service management) from **client/runtime** access (OPC/IoT Gateway data read/write) — a SCADA integration account should never carry project-edit rights.
* Scope client accounts to the **minimum tag namespace** they require; a client needing read access to one production line should not receive server-wide read, let alone write, scope.
* No shared credentials across multiple client applications — each integration gets its own named identity so that activity (and any incident) is attributable ([Identity-Management.md](../../../05-Identity/Identity-Management.md)).

## 3.2 Project Security

The Kepware project file defines every channel, device and tag in the connected estate — it is a **topology map of the OT environment**. Password-protect project editing, store project backups with the same rigor as any sensitive engineering artifact ([Configuration-Management.md](../../../09-Operations/Configuration-Management.md)), and restrict who may open the configuration interface, not merely who may run the service.

## 3.3 OPC UA Identity

Enable **certificate-based mutual authentication** on the OPC UA server interface with a managed trust list (no "trust any client certificate"), issued from the internal PKI where practical ([PKI.md](../../../05-Identity/PKI.md), [Certificates.md](../../../05-Identity/Certificates.md)); enforce **Sign** at minimum, **SignAndEncrypt** where clients support it, and pair certificate identity with **User Manager** authentication rather than relying on transport security alone.

## 3.4 Service Accounts

The Windows service hosting Kepware, and any stored southbound device credentials (for protocols that support authentication), should run least-privileged, non-interactive, and be included in periodic access review ([Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md)).

---

# 4 — Communications Security

## 4.1 Southbound (Native Protocols)

Per-protocol depth is owned by [OT-Protocols.md](../../../04-Network/OT-Protocols.md). Kepware-specific guidance: enable only the driver plugins actually connected to real devices; where a driver speaks an authenticated protocol (some modern controllers), configure and use that authentication rather than defaulting to open access; where it speaks an unauthenticated legacy protocol, rely on network containment around the source controller exactly as documented in that controller's own reference document.

## 4.2 OPC DA / DCOM — the Legacy Problem

OPC DA rides on **COM/DCOM**, which historically means dynamic RPC port ranges, a notoriously fiddly launch/access-permission ACL model, and — very commonly in real deployments — DCOM security settings loosened or disabled during commissioning "to make it connect" and never revisited. Guidance:

* **Never bridge OPC DA/DCOM across a firewall boundary.** [ISA95.md](../../../02-Standards/ISA95.md) states this as an explicit design rule: terminate OPC DA **on the Kepware server itself**, on the OT side of the boundary, and expose only **OPC UA** (or another authenticated interface) to any client beyond that zone.
* Where OPC DA clients remain within the same zone as the Kepware server, correctly scope DCOM launch/access/configuration permissions per the vendor's hardening guidance rather than the common shortcut of granting Everyone/Anonymous access.
* **Plan active migration off OPC DA** for every remaining client; it is a deprecated, insecure-by-design technology, not a permanent architecture.

## 4.3 OPC UA (Preferred Northbound)

Prefer OPC UA for every client capable of it: certificate-based mutual authentication, Sign/SignAndEncrypt, User Manager identity layered on top (section 3.3). This is the interface that lets Kepware actually meet a meaningful Target SL at the boundary it occupies.

## 4.4 IoT Gateway (MQTT / REST / ThingWorx)

Treat the IoT Gateway exactly as any cloud/enterprise data-egress path documented elsewhere in this handbook (compare [PI-System.md](../../AVEVA/PI%20System/PI-System.md) § 6, [WebAccess-DMP.md](../../Advantech/WebAccess-DMP/WebAccess-DMP.md) § 5.3):

* Define explicitly **which tags** are published, to which destination, and under what data-classification decision — do not let the tag list grow ad hoc.
* Use TLS for MQTT/REST connections; authenticate the broker/endpoint; avoid anonymous MQTT publish.
* Route the path through the **IDMZ**, not a direct OT-to-cloud connection ([Network-Segmentation.md](../../../04-Network/Network-Segmentation.md)).
* Treat ThingWorx (or any IIoT platform) credentials as privileged integration credentials under the same governance as any other service account (3.4).

## 4.5 Time Synchronization

NTP on the Kepware host for reliable event correlation with SCADA, historian and SIEM timestamps ([Logging.md](../../../09-Operations/Logging.md)).

---

# 5 — Hardening

Phased; each phase ends with validation and a baseline capture ([Configuration-Management.md](../../../09-Operations/Configuration-Management.md)).

**Phase 1 — Platform baseline.** Hardened Windows host ([Windows-Hardening.md](../../../09-Operations/Windows-Hardening.md)); minimal roles; allowlisting; patched per vendor cadence.

**Phase 2 — Driver minimisation.** Install and enable only the driver plugins actually required by connected devices; remove/disable the rest; document the enabled-driver list as part of the asset record ([Asset-Management.md](../../../09-Operations/Asset-Management.md)).

**Phase 3 — Identity configuration.** Implement User Manager roles per section 3.1; separate administrative from runtime access; remove default/shared accounts; password-protect the project.

**Phase 4 — OPC UA hardening.** Enforce certificate-based mutual authentication, Sign/SignAndEncrypt, and a managed trust list on the OPC UA server interface.

**Phase 5 — OPC DA containment.** Confirm OPC DA/DCOM is used only within the local zone and never bridged across a firewall boundary; correctly scope DCOM permissions for any remaining local clients; document a migration plan off OPC DA.

**Phase 6 — IoT Gateway governance.** Apply section 4.4 in full: explicit tag scoping, TLS, IDMZ routing, credential governance.

**Phase 7 — Network hardening.** Place the server at the documented L2/L3 boundary; default-deny boundary firewall; permit only the specific southbound device flows and northbound client flows required ([Firewall-Design.md](../../../04-Network/Firewall-Design.md)).

**Phase 8 — Validation.** Test that client accounts see only their intended tag scope; confirm OPC UA rejects untrusted certificates; confirm no DCOM traffic crosses the firewall boundary; confirm IoT Gateway publishes only the intended tag list; capture the baseline.

---

# 6 — Monitoring and Logging

Forward to the SIEM ([SIEM.md](../../../09-Operations/SIEM.md)) and treat as high-value detections ([Monitoring.md](../../../09-Operations/Monitoring.md)):

* **OPC client connections and session activity** — new/unexpected client sources, especially any DCOM connection attempt from beyond the local zone.
* **Tag writes**, correlated with the client identity that issued them — the platform's command-audit equivalent, since a write here can reach any connected controller.
* **User Manager and project configuration changes** — new users, permission changes, driver additions.
* **Driver/plugin errors and southbound connection failures** — availability signal, and occasionally an early indicator of protocol-level probing.
* **IoT Gateway publish volume and configuration changes** — unexpected growth in published tags or a new destination is a data-egress signal worth investigating immediately.
* Windows security logs of the host itself.

---

# 7 — Backup and Recovery

* **Back up** the Kepware project (channels/devices/tags/security configuration), the enabled-driver/version inventory, OPC UA certificates and trust list, User Manager configuration, and IoT Gateway publish configuration — stored offline and version-controlled ([Backup.md](../../../09-Operations/Backup.md)).
* **Test restoration** onto representative infrastructure, confirming southbound reconnection to sample devices and that User Manager/OPC UA security configuration is restored, not silently reset to defaults ([Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md)).
* Track **driver plugin versions** explicitly in the backup/inventory record; a restored server running different driver versions than production may behave differently against specific device firmware — validate before declaring recovery complete.

---

# 8 — Best Practices and Common Mistakes

**Practices that pay off:** only required drivers enabled; OPC DA/DCOM never crossing a firewall (terminated on-server per the ISA-95 pattern); OPC UA with certificates and Sign/SignAndEncrypt as the default northbound interface; User Manager roles scoped per client function; IoT Gateway tag lists explicitly reviewed; project file protected and backed up; tag writes and new client connections monitored.

**Common mistakes:**

* DCOM security "temporarily" disabled during commissioning and never re-enabled.
* OPC DA bridged directly through a firewall with dynamic DCOM ports opened wide.
* Every SCADA/MES/integration client sharing one broad, unscoped OPC account.
* Every available driver plugin installed "in case it's needed," expanding attack surface for no operational benefit.
* IoT Gateway tag list grown ad hoc with no review of what now leaves the OT estate.
* Project file unprotected and un-backed-up — the estate's topology map, freely readable.
* No monitoring of tag writes, so a malicious or erroneous write reaching a controller through Kepware would go unnoticed.
* Treating Kepware as "just middleware" and omitting it from the asset inventory, patch cycle and monitoring scope applied to controllers and SCADA.

---

# Architect Notes

Kepware's risk profile is defined by one architectural fact: it is built to sit **between** everything, in **both directions**, across **many protocols at once**. That is also exactly its value. The security work is therefore not about picking one dominant control but about applying the same discipline — least privilege, explicit conduit design, monitored writes — consistently across every driver and every northbound interface, because a gap in any single one reopens the whole aggregation point. Treat it with the seriousness of a SCADA server, not the casualness of "just a data connector," and the DCOM legacy problem in particular deserves a firm, permanent architectural answer (terminate on-server, migrate to OPC UA) rather than a series of one-off exceptions.

---

# Decision Support

1. Which driver plugins are actually enabled, and does each correspond to a real, inventoried connected device?
2. Is OPC DA/DCOM traffic ever crossing a firewall boundary — and if legacy OPC DA remains in use, is it correctly terminated on the Kepware server itself?
3. Is OPC UA the default northbound interface, with certificate-based mutual authentication and Sign/SignAndEncrypt enforced?
4. Are OPC/IoT Gateway client accounts individually scoped to the minimum tag namespace they require, with no shared credentials?
5. Is the IoT Gateway tag list explicitly reviewed, and does the cloud/enterprise path route through the IDMZ?
6. Is the project file protected, version-controlled and backed up?
7. Are tag writes and new client connections monitored and forwarded to the SIEM?
8. Has full restoration — including User Manager, OPC UA security configuration and southbound reconnection — been demonstrated, not just service restart?

---

# AI Guidance

When discussing PTC Kepware Server / KEPServerEX:

* Frame it as a bidirectional OPC aggregation server/conduit-termination point, not a passive data connector — its compromise gives read/write reach across every connected protocol and device, so treat it with SCADA-server-level rigor.
* Anchor OPC DA guidance in the established handbook rule (from ISA95.md): terminate OPC DA/DCOM on the Kepware server itself, never bridge it across a firewall boundary; recommend active migration to OPC UA.
* Recommend least-privilege, per-client User Manager scoping and certificate-based OPC UA security as defaults.
* Treat the IoT Gateway (MQTT/REST/ThingWorx) as a governed cloud/enterprise data-egress conduit requiring explicit tag scoping and IDMZ routing, comparable to PI System's cloud connectors.
* Recommend minimising enabled driver plugins to reduce attack surface, and tracking PTC PSIRT advisories per-driver, not just per-product-version.
* Delegate southbound native-protocol depth to OT-Protocols.md, IDMZ/segmentation detail to Network-Segmentation.md and ISA95.md, and identity mechanics to the 05-Identity layer; verify release-specific capabilities and PTC PSIRT advisories against current vendor sources rather than asserting them.

---

# Sources / Grounding

* **PTC Kepware / KEPServerEX documentation** — driver plugins, User Manager, OPC UA configuration, IoT Gateway (authoritative for the deployed release).
* **PTC PSIRT security advisories** — Kepware/KEPServerEX and driver-plugin vulnerability history and current guidance.
* **OPC Foundation OPC UA specifications** — security model referenced in section 4.3; see [OT-Protocols.md](../../../04-Network/OT-Protocols.md).
* **ISA/IEC 62443** — the OPC-aggregation-server conduit-termination pattern this document implements; also independently documented in [ISA95.md](../../../02-Standards/ISA95.md). See [IEC62443.md](../../../02-Standards/IEC62443.md).
* **NIST SP 800-82 Rev. 3** — OT security-architecture context.

> Foundational references; the vendor documentation of the deployed release is authoritative for product specifics.

---

# Related Documents

* [dataFEED-OPC-Suite.md](../../Softing/dataFEED%20OPC%20Suite/dataFEED-OPC-Suite.md)
* [PI-System.md](../../AVEVA/PI%20System/PI-System.md)
* [PlantSCADA.md](../../AVEVA/PlantSCADA/PlantSCADA.md)
* [ISA95.md](../../../02-Standards/ISA95.md)
* [Purdue-Model.md](../../../02-Standards/Purdue-Model.md)
* [IEC62443.md](../../../02-Standards/IEC62443.md)
* [OT-Protocols.md](../../../04-Network/OT-Protocols.md)
* [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md)
* [Firewall-Design.md](../../../04-Network/Firewall-Design.md)
* [Risk-Based-Security.md](../../../03-Architecture/Risk-Based-Security.md)
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
| 1.0.0 | 2026-07-07 | Initial release — new 08-Technologies platform reference for PTC Kepware Server (KEPServerEX), implementing in detail the "OPC aggregation server" conduit-termination pattern already named in ISA95.md: plugin-driver architecture and attack-surface implications, the bidirectional-conduit threat model (read AND write reach across every connected protocol), the OPC DA/DCOM legacy containment rule, OPC UA certificate-based hardening, IoT Gateway/ThingWorx cloud-egress governance, User Manager identity scoping, phased hardening, monitoring and recovery; first PTC vendor branch and first pure OPC-aggregation-class document in 08-Technologies |
| 1.0.1 | 2026-07-07 | Resolved Windows-Hardening.md and added OPC-UA.md cross-link now that both documents exist |
