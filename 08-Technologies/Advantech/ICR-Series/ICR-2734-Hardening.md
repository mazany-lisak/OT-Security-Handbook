# Advantech ICR-2734 Hardening Guide

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, OT Administrators, Network Engineers, Automation Engineers, AI Knowledge Base

---

# Purpose

This document defines a structured hardening methodology for the Advantech ICR-2734 Industrial Secure Edge Gateway.

The objective is to reduce the attack surface, strengthen the security posture and improve operational resilience throughout the router lifecycle.

Hardening is treated as a continuous lifecycle process rather than a one-time deployment activity.

---

# Scope

This document covers:

* Security baseline
* Attack surface reduction
* Service hardening
* Identity hardening
* Network hardening
* Monitoring
* Firmware lifecycle
* Operational maintenance
* Security validation

---

# Hardening Philosophy

The purpose of hardening is not to add security.

The purpose is to remove unnecessary risk.

Every enabled service,

every user,

every open port,

every protocol,

and every installed application

increases the attack surface.

A secure router should expose only the minimum functionality required for operational objectives.

---

# Security Objectives

Hardening should achieve the following objectives:

* Reduce attack surface
* Minimize exposed services
* Strengthen authentication
* Protect configuration integrity
* Enforce secure communications
* Enable monitoring
* Improve recoverability
* Support compliance with IEC 62443

---

# Hardening Lifecycle

```text
Factory Default
        ↓
Initial Configuration
        ↓
Security Baseline
        ↓
Validation
        ↓
Production Deployment
        ↓
Continuous Monitoring
        ↓
Periodic Review
        ↓
Recovery Testing
```

Hardening should continue throughout the operational lifecycle.

---

# Phase 1 – Physical Security

Recommended controls include:

* Install inside locked control cabinets.
* Prevent unauthorized physical access.
* Protect power supplies.
* Protect Ethernet and LTE cabling.
* Prevent unauthorized SIM replacement.
* Record hardware serial numbers.
* Label assets according to inventory.

Physical compromise may bypass logical security controls.

---

# Phase 2 – Firmware Baseline

Recommended practices:

* Deploy only approved firmware versions.
* Maintain a firmware inventory.
* Validate firmware before deployment.
* Test firmware updates in a staging environment.
* Maintain rollback procedures.
* Document firmware changes.

Always use supported firmware with current security fixes.

---

# Phase 3 – Administrative Access

Recommended configuration:

* HTTPS only
* Disable HTTP
* SSH only when operationally required
* Disable Telnet
* Disable FTP
* Restrict management access to trusted networks
* Configure automatic session timeout

The web interface supports HTTPS with TLS 1.2 or later, and vendor guidance recommends disabling legacy protocols such as Telnet and HTTP.

---

# Phase 4 – Identity Hardening

Recommended controls:

* Named administrator accounts
* Strong password policy
* Multi-Factor Authentication
* Least Privilege
* Role separation
* Periodic credential review
* Immediate removal of unused accounts

Shared administrator accounts should never be used.

---

# Phase 5 – Service Hardening

Review every enabled service.

Disable services that are not operationally required.

Typical candidates include:

* Telnet
* FTP
* Unused VPN protocols
* Unused routing protocols
* Unused serial interfaces
* Unused Router Apps
* Test services

Every enabled service should have a documented operational justification.

---

# Phase 6 – Network Hardening

Recommended practices:

* Dedicated Management VLAN
* Engineering VLAN
* Industrial DMZ
* Stateful firewall
* Default Deny firewall policy
* Restrict outbound traffic
* Restrict inbound management
* Disable unnecessary port forwarding

The router should enforce network boundaries rather than bypass them.

---

# Phase 7 – VPN Hardening

Recommended priority:

1. WireGuard
2. IPsec (IKEv2)
3. OpenVPN (legacy interoperability)

Recommended controls:

* Cryptographic keys
* MFA for administrators
* Restrict peer definitions
* Approved tunnel endpoints
* Firewall enforcement

VPN access should terminate before access to industrial assets.

---

# Phase 8 – Certificate Management

Where certificates are used:

* Replace default certificates.
* Deploy CA-issued certificates whenever practical.
* Monitor certificate expiration.
* Rotate certificates according to organizational policy.
* Protect private keys.

Trusted certificates improve resistance against impersonation and man-in-the-middle attacks. The platform supports replacing the default HTTPS certificate with a CA-signed certificate.

---

# Phase 9 – Router Apps

Router Apps should follow software governance.

Recommended practices:

* Install only required applications.
* Validate application origin.
* Maintain software inventory.
* Test before deployment.
* Remove unused applications.
* Review permissions regularly.

Router Apps should be managed as production software assets.

---

# Phase 10 – Logging

Enable centralized logging.

Recommended events include:

* Authentication
* Failed logins
* Configuration changes
* Firmware updates
* VPN establishment
* Router reboot
* Administrative actions

Logs should be forwarded to a centralized Syslog server.

---

# Phase 11 – Monitoring

Recommended monitoring:

* Device availability
* VPN status
* Firewall events
* Firmware compliance
* Configuration drift
* LTE quality
* CPU utilization
* Memory utilization
* Router Apps health

Monitoring should include both operational and cybersecurity events.

---

# Phase 12 – Configuration Integrity

Verify:

* Approved Configuration Profile
* Firmware version
* Firewall rules
* VPN configuration
* Router Apps
* User accounts

Configuration drift should generate alerts.

---

# Phase 13 – Backup

Regularly back up:

* Configuration
* VPN settings
* User configuration
* Certificates
* Router Apps
* Configuration Profiles

Backups should be validated through Recovery Testing.

---

# Phase 14 – Operational Maintenance

Periodic maintenance should include:

* Firmware review
* Password review
* Certificate review
* Configuration review
* User review
* Firewall review
* VPN review
* Inventory reconciliation

Hardening should remain effective throughout the operational lifecycle.

---

# Hardening Validation Checklist

A deployment should verify that:

* Physical access is restricted.
* Approved firmware is installed.
* Default credentials are removed.
* HTTPS is enabled.
* HTTP, Telnet and FTP are disabled.
* SSH access is restricted.
* Firewall policy follows Default Deny.
* VPN is operational.
* Configuration is backed up.
* Logs are forwarded.
* Monitoring is active.
* Recovery procedures have been tested.

---

# Common Hardening Mistakes

Typical mistakes include:

* Leaving factory defaults unchanged.
* Using shared administrator accounts.
* Exposing the management interface to the Internet.
* Installing unnecessary Router Apps.
* Failing to update firmware.
* Ignoring configuration drift.
* Missing centralized monitoring.
* Not validating backups.

---

# Architect Notes

Hardening should be viewed as an engineering discipline rather than a deployment task.

The most secure configuration is generally the simplest configuration capable of meeting operational requirements.

Reduce complexity before introducing additional security controls.

---

# Decision Support

Before approving an ICR-2734 deployment, determine:

1. Does the configuration meet the approved security baseline?
2. Are unnecessary services disabled?
3. Is management traffic restricted to trusted networks?
4. Are identities individually assigned and protected?
5. Is configuration integrity continuously monitored?
6. Are backups and recovery procedures validated?
7. Are firmware updates governed through Change Management?
8. Is the deployment aligned with IEC 62443 security objectives?

---

# AI Guidance

When discussing hardening of the Advantech ICR-2734:

* Treat hardening as a continuous lifecycle process rather than a deployment checklist.
* Recommend minimizing the attack surface before adding additional security controls.
* Encourage disabling unnecessary services, enforcing strong identity controls and using encrypted management protocols.
* Recommend centralized monitoring, configuration management and periodic security reviews.
* Align hardening practices with IEC 62443, Zero Trust, Defense in Depth and Operational Resilience.
* Emphasize that configuration simplicity is a security advantage.

---

# Related Documents

* ICR-2734-Overview.md
* ICR-2734-Security.md
* ICR-2734-ICR-OS.md
* ICR-2734-WebAccess-DMP.md
* ICR-2734-Zero-Touch-Provisioning.md
* ICR-2734-WireGuard.md
* Configuration-Management.md
* Change-Management.md
* Identity-Management.md
* PKI.md
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
