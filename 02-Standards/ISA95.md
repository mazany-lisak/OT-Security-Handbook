---
title: ISA-95 — Enterprise-Control System Integration Standard
category: Standards
version: 1.3.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-07-01
review_cycle: Annual
---

# Purpose

This document provides an engineering-oriented overview of the ISA-95 standard (IEC 62264) from the perspective of an OT Security Architect.

ISA-95 is **not a cybersecurity standard**. It is a functional reference model that defines responsibilities, information flows and integration architecture between manufacturing operations and enterprise business systems.

Understanding ISA-95 is a prerequisite for correct security architecture. Security zones cannot be correctly designed, conduits cannot be correctly defined, and access controls cannot be correctly assigned without first understanding what systems exist at each functional level, what they do, and what information flows between them.

This document covers:

* The ISA-95 functional hierarchy (Levels 0–4).
* Detailed system inventory per level.
* Information flows, ownership and criticality.
* Industrial protocols at each level boundary.
* Integration architecture patterns and their security implications.
* Relationship with the Purdue Model and IEC 62443.
* Sector-specific ISA-95 architecture variants.
* OT asset inventory structure.

---

# Related Documents

| Document | Relationship |
|----------|-------------|
| [NIS2.md](NIS2.md) | Regulatory framework — ISA-95 helps identify which systems fall within regulatory scope |
| [Czech-Cybersecurity-Act.md](Czech-Cybersecurity-Act.md) | National obligations — ISA-95 informs asset inventory and regulatory scope determination |
| [IEC62443.md](IEC62443.md) | Security framework — IEC 62443 secures the systems and flows that ISA-95 defines |
| [Purdue-Model.md](Purdue-Model.md) | Network-trust reference architecture (PERA) — the same level numbering seen from a *network trust boundary* perspective (L0–L5 + L3.5), the complement to this functional view |

---

# Position in the Architecture Stack

```
[NIS2 / Czech Cybersecurity Act]       ← What must be protected (regulatory)
        │
[IEC 62443]                            ← How to protect it (security architecture)
        │
[ISA-95 / Purdue Model]                ← What exists and what it does (functional model)
```

ISA-95 answers: *"Which system owns this function? Which information flows where? Who is responsible?"*
IEC 62443 answers: *"How do we secure those systems and those flows?"*

**The fundamental rule:** Always understand the functional architecture before designing the security architecture. A security architect who does not understand ISA-95 functional boundaries will produce a security design that either over-restricts legitimate operational flows or fails to control genuinely dangerous integration paths.

---

# What Is ISA-95?

ISA-95 (formally ANSI/ISA-95, internationally adopted as IEC 62264) is an international standard for the integration of enterprise and control systems, developed by ISA (International Society of Automation).

Its primary objective is to **standardize communication and information exchange** between business systems and manufacturing operations, providing a common language for engineers, automation specialists, software developers and business stakeholders across organizational and vendor boundaries.

## ISA-95 Standard Parts

| Part | Title | Content |
|------|-------|---------|
| ISA-95.00.01 (IEC 62264-1) | Models and Terminology | Functional hierarchy, object models, terminology |
| ISA-95.00.02 (IEC 62264-2) | Object Model Attributes | Detailed attributes for manufacturing objects |
| ISA-95.00.03 (IEC 62264-3) | Activity Models of Manufacturing Operations Management | Manufacturing operations workflows |
| ISA-95.00.04 (IEC 62264-4) | Objects and Attributes for Manufacturing Operations | Manufacturing operations data models |
| ISA-95.00.05 (IEC 62264-5) | Business to Manufacturing Transactions | Transaction models for Level 3/4 integration |
| ISA-95.00.06 (IEC 62264-6) | Messaging Service Model | Messaging standards for system integration |

For security architects, **Part 1** (functional hierarchy and terminology) and **Part 3** (activity models) are the primary references.

---

# Why ISA-95 Matters for Security Architects

## The Core Problem It Solves

Modern industrial environments depend on information flowing between production systems and enterprise applications. Without a structured functional reference model:

* Integration boundaries are undefined — security zones cannot be correctly designed because it is unclear which systems logically belong together.
* Information ownership is unclear — access controls cannot be correctly assigned because it is unclear which system is authoritative for which data.
* Unnecessary integrations accumulate over years of system evolution — the attack surface grows without anyone making a deliberate decision to accept that risk.
* Architectural decisions are made without understanding operational context — leading to security controls that impair legitimate operations or fail to protect against real threats.

## The Security Implication

Every unnecessary integration is a potential attack vector.

In practice, OT environments typically accumulate integrations over decades: a historian that was given read access to a PLC "just to display data" now has a writable OPC connection because someone needed to write timestamps; a maintenance system that only needed production counts now has direct SQL access to the SCADA database; a vendor remote access path that was opened for a commissioning project five years ago was never closed.

ISA-95 provides the analytical framework to answer: *"Which integrations are functionally justified, and which can be eliminated or constrained?"*

An integration that does not exist cannot be exploited.

---

# Functional Levels (ISA-95 Hierarchy)

ISA-95 defines five functional levels. These align closely with the Purdue Model (PERA) but emphasize **business responsibilities and information ownership** rather than network trust boundaries.

```
╔══════════════════════════════════════════════════════════════════════╗
║  LEVEL 4 — Business Planning and Logistics                          ║
║  Business horizon: weeks to months                                  ║
║  ERP, SCM, Finance, HR, Customer Orders                             ║
╠══════════════════════════════════════════════════════════════════════╣
║  ── L3/L4 BOUNDARY ── (Industrial DMZ / B2M interface) ──          ║
╠══════════════════════════════════════════════════════════════════════╣
║  LEVEL 3 — Manufacturing Operations Management (MOM)                ║
║  Production horizon: shifts to days                                 ║
║  MES, Quality, Maintenance, Historians, Batch, Analytics            ║
╠══════════════════════════════════════════════════════════════════════╣
║  ── L2/L3 BOUNDARY ── (Control network / MOM interface) ──         ║
╠══════════════════════════════════════════════════════════════════════╣
║  LEVEL 2 — Supervisory / Monitoring Control                         ║
║  Production horizon: hours to shifts                                ║
║  SCADA, DCS supervisory, HMI servers, Alarm Management              ║
╠══════════════════════════════════════════════════════════════════════╣
║  ── L1/L2 BOUNDARY ── (Control loop / supervisory interface) ──    ║
╠══════════════════════════════════════════════════════════════════════╣
║  LEVEL 1 — Basic / Regulatory Control                               ║
║  Production horizon: seconds to minutes                             ║
║  PLC, RTU, DCS controllers, Drive systems                           ║
╠══════════════════════════════════════════════════════════════════════╣
║  ── L0/L1 BOUNDARY ── (Physical / digital interface) ──            ║
╠══════════════════════════════════════════════════════════════════════╣
║  LEVEL 0 — Physical Process                                         ║
║  Sensors, Actuators, Field instruments                              ║
╚══════════════════════════════════════════════════════════════════════╝
```

> **Key distinction from Purdue:** ISA-95 levels define *who is responsible for which business function and over what time horizon*. Purdue levels define *network trust boundaries and communication paths*. Both models are used together in architecture design — they are complementary, not competing.

---

# Level 4 — Business Planning and Logistics

## Time Horizon

Days to months — strategic and tactical business planning.

## Typical Systems

| System Category | Examples | Primary Business Function |
|----------------|---------|--------------------------|
| ERP | SAP S/4HANA, Oracle ERP Cloud, Microsoft Dynamics 365 | Production planning, financials, procurement |
| Supply Chain Management | SAP SCM, Oracle SCM Cloud | Supplier management, inventory, logistics |
| Customer Order Management | CRM, order management systems | Customer orders, delivery scheduling |
| Financial systems | SAP FI, Oracle Financials | Accounting, cost tracking, reporting |
| HR systems | SAP HCM, Workday | Workforce management, payroll |
| Asset Management (Enterprise) | IBM Maximo (enterprise tier), SAP PM | Strategic asset lifecycle management |
| Business Intelligence / Reporting | SAP BW, Power BI (enterprise), Tableau | KPIs, management reporting, dashboards |

## Business Responsibilities at Level 4

* Production planning — translating customer orders and market demand into manufacturing schedules.
* Resource allocation — materials, machinery, labor.
* Financial reporting and cost tracking.
* Supply chain coordination and procurement.
* Strategic asset investment decisions.

## Security Context for Level 4

