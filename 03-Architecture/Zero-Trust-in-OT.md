---
title: Zero Trust in OT
category: Architecture
version: 1.1.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-07-01
review_cycle: Annual
---

# Purpose

Zero Trust is a security strategy based on the principle that trust should never be assumed and must always be explicitly established, continuously evaluated and appropriately limited.

Within Operational Technology (OT), Zero Trust should not be interpreted as a direct copy of enterprise IT implementations.

Instead, Zero Trust must be adapted to industrial environments where safety, availability and deterministic operation remain primary objectives.

The purpose of this document is to explain how Zero Trust principles can be realistically and safely applied within Industrial Automation and Control Systems (IACS).

> **Single source of truth.** This document is authoritative for Zero Trust in OT across the handbook. Other documents (e.g. [Network-Segmentation.md](Network-Segmentation.md)) reference Zero Trust briefly and delegate the detail here. Zero Trust is one of the three complementary architectural strategies alongside [Defense-in-Depth.md](Defense-in-Depth.md) and [Risk-Based-Security.md](Risk-Based-Security.md), and it realises several principles in [OT-Architecture-Principles.md](OT-Architecture-Principles.md) (notably *Least Privilege* and *Identity Before Connectivity*).

---

# Scope

This document describes:

* Zero Trust principles
* OT-specific adaptations
* Identity-centric security
* Continuous verification
* Trust boundaries
* Architectural implementation
* Relationship with IEC 62443
* Relationship with Defense in Depth

Vendor-specific technologies are intentionally excluded.

---

# Security Philosophy

Traditional industrial networks often assume:

> "Everything inside the OT network is trusted."

Modern industrial environments invalidate this assumption.

Industrial systems increasingly include:

* Remote maintenance
* Cloud connectivity
* IIoT devices
* Mobile engineering laptops
* Third-party vendors
* Virtualized infrastructure
* Enterprise integration

Trust should therefore become explicit rather than implicit.

---

# What Zero Trust Is

Zero Trust is an architectural strategy.

It is based on several principles:

* Never trust implicitly.
* Verify explicitly.
* Grant minimum required access.
* Continuously evaluate trust.
* Assume compromise is possible.

Zero Trust is not a single technology or product.

---

# What Zero Trust Is Not

Zero Trust does **not** mean:

* Blocking all communication.
* Eliminating trust entirely.
* Preventing all attacks.
* Replacing network segmentation.
* Replacing Defense in Depth.

Instead, it improves how trust decisions are made.

---

# Why Zero Trust Matters in OT

Modern OT environments increasingly connect:

* Enterprise systems
* Cloud services
* Historians
* MES
* Remote vendors
* OEM support
* Mobile engineers

Traditional perimeter security becomes less effective as trust boundaries expand.

Zero Trust helps reduce excessive trust relationships.

---

# Core Principles

Recommended Zero Trust principles include:

* Explicit identity
* Least Privilege
* Continuous verification
* Segmentation
* Secure communications
* Monitoring
* Device trust
* Session validation

No access should be granted solely because a device resides on a trusted network.

---

# Identity First

Identity becomes the primary security boundary.

Examples include:

* User identity
* Device identity
* Application identity
* Service identity
* Certificate identity

Identity should be verified before access is granted.

---

# Device Trust

Devices should not automatically inherit trust.

Architects should consider:

* Firmware version
* Security configuration
* Ownership
* Compliance status
* Certificate validity
* Asset inventory

Compromised devices should not retain unrestricted access.

---

# Continuous Verification

Trust should not be established once and assumed indefinitely.

Verification may include:

* Session monitoring
* Certificate validation
* Device health
* Identity changes
* Authentication events
* Policy evaluation

Trust should evolve with operational context.

---

# Least Privilege

Every identity should receive only the permissions required to perform its function.

Examples include:

* Read-only historian access
* Temporary engineering access
* Time-limited vendor access
* Role-based administration
* Protocol-specific permissions

Permissions should be periodically reviewed.

---

# Network Architecture

Zero Trust complements—not replaces—network segmentation.

Recommended architecture:

```text
Enterprise
        │
Identity Verification
        │
Industrial DMZ
        │
Engineering Zone
        │
Control Zone
        │
Field Devices
```

Identity controls should exist at every trust boundary. Segmentation implementation is covered in [Network-Segmentation.md](Network-Segmentation.md); the zone/conduit model is in [IEC62443.md](IEC62443.md).

---

# Secure Communications

Where supported, communications should use:

* TLS
* X.509 Certificates
* Mutual Authentication
* Secure VPN
* Modern authentication protocols

Legacy protocols should be protected through compensating architectural controls.

---

# Engineering Workstations

Engineering systems represent one of the highest-value trust relationships.

Recommended controls include:

* Dedicated engineering workstations
* MFA
* Privileged Access Management (PAM)
* Session recording
* Application allowlisting
* Certificate-based authentication

Engineering access should be treated as administrative access.

---

# Remote Vendor Access

Zero Trust principles strongly support:

* Approval workflows
* MFA
* Time-limited access
* Jump Servers
* Session recording
* Continuous monitoring

Permanent VPN accounts should be avoided whenever practical. See [Secure-Remote-Access.md](Secure-Remote-Access.md) for the remote-access architecture pattern.

---

# Monitoring

Verification requires visibility.

Recommended monitoring includes:

* Authentication events
* Certificate validation
* Device posture changes
* VPN sessions
* Privilege escalation
* Engineering sessions
* Firewall events
* Configuration drift

Monitoring validates that trust assumptions remain valid.

---

# Relationship with IEC 62443

Zero Trust complements several IEC 62443 concepts:

* Identification and Authentication (FR1)
* Least Privilege (FR2)
* Zones & Conduits (FR5)
* Secure Communications (FR3/FR4)
* Defense in Depth
* Monitoring (FR6)

