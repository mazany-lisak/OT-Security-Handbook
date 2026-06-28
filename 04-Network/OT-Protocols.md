# OT Protocols

Version: 1.0
Status: Draft
Audience: OT Architects, Network Architects, Security Engineers, Automation Engineers, AI Knowledge Base

---

# Purpose

Industrial communication protocols are the foundation of every Industrial Control System (ICS).

Unlike enterprise protocols, OT protocols were primarily designed to ensure deterministic communication, interoperability and operational reliability—not cybersecurity.

Understanding protocol characteristics is essential for:

* secure architecture design,
* network segmentation,
* firewall policy development,
* protocol inspection,
* monitoring,
* incident response,
* risk assessment.

This document provides an architectural overview of the most common OT protocols and their security implications.

---

# Scope

This document covers the following protocol families:

* OPC UA
* OPC DA
* OPC Classic
* PROFINET
* EtherNet/IP
* Modbus TCP
* Modbus RTU
* S7 Communication
* S7Comm Plus
* DNP3
* IEC 60870-5-104
* IEC 61850
* MQTT
* SNMP
* NTP
* Syslog
* HTTPS
* RADIUS
* LDAP / LDAPS

Vendor-specific implementation details are intentionally excluded and documented separately.

---

# Design Principles

Protocol selection should always support:

* operational requirements,
* deterministic communication,
* interoperability,
* maintainability,
* long lifecycle,
* vendor support,
* cybersecurity.

Choosing the newest protocol is not automatically the best architectural decision.

---

# Security Principles

Industrial protocols should be evaluated according to:

* authentication
* authorization
* encryption
* integrity protection
* replay protection
* protocol inspection support
* logging capability
* monitoring capability
* vendor support
* lifecycle maturity

Not every protocol provides all security features.

Compensating controls may therefore be required.

---

# Protocol Categories

Protocols generally belong to one of the following categories.

## Process Communication

Examples:

* PROFINET
* EtherNet/IP
* Modbus TCP
* Modbus RTU
* S7 Communication

These protocols directly influence industrial processes.

Availability is usually the highest priority.

---

## Supervisory Communication

Examples:

* OPC UA
* OPC DA
* Historian interfaces

These protocols provide operational visibility.

They should not directly control safety functions.

---

## Infrastructure Services

Examples:

* DNS
* NTP
* Syslog
* SNMP
* RADIUS
* LDAP

Although not process protocols, these services are essential for secure OT operation.

---

## Remote Management

Examples:

* HTTPS
* SSH
* RDP
* VPN

These protocols require particularly strong identity controls.

---

# Protocol Overview

| Protocol        | Typical Use                        | Native Security           | Recommendation                   |
| --------------- | ---------------------------------- | ------------------------- | -------------------------------- |
| OPC UA          | Secure industrial interoperability | High                      | Preferred for new integrations   |
| OPC DA          | Legacy Windows interoperability    | Low                       | Migrate when practical           |
| PROFINET        | PLC and I/O communication          | Limited                   | Protect through segmentation     |
| Modbus TCP      | Simple industrial communication    | None                      | Use compensating controls        |
| S7Comm          | Siemens PLC communication          | Limited                   | Restrict to engineering zones    |
| S7Comm Plus     | Newer Siemens communication        | Improved                  | Preferred over legacy S7Comm     |
| EtherNet/IP     | Industrial control                 | Limited                   | Segment and monitor              |
| MQTT            | Telemetry and IIoT                 | Depends on implementation | Use TLS and authentication       |
| IEC 60870-5-104 | Energy automation                  | Limited                   | Protect through network controls |
| IEC 61850       | Electrical substations             | Moderate                  | Segment carefully                |

---

# Legacy Protocols

Many industrial protocols were designed before cybersecurity became a primary concern.

Typical characteristics include:

* no encryption,
* no authentication,
* broadcast discovery,
* trust-by-network-location.

These limitations should not automatically require replacement.

Instead, architects should evaluate compensating controls.

---

# Compensating Controls

Where protocol security is insufficient, consider:

* network segmentation,
* Industrial DMZ,
* firewall policies,
* protocol allowlists,
* jump servers,
* secure remote access,
* monitoring,
* IDS,
* strict change management.

Architecture should compensate for protocol limitations.

---

# Protocol Placement

Protocols should be placed according to operational purpose.

Example:

PLC Communication

↓

Control Network

↓

No direct Enterprise access

---

Historian Replication

↓

Industrial DMZ

↓

Enterprise

---

Remote Engineering

↓

VPN

↓

Jump Server

↓

Engineering Workstation

↓

PLC

Communication paths should remain explicit and documented.

---

# Protocol Inspection

Where supported, industrial firewalls should inspect protocol semantics rather than only TCP/UDP ports.

Examples include:

* OPC UA inspection
* Modbus inspection
* EtherNet/IP inspection
* PROFINET awareness
* DNP3 inspection

Deep packet inspection improves visibility but should be evaluated for performance impact.

---

# Monitoring

Security monitoring should include:

* protocol anomalies,
* unauthorized clients,
* unexpected services,
* unusual communication frequency,
* firmware uploads,
* engineering sessions,
* configuration downloads,
* authentication failures.

Operational baselines significantly improve anomaly detection.

---

# Common Design Mistakes

Typical mistakes include:

* Allowing any protocol between IT and OT.
* Using OPC DA for new deployments.
* Exposing PLC protocols directly to enterprise networks.
* Assuming VLAN separation provides protocol security.
* Leaving unused protocol services enabled.
* Ignoring engineering workstation traffic.
* Allowing unrestricted vendor engineering access.
* Monitoring only IP addresses rather than industrial protocols.

---

# Protocol Selection Strategy

When selecting an industrial protocol, consider:

1. Vendor support.
2. Lifecycle.
3. Security capabilities.
4. Deterministic behaviour.
5. Operational requirements.
6. Performance.
7. Future interoperability.
8. Monitoring capabilities.

Security should influence protocol selection but should not compromise operational requirements.

---

# Architect Notes

Protocol selection is rarely an isolated technical decision.

It affects:

* network architecture,
* firewall design,
* monitoring,
* identity management,
* maintenance,
* incident response,
* future modernization.

Whenever possible, prefer protocols supporting:

* authentication,
* encryption,
* certificate-based trust,
* standardized interoperability.

However, replacing a stable industrial protocol solely for cybersecurity reasons should always be evaluated against operational risk.

---

# Decision Support

Before recommending an industrial protocol, determine:

1. What is the operational purpose?
2. Does the protocol require deterministic communication?
3. Which vendor supports the protocol?
4. Is interoperability required?
5. Are encryption and authentication available?
6. Can the protocol be inspected?
7. Which trust boundaries will it cross?
8. How will it be monitored?

---

# AI Guidance

When recommending industrial protocols:

* Begin with the operational requirement, not the protocol.
* Explain security strengths and weaknesses.
* Recommend compensating controls where protocol security is limited.
* Prefer open, well-supported and secure protocols for new designs.
* Never recommend exposing PLC protocols directly to enterprise networks or the Internet.
* Distinguish protocol capabilities from vendor-specific implementations.

---

# Related Documents

* Network-Segmentation.md
* Purdue-Model.md
* Industrial-DMZ.md
* Secure-Remote-Access.md
* IEC62443-Overview.md
* OPC-UA.md
* PROFINET.md
* Modbus-TCP.md
* S7-Communication.md
* Identity-Management.md

---

# Revision History

| Version | Date    | Description     |
| ------- | ------- | --------------- |
| 1.0     | 2026-06 | Initial release |
