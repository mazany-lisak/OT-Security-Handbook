# OPC UA (Open Platform Communications Unified Architecture)

Version: 1.0
Status: Draft
Audience: OT Architects, Automation Engineers, Security Architects, System Integrators, AI Knowledge Base

---

# Purpose

OPC UA (Open Platform Communications Unified Architecture) is a platform-independent industrial communication standard designed to enable secure, reliable and interoperable communication between industrial systems.

Unlike OPC Classic (OPC DA, HDA and A&E), OPC UA was designed with cybersecurity, interoperability and scalability as core architectural principles.

For modern OT architectures, OPC UA is generally the preferred communication protocol for integrating PLCs, SCADA systems, historians, MES platforms and enterprise applications.

---

# Scope

This document focuses on OPC UA from an architectural and security perspective.

Vendor-specific implementation details are intentionally excluded.

Typical implementations include:

* PLC communication
* SCADA integration
* Historian integration
* MES integration
* ERP integration
* IIoT
* Edge Computing
* Cloud gateways

---

# Why OPC UA Exists

The original OPC Classic family depended heavily on Microsoft COM/DCOM technology.

This created several limitations:

* Windows dependency
* Complex firewall configuration
* Poor interoperability
* Difficult remote communication
* Limited security
* Difficult certificate management

OPC UA was designed to remove these limitations while introducing modern security concepts.

---

# Core Design Principles

OPC UA was designed around five architectural goals:

* Platform independence
* Interoperability
* Information modelling
* Scalability
* Security by Design

Unlike many industrial protocols, security is an integral part of the protocol rather than an optional extension.

---

# Architecture

Typical OPC UA architecture consists of:

Client

↓

Secure Channel

↓

Session

↓

Subscription

↓

Server

Each layer provides a specific function.

Understanding this layered architecture is essential when troubleshooting connectivity or designing secure communication.

---

# Communication Model

OPC UA supports multiple communication patterns.

Typical examples include:

### Client / Server

Traditional industrial communication.

Most common deployment model.

---

### Publish / Subscribe (PubSub)

Designed for scalable event-driven architectures.

Frequently used in IIoT and distributed systems.

---

### Discovery Services

Allow automatic discovery of available OPC UA servers.

Discovery should be carefully controlled within production environments.

---

# Information Model

One of the greatest strengths of OPC UA is its rich information model.

Instead of exposing only registers or memory addresses, OPC UA exposes structured objects including:

* Variables
* Methods
* Objects
* Events
* Data Types
* References

This allows applications from different vendors to understand data semantics rather than only raw values.

---

# Security Model

OPC UA includes built-in security mechanisms.

These include:

* X.509 certificates
* Mutual authentication
* Message signing
* Encryption
* Integrity protection
* User authentication
* Role-based authorization

Unlike legacy protocols, security is part of the protocol architecture.

---

# Certificates

Certificates establish trust between OPC UA applications.

Every application instance typically owns its own certificate.

Trust relationships should be explicitly managed.

Typical lifecycle:

Generate Certificate

↓

Distribute

↓

Trust

↓

Monitor Expiration

↓

Renew

↓

Revoke if Compromised

Certificate lifecycle management is frequently underestimated.

---

# Authentication

OPC UA supports several authentication mechanisms.

Typical methods include:

* Anonymous
* Username / Password
* X.509 Certificate
* Kerberos (vendor dependent)

Anonymous access should rarely be used in production systems.

---

# Security Policies

Security policies define the cryptographic algorithms used by OPC UA.

Architects should prefer modern, vendor-supported security policies and disable deprecated options whenever operationally possible.

Security policies should be reviewed whenever firmware or software is upgraded.

---

# Network Placement

OPC UA should never be viewed as a justification for unrestricted connectivity.

Recommended communication path:

PLC

↓

OPC UA Server

↓

Industrial DMZ

↓

Historian

↓

Enterprise

Trust boundaries remain necessary even when communication is encrypted.

---

# Firewall Considerations

Firewall rules should permit only required communication.

Recommended practices include:

* Explicit allow rules
* Protocol documentation
* Source restriction
* Destination restriction
* Logging
* Change control

Avoid broad "allow any" rules.

---

# Monitoring

Security monitoring should include:

* Failed certificate validation
* Certificate expiration
* Authentication failures
* Unexpected clients
* New server instances
* Security policy changes
* Session creation
* Subscription anomalies

Monitoring should verify trust relationships rather than only connectivity.

---

# Common Deployment Models

Typical architectures include:

PLC

↓

Embedded OPC UA Server

↓

SCADA

---

PLC

↓

Gateway

↓

Industrial DMZ

↓

Enterprise

---

Multiple PLCs

↓

Aggregation Server

↓

Historian

↓

MES

↓

ERP

Each architecture has different security requirements.

---

# Common Design Mistakes

Typical mistakes include:

* Accepting all certificates permanently.
* Never renewing certificates.
* Using Anonymous authentication.
* Leaving deprecated security policies enabled.
* Placing OPC UA servers directly in enterprise networks.
* Ignoring certificate revocation.
* Sharing application certificates between systems.
* Treating encryption as a replacement for segmentation.

---

# Performance Considerations

Encryption introduces computational overhead.

When designing large-scale systems consider:

* CPU capacity
* Subscription rates
* Update intervals
* Network latency
* Certificate validation
* Redundancy

Security should be balanced with operational performance.

---

# Relationship with IEC 62443

OPC UA naturally supports several IEC 62443 principles including:

* Authentication
* Integrity
* Confidentiality
* Least Privilege
* Secure Communications

However, OPC UA alone does not provide complete compliance.

Architectural controls remain necessary.

---

# Architect Notes

OPC UA should not be selected simply because it is "the modern protocol."

It should be selected because it provides:

* interoperable information models,
* standardized security,
* long-term maintainability,
* vendor independence,
* scalable integration.

Poor certificate management is currently one of the most common causes of insecure OPC UA deployments.

Successful OPC UA projects are usually PKI projects as much as communication projects.

---

# Decision Support

Before recommending OPC UA, determine:

1. Which systems must exchange data?
2. Who owns the OPC UA server?
3. Who owns the certificates?
4. Will a PKI be implemented?
5. Which trust boundaries will communication cross?
6. Which authentication methods are supported?
7. Is high availability required?
8. How will certificate renewal be managed?

---

# AI Guidance

When recommending OPC UA:

* Explain why OPC UA is preferred over OPC Classic for new deployments.
* Treat certificate management as a first-class architectural topic.
* Recommend secure authentication methods.
* Avoid Anonymous authentication unless explicitly justified.
* Recommend segmentation even when communication is encrypted.
* Distinguish protocol capabilities from vendor-specific implementations.
* Explain that secure communication does not eliminate the need for governance, monitoring or lifecycle management.

---

# Related Documents

* OT-Protocols.md
* Network-Segmentation.md
* Industrial-DMZ.md
* Identity-Management.md
* PKI.md
* Certificates.md
* IEC62443-Overview.md
* Siemens-S7-1500.md
* GeoSCADA.md
* PlantSCADA.md

---

# Revision History

| Version | Date    | Description     |
| ------- | ------- | --------------- |
| 1.0     | 2026-06 | Initial release |