Zero Trust provides an architectural philosophy rather than additional compliance requirements. See [IEC62443.md](IEC62443.md).

---

# Relationship with Defense in Depth

Defense in Depth answers:

> How many independent security layers exist?

Zero Trust answers:

> Which identities should be trusted and under what conditions?

Both strategies are complementary. Layered controls remain essential even when strong identity verification exists. See [Defense-in-Depth.md](Defense-in-Depth.md).

---

# Relationship with Identity Management

Identity Management provides:

* identities,
* authentication,
* authorization.

Zero Trust determines:

* when identities are trusted,
* how trust changes,
* how trust is validated,
* how access decisions are made.

Identity Management is therefore a prerequisite for mature Zero Trust architectures. See [Identity-Management.md](Identity-Management.md).

---

# Relationship with Risk-Based Security

Risk determines:

* which assets require stronger verification,
* where trust should be minimized,
* where additional controls are justified.

Zero Trust should be implemented according to operational risk rather than uniformly. See [Risk-Based-Security.md](Risk-Based-Security.md).

---

# Common Design Mistakes

Typical mistakes include:

* Treating Zero Trust as a product.
* Copying enterprise Zero Trust architectures into OT without adaptation.
* Ignoring safety and availability.
* Assuming MFA alone provides Zero Trust.
* Ignoring device identities.
* Leaving permanent vendor access enabled.
* Performing authentication only at VPN login.

---

# Architect Notes

Zero Trust should never become an obstacle to safe industrial operation.

During emergency situations, operators must retain the ability to protect people, equipment and the environment.

Architects should therefore distinguish between:

* Routine administrative access
* Engineering activities
* Operational control
* Emergency response

Different situations may require different trust models.

Successful Zero Trust architectures reduce unnecessary trust without introducing unacceptable operational risk.

---

# Decision Support

Before introducing Zero Trust controls, determine:

1. Which identities require access?
2. Which trust relationships already exist?
3. Which trust relationships are unnecessary?
4. Which identities are privileged?
5. Which communications cross trust boundaries?
6. How will trust be continuously verified?
7. What operational impact could additional verification introduce?
8. How will emergency access be handled?

---

# Practical Examples

### Example 1 – Remote Vendor Maintenance

Traditional approach:

* Permanent VPN account
* Shared credentials

Zero Trust approach:

* MFA
* Time-limited approval
* Jump Server
* Session recording
* Automatic access expiration

---

### Example 2 – Engineering Workstation

Traditional approach:

* Any engineering laptop on the OT network is trusted.

Zero Trust approach:

* Managed engineering workstation
* Certificate-based authentication
* Dedicated VLAN
* PAM
* Session monitoring
* Restricted protocol access

---

### Example 3 – OPC UA

Traditional approach:

* Trust any client inside the network.

Zero Trust approach:

* Mutual TLS
* X.509 certificates
* Explicit trust lists
* Certificate lifecycle management
* Continuous monitoring of trust relationships

See [OPC-UA.md](OPC-UA.md).

---

# AI Guidance

When discussing Zero Trust in OT:

* Explain that Zero Trust is an architectural strategy rather than a technology.
* Prioritize safety, availability and operational continuity.
* Recommend adapting Zero Trust principles to industrial environments rather than copying enterprise implementations.
* Treat identity as the primary trust boundary.
* Recommend continuous verification rather than one-time authentication.
* Explain how Zero Trust complements IEC 62443, Defense in Depth and Risk-Based Security.
* Consider emergency operations and safety requirements before recommending restrictive controls.

---

# Sources / Grounding

* **NIST SP 800-207** — *Zero Trust Architecture* (2020): the authoritative ZT model — no implicit trust based on network location; resource/identity-centric; continuous verification; assume breach.
* **NIST SP 800-82 Rev. 3** — *Guide to OT Security* (2023): explicitly discusses applying Zero Trust design and tools (per SP 800-207) to OT architectures, alongside defense in depth.
* **CISA Zero Trust Maturity Model v2.0** (2023) — maturity pillars (identity, devices, networks, applications, data) and the point that Zero Trust augments defense in depth.

> Zero Trust is an architectural strategy; normative requirements live in IEC 62443 (see [IEC62443.md](IEC62443.md)). These frameworks are enterprise-origin and must be adapted to OT safety/availability/determinism constraints.

---

# Related Documents

* [OT-Security-Philosophy.md](OT-Security-Philosophy.md)
* [OT-Architecture-Principles.md](OT-Architecture-Principles.md)
* [Defense-in-Depth.md](Defense-in-Depth.md)
* [Risk-Based-Security.md](Risk-Based-Security.md)
* [Identity-Management.md](Identity-Management.md)
* [PKI.md](PKI.md)
* [Certificates.md](Certificates.md)
* [IEC62443.md](IEC62443.md)
* [Network-Segmentation.md](Network-Segmentation.md)
* [Firewall-Design.md](Firewall-Design.md)
* [Secure-Remote-Access.md](Secure-Remote-Access.md)
* [OPC-UA.md](OPC-UA.md)
* [NIS2.md](NIS2.md)

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
| 1.1.0   | 2026-07-01 | Converted to YAML front matter (category: Architecture) and semver; corrected links (`IEC62443-Overview.md` → `IEC62443.md`) and removed the retired `Zones-and-Conduits.md` reference (zones/conduits now via IEC62443.md); designated single source of truth for Zero Trust; added Sources/Grounding (NIST SP 800-207, NIST SP 800-82 Rev. 3, CISA ZTMM v2.0); added cross-links to OT-Architecture-Principles.md and the Defense-in-Depth / Risk-Based-Security strategy trio; converted Related Documents to markdown links |
