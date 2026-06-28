# Risk-Based Security

Version: 1.0
Status: Production Draft
Audience: OT Security Architects, Security Engineers, Automation Engineers, Compliance Officers, AI Knowledge Base

---

# Purpose

Risk-Based Security is the foundational decision-making methodology for designing, operating and improving secure Industrial Automation and Control Systems (IACS).

The objective is **not** to eliminate all risk.

Instead, the objective is to understand, prioritize and reduce risk to an acceptable level while maintaining safe and reliable industrial operations.

Every architectural recommendation within this handbook assumes a risk-based approach.

---

# Scope

This document describes:

* Risk-Based Security principles
* Risk assessment
* Risk treatment
* Decision making
* Architectural prioritization
* Operational trade-offs
* Relationship with standards and regulations

Vendor-specific methodologies are intentionally excluded.

---

# Security Philosophy

Absolute security does not exist.

Every architecture contains:

* vulnerabilities,
* operational constraints,
* business limitations,
* human factors,
* unknown risks.

Attempting to eliminate all risk usually creates unacceptable operational complexity.

Good architecture balances security with operational reality.

---

# Why Risk-Based Security Matters

Industrial environments differ fundamentally from enterprise IT.

Security decisions may affect:

* Human safety
* Environmental protection
* Process stability
* Production continuity
* Regulatory compliance
* Financial performance

Therefore, security decisions must consider both cyber risk and operational consequences.

---

# What is Risk?

Within the context of this handbook, risk is the combination of:

* Threat
* Vulnerability
* Likelihood
* Consequence

Risk cannot be evaluated by considering only vulnerabilities.

Operational context is equally important.

---

# Components of Risk

Effective risk assessment requires understanding:

## Assets

What must be protected?

Examples include:

* PLCs
* SCADA systems
* Historians
* Engineering workstations
* Safety systems
* Network infrastructure

---

## Threats

Who or what could cause harm?

Examples include:

* External attackers
* Insider threats
* Human error
* Hardware failure
* Software defects
* Supply chain compromise

---

## Vulnerabilities

What weaknesses exist?

Examples include:

* Legacy protocols
* Weak authentication
* Flat networks
* Unsupported operating systems
* Shared administrator accounts

---

## Consequences

What happens if the threat succeeds?

Possible impacts include:

* Personnel injury
* Environmental damage
* Production loss
* Equipment damage
* Regulatory penalties
* Reputation loss

---

# Risk Is Context

A CVSS score alone does not determine operational risk.

Example:

A vulnerability with a high CVSS score affecting an isolated engineering workstation may represent lower operational risk than a medium-severity vulnerability affecting a Safety Instrumented System (SIS).

Context always matters.

---

# Risk Treatment

Organizations typically have four options:

## Mitigate

Reduce the risk through technical or organizational controls.

Examples:

* Network segmentation
* MFA
* Monitoring
* Hardening

---

## Transfer

Transfer part of the risk.

Examples:

* Insurance
* Contractual obligations
* Managed services

Responsibility cannot be completely transferred.

---

## Accept

Accept the residual risk.

Acceptance should be:

* documented,
* justified,
* approved by management,
* periodically reviewed.

---

## Avoid

Eliminate the activity creating the risk.

Examples:

* Removing unnecessary remote access
* Eliminating unsupported systems
* Decommissioning obsolete services

---

# Risk Prioritization

Not every risk deserves the same response.

Architects should prioritize according to:

1. Human safety
2. Environmental protection
3. Operational continuity
4. Regulatory obligations
5. Financial impact
6. Reputation

Cost alone should rarely determine security priorities.

---

# Security vs Operational Risk

Security controls may themselves introduce risk.

Examples include:

* Firewalls increasing latency
* Antivirus disrupting PLC engineering
* Authentication delaying emergency response
* Patching causing production downtime

Architectural decisions should reduce total organizational risk rather than cyber risk alone.

---

# Residual Risk

No architecture completely eliminates risk.

After implementing controls, residual risk remains.

Residual risk should be:

* understood,
* documented,
* monitored,
* periodically reassessed.

Residual risk is not failure.

It is an expected outcome of mature risk management.

---

# Continuous Risk Assessment

Risk changes continuously.

Examples include:

* New vulnerabilities
* Firmware updates
* New suppliers
* Network changes
* New regulations
* New attack techniques

