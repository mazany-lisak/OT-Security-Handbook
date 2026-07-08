# OT Security Handbook — Corpus Index

*Generated: 2026-07-07 · 69 documents · 0 planned · machine-readable manifest: [index.yaml](index.yaml)*

Every document carries canonical YAML front matter (`id`, `category`, `layer`, `version`, `status`, `language`, `summary`, `keywords`; technology documents add `vendor`/`product`). Links are relative paths within this tree; file basenames are corpus-unique. Documents marked *Planned* below are referenced by the corpus but not yet written — links to them resolve once the file is created at the listed path.

## 00-Core

Mindset, principles and decision method the whole handbook assumes.

| Document | Version | Summary |
|---|---|---|
| [OT-Architecture-Principles.md](00-Core/OT-Architecture-Principles.md) | 1.0.1 | Fifteen vendor-neutral principles a secure OT architecture must embody: explicit trust boundaries, defense in depth, least privilege, identity before connectivity, recoverability, observability and full-lifecycle design. |
| [OT-Lifecycle.md](00-Core/OT-Lifecycle.md) | 1.1.1 | Lifecycle reference model for OT systems from concept to decommissioning; integrates security into every phase, reflecting 15–30 year IACS lifespans and the role of compensating controls for legacy equipment. |
| [OT-Security-Philosophy.md](00-Core/OT-Security-Philosophy.md) | 1.1.2 | Engineering philosophy of the handbook: the canonical Safety → Availability → Integrity → Confidentiality priority hierarchy, risk-based and architecture-first thinking, defense in depth and vendor neutrality. |
| [Risk-Management-Principles.md](00-Core/Risk-Management-Principles.md) | 1.1.2 | Principles of cybersecurity risk management in OT: the risk cycle, context-dependent risk, OT consequence categories (safety, environmental, production, equipment), treatment options and residual risk. |
| [Security-Decision-Framework.md](00-Core/Security-Decision-Framework.md) | 1.1.2 | Structured eight-step decision sequence from business objective through process, assets, risk and constraints to architecture and technology selection; operational expansion of the canonical priority hierarchy. |
## 01-Legislation

Legal obligations (EU and Czech national framework).

| Document | Version | Summary |
|---|---|---|
| [Czech-Cybersecurity-Act.md](01-Legislation/Czech-Cybersecurity-Act.md) | 1.3.2 | Czech national cybersecurity framework: Act No. 264/2025 Sb. and Decrees 408/409/410/2025 — scope assessment (regulated services), capability domains for higher obligations, NÚKIB incident reporting and inspections. |
| [NIS2.md](01-Legislation/NIS2.md) | 1.2.2 | Engineering-oriented overview of the NIS2 Directive (EU 2022/2555): scope and entity categories, Article 20/21/23 obligations with OT-specific implementation, supervision, penalties and sector implications. |
## 02-Standards

Standards and reference models: what to protect, how, and where it sits.

| Document | Version | Summary |
|---|---|---|
| [CRA.md](02-Standards/CRA.md) | 1.0.1 | EU Cyber Resilience Act (Regulation 2024/2847): products with digital elements, risk classification and conformity routes, Annex I essential and vulnerability-handling requirements, SBOM, Article 14 reporting, CE marking and OT relevance. |
| [IEC62443.md](02-Standards/IEC62443.md) | 1.4.0 | IEC 62443 series overview for OT architects: standard structure and roles, zones and conduits, Security Levels (SL-T/SL-A/SL-C), foundational and system requirements (FR/SR), and the 62443-3-2 risk-assessment process. |
| [ISA95.md](02-Standards/ISA95.md) | 1.4.1 | ISA-95 (IEC 62264) functional hierarchy for security architects: systems, responsibilities and information flows at Levels 0–4, the Industrial DMZ inventory, and enterprise–control integration patterns. |
| [Purdue-Model.md](02-Standards/Purdue-Model.md) | 1.1.1 | Purdue (PERA) as the network-trust reference model: trust roles of Levels 0–5 and the 3.5 Industrial DMZ termination zone, critical trust boundaries, and the modern IIoT/edge/cloud reading via zones and conduits. |
## 03-Architecture

The three complementary architectural strategies.

