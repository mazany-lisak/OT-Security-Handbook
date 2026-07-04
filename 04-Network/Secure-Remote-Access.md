---
title: "OT Secure Remote Access — Architecture, Identity and Vendor Governance"
document: "Secure-Remote-Access.md"
version: "1.0"
status: "Production"
date: "2026-06"
document_type: "LLM Skill / RAG Knowledge Base"
audience:
  - OT Security Architects
  - OT / Network Architects
  - Security Architects
  - Automation & Control Engineers
  - SOC Analysts / OT Detection Engineers
  - Identity & Access / PAM Engineers
  - AI Knowledge Base / RAG / LLM Skill
frameworks:
  - IEC 62443 (FR1 IAC, FR2 UC, conduits, Defense in Depth — see IEC62443.md)
  - NIS2 Directive EU 2022/2555 (Art. 21 access control/MFA; Art. 21(2)(d)/22 supply chain; Art. 23 — see NIS2.md)
  - Czech Cybersecurity Act — Zákon č. 264/2025 Sb. + Vyhl. 408/409/410/2025 (see Czech-Cybersecurity-Act.md)
  - NIST SP 800-82 Rev. 3; NSA/CISA OT remote-access guidance
scope_notes:
  - "This document covers WHO connects from outside and HOW the session is brokered and controlled."
  - "WHERE the boundary sits (zones/IDMZ) is in Network-Segmentation.md; enforcement is in Firewall-Design.md."
  - "Identity lifecycle detail is in Identity-Management.md; protocol/engineering-traffic detail is in OT-Protocols.md."
keywords:
  - secure remote access
  - jump host / bastion
  - phishing-resistant MFA
  - VPN WireGuard IPsec
  - vendor / third-party access
  - privileged access management
  - just-in-time access
  - unidirectional gateway
  - ZTNA OT
  - session recording
---

# OT Secure Remote Access — Architecture, Identity and Vendor Governance

> **What this document is.** A source-verified knowledge base on **secure remote access to OT/ICS**, written as an **LLM skill / RAG** reference. It covers *who* may connect from outside the control network and *how* each session is brokered, authenticated, restricted, recorded and torn down. It is the access-layer companion to `Network-Segmentation.md` (which defines *where* the boundary sits — the Industrial DMZ) and `Firewall-Design.md` (which *enforces* the conduit). Remote access is consistently identified by NSA/CISA as one of the highest-risk and most-exploited paths into OT.
>
> **Deliberate scope limits.** Boundary placement and the IDMZ are defined in `Network-Segmentation.md`; rule enforcement and device selection are in `Firewall-Design.md`; identity lifecycle (joiner/mover/leaver, RBAC, PAM detail) is in `Identity-Management.md`; engineering/programming-protocol behaviour is in `OT-Protocols.md`; zone/conduit and FR theory is in `IEC62443.md`.
>
> **Core thesis.** Remote access must **terminate at the security boundary (the IDMZ) and never directly inside the control network.** Connectivity is not authorization: a tunnel grants reachability, identity and policy grant the right to act. Every session must be attributable to a named human, least-privileged to a specific task, time-limited, monitored and recorded — and a permanent tunnel must never imply permanent engineering access.
>
> **Accuracy caveat for the model.** Specific products, MFA methods, VPN algorithms and validation programs change. The model should give architecture and method, defer regulatory specifics to the referenced documents, and not invent product features, default ports or validation claims.

---

## How to Use This Skill (Guidance for the LLM)

When answering remote-access questions, the model should:

1. **Terminate access in the IDMZ, never in the control network.** Recommend the brokered chain (VPN → MFA → jump host → controlled target), not direct reachability to PLCs/HMIs.
2. **Separate connectivity from authorization.** A VPN provides reachability; named identity, MFA and least privilege provide the right to act.
3. **Require phishing-resistant MFA** (FIDO2/PKI), not SMS or push, for privileged/remote OT access.
4. **Treat vendor/third-party access as the highest-risk category** — brokered, approved per engagement, time-boxed, supervised and recorded.
5. **Treat the remote session itself as administrative/engineering access** and apply the controls in `OT-Protocols.md` (program-mode, setpoint and logic-integrity protections).
6. **Respect OT priorities** — safety, availability, determinism — and preserve the ability to operate manually if the remote path is compromised.
7. **Reference** `Network-Segmentation.md`, `Firewall-Design.md`, `Identity-Management.md`, `OT-Protocols.md`, `IEC62443.md` instead of restating them.

