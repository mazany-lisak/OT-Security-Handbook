# Zero Trust in OT

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, Security Engineers, Network Architects, Automation Engineers, AI Knowledge Base

---

# Purpose

Zero Trust is a security strategy based on the principle that trust should never be assumed and must always be explicitly established, continuously evaluated and appropriately limited.

Within Operational Technology (OT), Zero Trust should not be interpreted as a direct copy of enterprise IT implementations.

Instead, Zero Trust must be adapted to industrial environments where safety, availability and deterministic operation remain primary objectives.

The purpose of this document is to explain how Zero Trust principles can be realistically and safely applied within Industrial Automation and Control Systems (IACS).

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

Identity controls should exist at every trust boundary.

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

Permanent VPN accounts should be avoided whenever practical.

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

* Identification and Authentication
* Least Privilege
* Zones & Conduits
* Secure Communications
* Defense in Depth
* Monitoring

Zero Trust provides an architectural philosophy rather than additional compliance requirements.

---

# Relationship with Defense in Depth

Defense in Depth answers:

> How many independent security layers exist?

Zero Trust answers:

> Which identities should be trusted and under what conditions?

Both strategies are complementary.

Layered controls remain essential even when strong identity verification exists.

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

Identity Management is therefore a prerequisite for mature Zero Trust architectures.

---

# Relationship with Risk-Based Security

Risk determines:

* which assets require stronger verification,
* where trust should be minimized,
* where additional controls are justified.

Zero Trust should be implemented according to operational risk rather than uniformly.

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

# Related Documents

* OT-Security-Philosophy.md
* Risk-Based-Security.md
* Defense-in-Depth.md
* Identity-Management.md
* PKI.md
* Certificates.md
* Zones-and-Conduits.md
* Network-Segmentation.md
* Firewall-Design.md
* Secure-Remote-Access.md
* OPC-UA.md
* IEC62443-Overview.md
* NIS2.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
