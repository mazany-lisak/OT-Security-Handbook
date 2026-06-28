# Monitoring

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, SOC Analysts, Security Engineers, Automation Engineers, AI Knowledge Base

---

# Purpose

Monitoring is the continuous process of collecting, analyzing and correlating operational and security-related information to detect abnormal behavior, validate security assumptions and support informed decision-making.

Within Operational Technology (OT), monitoring is not limited to cybersecurity.

It also supports:

* Human safety
* Process stability
* Operational continuity
* Equipment integrity
* Regulatory compliance

The objective is to identify meaningful deviations from trusted operational behavior as early as possible.

---

# Scope

This document describes:

* Monitoring principles
* OT-specific monitoring
* Security monitoring
* Operational monitoring
* Detection strategies
* Telemetry sources
* Governance
* Continuous improvement

Detailed log management is described separately in **Logging.md**.

---

# Security Philosophy

No preventive security control is perfect.

Architectural assumptions continuously change because of:

* Configuration changes
* New vulnerabilities
* Insider activity
* Vendor maintenance
* Operational modifications
* Cyber attacks

Monitoring continuously validates whether the environment remains in its intended trusted state.

---

# Why Monitoring Matters

Monitoring enables organizations to:

* Detect attacks
* Detect failures
* Validate security controls
* Detect configuration drift
* Improve incident response
* Support forensic investigations
* Improve operational resilience

Organizations cannot effectively protect assets they cannot observe.

---

# Monitoring Objectives

A mature monitoring capability should:

* Detect abnormal activity
* Detect unauthorized changes
* Validate security assumptions
* Support rapid response
* Reduce attacker dwell time
* Improve operational visibility
* Provide actionable information

Monitoring should enable decisions rather than simply generate alerts.

---

# Monitoring Lifecycle

Recommended lifecycle:

```text id="monlife"
Collect
        ↓
Normalize
        ↓
Correlate
        ↓
Detect
        ↓
Investigate
        ↓
Respond
        ↓
Improve Detection
```

Monitoring should evolve continuously.

---

# Monitoring Domains

Monitoring in OT generally includes multiple domains.

## Security Monitoring

Examples:

* Authentication
* Authorization
* Privileged activity
* VPN sessions
* Certificate changes
* Firewall events
* Malware detection

---

## Network Monitoring

Examples:

* Protocol activity
* Network flows
* Unexpected communication
* New devices
* Network segmentation violations
* Bandwidth anomalies

---

## System Monitoring

Examples:

* Server health
* CPU utilization
* Memory
* Disk usage
* Service failures
* Configuration changes

---

## OT Process Monitoring

Examples:

* PLC mode changes
* Logic downloads
* Firmware updates
* Unexpected parameter changes
* Safety events
* Alarm suppression
* Engineering sessions

Operational telemetry is often more valuable than endpoint telemetry.

---

# Telemetry Sources

Common telemetry sources include:

* Firewalls
* Active Directory
* Microsoft Entra ID
* VPN gateways
* Industrial firewalls
* PLC diagnostics
* Engineering workstations
* SCADA systems
* Historians
* Hypervisors
* OPC UA Servers
* Syslog
* Windows Event Logs
* Linux logs
* Network IDS
* OT IDS
* NetFlow
* Certificate Services

No individual telemetry source provides complete visibility.

---

# Detection Strategy

Effective detection combines:

* Signature-based detection
* Behavioral detection
* Anomaly detection
* Threat intelligence
* Correlation rules
* Manual threat hunting

Multiple independent detection methods improve resilience.

---

# Monitoring by Architecture Layer

Recommended focus areas:

| Architecture Layer | Typical Monitoring                      |
| ------------------ | --------------------------------------- |
| Identity           | Authentication, MFA, PAM                |
| Network            | Flows, Segmentation, Firewalls          |
| Systems            | Configuration, Health, Changes          |
| Applications       | OPC UA, SCADA, Historians               |
| Controllers        | Programming, Firmware, Mode Changes     |
| Safety             | SIS Events, Interlocks, Critical Alarms |

Each architectural layer contributes different telemetry.

---

# Detection Priorities

High-value detection opportunities include:

* New privileged accounts
* Failed MFA attempts
* PLC programming
* Engineering workstation logins
* Vendor remote access
* Firewall policy changes
* Certificate replacement
* Unexpected firmware updates
* Configuration drift
* New network paths