Level 4 systems reside entirely in the enterprise IT domain and are subject to IT security controls (ISO 27001, NIST CSF, or equivalent).

**Critical boundary rules:**
* Level 4 systems must communicate with Level 3 only through the Industrial DMZ — never directly.
* Level 4 systems must never have direct network access to Level 2 or below.
* Compromise of Level 4 (e.g., via ransomware, phishing, supply chain attack) must not propagate into the OT network.

**Real-world risk:** The 2017 Merck NotPetya ransomware incident and the 2020 Norsk Hydro ransomware attack both propagated from corporate IT into OT systems because the L4/L3 boundary was inadequately controlled. The L4/L3 boundary is one of the highest-value architectural controls in modern industrial cybersecurity.

**Information ownership at Level 4:**
Level 4 is the authoritative owner of:
* Customer orders and delivery commitments.
* Production master schedules.
* Bill of materials (BOM).
* Cost center structures and financial KPIs.
* Supplier contracts and procurement data.

Level 4 consumes from Level 3:
* Production actuals (quantities, yields, cycle times).
* Quality results.
* Inventory counts.
* Equipment utilization data (for depreciation and investment planning).

---

# Level 3 — Manufacturing Operations Management (MOM)

## Time Horizon

Shifts to days — operational production management.

Level 3 is the bridge between enterprise business planning (Level 4) and real-time process control (Level 2). It is architecturally the most complex and security-sensitive boundary in most OT environments.

## Typical Systems

| System | Examples | Primary Function |
|--------|---------|-----------------|
| MES (Manufacturing Execution System) | Siemens Opcenter, Rockwell FactoryTalk ProductionCentre, GE Proficy, Aveva MES, Werum PAS-X (pharma) | Production execution, tracking, dispatching |
| Process Historian | AVEVA PI System (formerly OSIsoft PI), Honeywell Uniformance, AspenTech IP.21, Yokogawa Exaquantum | Time-series process data collection and storage |
| Quality Management (QMS) | SAP QM, Siemens Opcenter Quality, dedicated QMS platforms | Quality control, SPC, non-conformance management |
| Laboratory Information Management (LIMS) | LabWare, LabVantage, STARLIMS | Laboratory data management, sample tracking |
| Computerized Maintenance Management (CMMS) | IBM Maximo (plant tier), SAP PM (plant tier), Infor EAM | Maintenance planning, work orders, spare parts |
| Production Scheduling | Preactor (Siemens), Asprova, Infor Advanced Scheduling | Detailed production scheduling, constraint-based planning |
| Batch Management | Emerson DeltaV Batch, ABB Symphony Batch, Siemens SIMATIC Batch | Batch recipe execution, batch records (pharma/food/chemical) |
| Manufacturing Analytics / MIS | Custom BI on historian data, Seeq, AspenTech Mtell | OEE calculation, yield analysis, predictive maintenance |
| Energy Management (plant level) | Siemens EnergyManager, ABB ability Energy Manager | Plant energy monitoring and optimization |

## Responsibilities at Level 3

* Production execution tracking — dispatching work orders to Level 2, confirming completions.
* Quality assurance — in-process quality checks, laboratory results, SPC.
* Maintenance management — work order scheduling, planned maintenance, breakdown management.
* Production performance analysis — OEE, yield, downtime analysis, shift reports.
* Batch record management (regulated industries) — complete manufacturing records per batch.
* Process data collection — historian collects time-series data from Level 2 systems.
* Genealogy and traceability — tracking materials and operations per product.

## Security Context for Level 3

**The historian: highest-exposure asset at Level 3**

The process historian is frequently the most security-sensitive asset at Level 3 because:
* It has read connections downward into Level 2/1 (collecting process data from SCADA, DCS, PLCs).
* It has read connections upward toward Level 4 (providing reporting data to ERP, BI tools).
* It is accessible to many users across multiple zones (operators, engineers, management, IT analysts).
* It stores years of detailed process data, including production rates, recipes, setpoints — valuable IP.

**Historian architecture pattern — secure design:**

```
[Level 4 — Enterprise IT]
    ERP, BI tools, Management dashboards
        │  Read only, via Industrial DMZ
[Industrial DMZ]
    Historian REPLICA (read-only copy, e.g., PI AF replica)
    ← Unidirectional replication from production historian
        │  No write-back path
[Level 3 — MOM zone]
    Production HISTORIAN (primary, PI Server / Uniformance)
    ← Collects via OPC UA / OPC DA from Level 2
        │  Read-only data collection from SCADA/DCS
[Level 2 — Control zone]
    SCADA / DCS / OPC server
```

The enterprise network reads from the replica — never from the production historian. The production historian has no writable connection toward Level 4. This eliminates the historian as a lateral movement path from IT into OT.

**MES: bi-directional integration challenge**

MES systems are architecturally challenging because they require genuine bi-directional communication:
* Downward: dispatching work orders, sending recipes, downloading production parameters.
* Upward: reporting production actuals, quality results, inventory confirmations.

Security design for MES integration:
* Place MES in a dedicated MOM zone, not in the control zone.
* Use OPC UA with Sign and Encrypt security profile for L2/L3 integration.
* Apply allowlist conduit rules: only defined MES-to-SCADA message types are permitted.
* Log all MES-originated write operations to Level 2 systems.
* Implement role-based access for MES — production planner does not need SCADA write access.

**CMMS remote access pattern:**

CMMS systems frequently initiate remote access to field-level equipment (VFDs, smart instruments) for condition monitoring. This creates a Level 3→Level 1 path that bypasses Level 2. Security control: require all CMMS connections to Level 1 to go through the standard conduit architecture; do not permit direct CMMS-to-field connections that circumvent zone boundaries.

---

# Level 3.5 — Industrial DMZ

The Industrial DMZ (sometimes called Level 3.5) is not defined in ISA-95 itself, but is an architectural construct that has become essential in modern OT security design.

## Purpose

The Industrial DMZ serves as the only controlled pathway between the enterprise IT network (Level 4) and the OT network (Level 3 and below). It is not a transit zone — it is a termination zone. Connections from Level 4 terminate in the DMZ; connections from Level 3 terminate in the DMZ. No connection passes through the DMZ from end to end.

## Typical Systems in the Industrial DMZ

| System | Function | Security Role |
|--------|---------|--------------|
| Historian replica (read-only) | Read-only copy of production historian for IT consumers | Eliminates direct IT access to production historian |
| Jump server / Bastion host | Single controlled entry point for remote access to OT | All remote sessions terminate here; full session recording |
| File transfer server | Controlled file exchange between IT and OT | Anti-malware scanning; allowlist of permitted file types |
| Patch staging server | Validated patches queued for OT deployment | Staging and validation before OT distribution |
| AV/EDR update mirror | Security signature updates for OT endpoint tools | Eliminates direct internet access from OT zone |
| Remote access gateway (MFA) | MFA authentication for VPN/remote access | First authentication layer before jump server |
| OPC UA proxy / aggregator | Protocol-aware mediation between IT and OT OPC connections | Terminates OPC connections; prevents direct OPC DA bridging |
| DMZ firewalls (inner and outer) | Zone boundary enforcement | Two independent firewalls from different vendors recommended |
| Log relay / SIEM forwarder | Forwards OT logs to enterprise SIEM without direct connection | Decouples SIEM from OT network |

## Industrial DMZ Design Rules

* The DMZ must have two independent firewall boundaries — one toward IT, one toward OT.
* No traffic passes through the DMZ directly — all traffic terminates and is re-initiated.
* The DMZ itself is a zone with its own Security Level Target (typically SL 2).
* All DMZ systems are maintained and patched as a priority — they are the most exposed OT-adjacent systems.
* Compromise of a DMZ system should not automatically grant access to Level 3 — DMZ systems must have minimal and controlled access to the OT network.

---

# Level 2 — Supervisory and Monitoring Control

## Time Horizon

Hours to shifts — real-time process supervision and operator interaction.

## Typical Systems