| Document | Version | Summary |
|---|---|---|
| [Defense-in-Depth.md](03-Architecture/Defense-in-Depth.md) | 1.1.1 | Authoritative deep-dive on layered security in OT: independent control layers from physical security to backup and recovery, design criteria for independence, and illustrative mapping to ATT&CK for ICS. |
| [Risk-Based-Security.md](03-Architecture/Risk-Based-Security.md) | 1.1.1 | Applied strategy of selecting and prioritising controls by risk: control strength proportional to consequence and exposure, security-vs-operational-risk trade-offs, and vulnerability triage combining CVSS, EPSS, CISA KEV and context. |
| [Zero-Trust-in-OT.md](03-Architecture/Zero-Trust-in-OT.md) | 1.1.1 | Zero Trust adapted to industrial environments: identity as the primary trust boundary, continuous verification, device and application trust, and preserving safety, availability and emergency operations. |
## 04-Network

Network trust boundaries and their enforcement.

| Document | Version | Summary |
|---|---|---|
| [Firewall-Design.md](04-Network/Firewall-Design.md) | 1.2.0 | Enforcing conduits with OT firewalls: choosing the enforcement device (NGFW, industrial DPI, host-based, data diode), typical placements, default-deny rule design, industrial DPI, logging, HA fail behaviour and policy lifecycle. |
| [Network-Segmentation.md](04-Network/Network-Segmentation.md) | 1.2.0 | Implementing zones, conduits and trust boundaries: segmentation vs isolation, the Industrial DMZ, unidirectional gateways, specialized cases (SIS, engineering, vendor, wireless/IIoT) and the segmentation lifecycle. |
| [OPC-UA.md](04-Network/OPC-UA.md) | 1.0.0 | OPC UA security deep-dive and single source of truth for the protocol: security policies and modes (None/Sign/SignAndEncrypt), certificate and trust-list lifecycle, user authentication and node-level access control, PubSub and OPC UA over TSN/FX, architecture placement, and detection. |
| [OT-Protocols.md](04-Network/OT-Protocols.md) | 1.3.4 | Security reference for industrial protocols — Modbus, S7comm, PROFINET, EtherNet/IP (CIP), OPC UA/DA, POWERLINK, DNP3, IEC 60870-5-104, IEC 61850, MQTT — with default ports, DPI, monitoring and platform notes (S7-1500, M580, Twido, X20). |
| [Secure-Remote-Access.md](04-Network/Secure-Remote-Access.md) | 1.1.0 | Brokered remote-access architecture for OT: identity and MFA before connectivity, VPN selection and hardening (WireGuard → IPsec → OpenVPN), jump/bastion hosts, vendor governance, session recording and operating without the remote path. |
## 05-Identity

Identity as the primary trust boundary: identities, credentials, privileges.

| Document | Version | Summary |
|---|---|---|
| [Certificates.md](05-Identity/Certificates.md) | 1.1.0 | X.509 certificates as managed digital identities: structure and types, lifecycle, validation, expiration monitoring, renewal and revocation, private-key protection and certificate-intensive OT technologies such as OPC UA. |
| [Identity-Management.md](05-Identity/Identity-Management.md) | 1.1.0 | Identity as the primary OT trust boundary: identity categories (human, device, application, service), lifecycle, authentication and authorization, governance, privileged identities and the relationship to Zero Trust and IEC 62443. |
| [MFA.md](05-Identity/MFA.md) | 1.1.0 | Risk-based MFA deployment in OT: factor categories and methods, phishing-resistant authentication (FIDO2, certificates), emergency access, identity-infrastructure resilience and operational constraints for control-room use. |
| [PKI.md](05-Identity/PKI.md) | 1.1.0 | PKI as a trust architecture for IACS: Root and Intermediate CAs, certificate lifecycle, revocation (CRL/OCSP), private-key protection (HSM/TPM), governance and enterprise vs dedicated OT PKI deployment decisions. |
| [Privileged-Access-Management.md](05-Identity/Privileged-Access-Management.md) | 1.1.0 | PAM as a governance discipline: privileged identity lifecycle, credential vaulting and rotation, Just-In-Time access and JEA, session recording, engineering and vendor privileged access, and emergency-access handling. |
## 06-Vulnerability-Management

Scoring and intelligence inputs; the workflow hub lives in 09-Operations/Vulnerability-Management.md.