---

# 1. Purpose

Remote access into OT exists because it delivers real value (remote maintenance, diagnostics, vendor support, centralized operations). It is also one of the most exploited initial-access paths into critical infrastructure. The purpose of this document is to define a **secure-by-design remote-access architecture** that enables legitimate access while making unauthorized or excessive access hard, attributable and contained.

# 2. Scope

Covers remote-access architecture, identity and authentication, VPN selection and hardening, jump/bastion hosts, vendor/third-party governance, least-privilege and just-in-time access, controller protection during sessions, emerging access models, session monitoring/auditing, and resilience. **Out of scope (referenced):** boundary/IDMZ design (`Network-Segmentation.md`), firewall enforcement (`Firewall-Design.md`), identity lifecycle (`Identity-Management.md`), protocols (`OT-Protocols.md`).

# 3. Why Remote Access Is the Hardest OT Problem

Every legitimate reason to connect IT/Internet to OT is also an attack path. The risk concentrates here for several reasons: edge **VPN/remote-access appliances are high-value targets** (repeated zero-day exploitation of major gateway products has enabled authentication bypass, web shells and credential theft); **session hijacking** can bypass MFA entirely via stolen tokens; and **third-party/vendor connections** account for a large and growing share of breaches. Public threat-hunt findings at critical-infrastructure operators repeatedly cite weak or missing bastion-host isolation, regular IT workstations able to reach OT, and insufficient logging. The architecture below is built to address exactly these failure modes.

# 4. Core Principles

1. **Terminate at the boundary.** Remote sessions land in the IDMZ; they never reach the control network directly.
2. **Connectivity ≠ authorization.** Reachability and the right to act are separate decisions.
3. **Named identities only.** Every session attributable to an individual; no shared or generic accounts.
4. **Phishing-resistant MFA** for privileged/remote access.
5. **Least privilege, scoped to the task** (asset, role, function, time).
6. **Time-limited / just-in-time access** — no standing engineering rights on a permanent tunnel.
7. **Validate legitimate business need** before each access; **disable dormant accounts**.
8. **Monitor, record and audit** every session; alert on anomalies.
9. **Defense in depth** — remote access is one layer over segmentation, firewalling, identity and monitoring.
10. **Preserve manual operation** — the process must survive loss/compromise of the remote path.

# 5. Reference Architecture (the brokered chain)

The canonical, widely-endorsed OT remote-access chain (the minimum acceptable pattern for higher-impact sites) brokers every session through the IDMZ:

```text
Remote user / vendor (managed device)
        │  encrypted VPN (strong crypto)
        ▼
VPN concentrator in the Industrial DMZ (L3.5)
        │  phishing-resistant MFA + dedicated remote-access account
        ▼
Jump / Bastion host (hardened, isolated, session recording)
        │  SECOND authentication with dedicated OT identity (RBAC, least privilege)
        ▼
Engineering Workstation (in engineering zone)
        │  approved engineering protocol, restricted, logged
        ▼
PLC / RTU / HMI  (control zone)
```

Key properties:
- **Two trust transitions, two authentications**: VPN/MFA into the DMZ, then re-authentication with dedicated OT credentials at the jump host. The OT identity store (e.g. an ICS-dedicated directory at Level 3) **must not be connected to corporate IT identity** in any way.
- **No direct path** from a remote endpoint (or a regular IT workstation) to a PLC/HMI. Regular IT workstations must not be able to reach OT at all.
- **The IDMZ does the brokering** (see `Network-Segmentation.md` §8 and `ISA95.md` Industrial DMZ patterns); the firewall enforces the conduit (see `Firewall-Design.md` §7 vendor-access placement).

# 6. Identity and Authentication