Monitoring should prioritize behaviors with the highest operational impact.

---

# Threat Hunting

Monitoring should support proactive investigation.

Examples include:

* Unexpected engineering sessions
* Rare protocol usage
* Unusual VPN activity
* Long-running privileged sessions
* New trust relationships
* Abnormal communication patterns

Threat hunting complements automated detection.

---

# Monitoring Quality

Effective monitoring requires:

* High-quality telemetry
* Accurate timestamps
* Time synchronization
* Asset identification
* Context enrichment
* False-positive reduction

Poor-quality telemetry reduces detection effectiveness.

---

# Relationship with MITRE ATT&CK

Monitoring enables detection of attacker techniques.

Examples include:

* Initial Access
* Discovery
* Lateral Movement
* Credential Access
* Persistence
* Controller Manipulation

Detection strategies should map to relevant ATT&CK techniques where practical.

---

# Relationship with Incident Response

Monitoring provides:

* Early detection
* Incident timelines
* Evidence
* Response prioritization
* Recovery validation

Incident Response depends on effective monitoring.

---

# Relationship with Defense in Depth

Monitoring represents one of the independent security layers.

While preventive controls attempt to stop attacks, monitoring identifies attacks that bypass preventive measures.

Defense in Depth requires both prevention and detection.

---

# Relationship with Zero Trust

Zero Trust assumes trust should be continuously evaluated.

Monitoring validates:

* Identity behavior
* Device trust
* Session activity
* Policy compliance
* Trust relationships

Continuous verification depends on continuous monitoring.

---

# Relationship with IEC 62443

Monitoring supports several IEC 62443 concepts including:

* Security Monitoring
* Accountability
* System Integrity
* Continuous Improvement
* Incident Detection

Monitoring contributes directly to cyber resilience.

---

# Common Design Mistakes

Typical mistakes include:

* Monitoring only IT assets.
* Ignoring engineering workstations.
* Collecting logs without analysis.
* Monitoring without asset inventory.
* Excessive alert volume.
* Missing time synchronization.
* No validation of detection rules.
* Monitoring only cybersecurity while ignoring process behavior.

---

# Architect Notes

One of the most common misconceptions is:

> "More logs equal better monitoring."

Not necessarily.

Monitoring quality depends on:

* Relevance
* Context
* Correlation
* Detection logic
* Response capability

Architectures should optimize for actionable visibility rather than maximum telemetry volume.

---

# Decision Support

Before introducing new monitoring capabilities, determine:

1. Which assets are most critical?
2. Which attacker behaviors should be detected?
3. Which telemetry sources already exist?
4. Which trust assumptions require validation?
5. Which detections support Incident Response?
6. How will false positives be managed?
7. How will monitoring effectiveness be measured?
8. How will detection rules be continuously improved?

---

# Practical Examples

### Example 1 – Engineering Workstation

Recommended monitoring:

* User authentication
* PAM sessions
* PLC programming
* USB device usage
* Configuration changes
* Network connections

---

### Example 2 – OPC UA Server

Recommended monitoring:

* Certificate validation failures
* New trusted certificates
* Failed secure channel creation
* Unexpected client connections
* Security policy changes

---

### Example 3 – Industrial Firewall

Recommended monitoring:

* Policy changes
* New communication paths
* Denied connections
* VPN sessions
* Administrative logins
* Firmware changes

---

# AI Guidance

When discussing Monitoring:

* Explain that monitoring is a continuous validation process rather than simple log collection.
* Distinguish operational monitoring from security monitoring while emphasizing their interdependence.
* Recommend monitoring based on asset criticality and architectural risk.
* Encourage correlating telemetry from multiple independent sources.
* Prioritize engineering workstations, privileged identities and industrial control systems as high-value monitoring targets.
* Integrate monitoring with MITRE ATT&CK, Incident Response, Zero Trust and Defense in Depth.
* Focus on actionable detections that support timely and informed operational decisions.

---

# Related Documents

* Logging.md
* Incident-Response.md
* Backup.md
* Risk-Based-Security.md
* Defense-in-Depth.md
* Zero-Trust-in-OT.md
* Identity-Management.md
* Privileged-Access-Management.md
* MITRE-ATTACK-ICS.md
* ICS-Kill-Chain.md
* OPC-UA.md
* Network-Segmentation.md
* IEC62443-Overview.md
* NIS2.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
