---
id: identity-management
title: "Identity Management"
category: Identity
layer: "05-Identity"
version: 1.1.0
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Identity as the primary OT trust boundary: identity categories (human, device, application,
  service), lifecycle, authentication and authorization, governance, privileged identities and the
  relationship to Zero Trust and IEC 62443.
keywords: [identity management, správa identit, identity lifecycle, RBAC, authentication, authorization, governance, device identity]
---

# Purpose

Identity Management is the process of establishing, verifying, authorizing and governing the identities of users, devices, applications and services throughout their lifecycle.

Within modern Operational Technology (OT), identity has become one of the primary security boundaries.

Secure identity management enables organizations to reduce implicit trust, enforce least privilege and support Zero Trust architectures without compromising operational safety or availability.

---

# Scope

This document describes:

* Identity concepts
* Identity lifecycle
* Human identities
* Device identities
* Application identities
* Service identities
* Authentication
* Authorization
* Governance
* Identity architecture

Implementation details for specific identity platforms are intentionally excluded.

---

# Security Philosophy

Historically, industrial systems often assumed:

> "Everything connected to the control network is trusted."

Modern OT environments invalidate this assumption.

Identity—not network location—should determine access decisions.

Trust should always be associated with verified identities rather than IP addresses or physical network placement.

---

# What Is an Identity?

An identity uniquely represents an entity interacting with the industrial environment.

Examples include:

* Human operators
* Engineers
* Administrators
* Vendors
* PLCs
* SCADA servers
* OPC UA applications
* Historian services
* VPN gateways
* APIs

Identity is broader than user accounts.

---

# Identity Categories

Industrial environments commonly contain four identity categories.

## Human Identities

Examples:

* Operators
* Engineers
* Administrators
* Contractors
* Vendors
* Auditors

---

## Device Identities

Examples:

* PLCs
* HMIs
* Firewalls
* Switches
* Industrial routers
* Engineering laptops
* Servers

---

## Application Identities

Examples:

* OPC UA Servers
* OPC UA Clients
* SCADA services
* Historians
* MES applications
* Backup software

---

## Service Identities

Examples:

* Windows Services
* Linux Services
* Scheduled Tasks
* Automation Services
* API Accounts

Each category requires different governance.

---

# Identity Lifecycle

Every identity should follow a managed lifecycle.

```text
Request
        ↓
Approval
        ↓
Provisioning
        ↓
Authentication
        ↓
Authorization
        ↓
Monitoring
        ↓
Review
        ↓
Modification
        ↓
Deprovisioning
```

Identity lifecycle management is a continuous operational process.

---

# Authentication

Authentication answers:

> **Who are you?**

Common authentication methods include:

* Passwords
* MFA
* Smart Cards
* X.509 Certificates
* FIDO2 Security Keys
* Kerberos
* RADIUS
* LDAP
* Active Directory
* Microsoft Entra ID (where appropriate)

Authentication strength should reflect operational risk.

---

# Authorization

Authorization answers:

> **What are you allowed to do?**

Recommended principles include:

* Least Privilege
* Role-Based Access Control (RBAC)
* Attribute-Based Access Control (ABAC), where appropriate
* Separation of Duties
* Time-limited privileges

Authorization should be reviewed regularly.

---

# Identity Governance

Governance ensures identities remain accurate throughout their lifecycle.

Recommended practices include:

* Identity ownership
* Periodic access reviews
* Joiner/Mover/Leaver processes
* Privileged account reviews
* Vendor account reviews
* Automated deprovisioning where feasible

Identity governance is as important as authentication.

---

# Privileged Identities

Privileged identities represent one of the highest-value attack targets.

Examples include:

* Domain Administrators
* PLC Administrators
* Firewall Administrators
* SCADA Administrators
* Hypervisor Administrators
* Backup Administrators

Recommended controls:

* Privileged Access Management (PAM)
* MFA
* Session recording
* Just-In-Time (JIT) access
* Approval workflows

---

# Device Identity

Modern OT increasingly relies on trusted devices.

Examples include:

* OPC UA certificates
* TPM-backed identities
* Device certificates
* Secure Boot (platform dependent)
* Hardware attestation

Device identity should complement—not replace—user identity.

---

# Application Identity

Applications increasingly authenticate independently of users.

Examples include:

* OPC UA Application Certificates
* API tokens
* Mutual TLS (mTLS)
* Service certificates

Application identities should be managed with the same rigor as human identities.

---

# PKI and Certificates

Public Key Infrastructure (PKI) enables scalable trust between systems.

Typical use cases include:

* OPC UA
* HTTPS
* MQTT over TLS
* VPN
* Device authentication

Certificate lifecycle management should include:

* Issuance
* Distribution
* Renewal
* Revocation
* Expiration monitoring

---

