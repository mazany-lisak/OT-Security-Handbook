# Patch Management

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, Security Engineers, Automation Engineers, System Administrators, AI Knowledge Base

---

# Purpose

Patch Management is the controlled process of evaluating, testing, approving, deploying and validating software, firmware and configuration updates throughout Industrial Automation and Control Systems (IACS).

Within Operational Technology (OT), the objective of Patch Management is not to install updates as quickly as possible, but to reduce organizational risk while preserving safety, availability and operational continuity.

Patch Management should therefore be treated as a component of Risk Management rather than a purely technical maintenance activity.

---

# Scope

This document describes:

* Patch Management principles
* Risk-based prioritization
* OT-specific challenges
* Patch lifecycle
* Testing and validation
* Compensating controls
* Governance
* Continuous improvement

Vendor-specific update procedures are intentionally excluded.

---

# Security Philosophy

Not every vulnerability requires immediate patching.

Likewise, delaying every update increases long-term exposure.

Effective Patch Management balances:

* Cybersecurity
* Safety
* Availability
* Reliability
* Operational continuity
* Vendor support

The objective is reducing overall organizational risk rather than maximizing patch deployment speed.

---

# Why Patch Management Matters

Industrial systems often operate:

* Continuously
* Under strict maintenance windows
* With safety-critical functions
* Using vendor-certified software
* With limited operational redundancy

Uncontrolled patch deployment may introduce greater operational risk than the vulnerability itself.

---

# Patch Management Lifecycle

Recommended lifecycle:

```text id="patchlife"
Asset Inventory
        ↓
Identify Vulnerability
        ↓
Vendor Advisory Review
        ↓
Risk Assessment
        ↓
Testing
        ↓
Approval
        ↓
Deployment
        ↓
Validation
        ↓
Documentation
        ↓
Continuous Monitoring
```

Every stage contributes to reducing deployment risk.

---

# Asset Inventory

Successful Patch Management begins with an accurate asset inventory.

Organizations should know:

* Hardware
* Firmware
* Software
* Operating Systems
* Versions
* Owners
* Criticality
* Vendor support status

Unknown assets cannot be patched effectively.

---

# Vulnerability Evaluation

Before deploying a patch, determine:

* Applicable CVEs
* CVSS score
* EPSS score
* CISA KEV status
* Vendor advisory
* Affected functionality

Patching decisions should be based on evidence rather than assumptions.

---

# Risk Assessment

Risk assessment should consider:

* Safety implications
* Production impact
* Maintenance windows
* Redundancy
* Vendor recommendations
* Recovery capability

The objective is selecting the remediation strategy that minimizes total organizational risk.

---

# Testing

Patches should be tested before production deployment whenever practical.

Testing should evaluate:

* Functional compatibility
* Performance
* Stability
* Security improvements
* Communication protocols
* Industrial applications

Representative test environments significantly reduce deployment risk.

---

# Deployment

Recommended deployment principles include:

* Planned maintenance windows
* Change approval
* Rollback planning
* Configuration backup
* Operator notification
* Vendor coordination where required

Emergency deployment should remain exceptional.

---

# Validation

Following deployment, organizations should verify:

* Successful installation
* System functionality
* Process stability
* Security improvements
* Communication integrity
* Alarm functionality
* Performance

Deployment is not complete until validation succeeds.

---

# Rollback Planning

Every deployment should include a rollback strategy.

Rollback planning should consider:

* Configuration backups
* Firmware recovery
* Virtual machine snapshots (where appropriate)
* Application backups
* Restoration procedures

Rollback procedures should be tested periodically.

---

# Compensating Controls

Immediate patching is not always possible.

Compensating controls may include:

* Network Segmentation
* Firewall restrictions
* Application allowlisting
* Enhanced monitoring
* Access restrictions
* Vendor mitigations
* Temporary service isolation

Compensating controls reduce risk while awaiting permanent remediation.

---

# Vendor Coordination

Industrial vendors frequently certify software combinations.

Organizations should:

* Review vendor advisories.
* Confirm supported versions.
* Validate firmware compatibility.
* Coordinate maintenance activities.
* Follow vendor testing recommendations where appropriate.