- **Named, individual accounts.** No shared/generic/vendor-pool accounts; every action attributable. (Lifecycle in `Identity-Management.md`.)
- **Phishing-resistant MFA (FIDO2 hardware keys or PKI/certificate-based)** for all remote and privileged access. SMS and push-based MFA are insufficient — they fall to real-time phishing proxies and push-fatigue; disable them on remote pathways where phishing-resistant options exist.
- **Dedicated remote-access accounts** — ideally *not* OT domain accounts for the first (VPN) factor; re-authenticate with **dedicated OT domain credentials** at the jump host. Maintain an **ICS-dedicated directory** (typically at Level 3) that is **isolated from corporate Active Directory**.
- **Centralized AAA** (RADIUS/TACACS+ → IdP) rather than per-device or per-application MFA settings, to close configuration gaps. (See `OT-Protocols.md` for RADIUS/TACACS+/LDAPS transport.)
- **Account hygiene:** disable dormant accounts; lock after repeated failures; enforce strong password policy; remove/rename default and elevated-privilege accounts on all devices including controllers.
- **Privileged Access Management (PAM):** broker, vault and rotate privileged credentials; require check-out, approval and session recording for privileged OT access.

# 7. VPN Selection and Hardening

**Technology preference (per the IACS edge-gateway guidance, e.g. ICR-2734 — see `ICR-2734-Security.md`):** **WireGuard** (modern, small attack surface, key-based) → **IPsec (IKEv2)** → **OpenVPN** (legacy interoperability); **GRE only inside IPsec**. Prefer **cryptographic credentials over passwords** for tunnel authentication.

**Hardening:**
- Use strong, current algorithms (e.g. NIST-approved / NIAP-validated where applicable); avoid deprecated ciphers and SSH v1.
- **Remove OT from the public Internet** — use private IP connectivity; do not expose management/VPN interfaces directly. Use **Shodan-style external discovery** to find and eliminate accidental exposure.
- **Filter to known source IPs**; apply geo-blocking where appropriate; default-deny everything else (see `Firewall-Design.md`).
- **Patch and harden the VPN/edge appliance aggressively** — these are prime zero-day targets; monitor administrative access and for post-auth/session-hijack indicators.
- Terminate the tunnel in the **IDMZ**, not in the control network.

> **Important nuance:** an always-on VPN often grants **far more reachability than a maintenance task needs**, and many "secure remote access" tools ship dangerous conveniences (file transfer of arbitrary files, clipboard paste of large scripts). For high-consequence sites, authorities increasingly advise **against broad always-on VPNs** in favour of brokered, task-scoped access and stronger boundary controls (see §10).

# 8. Jump / Bastion Hosts

A jump host (bastion / jump box) is a hardened, dedicated system that is the **sole access point** between a less-trusted segment and the protected OT network, inspecting and filtering all traffic and preventing lateral movement. Requirements:

- **Dedicated and isolated from IT networks**; never a regular admin workstation.
- **Hardened and monitored** with the scrutiny of a Tier-0 asset (allowlisting, minimal services, current patches, dedicated logging).
- **Phishing-resistant MFA** to reach it; **second authentication** with dedicated OT identity before onward access.
- **Session recording / keystroke and screen capture** for privileged sessions; **inactivity timeout** and forced session termination.
- **No unmanaged or BYOD endpoints** beyond it; route unmanaged/contractor devices through a posture-checked brokered path and restrict what they can reach even after passing checks.

# 9. Vendor / Third-Party Access Governance

Third-party access is the **single highest-risk remote-access category** and a NIS2/Czech-Act supply-chain concern (see `NIS2.md` → *Article 21(2)(d) and 22 — Supply Chain Security*; `Czech-Cybersecurity-Act.md` → *Domain 11 — Supply chain*). Govern it explicitly:

- **Validate legitimate business need** per engagement; no standing vendor access.
- **Per-session approval and time-boxing** (just-in-time): access opens for an approved window and scope, then closes automatically.
- **Supervised / attended sessions** for high-impact work; **session recording** retained.
- **Dedicated vendor identities** (named, not pooled); disable immediately when the engagement ends.
- **Least privilege to the specific asset and scope of work**; no broad network reach.
- **Prefer vendor-initiated, outbound, brokered models** (see §10) over inbound vendor tunnels into OT.
- Coordinate with vendors/integrators/MSPs on secure configuration — misconfigurations are frequently introduced by integrators or as product defaults.