| System Category | Examples | Primary Function |
|----------------|---------|-----------------|
| SCADA platforms | AVEVA System Platform (Wonderware), Ignition (Inductive Automation), GE iFIX / iBatch, Siemens WinCC / WinCC OA, Rockwell FactoryTalk View SE, Schneider EcoStruxure SCADA | Process supervision, alarming, data collection |
| DCS supervisory | ABB 800xA, Honeywell Experion PKS, Emerson DeltaV, Yokogawa CENTUM VP, Siemens PCS 7 | Distributed control system operator and engineering interface |
| HMI servers and thin clients | Citrix-based HMI delivery, web-based HMI | Operator interface delivery |
| Alarm management | AVEVA Alarm Manager, Rockwell FactoryTalk Alarms & Events, dedicated alarm rationalization tools | Alarm lifecycle management (ISA-18.2) |
| Engineering workstations | Siemens TIA Portal, Rockwell Studio 5000, Schneider EcoStruxure Control Expert, ABB Automation Builder | PLC/DCS programming, configuration, commissioning |
| OPC server (process) | Matrikon OPC, Kepware KEPServerEX, Native vendor OPC servers | Protocol translation and data aggregation for Level 2/3 boundary |
| Advanced Process Control (APC) | AspenTech DMC3, Honeywell Profit Controller | Optimization overlays on DCS control |
| Safety system workstations | Triconex (Schneider), HIMA, Pilz engineering stations | SIS programming and engineering (must be separate from BPCS engineering) |

## Responsibilities at Level 2

* Supervisory control — operator interaction with the industrial process via HMI screens.
* Alarm management — detection, presentation and documentation of process and equipment alarms.
* Process visualization — real-time displays of process state, trends, flowsheets.
* PLC/DCS engineering and configuration — downloading programs, configuring I/O, tuning loops.
* Data collection — reading process values from Level 1 for display and historian forwarding.
* Recipe management (process industries) — managing and executing production recipes.

## Security Context for Level 2

**Engineering workstations: highest-risk assets at Level 2**

Engineering workstations require special attention because:
* They have write access to PLC and DCS controllers — the ability to modify control logic.
* They run vendor-specific engineering software (TIA Portal, Studio 5000, Control Expert) that communicates directly with Level 1 devices.
* They are often portable (laptops) that move between the OT environment and external networks.
* They are used by third-party vendors for commissioning, maintenance and troubleshooting.
* Unauthorized access to an engineering workstation is operationally equivalent to physical access to the controller cabinet.

**Engineering workstation security requirements:**
* Dedicated zone (Engineering Zone) separate from the SCADA/HMI zone.
* PAM (Privileged Access Management) for all engineering software access.
* Removable media control — no USB without scanning; use dedicated scanning station in DMZ.
* Laptop management — engineering laptops must meet OT security baseline before network connection; personal devices prohibited.
* Change management — every PLC download or DCS configuration change must be approved and logged.
* Version control — PLC programs and DCS configurations must be version-controlled with backup.

**SCADA server hardening:**
SCADA servers are long-lived Windows-based systems that require specific security treatment:
* Apply CIS Benchmark for Windows Server (adapted for SCADA constraints).
* Application whitelisting — only approved SCADA runtime, engineering software and defined utilities.
* Disable all unnecessary Windows features: IIS, remote registry, unused network protocols.
* Restrict outbound network connections to allowlisted destinations only.
* Configure Windows Firewall as secondary control behind network firewall.

**OPC DA vs OPC UA — critical security distinction:**

| | OPC DA (legacy) | OPC UA (recommended) |
|-|-----------------|---------------------|
| Technology base | DCOM/COM (Windows) | Platform-independent, TCP-based |
| Authentication | Windows authentication only | Username/password, X.509 certificates |
| Encryption | None | TLS (configurable) |
| Firewall traversal | Requires dynamic ports — practically unfirewallable | Fixed TCP port (default 4840) — firewallable |
| Security profiles | None | None / Sign / Sign and Encrypt |
| Recommendation | Replace or terminate before firewall | Use with Sign and Encrypt profile |

Wherever OPC DA must remain due to legacy constraints, it must be terminated on an OPC aggregator server on the OT side of the conduit — never bridged through a firewall using DCOM dynamic port mappings.

---

# Level 1 — Basic / Regulatory Control

## Time Horizon

Seconds to minutes — real-time control loop execution.

## Typical Systems

| System Category | Examples | Key Security Characteristics |
|----------------|---------|------------------------------|
| PLC — Siemens | SIMATIC S7-300/400 (legacy), S7-1200/1500 (current) | S7-1500: TLS, access levels, integrity protection; S7-300/400: minimal security |
| PLC — Rockwell | ControlLogix, CompactLogix, MicroLogix | FactoryTalk Security integration; CIP Security (EtherNet/IP authentication) |
| PLC — Schneider | Modicon M580, M340, Quantum | M580: CIP Security, HTTPS; older models: minimal security |
| PLC — ABB | AC500, AC500-S (safety) | Varies by model and version |
| PLC — Beckhoff | CX series, embedded PC-based | Windows-based: standard IT security applicable |
| DCS controllers | ABB S800/S900, Honeywell C300, Emerson DeltaV MD+/CIOC, Yokogawa CENTUM field controllers | Typically no native authentication; security through zone isolation |
| RTU | ABB RTU560, Schneider ION, GE D20MX, SEL-3530 | Substation automation; IEC 61850, DNP3; limited security in older models |
| Safety PLC / SIS | Triconex Trident/Tricon (Schneider), HIMA HIMatrix, Pilz PSS 4000, ABB Hi-Integrity | Proprietary OS; strict change management; separate engineering environment required |
| Variable frequency drives | Siemens SINAMICS, ABB ACS, Rockwell PowerFlex, Danfoss VLT | Increasingly Ethernet-connected; often minimal security; must be in field zone |
| Smart field devices | HART-enabled transmitters, Foundation Fieldbus devices, PROFIBUS PA | Diagnostic access; asset management integration; mostly physical access concern |

## Responsibilities at Level 1

* Execution of control logic — ladder diagram, Function Block Diagram (FBD), Structured Text (ST), Sequential Function Chart (SFC) programs.
* Collection of process measurements — reading from sensors via analog I/O, HART, Fieldbus.
* Control of actuators — writing to control valves, motor starters, variable speed drives.
* Interlocking and sequencing — safety interlocks (non-SIS), sequence of operations.
* Loop control — PID control loops for temperature, pressure, flow, level regulation.
* Communication to Level 2 — reporting process values via Modbus, EtherNet/IP, PROFINET, IEC 61850.

## Security Context for Level 1

**The fundamental security challenge of Level 1:**

Most Level 1 devices (PLCs, RTUs, DCS controllers) have limited or no native cybersecurity capabilities, particularly in legacy installations:
* No user authentication (any device on the network can read/write without credentials).
* No communication encryption (Modbus TCP, EtherNet/IP, PROFINET transmit in cleartext).
* No audit logging of configuration changes.
* Cannot be patched without process interruption — maintenance windows measured in months.
* Run proprietary real-time operating systems that cannot host traditional security software.

Security for Level 1 is therefore primarily achieved through:
1. **Network isolation** — strict zone boundaries and conduit controls prevent unauthorized access to Level 1 devices.
2. **Conduit filtering** — protocol-aware conduits filter Modbus function codes, EtherNet/IP services, limiting what commands can reach PLCs.
3. **Monitoring** — passive network monitoring detects anomalous communication patterns (unexpected source, unusual function codes, off-schedule write operations).
4. **Physical security** — locked cabinets, controlled physical access to controller hardware.
5. **Configuration management** — version-controlled backups of PLC programs; integrity monitoring comparing runtime program against known-good baseline.

**PLC program integrity monitoring:**

The most dangerous attack on a Level 1 device is unauthorized modification of the control program. This can be detected through:
* Periodic comparison of runtime PLC program hash against a known-good baseline.
* OT monitoring platforms (Dragos, Claroty, Nozomi) with passive read of PLC program metadata.
* Vendor tools: Siemens TIA Portal Audit, Rockwell FactoryTalk AssetCentre program comparison.
* Change management process: every PLC download must be authorized, recorded, and verified.

**Security capability comparison across PLC generations:**

| Generation | Typical Authentication | Encryption | Audit Log | Recommended Security Approach |
|------------|----------------------|-----------|-----------|-------------------------------|
| Legacy (pre-2010) S7-300, Quantum, older ControlLogix | None | None | None | Strict zone isolation, monitoring, compensating controls |
| Mid-generation (2010–2018) S7-1200, M340, newer ControlLogix | Basic password | None or partial | Limited | Zone isolation + vendor security features (access levels, CIP Security where available) |
| Current (post-2018) S7-1500, M580, CompactLogix 5480 | Certificate-based, user authentication | TLS (partial) | Yes | Apply all native security features + zone isolation + monitoring |

**Safety PLC (SIS) — special requirements:**

