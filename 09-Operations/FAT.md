---
id: fat
title: "Factory Acceptance Test (FAT)"
category: Operations
layer: "09-Operations"
version: 1.0.0
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Factory Acceptance Test: security validation scope and procedures performed at the vendor's/
  integrator's facility before shipment — hardening baseline, credential and default-account
  checks, and functional security testing on the as-built system prior to site integration.
keywords: [FAT, Factory Acceptance Test, testovací akceptace, security validation, hardening baseline, pre-shipment testing, SAT]
---

# Purpose

The Factory Acceptance Test (FAT) is the structured validation of a system — a control panel, a skid, a PLC/SCADA package, an engineering-built subsystem — performed at the **vendor's or system integrator's facility**, before the system ships to site. Its purpose is to confirm the system meets functional and security requirements while it is still cheap and straightforward to correct problems: before transport, before site installation, before it is connected to a live production network.

Within this handbook, FAT is the point in the [OT-Lifecycle.md](../00-Core/OT-Lifecycle.md) Testing phase where **security requirements defined during Architecture and Requirements are first verified against the actual built system**, in a controlled, isolated environment. It is deliberately distinct from [SAT.md](SAT.md), which validates the same system after site integration, on the real network, under real operating conditions.

---

# Scope

This document covers: what to validate at FAT specifically (as opposed to SAT), the security-relevant FAT checklist, roles, documentation, and common gaps between vendor-claimed and demonstrated security posture. It assumes the system's security requirements were already defined (via [Risk-Assessment.md](Risk-Assessment.md) and [Security-Decision-Framework.md](../00-Core/Security-Decision-Framework.md)) before FAT begins — FAT verifies against requirements, it does not derive them.

---

# Why FAT Matters for Security

A defect found at FAT costs a configuration change at the vendor's bench. The same defect found after site installation costs a maintenance window, a change-control cycle, and — if it's a security defect discovered after the system has been live and reachable — potentially an incident. FAT is the cheapest point in the entire lifecycle to catch a hardening gap, a default credential, or a misconfigured protection level, precisely because the system is isolated, the vendor's engineers are present, and nothing is yet exposed to production risk.

FAT also establishes the **security baseline the delivered system is supposed to arrive at site with** — the reference point [SAT.md](SAT.md) and later [Configuration-Management.md](Configuration-Management.md) baselining depend on.

---

# What FAT Validates (Security-Relevant Scope)

FAT security validation should confirm, on the as-built system, in the isolated factory environment:

* **Hardening baseline applied.** The platform-specific hardening guidance relevant to the delivered equipment has actually been applied — not merely documented as intended. For a PLC/controller this means checking against the applicable `08-Technologies` document (e.g. access levels, protection levels, PUT/GET settings for Siemens; user management and secure transfer for B&R; ACLs and services for Schneider — see the relevant platform reference).
* **No default or vendor-installation credentials remain.** Every account — controller, HMI, network device, engineering workstation image — checked explicitly for default/blank/vendor-standard credentials.
* **Unnecessary services and interfaces disabled.** Web servers, legacy protocol support, unused communication modules, and diagnostic/debug interfaces not required by the operational design are identified and disabled, not left "off by default but present."
* **Network device configuration matches design.** Firewall rules, VLAN assignments, and switch configurations match the approved design documents — not a working-but-undocumented configuration the integrator arrived at during commissioning.
* **Identity and access configuration.** User/role definitions match the approved design (least privilege, no shared accounts); where the platform's identity model is authored in an engineering tool project (e.g. [Automation-Studio.md](../08-Technologies/B&R/Automation-Studio/Automation-Studio.md), [TIA-Portal.md](../08-Technologies/SIEMENS/TIA%20Portal/TIA-Portal.md)), verify the deployed configuration matches the reviewed project.
* **Certificate and PKI configuration** (where applicable) — OPC UA security policies, trust lists, and certificate provenance verified, not left at vendor test defaults ([OPC-UA.md](../04-Network/OPC-UA.md)).
* **Documentation completeness.** As-built network diagrams, configuration exports, and a device/firmware inventory are delivered and match the physical system — this documentation becomes the initial [Asset-Management.md](Asset-Management.md) and [Configuration-Management.md](Configuration-Management.md) record.
* **Functional security testing.** Where feasible in the isolated environment: attempted access with incorrect credentials is correctly rejected, protection levels behave as configured, and safety-relevant functions are unaffected by security configuration (never assume — test it).

---

# What FAT Does Not (and Cannot) Validate

Be explicit about the boundary, so FAT is not mistaken for complete security validation:

* **Real network integration** — conduits to the actual site network, the actual Industrial DMZ, actual remote-access paths — do not exist yet. This is [SAT.md](SAT.md)'s job.
* **Interaction with other site systems** — how this system behaves alongside the rest of the site's assets, existing monitoring, and existing identity infrastructure cannot be tested until integration.
* **Production-representative load and operating conditions** — FAT typically runs on bench power, simulated I/O and a subset of real operating scenarios.

