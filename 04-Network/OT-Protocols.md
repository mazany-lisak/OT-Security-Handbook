---
id: ot-protocols
title: "Industrial OT Communication Protocols — Architecture, Security and Platform Reference"
category: Network
layer: "04-Network"
version: 1.3.4
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  Security reference for industrial protocols — Modbus, S7comm, PROFINET, EtherNet/IP (CIP), OPC
  UA/DA, POWERLINK, DNP3, IEC 60870-5-104, IEC 61850, MQTT — with default ports, DPI, monitoring
  and platform notes (S7-1500, M580, Twido, X20).
keywords: [industrial protocols, průmyslové protokoly, Modbus, PROFINET, S7comm, OPC UA, DNP3, IEC 61850, MQTT, default ports]
---

> **What this document is.** A single, source-verified knowledge base on industrial (OT) communication protocols, written as an **LLM skill / RAG** reference. It consolidates and supersedes the previous separate protocol notes (`OT-Protocols.md`, `Modbus-TCP.md`, `S7-Communication.md`, `PROFINET.md`, `OPC-UA.md`, `OPC-DA.md` — historical file names) and adds **platform-specific chapters** for the controllers most commonly encountered in the field: **Siemens S7-1500**, **Schneider Modicon M580**, **Schneider Twido**, and **B&R X20**.
>
> **Deliberate scope limits.** Network segmentation and firewall design are discussed only as far as needed to understand protocol placement — the full treatment lives in [Network-Segmentation.md](Network-Segmentation.md) and [Firewall-Design.md](Firewall-Design.md). The **Purdue model** and **Zones-and-Conduits** are referenced only; for the authoritative treatment see [IEC62443.md](../02-Standards/IEC62443.md). Regulatory bindings to **NIS2** and the **Czech Cybersecurity Act (264/2025 Sb.)** are given at the level needed for design, requirement verification and audit, with pointers to [NIS2.md](../01-Legislation/NIS2.md) and [Czech-Cybersecurity-Act.md](../01-Legislation/Czech-Cybersecurity-Act.md) for detail.
>
> **Accuracy caveat for the model.** Protocol security extensions and platform capabilities are firmware/version-dependent. Ports, default behaviours, supported security policies and certification status change between controller firmware and engineering-tool releases. Where a user needs an authoritative version-specific answer, the model must defer to the relevant vendor manual (Siemens TIA Portal Information System, Schneider EcoStruxure Control Expert / Cybersecurity User Guide, B&R Automation Help) and the protocol owner's specification, rather than inventing specifics.

---

## How to Use This Skill (Guidance for the LLM)

When answering protocol questions, the model should:

1. **Start from the operational requirement, not the protocol.** Determinism, cycle time, interoperability and lifecycle drive the choice; security is a constraint, not the starting point.
2. **State native security honestly.** Most field protocols were designed for trusted networks and have *no* authentication, encryption or integrity. Say so plainly, then give compensating controls.
3. **Distinguish protocol capability from device/platform implementation.** "S7CommPlus is encrypted" or "OPC UA is secure" is only true if the specific CPU firmware and configuration enable it. Always tell the user to verify on the actual device.
4. **Separate the protocol from the architecture.** Security comes mostly from *where* the protocol runs (zone/conduit) and *who* may speak it — handled in the segmentation/firewall/IEC 62443 documents, referenced here.
5. **Treat engineering/programming traffic as administrative traffic** (S7comm, CIP programming, Control Expert, Automation Studio, TwidoSuite). It always needs stronger controls than process traffic.
6. **Never recommend exposing field protocols (Modbus, S7comm, PROFINET, EtherNet/IP, POWERLINK) directly to enterprise networks or the Internet.**
7. **Do not fabricate** ports, function codes, certification levels or firmware behaviours; cite the vendor/spec and flag uncertainty.

---

# 1. Purpose

Industrial communication protocols are the foundation of every Industrial Control System (ICS/IACS). Unlike enterprise protocols, most OT protocols were designed for **deterministic communication, interoperability and operational reliability — not cybersecurity**.

Understanding protocol characteristics is essential for secure architecture design, segmentation, firewall/conduit policy, protocol inspection, monitoring, incident response and risk assessment. This document provides an architectural and security overview of the most common OT protocols, plus concrete guidance for four widely deployed controller platforms.

# 2. Scope

**Covered protocol families:** Modbus (RTU/TCP), S7 Communication (S7comm / S7CommPlus), PROFINET, EtherNet/IP (CIP), OPC UA, OPC Classic (OPC DA), POWERLINK, DNP3, IEC 60870-5-104, IEC 61850, MQTT, and the supporting infrastructure services (NTP, Syslog, SNMP, RADIUS, LDAP/LDAPS, DNS, HTTPS/SSH).

**Covered platforms:** Siemens SIMATIC S7-1500, Schneider Electric Modicon M580 (ePAC), Schneider Electric Twido (legacy micro-PLC), and B&R X20.

**Out of scope (referenced only):** detailed firewall rule design and segmentation ([Network-Segmentation.md](Network-Segmentation.md), [Firewall-Design.md](Firewall-Design.md)); Purdue model and Zones-and-Conduits theory ([IEC62443.md](../02-Standards/IEC62443.md)); full regulatory text ([NIS2.md](../01-Legislation/NIS2.md), [Czech-Cybersecurity-Act.md](../01-Legislation/Czech-Cybersecurity-Act.md)). Vendor PLC programming detail is in separate platform documents.

# 3. Design Principles

Protocol selection should support operational requirements, deterministic communication, interoperability, maintainability, long lifecycle, vendor support and cybersecurity — **in that priority order for control-level traffic**. Choosing the newest protocol is not automatically the best architectural decision; replacing a stable industrial protocol solely for cybersecurity reasons must always be weighed against operational risk.

# 4. Security Evaluation Criteria

Evaluate each protocol against: authentication, authorization, encryption (confidentiality), integrity protection, replay protection, protocol-inspection support, logging/monitoring capability, vendor support and lifecycle maturity. **Not every protocol provides all of these; compensating controls are therefore the norm, not the exception.**

# 5. Regulatory and Standards Binding (minimal — see referenced documents)

This section establishes *just enough* binding to drive protocol-level design, requirements verification and audit. For full detail, follow the references.

