---
id: automation-studio
title: "B&R Automation Studio — Engineering Platform Security and Governance"
category: Technologies
layer: "08-Technologies/B&R/Automation-Studio"
vendor: "B&R Industrial Automation"
product: "Automation Studio"
version: 1.0.0
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Engineering platform security and governance reference for B&R Automation Studio: the
  project/Automation Runtime build relationship, workstation hardening, user/role management
  source of truth, secure transfer and deployment control, version and library governance,
  Technology Guarding, and integration with PAM/MFA and change management.
keywords: [Automation Studio, B&R, ABB, engineering workstation, Automation Runtime, Technology Guarding, secure transfer, POWERLINK, mapp, project governance, PAM]
---

# Purpose

This document provides a security and governance reference for **B&R Automation Studio**, the engineering platform used to develop, configure, build and deploy applications to the [BR-X20.md](../X20/BR-X20.md) controller family (and B&R's wider controller range), from the perspective of an OT Security Architect.

Automation Studio occupies the same structural role in the B&R ecosystem that [TIA-Portal.md](../../SIEMENS/TIA%20Portal/TIA-Portal.md) occupies for Siemens and [EcoStruxure-Control-Expert.md](../../Schneider%20Electric/Control%20Expert/EcoStruxure-Control-Expert.md) occupies for Schneider Electric: it is not merely a programming tool, it is the **source of truth for the controller's identity model**. Automation Runtime user management, roles and OPC UA authentication configuration are all *defined in the Automation Studio project* and pushed to the controller on deployment — which means Automation Studio governance is not a secondary concern next to controller hardening, it is a prerequisite for it. A controller with exemplary native security (per [BR-X20.md](../X20/BR-X20.md) § 3–4) configured from an ungoverned engineering workstation has, in practice, no security at all.

This document covers workstation and project governance, secure transfer/deployment, library and version management, and the platform's own protection mechanisms. Configuration click-paths are excluded; consult the deployed Automation Studio version's documentation.

> **Verification note.** Automation Studio's feature set, supported security capabilities, and the Automation Runtime versions it can target evolve across releases. Confirm capabilities against the deployed Automation Studio version; treat this document as the pattern, not a substitute for current B&R documentation.

---

# Related Documents

* [BR-X20.md](../X20/BR-X20.md) — the controller platform this engineering tool builds for; read together, especially BR-X20.md § 3–4 (Hardening, Identity and Access) which this document is the prerequisite for.
* [OPC-UA.md](../../../04-Network/OPC-UA.md) — OPC UA security configured through Automation Studio and deployed to the Automation Runtime.
* [TIA-Portal.md](../../SIEMENS/TIA%20Portal/TIA-Portal.md), [EcoStruxure-Control-Expert.md](../../Schneider%20Electric/Control%20Expert/EcoStruxure-Control-Expert.md) — peer engineering-platform documents; comparable governance model, vendor-specific mechanisms.
* [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md), [MFA.md](../../../05-Identity/MFA.md), [Identity-Management.md](../../../05-Identity/Identity-Management.md) — the identity layer governing engineering access.
* [Change-Management.md](../../../09-Operations/Change-Management.md), [Configuration-Management.md](../../../09-Operations/Configuration-Management.md) — the governance wrapper around every project change and deployment.
* [ICS-Kill-Chain.md](../../../07-Threat-Intelligence/ICS-Kill-Chain.md) — engineering-workstation compromise as an attack-lifecycle phase.

---

# Document Structure

1. Overview — role, the project/runtime relationship, placement
2. Security — threat model, defense in depth
3. Workstation Governance
4. Identity — the User Management Source of Truth
5. Secure Transfer and Deployment
6. Project, Version and Library Governance
7. Technology Guarding and Application Protection
8. Monitoring and Recovery
9. Best Practices and Common Mistakes

---

# 1 — Overview

## 1.1 Platform Role

Automation Studio is B&R's integrated development environment: hardware configuration, IEC 61131-3 / C / mapp-based application logic, POWERLINK network configuration, safety (openSAFETY) configuration, and — critically for security — **Automation Runtime user/role definitions and OPC UA security settings** are all authored here and compiled into the Automation Runtime image deployed to the controller ([BR-X20.md](../X20/BR-X20.md) § 1.2, § 1.5).

## 1.2 The Project → Runtime Relationship

Understanding this relationship is the key to understanding why Automation Studio governance matters as much as controller hardening:

```text
Automation Studio Project (source: logic, HW config, users/roles, OPC UA security, safety config)
        │  build
Automation Runtime image
        │  transfer/deployment
X20 Controller (executes exactly what the project defined)
```

The controller does not decide its own security posture — the project does. An attacker who can modify the project, or force a deployment of a project with weaker security than production expects, can silently downgrade every control documented in [BR-X20.md](../X20/BR-X20.md).

## 1.3 ISA-95 / Purdue Placement

Automation Studio runs on a **dedicated engineering workstation** in a separate engineering zone — never an operator console or general office machine ([Purdue-Model.md](../../../02-Standards/Purdue-Model.md) → Engineering ↔ Operations boundary). This is administrative access to every controller the workstation can reach, and should be treated with the same rigor as domain-administrator access in IT.

---

# 2 — Security

## 2.1 Security Philosophy

Every credible attack path against a well-hardened X20 controller runs through Automation Studio, because that is where the controller's security configuration is authored. The governing question is not "is the controller secure" in isolation, but "**is the chain from the engineering workstation to the deployed runtime image trustworthy end-to-end**."

## 2.2 Threat Model

1. **Workstation compromise** — malware, a compromised update, or physical access to an ungoverned Automation Studio host gives an attacker the ability to modify any project it holds, and by extension reconfigure or reprogram every controller that project targets ([ICS-Kill-Chain.md](../../../07-Threat-Intelligence/ICS-Kill-Chain.md) → Engineering Access).
2. **Project tampering** — unauthorised modification of logic, user/role definitions, or OPC UA security settings within a project, followed by a routine-looking deployment, is the highest-consequence and potentially hardest-to-detect attack path in the B&R ecosystem — the resulting Runtime image looks legitimate because it *is* built by the legitimate tool from a tampered source.
3. **Unauthorised or unauthorised-scope deployment** — deploying to the wrong controller, deploying outside a change window, or a compromised session pushing a malicious Runtime image, particularly if secure transfer (§ 5) is not enforced.
4. **Version/library supply-chain risk** — third-party or unreviewed libraries and function blocks incorporated into a project execute with the same trust as the rest of the application; an untrusted library is a code-injection path into every controller the project targets.
5. **Credential/session reuse** — a shared or long-lived Automation Studio login used across multiple engineers erodes the accountability that makes project-change investigation possible.
6. **Platform vulnerabilities** — track **B&R/ABB PSIRT** advisories for Automation Studio itself, not only for the Automation Runtime; prioritise via [Vulnerability-Management.md](../../../09-Operations/Vulnerability-Management.md).

## 2.3 Defense in Depth Applied

| Layer | Automation Studio specifics |
|---|---|
| Physical | Workstation physically secured; no unattended sessions |
| Network | Engineering zone isolated from operations; controller access only in change windows |
| Identity | Named engineer accounts, MFA, PAM elevation for deployment actions |
| Application | Project version control, library review, Technology Guarding on sensitive applications |
| Endpoint | Hardened, dedicated workstation; allowlisting; controlled removable media |
| Monitoring | Deployment/transfer events, project-repository changes, login activity |
| Recovery | Version-controlled project backups, preserved toolchain version, tested restoration |

See [Defense-in-Depth.md](../../../03-Architecture/Defense-in-Depth.md).

---

# 3 — Workstation Governance

* **Dedicated hardware.** Automation Studio runs on a workstation used for engineering only — no email, no general web browsing, no unrelated software. A dual-purpose "engineer's laptop that also does everything else" is the single most common way malware reaches an engineering toolchain.
* **Hardening baseline.** Apply the organisation's Windows hardening baseline ([Windows-Hardening.md](../../../09-Operations/Windows-Hardening.md)): minimal installed software, application allowlisting, controlled/logged removable-media use, patched OS and endpoint protection compatible with the vendor toolchain.
* **Network placement.** The workstation sits in a defined engineering zone with controlled, logged, change-window-scoped connectivity to controller networks — not a standing, always-open path ([Network-Segmentation.md](../../../04-Network/Network-Segmentation.md)).
* **Offline capability.** Where practical, keep the workstation capable of project development without a live connection to production controllers, connecting only for the deployment step itself.

---

# 4 — Identity — the User Management Source of Truth

## 4.1 Engineer Identity

Named Automation Studio user accounts, no shared logins, under [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md): MFA for every session ([MFA.md](../../../05-Identity/MFA.md)), Just-In-Time elevation specifically for deployment/transfer actions rather than standing project-write access, and session logging.

## 4.2 The Controller User Management Chain

Recall from [BR-X20.md](../X20/BR-X20.md) § 4.1 that Automation Runtime user/role definitions are **authored in the Automation Studio project**. This creates a governance chain that must be treated as a single unit:

* Changes to the project's user/role model are **engineering changes** and belong under the same change-control discipline as logic changes ([Change-Management.md](../../../09-Operations/Change-Management.md)) — not an informal side-channel edit.
* Review who can edit the user/role section of a project specifically; in a mature setup this may be restricted to a smaller group than general logic-editing access (separation of duties).
* When auditing "who can access this controller," the true answer starts with "who can edit and deploy this Automation Studio project" — controller-side access review is incomplete without workstation-side access review.

## 4.3 Certificate and OPC UA Security Authoring

OPC UA security settings (policies, trust configuration) for the deployed Runtime are likewise authored in the project ([OPC-UA.md](../../../04-Network/OPC-UA.md), [BR-X20.md](../X20/BR-X20.md) § 3 Phase 6). Certificate provisioning workflows should draw from the internal PKI ([PKI.md](../../../05-Identity/PKI.md)) rather than ad hoc, per-project self-signed certificates.

---

# 5 — Secure Transfer and Deployment

* **Enable secure transfer** at the Automation Runtime level (per [BR-X20.md](../X20/BR-X20.md) § 3 Phase 4) so that project/Runtime deployment itself requires authenticated authorisation, not merely network reachability.
* **Deployment as a privileged, logged action** — treat "download to controller" as equivalent in sensitivity to a production database write: require the change-control approval to already be in place before the deployment session begins, execute within the approved window, and log who deployed what to which controller and when.
* **Verify target before deploying** — confirm the intended controller identity before transfer; a misdirected deployment to the wrong controller is both an operational and a security incident.
* **Rollback readiness** — do not begin a deployment without a verified, restorable prior project/Runtime version already in hand (§ 8).

---

# 6 — Project, Version and Library Governance

* **Version control every project** — with author, timestamp and change reference, diffable against the previous version ([Configuration-Management.md](../../../09-Operations/Configuration-Management.md)). A project that exists only as "the file on the engineer's laptop" has no audit trail and no reliable recovery path.
* **Library and function-block review** — inventory third-party and reused libraries incorporated into projects; review unfamiliar or vendor-external libraries before inclusion, since they execute with full application trust once built into the Runtime image.
* **Baseline capture** — after every approved deployment, capture the deployed project as the new configuration baseline; unexplained drift between the baseline and what a controller is actually running is a security signal, not just a housekeeping issue ([Configuration-Management.md](../../../09-Operations/Configuration-Management.md)).

---

# 7 — Technology Guarding and Application Protection

B&R provides **Technology Guarding** mechanisms in Automation Studio to protect application intellectual property and restrict unauthorised viewing, copying or modification of a project. Enable these as **deterrence layered on top of, not instead of, access control**: they raise the bar against casual IP theft and accidental modification, but the workstation and identity controls in §§ 3–4 remain the actual access-control mechanism. Do not treat Technology Guarding as a substitute for PAM-governed engineering access.

---

# 8 — Monitoring and Recovery

**Monitoring** ([Monitoring.md](../../../09-Operations/Monitoring.md), [SIEM.md](../../../09-Operations/SIEM.md)):

* Automation Studio login activity and session duration, correlated with named engineer identity.
* Deployment/transfer events — target controller, timestamp, initiating identity — cross-referenced against approved change records.
* Project-repository changes (version-control commits) outside expected engineering activity or change windows.
* Any deployment attempt that secure transfer rejects (§ 5) as an unauthorised-transfer signal.

**Recovery** ([Backup.md](../../../09-Operations/Backup.md), [Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md)):

* Version-controlled project backups stored offline, verified periodically to open and build successfully.
* Preserve the exact Automation Studio version (and any required licences) needed to rebuild a given project — toolchain version drift can itself block recovery.
* Test full restoration — project restore, Runtime rebuild, deployment to representative hardware — not merely file backup.

---

# 9 — Best Practices and Common Mistakes

**Practices that pay off:** dedicated, hardened, offline-capable engineering workstation; named accounts with MFA and PAM elevation for deployment specifically; user/role project sections under the same change control as logic; secure transfer enabled and enforced; every project version-controlled with library provenance reviewed; Technology Guarding used as deterrence, not access control; toolchain version preserved alongside project backups.

**Common mistakes:**

* Automation Studio on a shared or Internet-capable machine used for general work.
* Shared engineering login with no per-engineer accountability.
* User/role model changes made informally, outside change control, because "it's just a config tab, not logic."
* Secure transfer left disabled, so deployment requires only network reachability.
* Projects existing only as local files with no version control or offline backup.
* Unreviewed third-party libraries incorporated into production projects.
* Assuming Technology Guarding password protection is equivalent to real access control.
* No monitoring of deployment events, so an unauthorised or misdirected download would go unnoticed.

---

# Architect Notes

The recurring lesson across every engineering-platform document in this handbook applies here with full force: the controller is never more trustworthy than the toolchain that built its configuration. For B&R specifically, that chain is unusually direct — user management, roles and OPC UA security are literally authored as project artifacts, not configured independently on the device — which makes Automation Studio governance not an adjacent best practice but a structural prerequisite for everything documented in [BR-X20.md](../X20/BR-X20.md) §§ 3–4. Get the workstation, identity and deployment discipline right here, and the controller's native security features mean what they claim to mean; skip it, and they don't.

---

# Decision Support

1. Does Automation Studio run on a dedicated, hardened workstation with no general-purpose use?
2. Are engineer accounts named, MFA-protected, and PAM-elevated specifically for deployment actions?
3. Is the project's user/role management section under the same change control as logic changes, with restricted edit access?
4. Is secure transfer enabled at the Runtime level, so deployment requires authenticated authorisation?
5. Is every project version-controlled, with third-party libraries reviewed before inclusion?
6. Is Technology Guarding treated as deterrence layered on top of, not instead of, access control?
7. Are deployment/transfer events monitored and cross-referenced against approved change records?
8. Is project + toolchain-version backup verified to actually restore and redeploy successfully?

---

# AI Guidance

When discussing B&R Automation Studio:

* Frame it as the source of truth for the controller's identity model (user management, roles, OPC UA security are project artifacts), not merely a programming tool — governance here is a prerequisite for BR-X20.md's controller-level security, not a separate topic.
* Recommend dedicated hardened workstations, named MFA/PAM-governed engineer accounts, and change control extended explicitly to the project's user/role section.
* Insist secure transfer is enabled so deployment requires authorisation, not just network reachability.
* Recommend version control and library-provenance review for every project; treat Technology Guarding as IP/deterrence protection, not access control.
* Delegate controller-side hardening detail to BR-X20.md, OPC UA depth to OPC-UA.md, and change/configuration governance mechanics to the 09-Operations layer; verify Automation Studio version-specific capabilities and B&R/ABB PSIRT advisories against current vendor sources rather than asserting them.

---

# Sources / Grounding

* **B&R Automation Studio / Automation Runtime documentation** — project structure, user management, secure transfer, Technology Guarding (authoritative for the deployed version).
* **B&R / ABB PSIRT security advisories** — Automation Studio and Automation Runtime vulnerability history and guidance.
* **ISA/IEC 62443 (incl. 62443-4-1)** — secure product/engineering-toolchain development lifecycle context. See [IEC62443.md](../../../02-Standards/IEC62443.md).
* **NIST SP 800-82 Rev. 3** — OT engineering-workstation security context.

> Foundational references; the vendor documentation for the deployed Automation Studio version is authoritative for product specifics.

---

# Related Documents

* [BR-X20.md](../X20/BR-X20.md)
* [TIA-Portal.md](../../SIEMENS/TIA%20Portal/TIA-Portal.md)
* [EcoStruxure-Control-Expert.md](../../Schneider%20Electric/Control%20Expert/EcoStruxure-Control-Expert.md)
* [OPC-UA.md](../../../04-Network/OPC-UA.md)
* [Purdue-Model.md](../../../02-Standards/Purdue-Model.md)
* [IEC62443.md](../../../02-Standards/IEC62443.md)
* [Network-Segmentation.md](../../../04-Network/Network-Segmentation.md)
* [Identity-Management.md](../../../05-Identity/Identity-Management.md)
* [MFA.md](../../../05-Identity/MFA.md)
* [Privileged-Access-Management.md](../../../05-Identity/Privileged-Access-Management.md)
* [PKI.md](../../../05-Identity/PKI.md)
* [Change-Management.md](../../../09-Operations/Change-Management.md)
* [Configuration-Management.md](../../../09-Operations/Configuration-Management.md)
* [Windows-Hardening.md](../../../09-Operations/Windows-Hardening.md)
* [Monitoring.md](../../../09-Operations/Monitoring.md)
* [SIEM.md](../../../09-Operations/SIEM.md)
* [Backup.md](../../../09-Operations/Backup.md)
* [Recovery-Testing.md](../../../09-Operations/Recovery-Testing.md)
* [Vulnerability-Management.md](../../../09-Operations/Vulnerability-Management.md)
* [ICS-Kill-Chain.md](../../../07-Threat-Intelligence/ICS-Kill-Chain.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-07 | Initial release — new 08-Technologies engineering-platform reference for B&R Automation Studio, matching the TIA-Portal.md/EcoStruxure-Control-Expert.md peer pattern: the project-to-Runtime relationship establishing Automation Studio as the source of truth for controller user management and OPC UA security, workstation governance, secure transfer/deployment discipline, project/version/library governance, Technology Guarding framed as deterrence not access control, monitoring and recovery; resolves the Planned manifest entry referenced by BR-X20.md and OT-Protocols.md |