# Identity in OT Architecture

Identity should be evaluated at every trust boundary.

Typical architecture:

```text
Operator
        │
Identity Provider
        │
MFA
        │
Jump Server
        │
Engineering Workstation
        │
PLC
```

Identity controls should be applied consistently across administrative and operational access paths.

---

# Relationship with Zero Trust

Identity is the foundation of Zero Trust.

Zero Trust answers:

> **Under which conditions should an identity be trusted?**

Identity Management provides:

* Authentication
* Authorization
* Identity lifecycle
* Governance

Together they enable continuous trust evaluation.

---

# Relationship with IEC 62443

Identity Management supports several IEC 62443 concepts including:

* Identification and Authentication Control (IAC)
* Use Control (UC)
* Least Privilege
* Accountability
* Secure Communications

Strong identity management is a prerequisite for achieving many IEC 62443 security requirements.

---

# Relationship with Defense in Depth

Identity is one of the primary layers within Defense in Depth.

Compromise of one identity should not result in unrestricted access across the industrial environment.

Identity controls should complement:

* Network segmentation
* Firewalls
* Monitoring
* Incident response

---

# Monitoring

Recommended identity monitoring includes:

* Authentication failures
* Privilege escalation
* New privileged accounts
* Certificate changes
* Service account modifications
* Vendor logins
* MFA failures
* Dormant account usage
* Unusual login times
* Geographic anomalies (where applicable)

Identity telemetry should be integrated into centralized monitoring.

---

# Common Design Mistakes

Typical mistakes include:

* Shared engineering accounts
* Permanent vendor accounts
* Overprivileged service accounts
* Missing account reviews
* No MFA for privileged access
* Ignoring certificate expiration
* Device trust based solely on IP addresses
* Manual identity lifecycle management without governance

---

# Architect Notes

Identity is rapidly replacing the network perimeter as the primary security boundary in modern OT environments.

However, identity should never be treated in isolation.

Effective identity architectures integrate:

* Network Segmentation
* Zero Trust
* Defense in Depth
* PKI
* Monitoring
* Governance

The objective is not simply to authenticate users, but to establish trustworthy relationships between people, devices, applications and industrial processes.

---

# Decision Support

Before introducing a new identity or authentication mechanism, determine:

1. Who or what requires the identity?
2. Who owns the identity?
3. How will the identity be authenticated?
4. Which privileges are required?
5. How will privileges be reviewed?
6. How will the identity be monitored?
7. How will the identity be revoked?
8. How does this identity affect existing trust relationships?

---

# Practical Examples

### Example 1 – Engineering Workstation

Traditional approach:

* Shared administrator account
* Password-only authentication

Recommended approach:

* Named user account
* MFA
* PAM
* Session recording
* RBAC
* Dedicated engineering workstation

---

### Example 2 – OPC UA

Traditional approach:

* Trust any client inside the OT network

Recommended approach:

* Application certificates
* Mutual TLS
* Trusted certificate store
* Certificate lifecycle management
* Certificate revocation process

---

### Example 3 – Vendor Remote Access

Traditional approach:

* Permanent VPN account
* Shared credentials

Recommended approach:

* Individual identity
* MFA
* Just-In-Time access
* Approval workflow
* Session recording
* Automatic account expiration

---

# AI Guidance

When discussing Identity Management:

* Treat identity as the primary trust boundary rather than network location.
* Distinguish authentication from authorization.
* Explain the complete identity lifecycle.
* Recommend unique identities for users, devices, applications and services.
* Prioritize Least Privilege, RBAC and governance.
* Recommend MFA and certificate-based authentication where operationally appropriate.
* Explain how Identity Management supports Zero Trust, Defense in Depth and IEC 62443.
* Consider operational continuity, emergency access and safety before recommending restrictive identity controls.

---

# Related Documents

* [OT-Security-Philosophy.md](../00-Core/OT-Security-Philosophy.md)
* [Risk-Based-Security.md](../03-Architecture/Risk-Based-Security.md)
* [Defense-in-Depth.md](../03-Architecture/Defense-in-Depth.md)
* [Zero-Trust-in-OT.md](../03-Architecture/Zero-Trust-in-OT.md)
* [PKI.md](PKI.md)
* [Certificates.md](Certificates.md)
* [Secure-Remote-Access.md](../04-Network/Secure-Remote-Access.md)
* [OPC-UA.md](../04-Network/OPC-UA.md)
* [Network-Segmentation.md](../04-Network/Network-Segmentation.md)
* [IEC62443.md](../02-Standards/IEC62443.md)
* [Firewall-Design.md](../04-Network/Firewall-Design.md)
* [NIS2.md](../01-Legislation/NIS2.md)
---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
| 1.1.0 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings; migrated from draft header (no front matter) to canonical schema |
