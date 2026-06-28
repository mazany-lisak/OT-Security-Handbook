# S7 Communication

Version: 1.0
Status: Draft
Audience: OT Architects, Automation Engineers, Security Architects, Network Engineers, AI Knowledge Base

---

# Purpose

S7 Communication is Siemens' proprietary communication protocol family used for engineering, diagnostics, programming and data exchange between Siemens automation components.

The protocol family has evolved over several generations of Siemens PLCs and includes both legacy implementations (commonly referred to as S7Comm) and newer secure variants such as S7Comm Plus.

S7 Communication remains one of the most important industrial protocols encountered in modern OT environments.

Understanding its architectural role is essential when designing secure Siemens-based automation systems.

---

# Scope

This document focuses on the communication protocol itself.

Vendor-specific PLC configuration, programming and TIA Portal configuration are described in separate documents.

Typical use cases include:

* PLC programming
* Online diagnostics
* Variable monitoring
* HMI communication
* SCADA communication
* PLC-to-PLC communication
* Engineering stations
* Maintenance activities

---

# Architectural Role

Unlike OPC UA, S7 Communication was never intended to provide vendor-independent interoperability.

Its primary objective is communication inside Siemens automation environments.

Typical architecture:

Engineering Station

↓

S7 Communication

↓

PLC

↓

PROFINET

↓

Remote I/O

The protocol should therefore be considered an engineering and automation protocol rather than an enterprise integration protocol.

---

# Communication Types

Typical communication scenarios include:

Engineering

↓

PLC Programming

---

HMI

↓

PLC Variables

---

SCADA

↓

PLC Data Collection

---

PLC

↓

PLC Data Exchange

---

Diagnostics

↓

Device Status

↓

Firmware Information

Each communication type has different security implications.

---

# Legacy S7 Communication (S7Comm)

Legacy S7Comm implementations were designed when industrial networks were assumed to be trusted.

Typical characteristics include:

* minimal authentication,
* no native encryption,
* engineering-focused communication,
* extensive diagnostic capabilities.

Consequently, architectural protection is required.

---

# S7Comm Plus

Newer Siemens platforms introduced S7Comm Plus, providing improved security capabilities.

Typical improvements include:

* enhanced authentication,
* improved integrity protection,
* stronger engineering communication,
* tighter integration with modern CPU security features.

Support depends on PLC generation, firmware version and engineering software.

Architects should always verify actual capabilities rather than relying on product family names.

---

# Relationship with PROFINET

S7 Communication and PROFINET are frequently confused.

They serve different purposes.

PROFINET

* deterministic process communication,
* controller ↔ field devices.

S7 Communication

* engineering,
* diagnostics,
* programming,
* controller services.

Most Siemens installations require both protocols.

---

# Relationship with OPC UA

OPC UA should generally be preferred for enterprise integration.

S7 Communication should remain inside engineering and automation zones.

Recommended architecture:

Enterprise

↓

OPC UA

↓

SCADA

↓

Engineering Zone

↓

S7 Communication

↓

PLC

Avoid exposing S7 Communication beyond trusted OT networks.

---

# Authentication

Legacy implementations provide limited authentication.

Modern Siemens platforms introduce stronger identity verification, depending on CPU capabilities and configured security options.

Authentication should never rely solely on network location.

Engineering access should be explicitly controlled.

---

# Encryption

Encryption support depends on:

* PLC generation,
* firmware,
* engineering software,
* configured security features.

Architects should verify:

* communication protection,
* project protection,
* access protection,
* certificate usage,
* CPU security configuration.

Encryption alone does not eliminate the need for segmentation.

---

# Network Placement

S7 Communication should remain within dedicated engineering zones.

Recommended architecture:

Engineering Workstation

↓

Industrial Firewall

↓

Engineering VLAN

↓

PLC

↓

PROFINET

↓

Field Devices

Direct enterprise access should not be permitted.

---

# Firewall Considerations

Industrial firewalls should restrict S7 Communication to explicitly authorized engineering systems.

Recommended controls include:

* source restrictions,
* destination restrictions,
* maintenance windows,
* logging,
* session monitoring.

Engineering communication should never be permanently unrestricted.

---

# Engineering Workstations

Engineering workstations represent one of the highest-value attack targets.

Recommended controls include:

* dedicated engineering VLAN,
* privileged access management,
* application allowlisting,
* endpoint protection,
* controlled Internet access,
* session auditing.

Protecting engineering stations often provides greater security benefit than protecting individual PLCs.

---

# Monitoring

Security monitoring should include:

* programming sessions,
* CPU mode changes,
* firmware updates,
* online downloads,
* engineering logins,
* unexpected engineering stations,
* project uploads,
* configuration changes.

Programming activity outside approved maintenance windows should always be investigated.

---

# Common Design Mistakes

Typical mistakes include:

* allowing unrestricted engineering access,
* exposing S7 Communication to enterprise networks,
* shared engineering accounts,
* unmanaged engineering laptops,
* permanent vendor VPN access,
* outdated firmware,
* undocumented engineering stations,
* assuming S7Comm Plus alone provides sufficient security.

---

# Security Considerations

The protocol itself should not be considered the primary security boundary.

Instead, security should rely on:

* network segmentation,
* identity management,
* engineering workstation protection,
* secure remote access,
* monitoring,
* firmware management,
* change management.

Protocol security complements—but does not replace—architectural controls.

---

# Relationship with IEC 62443

IEC 62443 supports protecting S7 Communication through:

* Zones and Conduits,
* Least Privilege,
* Secure Engineering,
* Asset Management,
* Monitoring,
* Authentication,
* Secure Remote Access.

These controls are generally more effective than relying solely on protocol capabilities.

---

# Relationship with Siemens Security Features

Modern Siemens CPUs introduce additional security mechanisms including:

* access protection,
* know-how protection,
* communication protection,
* project protection,
* secure boot (platform dependent),
* certificate-based services,
* OPC UA security.

These mechanisms should be considered complementary layers rather than replacements for network security.

---

# Architect Notes

One of the most common architectural mistakes is treating engineering communication as ordinary application traffic.

Engineering traffic is administrative traffic.

Administrative communication always requires stronger controls than operational communication.

Whenever possible:

* isolate engineering stations,
* restrict programming access,
* document every engineering connection,
* monitor all downloads,
* separate engineering from process communication.

---

# Decision Support

Before allowing S7 Communication across a network boundary, determine:

1. Is engineering communication actually required?
2. Which engineering stations are authorized?
3. Which PLC models are involved?
4. Which firmware versions are installed?
5. Which security features are enabled?
6. Can communication remain inside the engineering zone?
7. Will vendor access be required?
8. How will programming activity be monitored?

---

# AI Guidance

When discussing S7 Communication:

* Explain that it is an engineering protocol rather than an enterprise integration protocol.
* Distinguish S7 Communication from PROFINET and OPC UA.
* Recommend keeping S7 Communication inside engineering networks.
* Do not recommend exposing S7 Communication directly to enterprise systems or the Internet.
* Treat engineering workstations as critical security assets.
* Explain that protocol improvements in newer PLC generations do not remove the need for segmentation, monitoring and identity management.

---

# Related Documents

* OT-Protocols.md
* PROFINET.md
* OPC-UA.md
* Network-Segmentation.md
* Industrial-DMZ.md
* Identity-Management.md
* Secure-Remote-Access.md
* Siemens-S7-1500.md
* TIA-Portal.md
* IEC62443-Overview.md

---

# Revision History

| Version | Date    | Description     |
| ------- | ------- | --------------- |
| 1.0     | 2026-06 | Initial release |