# 10. Emerging / High-Assurance Access Models

- **OT-specific SRA (vendor-initiated / rendezvous):** a client inside OT reaches *outbound* through the IT/OT firewall to a broker/cloud rendezvous to meet the remote technician — avoiding inbound exposure and constant-IP server pivots. Reduces attack surface versus inbound VPN.
- **ZTNA (Zero Trust Network Access), adapted for OT:** per-session, identity- and posture-based access to a specific application/asset rather than network-level reachability. Adapt to OT constraints (legacy protocols, determinism); use it to scope access, not to inject per-packet auth into control loops (see `Network-Segmentation.md` §12 Zero Trust in OT).
- **Unidirectional gateways / data diodes:** for one-way needs (telemetry/historian export, SOC feed) where interactive remote access is *not* required; hardware-enforced, no return path (see `Network-Segmentation.md` §9).
- **Timed physical disconnect ("air-gap on a timer"):** a hardware switch that connects the remote path only during an approved, supervised window — for the highest-consequence assets.

Match the model to **consequence**: the higher the worst-case physical impact, the further toward brokered/one-way/physically-gated access the design should move.

# 11. Protecting Controllers During Remote Sessions

A remote session is **administrative/engineering access** and inherits the controller-level risks in `OT-Protocols.md`. During any remote engineering access:

- **Prevent controllers from remaining in remote *program* mode during operation** where possible; use the platform's protection (e.g. Siemens S7-1500 protection levels / PUT-GET off; Schneider M580 RUN/STOP authorization bit and Memory Protect — see `OT-Protocols.md` platform chapters).
- **Lock or limit set points** to bound the consequence of unauthorized access.
- **Integrity-check controller logic against a known-good baseline** before and after engineering sessions.
- **Monitor for unauthorized controller change attempts**, program downloads, mode changes (RUN→STOP) and engineering sessions outside approved windows.

# 12. Monitoring, Logging and Session Audit

Every remote session and trust transition must produce telemetry to a SIEM (prerequisite for NIS2 Art. 23 reporting and Czech-Act Domain 7):

- **Authentication attempts** (success/failure), lockouts, MFA approval anomalies (rapid approvals, new-device registrations).
- **Command-line executions with arguments**, network connections, file transfers, and **session recordings**.
- **Connections into OT** monitored for misuse, anomalous activity and unexpected OT protocols; **controller change attempts** flagged.
- **Inactivity timeout** and **session termination**; retain logs per policy for historical/forensic analysis.
- Correlate VPN, jump-host, AD/identity, firewall and ICS-IDS logs (see `Network-Segmentation.md` §13, `Firewall-Design.md` §10–11).

# 13. Resilience — Operating Without the Remote Path

Assume the remote path can be compromised or must be severed. Maintain and regularly exercise the ability to **operate the process manually**, to **isolate OT from a compromised IT/remote path** (workarounds, manual controls), and to **restore** OT devices/services from tested backups. Define roles for emergency disconnection. (See `ICR-2734-Backup-and-Recovery.md`, `Disaster-Recovery.md`.)

# 14. Regulatory Verification & Audit Mapping

| Remote-access concern | IEC 62443 (`IEC62443.md`) | NIS2 (`NIS2.md`) | Czech Act 264/2025 (`Czech-Cybersecurity-Act.md`) |
|---|---|---|---|
| Named identity + MFA for remote/privileged access | FR1 (IAC), FR2 (UC) | Art. 21 — access control, MFA | Domain 4 — IAM |
| Brokered access via IDMZ; no direct OT reach | FR5 (RDF); Conduits; Defense in Depth | Art. 21 — network security | Domain 5 — Network security & segmentation |
| Vendor/third-party access governance | 62443-2-4 (service suppliers) | Art. 21(2)(d), Art. 22 — supply chain | Domain 11 — Supply chain |
| Session logging, monitoring, audit | FR6 (Timely Response) | Art. 21 — monitoring; Art. 23 — reporting | Domain 7 — Logging & monitoring |
| Manual-operation resilience / recovery | FR7 (Resource Availability) | Art. 21 — business continuity | Domain 12 — Business continuity |

