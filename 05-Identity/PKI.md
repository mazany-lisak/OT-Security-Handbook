# Public Key Infrastructure (PKI)

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, Identity Architects, Security Engineers, Automation Engineers, AI Knowledge Base

---

# Purpose

Public Key Infrastructure (PKI) is the foundation for establishing, managing and maintaining digital trust within modern Industrial Automation and Control Systems (IACS).

PKI enables secure authentication, encryption, integrity protection and non-repudiation through the use of digital certificates and asymmetric cryptography.

Within Operational Technology (OT), PKI has become an essential architectural component supporting secure communications, device identities and Zero Trust principles.

PKI should be viewed as a **trust architecture**, not merely as a certificate issuance system.

---

# Scope

This document describes:

* PKI concepts
* Trust architecture
* Certificate Authorities
* Certificate lifecycle
* Trust relationships
* Revocation
* OT-specific PKI considerations
* Governance
* Operational best practices

Cryptographic algorithm details are intentionally outside the scope of this document.

---

# Security Philosophy

Modern OT increasingly depends on digital trust rather than network trust.

Instead of assuming that devices inside the control network are trustworthy, systems should verify identity using cryptographic credentials.

PKI provides the mechanism for establishing this trust.

Trust becomes explicit, verifiable and manageable.

---

# Why PKI Matters in OT

Modern industrial technologies commonly depend on PKI, including:

* OPC UA
* HTTPS
* MQTT over TLS
* VPN
* Device authentication
* Web management interfaces
* Industrial APIs
* Secure remote access

Without PKI, secure authentication between systems becomes difficult to scale and govern.

---

# Core Components

A typical PKI consists of:

* Root Certificate Authority (Root CA)
* Intermediate Certificate Authority (Intermediate CA)
* Registration Authority (optional)
* Certificate Repository
* Certificate Revocation mechanisms
* Certificate lifecycle management
* Policies and governance

Each component contributes to the overall trust architecture.

---

# Certificate Authorities

## Root CA

The Root CA represents the highest level of trust.

Characteristics include:

* Self-signed certificate
* Long validity period
* Offline storage recommended
* Rarely used for certificate issuance

Compromise of the Root CA compromises the entire PKI.

---

## Intermediate CA

Intermediate CAs issue operational certificates.

Advantages include:

* Reduced exposure of the Root CA
* Easier lifecycle management
* Delegated administration
* Operational flexibility

Production certificates should generally be issued by Intermediate CAs rather than directly by the Root CA.

---

# Certificate Lifecycle

Certificates require continuous management.

Recommended lifecycle:

```text id="0pn7kp"
Generate Key Pair
        ↓
Certificate Signing Request (CSR)
        ↓
Certificate Issuance
        ↓
Distribution
        ↓
Installation
        ↓
Trust Establishment
        ↓
Monitoring
        ↓
Renewal
        ↓
Revocation (if required)
        ↓
Retirement
```

Certificate management is an operational process rather than a one-time deployment task.

---

# Trust Model

PKI establishes trust through chains of trust.

Typical model:

```text id="nltj0t"
Root CA
      │
Intermediate CA
      │
Application Certificate
      │
Mutual Trust
      │
Secure Communication
```

Trust should always be explicitly established and periodically reviewed.

---

# Certificate Types

Common certificate types in OT include:

## User Certificates

Used for:

* Engineers
* Administrators
* Smart Cards
* Client authentication

---

## Device Certificates

Used for:

* PLCs
* Industrial PCs
* Firewalls
* Switches
* Gateways

---

## Application Certificates

Used for:

* OPC UA Servers
* OPC UA Clients
* MQTT Brokers
* Web Services
* APIs

---

## Server Certificates

Used for:

* HTTPS
* VPN gateways
* Web management
* Identity services

Each certificate type has different lifecycle requirements.

---

# Certificate Revocation

Certificates should be revoked when:

* Private keys are compromised
* Devices are decommissioned
* Employees leave the organization
* Applications are retired
* Trust relationships change

Revocation mechanisms may include:

* Certificate Revocation Lists (CRLs)
* Online Certificate Status Protocol (OCSP), where supported

Revocation should be integrated into operational procedures.

---

# Private Key Protection

The private key is the most critical asset in PKI.

Recommended protections include:

* Hardware Security Modules (HSMs), where appropriate
* Trusted Platform Modules (TPMs)
* Encrypted key storage
* Restricted administrative access
* Backup and recovery procedures
* Key rotation policies

Loss or compromise of private keys undermines trust.

