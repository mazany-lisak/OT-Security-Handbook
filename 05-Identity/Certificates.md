---
id: certificates
title: "Digital Certificates"
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
  X.509 certificates as managed digital identities: structure and types, lifecycle, validation,
  expiration monitoring, renewal and revocation, private-key protection and certificate-intensive
  OT technologies such as OPC UA.
keywords: [certificates, certifikáty, X.509, certificate lifecycle, expirace, revocation, SAN, key usage]
---

# Purpose

Digital certificates provide cryptographically verifiable identities for users, devices, applications and services.

Within Operational Technology (OT), certificates enable trusted communication, secure authentication and encrypted data exchange across industrial systems.

Certificates should be regarded as **digital identities** rather than merely cryptographic artifacts.

Proper certificate lifecycle management is essential for maintaining secure and resilient industrial architectures.

---

# Scope

This document describes:

* Certificate concepts
* Certificate structure
* Certificate lifecycle
* Trust establishment
* Certificate validation
* Revocation
* Operational management
* OT-specific best practices

Details of Certificate Authority (CA) design are described separately in **[PKI.md](PKI.md)**.

---

# Security Philosophy

Traditional OT environments often relied on network location as the basis of trust.

Modern OT architectures increasingly rely on certificates to establish trust explicitly.

Certificates allow systems to answer two critical questions:

* Who are you?
* Can I trust you?

Trust becomes based on cryptographic proof rather than network location.

---

# What Is a Digital Certificate?

A digital certificate is a signed document that binds an identity to a public cryptographic key.

A certificate typically identifies:

* A person
* A device
* An application
* A service
* A server

Certificates do **not** provide trust by themselves.

Trust is established through the PKI that issues and validates them.

---

# Certificate Components

A typical X.509 certificate contains:

* Subject
* Issuer
* Public Key
* Serial Number
* Validity Period
* Key Usage
* Extended Key Usage
* Subject Alternative Name (SAN)
* Signature Algorithm
* Digital Signature

Applications should validate all relevant certificate fields before establishing trust.

---

# Certificate Types

Common certificate categories include:

## User Certificates

Typical uses:

* Engineers
* Administrators
* Smart Cards
* Client authentication

---

## Device Certificates

Typical uses:

* PLCs
* Firewalls
* Industrial switches
* Routers
* HMIs

---

## Application Certificates

Typical uses:

* OPC UA Servers
* OPC UA Clients
* MQTT Brokers
* APIs

---

## Server Certificates

Typical uses:

* HTTPS
* VPN
* Web Management Interfaces
* Identity Services

---

## Code Signing Certificates

Typical uses:

* Software signing
* Firmware signing
* Script validation
* Trusted software distribution

Code signing helps ensure software integrity and authenticity.

---

# Certificate Lifecycle

Certificates require active lifecycle management.

Recommended lifecycle:

```text id="pl2m6d"
Identity Created
        ↓
Key Pair Generated
        ↓
Certificate Issued
        ↓
Certificate Installed
        ↓
Trust Established
        ↓
Operational Use
        ↓
Monitoring
        ↓
Renewal
        ↓
Revocation (if required)
        ↓
Retirement
```

Lifecycle management should be automated where practical.

---

# Certificate Validation

Before trusting a certificate, systems should verify:

* Issuing CA
* Validity period
* Digital signature
* Revocation status
* Intended key usage
* Subject identity
* Certificate chain

Incomplete validation weakens the trust model.

---

# Certificate Expiration

Certificate expiration is one of the most common operational risks.

Organizations should maintain:

* Certificate inventory
* Expiration monitoring
* Renewal procedures
* Notification workflows
* Emergency renewal procedures

Unexpected certificate expiration may disrupt industrial communication.

---

# Certificate Renewal

Renewal should occur before certificate expiration.

Recommended practices include:

* Automated renewal where supported
* Scheduled maintenance windows
* Renewal testing
* Backup of private keys (when appropriate)
* Validation after deployment

Certificate renewal should not be treated as an emergency activity.

---

# Certificate Revocation

Certificates should be revoked when:

* Private keys are compromised
* Devices are decommissioned
* Applications are retired
* Users leave the organization
* Trust relationships change

Revocation mechanisms include:

* CRLs
* OCSP (where supported)

Operational procedures should define revocation responsibilities.

---

# Private Key Protection

Certificates are only as secure as their associated private keys.

Recommended controls include:

* TPM-backed storage
* HSMs for critical systems
* Encrypted storage
* Restricted administrative access
* Secure backups
* Key rotation policies

Private keys should never be shared between identities.

---

# Certificates in OT

Modern OT increasingly depends on certificates.