Risk assessment should therefore be a continuous operational process rather than a one-time project activity.

---

# Relationship with IEC 62443

IEC 62443 is fundamentally risk-based.

Examples include:

* Security Levels
* Zones & Conduits
* System Requirements
* Risk Assessment

Architectural controls should be selected according to identified risks rather than applied uniformly.

---

# Relationship with NIS2

NIS2 requires organizations to implement risk management measures appropriate to their circumstances.

The directive intentionally avoids prescribing identical technical controls for every organization.

Risk-Based Security therefore provides the decision-making methodology supporting NIS2 compliance.

---

# Relationship with MITRE ATT&CK

MITRE ATT&CK describes attacker behaviour.

Risk assessment determines:

* whether those techniques are relevant,
* which assets they threaten,
* which controls are justified.

Threat intelligence should inform—but not replace—risk assessment.

---

# Relationship with Defense in Depth

Defense in Depth answers:

> **How should security controls be layered?**

Risk-Based Security answers:

> **Which controls are actually needed?**

The two concepts are complementary.

Risk determines priorities.

Defense in Depth determines implementation.

---

# Common Design Mistakes

Typical mistakes include:

* Prioritizing CVSS scores without operational context.
* Treating compliance as risk management.
* Ignoring safety impacts.
* Performing risk assessments only during audits.
* Using identical security controls everywhere.
* Ignoring residual risk.
* Focusing exclusively on technology.

---

# Architect Notes

Experienced architects rarely begin by asking:

"What security product should we buy?"

Instead, they ask:

* What are we protecting?
* Why is it important?
* What could realistically happen?
* Which consequence is unacceptable?
* Which control reduces the greatest amount of risk with the least operational impact?

Good architecture is the result of informed trade-offs.

Not maximal security.

---

# Decision Framework

Recommended decision sequence:

```text
Business Objective
        ↓
Asset Identification
        ↓
Threat Assessment
        ↓
Vulnerability Assessment
        ↓
Risk Evaluation
        ↓
Architecture Design
        ↓
Control Selection
        ↓
Residual Risk Review
        ↓
Continuous Improvement
```

Every architectural decision should be traceable back to an identified business objective and risk.

---

# Practical Examples

### Example 1 – Legacy PLC

**Situation:** A PLC does not support encrypted communications.

**Poor recommendation:**

> Replace the PLC immediately.

**Risk-based recommendation:**

* Assess operational criticality.
* Evaluate vendor support.
* Implement network segmentation.
* Restrict engineering access.
* Monitor communications.
* Plan phased modernization.

---

### Example 2 – Remote Vendor Access

**Situation:** A vendor requires remote maintenance.

**Poor recommendation:**

> Disable remote access permanently.

**Risk-based recommendation:**

* Use VPN with MFA.
* Restrict access to maintenance windows.
* Require approval.
* Record sessions.
* Monitor activities.
* Review access periodically.

---

### Example 3 – High CVSS Vulnerability

**Situation:** A historian contains a vulnerability with a CVSS score of 9.8.

**Questions before deciding:**

* Is the historian Internet-accessible?
* Is exploitation realistic?
* Does CISA KEV include the vulnerability?
* What is the EPSS score?
* What operational impact would patching have?

The appropriate response depends on context, not solely on the CVSS score.

---

# AI Guidance

When applying Risk-Based Security:

* Begin by understanding business and operational objectives.
* Distinguish between cyber risk and operational risk.
* Never recommend technical controls without understanding the industrial process.
* Explain trade-offs between security, availability and safety.
* Encourage documenting residual risk.
* Map recommendations to IEC 62443, NIS2 and organizational governance where appropriate.
* Explain that compliance supports—but does not replace—risk management.

---

# Related Documents

* OT-Security-Philosophy.md
* Defense-in-Depth.md
* NIS2.md
* Czech-Cybersecurity-Act.md
* IEC62443-Overview.md
* MITRE-ATTACK-ICS.md
* ICS-Kill-Chain.md
* CVE.md
* CVSS.md
* EPSS.md
* CISA-KEV.md
* Zones-and-Conduits.md
* Identity-Management.md

---

# Revision History

| Version | Date    | Description                |
| ------- | ------- | -------------------------- |
| 1.0     | 2026-06 | Initial production release |
