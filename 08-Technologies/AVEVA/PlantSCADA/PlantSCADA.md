---
id: plantscada
title: "AVEVA Plant SCADA — Architecture Overview"
category: Technologies
layer: "08-Technologies/AVEVA/PlantSCADA"
vendor: "AVEVA"
product: "Plant SCADA"
version: 1.1.0
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  AVEVA Plant SCADA (formerly Citect) architecture overview: components, redundancy and high
  availability, typical Purdue placement, industrial communication, integration and security
  architecture.
keywords: [AVEVA, Plant SCADA, Citect, SCADA, redundancy, vysoká dostupnost, architecture overview]
---

# Purpose

AVEVA Plant SCADA (formerly Schneider Electric Citect SCADA) is a Supervisory Control and Data Acquisition (SCADA) platform designed for monitoring, supervising and controlling industrial automation systems.

Plant SCADA provides centralized visualization, alarm management, historical data collection, operator interaction and integration with industrial controllers across manufacturing and process industries.

Within an Industrial Automation and Control System (IACS), Plant SCADA acts as the primary supervisory layer between industrial controllers and plant operators.

This document describes the architectural role of Plant SCADA, its primary components and security considerations.

---

# Scope

This document describes:

* Architectural role
* System components
* Typical deployment
* Security architecture
* High availability
* Industrial integration
* OT security considerations

Configuration procedures are covered in technology-specific documents.

---

# Product Philosophy

Plant SCADA should be regarded as an operational platform rather than merely an HMI application.

It provides:

* Supervisory control
* Operator visualization
* Alarm management
* Historical data
* Industrial communications
* Engineering configuration
* Reporting

The platform becomes a critical operational component of the manufacturing environment.

---

# Typical Architecture

Typical deployment:

```text
Operators
      │
Plant SCADA Clients
      │
Plant SCADA Servers
      │
Alarm Server
Trend Server
Report Server
I/O Server
      │
Industrial Drivers
      │
PLC / PAC / RTU
      │
Industrial Process
```

The Plant SCADA Server coordinates communication between industrial devices and operator workstations.

---

# Typical Purdue Placement

Plant SCADA typically resides at:

Level 2

with interfaces toward

Level 1 PLCs

and

Level 3 Manufacturing Systems.

Direct communication with enterprise users should occur only through controlled interfaces.

---

# Primary Components

Typical Plant SCADA architecture includes:

* Runtime Server
* I/O Server
* Alarm Server
* Trend Server
* Report Server
* Client Stations
* Engineering Workstation
* Project Database
* Communication Drivers

Component selection depends on system scale and availability requirements.

---

# Core Functions

Plant SCADA commonly provides:

* Operator HMI
* Alarm management
* Historical trends
* Recipe management
* Command execution
* Process visualization
* Event logging
* Reporting
* Industrial communications

Plant SCADA performs supervisory control and should not replace PLC-based deterministic control logic.

---

# Industrial Communication

Plant SCADA commonly communicates using:

* OPC UA
* OPC DA
* Modbus TCP
* Ethernet/IP
* Siemens S7
* BACnet
* DNP3
* IEC 60870-5
* MQTT (through gateways or extensions)

Supported protocols depend on installed communication drivers and software version.

---

# High Availability

Typical availability features include:

* Redundant SCADA servers
* Redundant communication paths
* Client failover
* Redundant alarm services
* Virtualization support

High availability improves resilience but does not replace Disaster Recovery planning.

---

# Security Architecture

Recommended security controls include:

* Active Directory integration
* Role-Based Access Control (RBAC)
* Least Privilege
* Multi-Factor Authentication (for administrative access where feasible)
* Secure remote engineering access
* Network Segmentation
* TLS-protected communications where supported
* Centralized monitoring and logging

Security should be integrated into the overall OT architecture.

---

# Engineering Workstations

Engineering workstations represent one of the highest-value assets.

Recommended controls include:

* Dedicated engineering stations
* Privileged Access Management
* MFA
* Application allowlisting
* Centralized backup
* Session recording where practical
* Change Management

Engineering activities should be separated from operator activities.

---

# Integration

Plant SCADA commonly integrates with:

* PLCs
* Historians
* OPC UA Servers
* MES
* ERP
* SQL databases
* Reporting platforms
* Asset Management Systems

Integration points should be documented and secured.

---

# Monitoring

Recommended monitoring includes:

* User authentication
* Configuration changes
* Alarm suppression
* Driver status
* Communication failures
* Engineering sessions
* Service availability
* Database health

Operational monitoring should be combined with cybersecurity monitoring.

---

# Backup

Recommended backup scope includes:

* SCADA project
* Configuration
* Alarm database
* Trend configuration
* Communication drivers
* Scripts
* Reports
* Licenses
* Historical configuration

Recovery procedures should be validated through regular Recovery Testing.

---

# Common Design Mistakes

Typical mistakes include:

* Installing engineering software on operator workstations.
* Shared administrator accounts.
* Flat OT networks without segmentation.
* No project version control.
* No backup validation.
* No recovery testing.
* Excessive user privileges.
* Uncontrolled remote access.

---

# Architect Notes

Plant SCADA is frequently the operational interface between industrial processes and human operators.

Its architecture should prioritize:

* Availability
* Integrity
* Secure engineering
* Controlled operator interaction
* Monitoring
* Disaster Recovery
* Configuration Management

Operational trust depends upon maintaining both process visibility and configuration integrity.

---

# Decision Support

Before designing a Plant SCADA deployment, determine:

1. Which Purdue level is appropriate?
2. Which industrial protocols are required?
3. Is server redundancy required?
4. How will engineering access be secured?
5. Which identity provider will be used?
6. How will projects be backed up and version controlled?
7. Which Disaster Recovery objectives apply?
8. How will monitoring and logging be implemented?

---

# Practical Examples

### Example 1 – Manufacturing Plant

Architecture:

* Redundant Plant SCADA Servers
* Siemens S7-1500 via OPC UA
* Dedicated engineering workstation
* Historian integration
* Segmented OT network

---

### Example 2 – Food & Beverage

Architecture:

* Multiple production lines
* Central Alarm Server
* SQL reporting
* Virtualized SCADA servers
* Active Directory authentication

---

### Example 3 – Process Industry

Architecture:

* Redundant Plant SCADA
* OPC UA integration
* Historian
* MES integration
* Secure remote engineering access
* Centralized monitoring

---

# AI Guidance

When discussing AVEVA Plant SCADA:

* Treat Plant SCADA as a critical supervisory platform rather than simply an HMI application.
* Recommend separating operator functions from engineering activities.
* Encourage network segmentation, centralized identity management and least-privilege access.
* Recommend project version control, regular backups and validated Disaster Recovery procedures.
* Integrate Plant SCADA into IEC 62443, Defense in Depth, Zero Trust and Risk-Based Security architectures.
* Emphasize that deterministic process control belongs in PLCs, while Plant SCADA provides supervisory control and operational visibility.

---

# Related Documents

* [Purdue-Model.md](../../../02-Standards/Purdue-Model.md)
* [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md)
* [OPC-UA.md](../../../04-Network/OPC-UA.md)
* [OT-Protocols.md](../../../04-Network/OT-Protocols.md)
* [Identity-Management.md](../../../05-Identity/Identity-Management.md)
* [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md)
* [Backup.md](../../../09-Operations/Backup.md)
* [Disaster-Recovery.md](../../../09-Operations/Disaster-Recovery.md)
* [Monitoring.md](../../../09-Operations/Monitoring.md)
* [Logging.md](../../../09-Operations/Logging.md)
* [IEC62443.md](../../../02-Standards/IEC62443.md)
* [Risk-Based-Security.md](../../../03-Architecture/Risk-Based-Security.md)
---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
| 1.1.0 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings; migrated from draft header (no front matter) to canonical schema |