Examples include:

* OPC UA
* HTTPS
* MQTT over TLS
* VPN authentication
* Secure APIs
* Device authentication

Certificate management should therefore become part of routine OT operations.

---

# Relationship with PKI

PKI defines the trust architecture.

Certificates implement that trust.

PKI answers:

> Who can issue trust?

Certificates answer:

> Who is trusted?

The two concepts should always be considered together.

---

# Relationship with Identity Management

Certificates represent cryptographic identities.

Identity Management governs:

* Ownership
* Authorization
* Lifecycle
* Accountability

Certificates should never exist without an accountable identity owner.

---

# Relationship with Zero Trust

Zero Trust relies on continuous identity verification.

Certificates enable:

* Mutual authentication
* Device identity
* Application identity
* Secure communications

Certificate validity should be continuously evaluated rather than assumed.

---

# Relationship with OPC UA

OPC UA is one of the most certificate-intensive industrial technologies.

Certificates are used for:

* Application identity
* Mutual authentication
* Secure channels
* Trust establishment

Certificate lifecycle management is therefore a critical operational process for OPC UA deployments.

---

# Relationship with IEC 62443

Certificates support several IEC 62443 objectives:

* Identification and Authentication
* Secure Communications
* System Integrity
* Least Privilege

Certificates alone do not provide compliance.

They support implementation of broader architectural controls.

---

# Monitoring

Recommended monitoring includes:

* Certificate expiration
* Certificate validation failures
* Unexpected certificate issuance
* New trusted certificates
* Certificate revocation events
* Certificate chain failures
* Unauthorized certificate replacement

Certificate telemetry should be integrated into centralized monitoring and SIEM platforms.

---

# Common Design Mistakes

Typical mistakes include:

* Accepting every certificate permanently.
* Ignoring expiration dates.
* Sharing certificates between systems.
* Failing to revoke retired certificates.
* Missing certificate inventory.
* Storing private keys insecurely.
* Treating certificates as one-time deployment artifacts.

---

# Architect Notes

Certificates should not be viewed as technical files.

They are operational identities.

A mature OT organization should know:

* Which certificates exist.
* Who owns them.
* Where they are installed.
* When they expire.
* Which systems trust them.
* How they are renewed.
* How they are revoked.

Certificate governance is a critical component of digital trust.

---

# Decision Support

Before deploying a certificate, determine:

1. Which identity will the certificate represent?
2. Who owns the identity?
3. Which CA issued the certificate?
4. What is the certificate validity period?
5. How will expiration be monitored?
6. How will renewal occur?
7. How will revocation be handled?
8. How will the associated private key be protected?

---

# Practical Examples

### Example 1 – OPC UA Server

Traditional approach:

* Self-signed certificate manually accepted by each client.

Recommended approach:

* Certificate issued by an internal Intermediate CA.
* Managed trust store.
* Central certificate inventory.
* Automated expiration monitoring.
* Documented renewal process.

---

### Example 2 – VPN Gateway

Traditional approach:

* Shared username and password.

Recommended approach:

* Server certificate.
* Client certificate.
* Mutual TLS.
* MFA for user authentication.
* Certificate revocation upon device loss or employee departure.

---

### Example 3 – Engineering Workstation

Traditional approach:

* Password-based login only.

Recommended approach:

* User certificate.
* TPM-protected private key.
* Smart Card or FIDO2 where operationally appropriate.
* Certificate renewal integrated with identity lifecycle management.

---

# AI Guidance

When discussing digital certificates:

* Explain that certificates represent digital identities rather than merely cryptographic files.
* Distinguish certificates from the PKI that governs them.
* Emphasize certificate lifecycle management as an operational responsibility.
* Recommend centralized certificate inventory and expiration monitoring.
* Promote certificate-based authentication for OPC UA, VPNs and secure industrial services where appropriate.
* Explain how certificates support Identity Management, Zero Trust and Defense in Depth.
* Consider operational continuity when recommending certificate renewal or revocation processes.

---

# Related Documents

* [PKI.md](PKI.md)
* [Identity-Management.md](Identity-Management.md)
* [Zero-Trust-in-OT.md](../03-Architecture/Zero-Trust-in-OT.md)
* [Defense-in-Depth.md](../03-Architecture/Defense-in-Depth.md)
* [OPC-UA.md](../04-Network/OPC-UA.md)
* [Secure-Remote-Access.md](../04-Network/Secure-Remote-Access.md)
* [IEC62443.md](../02-Standards/IEC62443.md)
---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
| 1.1.0 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings; migrated from draft header to canonical schema; Application/Device-Identity references folded into Identity-Management.md |
