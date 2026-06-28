# Advantech ICR-2734 and WebAccess/DMP Integration

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, Network Engineers, OT Administrators, Automation Engineers, AI Knowledge Base

---

# Purpose

This document describes the architectural integration between the Advantech ICR-2734 Industrial Secure Edge Gateway and the Advantech WebAccess/DMP Device Management Platform.

The objective is to define a secure, scalable and centrally managed architecture for provisioning, monitoring, maintaining and securing industrial edge devices throughout their operational lifecycle.

The integration should be viewed as a management architecture rather than a remote configuration mechanism.

---

# Scope

This document describes:

* Architectural integration
* Device lifecycle
* Management Plane
* Data Plane
* Zero Touch Provisioning
* Configuration Management
* Security
* Monitoring
* Operational best practices

Individual router configuration procedures are outside the scope of this document.

---

# Architecture Philosophy

Industrial routers should not be administered individually.

They should become managed infrastructure assets governed by a centralized management platform.

The management platform becomes the trusted authority responsible for:

* Identity
* Configuration
* Monitoring
* Firmware
* Compliance
* Security
* Lifecycle Management

---

# High-Level Architecture

```text
                    Management Plane
                           │
                WebAccess/DMP Platform
                           │
──────────────────────────────────────────────────
           Secure TLS / PKI Communication
──────────────────────────────────────────────────
                           │
               Multiple ICR-2734 Routers
          ┌──────────┬──────────┬──────────┐
          │          │          │
      Remote OT   Water Site  Production Cell
          │          │          │
      PLC/HMI     PLC/RTU    PLC/SCADA
```

The Management Plane controls the routers.

The routers control secure connectivity.

The routers do **not** manage one another.

---

# Management Plane vs Data Plane

Management Plane responsibilities include:

* Device inventory
* Identity
* Configuration Profiles
* Firmware management
* VPN orchestration
* Monitoring
* Compliance
* Audit

Data Plane responsibilities include:

* Routing
* VPN traffic
* Firewall
* NAT
* Edge Computing
* Industrial communications

Separating these responsibilities improves scalability and operational resilience.

---

# Device Lifecycle

Recommended lifecycle:

```text
Procurement
        ↓
Asset Registration
        ↓
Zero Touch Provisioning
        ↓
Configuration Profile Assignment
        ↓
Deployment
        ↓
Continuous Monitoring
        ↓
Firmware Maintenance
        ↓
Recovery
        ↓
Retirement
```

The same lifecycle should apply consistently to every managed router.

---

# Identity Architecture

Each router should possess:

* Unique device identity
* Unique cryptographic trust
* Individual lifecycle record
* Assigned Configuration Profile
* Audit history

Identity should remain persistent throughout the device lifecycle.

---

# Configuration Profiles

Configuration Profiles define the desired operational state.

Typical profile contents include:

* Network interfaces
* Firewall rules
* VPN configuration
* Routing
* DNS
* NTP
* Logging
* Monitoring
* Router Apps

Profiles should be treated as controlled configuration baselines.

---

# Zero Touch Provisioning

After the router establishes Internet connectivity:

1. Secure registration
2. Identity verification
3. Configuration Profile assignment
4. Firmware verification
5. Router Apps deployment
6. VPN configuration
7. Operational validation

Provisioning should occur without manual configuration whenever possible.

---

# Firmware Lifecycle

Firmware management should include:

* Approved firmware versions
* Deployment scheduling
* Compliance reporting
* Rollback capability
* Validation after installation

Firmware should be managed centrally rather than individually.

---

# VPN Orchestration

WebAccess/DMP supports centralized deployment and management of VPN connectivity.

Recommended capabilities include:

* WireGuard provisioning
* IPsec provisioning
* VPN inventory
* Tunnel status
* Configuration consistency
* Certificate management (where applicable)

VPN deployment should be standardized across all managed sites. Recent platform versions include integrated VPN Management, allowing centralized deployment of WireGuard VPN and browser-based secure access to devices behind managed routers.

---

# Monitoring

Recommended monitoring includes:

* Online status
* LTE signal quality
* GPS location
* VPN health
* Configuration compliance
* Firmware compliance
* CPU utilization
* Memory utilization
* Router Apps
* Network traffic