> **Audit-defensible artifact:** an **access register** mapping each remote-access path and account to a named owner, business justification, scope, MFA method, time window, jump-host route and log/recording location — reconciled against the conduit inventory. Direct-to-PLC access, shared vendor accounts, dormant accounts and "always-on" vendor tunnels are classic IEC 62443 and NÚKIB/NIS2 inspection findings.

# 15. Common Design Mistakes

Remote/vendor access terminating directly in the PLC/control network; shared or pooled vendor accounts; SMS/push MFA (or no MFA) for privileged access; OT identity linked to corporate AD; regular IT workstations able to reach OT; always-on, over-broad VPNs; unmanaged/BYOD endpoints with full access; no session recording or inactivity timeout; dormant accounts left enabled; OT/edge VPN appliances unpatched and Internet-exposed; controllers left in remote program mode during operation; no logging of remote sessions; no manual-operation fallback; treating a tunnel as authorization.

# 16. Decision Support

1. Is remote access *truly* required for this asset, or can it be removed/replaced with one-way export?
2. Who needs access, to which specific asset, for which task, and for how long?
3. Does the session terminate in the IDMZ and pass through a hardened jump host?
4. Is MFA phishing-resistant, and is the OT identity store isolated from corporate IT?
5. How is vendor access approved per engagement, time-boxed, supervised and recorded?
6. What controller protections (program-mode, setpoints, logic integrity) apply during the session?
7. How are sessions logged/recorded and correlated in the SIEM?
8. Can the process run manually and be isolated if the remote path is compromised?

# 17. Architect Notes

The defining mistake in OT remote access is conflating **reachability** with **authorization** — building a tunnel and assuming the job is done. A secure design instead treats remote access as a *brokered, identity-bound, least-privileged, recorded, time-limited engineering session that terminates at the boundary.* The hardest risks are not the protocols but the **people and third parties** holding the credentials, and the **edge appliances** that terminate the tunnels. Design for the day the remote path is hostile: keep it brokered, keep it monitored, and keep the ability to run the plant without it.

# 18. AI Guidance (summary for the model)

- Recommend the brokered chain (VPN → phishing-resistant MFA → hardened jump host → re-auth with dedicated OT identity → EWS → PLC); never direct-to-PLC.
- Separate connectivity from authorization; require named identities, least privilege and time-limited/just-in-time access.
- Treat vendor/third-party access as the top risk: per-engagement approval, time-boxing, supervision, recording, dedicated identities, immediate de-provisioning.
- Prefer WireGuard/IPsec with strong crypto; harden and patch edge/VPN appliances; remove OT from the public Internet; filter to known IPs.
- Apply controller protections during sessions (program-mode, setpoints, logic integrity) per `OT-Protocols.md`.
- Log and record everything to the SIEM; preserve manual-operation resilience.
- Map controls to IEC 62443 FR1/FR2/FR5/FR6/FR7, NIS2 Art. 21/22/23, and Czech-Act Domains 4/5/7/11/12; reference `Network-Segmentation.md`, `Firewall-Design.md`, `Identity-Management.md`, `OT-Protocols.md`, `IEC62443.md` rather than restating them.

---

# Appendix A — Glossary

- **Bastion / jump host (jump box)** — hardened, isolated single access point between networks; brokers and records OT sessions.
- **IDMZ** — Industrial DMZ (Purdue/ISA-95 Level 3.5) where remote access terminates.
- **JIT access** — just-in-time access: rights granted for an approved window/scope, then revoked.
- **NIAP** — National Information Assurance Partnership (validated product program).
- **PAM** — Privileged Access Management (vaulting, brokering, recording of privileged credentials/sessions).
- **Phishing-resistant MFA** — FIDO2 hardware keys or PKI/certificate-based authentication (not SMS/push).
- **Rendezvous / vendor-initiated SRA** — OT-side client reaches outbound to a broker to meet a remote technician, avoiding inbound exposure.
- **Unidirectional gateway / data diode** — hardware-enforced one-way data path.
- **ZTNA** — Zero Trust Network Access: per-session, identity/posture-based access to a specific asset.