Safety PLCs (used in Safety Instrumented Systems — SIS) require treatment fundamentally different from standard PLCs:
* **Physical separation** from BPCS: SIS must be on physically separate hardware and network infrastructure — not merely logically isolated on shared infrastructure.
* **Separate engineering environment**: SIS engineering workstations must be separate from BPCS engineering workstations. No shared accounts, no shared tools, no shared network paths.
* **No remote access to SIS**: Remote access to SIS should be architecturally prohibited. If unavoidable, requires additional approval, physical presence of authorized personnel, and enhanced audit logging.
* **Firmware integrity**: SIS firmware and configuration must be verified against known-good baseline periodically.
* **Reference**: The Triton/TRISIS (2017) attack demonstrated that nation-state actors will specifically target SIS to defeat safety protections. See [IEC62443.md](IEC62443.md) for full technical analysis.

---

# Level 0 — Physical Process

## Typical Components

| Component Category | Examples | Integration to L1 |
|-------------------|---------|-------------------|
| Sensors — temperature | Thermocouple, PT100/PT1000, IR thermometer | 4–20 mA, HART, Fieldbus |
| Sensors — pressure | Diaphragm transmitter, pressure switch | 4–20 mA, HART |
| Sensors — flow | Electromagnetic, Coriolis, vortex, orifice plate + DP | 4–20 mA, HART, PROFIBUS PA |
| Sensors — level | Radar, ultrasonic, float switch, pressure | 4–20 mA, HART |
| Sensors — analytical | pH, conductivity, O2, gas analyzers | 4–20 mA, HART, PROFIBUS PA |
| Actuators — control valves | Globe, butterfly, ball with positioner | 4–20 mA, HART, Foundation Fieldbus |
| Actuators — on/off valves | Pneumatic with solenoid | Digital output |
| Actuators — motors | Pump, fan, compressor motor starters | Digital output, feedback |
| Variable frequency drives | Speed-controlled motors | Digital output + analog feedback; or Fieldbus/Ethernet |
| Field instruments | Transmitters, positioners | Various Fieldbus or analog |

## Responsibilities at Level 0

Level 0 components perform physical measurement (sensors) and physical actuation (actuators). They translate between the digital control domain (Level 1) and the physical process domain (process fluid, mechanical motion, heat, pressure).

## Security Context for Level 0

Level 0 devices generally have no cybersecurity capabilities. They are physical components — a thermocouple transmitting 4–20 mA has no network interface to compromise.

However, this changes as smart instrumentation and digital fieldbus networks become more prevalent:
* **HART-enabled devices** have diagnostic and configuration interfaces accessible via HART modems — improper access can change calibration or configuration.
* **Foundation Fieldbus / PROFIBUS PA** devices are network-accessible at the fieldbus level — configuration is possible from the fieldbus host.
* **Ethernet-connected drives (VFDs)** increasingly have web interfaces and Ethernet/IP connectivity — they must be treated as Level 1 devices, not Level 0.

**Primary security controls at Level 0:**
* Physical access control to field locations (cabinets, junction boxes, field instruments).
* Tamper detection (sealed enclosures, intrusion alarm contacts).
* Monitoring at the Level 1/2 boundary — detecting anomalous actuator behavior (unexpected valve movements, unexpected motor starts/stops) that may indicate a compromised controller.
* Asset management for smart field devices — documenting device types, firmware versions, calibration status.

---

# Information Flows — Detailed Analysis

ISA-95 defines the logical information exchange between levels. Understanding these flows — their content, direction, frequency and criticality — is essential for correct conduit design.

## Downward Flows (Enterprise → Manufacturing)

| Information Object | From Level | To Level | Typical System Path | Security Criticality |
|-------------------|-----------|---------|--------------------|--------------------|
| Production schedule | 4 (ERP) | 3 (MES) | ERP → MES via DMZ | Medium — controls what is produced |
| Production order | 4 (ERP) | 3 (MES) | ERP → MES via DMZ | Medium — quantity, timing |
| Recipe / formulation | 4 or 3 | 2 (SCADA/DCS) | MES → SCADA via conduit | **High** — defines product parameters |
| Process setpoints | 3 or 2 | 1 (PLC/DCS) | SCADA → PLC via conduit | **High** — directly affects process |
| Maintenance work order | 3 (CMMS) | 2 (engineering) | CMMS → Engineering WS | Medium — controls maintenance activities |
| Software update / patch | DMZ staging | 2 or 1 | Patch server → target system | **Critical** — code modification |
| Firmware update | DMZ staging | 1 (PLC/RTU) | Engineering WS → controller | **Critical** — modifies control logic |

**Security principle for downward flows:** The further down the hierarchy a write operation reaches, the more critical its security must be. A recipe being pushed to Level 1 controllers is a high-criticality operation that requires authentication, authorization, change management approval, and audit logging.

## Upward Flows (Manufacturing → Enterprise)

| Information Object | From Level | To Level | Typical System Path | Notes |
|-------------------|-----------|---------|--------------------|--------------------|
| Process data (time-series) | 1/2 | 3 (Historian) | SCADA/DCS → Historian | High volume; primarily unidirectional |
| Production actuals | 3 (MES/Historian) | 4 (ERP) | MES → ERP via DMZ | Batch or event-driven |
| Equipment status / alarms | 1/2 | 3 (CMMS/MES) | SCADA → CMMS | For maintenance triggers |
| Quality / lab results | 2/3 (QMS/LIMS) | 4 (ERP/QMS) | LIMS → ERP via DMZ | Regulated industries: GMP records |
| OEE / performance KPIs | 3 (Historian/MES) | 4 (BI/ERP) | Historian replica → BI via DMZ | Typically read-only aggregated data |
| Inventory counts | 3 (MES/WMS) | 4 (ERP) | MES → ERP via DMZ | Material balance reconciliation |
| Energy consumption | 3 (Energy mgmt) | 4 (ERP/BI) | Energy manager → reporting | Utility tracking, ISO 50001 |
| Audit records / batch records | 3 (MES/Batch) | 4 or archive | MES → archive | Regulated: must not be alterable |
| Security events / logs | All levels | SIEM (IT) | Via log relay in DMZ | Unidirectional; security monitoring |

## Information Ownership

A critical concept in ISA-95 is **information ownership** — which system is the authoritative source of truth for each data object.

| Data Object | Authoritative Owner | Consumer Systems |
|------------|--------------------|--------------------|
| Customer order | Level 4 (ERP CRM module) | Level 3 MES (receives production order) |
| Production schedule (master) | Level 4 (ERP PP module) | Level 3 MES (detailed scheduling) |
| Production schedule (detailed) | Level 3 MES | Level 2 SCADA (dispatch) |
| Recipe (master formula) | Level 4 (ERP or PLM) | Level 3 MES (recipe management) |
| Recipe (control recipe) | Level 3 MES (batch) | Level 2 DCS (execution) |
| Process setpoint | Level 2 SCADA/DCS | Level 1 PLC (execution) |
| Time-series process data | Level 1 PLC/RTU (measured) | Level 2 SCADA (display) → Level 3 Historian (storage) |
| Equipment master data | Level 3 CMMS | All levels (for context) |
| Calibration data | Level 3 LIMS/CMMS | Level 0/1 (field instruments) |
| Product quality result | Level 3 QMS/LIMS | Level 4 ERP (for shipment release) |

Clarity on information ownership prevents both redundant system connectivity (multiple systems maintaining independent copies and needing separate sync paths) and incorrect access control design (granting write access to non-authoritative systems).

---

# Industrial Protocols — Security Analysis by Level

## Protocol Overview

| Level Boundary | Common Protocols | Direction | Security Profile |
|----------------|-----------------|-----------|-----------------|
| L4 ↔ L3 (via DMZ) | OPC UA, REST/HTTP(S), SOAP/XML, database link, file transfer | Bidirectional via DMZ | Must be encrypted; authenticate source; terminate in DMZ |
| L3 ↔ L2 | OPC UA, OPC DA (legacy), SQL, proprietary historian protocols | Bidirectional | OPC UA with Sign+Encrypt; eliminate OPC DA where possible |
| L2 ↔ L1 | Modbus TCP, EtherNet/IP (CIP), PROFINET, IEC 61850 (GOOSE/MMS), DNP3 | Bidirectional | No native encryption; rely on zone isolation and conduit filtering |
| L1 ↔ L0 | 4–20 mA (analog), HART, Foundation Fieldbus, PROFIBUS DP/PA, IO-Link | Bidirectional | Physical; limited attack surface; HART diagnostic interfaces require physical access control |

## Protocol Deep Dive — Security Characteristics

