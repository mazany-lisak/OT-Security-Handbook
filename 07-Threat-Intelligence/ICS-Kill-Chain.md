# ICS Kill Chain

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, SOC Analysts, Incident Responders, Automation Engineers, AI Knowledge Base

---

# Purpose

The ICS Kill Chain is a conceptual model describing the typical progression of cyber attacks against Industrial Control Systems (ICS).

Unlike traditional enterprise attack models, the objective of an ICS attack is often not data theft but disruption or manipulation of physical processes.

Understanding the attack lifecycle enables architects to design layered security controls that interrupt adversary activity before operational impact occurs.

The ICS Kill Chain complements frameworks such as MITRE ATT&CK for ICS by providing a lifecycle-oriented view of industrial attacks.

---

# Scope

This document covers:

* Attack lifecycle
* ICS-specific attack stages
* Defensive architecture
* Detection opportunities
* Prevention strategies
* Incident response considerations
* Architecture validation

The document intentionally remains technology-agnostic.

---

# Why an ICS Kill Chain?

Industrial attacks differ significantly from enterprise attacks.

Enterprise attacks often end after:

* credential theft,
* ransomware deployment,
* data exfiltration.

Industrial attacks usually continue much further.

The attacker often attempts to:

* understand the industrial process,
* gain engineering access,
* modify control logic,
* manipulate safety systems,
* disrupt physical production.

Consequently, defending ICS environments requires understanding operational objectives rather than only IT infrastructure.

---

# Security Philosophy

Every attack consists of multiple stages.

Stopping an attacker during any stage may prevent progression to the next stage.

Therefore, effective OT security focuses on:

* prevention,
* detection,
* containment,
* recovery,

rather than assuming attacks can always be prevented.

---

# ICS Attack Lifecycle

A generalized ICS Kill Chain can be represented as:

```text
Reconnaissance
        ↓
Initial Access
        ↓
Establish Persistence
        ↓
Privilege Escalation
        ↓
IT Discovery
        ↓
OT Discovery
        ↓
Lateral Movement
        ↓
Engineering Access
        ↓
Process Understanding
        ↓
Controller Manipulation
        ↓
Safety System Interaction
        ↓
Operational Impact
```

The exact sequence varies between incidents, but the progression illustrates increasing proximity to the physical process.

---

# Phase 1 – Reconnaissance

The attacker gathers information about:

* Organization
* Suppliers
* Remote access methods
* Public infrastructure
* Technologies
* Vendors
* Employees

Architectural objective:

Reduce publicly available information and harden externally exposed services.

---

# Phase 2 – Initial Access

Common entry points include:

* Phishing
* VPN compromise
* Exposed services
* Third-party access
* Stolen credentials
* Supply chain compromise

Recommended controls:

* MFA
* Secure Remote Access
* Identity Protection
* Asset Management

---

# Phase 3 – Persistence

Attackers establish long-term access.

Examples include:

* New user accounts
* Scheduled tasks
* Services
* Backdoors
* Rogue VPN accounts

Detection opportunities:

* Identity monitoring
* Configuration monitoring
* Change management

---

# Phase 4 – Discovery

The attacker maps both IT and OT environments.

Typical targets include:

* Active Directory
* Engineering workstations
* PLCs
* Historians
* OPC Servers
* SCADA systems

Architectural objective:

Reduce unnecessary visibility through segmentation and least privilege.

---

# Phase 5 – Lateral Movement

The attacker attempts to move toward increasingly critical assets.

Typical paths include:

```text
Office Workstation
        ↓
Domain Controller
        ↓
Engineering Workstation
        ↓
SCADA
        ↓
PLC
```

Trust boundaries should interrupt this progression.

---

# Phase 6 – Engineering Access

This phase is unique to industrial environments.

The attacker attempts to obtain engineering capabilities.

Typical targets include:

* TIA Portal
* Studio 5000
* EcoStruxure Control Expert
* GX Works
* Vendor engineering software

Compromise of engineering workstations significantly increases operational risk.

---

# Phase 7 – Process Understanding

Unlike enterprise attacks, ICS attackers frequently study the industrial process before taking action.

Typical activities include:

* Reading PLC variables
* Downloading projects
* Mapping I/O
* Understanding interlocks
* Identifying safety functions

The objective is understanding the process before manipulating it.

---

# Phase 8 – Controller Manipulation

Examples include:

* Logic modification
* Parameter changes
* Unauthorized downloads
* Firmware updates
* Unauthorized write operations

Recommended controls:

* Engineering authentication
* Change approval
* Programming monitoring
* Project integrity verification

---

# Phase 9 – Safety System Interaction

Advanced attacks may attempt to:

* Disable safety mechanisms
* Modify SIS logic
* Prevent emergency shutdown
* Reduce process protection

Safety systems should therefore be architecturally isolated whenever practical.

---

# Phase 10 – Operational Impact

Possible impacts include:

* Production interruption
* Equipment damage
* Environmental impact
* Safety incidents
* Financial loss
* Regulatory consequences

Successful defense aims to prevent progression to this phase.

---

# Defensive Mapping

The following illustrates where common architectural controls interrupt the attack lifecycle.

| Kill Chain Phase        | Primary Defensive Controls                                  |
| ----------------------- | ----------------------------------------------------------- |
| Reconnaissance          | Asset Management, External Attack Surface Management        |
| Initial Access          | MFA, Secure Remote Access, Email Security                   |
| Persistence             | Identity Monitoring, Configuration Management               |
| Discovery               | Network Segmentation, Least Privilege                       |
| Lateral Movement        | Zones & Conduits, Firewalls, PAM                            |
| Engineering Access      | Dedicated Engineering Workstations, MFA, Jump Servers       |
| Process Understanding   | Monitoring, Logging, Engineering Session Auditing           |
| Controller Manipulation | Change Management, PLC Access Protection, Project Integrity |
| Safety Interaction      | Safety Zone Isolation, Independent SIS, Monitoring          |
| Operational Impact      | Incident Response, Recovery Plans, Backup & Restore         |

---

# Relationship with MITRE ATT&CK for ICS

The frameworks complement one another.

The ICS Kill Chain explains:

> **Where the attacker is in the campaign.**

MITRE ATT&CK explains:

> **What techniques the attacker is using.**

Together they provide strategic and tactical perspectives.

---

# Relationship with IEC 62443

IEC 62443 provides engineering practices that interrupt multiple phases of the kill chain.

Examples include:

* Zones and Conduits
* Defense in Depth
* Least Privilege
* Secure Remote Access
* Identity Management
* Monitoring

Rather than stopping a single technique, these controls reduce the attacker's ability to progress through the lifecycle.

---

# Detection Strategy

Detection should increase as attackers move closer to operational assets.

High-priority telemetry includes:

* Authentication failures
* VPN sessions
* Firewall events
* Engineering workstation activity
* PLC programming events
* OPC UA security events
* Certificate changes
* Configuration drift
* Firmware updates

Detection opportunities become more valuable nearer the control process.

---

# Common Design Mistakes

Typical mistakes include:

* Focusing only on perimeter defense.
* Ignoring engineering workstations.
* Monitoring only enterprise systems.
* Treating PLCs as isolated devices.
* Assuming attackers immediately target controllers.
* Ignoring safety systems during threat modelling.

---

# Architect Notes

One of the most important lessons from real-world ICS incidents is that attackers rarely begin by targeting PLCs.

They usually spend significant time:

* understanding the environment,
* identifying trusted relationships,
* compromising engineering assets,
* learning the process.

Architectures that protect engineering functions often provide greater long-term resilience than architectures focused exclusively on field devices.

---

# Decision Support

When performing an architecture review, ask:

1. Where would an attacker most likely gain initial access?
2. Which trust boundaries would slow lateral movement?
3. Which engineering assets require the strongest protection?
4. How would unauthorized controller programming be detected?
5. Are safety systems sufficiently independent?
6. Which kill chain phases currently lack detection?
7. Can recovery occur before operational impact?
8. Which defensive control provides the greatest reduction in attacker progression?

---

# AI Guidance

When using the ICS Kill Chain:

* Explain that the model represents attack progression rather than specific techniques.
* Distinguish lifecycle phases from MITRE ATT&CK techniques.
* Focus on interrupting attacker progression through layered controls.
* Emphasize engineering workstations and trust relationships as critical assets.
* Map defensive recommendations to architectural controls such as Zones and Conduits, Identity Management and Secure Remote Access.
* Consider safety implications before recommending containment actions in operational environments.

---

# Related Documents

* MITRE-ATTACK-ICS.md
* Risk-Based-Security.md
* Defense-in-Depth.md
* Zones-and-Conduits.md
* Network-Segmentation.md
* Firewall-Design.md
* Identity-Management.md
* Secure-Remote-Access.md
* Incident-Response.md
* Monitoring.md
* Logging.md
* IEC62443-Overview.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
