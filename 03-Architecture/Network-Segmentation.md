# Network Segmentation

Version: 1.0
Status: Draft
Audience: OT Architects, Network Architects, Security Architects, Automation Engineers, AI Knowledge Base

---

# Purpose

Network segmentation is one of the most effective architectural controls for reducing cyber risk in Industrial Control Systems (ICS) and Operational Technology (OT).

Its objective is **not** merely to divide networks into VLANs.

The primary objective is to reduce the probability and impact of security incidents by limiting trust relationships and controlling communication paths between systems.

Effective segmentation supports:

* Safety
* Availability
* Cyber resilience
* Incident containment
* Regulatory compliance
* Operational maintainability

---

# Scope

This document applies to:

* Industrial Control Systems (ICS)
* SCADA systems
* Distributed Control Systems (DCS)
* PLC networks
* Industrial Ethernet
* Industrial DMZ
* Engineering workstations
* Historians
* OPC UA servers
* Remote access solutions
* OT virtualization platforms

---

# Why Network Segmentation Matters

Flat industrial networks remain one of the most common architectural weaknesses.

In flat environments:

* every compromised device becomes a potential attack pivot,
* malware spreads rapidly,
* unauthorized discovery is easier,
* lateral movement requires little effort,
* incident containment becomes difficult.

Segmentation limits attacker movement rather than assuming attacks can always be prevented.

---

# Security Objectives

Network segmentation should:

* reduce attack surface,
* minimize trust relationships,
* isolate critical assets,
* protect safety functions,
* support monitoring,
* simplify firewall policies,
* improve recoverability.

Segmentation is a risk reduction mechanism—not a compliance exercise.

---

# Segmentation vs Isolation

These terms are often confused.

## Segmentation

Communication is permitted but explicitly controlled.

Typical examples:

* Firewall policies
* Industrial firewalls
* Layer 3 routing
* ACLs
* Industrial DMZ

---

## Isolation

Communication is intentionally prevented.

Typical examples:

* Air-gapped systems
* One-way gateways
* Temporary disconnected networks

Complete isolation is uncommon in modern OT environments.

---

# Defense in Depth

Network segmentation is only one layer.

It should operate together with:

* Identity Management
* Authentication
* Secure Remote Access
* Endpoint Hardening
* Monitoring
* Logging
* Backup Strategy
* Physical Security

Segmentation alone cannot stop credential theft or malicious insiders.

---

# Purdue Model

The Purdue Enterprise Reference Architecture remains a useful conceptual model for understanding industrial communication flows.

Typical layers include:

Level 0

Physical Process

---

Level 1

Sensors

Actuators

PLC I/O

---

Level 2

PLCs

RTUs

HMIs

SCADA Servers

---

Level 3

Operations

Engineering Workstations

Historian

Patch Management

Domain Services

---

Level 3.5

Industrial DMZ

---

Level 4

Enterprise IT

---

Level 5

External Services

Cloud

Internet

Partner Networks

The Purdue Model should guide communication analysis rather than dictate physical implementation.

---

# IEC 62443 Perspective

IEC 62443 introduces two fundamental concepts:

## Zones

Assets sharing similar security requirements.

Examples:

* PLC Zone
* Safety PLC Zone
* SCADA Zone
* Historian Zone
* Engineering Zone

---

## Conduits

Controlled communication paths between zones.

Examples:

* OPC UA
* HTTPS
* Syslog
* NTP
* RDP (where justified)
* SQL replication

Every conduit should have:

* defined owner,
* documented purpose,
* approved protocol,
* approved ports,
* authentication method,
* monitoring capability.

---

# Typical OT Segmentation

Example architecture:

Enterprise

↓

Corporate Firewall

↓

Industrial DMZ

↓

OT Firewall

↓

Operations Network

↓

Engineering Network

↓

PLC Network

↓

Safety Network

↓

Field Devices