### Modbus TCP

* **History:** Originally developed by Modicon (1979), designed for serial communication, no security features designed in.
* **Native security:** None — no authentication, no encryption, no integrity protection.
* **Attack possibilities:** Any device on the network can read or write any coil/register; function code 16 (write multiple registers) and function code 5 (write single coil) can directly affect process.
* **Mitigations:** Zone isolation (Modbus traffic must stay within the field zone); conduit filtering on allowed function codes (e.g., block FC16 from unexpected sources); passive monitoring for anomalous Modbus activity.

### EtherNet/IP (CIP)

* **History:** Allen-Bradley / Rockwell standard, now maintained by ODVA; CIP (Common Industrial Protocol) over Ethernet.
* **Native security:** CIP Security (added in 2017) provides authentication, authorization and encryption using TLS; requires both device and client to support CIP Security.
* **Legacy status:** Most deployed ControlLogix/CompactLogix systems predate CIP Security or require upgrade.
* **Attack possibilities:** Without CIP Security: unauthorized reads/writes; replay attacks; CIP program upload/download.
* **Mitigations:** Enable CIP Security where supported (Rockwell Studio 5000 v31+, newer ControlLogix firmware); zone isolation and conduit filtering for legacy deployments; monitor CIP service codes.

### PROFINET

* **History:** Siemens-originated, now maintained by PI (PROFIBUS and PROFINET International); used in most Siemens and many other European OT installations.
* **Native security:** PROFINET Security (IEC 62443-3-3 aligned) added in recent versions; S7-1500 supports TLS and integrity protection; older S7-300/400 do not.
* **Attack possibilities:** S7comm protocol (older): no authentication; Siemens S7 vulnerability class (similar to Stuxnet attack surface).
* **Mitigations:** Upgrade to S7-1500 where feasible; enable S7-1500 security features (access levels, TLS); use Siemens TIA Portal Audit for S7-1500 change tracking; VLAN isolation; monitoring for S7comm anomalies.

### IEC 61850 (GOOSE / MMS / SAMPLED VALUES)

* **History:** Standard for substation automation and protection; used in electrical utility environments.
* **Subsystems:** GOOSE (Generic Object Oriented Substation Event) — high-speed multicast for protection; MMS (Manufacturing Message Specification) — for configuration and monitoring; Sampled Values — for differential protection.
* **Native security:** IEC 62351 defines security extensions for IEC 61850 (authentication, encryption); adoption is inconsistent.
* **Attack possibilities:** GOOSE spoofing — injecting false GOOSE messages can trigger incorrect protection operations or disable real protection; MMS — unauthorized access to IED configuration.
* **Mitigations:** Network isolation of GOOSE traffic (VLAN, timing-sensitive so minimal network overhead); implement IEC 62351 authentication for MMS; monitor for unexpected GOOSE publishers; physical security of IEDs.

### DNP3

* **History:** Developed for electric utilities (EPRI) and water/wastewater; widely used for RTU communication over WAN.
* **Native security:** DNP3 Secure Authentication (SA) v5 adds HMAC-based authentication; not universally deployed.
* **Attack possibilities:** Without SA: unauthorized reads/writes to RTU; command injection over WAN or local network.
* **Mitigations:** Implement DNP3 Secure Authentication v5 where supported; encrypt WAN transport (IPsec VPN, TLS wrapper); strict access control on DNP3 master stations.

### OPC UA

* **History:** OPC Foundation successor to OPC DA; platform-independent, security-designed.
* **Native security:** Three security profiles: None (no security), Sign (integrity), Sign and Encrypt (integrity + confidentiality); certificate-based authentication.
* **Recommendation:** Always use Sign and Encrypt profile for inter-zone communication. None profile is acceptable only within a single zone where additional controls are in place.
* **Attack possibilities:** Misconfigured OPC UA (None profile, anonymous access) provides full read/write to all exposed nodes; certificate management failures allow unauthorized clients.
* **Mitigations:** Enforce Sign and Encrypt profile at all conduit-crossing connections; use certificate-based authentication; configure node-level access control; audit anonymous access attempts.

### OPC DA (Legacy)

* **Why it remains:** Massive installed base; many legacy SCADA/DCS versions only support OPC DA; some vendors have not completed OPC UA migration.
* **Security problems:** Based on DCOM (Distributed Component Object Model); requires dynamic port ranges (above 1024) making firewall rules impractical; relies on Windows authentication via DCOM — complex to configure correctly; effectively cannot be firewalled without breaking functionality.
* **Migration path:** Replace with OPC UA where possible. Where not immediately possible: terminate OPC DA on an aggregation server in the OT network; expose only OPC UA upward to DMZ and Level 3.

---

# Integration Architecture Patterns

## Pattern 1: Historian Integration (Most Common)

**Problem:** Enterprise BI tools, management dashboards and ERP systems need read access to process data. The production historian stores this data but must not be directly accessible from IT.

**Secure pattern:**

```
[Level 4 — Enterprise IT]
    Power BI, management dashboards, ERP reporting
        ↑ Read only via HTTPS/OPC UA
[Industrial DMZ]
    Historian replica (e.g., PI AF server, read-only replica)
        ← Unidirectional replication (data diode or one-way replication channel)
[Level 3 — MOM zone]
    Production historian (PI Server, Uniformance, etc.)
        ↑ OPC UA / OPC DA collection
[Level 2 — Control zone]
    SCADA / DCS / OPC aggregator
```

**Key design decisions:**
* Replication is unidirectional (OT→IT) — no write-back from replica to production historian.
* Enterprise consumers authenticate to the replica, not the production historian.
* The replica has a data retention lag (minutes) — acceptable for management reporting; not suitable for real-time operator use.

---

## Pattern 2: MES Integration (Production Dispatching)

**Problem:** MES needs to send work orders and recipes to SCADA/DCS and receive production confirmations back. Genuine bidirectional communication is required.

**Secure pattern:**

```
[Level 4 — ERP]
    Production orders, BOM, schedules
        ↓↑ via Industrial DMZ (REST API / flat file via SFTP)
[Level 3 — MES]
    MES server (production execution, tracking)
        ↓ Recipes, work order dispatch (OPC UA write, allowlisted nodes only)
        ↑ Production actuals, material consumption (OPC UA read)
[Level 2 — SCADA/DCS]
    SCADA server, DCS workstation
```

**Key design decisions:**
* All MES→SCADA communication uses OPC UA with Sign and Encrypt profile.
* OPC UA node access is allowlisted — MES can write only to specific recipe parameters and read only from production counter tags.
* MES-originated write operations to Level 2 are logged for audit.
* MES cannot initiate connections below Level 2 — it does not have network access to Level 1.

---

## Pattern 3: Vendor Remote Access

**Problem:** PLC vendor needs periodic remote access for firmware updates, troubleshooting and maintenance. Network path must exist, but must be fully controlled.

**Secure pattern:**

```
[Vendor — external internet]
    Vendor technician laptop (approved, managed device)
        ↓ MFA-authenticated VPN tunnel
[Industrial DMZ — Remote Access Zone]
    MFA gateway, VPN concentrator
        ↓ Authenticated session (time-limited)
[Industrial DMZ — Jump Server]
    Jump server / bastion host
    Session recording (video + keystroke)
        ↓ Allowlisted connection to specific target only
[Target zone — e.g., Engineering Zone]
    Specific engineering workstation or PLC
```

**Key design decisions:**
* Vendor has zero network access outside the specific approved session.
* All credentials are time-limited and auto-expire after the session window.
* Full session recording is maintained outside the OT network.
* Internal coordinator reviews or approves the session in real time for high-criticality systems.
* Post-session: credentials revoked, PLC program integrity verified.

---

## Pattern 4: Security Monitoring (Log Flow)

**Problem:** Security monitoring requires log data from OT systems to be available to an enterprise SIEM, but without creating a writable path from IT into OT.

**Secure pattern:**

```
[Level 2/3 — OT network]
    SCADA, firewalls, OT IDS, engineering WS, historians
        ↓ Syslog / CEF / LEEF (one-way push)
[Industrial DMZ — Log Relay]
    Log relay / syslog forwarder (or data diode for log traffic)
        ↓ Forward (no command channel back)
[Enterprise IT — SIEM]
    Splunk, IBM QRadar, Microsoft Sentinel, etc.
```

**Key design decisions:**
* Log flow is always OT → IT (push from OT, never pull from IT).
* Data diode can enforce hardware unidirectionality for maximum assurance.
* SIEM has no network access back into the OT network — alerts and investigation require the jump server path.
* OT-specific parsing rules must be implemented in SIEM for industrial protocol events.

