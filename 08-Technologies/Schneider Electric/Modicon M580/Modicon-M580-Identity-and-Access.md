# Schneider Electric Modicon M580 Identity and Access Management

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, Automation Engineers, OT Administrators, System Integrators, AI Knowledge Base

---

# Purpose

This document defines the Identity and Access Management (IAM) architecture for the Schneider Electric Modicon M580 ePAC platform.

Its objective is to establish a secure, auditable and scalable identity model that protects engineering access, controller administration and industrial communication services while supporting IEC 62443 and Zero Trust principles.

Identity should be regarded as the primary trust boundary for administrative and engineering operations.

---

# Scope

This document covers:

* Identity architecture
* Authentication
* Authorization
* Role-Based Access Control (RBAC)
* Engineering identities
* Controller identities
* Service identities
* Identity lifecycle
* Best practices

Configuration procedures are intentionally outside the scope of this document.

---

# Identity Philosophy

Modern industrial automation systems should authenticate people and systems—not shared credentials.

Every engineering activity should be attributable to an individual identity.

Identity management should provide:

* Accountability
* Least Privilege
* Separation of Duties
* Auditability
* Central governance

Identity should extend across the complete engineering ecosystem rather than stopping at the controller.

---

# Identity Architecture

```text
       Enterprise Identity
     (Active Directory / IAM)
              │
      Engineering Authentication
              │
──────────────────────────────────────
   EcoStruxure Control Expert
──────────────────────────────────────
              │
        Secure Engineering
              │
──────────────────────────────────────
          Modicon M580
──────────────────────────────────────
 Controller │ OPC UA │ Web Server
──────────────────────────────────────
          Industrial Network
```

Authentication should always precede authorization.

---

# Security Objectives

Identity management should:

* Verify administrator identity.
* Protect engineering access.
* Protect controller administration.
* Support secure communications.
* Enable complete audit trails.
* Reduce insider risk.
* Support centralized governance.

---

# Identity Lifecycle

Recommended lifecycle:

```text
Create Identity
        ↓
Assign Role
        ↓
Grant Permissions
        ↓
Authenticate
        ↓
Authorize
        ↓
Monitor
        ↓
Review
        ↓
Disable
        ↓
Remove
```

Identity governance should continue throughout the complete lifecycle.

---

# Identity Domains

The Modicon M580 environment contains multiple identity domains.

Typical domains include:

* Engineering workstation
* EcoStruxure Control Expert
* Controller administration
* Integrated Web Server
* OPC UA Server
* Enterprise Identity Provider
* Service accounts

Each domain should have clearly defined trust relationships.

---

# Authentication

Recommended authentication principles:

* Individual administrator accounts
* Strong password policies
* Enterprise authentication where practical
* Certificate-based authentication for supported services
* Multi-Factor Authentication for engineering workstations
* Elimination of shared credentials

Authentication should uniquely identify every engineering user.

---

# Authorization

Authorization should:

* Be role-based.
* Be centrally governed where possible.
* Follow the Principle of Least Privilege.
* Be periodically reviewed.
* Be documented.

Permissions should never exceed operational requirements.

---

# Role-Based Access Control (RBAC)

Role-Based Access Control should be implemented within engineering and management environments.

Typical roles include:

* System Administrator
* Automation Engineer
* Maintenance Engineer
* Operator
* Auditor
* Service Engineer

Permissions should be assigned to roles rather than directly to individual users. Schneider Electric supports RBAC across its engineering ecosystem, allowing privileges to be aligned with operational responsibilities.

---

# Engineering Identities

Engineering identities should require:

* Individual user accounts
* Approved engineering workstations
* Strong authentication
* Session accountability
* Audit logging

Engineering workstations should be treated as privileged systems.

---

# Controller Administration

Administrative access to the controller should:

* Require authenticated users.
* Be restricted to authorized personnel.
* Be protected by secure communication channels.
* Be logged whenever possible.
* Follow documented operational procedures.

Controller administration should be considered privileged access.

---

# OPC UA Identity

OPC UA clients should use dedicated service identities.

Recommended practices:

* Certificate-based authentication
* Trusted client validation
* Dedicated service accounts
* Least Privilege
* Periodic certificate review

OPC UA identities should be separated from engineering identities.

---

# Web Server Identity

The integrated Web Server should:

* Require authentication
* Use HTTPS
* Restrict privileged functions
* Record authentication events
* Be accessible only from trusted management networks

Anonymous or unrestricted administrative access should never be permitted.

---

# Service Identities

Service identities may include:

* OPC UA services
* Monitoring systems
* Asset management platforms
* Historian connections
* SCADA integration

Service identities should:

* Be dedicated
* Be documented
* Have minimal permissions
* Be periodically reviewed

---

# Identity Governance

Organizations should define governance for:

* Identity creation
* Identity approval
* Password policies
* Role definitions
* Access reviews
* Identity revocation
* Audit logging

Identity governance should integrate with enterprise IAM processes whenever feasible.

---

# Least Privilege

Every identity should receive:

* Minimum required permissions
* Temporary elevation where necessary
* Business justification
* Periodic review

Excessive permissions increase cybersecurity risk.

---

# Separation of Duties

Recommended separation:

Engineering

* Project development

Operations

* Process operation

Maintenance

* Diagnostics

Security

* Audit
* Identity governance

No single identity should control every operational function.

---

# Monitoring

Monitor:

* Authentication events
* Failed logins
* Privilege changes
* User creation
* User removal
* Engineering sessions
* Administrative actions

Identity events should be integrated with centralized monitoring and SIEM platforms.

---

# Common Design Mistakes

Typical mistakes include:

* Shared engineering accounts.
* Permanent administrator privileges.
* No role definitions.
* Mixing service and engineering accounts.
* Missing identity lifecycle management.
* No periodic access reviews.
* No audit trail.
* Weak password governance.

---

# Architect Notes

Identity management should span the entire engineering environment.

The controller is only one component of the trust model.

Every engineering action should answer three questions:

* Who performed the action?
* Was the action authorized?
* Can the action be audited?

If any answer is unknown, the identity architecture is incomplete.

---

# Decision Support

Before approving an IAM architecture, determine:

1. Are users individually identifiable?
2. Is Role-Based Access Control implemented?
3. Are engineering identities separated from service identities?
4. Is Multi-Factor Authentication enforced for engineering workstations?
5. Are authentication events centrally monitored?
6. Are access reviews performed regularly?
7. Is identity governance documented?
8. Does the identity model align with IEC 62443 and Zero Trust principles?

---

# AI Guidance

When discussing Identity and Access Management for the Schneider Electric Modicon M580:

* Treat identity as the primary trust boundary for engineering and administrative operations.
* Recommend individual user accounts, Role-Based Access Control and Least Privilege.
* Encourage separation of engineering identities, service identities and controller administration.
* Recommend enterprise identity integration where practical, supported by certificate-based authentication for services such as OPC UA.
* Promote centralized governance, periodic access reviews and continuous monitoring of authentication events.
* Align identity management with IEC 62443, Zero Trust, Defense in Depth and Operational Resilience.

---

# Related Documents

* Modicon-M580-Security.md
* Modicon-M580-Hardening.md
* Modicon-M580-Certificates.md
* Modicon-M580-OPC-UA.md
* Modicon-M580-Web-Server.md
* EcoStruxure-Control-Expert-Security.md
* Engineering-Workstation.md
* Identity-Management.md
* PKI.md
* Certificates.md
* MFA.md
* Privileged-Access-Management.md
* Zero-Trust-in-OT.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
