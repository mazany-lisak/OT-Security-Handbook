# Multi-Factor Authentication (MFA)

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, Identity Architects, Security Engineers, Automation Engineers, AI Knowledge Base

---

# Purpose

Multi-Factor Authentication (MFA) strengthens authentication by requiring two or more independent authentication factors before access is granted.

Within Operational Technology (OT), MFA significantly reduces the risk of credential compromise and unauthorized access.

However, MFA must always be implemented in a manner that preserves operational safety, availability and emergency response capabilities.

The objective is not to maximize authentication complexity, but to establish trustworthy access appropriate to operational risk.

---

# Scope

This document describes:

* MFA concepts
* Authentication factors
* OT-specific implementation
* Risk-based deployment
* Emergency access
* Operational considerations
* Governance
* Best practices

Vendor-specific MFA implementations are intentionally excluded.

---

# Security Philosophy

Passwords alone no longer provide sufficient protection for privileged access.

Modern attacks frequently involve:

* Credential theft
* Password spraying
* Phishing
* Session hijacking
* Social engineering
* Credential reuse

MFA significantly increases the effort required to compromise an identity.

Within OT, MFA should be applied where it meaningfully reduces risk without impairing safe operations.

---

# What Is MFA?

Multi-Factor Authentication requires authentication using multiple independent factors.

Typical factor categories include:

### Something You Know

Examples:

* Password
* PIN
* Passphrase

---

### Something You Have

Examples:

* Smart Card
* FIDO2 Security Key
* Hardware Token
* Mobile Authenticator
* Certificate

---

### Something You Are

Examples:

* Fingerprint
* Face Recognition
* Iris Recognition

Authentication is stronger when factors originate from different categories.

---

# Why MFA Matters in OT

Industrial environments increasingly support:

* Remote maintenance
* Vendor access
* VPN connectivity
* Engineering workstations
* Virtual infrastructure
* Identity federation

Credential compromise frequently represents the first step in industrial attack campaigns.

MFA helps interrupt this attack path.

---

# Risk-Based Deployment

Not every OT system requires the same authentication strength.

Architects should prioritize MFA for:

* Engineering workstations
* Domain Administrators
* Firewall administrators
* VPN gateways
* Jump Servers
* Hypervisors
* SCADA administration
* Backup administration
* Cloud administration

Operator interfaces supporting real-time process control require careful evaluation.

---

# Authentication Methods

Common MFA methods include:

* Smart Cards
* FIDO2 Security Keys
* Authenticator Applications
* Hardware OTP Tokens
* Certificate-Based Authentication
* Push Notifications (risk dependent)

SMS-based authentication is generally discouraged due to known security limitations.

---

# MFA and Identity

MFA strengthens authentication.

It does not replace:

* Identity Management
* Authorization
* RBAC
* PAM
* Zero Trust

Successful identity architectures require all of these components.

---

# MFA and Zero Trust

Zero Trust assumes identity verification should be explicit.

MFA provides stronger identity assurance before trust is granted.

However, Zero Trust also requires:

* Device trust
* Continuous verification
* Least Privilege
* Monitoring
* Policy evaluation

MFA represents one component of Zero Trust rather than the entire strategy.

---

# Emergency Access

OT environments require special consideration for emergency situations.

Examples include:

* Safety incidents
* Environmental emergencies
* Process instability
* Disaster recovery
* Identity infrastructure failures

Emergency procedures should be:

* documented,
* approved,
* periodically tested,
* monitored,
* audited after use.

Emergency access should remain exceptional rather than routine.

---

# Vendor Access

Recommended controls include:

* MFA
* Individual identities
* Approval workflow
* Time-limited access
* Session recording
* Jump Servers
* Automatic session termination

Shared vendor accounts should be avoided.

---

# Engineering Workstations

Engineering workstations represent high-value administrative assets.

Recommended controls include:

* MFA
* Dedicated engineering devices
* PAM
* Certificate-based authentication
* Application allowlisting
* Session auditing

