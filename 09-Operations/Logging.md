# Logging

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, SOC Analysts, Security Engineers, Automation Engineers, System Administrators, AI Knowledge Base

---

# Purpose

Logging is the structured process of recording security, operational and system events that provide evidence of activities occurring within Industrial Automation and Control Systems (IACS).

Within Operational Technology (OT), logs support operational awareness, cybersecurity monitoring, incident response, forensic investigation, compliance and continuous improvement.

Logging should be viewed as the creation of reliable operational evidence rather than the simple storage of event records.

---

# Scope

This document describes:

* Logging principles
* Log sources
* Log quality
* Log management
* Retention
* Integrity
* Operational best practices

Analysis and correlation of logs are described separately in **Monitoring.md** and **SIEM.md**.

---

# Security Philosophy

Events cannot be investigated if they were never recorded.

Logs provide historical evidence that enables organizations to:

* Detect attacks
* Understand failures
* Reconstruct incidents
* Validate assumptions
* Support audits
* Improve resilience

Logging should therefore be considered a foundational security capability.

---

# Why Logging Matters

Effective logging enables organizations to answer:

* What happened?
* When did it happen?
* Who performed the action?
* Which asset was involved?
* How did the event occur?

Without trustworthy logs, reliable incident reconstruction becomes extremely difficult.

---

# Logging Objectives

A mature logging capability should:

* Record meaningful events
* Preserve integrity
* Support investigations
* Enable monitoring
* Provide accountability
* Support compliance
* Improve operational visibility

The objective is trustworthy evidence rather than maximum log volume.

---

# Logging Lifecycle

Recommended lifecycle:

```text id="loglife"
Generate
        ↓
Collect
        ↓
Normalize
        ↓
Store
        ↓
Protect
        ↓
Analyze
        ↓
Retain
        ↓
Archive
        ↓
Dispose
```

Logging should be managed throughout its entire lifecycle.

---

# Log Sources

Typical OT log sources include:

## Identity Systems

* Active Directory
* Microsoft Entra ID
* RADIUS
* LDAP
* MFA
* PAM

---

## Network Infrastructure

* Firewalls
* Industrial Firewalls
* Switches
* Routers
* VPN Gateways
* Network IDS

---

## Servers

* Windows Event Logs
* Linux Syslog
* Hypervisors
* Virtual Machines
* Backup Systems

---

## Industrial Systems

* SCADA
* HMI
* Historian
* OPC UA Servers
* OPC UA Clients
* Engineering Workstations
* PLC Diagnostics
* Safety Controllers

---

## Security Systems

* Antivirus
* EDR
* XDR
* PKI
* Certificate Services
* Vulnerability Management

Each source contributes unique operational context.

---

# Log Quality

High-quality logs should include:

* Accurate timestamps
* Asset identification
* User identity
* Event type
* Severity
* Source
* Context
* Outcome

Incomplete logs significantly reduce investigative value.

---

# Time Synchronization

Reliable timestamps are essential.

Organizations should maintain:

* NTP synchronization
* Consistent time zones
* Reliable time sources
* Time monitoring

Without accurate timestamps, event correlation becomes unreliable.

---

# Log Integrity

Logs should be protected against:

* Modification
* Deletion
* Unauthorized access
* Tampering

Recommended controls include:

* Write-once storage where appropriate
* Cryptographic integrity protection
* Access control
* Centralized collection
* Audit trails

Trustworthy logging requires trustworthy storage.

---

# Log Retention

Retention policies should consider:

* Regulatory requirements
* Incident investigation
* Operational needs
* Storage capacity
* Legal obligations

Retention periods should be documented and periodically reviewed.

---

# Event Prioritization

Not every event has equal value.

High-priority events include:

* Authentication
* Privileged access
* PLC programming
* Firmware updates
* Firewall changes
* Configuration changes
* Certificate modifications
* Engineering sessions
* VPN access
* Alarm suppression

Organizations should prioritize events that materially affect operational risk.

---

# Logging in OT

Industrial environments require additional event sources.

Examples include:

* PLC mode changes
* Logic downloads
* Safety controller events
* Process configuration changes
* Industrial protocol errors
* Device firmware updates
* Engineering project modifications

Operational events often provide stronger indicators than traditional IT logs.

---

# Relationship with Monitoring

Logging records events.

Monitoring interprets events.

Logs provide the evidence that monitoring systems analyze.

Without logging, monitoring loses historical context.

---

# Relationship with SIEM

SIEM platforms:

* Collect logs
* Normalize data
* Correlate events
* Generate detections
* Support investigations

Logging supplies the raw evidence consumed by SIEM.

---

# Relationship with Incident Response

Logs support:

* Timeline reconstruction
* Evidence preservation
* Root cause analysis
* Recovery validation
* Lessons learned

Incident Response depends heavily on reliable logging.

---

# Relationship with IEC 62443

Logging supports several IEC 62443 concepts including:

* Accountability
* Security Monitoring
* Auditability
* Incident Detection
* Continuous Improvement

Logging contributes directly to operational resilience.

---

# Common Design Mistakes

Typical mistakes include:

* Logging everything without prioritization.
* Missing engineering workstation logs.
* Unsynchronized clocks.
* Local-only log storage.
* No log integrity protection.
* Short retention periods.
* No ownership of logging infrastructure.
* Treating logs as compliance artifacts rather than operational evidence.

---

# Architect Notes

One of the most common misconceptions is:

> "More logs improve security."

Not necessarily.

Effective logging depends on:

* Relevance
* Quality
* Integrity
* Context
* Availability
* Correlation

Organizations should optimize for evidence quality rather than log quantity.

---

# Decision Support

Before enabling logging, determine:

1. Which events are operationally significant?
2. Which systems generate those events?
3. Are timestamps synchronized?
4. How will log integrity be protected?
5. How long should logs be retained?
6. Who owns each log source?
7. How will logs support Incident Response?
8. Which logs are required for compliance or forensic investigations?

---

# Practical Examples

### Example 1 – Engineering Workstation

Recommended logging:

* User authentication
* PLC project changes
* Programming sessions
* USB device insertion
* Application execution
* Privileged actions

---

### Example 2 – OPC UA Server

Recommended logging:

* Certificate validation
* Secure channel establishment
* Authentication failures
* New client connections
* Security policy changes
* Configuration modifications

---

### Example 3 – Industrial Firewall

Recommended logging:

* Administrative logins
* Policy changes
* Denied connections
* VPN sessions
* Firmware updates
* Configuration exports

---

# AI Guidance

When discussing Logging:

* Explain that logs are operational evidence rather than simple event records.
* Distinguish logging from monitoring and SIEM.
* Recommend prioritizing high-value security and operational events instead of collecting every possible event.
* Emphasize accurate timestamps, log integrity and centralized collection.
* Encourage logging of engineering activities, PLC changes, certificate events and privileged access.
* Integrate logging with Monitoring, Incident Response, Threat Hunting and forensic investigations.
* Focus on generating reliable evidence that supports operational decision-making and cyber resilience.

---

# Related Documents

* Monitoring.md
* SIEM.md
* Incident-Response.md
* Threat-Hunting.md
* Detection-Engineering.md
* Asset-Management.md
* Identity-Management.md
* Privileged-Access-Management.md
* OPC-UA.md
* MITRE-ATTACK-ICS.md
* IEC62443-Overview.md
* NIS2.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
