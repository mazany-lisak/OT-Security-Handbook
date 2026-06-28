# MITRE ATT&CK for ICS

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, SOC Analysts, Incident Responders, Automation Engineers, AI Knowledge Base

---

# Purpose

MITRE ATT&CK for ICS is a publicly available knowledge base describing adversary tactics, techniques and procedures (TTPs) observed in Industrial Control Systems (ICS).

Unlike traditional enterprise threat models, ATT&CK for ICS focuses on attacks targeting industrial processes, operational technology and physical infrastructure.

The framework is intended to help organizations:

* understand attacker behaviour,
* improve detection,
* validate security architecture,
* prioritize defensive controls,
* support incident response,
* improve cyber resilience.

This document explains how to apply the framework in OT architecture rather than reproducing the ATT&CK knowledge base.

---

# Scope

This document covers:

* ATT&CK for ICS concepts
* Tactics
* Techniques
* Detection strategy
* Mitigation strategy
* Architecture mapping
* Risk assessment
* Incident response
* Threat modelling

Specific ATT&CK techniques should always be referenced from the official MITRE knowledge base.

---

# Why ATT&CK for ICS Matters

Traditional vulnerability management answers:

> *What vulnerabilities exist?*

ATT&CK answers:

> *How do attackers actually operate?*

Understanding attacker behaviour allows architects to design security controls that interrupt realistic attack paths rather than theoretical ones.

---

# Framework Philosophy

ATT&CK is **not** a checklist.

It is a behavioural model.

The framework should be used to:

* understand adversary objectives,
* identify defensive gaps,
* improve detection,
* prioritize investments,
* validate architecture.

The objective is improving resilience rather than achieving complete coverage.

---

# ICS vs Enterprise ATT&CK

Enterprise ATT&CK focuses primarily on information systems.

ATT&CK for ICS focuses on operational processes.

Key differences include:

| Enterprise          | ICS                         |
| ------------------- | --------------------------- |
| Data theft          | Process manipulation        |
| User endpoints      | PLCs and controllers        |
| Business disruption | Physical process disruption |
| Confidentiality     | Safety and availability     |
| IT infrastructure   | Industrial infrastructure   |

The consequences of successful attacks are therefore fundamentally different.

---

# Attack Lifecycle

Although attack paths vary, many ICS attacks follow a common progression:

```text
Initial Access
        ↓
Execution
        ↓
Persistence
        ↓
Privilege Escalation
        ↓
Discovery
        ↓
Lateral Movement
        ↓
Collection
        ↓
Command & Control
        ↓
Inhibit Response Function
        ↓
Impair Process Control
        ↓
Impact
```

The earlier an attack is detected, the lower the operational impact.

---

# Architectural Perspective

ATT&CK should influence architecture rather than only incident response.

Questions architects should ask include:

* Which attack techniques are realistic?
* Which assets are most attractive?
* Which trust boundaries exist?
* Which communications should be monitored?
* Which identities are privileged?
* Which engineering workstations require additional protection?

Threat modelling should begin during system design.

---

# Detection Strategy

Detection should combine multiple telemetry sources.

Typical sources include:

* Firewall logs
* Windows Event Logs
* Linux logs
* VPN logs
* Active Directory
* Engineering workstation logs
* PLC diagnostics
* Industrial IDS
* NetFlow
* Syslog
* OPC UA security events
* Switch logs

No single telemetry source provides complete visibility.

---

# Mitigation Strategy

Effective mitigation combines:

* Network Segmentation
* Zones and Conduits
* Least Privilege
* Secure Remote Access
* MFA
* Monitoring
* Logging
* Backup
* Change Management
* Incident Response

Multiple independent controls increase resilience against diverse attack techniques.

---

# Mapping to IEC 62443

ATT&CK describes attacker behaviour.

IEC 62443 describes defensive engineering practices.

Typical relationship:

```text
ATT&CK Technique
        ↓
Risk Assessment
        ↓
IEC 62443 Requirement
        ↓
Architecture
        ↓
Technical Control
```

Using both frameworks together provides a balanced view of offensive and defensive security.

---

# Mapping to Purdue Model

Threat exposure varies across Purdue Levels.

Typical considerations:

Level 4–5

Enterprise threats

Identity attacks

Email

Cloud

---

Level 3

Engineering

Historians

Patch Management

---

Level 2

SCADA

HMIs

Controllers

---

Level 1

Remote I/O

PLCs

---

Level 0

Physical Process

Sensors

Actuators

Different levels require different detection strategies.

---

# Relationship with Vulnerability Management

Vulnerability management answers:

"What is vulnerable?"

ATT&CK answers:

"How could an attacker exploit the environment?"

Both perspectives are required.

High CVSS scores do not automatically imply high operational risk.

Threat modelling should always consider operational context.

---

# Threat Hunting

Threat hunting should focus on behaviours rather than signatures.

Examples include:

* Unexpected engineering sessions
* New trusted certificates
* Firmware uploads
* PLC mode changes
* Unauthorized write operations
* Unexpected VPN sessions
* Configuration drift
* New communication paths

Behavioural anomalies are often more valuable than isolated indicators.

---

# Monitoring Priorities

High-value monitoring targets include:

* Engineering workstations
* Domain Controllers
* VPN gateways
* Firewalls
* PLC programming activity
* Certificate management
* Historian communication
* Remote vendor access
* Jump Servers

Compromise of these assets often enables multiple attack techniques.

---

# Common Design Mistakes

Typical mistakes include:

* Treating ATT&CK as a compliance checklist.
* Monitoring only endpoints.
* Ignoring engineering workstations.
* No logging from industrial firewalls.
* No monitoring of remote access.
* Assuming segmentation alone prevents attacks.
* Focusing exclusively on CVEs.

---

# Relationship with Incident Response

ATT&CK significantly improves incident response by helping analysts:

* identify attacker objectives,
* estimate next steps,
* prioritize containment,
* identify affected assets,
* improve post-incident lessons learned.

Threat intelligence should become part of the incident response lifecycle.

---

# Architect Notes

Architects should avoid designing exclusively against known vulnerabilities.

Attackers exploit weaknesses in architecture, trust relationships and operational processes as much as software vulnerabilities.

Understanding attacker behaviour often provides greater long-term value than reacting to individual CVEs.

---

# Decision Support

When using ATT&CK during architecture reviews, ask:

1. Which attacker objectives are realistic?
2. Which assets are most critical?
3. Which techniques would have the greatest operational impact?
4. Which existing controls detect those techniques?
5. Which controls only prevent them?
6. Which techniques remain undetected?
7. Which telemetry sources are available?
8. How will detections be validated?

---

# AI Guidance

When using MITRE ATT&CK for ICS:

* Explain that ATT&CK is a behavioural framework rather than a compliance standard.
* Focus on attacker objectives and techniques rather than only malware names.
* Recommend layered detection using multiple telemetry sources.
* Map attack techniques to architectural controls where possible.
* Distinguish between vulnerability management and threat modelling.
* Encourage using ATT&CK alongside IEC 62443, NIS2 and organizational risk assessments.
* Reference the official MITRE ATT&CK for ICS knowledge base for specific technique identifiers (TIDs) and the latest mappings.

---

# Related Documents

* OT-Security-Philosophy.md
* Risk-Based-Security.md
* CVE.md
* CVSS.md
* EPSS.md
* CISA-KEV.md
* Network-Segmentation.md
* Zones-and-Conduits.md
* Firewall-Design.md
* Identity-Management.md
* Incident-Response.md
* Monitoring.md
* Logging.md
* IEC62443-Overview.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
