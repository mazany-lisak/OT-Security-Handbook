# Advantech ICR-OS

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, Network Engineers, Automation Engineers, Software Engineers, AI Knowledge Base

---

# Purpose

ICR-OS is the Linux-based operating system powering Advantech Industrial Cellular Routers, including the ICR-2734.

Rather than acting solely as firmware for a networking device, ICR-OS provides a secure industrial edge computing platform that combines networking, cybersecurity, automation and application hosting within a single operating environment.

This document describes the architecture, capabilities and security principles of ICR-OS.

---

# Scope

This document describes:

* ICR-OS architecture
* Core operating system capabilities
* Networking
* Security
* Router Apps
* Edge Computing
* Lifecycle management
* Operational best practices

Detailed router configuration procedures are outside the scope of this document.

---

# Platform Philosophy

ICR-OS should be regarded as an industrial edge operating system.

Its responsibilities include:

* Secure communications
* Network routing
* VPN termination
* Firewall enforcement
* Device management
* Application hosting
* Edge processing
* Remote diagnostics

The operating system transforms the router into an industrial edge platform rather than a simple communication device. ICR-OS is built on the Linux kernel and combines a web-based management interface with an open platform for Router Apps and custom development.

---

# Platform Architecture

```text
WebAccess/DMP
        │
Management APIs
        │
──────────────────────────────
ICR-OS
──────────────────────────────
Networking
VPN
Firewall
Authentication
Logging
Router Apps
Linux Services
──────────────────────────────
Hardware Platform
──────────────────────────────
Ethernet
LTE
USB
Serial
GPIO (selected models)
```

ICR-OS provides a modular software architecture that separates platform services from customer applications.

---

# Linux Foundation

ICR-OS is based on Linux.

The platform provides:

* Standard Linux services
* Open development environment
* Process isolation
* Networking stack
* File system
* Service management

This architecture enables long-term extensibility while maintaining industrial reliability.

---

# Core Capabilities

ICR-OS provides:

* Industrial networking
* Routing
* VPN services
* Firewall
* NAT
* VLAN support
* DHCP
* SNMP
* QoS
* Dynamic routing (through supported features)
* Logging
* Diagnostics
* Event automation
* Firmware management

These services form the operational foundation of the platform.

---

# Networking

Supported networking capabilities include:

* IPv4
* IPv6 Dual Stack
* VLAN
* NAT/PAT
* Port Forwarding
* VRRP
* DHCP
* DNS Proxy
* QoS
* Backup Routes
* Ethernet Bridging
* Load Balancing
* Dynamic routing (RIP, OSPF, BGP and others where supported)

Network design should follow IEC 62443 zoning and conduit principles.

---

# VPN Services

ICR-OS supports multiple secure communication technologies.

Recommended order:

1. WireGuard
2. IPsec (IKEv2)
3. OpenVPN

Additional capabilities include:

* GRE
* L2TP
* DMVPN (through supported functionality)

VPN technology should be selected according to the organization's security architecture.

---

# Router Apps

Router Apps extend the functionality of ICR-OS.

Typical applications include:

* Industrial protocol gateways
* MQTT connectivity
* Cloud integration
* Security enhancements
* Layer 2 Firewall
* HTTP Authentication
* Digital I/O control
* Custom applications

Applications should be installed only when operationally required and managed as software assets.

---

# Edge Computing

ICR-OS enables edge processing through:

* Python
* C
* C++
* Bash scripting
* Linux services

Typical use cases include:

* Local protocol conversion
* Data filtering
* Alarm preprocessing
* Local automation
* Cloud integration
* Telemetry aggregation

Processing data locally can reduce bandwidth consumption and improve operational resilience.

---

# Security Architecture

ICR-OS provides security capabilities including:

* HTTPS
* SSH
* SFTP
* Firewall
* IP filtering
* MAC filtering
* WireGuard
* IPsec
* OpenVPN
* RADIUS
* TACACS+
* Two-Factor Authentication
* SCEP (where supported)

The Secure S1 edition additionally provides Secure Boot, encrypted firmware, integrity checking, a read-only filesystem and alignment with IEC 62443-4-2 Security Level 1.

---

# Authentication

Recommended authentication architecture:

* Named administrator accounts
* MFA
* RADIUS
* TACACS+
* Least Privilege
* Role separation

Shared administrative accounts should never be used.

---

# Event Engine

The built-in Event Engine can automate responses based on:

* Interface state
* Signal strength
* Timer events
* Device temperature
* Power state
* Data usage
* Digital inputs

Typical actions include:

* Execute scripts
* Send SNMP traps
* Send email notifications
* Generate logs
* Trigger Router Apps

Automation should reduce manual operational tasks while remaining deterministic.

---

# Diagnostics

ICR-OS provides:

* System Log
* Kernel Log
* Reboot Log
* Signal strength
* Routing table
* Configuration reports
* Remote diagnostics (SSH)

Diagnostics should be integrated with centralized monitoring and SIEM platforms.

---

# Firmware Management

Recommended practices include:

* Approved firmware baseline
* Controlled upgrades
* Rollback procedures
* Change Management
* Version inventory
* Recovery validation

Firmware should be updated through a controlled lifecycle using WebAccess/DMP where possible.

---

# Integration with WebAccess/DMP

ICR-OS integrates natively with WebAccess/DMP for:

* Zero Touch Provisioning
* Configuration Profiles
* Firmware deployment
* Monitoring
* VPN orchestration
* Device inventory
* Remote diagnostics

Centralized lifecycle management reduces configuration drift and operational risk.

---

# Common Design Mistakes

Typical mistakes include:

* Treating ICR-OS as closed firmware rather than an operating system.
* Installing unnecessary Router Apps.
* Allowing unrestricted SSH access.
* Using shared administrator accounts.
* Not managing Router Apps through Change Management.
* Failing to update firmware.
* Ignoring centralized monitoring.
* Running custom applications without validation.

---

# Architect Notes

ICR-OS is one of the most flexible industrial router operating systems currently available.

Its openness enables advanced edge computing capabilities but also increases architectural responsibility.

Every additional service or Router App expands the attack surface.

Architects should therefore prioritize:

* Minimal attack surface
* Secure configuration
* Controlled software lifecycle
* Identity-first administration
* Continuous monitoring

Operational flexibility should never compromise security.

---

# Decision Support

Before deploying ICR-OS, determine:

1. Which Router Apps are truly required?
2. Will custom software be developed?
3. Which VPN technology is preferred?
4. Which authentication source will be used?
5. How will firmware updates be governed?
6. Which logs will be forwarded to the SIEM?
7. Which Event Engine automations are appropriate?
8. How will configuration compliance be monitored?

---

# AI Guidance

When discussing ICR-OS:

* Treat ICR-OS as an industrial edge operating system rather than router firmware.
* Recommend minimizing installed services and Router Apps to reduce the attack surface.
* Encourage centralized lifecycle management through WebAccess/DMP.
* Recommend secure authentication, VPN-first remote access, centralized monitoring and controlled firmware management.
* Integrate ICR-OS into architectures based on IEC 62443, Zero Trust, Defense in Depth and Operational Resilience.
* Emphasize that Router Apps are software assets requiring the same governance, testing and lifecycle management as any other production software.

---

# Related Documents

* ICR-2734-Overview.md
* ICR-2734-Security.md
* ICR-2734-WireGuard.md
* ICR-2734-Router-Apps.md
* WebAccess-DMP-Overview.md
* Zero-Trust-in-OT.md
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
