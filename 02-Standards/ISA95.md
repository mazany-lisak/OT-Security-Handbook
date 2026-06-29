---
title: ISA-95 — Enterprise-Control System Integration Standard
category: Standards
version: 1.1.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-06-29
review_cycle: Annual
---

# Purpose

This document provides an engineering-oriented overview of the ISA-95 standard (IEC 62264) from the perspective of an OT Security Architect.

ISA-95 is **not a cybersecurity standard**. It is a functional reference model that defines responsibilities, information flows and integration architecture between manufacturing operations and enterprise business systems.

Understanding ISA-95 is a prerequisite for correct security architecture: you cannot define security zones, conduits or access controls without first understanding what systems exist, what they do, and what information flows between them.

---

# Related Documents

| Document | Relationship |
|----------|-------------|
| [NIS2.md](#) | Regulatory framework — ISA-95 helps identify which systems fall in scope |
| [Czech-Cybersecurity-Act.md](#) | National obligations — ISA-95 informs asset inventory and scope determination |
| [IEC62443-Overview.md](#) | Security framework — IEC 62443 secures the systems and flows ISA-95 defines |

---

# Position in the Architecture Stack

```
[NIS2 / Czech Cybersecurity Act]       ← What must be protected (regulatory)
        │
[IEC 62443]                            ← How to protect it (security architecture)
        │
[ISA-95 / Purdue Model]                ← What exists and what it does (functional model)
```

ISA-95 answers: *"Which system owns this function? Which information flows where?"*
IEC 62443 answers: *"How do we secure those systems and flows?"*

Always understand the functional architecture before designing the security architecture.

---

# What Is ISA-95?

ISA-95 (formally ANSI/ISA-95, internationally adopted as IEC 62264) is an international standard for the integration of enterprise and control systems.

Its primary objective is to **standardize communication and information exchange** between business systems and manufacturing operations, providing a common language for engineers, automation specialists, software developers and business stakeholders.

ISA-95 was developed by ISA (International Society of Automation) and is maintained in alignment with IEC 62264.

---

# Why ISA-95 Matters for Security Architects

Modern industrial environments depend on information flowing between production systems and enterprise applications. Without a common functional reference model:

* Integration boundaries are undefined — security zones cannot be correctly designed.
* Information ownership is unclear — access controls cannot be correctly assigned.
* Unnecessary integrations accumulate — attack surface grows without justification.
* Architectural decisions are made without understanding operational context.

**The security implication:** Every unnecessary integration is a potential attack vector. ISA-95 helps identify which integrations are functionally justified and which can be eliminated.

---

# Functional Levels (ISA-95 Hierarchy)

ISA-95 defines five functional levels. These align closely with the Purdue Model but emphasize **business responsibilities** rather than network trust boundaries.

```
┌─────────────────────────────────────────────────────────┐
│  Level 4 — Business Planning and Logistics              │
│  ERP, SCM, Finance, Customer Orders                     │
├─────────────────────────────────────────────────────────┤
│  Level 3 — Manufacturing Operations Management          │
│  MES, Quality, Maintenance, Production Scheduling,      │
│  Historians, Manufacturing Analytics                    │
├─────────────────────────────────────────────────────────┤
│  Level 2 — Supervisory / Monitoring Control             │
│  SCADA, DCS supervisory, HMI servers, Alarm Management  │
├─────────────────────────────────────────────────────────┤
│  Level 1 — Basic / Regulatory Control                   │
│  PLC, RTU, DCS controllers, Drive systems               │
├─────────────────────────────────────────────────────────┤
│  Level 0 — Physical Process                             │
│  Sensors, Actuators, Field instruments                  │
└─────────────────────────────────────────────────────────┘
```

> **Key distinction from Purdue:** ISA-95 levels define *who is responsible for which business function*. Purdue levels define *network trust boundaries*. Both models are used together in architecture design.

---

# Level 4 — Business Planning and Logistics

**Typical systems:**
* ERP (Enterprise Resource Planning) — SAP, Oracle, Microsoft Dynamics.
* Supply Chain Management (SCM).
* Financial and accounting systems.
* Customer order management.
* HR systems.

**Responsibilities:**
* Business planning and resource allocation.
* Financial reporting.
* Customer order processing.
* Supply chain coordination.

**Security context:**
Level 4 systems reside in the enterprise IT domain. Their connectivity to Level 3 must be controlled via the Industrial DMZ. They must not have direct access to Level 2 or below. Compromise of Level 4 systems (e.g., via ransomware) must not propagate into the OT network.

---

# Level 3 — Manufacturing Operations Management

**Typical systems:**
* MES (Manufacturing Execution System).
* Quality Management System (QMS).
* Laboratory Information Management System (LIMS).
* Computerized Maintenance Management System (CMMS).
* Production scheduling.
* Process historians (OSIsoft PI / AVEVA PI, Aspentech, Honeywell Uniformance).
* Manufacturing analytics and KPI dashboards.
* Batch management systems.

**Responsibilities:**
* Production execution and tracking.
* Quality assurance and reporting.
* Maintenance planning and work order management.
* Performance analysis (OEE, yield, downtime).
* Production data collection and aggregation.

**Security context:**
Level 3 is the most complex boundary in OT security. Historians typically have connections both upward (to Level 4 reporting) and downward (collecting from Level 2). MES systems send production orders downward and receive completion confirmations upward. This bi-directional information flow requires careful conduit design.

The historian is frequently the most exposed OT asset — it aggregates data from the entire control network and must communicate with enterprise IT. A common pattern is a **read-only historian replica** in the Industrial DMZ — the enterprise network reads from the replica, never from the production historian.

---

# Level 2 — Supervisory and Monitoring Control

**Typical systems:**
* SCADA servers and clients (Wonderware/AVEVA, Ignition, iFIX, WinCC, ClearSCADA/GeoSCADA).
* DCS supervisory workstations (ABB 800xA, Honeywell Experion, Emerson DeltaV, Yokogawa CENTUM).
* HMI servers and clients.
* Alarm management systems.
* Engineering workstations.

**Responsibilities:**
* Supervisory control of industrial processes.
* Operator interface and alarm management.
* Process visualization.
* PLC/DCS engineering and configuration.

**Security context:**
Level 2 is the boundary between operator-facing systems and control logic. Engineering workstations at this level have write access to PLC and DCS controllers — they are high-value targets. Unauthorized access to an engineering workstation is equivalent to control over the physical process.

Security requirements: strict access control, PAM, audit logging, change management for all configuration modifications.

---

# Level 1 — Basic Control

**Typical systems:**
* PLC (Programmable Logic Controllers) — Siemens S7, Rockwell ControlLogix, Schneider Modicon, ABB AC500, Beckhoff.
* RTU (Remote Terminal Units) — for geographically distributed systems (pipelines, substations).
* DCS controllers (field controllers, I/O modules).
* Variable frequency drives (VFDs) with network interfaces.
* Safety PLC / Safety Controllers (within SIS).

**Responsibilities:**
* Execution of control logic (ladder, FBD, SFC, ST programs).
* Collection of process measurements.
* Control of actuators (valves, motors, pumps, compressors).
* Interlocking and sequencing.

**Security context:**
Level 1 devices (PLCs, RTUs) typically have limited native security capabilities. Many run proprietary real-time operating systems, do not support authentication, and cannot be patched without process interruption. Security is primarily achieved through network isolation (zone design), strict conduit control, and monitoring of communication anomalies.

Unauthorized modifications to PLC programs can directly cause physical process incidents.

---

# Level 0 — Physical Process

**Typical components:**
* Sensors (temperature, pressure, flow, level, analytical).
* Actuators (control valves, on/off valves, dampers).
* Motors and drives.
* Field instruments (transmitters, analyzers).

**Responsibilities:**
* Physical measurement and actuation.
* Process interaction.

**Security context:**
Level 0 devices generally have no cybersecurity capabilities. Physical security (access control to field locations, tamper protection) is the primary control. Monitoring at the Level 1/2 boundary can detect anomalous actuator behavior that may indicate a compromised controller.

---

# Information Flows

ISA-95 defines the logical information exchange between levels. Understanding these flows is essential for correct conduit design.

## Downward Flows (Enterprise → Manufacturing)

| Information | From | To |
|-------------|------|----|
| Production orders | Level 4 (ERP) | Level 3 (MES) |
| Work schedules | Level 4 | Level 3 |
| Recipes / formulations | Level 3/4 | Level 2 (SCADA/DCS) |
| Maintenance work orders | Level 3 (CMMS) | Level 2 |
| Set-point updates | Level 2 | Level 1 (PLC) |
| Control commands | Level 2 | Level 1 |

## Upward Flows (Manufacturing → Enterprise)

| Information | From | To |
|-------------|------|----|
| Production actuals | Level 3 (MES/Historian) | Level 4 (ERP) |
| Equipment status | Level 1/2 | Level 3 (CMMS) |
| Quality data | Level 1/2/3 | Level 3 (QMS) / Level 4 |
| Inventory updates | Level 3 | Level 4 |
| OEE / performance KPIs | Level 3 (Historian) | Level 4 |
| Alarm and event data | Level 1/2 | Level 3 / Level 4 |

**Security implication:** Every information flow is a potential attack vector if not implemented through a properly defined and monitored conduit. Flows should be:

* Unidirectional where possible (data diode from OT to IT).
* Protocol-specific (OPC UA with security profiles, not raw TCP).
* Authenticated and authorized.
* Monitored for anomalies.

---

# ISA-95 and Industrial Protocols

ISA-95 informs which protocols are appropriate at each level boundary.

| Boundary | Common Protocols | Security Consideration |
|----------|-----------------|----------------------|
| L4 ↔ L3 | OPC UA, REST API, database replication | Must pass through Industrial DMZ |
| L3 ↔ L2 | OPC UA, OPC DA (legacy), SQL | OPC UA preferred; DA is unencrypted |
| L2 ↔ L1 | Modbus TCP, EtherNet/IP, PROFINET, IEC 61850, DNP3 | Minimal native security; rely on zone isolation |
| L1 ↔ L0 | 4–20 mA (analog), HART, Foundation Fieldbus, PROFIBUS | Mostly physical; monitor at L1/L2 level |

---

# Relationship with the Purdue Model

ISA-95 and the Purdue Model (Reference Model for Plant Hierarchical Control) are complementary.

| Dimension | Purdue Model | ISA-95 |
|-----------|-------------|--------|
| Focus | Network trust levels and segmentation | Business functions and information ownership |
| Primary use | Defining security boundaries | Defining functional responsibilities |
| Level labels | L0–L5 | L0–L4 |
| Introduced by | Theodore Williams (1989, Purdue University) | ISA-95 committee (2000s) |
| Used in | IEC 62443, network design | MES/ERP integration, manufacturing architecture |

Both are used together in practice: ISA-95 defines *what* the architecture contains; Purdue defines *where the trust boundaries are*; IEC 62443 defines *how to secure it*.

---

# Relationship with IEC 62443

ISA-95 and IEC 62443 address complementary architectural concerns.

| ISA-95 Defines | IEC 62443 Secures |
|---------------|------------------|
| Which systems exist at each level | Zones grouping those systems |
| What information flows between levels | Conduits controlling those flows |
| Who is responsible for which function | Security Level requirements for each zone |
| Which integrations are functionally necessary | Which integrations require specific security controls |

**Practical integration:**

1. Use ISA-95 to understand the functional architecture and information flows.
2. Use the Purdue Model to define initial zone and trust boundary candidates.
3. Use IEC 62443-3-2 to assess risk and refine zone/conduit design.
4. Use IEC 62443-3-3 to define security requirements for each zone.

---

# OT Asset Inventory from ISA-95 Perspective

A complete OT asset inventory structured by ISA-95 levels:

| Level | Asset Type | Examples |
|-------|-----------|---------|
| 4 | Enterprise IT | ERP servers, business workstations |
| 3 | Manufacturing IT | MES servers, historians, CMMS, QMS |
| 3.5 | Industrial DMZ | Historian replica, jump servers, file transfer servers, DMZ firewalls |
| 2 | SCADA / DCS | SCADA servers, HMI servers, DCS workstations, alarm servers |
| 2 | Engineering | Engineering workstations, PLC programming laptops |
| 1 | Controllers | PLC, RTU, DCS field controllers, safety PLC |
| 0 | Field | Sensors, actuators, drives, field instruments |

This structure should align with the asset inventory required by [Czech-Cybersecurity-Act.md](#) (Decree 409/410) and the Zone definition process in [IEC62443-Overview.md](#).

---

# Common Misconceptions

ISA-95 is **not**:

* A cybersecurity standard.
* A network segmentation guide.
* A firewall design methodology.
* A PLC programming standard.
* A replacement for IEC 62443.
* Identical to the Purdue Model (though closely related).

Its focus is operational integration, manufacturing information ownership and functional boundary definition.

---

# Architect Notes

Experienced architects use ISA-95 **before** discussing cybersecurity controls.

Questions to answer using ISA-95 before designing security:

* Which system owns production scheduling — ERP or MES?
* Where should recipe management reside?
* Which systems require read access to historian data — and from which network?
* Which applications absolutely require access to Level 1/2, and which only require aggregated data from Level 3?
* Which current integrations are functionally necessary and which have accumulated without design intent?

**Reducing unnecessary integrations improves both maintainability and cybersecurity.** An integration that does not exist cannot be exploited.

---

# AI Guidance

When answering ISA-95-related questions:

* Explain functional responsibilities and information ownership before discussing security.
* Use ISA-95 levels to contextualize where systems and data reside.
* Distinguish ISA-95 (functional) from IEC 62443 (security) and Purdue (network trust).
* Recommend using ISA-95 as input to IEC 62443 zone and conduit design.
* Use ISA-95 to identify unnecessary integrations that increase attack surface.
* Always integrate with [IEC62443-Overview.md](#) when security architecture questions arise.
* Do not describe ISA-95 as a security standard.
* Consider both operational efficiency and cybersecurity implications of integration decisions.

---

# Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | 2026-06-28 | Initial release |
| 1.1.0 | 2026-06-29 | Added security context per level, information flow tables, protocol table, asset inventory table, Purdue comparison, functional/security integration guidance, cross-links to all related documents |
