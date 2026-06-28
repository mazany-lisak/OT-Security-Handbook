# PROFINET

Version: 1.0
Status: Draft
Audience: OT Architects, Automation Engineers, Network Engineers, Security Architects, AI Knowledge Base

---

# Purpose

PROFINET is an industrial Ethernet communication standard developed by PROFIBUS & PROFINET International (PI) for deterministic communication between industrial automation devices.

It is one of the most widely deployed communication technologies in manufacturing automation, particularly within Siemens-based environments, but is also supported by many other vendors.

PROFINET is primarily designed for:

* deterministic communication,
* high availability,
* interoperability,
* real-time industrial control.

Cybersecurity was not the original design objective, although modern versions introduce additional security capabilities.

---

# Scope

This document focuses on PROFINET from an architectural and cybersecurity perspective.

It does not describe protocol implementation details intended for PLC programmers.

Typical systems include:

* PLCs
* Remote I/O
* Variable Frequency Drives (VFD)
* HMIs
* SCADA
* Industrial Switches
* Distributed Automation Systems

---

# Architecture Overview

A typical PROFINET architecture consists of:

PLC

↓

Industrial Ethernet Switch

↓

Remote I/O

↓

Drives

↓

Sensors

↓

Actuators

Communication is cyclic and deterministic.

Predictable timing is one of the primary design objectives.

---

# Communication Classes

PROFINET defines several communication classes.

## PROFINET NRT

Non-Real-Time communication.

Used for:

* diagnostics,
* engineering,
* configuration,
* file transfer.

---

## PROFINET RT

Real-Time communication.

Used for most industrial control applications.

Provides deterministic communication suitable for standard automation.

---

## PROFINET IRT

Isochronous Real-Time.

Designed for applications requiring extremely precise synchronization.

Typical examples:

* motion control,
* robotics,
* high-speed manufacturing.

IRT introduces additional infrastructure requirements.

---

# Network Characteristics

PROFINET networks are characterized by:

* cyclic communication,
* deterministic timing,
* multicast traffic,
* topology discovery,
* engineering traffic,
* diagnostic communication.

Architectural decisions should preserve these characteristics.

---

# Discovery Mechanisms

PROFINET uses DCP (Discovery and Configuration Protocol) for device identification and commissioning.

DCP simplifies engineering but introduces security considerations.

Architects should understand that DCP assumes trusted industrial networks.

Uncontrolled exposure of DCP beyond engineering environments should be avoided.

---

# Security Considerations

Legacy PROFINET deployments generally rely on network trust rather than protocol-level security.

Typical limitations include:

* limited authentication,
* limited encryption,
* trust-by-network-location,
* unrestricted discovery,
* limited integrity protection.

Modern architectures should compensate for these limitations.

---

# PROFINET Security Class 1

Modern PROFINET environments may support Security Class 1 concepts including:

* authenticated engineering communication,
* integrity protection,
* encrypted engineering sessions,
* improved device authentication.

Architects should verify actual device capabilities rather than assuming protocol support.

Mixed environments containing legacy devices remain common.

---

# Network Segmentation

PROFINET traffic should remain inside dedicated industrial control zones.

Recommended architecture:

Engineering Zone

↓

Industrial Firewall

↓

PLC Zone

↓

PROFINET Network

↓

Remote I/O

↓

Field Devices

Enterprise applications should never communicate directly with field devices.

---

# Switch Infrastructure

Industrial Ethernet switches play a critical role.

Architectural recommendations include:

* managed switches,
* VLAN support where appropriate,
* Quality of Service,
* redundant topology support,
* monitoring,
* firmware lifecycle management.

Switches should be considered critical industrial assets.

---

# Redundancy

High availability often requires redundant communication paths.

Common mechanisms include:

* Media Redundancy Protocol (MRP)
* Device redundancy
* Controller redundancy
* Ring topologies

Redundancy improves availability but also increases architectural complexity.

---

# Firewall Considerations

Firewalls should protect trust boundaries rather than individual PROFINET frames.

Recommended placement:

Enterprise

↓

Industrial DMZ

↓

Industrial Firewall

↓

Operations Network

↓

PROFINET Network

Deep packet inspection should be evaluated carefully to avoid introducing unacceptable latency.

---

# Monitoring

Monitoring should include:

* topology changes,
* new devices,
* DCP activity,
* engineering sessions,
* configuration downloads,
* firmware updates,
* controller failover,
* network utilization,
* multicast anomalies.

Unexpected engineering traffic should always be investigated.

---

# Typical Risks

Common risks include:

* unauthorized engineering access,
* rogue engineering stations,
* firmware tampering,
* uncontrolled discovery,
* flat industrial networks,
* unmanaged switches,
* obsolete firmware,
* undocumented communication paths.

These risks generally arise from architectural weaknesses rather than protocol flaws.

---

# Performance Considerations

Security controls should preserve deterministic communication.

Before introducing new security technologies evaluate:

* latency,
* jitter,
* controller scan time,
* synchronization,
* multicast behaviour,
* redundancy mechanisms.

Enterprise security appliances are not always suitable for industrial control networks.

---

# Relationship with IEC 62443

IEC 62443 recommends architectural controls that complement PROFINET deployments.

Typical examples include:

* Zones and Conduits
* Least Privilege
* Secure Remote Access
* Network Segmentation
* Monitoring
* Asset Management

The protocol itself does not provide complete security.

Security is achieved through architecture.

---

# Relationship with OPC UA

PROFINET and OPC UA serve different purposes.

PROFINET:

* deterministic control,
* cyclic process communication,
* field-level automation.

OPC UA:

* interoperability,
* information modelling,
* supervisory communication,
* enterprise integration.

The protocols complement rather than replace one another.

Typical architecture:

Field Level

↓

PROFINET

↓

PLC

↓

OPC UA Server

↓

SCADA

↓

Historian

↓

MES

---

# Architect Notes

PROFINET should be viewed primarily as a process-control protocol.

Do not expose PROFINET beyond the industrial control zone.

Whenever data is required outside the control network, prefer transferring information through higher-level services such as OPC UA rather than exposing field-level communication.

Maintaining deterministic communication is more important than introducing unnecessary security complexity.

---

# Decision Support

Before recommending PROFINET, determine:

1. Is deterministic communication required?
2. What is the required update interval?
3. Is motion control involved?
4. Which redundancy mechanisms are required?
5. Which devices support Security Class 1?
6. Where are the trust boundaries?
7. How will engineering access be controlled?
8. How will firmware be managed?

---

# AI Guidance

When discussing PROFINET:

* Explain that PROFINET is optimized for deterministic industrial communication rather than cybersecurity.
* Distinguish RT, IRT and NRT communication classes.
* Recommend network segmentation as the primary security control.
* Recommend Industrial DMZ and OPC UA gateways for enterprise integration.
* Avoid recommending direct IT-to-PROFINET communication.
* Explain that protocol security does not replace architectural security.
* Distinguish between protocol capabilities and device-specific implementations.

---

# Related Documents

* OT-Protocols.md
* OPC-UA.md
* Network-Segmentation.md
* Purdue-Model.md
* Industrial-DMZ.md
* Secure-Remote-Access.md
* IEC62443-Overview.md
* Siemens-S7-1500.md
* Identity-Management.md

---

# Revision History

| Version | Date    | Description     |
| ------- | ------- | --------------- |
| 1.0     | 2026-06 | Initial release |