| Document | Version | Summary |
|---|---|---|
| [CISA-KEV.md](06-Vulnerability-Management/CISA-KEV.md) | 1.1.1 | CISA Known Exploited Vulnerabilities catalogue: confirmed active exploitation as a prioritisation signal, what KEV does not measure, OT interpretation and compensating controls when patching is impractical. |
| [CVE.md](06-Vulnerability-Management/CVE.md) | 1.1.1 | What a CVE identifier is — and is not: the identification lifecycle, what a CVE does not tell you, and its relationship to CVSS severity, EPSS probability and CISA KEV confirmed exploitation. |
| [CVSS.md](06-Vulnerability-Management/CVSS.md) | 1.1.1 | CVSS v4.0 technical-severity scoring: Base/Threat/Environmental/Supplemental metric groups including the Safety metric, interpretation limits, and why severity alone must never drive OT remediation priority. |
| [EPSS.md](06-Vulnerability-Management/EPSS.md) | 1.1.1 | Exploit Prediction Scoring System: data-driven probability of near-term exploitation, typical CVSS×EPSS interpretation patterns, and its role alongside KEV and operational context in OT prioritisation. |
## 07-Threat-Intelligence

Attacker behaviour models used for detection and architecture validation.

| Document | Version | Summary |
|---|---|---|
| [ICS-Kill-Chain.md](07-Threat-Intelligence/ICS-Kill-Chain.md) | 1.1.1 | ICS attack-lifecycle model (SANS two-stage ICS Cyber Kill Chain synthesis): phases from reconnaissance through engineering access and controller manipulation to operational impact, with defensive control mapping. |
| [MITRE-ATTACK-ICS.md](07-Threat-Intelligence/MITRE-ATTACK-ICS.md) | 1.1.1 | Applying MITRE ATT&CK for ICS (12 tactics, 83 techniques) in architecture and operations: detection and mitigation strategy, mapping to IEC 62443 and Purdue, threat hunting and monitoring priorities. |
## 08-Technologies

Vendor/product references realising the vendor-neutral guidance above.

