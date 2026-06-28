# Firewall Design

Version: 1.0
Status: Production Draft
Audience: OT Architects, Network Architects, Security Architects, Automation Engineers, AI Knowledge Base

---

# Purpose

Industrial firewalls are one of the primary technical controls used to enforce communication policies between security zones in Industrial Automation and Control Systems (IACS).

A firewall should **never** be considered the architecture itself.

Instead, it is the technical implementation of architectural decisions made during:

* Risk Assessment
* Zone Definition
* Conduit Definition
* Security Policy Design

Well-designed firewall policies reduce attack surface while preserving deterministic industrial communication.

---

# Scope

This document describes:

* Firewall architecture
* Industrial firewall placement
* Rule design
* Policy management
* Deep Packet Inspection (DPI)
* Logging
* Monitoring
* Operational considerations

Vendor-specific configuration is intentionally excluded.

---

# Security Philosophy

The most common misconception is:

> "Installing a firewall makes the network secure."

This is incorrect.

A firewall enforces a communication policy.

If the policy is incorrect, incomplete or overly permissive, the firewall simply enforces a poor architecture.

Architecture precedes technology.

---

# Firewall Objectives

Industrial firewalls should:

* enforce trust boundaries,
* reduce attack surface,
* prevent unauthorized communication,
* support monitoring,
* provide auditability,
* simplify incident response,
* support least privilege.

Firewalls should not become a substitute for proper network architecture.

---

# Relationship with Zones and Conduits

Firewalls enforce Conduits.

They do not define them.

The design sequence should always be:

Risk Assessment

↓

Security Zones

↓

Conduits

↓

Firewall Policies

↓

Monitoring

↓

Continuous Review

Designing firewall rules before defining Conduits usually results in inconsistent security.

---

# Typical Firewall Locations

Common deployment points include:

## Enterprise ↔ Industrial DMZ

Protects business networks from industrial environments.

---

## Industrial DMZ ↔ Operations Network

Protects operational systems from enterprise services.

---

## Engineering Zone ↔ Control Zone

Restricts engineering communication.

---

## Vendor Access Gateway

Controls remote maintenance sessions.

---

## Safety Zone ↔ Control Zone

Protects safety systems from unintended communication.

---

## External Connectivity

Protects Internet-facing services.

Internet connectivity should never terminate directly inside production control networks.

---

# Rule Design Principles

Firewall rules should be:

* explicit,
* documented,
* justified,
* minimal,
* reviewable,
* auditable.

Avoid generic rules.

Every rule should have a documented operational purpose.

---

# Least Privilege

Permit only:

* required source,
* required destination,
* required protocol,
* required port,
* required time,
* required user where applicable.

Everything else should be denied.

---

# Default Deny

Industrial firewalls should generally follow a "default deny" policy.

Communication should be explicitly approved rather than implicitly permitted.

Exceptions should be documented and periodically reviewed.

---

# Industrial Protocol Awareness

Modern industrial firewalls support protocol-aware inspection.

Typical protocols include:

* OPC UA
* Modbus TCP
* PROFINET
* EtherNet/IP
* DNP3
* S7 Communication

Protocol awareness provides significantly greater visibility than Layer 3 or Layer 4 filtering alone.

---

# Deep Packet Inspection (DPI)

DPI allows inspection of industrial protocol semantics.

Typical capabilities include:

* Function code validation
* Protocol anomaly detection
* Unauthorized write requests
* Unexpected engineering operations
* Protocol compliance verification

DPI improves detection but should be evaluated for latency and device compatibility.

---

# Logging

Every firewall should generate security logs.

Recommended events include:

* Allowed connections
* Denied connections
* Rule changes
* Administrative logins
* Firmware updates
* VPN sessions
* Certificate failures
* High-severity protocol events

Logs should be forwarded to a centralized logging or SIEM platform where appropriate.

---

# Monitoring

Recommended monitoring includes:

* Policy violations
* Unexpected communication
* New endpoints
* Configuration drift
* High connection rates
* Failed VPN authentication
* Rule modifications
* Threat detection alerts

Monitoring validates that firewall policies continue to reflect operational reality.

---

# High Availability

Firewall availability is often as important as security.

Consider:

* Redundant firewall pairs
* Automatic failover
* State synchronization
* Maintenance procedures
* Configuration backup
* Tested recovery plans

Availability requirements should be defined during architecture design.

---

# Change Management

Firewall rules should follow formal change management.

Each change should include:

* Business justification
* Risk assessment
* Testing
* Approval
* Documentation
* Rollback plan

Temporary rules should have defined expiration dates.

---

# Performance Considerations

Industrial environments may require:

* Low latency
* Deterministic communication
* High throughput
* Redundant paths

Firewall performance should be validated under realistic operational conditions.

Security controls should not compromise process safety.

---

# Common Design Mistakes

Typical mistakes include:

* Any → Any rules
* Excessive rule complexity
* Undocumented exceptions
* No periodic rule review
* Firewall placement inside flat networks without segmentation
* Permanent vendor access
* Missing logging
* Mixing IT and OT policy models without adaptation
* Using consumer-grade firewalls in critical industrial environments

---

# Firewall Policy Lifecycle

Recommended lifecycle:

```text
Business Requirement
        ↓
Risk Assessment
        ↓
Zone Definition
        ↓
Conduit Definition
        ↓
Firewall Rule Design
        ↓
Testing
        ↓
Deployment
        ↓
Monitoring
        ↓
Periodic Review
        ↓
Removal (if no longer required)
```

Firewall policies should evolve with the industrial process.

---

# Relationship with IEC 62443

Firewall deployment supports several IEC 62443 concepts:

* Zones and Conduits
* Least Privilege
* Defense in Depth
* Secure Communications
* Monitoring
* Access Control

IEC 62443 intentionally avoids prescribing specific firewall vendors or products.

---

# Architect Notes

One of the most common architectural failures is treating the firewall as the primary security control.

In mature OT architectures, the firewall is only one layer among many.

Successful architectures rely equally on:

* Network Segmentation
* Identity Management
* Secure Remote Access
* Asset Management
* Monitoring
* Logging
* Change Management

The firewall enforces architecture—it does not create it.

---

# Decision Support

Before introducing a firewall, determine:

1. Which trust boundary requires protection?
2. Which communication is operationally required?
3. Can communication be reduced?
4. Which industrial protocols will cross the boundary?
5. Is DPI required and supported?
6. What availability requirements exist?
7. How will policy changes be approved?
8. How will the firewall be monitored?

---

# AI Guidance

When recommending industrial firewalls:

* Begin with Zones and Conduits rather than firewall rules.
* Recommend explicit allow rules and a default-deny philosophy.
* Explain the difference between Layer 3/4 filtering and industrial DPI.
* Consider operational constraints such as latency and availability.
* Treat firewall rules as documentation of business requirements, not just network configuration.
* Recommend periodic policy reviews and configuration backups.
* Distinguish between enterprise firewall practices and OT-specific requirements.

---

# Related Documents

* OT-Security-Philosophy.md
* Risk-Based-Security.md
* Defense-in-Depth.md
* Zones-and-Conduits.md
* Network-Segmentation.md
* Industrial-DMZ.md
* Secure-Remote-Access.md
* OT-Protocols.md
* Identity-Management.md
* Monitoring.md
* Logging.md
* IEC62443-Overview.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