---

# PKI Governance

PKI requires organizational governance.

Recommended practices include:

* Certificate policies
* Naming conventions
* Identity ownership
* Approval workflows
* Expiration monitoring
* Renewal procedures
* Revocation procedures
* Periodic audits

Governance is as important as technology.

---

# PKI in OT Architecture

Typical architecture:

```text id="6k9mqb"
Enterprise PKI
        │
Intermediate CA
        │
Certificate Services
        │
OPC UA
HTTPS
VPN
MQTT
Industrial Applications
```

Architects should determine whether to use:

* Enterprise PKI
* Dedicated OT PKI
* Hybrid PKI

The decision should be based on organizational structure, operational requirements and trust boundaries.

---

# Relationship with Identity Management

PKI provides cryptographic identities.

Identity Management governs:

* ownership,
* lifecycle,
* authorization,
* accountability.

Together they establish trustworthy digital identities.

---

# Relationship with Zero Trust

Zero Trust requires explicit identity verification.

PKI enables:

* Mutual authentication
* Device identity
* Application identity
* Certificate-based trust
* Continuous verification

PKI is therefore a foundational technology for mature Zero Trust architectures.

---

# Relationship with OPC UA

OPC UA relies heavily on PKI.

Typical capabilities include:

* Application certificates
* Mutual TLS
* Trusted certificate stores
* Certificate revocation
* Secure channels

Poor certificate lifecycle management is one of the most common causes of insecure OPC UA deployments.

---

# Relationship with IEC 62443

PKI supports multiple IEC 62443 concepts including:

* Identification and Authentication
* Secure Communications
* System Integrity
* Least Privilege
* Accountability

PKI contributes to implementing—but does not by itself satisfy—IEC 62443 requirements.

---

# Monitoring

Recommended monitoring includes:

* Certificate expiration
* Failed certificate validation
* New trusted certificates
* Certificate revocation events
* CA changes
* Key compromise indicators
* Unauthorized certificate issuance
* Unexpected trust relationships

Certificate events should be integrated into centralized monitoring and SIEM platforms.

---

# Common Design Mistakes

Typical mistakes include:

* Using the Root CA for operational certificate issuance.
* Ignoring certificate expiration.
* Sharing private keys.
* Manually trusting certificates without governance.
* No certificate inventory.
* No revocation process.
* No renewal planning.
* Treating PKI as an IT-only technology.

---

# Architect Notes

One of the most common misconceptions is that PKI is primarily about cryptography.

In reality, cryptography is only the implementation mechanism.

PKI is fundamentally about establishing, maintaining and governing trust between identities.

Successful PKI projects are therefore governance projects as much as technology projects.

---

# Decision Support

Before implementing PKI, determine:

1. Which identities require certificates?
2. Who owns the Root CA?
3. Should the Root CA remain offline?
4. Will Intermediate CAs be deployed?
5. How will certificates be renewed?
6. How will revocation be managed?
7. How will private keys be protected?
8. How will trust relationships be audited?

---

# Practical Examples

### Example 1 – OPC UA

Traditional approach:

* Manually accept all certificates.

Recommended approach:

* Internal PKI
* Trusted Root CA
* Intermediate CA
* Certificate inventory
* Renewal process
* Revocation process

---

### Example 2 – Remote Vendor Access

Traditional approach:

* Password-only VPN

Recommended approach:

* Device certificate
* User certificate
* MFA
* Certificate lifecycle management
* Revocation upon contract termination

---

### Example 3 – Engineering Workstation

Traditional approach:

* Password authentication only

Recommended approach:

* User certificate
* Smart Card or FIDO2
* TPM-backed private key
* Centralized certificate lifecycle management

---

# AI Guidance

When discussing PKI:

* Explain that PKI is a trust architecture rather than simply a certificate service.
* Distinguish Root CA, Intermediate CA and end-entity certificates.
* Recommend protecting the Root CA offline whenever practical.
* Treat certificate lifecycle management as a continuous operational process.
* Recommend PKI as the preferred trust model for OPC UA, VPNs and secure industrial communications.
* Explain how PKI supports Identity Management, Zero Trust and IEC 62443.
* Consider governance and operational processes as equally important as cryptographic mechanisms.

---

# Related Documents

* Identity-Management.md
* Certificates.md
* Zero-Trust-in-OT.md
* Defense-in-Depth.md
* OPC-UA.md
* Secure-Remote-Access.md
* Network-Segmentation.md
* IEC62443-Overview.md
* NIS2.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