- **IEC 62443** — Protocols cross zone boundaries through **conduits**; each conduit inherits a target **Security Level (SL)** and the relevant **Foundational/System Requirements (FR/SR)** — especially *FR1 Identification & Authentication*, *FR3 System Integrity*, *FR4 Data Confidentiality*. A protocol's native security determines how much of the conduit's SL must be met by **compensating controls**. → See [IEC62443.md](../02-Standards/IEC62443.md) → *Zones and Conduits*, *Security Levels (SL)*, *Foundational Requirements (FR) and System Requirements (SR)*, and *Security Level Risk Assessment (IEC 62443-3-2)*.
- **NIS2 (EU 2022/2555)** — Article 21 risk-management measures require, among others, network security, access control, cryptography where appropriate, and monitoring/logging — all of which are directly affected by protocol choice and placement. → See [NIS2.md](../01-Legislation/NIS2.md) → *Article 21 — Risk Management Measures*.
- **Czech Cybersecurity Act — Zákon č. 264/2025 Sb.** (transposing NIS2) and implementing decrees **408/2025** (scope), **409/2025** (higher-obligation regime) and **410/2025** (lower-obligation regime). Protocol-relevant capability domains: **Domain 5 — Network security and segmentation**, **Domain 6 — Cryptography**, **Domain 7 — Logging and monitoring**. → See [Czech-Cybersecurity-Act.md](../01-Legislation/Czech-Cybersecurity-Act.md).

> **Audit-relevant principle:** an auditor will ask *"which protocols cross which conduits, what is their native security, and what compensating controls and monitoring exist?"* Maintaining a protocol/port/flow inventory (per IEC 62443 asset management and Czech Act Domain 2) is the prerequisite for answering this. A consolidated mapping table is in **Chapter 21**.

# 6. Protocol Categories

**Process communication** (directly influence the physical process; availability is the top priority): PROFINET, EtherNet/IP, Modbus, S7 Communication, POWERLINK.

**Supervisory communication** (operational visibility; should not directly control safety functions): OPC UA, OPC DA, historian interfaces, DNP3 / IEC 60870-5-104 to SCADA.

**Infrastructure services** (not process protocols but essential for secure OT operation): DNS, NTP, Syslog, SNMP, RADIUS, LDAP/LDAPS.

**Remote management** (require the strongest identity controls): HTTPS, SSH, RDP, VPN.

---

# 7. Protocol Overview and Default Ports

> Ports are the common defaults; many are configurable. Layer-2 protocols (PROFINET RT, POWERLINK, GOOSE/SV) are not IP-routable and are confined to their Ethernet segment by design — a segmentation property, not a security control.