Each transition represents an explicit trust boundary.

---

# Industrial DMZ

An Industrial DMZ is recommended whenever communication between Enterprise IT and OT cannot be avoided.

Typical services located inside the Industrial DMZ include:

* Historian replication
* OPC UA gateways
* Remote access gateways
* Patch repositories
* Antivirus update servers
* File transfer services
* Jump Servers

The DMZ should never become an alternative production network.

---

# VLANs Are Not Security

A common misconception is:

"We have VLANs, therefore we are segmented."

False.

VLANs provide logical separation.

Security requires:

* controlled routing,
* firewall enforcement,
* monitoring,
* authentication,
* least privilege.

Without policy enforcement, VLANs alone provide limited security.

---

# Secure Communication

Only explicitly required protocols should cross trust boundaries.

Every permitted protocol should be documented.

Typical protocols include:

* OPC UA
* HTTPS
* Syslog
* NTP
* SNMP (where justified)
* DNS
* RADIUS
* LDAP/LDAPS

Avoid unnecessary legacy protocols whenever practical.

---

# Remote Access

Vendor remote access should never terminate directly inside the PLC network.

Preferred architecture:

VPN

↓

MFA

↓

Jump Server

↓

Session Recording

↓

Engineering Workstation

↓

PLC

Every session should be attributable to an authenticated identity.

---

# Zero Trust in OT

Zero Trust principles are valuable but require adaptation.

Recommended concepts include:

* continuous identity verification,
* least privilege,
* authenticated communication,
* explicit trust boundaries,
* session auditing.

Avoid applying enterprise Zero Trust concepts without considering deterministic communication and operational constraints.

---

# Monitoring

Every trust boundary should generate security telemetry.

Recommended sources include:

* firewall logs,
* VPN logs,
* authentication logs,
* switch events,
* Windows events,
* Linux logs,
* industrial IDS,
* NetFlow where appropriate.

Monitoring validates that segmentation remains effective.

---

# Common Design Mistakes

Typical mistakes include:

* Flat OT networks
* Direct IT-to-PLC communication
* Shared administrator accounts
* Excessive firewall rules
* Unused open ports
* Vendor VPN terminating in production networks
* Undocumented communication flows
* Firewall policies based on "Any Any"
* Mixing safety and standard control traffic without justification

---

# Architect Notes

Segmentation should follow the industrial process—not organizational charts.

Many environments are segmented according to departments rather than operational functions.

Effective segmentation starts by understanding:

* the process,
* safety boundaries,
* operational dependencies,
* maintenance requirements,
* communication flows.

Technology is applied only after trust boundaries have been defined.

---

# Decision Support

Before designing segmentation, answer the following:

1. Which assets are safety-critical?
2. Which systems communicate continuously?
3. Which communication is truly required?
4. Which vendors require remote access?
5. Where should trust boundaries exist?
6. Which protocols cross those boundaries?
7. How will communication be monitored?
8. How will changes be governed?

If these questions cannot be answered, additional discovery should be performed before implementing segmentation.

---

# AI Guidance

When recommending network segmentation:

* Begin by understanding the industrial process.
* Identify trust boundaries before suggesting technologies.
* Do not recommend VLANs as the sole security control.
* Explain the difference between logical segmentation and security enforcement.
* Prefer IEC 62443 Zones and Conduits terminology where appropriate.
* Consider safety, availability and maintainability before recommending restrictive controls.
* Recommend an Industrial DMZ whenever sustained IT–OT communication is required.

---

# Related Documents

* OT-Security-Philosophy.md
* IEC62443-Overview.md
* Purdue-Model.md
* NIS2.md
* Czech-Cybersecurity-Act.md
* Secure-Remote-Access.md
* Identity-Management.md
* Architecture-Principles.md

---

# Revision History

| Version | Date    | Description     |
| ------- | ------- | --------------- |
| 1.0     | 2026-06 | Initial release |