| Document | Version | Summary |
|---|---|---|
| [PI-System.md (AVEVA)](08-Technologies/AVEVA/PI%20System/PI-System.md) | 1.0.2 | Security architecture and engineering reference for the AVEVA PI System (formerly OSIsoft PI): Data Archive, Asset Framework, PI Interfaces/Connectors and buffering, PI Vision and PI Web API, the estate-wide-aggregation threat model, PI Trusts vs Identity-based authentication, IDMZ placement and PI-to-PI replication, phased hardening, monitoring, backup and cloud integration. |
| [PlantSCADA.md (AVEVA)](08-Technologies/AVEVA/PlantSCADA/PlantSCADA.md) | 2.0.2 | Security architecture and engineering reference for AVEVA Plant SCADA (formerly Citect): component and redundancy architecture, threat model, identity model (roles, privilege levels, areas, Windows/AD integration), Cicode and CtAPI governance, phased hardening, communications security, monitoring, backup and recovery. |
| [ICR-2734.md (Advantech)](08-Technologies/Advantech/ICR-Series/ICR-2734.md) | 2.1.0 | Security knowledge base for the Advantech ICR-2734 industrial secure edge gateway (ICR-OS): verified hardware and S1 hardened variant, defense-in-depth controls, VPN hierarchy (WireGuard/IPsec), Zero Touch Provisioning, monitoring and recovery. |
| [WebAccess-DMP.md (Advantech)](08-Technologies/Advantech/WebAccess-DMP/WebAccess-DMP.md) | 2.0.0 | Security reference for the Advantech WebAccess/DMP device-management platform: management-plane threat model (fleet-wide configuration/firmware push, ZTP trust bootstrapping, API tokens), deployment models and placement, identity and tenant governance, hardening, network flows, monitoring and platform recovery for ICR router fleets. |
| [Automation-Studio.md (B&R Industrial Automation)](08-Technologies/B&R/Automation-Studio/Automation-Studio.md) | 1.0.0 | Engineering platform security and governance reference for B&R Automation Studio: the project/Automation Runtime build relationship, workstation hardening, user/role management source of truth, secure transfer and deployment control, version and library governance, Technology Guarding, and integration with PAM/MFA and change management. |
| [BR-X20.md (B&R Industrial Automation)](08-Technologies/B&R/X20/BR-X20.md) | 1.0.1 | Security architecture and engineering reference for the B&R (ABB) X20 modular PLC/controller system: architecture and CPU generations, POWERLINK/OPC UA/Modbus communications, threat model, identity and user management, phased hardening, Automation Studio / Automation Runtime governance, firmware and certificate handling, monitoring, backup and IEC 62443 alignment. |
| [MOXA-NPort-5000.md (Moxa)](08-Technologies/Moxa/NPort/MOXA-NPort-5000.md) | 1.0.0 | Security architecture and hardening reference for the Moxa NPort 5000 serial device server: its role as a serial-to-Ethernet bridge that exposes legacy serial equipment to IP networks, the resulting threat model (bridged trust, weak defaults, management-plane exposure), operating-mode security, phased hardening, monitoring, firmware and lifecycle, and IEC 62443 placement. |
| [Kepware-Server.md (PTC)](08-Technologies/PTC/Kepware%20Server/Kepware-Server.md) | 1.0.1 | Security architecture and engineering reference for the PTC Kepware Server (KEPServerEX) OPC aggregation platform: plugin-driver architecture, the OPC DA/DCOM legacy problem, OPC UA and IoT Gateway (MQTT/REST/ThingWorx) northbound paths, the bidirectional-write threat model, user management, phased hardening, monitoring, backup and IEC 62443 conduit placement. |
| [S7-1500.md (Siemens)](08-Technologies/SIEMENS/S7-1500/S7-1500.md) | 1.1.2 | Security architecture and engineering reference for the Siemens SIMATIC S7-1500: access levels and UMAC, secure PG/PC and OPC UA communication (TLS), firmware-dependent capabilities and the phased hardening lifecycle. |
| [S7-300.md (Siemens)](08-Technologies/SIEMENS/S7-300/S7-300.md) | 1.0.0 | Security architecture and compensating-control reference for the legacy (discontinued) Siemens SIMATIC S7-300 PLC: absence of native authentication, S7comm classic and the historical Stuxnet precedent, mechanical key-switch and weak block protection, architectural containment (segmentation, S7comm-aware firewall/diode, monitoring), STEP 7 engineering governance, migration to S7-1500. |
| [S7-400.md (Siemens)](08-Technologies/SIEMENS/S7-400/S7-400.md) | 1.0.0 | Security architecture and compensating-control reference for the legacy (discontinued) Siemens SIMATIC S7-400 high-end PAC: process-scale and H/R fault-tolerant redundancy considerations on top of the shared S7-300 threat model, synchronisation-link containment, PCS 7 process-control context, engineering governance, monitoring, backup/recovery and migration to S7-1500/PCS neo. |
| [TIA-Portal.md (Siemens)](08-Technologies/SIEMENS/TIA%20Portal/TIA-Portal.md) | 1.1.2 | Security architecture and engineering governance for Siemens TIA Portal: UMC/UMAC identity architecture, MFA and PAM integration, controller access levels, secure PG/PC communication and engineering-zone design. |
| [EcoStruxure-Control-Expert.md (Schneider Electric)](08-Technologies/Schneider%20Electric/Control%20Expert/EcoStruxure-Control-Expert.md) | 1.1.2 | Security architecture and engineering governance for Schneider Electric EcoStruxure Control Expert: identity and access (users, MFA, PAM), threat model, engineering-zone architecture, hardening and project protection. |
| [GeoSCADA.md (Schneider Electric)](08-Technologies/Schneider%20Electric/GeoSCADA/GeoSCADA.md) | 1.0.1 | Security architecture and engineering reference for EcoStruxure Geo SCADA Expert (formerly ClearSCADA), the wide-area telemetry SCADA: integrated-server architecture, WAN/field trust model, threat model for distributed RTU estates, identity and per-object permissions, telemetry-protocol security (DNP3 SA, IEC 60870-5-104), phased hardening, monitoring, backup and disaster recovery. |
| [Modicon-M580.md (Schneider Electric)](08-Technologies/Schneider%20Electric/Modicon%20M580/Modicon-M580.md) | 1.1.2 | Security architecture and engineering reference for the Schneider Electric Modicon M580 ePAC: embedded security capabilities, threat model, phased hardening (ACLs, services, communication, network) and IEC 62443 alignment. |
| [Modicon-Premium.md (Schneider Electric)](08-Technologies/Schneider%20Electric/Modicon%20Premium/Modicon-Premium.md) | 1.0.0 | Security architecture and compensating-control reference for the legacy (end-of-commercialisation) Schneider Electric Modicon Premium PAC: absence of native security, threat model for unauthenticated Modbus/X-Way/UMAS, architectural containment (segmentation, front-end firewall/diode, monitoring), Unity Pro/legacy engineering governance, migration to M580, and IEC 62443 compensating-control framing. |
| [Modicon-Twido.md (Schneider Electric)](08-Technologies/Schneider%20Electric/Modicon%20Twido/Modicon-Twido.md) | 1.0.0 | Security architecture and compensating-control reference for the legacy (end-of-life) Schneider Electric Modicon Twido micro-PLC: absence of native security, unauthenticated Modbus/serial and weak application protection, containment architecture for small OEM/machine deployments, engineering governance (TwidoSuite), monitoring, backup and migration to M221/M580. |
| [dataFEED-OPC-Suite.md (Softing Industrial Automation)](08-Technologies/Softing/dataFEED%20OPC%20Suite/dataFEED-OPC-Suite.md) | 1.0.1 | Security architecture and engineering reference for the Softing dataFEED OPC Suite modular OPC connectivity platform: component architecture (OPC servers, OPC UA Tunnel, Redundancy Manager, Logger), the OPC UA Tunnel as a native DCOM-elimination pattern, southbound fieldbus/PROFINET heritage, redundancy security considerations, identity, phased hardening, monitoring and recovery. |
## 09-Operations