| Protocol | Typical use | Default transport / port | Native security | Secure variant / standard | Recommendation |
|---|---|---|---|---|---|
| **Modbus TCP** | Simple register read/write | TCP **502** | None | **Modbus Security** (TLS + X.509), TCP **802** | Segment, restrict, inspect; treat writes as high-risk |
| **Modbus RTU** | Serial field comms | RS-232/422/**485** | None | — (IEC 62351 N/A) | Serial isolation, gateway control |
| **S7comm** (0x32) | Siemens PLC prog/HMI/SCADA | TCP **102** (ISO-on-TCP, RFC 1006) | None (cleartext, incl. password) | superseded by S7CommPlus | Restrict to engineering zone |
| **S7CommPlus** (0x72) | Newer Siemens (S7-1200/1500) | TCP **102** | Integrity + encryption | proprietary (Siemens) | Prefer over legacy S7comm |
| **PROFINET RT** | Controller ↔ field I/O | Layer 2 (EtherType **0x8892**) | Limited (trust-by-link) | **PROFINET Security Classes 1/2/3** | Keep on isolated machine net |
| **PROFINET DCP** | Discovery/commissioning | Layer 2 | None | secured DCP (Security Class) | Never expose beyond cell |
| **EtherNet/IP (CIP)** | ODVA control (Rockwell/Schneider/B&R) | TCP **44818** (explicit), UDP **2222** (implicit I/O) | None (older) | **CIP Security** (TLS/DTLS) | Enable CIP Security where supported |
| **OPC UA** | Secure interoperability/SCADA/MES | TCP **4840** (opc.tcp) | High (built-in) | Sign&Encrypt + X.509 PKI | Preferred for new integration |
| **OPC DA / Classic** | Legacy Windows interop | DCOM: TCP **135** + dynamic | Low (Windows/DCOM only) | none (migrate to UA) | Wrap/gateway to OPC UA |
| **POWERLINK** | B&R real-time / motion | Layer 2 (Ethernet, EtherType **0x88AB**) | None | openSAFETY (safety, not security) | Isolated machine network |
| **DNP3** | Energy/utility SCADA | TCP/UDP **20000** | Low | **DNP3 SAv5** + TLS (IEC 62351-3/-5) | Enable Secure Authentication |
| **IEC 60870-5-104** | Energy telecontrol | TCP **2404** | Low | TLS (IEC 62351-3/-5, -5-7) | Secure per IEC 62351 |
| **IEC 61850 MMS** | Substation automation | TCP **102** (shares w/ S7comm) | Moderate | IEC 62351-3/-4 (MMS), -6 (GOOSE/SV) | Segment carefully |
| **MQTT** | Telemetry / IIoT | TCP **1883** (TLS **8883**) | Depends on impl. | TLS + auth | Always TLS + authentication |
| **SNMP** | Device monitoring | UDP **161/162** | v1/v2c none; **v3** strong | SNMPv3 (auth+priv) | Use v3 only |
| **NTP** | Time sync | UDP **123** | Weak | NTS (where supported) | Restrict sources; monitor |
| **Syslog** | Centralized logging | UDP **514** / TLS TCP **6514** | None / TLS | Syslog over TLS | Forward to SIEM |
| **RADIUS** | AAA | UDP **1812/1813** | Shared secret | RADIUS/TLS (RadSec) | Central identity |
| **LDAP / LDAPS** | Directory | TCP **389** / **636** | LDAPS = TLS | Use LDAPS | Central identity |
| **HTTPS / SSH** | Mgmt | TCP **443** / **22** | TLS / SSH | — | Strong identity, restrict |

---

# 8. Modbus (RTU and TCP)

**Origin & role.** Created by Modicon (now Schneider Electric) in 1979 for serial communication between PLCs; **Modbus TCP** is the Ethernet/TCP-IP variant. Simple client/server model: the client issues requests (read/write coils and registers), the server responds. Lightweight, deterministic, vendor-neutral — and ubiquitous.

**Native security: effectively none.** No authentication, no encryption, no integrity, no authorization, no session management. The trust model assumes every device on the network is trusted — reasonable for an isolated 1980s plant, unacceptable for connected OT today. *Any* device that can reach the server can issue valid requests, including **write** function codes that change process variables.

**Secure variant.** The **Modbus Security** specification (Modbus Organization) wraps Modbus in **TLS** with **X.509v3** certificates for mutual authentication and message integrity, on **TCP port 802**. Adoption is limited because many PLCs lack firmware support — so in brownfield environments a **TLS proxy/gateway** is the practical path.

**Security through architecture (the rule for Modbus).** Because the protocol cannot defend itself, security is delivered by: zone/conduit placement (see [IEC62443.md](../02-Standards/IEC62443.md)), industrial firewalls with allowlisting (see [Firewall-Design.md](Firewall-Design.md)), restricting **write** access to the minimum, deep-packet inspection (function-code validation), and monitoring. **Unauthorized write requests should be treated as high-priority security events.**

**Monitoring signatures.** Unexpected Modbus clients; unauthorized write function codes (e.g. FC05/06/15/16/22/23); abnormal polling rates; out-of-range register access; scanning of port 502.

**Platform notes.** All four reference platforms speak Modbus: M580 and X20 natively over Ethernet; S7-1500 via serial CM/CP or instruction blocks; Twido via serial or an add-on Ethernet module. See the platform chapters.

# 9. S7 Communication (S7comm / S7CommPlus) — Siemens

**Role.** Siemens' **proprietary** protocol family for programming, data exchange and diagnostics between SIMATIC components and tools (TIA Portal, WinCC, SCADA). It is an **engineering/automation protocol**, *not* a vendor-independent integration protocol — contrast with OPC UA. It runs over **ISO-on-TCP (RFC 1006) on TCP port 102** (a port it shares with IEC 61850 MMS / ISO-TSAP).

**Two generations:**
- **Classic S7comm** (protocol ID **0x32**): used by S7-300/400/1200/1500. **Cleartext** — including the PLC password. No meaningful authentication.
- **S7CommPlus** (protocol ID **0x72**): introduced on S7-1200/1500. Adds **encryption and integrity checks**, and anti-replay. Capability depends on CPU generation, firmware and engineering software — *verify on the device.*

**Relationship with PROFINET and OPC UA.** A Siemens PLC speaks several protocols at once: **PROFINET** to field I/O/drives (deterministic process traffic), **S7comm/S7CommPlus** to TIA Portal/HMI/SCADA (engineering/supervisory), and optionally an **OPC UA** server for standardized integration. Keep S7 Communication **inside the engineering/control zone**; expose data to higher levels via OPC UA, not by routing S7comm upward.

**Security posture.** Authentication must never rely on network location. Engineering access must be explicitly controlled. Protocol improvements (S7CommPlus) **do not remove** the need for segmentation, monitoring and identity management. See platform chapter for S7-1500 protection levels and PUT/GET behaviour.

**Monitoring signatures.** Programming sessions and **CPU mode changes (RUN→STOP, function 0x29)**, online downloads/uploads, firmware operations, engineering logins, and any S7comm from an unexpected source — especially STOP commands on a production network — warrant immediate investigation.

# 10. PROFINET

**Role.** Open industrial-Ethernet standard (PROFIBUS & PROFINET International) for **deterministic** controller-to-device communication. Dominant in Siemens-centric plants but multi-vendor.

**Communication classes:** **NRT** (non-real-time: diagnostics, engineering, config, file transfer — TCP/UDP/IT), **RT** (real-time: Layer-2 EtherType **0x8892** for most control), **IRT** (isochronous real-time: hardware-synchronized for motion/robotics; needs special infrastructure).

**Discovery — DCP.** PROFINET uses **DCP (Discovery and Configuration Protocol)** at Layer 2 for device identification/commissioning. DCP assumes a trusted network; **uncontrolled DCP exposure beyond the cell is a risk** (device naming/IP can be changed).

**Native security: limited.** Legacy PROFINET relies on network trust: limited/no authentication, no encryption, trust-by-link, unrestricted discovery.

**Secure variant — PROFINET Security Classes.** The PROFINET Security Extensions define **Class 1** (robustness, secured/SNMPv3, integrity-protected configuration and DCP), **Class 2** (cryptographic **authenticity and integrity** of communication), and **Class 3** (additionally **confidentiality/encryption**). Support is device- and firmware-dependent; **mixed environments with legacy devices remain common** — verify per device, do not assume.

**Redundancy.** High availability commonly uses **MRP (Media Redundancy Protocol)** rings; design for it but note added complexity.

**Architecture.** Keep PROFINET inside the industrial control zone; never allow enterprise systems to talk directly to field devices. For enterprise data, use OPC UA at a higher layer. Evaluate DPI carefully — it can add latency that breaks determinism.

**Monitoring signatures.** Topology/neighbor changes, new devices, unexpected **DCP** activity, engineering sessions, configuration downloads, firmware updates, controller failover, multicast anomalies.

# 11. EtherNet/IP (CIP)

**Role.** ODVA's industrial-Ethernet protocol carrying **CIP (Common Industrial Protocol)**; common on Rockwell, and supported by Schneider (M580 via DTM/NOC modules) and B&R (X20). **Explicit messaging on TCP 44818**, **implicit (cyclic I/O) on UDP 2222**.

**Native security: low (older devices).** Like Modbus, classic CIP allows reading/writing device data and configuration **without authentication** — port 44818 is a known scan target.

**Secure variant — CIP Security.** Adds **TLS/DTLS**, device identity via **X.509 certificates**, authentication, integrity and optional confidentiality. Enable where supported; control programming ports; monitor for unexpected configuration changes/writes.

**Architecture & monitoring.** Segment and inspect; treat unauthorized writes and firmware/config changes as high-priority. For enterprise integration prefer OPC UA over exposing EtherNet/IP.

# 12. OPC UA

**Role.** Platform-independent, **security-by-design** interoperability standard (IEC 62541) for PLC↔SCADA↔historian↔MES↔cloud. Generally **the preferred protocol for new integrations**. Default endpoint **opc.tcp on TCP 4840**.

**Why it exists.** It removes OPC Classic's Windows/COM/DCOM dependency, firewall complexity and weak security, and adds a rich **information model** (objects, variables, methods, events, types) so applications understand data *semantics*, not just raw values.

**Communication models.** Client/Server (most common), **PubSub** (scalable, event-driven, IIoT; can run over TSN), and **Discovery** services (control discovery in production).

**Security model (built-in):** X.509 certificates, mutual authentication, message **signing** and **encryption**, integrity, user authentication and **role-based authorization**. **Security policies** select the cipher suite — prefer modern policies (e.g. **Basic256Sha256** or **Aes256-Sha256-RsaPss**) with **Sign & Encrypt**, and disable deprecated policies (Basic128Rsa15, Basic256). **Avoid Anonymous authentication** in production.

**Certificates are the project.** Each application instance owns a certificate; trust must be explicitly managed (generate → distribute → trust → monitor expiry → renew → revoke). Poor certificate management is the most common cause of insecure OPC UA deployments — *successful OPC UA projects are PKI projects.* See [PKI.md](../05-Identity/PKI.md) / [Certificates.md](../05-Identity/Certificates.md).

**Architecture.** Encryption is **not** a substitute for segmentation. Place OPC UA servers in the Industrial DMZ / supervisory zone, use explicit conduits, and monitor trust (failed cert validation, expiries, new/unexpected clients, policy changes). OPC UA supports IEC 62443 FR1/FR3/FR4 but does **not** by itself deliver compliance.

**Platform notes.** S7-1500 has a built-in OPC UA **server and client** (FW v2.0+, best with TIA Portal V16+); M580 supports **OPC UA Secure** (via OPC UA module); B&R is a pioneer of **OPC UA over TSN** and **OPC UA FX** (field-level). See platform chapters.

> **Deep-dive delegation.** The material above is the protocol-survey level. Full security depth — security policy/mode selection, the certificate and trust-list lifecycle ("successful OPC UA projects are PKI projects"), user authentication vs. node-level Role Permissions authorization, PubSub security, and OPC UA over TSN/FX — is maintained in [OPC-UA.md](OPC-UA.md), the single source of truth for OPC UA security in this handbook.

# 13. OPC Classic (OPC DA)

**Role.** Legacy interoperability standard (mid-1990s) built on Microsoft **COM/DCOM**; still widely deployed because ICS lifecycles are long.

**Core limitation — DCOM.** Windows dependency, complex authentication, **dynamic RPC port allocation** (TCP 135 + ephemeral high ports), firewall complexity, difficult troubleshooting, tight coupling to OS security. Most "OPC DA problems" are DCOM/Windows problems.

**Security.** Relies on Windows authentication, DCOM permissions and local OS security. **Missing:** end-to-end encryption, certificate-based auth, message integrity, platform independence. Security therefore depends on Windows hardening (see [Windows-Hardening.md](../09-Operations/Windows-Hardening.md)), Least Privilege (no Local Admin OPC servers), supported OS versions, and static RPC port configuration where feasible.

**Modernization (risk-based, no "big bang").** Inventory servers/clients → assess criticality and vendor support → segment → monitor → replace unsupported Windows → introduce an **OPC UA wrapper/gateway** → migrate incrementally. **Do not replace OPC DA solely because it is legacy;** reduce exposure first, replace when justified by risk.

**Monitoring.** Windows Event Logs, DCOM authentication failures, service restarts, OPC server availability, unexpected remote clients, configuration changes — monitoring Windows security events is often more valuable than monitoring OPC DA traffic.

# 14. POWERLINK (B&R)

**Role.** **Ethernet POWERLINK** is an open real-time Ethernet standard (managed by the Ethernet POWERLINK Standardization Group, EPSG) and **B&R's flagship field/motion bus**. It provides hard real-time determinism (100 Mbit/s, synchronization down to the sub-microsecond / ±100 ns range) for control, robotics, CNC and motion. It uses **CANopen device profiles** over Ethernet and runs at **Layer 2** (a managing node polls controlled nodes), so it is **not IP-routable** and is confined to its machine network by design.

**Native security: none** (typical of deterministic field buses). **openSAFETY** runs over POWERLINK and provides **functional safety (SIL3)** — this is *safety*, not *cybersecurity*; do not conflate them.

**Architecture.** Treat the POWERLINK network as an **isolated machine/cell network**. Bridge data to higher levels through the B&R controller via **OPC UA** (incl. OPC UA over TSN / FX), not by exposing POWERLINK. Monitor at the controller and at the cell boundary rather than inside the deterministic segment.

# 15. Utility / Telecontrol Protocols (DNP3, IEC 60870-5-104, IEC 61850)

These dominate energy, water and substation automation and are increasingly in scope for NIS2 / the Czech Act (energy, water sectors).

**DNP3** — Utility SCADA; TCP/UDP **20000**. Layer-2/Transport/Application architecture, efficient over constrained links. **DNP3 Secure Authentication (SAv5)** adds challenge-response authentication at the function-code level (works on serial too); **TLS** per **IEC 62351-3** adds transport security. Enable Secure Authentication where supported.

**IEC 60870-5-104** — European energy telecontrol over TCP **2404**. Secure with **TLS per IEC 62351-3**; **IEC 62351-5 / IEC 60870-5-7** add authentication at the application layer (also applicable to serial 101). The Modicon M580 can carry **secured IEC 60870-5-104 / DNP3** via RTU communication modules.

**IEC 61850** — Substation automation. **MMS** over TCP **102** (note the **port-102 overlap with S7comm/ISO-TSAP** — relevant when both exist on a network). **GOOSE** and **Sampled Values (SV)** are **Layer-2 multicast** with stringent timing. Security: **IEC 62351-3/-4** for MMS, **IEC 62351-6** for GOOSE/SV; retrofitting legacy substations is hard. Because GOOSE/SV control physical power equipment, malicious frames can cause equipment damage — segment rigorously and monitor.

# 16. MQTT and Infrastructure Services

**MQTT** — Lightweight publish/subscribe for telemetry/IIoT; broker-based; TCP **1883** plaintext or **8883** with TLS. Security depends entirely on implementation — **always use TLS and authentication** (client certificates or token/credentials), and authorize topics.

**Infrastructure services** (secure these as carefully as control protocols):
- **NTP (UDP 123)** — accurate, trusted time underpins log correlation, certificate validity and SAv5; restrict sources, monitor drift. (S7-1500 also supports cellular/secured time where available.)
- **Syslog (UDP 514 / TLS TCP 6514)** — forward OT events centrally; prefer TLS; feed the SIEM (Czech Act Domain 7 — Logging and monitoring; NIS2 Art. 21).
- **SNMP (UDP 161/162)** — **SNMPv3 only** (auth + privacy); never v1/v2c in production.
- **RADIUS (1812/1813) / LDAP–LDAPS (389/636)** — centralized identity/AAA for engineering and management access; prefer encrypted transports.
- **DNS (53), HTTPS (443), SSH (22)** — manage and restrict; HTTPS/SSH for device management only from the management zone.

---

# 17. Platform — Siemens SIMATIC S7-1500

**Position.** Siemens' flagship mid/high-range controller. Integrated **PROFINET** (and PROFIBUS DP on PN/DP CPUs, e.g. CPU 1516-3 PN/DP = 2× PROFINET + 1× PROFIBUS DP); additional interfaces via CM/CP modules (incl. RS-232/422/485 for Freeport/Modbus).

**Protocols & ports.**
- **PROFINET** to field I/O/drives (Layer 2, RT/IRT) — process zone.
- **S7comm / S7CommPlus** on **TCP 102** to TIA Portal, WinCC, HMIs, SCADA — engineering/supervisory.
- **OPC UA server and client** on **TCP 4840** (FW **v2.0+**; OPC UA works best from **TIA Portal V16+**, certain features need FW **v2.5+**). S7-1500 can be both server and client (S7-1200 = server only).
- **Secure Open User Communication (OUC)** with **TLS** for CPU-to-CPU and email (secure SMTP).
- Integrated **web server** (HTTP/HTTPS) — disable HTTP, restrict HTTPS.
- **Modbus TCP/RTU** via instructions / CM-CP modules where required.

**Built-in security features (verify per firmware).**
- **Protection levels**: *Full access (no protection)*, *Read access*, *HMI access*, *No access (complete protection)*; the maximum protection level is intended as the secure default. There is a built-in CPU firewall.
- **PUT/GET disabled by default** (FW v2.0+): legacy access via "Permit access with PUT/GET communication from remote partner" must be explicitly enabled (and should stay **off** unless a specific integration needs it).
- **Global security settings / Certificate Manager** in TIA Portal (project protection, certificate handling), with **OPC UA security policies** (use **Basic256Sha256 – Sign & Encrypt**; disable the rest).
- **Know-how protection** (block source protection), **copy protection / binding** to a CPU or memory card, and **password-protected** project access.
- **User management** and OPC UA user authentication; integrate with central identity where possible.

**Hardening recommendations (S7-1500).** Set the highest viable protection level; keep PUT/GET off; enable global security settings and replace default/self-signed certificates with managed certificates; restrict S7comm (TCP 102) to named engineering stations via conduit policy (see [Firewall-Design.md](Firewall-Design.md)); prefer OPC UA Sign&Encrypt for SCADA/MES; enable know-how/copy protection on critical blocks; forward security-relevant events to Syslog/SIEM; manage firmware via change control.

**Monitoring signatures.** S7comm STOP (function 0x29) on production; online downloads; PUT/GET re-enabled; protection-level changes; OPC UA certificate/policy changes; unexpected programming stations.

**IEC 62443 mapping.** Supports FR1 (protection levels, OPC UA auth), FR3 (S7CommPlus/OUC integrity), FR4 (OPC UA/TLS confidentiality) — but the achieved SL depends on configuration and surrounding conduit controls (see [IEC62443.md](../02-Standards/IEC62443.md)).

# 18. Platform — Schneider Electric Modicon M580 (ePAC)

**Position.** Schneider's **Ethernet Programmable Automation Controller** with an Ethernet backbone and X80 I/O; high-performance (dual-core CPU, large I/O counts, native Gigabit backbone). Programmed with **EcoStruxure Control Expert** (formerly **Unity Pro**). Marketed as Schneider's most cyber-secure controller — **Achilles Level 2** certified and **aligned with IEC 62443-4-2**.

**Protocols & ports.**
- **Modbus TCP** (**TCP 502**) server/client — via `READ_VAR` / `WRITE_VAR` / `DATA_EXCH` and I/O scanning.
- **EtherNet/IP (CIP)** server/scanner (ODVA) — configured via **DTM** in Control Expert; on CPU and on **BMENOC** network-option modules.
- **OPC UA Secure** to SCADA — via an embedded OPC UA module (e.g. BMENUA0100; *verify part number/firmware*).
- **Secured RTU/telecontrol**: **DNP3 over IP** and **IEC 60870-5-104** via RTU communication modules (e.g. BMENOR; *verify*).
- **IPsec** for securing engineering and selected services (on **BMENOC0311**).

**Built-in security features (configured in Control Expert).**
- **Access Control List (allowlist)** of IP addresses (and TCP ports) permitted to connect to the controller as **Modbus TCP / EtherNet/IP server** — on the CPU and on NOC modules (CPU list editable online).
- **Disable unused services** (FTP/HTTP/DHCP-BOOTP/SNMP/EtherNet-IP server). For firmware **≥ v4.10, FTP is replaced by HTTP/HTTPS**; SNMP is deprecated in newer firmware lines.
- **Password protection** for program/configuration changes; **Memory Protect**; **remote RUN/STOP authorization via internal bit**; **lock remote write commands**.
- **Firmware integrity check**; **integrity check of Control Expert executables**.
- **IPsec** for Control Expert ↔ controller and server services (SNMP, NTP client, EtherNet/IP TCP as adapter/server, Modbus server) on supporting modules.
- **Security event logging to Syslog**.

**Hardening recommendations (M580).** Configure the IP allowlist on CPU and NOC; disable every unused service; enforce program/RUN-STOP passwords and Memory Protect; enable firmware/executable integrity checks; use IPsec for engineering where supported; prefer **OPC UA Secure** and **secured DNP3/IEC 104** for upward comms; forward Syslog to the SIEM; manage firmware via change control. Keep Modbus/EtherNet-IP confined to the control conduit (see [Firewall-Design.md](Firewall-Design.md)).

**Monitoring signatures.** Connections from non-allowlisted IPs; RUN/STOP via network; unexpected Control Expert sessions/downloads; service re-enablement; firmware/integrity events (available in Syslog).

**IEC 62443 mapping.** Achilles L2 + 4-2 alignment supports FR1 (ACL, passwords), FR3 (firmware/executable integrity, Memory Protect), FR4 (IPsec, OPC UA Secure). Achieved SL still depends on deployment — see [IEC62443.md](../02-Standards/IEC62443.md).

# 19. Platform — Schneider Electric Twido (legacy micro-PLC)

**Position.** Compact/modular **micro-PLC** family (Modicon brand), now **legacy/obsolete**. Programmed with **TwidoSuite** (discontinued **2019**) or older **TwidoSoft**; projects are `*.twd`. Modern functional replacement: **Modicon M221** (EcoStruxure Machine Expert – Basic). The model should treat Twido as **end-of-life equipment requiring compensating controls and a migration plan.**

**Protocols & ports.**
- **Modbus serial (RTU/ASCII)** on RS-232 / RS-485 (typical defaults 19200 8N1 / 8E1) — native.
- **Modbus TCP (TCP 502)** only with an Ethernet option: integrated Ethernet on certain models, the **TWDNOE100** module, or the **ConneXium TwidoPort (499TWD01100)** serial-to-Ethernet gateway.
- Programming via serial / **USB Mini-B** (e.g. TWD XCPU USB).

**Security posture: none native.** No authentication, encryption, integrity or access control; designed for trusted serial/isolated networks. Security must come entirely from architecture.

**Hardening / risk-reduction (Twido).** Isolate on a dedicated control segment behind an industrial firewall (allowlist the SCADA/HMI master only); make Modbus access **read-only** wherever possible; restrict and log the serial/Ethernet gateway; document register maps and every flow; remove or tightly control engineering access; monitor for unexpected writes; and **plan migration** to a supported platform (M221/M580) — track this as a vulnerability-management item (Czech Act Domain 8; NIS2 Art. 21). Because TwidoSuite is unsupported, treat the **engineering laptop/software** as a critical, isolated asset.

**Monitoring signatures.** Any write from an unexpected master; new clients on port 502; gateway configuration changes; engineering connections.

# 20. Platform — B&R (Bernecker & Reiner / ABB) X20

**Position.** Modular controller + distributed I/O system, programmed with **Automation Studio** (Automation Runtime; IEC 61131-3 plus C/C++). B&R is a pioneer and OPC Foundation FLC founding member for **OPC UA FX**.

**Protocols & ports.**
- **POWERLINK** — flagship deterministic field/motion bus (Layer 2; openSAFETY for functional safety). Machine/cell network.
- **OPC UA** — server with **OPC UA over TSN** and **OPC UA FX** (field-level / controller-to-controller); access rights configured in Automation Studio; TCP **4840**.
- **Modbus TCP** (e.g. bus controller **X20BC0087**), **PROFINET IO** device/controller (e.g. **X20BC00E3**), **EtherNet/IP**, **PROFIBUS**, **CANopen**, **DeviceNet** via the appropriate bus-controller/interface modules.

**Security posture.** POWERLINK and the classic field buses have **no native security** → isolate. Security and standardized access are delivered primarily through **OPC UA (Sign & Encrypt, certificates, access rights in Automation Studio)** and through architecture. **openSAFETY ≠ cybersecurity.**

**Hardening recommendations (X20).** Confine POWERLINK/PROFINET/EtherNet-IP to isolated machine networks; expose data upward only via **OPC UA with Sign & Encrypt and managed certificates**; assign least-privilege OPC UA access rights in Automation Studio; protect the Automation Studio engineering environment; forward controller security/diagnostic events to the SIEM; manage Automation Runtime/firmware via change control.

**Monitoring signatures.** New nodes on the POWERLINK/PROFINET segment; OPC UA failed cert validation / policy changes / unexpected clients; engineering (Automation Studio) sessions and downloads.

---

# 21. Regulatory Verification & Audit Mapping (protocol lens)

Use this to demonstrate, per conduit/protocol, that requirements are met. Follow the references for the authoritative criteria.

| Protocol-level concern | IEC 62443 (see [IEC62443.md](../02-Standards/IEC62443.md)) | NIS2 (see [NIS2.md](../01-Legislation/NIS2.md)) | Czech Act 264/2025 (see [Czech-Cybersecurity-Act.md](../01-Legislation/Czech-Cybersecurity-Act.md)) |
|---|---|---|---|
| Identify/authenticate who may speak a protocol across a conduit | FR1; SL target per conduit | Art. 21 — access control | Domain 4 — IAM |
| Confidentiality/integrity of protocol data (TLS, S7CommPlus, OPC UA, IPsec, IEC 62351) | FR3, FR4 | Art. 21 — cryptography where appropriate | Domain 6 — Cryptography |
| Restrict protocols to zones; prevent IT↔field exposure | Zones & Conduits | Art. 21 — network security | Domain 5 — Network security & segmentation |
| Log protocol-relevant events (writes, mode changes, engineering sessions) to SIEM | FR6 (timely response) | Art. 21 — monitoring/logging; Art. 23 — incident reporting | Domain 7 — Logging & monitoring |
| Manage legacy/insecure protocol risk (Modbus/S7comm/Twido) | SL-RA (3-2); compensating controls | Art. 21 — risk management | Domain 8 — Vulnerability management |
| Vendor/firmware that enables secure protocols | 62443-4-1/4-2; supplier (2-4) | Art. 21(2)(d) — supply chain | Domain 11 — Supply chain |

> **Auditor's question, answerable from one artifact:** a maintained **protocol/port/flow inventory mapped to conduits, native security, compensating controls and monitoring** satisfies the "show me" for all rows above. Build it once; reuse it for IEC 62443 SL-RA, NIS2 Art. 21 and Czech-Act scoping/audit.

# 22. Protocol Inspection (DPI)

Where supported, industrial firewalls/NIDS should inspect **protocol semantics**, not just TCP/UDP ports — e.g. Modbus function-code/register validation, CIP/EtherNet-IP object access, PROFINET awareness, DNP3 inspection, OPC UA endpoint/security verification. DPI greatly improves visibility but must be **evaluated for latency/jitter impact** on deterministic networks (PROFINET IRT, POWERLINK). Detailed placement is in [Firewall-Design.md](Firewall-Design.md).

# 23. Monitoring & Detection (cross-protocol)

Baseline normal behaviour, then alert on: protocol anomalies, unauthorized/unexpected clients, unexpected services, unusual communication frequency, **write/command operations**, **PLC mode changes** (RUN/STOP), firmware/configuration downloads, engineering/programming sessions outside maintenance windows, and authentication failures. Forward to the SIEM (see Czech Act Domain 7; NIS2 Art. 21/23). Operational baselines are what make OT anomaly detection effective.

# 24. Legacy Protocols & Compensating Controls

Many OT protocols predate cybersecurity (no encryption/authentication, broadcast discovery, trust-by-location). **This does not automatically require replacement.** Evaluate compensating controls first: segmentation and Industrial DMZ ([Network-Segmentation.md](Network-Segmentation.md)), firewall allowlists and protocol inspection ([Firewall-Design.md](Firewall-Design.md)), jump servers and secure remote access ([Secure-Remote-Access.md](Secure-Remote-Access.md)), minimized **write** permissions, monitoring/IDS, and strict change management — then plan risk-based modernization (e.g. OPC UA wrappers, Modbus/TLS proxies, platform migration for Twido).

# 25. Protocol Selection Strategy

When selecting a protocol, weigh: vendor support, lifecycle, security capabilities, deterministic behaviour, operational requirements, performance, future interoperability and monitorability. Security should **influence** selection but not compromise operational requirements. For new **enterprise/supervisory integration**, prefer **OPC UA**; for **field/process control**, use the platform's native deterministic bus (PROFINET, POWERLINK, EtherNet/IP, Modbus) **inside an isolated zone**, and bridge upward via OPC UA.

# 26. Common Design Mistakes

Allowing any protocol between IT and OT; exposing field protocols (Modbus, S7comm, PROFINET, EtherNet/IP, POWERLINK) to enterprise/Internet; using OPC DA for new builds; assuming VLAN separation equals protocol security; assuming TLS/encryption removes the need for segmentation; leaving unused protocol services enabled; ignoring engineering-workstation traffic; permitting unrestricted/permanent vendor engineering access; enabling Modbus/PUT-GET write access where read-only suffices; monitoring only IP/port instead of protocol semantics; assuming a newer protocol generation (S7CommPlus, PROFINET Security Class) is enabled when it is not.

# 27. Decision Support

Before recommending or allowing a protocol across a boundary, determine: (1) the operational purpose; (2) whether deterministic communication is required; (3) which vendor/platform and firmware are involved and which secure features are actually enabled; (4) whether interoperability is required; (5) whether authentication/encryption/integrity are available and turned on; (6) whether the protocol can be inspected; (7) which trust boundaries/conduits it crosses (→ [IEC62443.md](../02-Standards/IEC62443.md)); (8) how it will be monitored and logged (→ Czech Act Domain 7); and (9) whether **write** access is truly needed.

# 28. Architect Notes

Protocol selection is never an isolated technical decision — it shapes network architecture, firewall/conduit design, monitoring, identity management, maintenance, incident response and future modernization. **Engineering traffic is administrative traffic** and always needs stronger controls than process traffic. Prefer protocols offering authentication, encryption, certificate-based trust and standardized interoperability — but **protocol security complements, never replaces, architectural security.** The most accurate framing for the legacy protocols: *they were designed for trusted networks, so the architecture must assume they cannot defend themselves.*

# 29. AI Guidance (summary for the model)

- Begin with the operational requirement; explain each protocol's security strengths and weaknesses honestly.
- Distinguish **protocol capability** from **device/platform implementation and firmware** — always tell the user to verify on the actual controller.
- Recommend compensating controls (segmentation, firewall allowlists, monitoring) where native security is limited, and reference [Network-Segmentation.md](Network-Segmentation.md), [Firewall-Design.md](Firewall-Design.md), [IEC62443.md](../02-Standards/IEC62443.md) rather than restating them.
- Prefer **OPC UA** for new supervisory/enterprise integration; keep field protocols (incl. **S7comm**, **PROFINET**, **POWERLINK**, **Modbus**, **EtherNet/IP**) inside isolated zones.
- **Never** recommend exposing field/PLC protocols directly to enterprise networks or the Internet.
- For the four focus platforms, give platform-correct facts (ports, native security toggles like Siemens PUT/GET and protection levels, Schneider ACL/Memory-Protect/IPsec, B&R OPC UA Sign&Encrypt, Twido legacy status) and cite the vendor manual for version-specific detail.
- Map controls to **IEC 62443 FR/SR**, **NIS2 Art. 21/23** and **Czech Act Domains 4–8** for requirement verification and audit, referencing the dedicated documents.

---

# Appendix A — Default Port / Identifier Quick Reference

| Protocol | Transport / port / identifier |
|---|---|
| Modbus TCP | TCP 502 (Modbus Security/TLS: TCP 802) |
| S7comm / S7CommPlus | TCP 102 (ISO-on-TCP, RFC 1006); IDs 0x32 / 0x72 |
| PROFINET RT | Layer 2, EtherType 0x8892; context manager UDP 34964; DCP Layer 2 |
| EtherNet/IP (CIP) | TCP 44818 (explicit); UDP 2222 (implicit I/O) |
| OPC UA | TCP 4840 (opc.tcp) |
| OPC DA / Classic | DCOM: TCP 135 + dynamic RPC ports |
| POWERLINK | Layer 2, Ethernet (EtherType 0x88AB) |
| DNP3 | TCP/UDP 20000 |
| IEC 60870-5-104 | TCP 2404 |
| IEC 61850 MMS / GOOSE / SV | MMS TCP 102; GOOSE/SV Layer 2 multicast |
| MQTT | TCP 1883 (TLS 8883) |
| SNMP | UDP 161/162 (use v3) |
| NTP | UDP 123 |
| Syslog | UDP 514 / TLS TCP 6514 |
| RADIUS | UDP 1812/1813 |
| LDAP / LDAPS | TCP 389 / 636 |
| DNS / HTTPS / SSH | 53 / 443 / 22 |

# Appendix B — Glossary

- **CIP** — Common Industrial Protocol (ODVA), carried by EtherNet/IP.
- **CIP Security** — TLS/DTLS + certificate-based security extension for EtherNet/IP.
- **Conduit** — an IEC 62443 communication channel between zones (see [IEC62443.md](../02-Standards/IEC62443.md)).
- **DCP** — PROFINET Discovery and Configuration Protocol (Layer 2).
- **DCOM** — Microsoft Distributed COM (underlies OPC Classic).
- **DPI** — Deep Packet Inspection (protocol-aware).
- **ePAC** — Ethernet Programmable Automation Controller (Modicon M580).
- **FR / SR** — IEC 62443 Foundational / System Requirements.
- **GOOSE / SV** — IEC 61850 Layer-2 substation messaging (events / sampled values).
- **IEC 62351** — security standards for telecontrol protocols (DNP3, 60870-5, 61850).
- **openSAFETY** — bus-independent functional-safety protocol (runs over POWERLINK); *safety, not security*.
- **OPC UA FX** — OPC UA Field eXchange (field-level, controller-to-controller).
- **OUC** — Open User Communication (Siemens); Secure OUC adds TLS.
- **POWERLINK** — open real-time Ethernet (EPSG); B&R flagship.
- **PROFINET Security Class 1/2/3** — robustness / authenticity+integrity / + confidentiality.
- **PUT/GET** — Siemens legacy S7 access mechanism; disabled by default on S7-1200/1500.
- **S7comm / S7CommPlus** — Siemens proprietary PLC protocol (0x32 cleartext / 0x72 secured).
- **SAv5** — DNP3 Secure Authentication v5.
- **TSN** — Time-Sensitive Networking (deterministic standard Ethernet; carries OPC UA FX).

# Appendix C — Sources (verified)

Platform and protocol facts in this document were reconciled against primary/official and reputable sources, including:

- **Siemens** — TIA Portal Information System and S7-1500 communication documentation (S7comm/S7CommPlus over ISO-on-TCP port 102; protection levels; PUT/GET default-off; OPC UA server/client, FW v2.0+/V16+, security policy Basic256Sha256 Sign&Encrypt; Certificate Manager / global security settings); Wireshark/Snap7 protocol references (protocol IDs 0x32/0x72, function 0x29 STOP).
- **Schneider Electric** — Modicon M580 catalog and **Modicon Controller Systems Cybersecurity User Guide** (Achilles L2; IEC 62443-4-2 alignment; IP access-control list; disable services; firmware/executable integrity; Memory Protect; RUN/STOP bit; Syslog; IPsec on BMENOC0311; FTP→HTTP/HTTPS from fw v4.10; SNMP deprecation); EcoStruxure Control Expert (ex Unity Pro); Twido programmable controllers software/hardware reference (Modbus serial/TCP, ConneXium TwidoPort 499TWD01100, TWDNOE100, TwidoSuite/TwidoSoft).
- **B&R Industrial Automation** — X20 system, networks/fieldbus, and OPC UA / OPC UA FX pages (POWERLINK 100 Mbit/s ±100 ns; OPC UA over TSN; OPC UA FX founding role; Automation Studio access rights; X20BC0087 Modbus TCP, X20BC00E3 PROFINET).
- **Protocol owners / standards** — Modbus Organization (Modbus Security TLS, X.509, port 802); ODVA (EtherNet/IP, CIP Security); PROFIBUS & PROFINET International (PROFINET classes, DCP, Security Classes); OPC Foundation (OPC UA security model, port 4840); IEC 62351 (DNP3/60870-5/61850 security; DNP3 SAv5; TLS for 104 on port 2404; MMS port 102; GOOSE/SV).

> Because firmware and specifications evolve, treat the above as a snapshot. For version-specific commands, defaults and certification status, defer to the current vendor manual and protocol specification.

# Appendix D — Related Documents

- [IEC62443.md](../02-Standards/IEC62443.md) — Zones & Conduits, Security Levels, FR/SR, SL Risk Assessment, Purdue/ISA-95, Defense in Depth (authoritative for everything referenced minimally here).
- [NIS2.md](../01-Legislation/NIS2.md) — Article 21 risk-management measures, Article 23 incident reporting, supply chain.
- [Czech-Cybersecurity-Act.md](../01-Legislation/Czech-Cybersecurity-Act.md) — Zákon 264/2025 Sb. + Vyhl. 408/409/410/2025; capability domains (esp. 4 IAM, 5 segmentation, 6 cryptography, 7 logging/monitoring, 8 vulnerability mgmt).
- [Network-Segmentation.md](Network-Segmentation.md), [Firewall-Design.md](Firewall-Design.md), [Secure-Remote-Access.md](Secure-Remote-Access.md) — architecture controls (out of scope here).
- [Identity-Management.md](../05-Identity/Identity-Management.md), [PKI.md](../05-Identity/PKI.md), [Certificates.md](../05-Identity/Certificates.md) — identity and certificate lifecycle (critical for OPC UA, CIP Security, IEC 62351, M580 IPsec, S7-1500 certificates).
- [Windows-Hardening.md](../09-Operations/Windows-Hardening.md) — required for OPC DA / DCOM environments.
- Platform docs: [S7-1500.md](../08-Technologies/SIEMENS/S7-1500/S7-1500.md), [TIA-Portal.md](../08-Technologies/SIEMENS/TIA%20Portal/TIA-Portal.md), [Modicon-M580.md](../08-Technologies/Schneider%20Electric/Modicon%20M580/Modicon-M580.md), [EcoStruxure-Control-Expert.md](../08-Technologies/Schneider%20Electric/Control%20Expert/EcoStruxure-Control-Expert.md), [PlantSCADA.md](../08-Technologies/AVEVA/PlantSCADA/PlantSCADA.md), [WebAccess-DMP.md](../08-Technologies/Advantech/WebAccess-DMP/WebAccess-DMP.md), [ICR-2734.md](../08-Technologies/Advantech/ICR-Series/ICR-2734.md), [BR-X20.md](../08-Technologies/B&R/X20/BR-X20.md), [Modicon-Twido.md](../08-Technologies/Schneider%20Electric/Modicon%20Twido/Modicon-Twido.md), [Modicon-Premium.md](../08-Technologies/Schneider%20Electric/Modicon%20Premium/Modicon-Premium.md), [MOXA-NPort-5000.md](../08-Technologies/Moxa/NPort/MOXA-NPort-5000.md), [S7-300.md](../08-Technologies/SIEMENS/S7-300/S7-300.md), [S7-400.md](../08-Technologies/SIEMENS/S7-400/S7-400.md), [PI-System.md](../08-Technologies/AVEVA/PI%20System/PI-System.md), [Kepware-Server.md](../08-Technologies/PTC/Kepware%20Server/Kepware-Server.md), [dataFEED-OPC-Suite.md](../08-Technologies/Softing/dataFEED%20OPC%20Suite/dataFEED-OPC-Suite.md), [Automation-Studio.md](../08-Technologies/B&R/Automation-Studio/Automation-Studio.md). The OPC UA security deep-dive is [OPC-UA.md](OPC-UA.md).

# Revision History

| Version | Date | Description |
|---|---|---|
| 1.0 | 2026-06 | Initial release (separate protocol notes) |
| 1.1 | 2026-06 | Minor consolidation of protocol overviews |
| **1.2.0** | **2026-06** | Consolidated all protocol documents into one source-verified knowledge base/LLM skill; added secure-variant detail (Modbus/TLS, S7CommPlus, PROFINET Security Classes, CIP Security, IEC 62351, DNP3 SAv5) with default ports; added POWERLINK and telecontrol/substation protocols; added **platform chapters for Siemens S7-1500, Schneider Modicon M580, Schneider Twido, B&R X20**; scoped firewall/segmentation and Purdue/Zones-and-Conduits to references; added regulatory verification & audit mapping bound to IEC62443.md, NIS2.md and Czech-Cybersecurity-Act.md (Zákon 264/2025 Sb.) |
| 1.3.0 | 2026-07-07 | Corpus restructure: canonical YAML front matter (id, layer, summary, keywords, language); links converted to layer-relative paths per the numbered directory tree; dangling targets remapped; LF line endings; migrated from skill-style front matter; Appendix D updated (existing platform docs linked incl. PlantSCADA/WebAccess-DMP/ICR-2734, planned docs marked, Industrial-DMZ reference removed) |
| 1.3.1 | 2026-07-07 | Appendix D updated to link the new platform documents (BR-X20, Modicon-Twido, Modicon-Premium, MOXA-NPort-5000) now that they exist; only Automation-Studio remains planned |
| 1.3.2 | 2026-07-07 | Appendix D updated to link the new platform documents (S7-300, S7-400, PI-System) now that they exist |
| 1.3.3 | 2026-07-07 | Appendix D updated to link the new OPC-aggregation platform documents (Kepware-Server, dataFEED-OPC-Suite) |
| 1.3.4 | 2026-07-07 | Added deep-dive delegation note in §12 pointing to the new OPC-UA.md; Appendix D updated — Windows-Hardening.md and Automation-Studio.md links resolved (no longer planned) now that both documents exist |