# Appendix B — Sources (verified)

- **NSA/CISA, FBI, EPA, DOE — "Primary Mitigations to Reduce Cyber Threats to Operational Technology"** (May 2025): private IP connectivity, VPN + phishing-resistant MFA, least privilege per asset/role/scope, disable dormant accounts, IT/OT segmentation + DMZ, maintain manual operation (aligned to CISA/NIST Cross-Sector CPGs).
- **NSA/CISA — "Recommend Immediate Actions to Reduce Exposure Across Operational Technologies and Control Systems"** (AA20-205A): DMZs, firewalls, jump servers and/or one-way diodes; VPN with strong encryption + MFA; validate legitimate business need; filter to known IPs; remove default accounts; NIAP-validated VPNs.
- **CISA et al. — Volt Typhoon advisory** (AA24-038A, 2024): route IT↔OT through a monitored intermediary (firewall/bastion/DMZ); monitor connections into OT and unauthorized controller-change attempts; prevent controllers remaining in remote program mode; lock/limit set points; logic integrity checks.
- **CISA/USCG — proactive threat-hunt findings** (AA25-212A, 2025): hardened bastion hosts isolated from IT with phishing-resistant MFA; regular IT workstations must not reach ICS/OT; comprehensive logging and retention.
- **NSA/CISA — Selecting and Hardening Remote Access VPNs** (Cybersecurity Information Sheet).
- **SANS Institute — Introduction to ICS Security (Part 3):** MFA to VPN with dedicated remote-access accounts; post-VPN access only to a jump host; second authentication with dedicated OT credentials; ICS-dedicated AD at Level 3 isolated from corporate AD; sessions through the IT/OT DMZ.
- **NIST SP 800-82 Rev. 3** (2023) — OT remote-access, segmentation and monitoring guidance.
- Industry analysis on VPN-appliance exploitation, session hijacking and third-party access risk (for threat context).

> Treat external versions/sections as a snapshot; for authoritative requirements follow the referenced documents and current advisories.

# Appendix C — Related Documents

- `Network-Segmentation.md` — IDMZ and remote/vendor-access boundary placement (§8, §9, §10.3).
- `Firewall-Design.md` — vendor-access gateway placement and enforcement (§7), device selection (§6).
- `Identity-Management.md` — identity lifecycle, RBAC, MFA, PAM detail.
- `OT-Protocols.md` — engineering/programming traffic as administrative traffic; controller protections; platform specifics (S7-1500, M580, Twido, X20).
- `IEC62443.md` — FR1 (IAC), FR2 (UC), FR5 (RDF), FR6/FR7, conduits, Defense in Depth, 62443-2-4 supplier requirements.
- `NIS2.md` — Article 21 (access control/MFA), Article 21(2)(d)/22 (supply chain), Article 23 (incident reporting).
- `Czech-Cybersecurity-Act.md` — Zákon 264/2025 Sb.; Domain 4 (IAM), 5 (segmentation), 7 (logging/monitoring), 11 (supply chain), 12 (business continuity).
- `ICR-2734-Security.md`, `ICR-2734-Overview.md` — WireGuard/IPsec edge VPN and secure remote maintenance reference.
- `Disaster-Recovery.md`, `Monitoring.md`, `Logging.md`.

# Revision History

| Version | Date | Description |
|---|---|---|
| **1.0** | **2026-06** | Initial production release as RAG/LLM skill: brokered remote-access reference architecture (VPN → phishing-resistant MFA → hardened jump host → dedicated OT identity → EWS → PLC); identity/MFA, VPN selection/hardening, jump-host, vendor/third-party governance, JIT/least-privilege, controller protection during sessions, emerging models (vendor-initiated SRA, ZTNA, data diode, timed disconnect), session monitoring and resilience; regulatory verification & audit mapping to IEC62443.md, NIS2.md and Czech-Cybersecurity-Act.md (Zákon 264/2025 Sb.); grounded in NSA/CISA OT remote-access guidance and NIST SP 800-82 Rev. 3; aligned with Network-Segmentation.md, Firewall-Design.md and OT-Protocols.md |