A system that passes FAT is confirmed to be **built correctly**; it is not yet confirmed to be **safely and securely integrated** — that confirmation is SAT's purpose.

---

# Roles

* **Vendor/Integrator** — presents the built system, demonstrates configuration, provides documentation.
* **OT security representative (asset owner or consultant)** — runs or reviews the security-specific checklist above; independent from the build team wherever practical.
* **Process/automation engineering (asset owner)** — confirms functional requirements alongside security ones; validates that security configuration does not compromise required functionality.
* **Sign-off authority** — formally accepts (or conditionally accepts, with a remediation list) the FAT result before shipment is authorised.

---

# Documentation

Every FAT should produce: the completed checklist (including the security items above) with pass/fail/remediation-required status per item; as-built configuration exports (firewall rules, device configs, user/role lists) as the initial configuration baseline; a punch list of any deficiencies with owner and target-resolution date; and formal sign-off recording who approved shipment and on what basis. This documentation feeds directly into [SAT.md](SAT.md) planning and the system's [Configuration-Management.md](Configuration-Management.md) baseline.

---

# Relationships

* **[SAT.md](SAT.md)** — the on-site counterpart; FAT validates the built system in isolation, SAT validates it integrated. Read together.
* **[OT-Lifecycle.md](../00-Core/OT-Lifecycle.md)** — FAT sits in the Testing phase (Phase 5), following Implementation and preceding site delivery.
* **[Risk-Assessment.md](Risk-Assessment.md)** and **[Security-Decision-Framework.md](../00-Core/Security-Decision-Framework.md)** — supply the requirements and Target Security Levels FAT verifies against.
* **[Configuration-Management.md](Configuration-Management.md)** — the FAT-documented configuration becomes the initial baseline.
* **[Change-Management.md](Change-Management.md)** — any deficiency remediation identified at FAT, and any subsequent change before SAT, should flow through change control rather than informal rework.
* The relevant **`08-Technologies`** platform document — the concrete hardening checklist for the specific equipment being validated.

---

# Common Mistakes

* Treating FAT as a purely functional test, with security validation deferred to "later" (which frequently means never).
* Accepting vendor assurance that hardening was applied without independently verifying it.
* Default credentials discovered only after site installation, when correction is far more disruptive.
* No as-built documentation captured, leaving no baseline for later configuration-management comparison.
* Security representative not present or not independent from the build team.
* Sign-off granted with open security punch-list items and no tracked remediation commitment.

---

# Decision Support

1. Does the FAT checklist include explicit security items (hardening baseline, credentials, services, identity configuration), not only functional tests?
2. Is hardening independently verified against the applicable platform document, not taken on vendor assurance alone?
3. Are as-built configuration exports captured as the initial configuration baseline?
4. Is an OT security representative involved, ideally independent from the build team?
5. Are punch-list items tracked to resolution with an owner and date before or shortly after shipment?
6. Is the boundary between what FAT validates (built-correctly) and what SAT validates (integrated-correctly) understood by all participants?

---

# AI Guidance

* Explain FAT as the isolated, pre-shipment validation point — the cheapest place in the lifecycle to catch hardening gaps, default credentials and misconfiguration, distinct from SAT's on-site integration validation.
* Always include explicit security checklist items (hardening baseline, default credentials, unnecessary services, identity/certificate configuration) when discussing FAT scope, not functional testing alone.
* Recommend independent security review rather than accepting vendor self-attestation of hardening.
* Frame FAT documentation (as-built configs, checklist results) as the seed of the system's later configuration-management baseline.
* Be explicit about what FAT cannot validate (real network integration, site interaction, production load) so it is never mistaken for complete security assurance.

---

# Sources / Grounding

* **IEC 62443-2-4** — security requirements for service providers/integrators, informing acceptance-testing expectations.
* **IEC 62443-4-1** — secure product development lifecycle context for vendor-delivered systems.
* **NIST SP 800-82 Rev. 3** — OT system lifecycle and testing guidance.

> Foundational references; defer to project-specific acceptance-test specifications and the current standard text for normative detail.

---

# Related Documents

* [SAT.md](SAT.md)
* [OT-Lifecycle.md](../00-Core/OT-Lifecycle.md)
* [Risk-Assessment.md](Risk-Assessment.md)
* [Security-Decision-Framework.md](../00-Core/Security-Decision-Framework.md)
* [Configuration-Management.md](Configuration-Management.md)
* [Change-Management.md](Change-Management.md)
* [Asset-Management.md](Asset-Management.md)
* [OPC-UA.md](../04-Network/OPC-UA.md)
* [IEC62443.md](../02-Standards/IEC62443.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-07 | Initial release — new 09-Operations document resolving the dangling `FAT.md` reference from OT-Lifecycle.md; defines the security-relevant FAT scope (hardening baseline verification, default-credential and service checks, identity/certificate configuration, as-built documentation), explicitly distinguished from SAT.md's on-site integration validation, roles and documentation requirements |