---

# Relationship with the Purdue Model

ISA-95 and the Purdue Reference Model for Plant Hierarchical Control (PERA) are related but distinct.

| Dimension | Purdue Model (PERA) | ISA-95 |
|-----------|--------------------|----|
| Primary focus | Network connectivity and trust boundaries | Business functions and information ownership |
| Level labeling | L0–L5 (includes enterprise at L5) | L0–L4 |
| Security use | Define network zone candidates | Understand what must be secured and why |
| Introduced by | Theodore Williams (1989, Purdue University) | ISA-95 committee (ANSI/ISA-95, first published 2000) |
| Primary domain | Network architecture | Manufacturing information systems |
| OT security use | Defines where firewalls go | Defines what goes behind the firewalls and why |

> **Single source of truth.** This document is authoritative for the **functional** hierarchy (Levels 0–4: systems, responsibilities, information flows, ownership). The **network-trust** reference architecture — the same level numbering interpreted as trust boundaries, including **Level 5 (enterprise/cloud)** and modern extensions (IIoT, edge, remote engineering) that ISA-95 does not model — is maintained in **[Purdue-Model.md](Purdue-Model.md)**. Refer there for network/trust detail rather than duplicating it here.

## How They Work Together

**Step 1: Use ISA-95** to identify systems, their functions, and information flows. Answer: "What exists? What does it do? What information moves between which systems?"

**Step 2: Use Purdue** to map those systems to network trust levels and identify initial zone candidates. Answer: "Where does each system sit in the network hierarchy? What connects to what?"

**Step 3: Use IEC 62443-3-2** to perform risk assessment, refine zone boundaries, assign Security Level Targets, and define conduit requirements. Answer: "What level of security does each zone need, and how do we verify it?"

---

# Relationship with IEC 62443

ISA-95 and IEC 62443 are complementary frameworks that must be used together. ISA-95 defines the functional architecture; IEC 62443 defines how to secure it.

| ISA-95 Output | IEC 62443 Input |
|--------------|----------------|
| System inventory per level | Asset inventory for SLRA (IEC 62443-3-2) |
| Functional boundaries | Initial zone partitioning candidates |
| Information flow direction and criticality | Conduit security requirements (IEC 62443-3-3 FR 5) |
| Write operations (recipe download, setpoint update) | High-criticality conduit controls (IAC, UC requirements) |
| Integration points (MES↔SCADA, Historian↔IT) | Security Level Target determination for boundary zones |
| Unnecessary integrations identified | Attack surface reduction opportunities |

**Practical sequence:**

```
ISA-95 Analysis → Functional architecture understood
        ↓
Purdue Mapping → Network trust levels identified
        ↓
IEC 62443-3-2 (SLRA) → Zone SL-T determined, conduit requirements defined
        ↓
IEC 62443-3-3 → Detailed security requirements per zone
        ↓
IEC 62443-4-2 → Component selection verification (SL-C ≥ SL-T)
```

---

# Sector-Specific ISA-95 Architecture Variants

The ISA-95 model applies universally, but its implementation varies significantly by industrial sector. Understanding sector-specific patterns is essential for correct scope determination (see [Czech-Cybersecurity-Act.md](Czech-Cybersecurity-Act.md)) and zone design.

## Electric Power and Utilities

**ISA-95 level mapping in power:**

| ISA-95 Level | Power Sector Systems |
|-------------|---------------------|
| Level 4 | Energy trading, financial settlement, customer billing, asset management |
| Level 3 | Energy Management System (EMS), Distribution Management System (DMS), Outage Management System (OMS), Historian, MDMS (meter data) |
| Level 3.5 | Industrial DMZ, ICCP gateway (inter-utility communication), market interface |
| Level 2 | SCADA (for transmission/distribution), HMI, control center operators |
| Level 1 | RTU, IED (Intelligent Electronic Devices), protection relays (IEC 61850), substation automation |
| Level 0 | Sensors (current transformers, voltage transformers), circuit breakers, switches, capacitor banks |

**Sector-specific integration challenges:**
* ICCP (Inter-Control Center Communication Protocol) — used for energy exchange data between utilities; must terminate in DMZ, not connect directly to control center.
* AMI (Advanced Metering Infrastructure) — smart meters create a new network extending from Level 4 (billing) to the customer premises; must be isolated from control network.
* DERMS (Distributed Energy Resource Management System) — manages distributed generation (solar, wind, batteries); creates new Level 3/2 integration points with bidirectional power flow implications.

## Water and Wastewater

**ISA-95 level mapping in water:**

| ISA-95 Level | Water Sector Systems |
|-------------|---------------------|
| Level 4 | Corporate ERP, billing systems, regulatory reporting |
| Level 3 | SCADA historian, lab information (LIMS for water quality), asset management (CMMS) |
| Level 3.5 | Industrial DMZ, remote operations center connectivity |
| Level 2 | SCADA (centralized or distributed), HMI at treatment works |
| Level 1 | PLC, RTU at pump stations, treatment processes, tanks |
| Level 0 | Sensors (flow meters, pressure, turbidity, chlorine, pH), actuators (pumps, valves) |

**Sector-specific integration challenges:**
* Geographically distributed systems — RTUs at remote pump stations connected via telemetry (4G/LTE, licensed radio); each remote site is a potential entry point.
* LIMS integration — lab results must feed into process control (e.g., chlorine dosing setpoints); this is a Level 3→Level 2 write path that requires careful control.
* Online analyser integration — continuous water quality monitoring (chlorine, pH, turbidity) at Level 0/1 feeds into SCADA alarms and potential automatic control actions.

## Process Industry (Chemical, Refining, Pharma)

**ISA-95 level mapping in process industry:**

| ISA-95 Level | Process Industry Systems |
|-------------|------------------------|
| Level 4 | ERP (SAP), Supply chain, regulatory reporting (pharma: LIMS to regulatory submissions) |
| Level 3 | MES (batch management — Werum PAS-X, Siemens Opcenter), Historian, QMS, LIMS |
| Level 3.5 | Industrial DMZ, ERP/MES integration point |
| Level 2 | DCS (primary control), APC (Advanced Process Control), SCADA (for utilities) |
| Level 1 | DCS field controllers, safety PLC (SIS), ESD, F&G systems |
| Level 0 | Process analysers, temperature/pressure/flow/level instruments, control valves |

**Sector-specific integration challenges:**
* Batch record integrity (pharma, food) — electronic batch records (eBR) created by MES must be tamper-evident and traceable; access controls and audit trails are regulatory requirements (FDA 21 CFR Part 11, EU GMP Annex 11).
* SIS/BPCS boundary — safety systems must be independent from basic process control; this boundary must be maintained in the ISA-95 functional architecture as well as the physical network.
* Advanced Process Control (APC) integration — APC systems calculate optimal setpoints and push them to DCS; this is a Level 3→Level 2 write path requiring the same controls as recipe download.

## Discrete Manufacturing (Automotive, Electronics)

**ISA-95 level mapping in discrete manufacturing:**

| ISA-95 Level | Discrete Manufacturing Systems |
|-------------|-------------------------------|
| Level 4 | ERP (SAP), PLM (product lifecycle management), SCM |
| Level 3 | MES (production tracking, quality, maintenance), Historian (OEE data), WMS (warehouse) |
| Level 3.5 | Industrial DMZ, ERP/MES integration point |
| Level 2 | SCADA (for utilities — compressed air, cooling), Robot cell HMI, Line PLC supervisory |
| Level 1 | Line PLC (cell controller, conveyor control), Robot controllers (KUKA, ABB, Fanuc) |
| Level 0 | Sensors (presence, vision systems, torque sensors), actuators (pneumatic, servo drives) |

**Sector-specific integration challenges:**
* Robot controllers — industrial robots (KUKA, ABB, Fanuc) run proprietary operating systems with Ethernet connectivity; they have both Level 1 (motion control) and Level 2 (supervisory) characteristics.
* Vision systems — machine vision cameras often run embedded Linux/Windows and have network connectivity; must be placed in appropriate zone.
* Tool management and MES integration — tool life data from machines to MES; tooling downloads from engineering to machines.

---

# OT Asset Inventory Structured by ISA-95 Levels

A complete OT asset inventory should be structured by ISA-95 level to facilitate zone assignment, access control design, and regulatory scope determination.

