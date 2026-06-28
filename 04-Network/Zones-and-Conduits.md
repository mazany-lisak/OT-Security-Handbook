# Zones and Conduits

Version: 1.0
Status: Production Draft
Audience: OT Architects, Security Architects, Network Architects, Automation Engineers, AI Knowledge Base

---

# Purpose

The concepts of **Zones** and **Conduits** are fundamental architectural principles defined by the IEC 62443 family of standards.

They provide a structured method for designing secure Industrial Automation and Control Systems (IACS) by grouping assets with similar security requirements and explicitly controlling communication between them.

Zones and Conduits are **not** network technologies.

They are architectural concepts that support risk-based security.

---

# Scope

This document describes:

* Security Zones
* Conduits
* Trust Boundaries
* Communication Control
* Architectural Design
* Risk Reduction
* Typical Industrial Implementations

Vendor-specific implementation guidance is intentionally excluded.

---

# Why Zones and Conduits Exist

Historically, many industrial networks were built as flat Ethernet networks.

While operationally simple, flat architectures introduce several problems:

* unrestricted lateral movement,
* excessive trust,
* difficult monitoring,
* complex incident response,
* weak change management.

Zones and Conduits reduce these risks by introducing explicit trust boundaries.

---

# Security Philosophy

One of the most common misunderstandings is:

> "A Zone equals a VLAN."

This is incorrect.

A Zone is **not** defined by topology.

A Zone is defined by **security requirements**.

Devices belong to the same Zone when they share:

* similar risk profile,
* similar security objectives,
* similar operational requirements,
* common trust level,
* common ownership,
* similar lifecycle.

Network technologies merely implement the architectural decision.

---

# What is a Zone?

A Zone is a logical grouping of assets with comparable cybersecurity requirements.

A Zone may contain:

* PLCs
* HMIs
* Engineering Workstations
* Historians
* Domain Controllers
* Firewalls
* Remote I/O
* Network Infrastructure
* Safety Controllers

Membership is determined by security policy—not by physical location.

---

# Typical OT Zones

Examples include:

## Enterprise Zone

Business applications

Email

ERP

Office workstations

---

## Industrial DMZ

Jump Servers

Patch Management

Historian Replication

Proxy Services

Update Repositories

---

## Operations Zone

SCADA

Historians

Engineering Servers

License Servers

---

## Engineering Zone

Engineering Workstations

TIA Portal

Studio 5000

Vendor Tools

---

## Control Zone

PLCs

RTUs

Controllers

---

## Safety Zone

Safety PLCs

Safety Controllers

Emergency Shutdown Systems

---

## Field Zone

Remote I/O

Sensors

Actuators

Drives

---

# Trust Boundaries

Every Zone establishes a trust boundary.

Crossing a trust boundary should always require explicit authorization.

Typical controls include:

* Firewalls
* Authentication
* Certificates
* VPN
* Monitoring
* Logging
* Protocol Validation

Trust should never be assumed.

---

# What is a Conduit?

A Conduit is a controlled communication path between two Zones.

A Conduit defines:

* permitted communication,
* protocols,
* authentication,
* inspection,
* ownership,
* monitoring,
* security policy.

A Conduit is more than a firewall rule.

It represents an architectural contract.

---

# Conduit Design

Every Conduit should define:

* Source Zone
* Destination Zone
* Business justification
* Protocols
* Ports
* Authentication method
* Encryption requirements
* Monitoring requirements
* Owner
* Review process

Undocumented communication should be considered unauthorized.

---

# Example Conduit

Engineering Zone

↓

Industrial Firewall

↓

Control Zone

Permitted:

* S7 Communication
* HTTPS
* NTP

Denied:

* SMB
* RDP
* Internet Access

All engineering activity logged.

---

# Communication Principles

Communication between Zones should follow three principles:

### Explicit

Every communication flow must be documented.

---

### Minimal

Permit only what is operationally required.

---

### Verifiable

Communication should be monitored and auditable.

---

# Zone Design Principles

When creating Zones, consider:

* Safety
* Operational function
* Ownership
* Maintenance
* Criticality
* Lifecycle
* Vendor dependencies
* Availability requirements

Avoid creating Zones solely based on IP addressing.

---

# Common Zone Models

Typical architecture:

```text
Enterprise
        │
Industrial DMZ
        │
Operations
        │
Engineering
        │
Control
        │
Safety
        │
Field Devices
```

Actual implementations vary according to operational requirements.

---

# Relationship with Purdue Model

The Purdue Model describes functional levels.

Zones describe security groupings.

The two concepts complement each other.

Multiple Zones may exist within a single Purdue Level.

Likewise, one Zone may span multiple physical networks if justified by identical security requirements.

---

# Relationship with Network Segmentation

Network Segmentation implements Zones.

Zones define the architecture.

Segmentation implements the architecture.

The correct design sequence is:

Risk Assessment

↓

Zone Definition

↓

Conduit Definition

↓

Network Segmentation

↓

Firewall Policies

↓

Monitoring

Technology follows architecture—not the reverse.

---

# Monitoring

Every Conduit should generate security telemetry.

Recommended monitoring includes:

* Connection attempts
* Firewall events
* Authentication failures
* Certificate failures
* Protocol anomalies
* Configuration changes
* Unauthorized communication
* New endpoints

Monitoring validates that Conduits continue enforcing intended trust relationships.

---

# Common Design Mistakes

Typical mistakes include:

* Defining Zones by VLANs.
* Allowing unrestricted communication between Zones.
* Creating unnecessary Zones.
* Ignoring ownership.
* Missing documentation.
* Using "Any → Any" firewall rules.
* Mixing Safety and Control systems without justification.
* Failing to review Conduits after system changes.

---

# Relationship with IEC 62443

Zones and Conduits are central concepts within IEC 62443.

They support implementation of:

* Defense in Depth
* Least Privilege
* Secure Communications
* Risk Reduction
* Asset Management
* Security Levels

IEC 62443 intentionally avoids prescribing specific network technologies.

---

# Architect Notes

Experienced OT architects rarely begin with VLAN design.

Instead, they ask:

* Which assets share the same risk?
* Which systems trust each other?
* Which communication is truly necessary?
* Where should trust stop?
* Who owns each communication path?

Only after answering these questions should firewalls, VLANs or routing be designed.

---

# Decision Support

Before defining a new Zone, determine:

1. Which assets belong together from a security perspective?
2. Do they share similar operational requirements?
3. Who owns the assets?
4. What is the required Security Level?
5. Which systems require communication outside the Zone?
6. Can communication be reduced?
7. How will the Zone be monitored?
8. Who approves future communication changes?

---

# AI Guidance

When discussing Zones and Conduits:

* Explain that Zones are based on security requirements rather than IP addressing.
* Distinguish architectural concepts from implementation technologies.
* Recommend defining Zones before VLANs or firewall rules.
* Encourage explicit documentation of every Conduit.
* Treat undocumented communication as an architectural risk.
* Explain that Zones and Conduits support—but do not replace—risk assessment.
* Map recommendations to IEC 62443 terminology whenever appropriate.

---

# Related Documents

* OT-Security-Philosophy.md
* Risk-Based-Security.md
* Defense-in-Depth.md
* Network-Segmentation.md
* Purdue-Model.md
* Industrial-DMZ.md
* Firewall-Design.md
* Secure-Remote-Access.md
* IEC62443-Overview.md
* Identity-Management.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