Unsupported configurations may increase operational risk.

---

# Emergency Patching

Emergency patching may be appropriate when:

* Active exploitation is confirmed.
* Critical assets are exposed.
* No effective compensating controls exist.
* Operational risk from exploitation exceeds deployment risk.

Emergency procedures should remain documented, approved and auditable.

---

# Relationship with Vulnerability Intelligence

Patch prioritization should consider:

```text id="patchflow"
CVE
        ↓
CVSS
        ↓
EPSS
        ↓
CISA KEV
        ↓
Vendor Advisory
        ↓
Asset Criticality
        ↓
Operational Context
        ↓
Risk Assessment
        ↓
Patch Decision
```

Patching should never be based on CVSS alone.

---

# Relationship with Risk-Based Security

Risk-Based Security determines:

* Whether remediation is necessary.
* When remediation should occur.
* Which remediation strategy is appropriate.

Patch Management implements those decisions.

---

# Relationship with IEC 62443

Patch Management supports several IEC 62443 concepts including:

* System Integrity
* Configuration Management
* Security Maintenance
* Risk-Based Security
* Change Management

Patching should be integrated into the overall system lifecycle.

---

# Monitoring

Recommended monitoring includes:

* Patch compliance
* Failed deployments
* Unsupported software
* Firmware versions
* New vendor advisories
* Patch rollback events
* Configuration drift
* Asset inventory changes

Monitoring should continue after deployment.

---

# Common Design Mistakes

Typical mistakes include:

* Patching immediately without testing.
* Never patching legacy systems.
* Ignoring vendor guidance.
* No rollback plan.
* No asset inventory.
* No validation after deployment.
* Treating maintenance windows as optional.
* Using identical patch schedules for IT and OT.

---

# Architect Notes

One of the most common misconceptions is:

> "The fastest patch is always the safest patch."

In OT, this is rarely true.

Architects should instead determine:

* Is the vulnerability applicable?
* Is exploitation realistic?
* Is the asset operationally critical?
* Can compensating controls reduce immediate risk?
* Has the patch been validated?
* Can the organization recover if deployment fails?

Successful Patch Management reduces organizational risk rather than maximizing deployment speed.

---

# Decision Support

Before deploying a patch, determine:

1. Is the patch applicable to deployed systems?
2. Has the vendor approved or certified the update?
3. Has the patch been tested?
4. What operational impact could deployment cause?
5. Is a rollback plan available?
6. Are compensating controls sufficient if deployment is delayed?
7. How will successful deployment be validated?
8. Has the change been documented and approved?

---

# Practical Examples

### Example 1 – Engineering Workstation

**Situation:**

Security update available for engineering software.

Recommended approach:

* Review vendor advisory.
* Test compatibility.
* Schedule deployment during maintenance.
* Validate project functionality.
* Document results.

---

### Example 2 – SCADA Server

**Situation:**

Critical Windows security update.

Recommended approach:

* Review affected services.
* Create backup.
* Test in representative environment.
* Coordinate downtime.
* Validate communications after deployment.

---

### Example 3 – PLC Firmware

**Situation:**

Vendor releases firmware update addressing multiple vulnerabilities.

Recommended approach:

* Review release notes.
* Confirm compatibility with engineering software.
* Back up PLC project.
* Test firmware on representative hardware if available.
* Deploy during planned outage.
* Validate process behaviour and communications.

---

# AI Guidance

When discussing Patch Management:

* Explain that Patch Management is a risk management process rather than a software update process.
* Recommend testing before production deployment whenever practical.
* Prioritize safety, availability and operational continuity.
* Encourage using CVE, CVSS, EPSS, CISA KEV and vendor advisories together when determining remediation priorities.
* Recommend compensating controls when immediate deployment is operationally impractical.
* Emphasize rollback planning, validation and documentation as integral parts of the patch lifecycle.

---

# Related Documents

* Risk-Based-Security.md
* CVE.md
* CVSS.md
* EPSS.md
* CISA-KEV.md
* Vulnerability-Management.md
* Asset-Management.md
* Change-Management.md
* Defense-in-Depth.md
* MITRE-ATTACK-ICS.md
* IEC62443-Overview.md
* NIS2.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