| Level | Asset Category | Typical Metadata Required |
|-------|---------------|--------------------------|
| 4 | Enterprise IT systems | System name, vendor, function, network location, IT owner |
| 3 | MES | Vendor, version, database type, integration points (up and down) |
| 3 | Historian | Vendor (PI, Uniformance), version, tag count, replication configuration |
| 3 | CMMS | Vendor, version, field device integration paths |
| 3 | QMS / LIMS | Vendor, version, integration to Level 4 and Level 2 |
| 3 | Batch management | Vendor, version, recipe server location |
| 3.5 | Historian replica | Vendor, version, replication source, consumer systems |
| 3.5 | Jump server | OS, version, session recording solution, access management tool |
| 3.5 | File transfer server | OS, version, allowed file types, scanning tool |
| 3.5 | Patch staging server | OS, version, patching tool |
| 3.5 | Firewalls (DMZ inner + outer) | Vendor, model, firmware version, ruleset review date |
| 2 | SCADA server | Vendor (AVEVA, Ignition, WinCC), version, OS, database, tag count |
| 2 | DCS workstation | Vendor, DCS platform version, connected controllers |
| 2 | HMI server | Vendor, version, OS, connected SCADA/DCS |
| 2 | Engineering workstation | Vendor engineering SW, version, connectivity to controllers |
| 2 | Alarm management | Vendor, version, integration |
| 2 | OPC server | Type (DA/UA), vendor, version, exposed nodes |
| 1 | PLC | Vendor, model, CPU type, HW revision, FW version, program version, IP address, protocols |
| 1 | RTU | Vendor, model, FW version, communication (DNP3, IEC 61850, Modbus), WAN connectivity |
| 1 | DCS controller | Vendor, platform, FW version, I/O count, redundancy configuration |
| 1 | Safety PLC (SIS) | Vendor (Triconex, HIMA, Pilz), model, FW version, SIL rating, proof test date |
| 1 | VFD with Ethernet | Vendor, model, FW version, connected network, control protocol |
| 0 | Smart field devices | Tag name, instrument type, HART device revision, calibration date |
| Cross-cutting | Industrial switches | Vendor, model, FW version, managed/unmanaged, VLAN config, management access |
| Cross-cutting | Industrial firewalls | Vendor, model, FW version, ruleset, last review |
| Cross-cutting | OT IDS/NDR | Vendor (Claroty, Nozomi, Dragos), version, monitored segments |
| Cross-cutting | Wireless AP (if any) | Vendor, model, FW version, security standard, zone |

This structure should align with the asset inventory required by [Czech-Cybersecurity-Act.md](Czech-Cybersecurity-Act.md) (Decree 409/410) and feed directly into the Zone partitioning in [IEC62443.md](IEC62443.md).

---

# Common Misconceptions

ISA-95 is **not**:

* A cybersecurity standard — it defines functional architecture, not security requirements.
* A network segmentation guide — it defines business function boundaries, not firewall rules.
* A firewall design methodology — conduit design requires IEC 62443, not ISA-95.
* A PLC programming standard — it does not address controller logic.
* Identical to the Purdue Model — Purdue focuses on network trust; ISA-95 focuses on business function. They are complementary.
* A replacement for IEC 62443 — ISA-95 tells you *what* to secure; IEC 62443 tells you *how*.
* Only for large enterprises — the functional hierarchy applies regardless of organization size; the depth of implementation scales.

Its primary purpose is operational integration, manufacturing information ownership, and functional boundary definition — which are prerequisites for correct security design.

---

# Architect Notes

Experienced architects use ISA-95 **before** discussing cybersecurity controls. Understanding the functional architecture first prevents the two most common architectural failures:

* **Over-restriction:** Blocking legitimate operational flows because their business purpose was not understood. Result: security is disabled in production because it prevents operations.
* **Under-restriction:** Failing to control dangerous integration paths because their risk was not understood. Result: attackers pivot from IT into OT via paths the security design did not consider.

## Questions to Answer Using ISA-95 Before Designing Security

**System ownership:**
* Which system is the authoritative owner of production scheduling — ERP or MES?
* Where is the master recipe maintained — ERP (PLM), MES, or SCADA?
* Which system generates production actuals that ERP consumes?
* Who is responsible for process historian data quality — IT or OT?

**Integration necessity:**
* Which applications require direct read access to Level 1/2 data, and which only need aggregated Level 3 data?
* Can historian replica in DMZ serve all IT consumers, or do any systems require real-time Level 2 data?
* Which Level 3→Level 2 write paths are functionally required (recipe download, setpoint push)?
* Which existing integrations have accumulated without deliberate design — and can they be eliminated?

**Attack surface reduction:**
* Which integration points have grown organically and are not formally documented?
* Which vendor remote access paths are still active but no longer needed?
* Which applications have been granted higher-level access than their function requires?

Reducing unnecessary integrations improves both maintainability and cybersecurity. An integration that does not exist cannot be exploited.

---

# Checklists / Kontrolní seznamy

---

## Checklist 1: ISA-95 Analýza před zahájením bezpečnostního návrhu

Použij před zahájením návrhu bezpečnostní architektury (zóny, kanály, přístupová práva). Dokončení tohoto checklistu je předpokladem pro správný zone/conduit design.

### Česky

**Inventář systémů dle ISA-95 úrovní**
- [ ] Byly identifikovány všechny systémy na úrovni 4 (ERP, SCM, BI, HR) a jejich funkce
- [ ] Byly identifikovány všechny systémy na úrovni 3 (MES, Historian, CMMS, QMS, LIMS, Batch) a jejich funkce
- [ ] Byl navržen nebo potvrzen obsah průmyslové DMZ (Historian replica, Jump server, File transfer, Patch staging)
- [ ] Byly identifikovány všechny systémy na úrovni 2 (SCADA, DCS, HMI, Engineering WS, OPC servery)
- [ ] Byly identifikovány všechny řídicí systémy na úrovni 1 (PLC, RTU, DCS řadiče, Safety PLC, VFD s Ethernet)
- [ ] Byly identifikovány smart field devices na úrovni 0 se síťovými rozhraními (HART, Fieldbus, Ethernet VFD)

**Vlastnictví informací**
- [ ] Je jasné, který systém je autoritativním zdrojem produkčního harmonogramu (ERP nebo MES)?
- [ ] Je jasné, kde jsou uloženy receptury / formule (ERP/PLM, MES, SCADA/DCS)?
- [ ] Je jasné, který systém generuje výrobní výsledky pro ERP?
- [ ] Je jasné, kdo zodpovídá za kvalitu dat v historickém archivu (IT nebo OT)?

**Informační toky**
- [ ] Byly zdokumentovány všechny downward flows (zápisy z L4→L3, L3→L2, L2→L1)
- [ ] Byly zdokumentovány všechny upward flows (sběr dat z L1→L2→L3→L4)
- [ ] Byl každý flow označen z hlediska bezpečnostní kritičnosti (kritický = write do řídicích systémů)
- [ ] Bylo posouzeno, které flows jsou unidirektionální vs. bidirektionální
- [ ] Bylo posouzeno, které flows mohou být realizovány jednosměrnou branou (data diode)?

**Integrace — nutnost vs. akumulace**
- [ ] Byly identifikovány všechny existující integrace (IT↔OT, systém↔systém)
- [ ] Bylo pro každou integraci ověřeno, zda má dokumentovaný provozní důvod
- [ ] Byly identifikovány integrace, které vznikly bez formálního návrhu (ad hoc, "dočasné")
- [ ] Bylo posouzeno, které integrace lze eliminovat nebo omezit bez provozního dopadu
- [ ] Byly identifikovány všechny aktivní remote access přístupy dodavatelů (stávající i historické)

**Připravenost pro bezpečnostní návrh**
- [ ] Je kompletní funkční architektura zdokumentována (systémy, funkce, toky)
- [ ] Jsou identifikovány systémy s bidirektionální komunikací přes úrovňové hranice
- [ ] Je document review schválen OT architektem i zástupcem provozu
- [ ] Může bezpečnostní architekt zahájit zone/conduit design na základě ISA-95 analýzy?

---

## Checklist 2: Posouzení integračních bodů L4/L3 a L3/L2

Použij pro každý identifikovaný integrační bod mezi ISA-95 úrovněmi.

### Česky

**Pro každou integraci vyplň:**