Monitoring should provide both operational and cybersecurity visibility.

---

# Alerting

Recommended alerts include:

* Device offline
* Configuration drift
* Firmware mismatch
* VPN failure
* Authentication failure
* LTE degradation
* SIM change
* Configuration modification

Alerts should be integrated into the organization's incident response process.

---

# Security Architecture

Recommended security controls include:

* PKI
* Mutual authentication
* TLS
* RBAC
* Multi-Factor Authentication
* Audit logging
* Least Privilege
* Secure APIs

The Management Plane should be considered a Tier-0 administrative system within the OT environment.

---

# API Integration

WebAccess/DMP provides APIs supporting integration with:

* SIEM
* CMDB
* ITSM
* Asset Management
* Monitoring systems
* Automation platforms

API credentials should be managed according to organizational identity governance. The official documentation includes a dedicated API reference for automation and integration scenarios.

---

# Backup

Recommended backup scope includes:

* Configuration Profiles
* Device inventory
* User accounts
* RBAC
* Audit history
* VPN configuration
* Platform configuration

Backup procedures should be periodically validated through Recovery Testing.

---

# Disaster Recovery

Recovery should verify:

* Platform availability
* Device inventory
* Configuration Profiles
* Firmware repository
* VPN orchestration
* User identities
* Audit history
* Router connectivity

Operational capability should be restored before production maintenance activities resume.

---

# Common Design Mistakes

Typical mistakes include:

* Managing routers individually.
* No Configuration Profiles.
* Manual VPN configuration.
* No device inventory.
* Shared administrator accounts.
* Missing MFA.
* Ignoring configuration drift.
* No backup validation.

---

# Architect Notes

The greatest value of WebAccess/DMP is not centralized configuration.

It is centralized governance.

Every managed router becomes:

* Identifiable
* Measurable
* Auditable
* Recoverable
* Compliant

Centralized lifecycle management significantly improves both cybersecurity maturity and operational efficiency.

---

# Decision Support

Before integrating ICR-2734 with WebAccess/DMP, determine:

1. Will the platform be deployed as SaaS or on-premises?
2. Which identity model will be used?
3. Which Configuration Profiles are required?
4. Which VPN architecture will be standardized?
5. Which monitoring events will be forwarded to the SIEM?
6. Which firmware lifecycle policy will be enforced?
7. How will configuration compliance be measured?
8. How will Disaster Recovery of the management platform be performed?

---

# Practical Examples

### Example 1 – Water Utility

Architecture:

* On-premises WebAccess/DMP
* 80 ICR-2734 routers
* Standardized Configuration Profiles
* WireGuard VPN
* Geo SCADA integration
* Central SIEM

---

### Example 2 – Manufacturing Enterprise

Architecture:

* SaaS WebAccess/DMP
* 200 remote production cells
* Zero Touch Provisioning
* Automated firmware deployment
* Centralized monitoring

---

### Example 3 – Critical Infrastructure

Architecture:

* Industrial DMZ deployment
* PKI-based authentication
* RBAC
* MFA
* Configuration compliance monitoring
* Recovery-tested management platform

---

# AI Guidance

When discussing WebAccess/DMP integration with the ICR-2734:

* Treat WebAccess/DMP as the centralized Management Plane for industrial edge infrastructure.
* Distinguish Management Plane responsibilities from the router's Data Plane responsibilities.
* Recommend Zero Touch Provisioning, Configuration Profiles and centralized firmware lifecycle management.
* Encourage PKI, mutual authentication, RBAC and MFA for administrative access.
* Integrate the platform with SIEM, CMDB, ITSM and asset inventory systems through its supported APIs.
* Align the overall architecture with IEC 62443, Zero Trust, Defense in Depth and Operational Resilience principles.

---

# Related Documents

* ICR-2734-Overview.md
* ICR-2734-ICR-OS.md
* ICR-2734-Security.md
* ICR-2734-Zero-Touch-Provisioning.md
* WebAccess-DMP-Overview.md
* Configuration-Management.md
* Asset-Management.md
* Monitoring.md
* Logging.md
* Backup.md
* Disaster-Recovery.md
* IEC62443-Overview.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