Engineering identities should receive stronger protection than ordinary operator accounts.

---

# Operational Considerations

Authentication mechanisms should support:

* High availability
* Offline operation (where required)
* Disaster recovery
* Redundancy
* Maintenance windows

Architectures should define how authentication continues during identity infrastructure outages.

---

# Identity Infrastructure

MFA depends on reliable identity services.

Architects should consider:

* Active Directory
* Microsoft Entra ID
* RADIUS
* Certificate Services
* Identity federation
* Local fallback mechanisms

Identity infrastructure should match operational availability requirements.

---

# Relationship with IEC 62443

MFA supports multiple IEC 62443 concepts including:

* Identification and Authentication Control
* Least Privilege
* Accountability
* Secure Administrative Access

IEC 62443 does not mandate specific MFA technologies.

Architectural decisions should remain risk-based.

---

# Relationship with NIS2

NIS2 promotes strong identity protection and access control.

MFA contributes to:

* Access security
* Identity protection
* Administrative security
* Operational resilience

Organizations should implement MFA according to risk.

---

# Monitoring

Recommended monitoring includes:

* Failed MFA attempts
* MFA bypass events
* New authentication devices
* Push fatigue attacks
* Hardware token enrollment
* Emergency access usage
* Vendor authentication
* Authentication anomalies

Identity telemetry should be integrated into centralized monitoring and SIEM platforms.

---

# Common Design Mistakes

Typical mistakes include:

* Applying MFA uniformly without considering operational impact.
* Using SMS as the primary second factor.
* Shared hardware tokens.
* Permanent MFA bypass accounts.
* Ignoring emergency access procedures.
* No monitoring of MFA failures.
* Treating MFA as sufficient protection on its own.

---

# Architect Notes

One of the most common misconceptions is:

> "MFA should protect every login equally."

In OT, authentication strength should reflect operational risk.

For example:

* A PLC operator responding to an emergency may require rapid access under defined procedures.
* A remote vendor performing maintenance should undergo significantly stronger authentication and oversight.

Successful MFA deployments balance security with operational continuity and safety.

---

# Decision Support

Before implementing MFA, determine:

1. Which identities require MFA?
2. What operational risk justifies MFA?
3. Which authentication factors are appropriate?
4. How will emergency access be handled?
5. What happens if the MFA infrastructure becomes unavailable?
6. How will vendor access be managed?
7. How will MFA events be monitored?
8. How will MFA support disaster recovery?

---

# Practical Examples

### Example 1 – Remote Vendor Access

Traditional approach:

* Username and password

Recommended approach:

* Individual identity
* VPN
* MFA
* Jump Server
* Time-limited approval
* Session recording

---

### Example 2 – Engineering Workstation

Traditional approach:

* Local administrator account
* Password only

Recommended approach:

* Named identity
* MFA
* PAM
* Certificate-based authentication
* Session monitoring

---

### Example 3 – SCADA Administration

Traditional approach:

* Shared administrator account

Recommended approach:

* Individual administrator account
* MFA
* RBAC
* Approval workflow
* Logging of privileged actions

---

# AI Guidance

When discussing MFA:

* Explain that MFA strengthens authentication but does not replace Identity Management or Zero Trust.
* Recommend risk-based deployment rather than uniform enforcement.
* Prioritize MFA for privileged, remote and administrative access.
* Consider operational continuity and emergency response requirements before recommending MFA for control-room operators.
* Recommend phishing-resistant authentication methods (for example FIDO2 security keys or certificate-based authentication) where operationally appropriate.
* Discourage SMS-based MFA except where no stronger alternative is available.
* Explain how MFA complements PAM, RBAC, PKI and certificate-based authentication.

---

# Related Documents

* Identity-Management.md
* PKI.md
* Certificates.md
* Zero-Trust-in-OT.md
* Defense-in-Depth.md
* Risk-Based-Security.md
* Secure-Remote-Access.md
* Privileged-Access-Management.md
* OPC-UA.md
* IEC62443-Overview.md
* NIS2.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