**Základní identifikace**
- [ ] Zdrojový systém a jeho ISA-95 úroveň: _______________
- [ ] Cílový systém a jeho ISA-95 úroveň: _______________
- [ ] Směr komunikace: unidirektionální / bidirektionální
- [ ] Protokol: OPC UA / OPC DA / SQL / REST / soubor / jiné: _______________
- [ ] Frekvence komunikace: real-time / periodicky (interval: ___) / event-driven

**Bezpečnostní posouzení**
- [ ] Je integrace nezbytná pro provozní funkci (bez ní by provoz selhal)?
- [ ] Pokud ano: je provozní důvod zdokumentován?
- [ ] Pokud ne: může být integrace eliminována nebo nahrazena omezenějším přístupem?
- [ ] Přenáší integrace write operace směrem dolů (k řídicím systémům)?
  - Pokud ano: je tento write přístup minimalizován (pouze nezbytné tagy/registry)?
  - Pokud ano: je write přístup logován a monitorován?
- [ ] Přenáší integrace citlivé informace (receptury, procesní data, IP)?

**Implementace kanálu (conduit)**
- [ ] Je integrace realizována přes definovaný kanál (conduit) s allowlist pravidly?
- [ ] Je použit protokol s native security (OPC UA Sign+Encrypt)?
- [ ] Pokud je použit OPC DA: je terminován na OPC aggregation serveru před průchodem firewallovou hranicí?
- [ ] Je komunikace monitorována (anomálie, neoprávněné zdroje)?
- [ ] Je integrace zahrnuta v inventáři integrací a přezkoumávána pravidelně?

---

## Checklist 3: Bezpečnostní revize stávajícího OT prostředí (ISA-95 lens)

Použij při bezpečnostním auditu nebo revizi stávající OT infrastruktury.

### Česky

**Mapování na ISA-95**
- [ ] Bylo provedeno mapování všech existujících systémů na ISA-95 úrovně?
- [ ] Existují systémy, jejichž ISA-95 umístění není jasné (nejasná funkce nebo vazby)?
- [ ] Existují systémy, které komunikují s více ISA-95 úrovněmi bez jasného důvodu?

**Neřízené integrace**
- [ ] Existují přímá propojení L4↔L2 nebo L4↔L1 (přeskakující L3 a DMZ)?
- [ ] Existuje přímá SQL nebo ODBC konektivita z IT do OT databází?
- [ ] Existují aktivní VPN tunely nebo modemová připojení bez evidence?
- [ ] Má Historian přímou konektivitu z IT mimo DMZ?
- [ ] Mají ERP systémy přímý přístup k SCADA nebo DCS bez průchodu DMZ?
- [ ] Existují sdílené servery nebo sdílená síťová infrastruktura IT/OT?

**Protokolové problémy**
- [ ] Je OPC DA (DCOM) použit přes firewallové hranice (L3/L2 nebo DMZ)?
- [ ] Jsou Modbus nebo EtherNet/IP systémy přístupné ze sítí mimo jejich zónu?
- [ ] Jsou použita výchozí SNMP community strings (public/private) na průmyslových switchích?
- [ ] Je management rozhraní průmyslových switchů dostupný z více zón než je nezbytné?

**Remote access**
- [ ] Jsou všechny aktivní remote access přístupy evidovány?
- [ ] Existují přístupy otevřené pro konkrétní projekt nebo servis, které nebyly uzavřeny?
- [ ] Jsou všechny remote přístupy vedeny přes jump server v DMZ?
- [ ] Je pro všechny remote přístupy vyžadováno MFA?

**Doporučení**
- [ ] Byly identifikovány integrace doporučené k eliminaci
- [ ] Byly identifikovány integrace doporučené k migraci na bezpečnější protokol (OPC DA → OPC UA)
- [ ] Byly identifikovány neřízené remote access přístupy doporučené k uzavření
- [ ] Je plán nápravných opatření zdokumentován a schválen managementem?

---

## Checklist 4: Dokumentace OT prostředí strukturovaná dle ISA-95

Použij pro ověření, že je dokumentace OT prostředí kompletní a strukturovaná způsobem, který podporuje bezpečnostní návrh i compliance.

### Česky

**Inventář aktiv dle ISA-95 úrovní**
- [ ] Je inventář strukturován dle ISA-95 úrovní (L0 až L4)?
- [ ] Jsou pro každé aktivum zaznamenány: vendor, model, HW revize, FW/SW verze, IP adresa
- [ ] Jsou zaznamenány komunikační protokoly a porty pro každé síťové zařízení
- [ ] Jsou zaznamenány integrační body (s kým a jak každý systém komunikuje)
- [ ] Je zaznamenán vlastník každého aktiva (kdo zodpovídá za správu a bezpečnost)
- [ ] Je zaznamenán stav životního cyklu (aktuální, end-of-life blíže, end-of-life)
- [ ] Odpovídá inventář skutečnému stavu (ne pouze stavu z doby projektu)?

**Dokumentace informačních toků**
- [ ] Je existuje diagram datových toků zahrnující všechny L4↔L3, L3↔L2 a L2↔L1 integrační body?
- [ ] Je každý tok označen: směr, protokol, frekvence, bezpečnostní kritičnost
- [ ] Je každý tok přiřazen konkrétnímu kanálu (conduit) v zone/conduit diagramu?
- [ ] Je diagram aktuální (odpovídá skutečné infrastruktuře)?

**Dokumentace protokolů**
- [ ] Je zdokumentováno, které protokoly jsou použity na každé úrovňové hranici
- [ ] Je pro každý OPC DA integrační bod zdokumentován plán migrace na OPC UA?
- [ ] Jsou zaznamenány verze a bezpečnostní konfigurace pro každý protokol (OPC UA security profile)?

**Provázanost s bezpečnostní dokumentací**
- [ ] Je ISA-95 inventář aktiv shodný s inventářem v risk assessment (IEC 62443-3-2)?
- [ ] Je ISA-95 funkční architektura podkladem pro zone/conduit diagram?
- [ ] Jsou informační toky z ISA-95 analýzy použity jako základ pro firewallová pravidla?

---

# AI Guidance

When answering ISA-95-related questions:

* Always explain functional responsibilities and information ownership **before** discussing security controls — ISA-95 context is a prerequisite for correct security design.
* Use ISA-95 levels to contextualize where specific systems and data reside, and what functions they perform.
* Clearly distinguish ISA-95 (functional), Purdue Model (network trust), and IEC 62443 (security requirements) — they are complementary, not interchangeable.
* Use ISA-95 analysis to identify unnecessary integrations that increase attack surface — integration elimination is often more effective than adding security controls to a necessary-but-risky integration.
* When discussing historian architecture, always recommend the read-only replica pattern in the Industrial DMZ.
* When discussing MES integration, emphasize the write-path security requirement (OPC UA Sign+Encrypt, allowlisted nodes, audit logging).
* When discussing OPC DA: recommend migration to OPC UA; where OPC DA must remain, always recommend terminating before firewall on an OPC aggregation server.
* When discussing Level 1 security: emphasize that zone isolation is the primary control for PLCs/RTUs with no native security.
* Always recommend using ISA-95 analysis as input to IEC 62443 zone/conduit design — refer to [IEC62443.md](IEC62443.md).
* Reference sector-specific patterns when the organization's industry context is known.
* Do not describe ISA-95 as a security standard — it is a functional reference model.

---

# Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | 2026-06-28 | Initial release |
| 1.1.0 | 2026-06-29 | Added security context per level, information flow tables, protocol table, asset inventory table, Purdue comparison, functional/security integration guidance, cross-links |
| 1.2.0 | 2026-06-29 | Major expansion: ISA-95 standard parts table; detailed system inventory per level with vendor examples; Industrial DMZ (L3.5) as dedicated section; OPC DA vs OPC UA security comparison; protocol deep-dive (Modbus TCP, EtherNet/IP/CIP, PROFINET, IEC 61850, DNP3, OPC UA, OPC DA); 4 integration architecture patterns (Historian, MES, Vendor Remote Access, Security Monitoring/Log Flow); sector-specific ISA-95 variants (Power, Water, Process Industry, Discrete Manufacturing); detailed information ownership table; information flow criticality analysis; extended OT asset inventory with metadata requirements; 4× checklist in Czech language |
| 1.3.0 | 2026-07-01 | Clarified single-source-of-truth split with Purdue-Model.md (this document authoritative for the functional hierarchy; network-trust view delegated to Purdue-Model.md); added Purdue-Model.md cross-link in Related Documents and in the Purdue relationship section; normalized all cross-reference links (removed dead `#` anchors; corrected `IEC62443-Overview.md` → `IEC62443.md`) |