Operate/maintain disciplines across the system lifecycle.

| Document | Version | Summary |
|---|---|---|
| [Asset-Management.md](09-Operations/Asset-Management.md) | 1.0.1 | The asset inventory as the foundation of the OT security programme: what to inventory, passive-first OT-safe discovery, criticality classification tied to the BIA, and keeping the inventory current through change and decommissioning. |
| [Backup.md](09-Operations/Backup.md) | 1.1.1 | OT backup strategy: what to back up (PLC programs, SCADA projects, infrastructure and security assets, documentation), RPO/RTO alignment, offline and immutable copies, validation and restoration testing. |
| [Business-Continuity.md](09-Operations/Business-Continuity.md) | 1.1.1 | Maintaining critical industrial operations during disruption: continuity strategies (manual, reduced-capacity, isolated, degraded modes), personnel and supplier dependencies, and coordination with DR and IR. |
| [Business-Impact-Analysis.md](09-Operations/Business-Impact-Analysis.md) | 1.1.1 | BIA for OT: identifying critical business processes and supporting assets, dependency analysis, impact categories (safety, operational, financial, regulatory, environmental) and deriving RTO/RPO/MTD and recovery priorities. |
| [Change-Management.md](09-Operations/Change-Management.md) | 1.0.1 | Governance wrapper for OT changes: change types (standard/normal/emergency), the change lifecycle with backup-before-change and rollback, maintenance windows, vendor coordination and the Management-of-Change intersection with process safety. |
| [Configuration-Management.md](09-Operations/Configuration-Management.md) | 1.0.1 | Baselines, versioning and drift detection for IACS configurations: the trusted "golden" state, version-controlled projects and device configs, drift as a security signal, and re-baselining only through approved change. |
| [Detection-Engineering.md](09-Operations/Detection-Engineering.md) | 1.0.0 | Designing, testing, tuning and maintaining detection rules mapped to ATT&CK for ICS: the detection lifecycle, coverage mapping, false-positive management, detection-as-code and validation, and the feedback loop from Threat Hunting and Incident Response. |
| [Disaster-Recovery-Plan.md](09-Operations/Disaster-Recovery-Plan.md) | 1.1.1 | Executable DRP runbook: activation criteria and authority, roles, the recovery sequence and preparation checklist, Trusted-Operational-State validation, communication plan and post-incident documentation. |
| [Disaster-Recovery.md](09-Operations/Disaster-Recovery.md) | 1.1.1 | Disaster Recovery as an architectural capability: restoring a Trusted Operational State, recovery strategies and priorities, dependency mapping, recovery objectives and operational validation before production resumes. |
| [FAT.md](09-Operations/FAT.md) | 1.0.0 | Factory Acceptance Test: security validation scope and procedures performed at the vendor's/ integrator's facility before shipment — hardening baseline, credential and default-account checks, and functional security testing on the as-built system prior to site integration. |
| [Incident-Response.md](09-Operations/Incident-Response.md) | 1.1.1 | OT incident response with safety-first priorities: the IR lifecycle, detection indicators, containment trade-offs against process stability, eradication, recovery with operational validation and lessons learned. |
| [Logging.md](09-Operations/Logging.md) | 1.1.1 | Logging as trustworthy operational evidence: OT log sources, log quality and time synchronization, integrity and retention, and prioritising high-value events (PLC programming, engineering sessions, certificates, privileged access). |
| [Monitoring.md](09-Operations/Monitoring.md) | 1.1.1 | Continuous validation of the trusted state: monitoring domains (security, network, system, OT process), telemetry sources, detection strategy and priorities, threat hunting and the relationships to ATT&CK, IR and Zero Trust. |
| [Patch-Management.md](09-Operations/Patch-Management.md) | 1.1.1 | Risk-based OT patching: the patch lifecycle from inventory and advisory review through testing, approval and deployment to validation; rollback planning, vendor coordination, emergency patching and compensating controls. |
| [Recovery-Testing.md](09-Operations/Recovery-Testing.md) | 1.1.1 | Validating recovery capability: exercise types from documentation review and tabletop to full DR exercises, success criteria and metrics (RTO/RPO achievement), Trusted-Operational-State verification and lessons learned. |
| [Risk-Assessment.md](09-Operations/Risk-Assessment.md) | 1.0.0 | Operational execution of OT risk assessment per IEC 62443-2-1/-3-2: the SLRA process from zone/conduit scoping through Cybersecurity Requirements Specification, the risk register, reassessment triggers, roles, and a worked example — the practice this handbook's risk principles and risk-based strategy are applied through. |
| [SAT.md](09-Operations/SAT.md) | 1.0.0 | Site Acceptance Test: on-site functional and security validation after network integration and before handover to operations — real conduits, real identity infrastructure, real operating conditions, and the formal transition into the operational security baseline. |
| [SIEM.md](09-Operations/SIEM.md) | 1.0.1 | The SIEM as the correlation and detection layer between logging and monitoring: passive-first OT ingestion, industrial data sources and protocol-aware parsing, data-residency constraints and high-value detections. |
| [Threat-Hunting.md](09-Operations/Threat-Hunting.md) | 1.0.0 | Proactive, hypothesis-driven investigation in OT telemetry beyond automated detection: hunting hypotheses grounded in ATT&CK for ICS and the ICS Kill Chain, data sources, cadence, a maturity model, and the relationship to Monitoring and Detection Engineering. |
| [Vulnerability-Management.md](09-Operations/Vulnerability-Management.md) | 1.0.1 | Hub for vulnerability management: the consolidated prioritisation workflow (CVE → CVSS v4.0 → EPSS → CISA KEV → criticality/exposure → operational context → risk decision), compensating controls and worked examples. |
| [Windows-Hardening.md](09-Operations/Windows-Hardening.md) | 1.0.0 | Hardening Windows hosts in OT (SCADA/historian/OPC servers, engineering workstations, jump hosts): OT domain architecture, service and RPC/DCOM minimisation, identity and privilege controls, PowerShell/logging, patching within OT constraints, EDR/allowlisting, and the DCOM containment this handbook's OPC DA guidance depends on. |

## 10-Templates

Authoring templates for new corpus documents.

| Document | Version | Summary |
|---|---|---|
| [Document-Template.md](10-Templates/Document-Template.md) | 1.0.0 | Canonical front-matter and section skeleton for new handbook documents. |

## Planned documents

*None currently — every document referenced by the corpus exists.*


## Conventions

- **Priority hierarchy** — Safety → Availability → Integrity → Confidentiality ([OT-Security-Philosophy.md](00-Core/OT-Security-Philosophy.md) is the single source of truth).
- **Delegation / single source of truth** — overlapping topics are owned by exactly one document (e.g. protocol security → OT-Protocols.md; zones & conduits → IEC62443.md; layered model → Defense-in-Depth.md; Zero Trust → Zero-Trust-in-OT.md; vulnerability workflow → Vulnerability-Management.md). Other documents link, not repeat.
- **Vendor neutrality** — layers 00–07 and 09 recommend capabilities, not products; products live in 08-Technologies.
- **AI usage** — each document carries an *AI Guidance* section with topic-specific instructions for LLM/RAG use.
