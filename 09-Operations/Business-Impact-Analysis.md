# Business Impact Analysis (BIA)

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, Business Continuity Managers, Operations Managers, Security Engineers, Automation Engineers, AI Knowledge Base

---

# Purpose

A Business Impact Analysis (BIA) is a structured process for identifying critical business processes, supporting assets and the operational consequences of their disruption.

Within Operational Technology (OT), the objective of a BIA is to understand how failures of industrial systems affect safety, production, regulatory compliance, environmental protection and business operations.

A BIA provides the foundation for Business Continuity Planning (BCP), Disaster Recovery Planning (DRP), Incident Response and Risk Management.

The objective is to prioritize recovery efforts based on business impact rather than technical complexity.

---

# Scope

This document describes:

* Business Impact Analysis principles
* Critical process identification
* Asset dependency analysis
* Recovery objectives
* Impact assessment
* Prioritization
* Governance
* Operational best practices

Detailed risk assessment methodologies are described separately in **Risk-Based-Security.md**.

---

# Business Philosophy

Not every system is equally important.

Not every failure has the same consequences.

Organizations should prioritize protection and recovery based on business impact rather than technology alone.

The most technically complex asset is not always the most business-critical asset.

---

# Why Business Impact Analysis Matters

Industrial organizations depend on:

* Production systems
* Safety systems
* Utilities
* Communication systems
* Engineering systems
* Supporting IT infrastructure

A structured understanding of these dependencies enables informed investment and recovery decisions.

---

# BIA Objectives

A mature Business Impact Analysis should:

* Identify critical business processes
* Identify supporting assets
* Determine operational dependencies
* Define recovery priorities
* Establish Recovery Time Objectives (RTO)
* Establish Recovery Point Objectives (RPO)
* Support Business Continuity Planning
* Support Disaster Recovery Planning

The objective is informed prioritization rather than exhaustive documentation.

---

# BIA Lifecycle

Recommended lifecycle:

```text id="bialife"
Identify Business Processes
        ↓
Identify Supporting Assets
        ↓
Analyze Dependencies
        ↓
Assess Business Impact
        ↓
Define Recovery Objectives
        ↓
Prioritize Recovery
        ↓
Review
        ↓
Maintain
```

Business Impact Analysis should be reviewed whenever significant operational changes occur.

---

# Identify Critical Business Processes

Typical OT business processes include:

* Production
* Process Control
* Batch Operations
* Packaging
* Energy Distribution
* Water Treatment
* Building Automation
* Utility Management

Business processes—not systems—should drive recovery priorities.

---

# Supporting Assets

Each business process depends on multiple assets.

Examples include:

* PLCs
* Safety PLCs
* SCADA Servers
* HMI Systems
* OPC UA Servers
* Engineering Workstations
* Historians
* Active Directory
* Industrial Firewalls
* Virtual Infrastructure
* Network Switches

Dependencies should be documented explicitly.

---

# Dependency Analysis

Organizations should identify:

* Process dependencies
* Technology dependencies
* Personnel dependencies
* Supplier dependencies
* Utility dependencies
* Communication dependencies

Understanding dependencies reduces the risk of incomplete recovery planning.

---

# Business Impact Categories

Potential impacts include:

## Safety

* Injury
* Loss of life
* Safety system degradation

---

## Operational

* Production interruption
* Reduced capacity
* Process instability
* Equipment damage

---

## Financial

* Lost revenue
* Recovery costs
* Contract penalties

---

## Regulatory

* Compliance violations
* Reporting obligations
* Certification impacts

---

## Environmental

* Pollution
* Environmental release
* Waste generation

Organizations should evaluate each impact category independently.

---

# Recovery Objectives

The BIA establishes recovery objectives including:

## Recovery Time Objective (RTO)

Maximum acceptable recovery time.

---

## Recovery Point Objective (RPO)

Maximum acceptable data loss.

---

## Maximum Tolerable Downtime (MTD)

Maximum acceptable process interruption before unacceptable business consequences occur.

Recovery objectives should reflect business priorities rather than technical preferences.

---

# Asset Criticality

Assets may be classified using categories such as:

* Critical
* High
* Medium
* Low

Classification criteria may include:

* Safety impact
* Production impact
* Regulatory impact
* Recovery complexity
* Dependency relationships

Asset criticality should guide investment decisions.

---

# Recovery Prioritization

Recovery should generally prioritize:

1. Safety systems
2. Process control
3. Communications
4. Supporting infrastructure
5. Business applications

Recovery order should follow business dependencies rather than organizational structure.

---

# Relationship with Risk Assessment

Risk Assessment evaluates:

* Likelihood
* Threats
* Vulnerabilities

Business Impact Analysis evaluates:

* Operational consequences

Together they support informed risk management decisions.

---

# Relationship with Business Continuity

Business Impact Analysis identifies:

* What must continue.

Business Continuity Planning defines:

* How continuity will be achieved.

The BIA is therefore an input to the Business Continuity Plan.

---

# Relationship with Disaster Recovery

Business Impact Analysis determines:

* Recovery priorities
* Recovery objectives

Disaster Recovery Planning defines:

* Recovery procedures
* Recovery architecture
* Recovery resources

The BIA guides Disaster Recovery strategy.

---

# Relationship with Backup

Backup strategies should align with:

* RTO
* RPO
* Asset criticality

Backup frequency should reflect business requirements rather than technical convenience.

---

# Relationship with IEC 62443

Business Impact Analysis supports IEC 62443 concepts including:

* Risk Management
* Security Program Development
* Operational Continuity
* System Lifecycle Management

Although not explicitly prescribed by IEC 62443, BIA strengthens risk-informed decision-making throughout the system lifecycle.

---

# Monitoring

Organizations should periodically review:

* Changes to business processes
* New operational dependencies
* Asset inventory updates
* Recovery objectives
* Organizational changes
* Vendor dependencies

The BIA should evolve alongside the operational environment.

---

# Common Design Mistakes

Typical mistakes include:

* Starting with technology instead of business processes.
* Ignoring operational dependencies.
* Using identical RTO values for all systems.
* Ignoring safety consequences.
* Treating the BIA as a one-time exercise.
* Failing to involve operations personnel.
* Not validating recovery priorities.

---

# Architect Notes

One of the most common misconceptions is:

> "Critical systems are the most expensive systems."

Criticality is determined by business impact—not by acquisition cost or technical complexity.

A relatively simple industrial switch may be more critical to operations than a high-performance engineering workstation if its failure interrupts production.

Architects should evaluate business consequences before defining technical priorities.

---

# Decision Support

Before completing a Business Impact Analysis, determine:

1. Which business processes are essential?
2. Which assets support those processes?
3. What dependencies exist between processes and assets?
4. What are the safety implications of disruption?
5. What are the required RTO, RPO and MTD values?
6. Which assets require the highest recovery priority?
7. Which recovery strategies are feasible?
8. How often will the BIA be reviewed and updated?

---

# Practical Examples

### Example 1 – Production Line

Business process:

* Automated production

Supporting assets:

* PLC
* SCADA
* HMI
* Industrial network
* Historian

Assessment:

* Production cannot continue without the PLC.
* SCADA degradation reduces visibility but may permit limited manual operation.
* Historian outage affects reporting but not immediate production.

Recovery priorities should reflect these operational dependencies.

---

### Example 2 – Water Treatment Facility

Business process:

* Water treatment and distribution

Primary impacts:

* Public safety
* Environmental compliance
* Regulatory obligations

Recovery objectives should prioritize process control and safety systems over reporting systems.

---

### Example 3 – Engineering Environment

Business process:

* Maintenance and engineering support

Assessment:

* Engineering workstations are critical for long-term maintenance.
* Short-term production may continue without engineering capabilities if process control remains operational.

Recovery priorities should reflect operational reality.

---

# AI Guidance

When discussing Business Impact Analysis:

* Explain that the BIA evaluates business consequences rather than technical failures.
* Recommend identifying critical business processes before evaluating supporting assets.
* Distinguish BIA from Risk Assessment, Disaster Recovery Planning and Business Continuity Planning.
* Encourage defining RTO, RPO and MTD according to business impact.
* Consider safety, environmental protection, operational continuity and regulatory obligations when assessing criticality.
* Recommend periodic reviews following significant operational or organizational changes.
* Use the BIA as the foundation for Backup, Disaster Recovery, Incident Response and Business Continuity planning.

---

# Related Documents

* Business-Continuity.md
* Disaster-Recovery.md
* Disaster-Recovery-Plan.md
* Backup.md
* Incident-Response.md
* Risk-Based-Security.md
* Asset-Management.md
* Change-Management.md
* Recovery-Testing.md
* IEC62443-Overview.md
* NIS2.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
