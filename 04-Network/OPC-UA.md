---
id: opc-ua
title: "OPC UA — Security Architecture Deep-Dive"
category: Network
layer: "04-Network"
version: 1.0.0
status: Stable
author: OT Security Handbook Project
classification: Public
language: en
last_reviewed: 2026-07-07
review_cycle: Annual
summary: >-
  OPC UA security deep-dive and single source of truth for the protocol: security policies and
  modes (None/Sign/SignAndEncrypt), certificate and trust-list lifecycle, user authentication and
  node-level access control, PubSub and OPC UA over TSN/FX, architecture placement, and detection.
keywords: [OPC UA, IEC 62541, security policy, Sign and Encrypt, certificate, trust list, GDS, PubSub, OPC UA over TSN, OPC UA FX, node-level access control, role permissions]
---

# 1. Purpose

This document is the **single source of truth** for OPC UA (IEC 62541) security in this handbook. [OT-Protocols.md](OT-Protocols.md) covers OPC UA at protocol-survey level alongside every other industrial protocol; this document is the full depth behind that entry — security policies and modes, certificate and trust-list lifecycle, user authentication, node-level access control, PubSub security, and OPC UA over TSN/FX — referenced from every document in the corpus that discusses OPC UA rather than re-explaining it.

OPC UA is unusual among the protocols in this handbook: it is **security-by-design**, built with authentication, integrity and confidentiality as first-class features rather than bolted on afterward. That is exactly why it is the preferred choice for new PLC↔SCADA↔historian↔MES↔cloud integrations across this corpus. It is also why getting it wrong is a *configuration* failure, not an *inherent* one — nearly every OPC UA security incident traces back to a deployment running with security disabled, certificates unmanaged, or authentication left anonymous, not to a flaw in the standard itself.

---

# 2. Scope

This document covers: the OPC UA security model (policies, modes, cipher suites); certificate and trust management (the practical discipline that makes or breaks an OPC UA deployment); user authentication and node-level authorization; PubSub security; OPC UA over TSN and OPC UA FX (field-level); architectural placement; and detection signatures. It does **not** cover: general PKI architecture (see [PKI.md](../05-Identity/PKI.md)), certificate lifecycle mechanics common to all certificate types (see [Certificates.md](../05-Identity/Certificates.md)), or platform-specific OPC UA implementation detail (see the relevant document in `08-Technologies`).

---

# 3. Why OPC UA Matters

OPC UA (IEC 62541) removes the dependency that made its predecessor, **OPC Classic (OPC DA)**, so difficult to secure: **Windows COM/DCOM**. DCOM's dynamic RPC port allocation, opaque ACL model and tight OS coupling made every OPC DA deployment a firewall and identity headache (see [OT-Protocols.md](OT-Protocols.md) § 13 and [Windows-Hardening.md](../09-Operations/Windows-Hardening.md) for the DCOM containment pattern this replaces). OPC UA is platform-independent, transport-agnostic, and carries a rich **information model** — objects, variables, methods, events and types — so a client understands data *semantics*, not just raw register values.

For architects, the practical consequence is this: **OPC UA can genuinely satisfy meaningful parts of IEC 62443 FR1/FR3/FR4 at the protocol layer**, which almost no other OT protocol in this handbook can claim. That capability is not automatic — it must be deliberately configured and then operated with discipline (§ 6) — but it is real, and it is why this handbook consistently recommends migrating away from unauthenticated legacy protocols toward OPC UA wherever a controller or platform supports it.

---

# 4. Architecture and Communication Models

* **Client/Server** — the dominant model in this handbook: a server (controller, SCADA, historian, OPC aggregator) exposes an address space; clients connect, browse, read, write, subscribe and call methods.
* **PubSub** — scalable, event-driven publish/subscribe, suited to IIoT and many-to-many data distribution; can run over UDP, MQTT or AMQP as the underlying transport, and over **TSN** for deterministic delivery (§ 9).
* **Discovery** — Local and Global Discovery Servers (LDS/GDS) let clients find servers and, in a GDS deployment, centralise certificate management across an estate (§ 6.4). Discovery services should themselves be access-controlled in production — an open discovery service is a reconnaissance aid.

Default endpoint: `opc.tcp://` on **TCP 4840** for the binary protocol (HTTPS-bound bindings also exist for specific use cases). This port carries meaningfully-secured traffic only when the security settings in § 5 are actually applied — the port number itself implies nothing about security.

---

# 5. Security Policies and Message Security Modes

OPC UA's security is negotiated per session through two independent choices:

**Message Security Mode:**

* **None** — no signing, no encryption. Equivalent to running the protocol with its security-by-design capability switched off. **Never use in production.**
* **Sign** — messages are signed for integrity and origin authentication, but not encrypted; contents remain readable on the wire.
* **SignAndEncrypt** — signed and encrypted. **The only mode this handbook recommends for production OT traffic.**

**Security Policy** (selects the cryptographic algorithm suite):

* Deprecated/weak: **None**, **Basic128Rsa15**, **Basic256** — disable these on every server and client.
* Current: **Basic256Sha256**, and the newer **Aes128-Sha256-RsaOaep** / **Aes256-Sha256-RsaPss** policies where server and client support them — prefer the strongest policy both ends support, and disable everything weaker at the server rather than merely "preferring" the strong option and leaving the weak one reachable.

**The default-permissive trap.** Many OPC UA stacks ship with `None`/`Sign` and even Anonymous authentication enabled out of the box to simplify first connection during commissioning. Left unchanged, this is the single most common OPC UA security failure in real deployments — commissioning convenience becomes permanent production exposure. Explicitly configure and verify **SignAndEncrypt + a current Security Policy + non-Anonymous authentication** before go-live, and re-verify after every firmware/software update, since updates occasionally reset security configuration to vendor defaults.

---

# 6. Certificates and Trust — "Successful OPC UA Projects Are PKI Projects"

Every OPC UA application instance — each server and each client — owns its own **X.509 application instance certificate**. This is the load-bearing fact of OPC UA security: **the protocol's security is only as good as the certificate and trust management around it**, and poor certificate management is the most common root cause of insecure OPC UA deployments in practice.

## 6.1 The Certificate Lifecycle

Generate → distribute → explicitly trust → monitor expiry → renew → revoke, exactly as for any certificate-dependent system ([Certificates.md](../05-Identity/Certificates.md)). OPC UA-specific points:

* Each application (not each host, not each user) has its own certificate — a server with three client integrations should see three distinct client certificates, not one shared credential.
* Certificates should be issued from the organisation's internal **PKI** ([PKI.md](../05-Identity/PKI.md)), not self-signed and manually trusted per connection — self-signed "trust it once" workflows are a commissioning shortcut that becomes an unmanaged, unauditable trust relationship in production.

## 6.2 Trust Lists — Never "Trust Any"

Every OPC UA server and client maintains a **trust list** of certificates (or issuing CAs) it accepts. The single most damaging trust-list misconfiguration is a blanket "accept any client certificate" or "auto-accept new certificates" setting — this is functionally equivalent to disabling authentication while leaving the UI showing that security is "on." Configure explicit, reviewed trust; treat any auto-accept setting as unacceptable in production.

## 6.3 Expiry Monitoring and Renewal

Certificate expiry is one of the most common *availability* incidents in OPC UA deployments — an expired certificate silently breaks a production integration, often discovered only when a SCADA screen goes stale. Monitor expiry proactively ([Certificates.md](../05-Identity/Certificates.md) § Certificate Expiration) and renew before expiry as a scheduled, tested operational process, not an emergency response.

## 6.4 Global Discovery Server (GDS)

In larger estates, a **GDS** centralises certificate issuance, trust-list distribution and revocation across many OPC UA applications, turning per-server manual trust management into a governed, auditable process. Consider a GDS wherever the number of OPC UA endpoints makes manual trust-list maintenance error-prone — which, in practice, is most real industrial estates beyond a handful of servers.

## 6.5 Revocation

Revoke and remove trust immediately when an application is decommissioned, a private key is suspected compromised, or an integration is retired — a forgotten, still-trusted client certificate is a standing access path with no corresponding active use to notice its absence.

---

# 7. User Authentication and Node-Level Access Control

Transport security (§ 5) answers "is this connection protected." It does not by itself answer "who is this client allowed to be, and what may they do." Two further layers apply:

## 7.1 User Token Policies

OPC UA servers support multiple **user identity token** types for the session layer, independent of the application-certificate transport authentication:

* **Anonymous** — no user identity presented. **Avoid in production**; it discards exactly the accountability that makes OPC UA superior to legacy protocols.
* **Username/Password** — simple and common; must run only over **SignAndEncrypt** (never send credentials over an unencrypted session), and should draw from the organisation's identity provider rather than local server accounts where the platform supports it ([Identity-Management.md](../05-Identity/Identity-Management.md)).
* **Certificate-based user authentication** — the user (not just the application) is identified by their own certificate; the strongest option, appropriate for privileged/engineering access ([MFA.md](../05-Identity/MFA.md), [PKI.md](../05-Identity/PKI.md)).
* **IssuedToken** (e.g. federated/SSO tokens) — where the platform integrates with a broader identity federation.

## 7.2 Role-Based Authorization and Node-Level Access Control

OPC UA's information model allows **fine-grained, per-node access control**: a Role Permissions mechanism can restrict read, write, browse and method-call rights down to individual nodes in the address space, not just at the session or server level. Use this deliberately — grant a monitoring client read-only access to the tags it needs, not the entire address space by default; reserve write and method-call rights on control-relevant nodes for the specific, named integrations that require them.

**Application authentication (§ 6) and user authentication (§ 7.1) are not substitutes for each other.** A compromised application certificate with an Anonymous user session, or valid user credentials over an untrusted application connection, both represent incomplete authorization. Require both, and layer node-level authorization (§ 7.2) on top so that even a legitimately authenticated session cannot exceed its intended scope.

---

# 8. PubSub Security

PubSub's decoupled publish/subscribe model requires its own security handling since there is no persistent client/server session to secure:

* Enable **dataset signing and encryption** where the PubSub security profile supports it — an unsecured PubSub deployment broadcasts process data (and, for writable datasets, accepts commands) with no cryptographic protection at all.
* Where PubSub rides over MQTT, apply the same broker security discipline documented in [OT-Protocols.md](OT-Protocols.md) § 16 (TLS, authenticated broker, authorized topics) in addition to OPC UA's own dataset security — the two layers are complementary, not redundant.
* Key management for PubSub security groups should follow the same lifecycle discipline as § 6 — PubSub does not get a lighter-touch certificate/key regime just because it lacks a session handshake.

---

# 9. OPC UA over TSN and OPC UA FX

**OPC UA over TSN** (Time-Sensitive Networking) extends OPC UA PubSub with deterministic, low-latency delivery suitable for motion control and hard real-time applications — closing the gap that historically required a separate deterministic fieldbus (PROFINET IRT, EtherCAT, POWERLINK) alongside a separate OPC UA layer for supervisory integration. **OPC UA FX** (Field eXchange) extends this further to field-level device-to-device and device-to-controller communication, aiming to bring OPC UA's security model down to where fieldbus protocols traditionally had none.

B&R is a recognised early implementer of both OPC UA over TSN and OPC UA FX; see [BR-X20.md](../08-Technologies/B&R/X20/BR-X20.md) for platform-specific detail. Architecturally, treat OPC UA over TSN/FX exactly as any OPC UA deployment for security purposes (§§ 5–7) — determinism and security are independent properties, and TSN's real-time guarantees do not substitute for enabling SignAndEncrypt and managed trust.

---

# 10. Architecture and Placement

**Encryption is not a substitute for segmentation.** A SignAndEncrypt OPC UA session is still a live conduit between two zones; place OPC UA servers according to the zone/conduit model ([IEC62443.md](../02-Standards/IEC62443.md)), typically at the supervisory zone or Industrial DMZ boundary for cross-zone integration ([Network-Segmentation.md](Network-Segmentation.md), [Purdue-Model.md](../02-Standards/Purdue-Model.md)), and firewall the conduit explicitly ([Firewall-Design.md](Firewall-Design.md)) rather than treating "it's OPC UA, it's secure" as a reason to skip network containment.

OPC UA aggregation platforms in this handbook ([Kepware-Server.md](../08-Technologies/PTC/Kepware%20Server/Kepware-Server.md), [dataFEED-OPC-Suite.md](../08-Technologies/Softing/dataFEED%20OPC%20Suite/dataFEED-OPC-Suite.md)) apply exactly this principle: OPC UA is the preferred *northbound* interface precisely because it can be secured end-to-end, while southbound legacy protocols remain contained within their source zone regardless of what wraps them northbound.

---

# 11. Monitoring and Detection

Forward to the SIEM ([SIEM.md](../09-Operations/SIEM.md)) and treat as high-value detections ([Monitoring.md](../09-Operations/Monitoring.md), [Logging.md](../09-Operations/Logging.md)):

* **Certificate validation failures** — untrusted certificate presented, expired certificate, revoked certificate used; a spike or a new failing source is worth investigating immediately.
* **New or unexpected client certificates/connections** — any application connecting that isn't in the inventory of expected integrations.
* **Security policy or mode downgrade attempts** — a client attempting to negotiate `None`/`Sign` where `SignAndEncrypt` is expected, or attempting a deprecated policy, is a textbook downgrade-attack signature.
* **Anonymous authentication attempts** where user authentication is required and enforced.
* **Trust list and server security-configuration changes** — who changed it, when, and was it authorised.
* **Certificate expiry approaching** (proactive, not reactive — § 6.3).
* **Node-level write/method-call activity** on control-relevant nodes, correlated with the authenticated identity that issued it — the OPC UA equivalent of the command-audit detections documented throughout `08-Technologies` platform documents.

---

# 12. Common Design Mistakes

* Leaving Message Security Mode at `None` or `Sign` (unencrypted) in production because that's what the vendor default shipped with.
* Anonymous user authentication left enabled "because the integration worked without credentials."
* Trust lists set to auto-accept any certificate — disabling authentication while appearing to have it configured.
* Self-signed certificates manually trusted per connection instead of issuing from a managed internal PKI.
* No certificate expiry monitoring — the integration silently fails when a certificate lapses, discovered only by the resulting outage.
* Treating OPC UA's built-in security as a substitute for network segmentation ("it's encrypted, so it doesn't need a firewall rule").
* Granting broad, server-wide read/write access to every client instead of using node-level Role Permissions.
* Assuming OPC UA over TSN/FX is inherently secure because it is deterministic — determinism and security are unrelated properties.
* Not re-verifying security configuration after firmware/software updates that may reset it to defaults.

---

# 13. Architect Notes

OPC UA is the rare case in OT security where the protocol itself is not the weak link — the discipline around it is. Every failure mode in § 12 is a configuration and operational-process failure, not a limitation of the standard. That reframes the architect's job: the technical decision (choose OPC UA over legacy alternatives) is usually easy and already made by this handbook's recommendations elsewhere; the hard, recurring work is treating certificate issuance, trust-list management and expiry monitoring as a **permanent operational process** — exactly the PKI discipline documented in [PKI.md](../05-Identity/PKI.md) and [Certificates.md](../05-Identity/Certificates.md) — rather than a one-time commissioning task. An estate with fifty OPC UA integrations and no certificate governance process will, with certainty, eventually run several of them on expired certificates, auto-accept trust, or both.

---

# 14. Decision Support

1. Is Message Security Mode set to SignAndEncrypt on every production OPC UA endpoint, with `None`/`Sign` and deprecated Security Policies explicitly disabled?
2. Is Anonymous user authentication disabled, with named users or certificate-based user authentication in place?
3. Are application instance certificates issued from the internal PKI, with trust lists explicitly managed (no auto-accept)?
4. Is certificate expiry monitored proactively, with a scheduled renewal process?
5. Is node-level Role Permissions access control applied, so clients hold only the read/write/method-call scope they actually need?
6. Are OPC UA servers placed and firewalled according to the zone/conduit model, not exposed on the assumption that encryption alone is sufficient?
7. For PubSub deployments, is dataset signing/encryption enabled, and is the underlying transport (e.g. MQTT broker) independently secured?
8. Are certificate failures, downgrade attempts and node-level writes monitored and forwarded to the SIEM?

---

# 15. AI Guidance

When discussing OPC UA:

* Treat this document as the authoritative deep-dive for OPC UA security; delegate protocol-survey-level mentions to OT-Protocols.md and platform-specific implementation detail to the relevant 08-Technologies document.
* Always recommend SignAndEncrypt with a current Security Policy (Basic256Sha256 or newer) and non-Anonymous authentication as the production baseline; flag `None`/`Sign`/deprecated policies/Anonymous auth as findings requiring remediation, not acceptable defaults.
* Frame certificate and trust-list management as an ongoing operational process, not a one-time setup step — this is the most common real-world OPC UA failure mode.
* Distinguish application authentication (certificates, § 6) from user authentication (§ 7.1) from node-level authorization (§ 7.2); recommend all three layered together, never one as a substitute for another.
* Never present OPC UA's built-in security as a substitute for network segmentation — recommend zone/conduit placement and firewalling regardless of transport security.
* For OPC UA over TSN/FX, explain that determinism and security are independent properties; apply the same certificate/authentication guidance regardless of the real-time transport.
* Verify server/client-specific defaults, supported Security Policy versions, and vendor PSIRT advisories against current vendor documentation rather than asserting them from memory.

---

# 16. Sources / Grounding

* **IEC 62541** (OPC UA specification series, OPC Foundation) — the authoritative technical specification for the security model, information model and PubSub/TSN extensions described in this document.
* **OPC Foundation OPC UA Security Best Practices** guidance — operational hardening recommendations for certificate management, security policies and trust configuration.
* **OPC Foundation Field Level Communications (FLC) initiative** — OPC UA over TSN and OPC UA FX specifications.
* **ISA/IEC 62443** — FR1/FR3/FR4 alignment and zone/conduit placement context. See [IEC62443.md](../02-Standards/IEC62443.md).
* **NIST SP 800-82 Rev. 3** — OT protocol security context.

> Foundational references; defer to the current OPC Foundation specification and vendor implementation documentation for authoritative, version-specific detail.

---

# 17. Related Documents

* [OT-Protocols.md](OT-Protocols.md)
* [Network-Segmentation.md](Network-Segmentation.md)
* [Firewall-Design.md](Firewall-Design.md)
* [Purdue-Model.md](../02-Standards/Purdue-Model.md)
* [IEC62443.md](../02-Standards/IEC62443.md)
* [Zero-Trust-in-OT.md](../03-Architecture/Zero-Trust-in-OT.md)
* [Identity-Management.md](../05-Identity/Identity-Management.md)
* [PKI.md](../05-Identity/PKI.md)
* [Certificates.md](../05-Identity/Certificates.md)
* [MFA.md](../05-Identity/MFA.md)
* [Monitoring.md](../09-Operations/Monitoring.md)
* [Logging.md](../09-Operations/Logging.md)
* [SIEM.md](../09-Operations/SIEM.md)
* [Kepware-Server.md](../08-Technologies/PTC/Kepware%20Server/Kepware-Server.md)
* [dataFEED-OPC-Suite.md](../08-Technologies/Softing/dataFEED%20OPC%20Suite/dataFEED-OPC-Suite.md)
* [BR-X20.md](../08-Technologies/B&R/X20/BR-X20.md)
* [PlantSCADA.md](../08-Technologies/AVEVA/PlantSCADA/PlantSCADA.md)

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | 2026-07-07 | Initial release — new 04-Network protocol deep-dive establishing OPC-UA.md as the single source of truth for OPC UA security: security policies and message security modes, the certificate/trust-list lifecycle ("successful OPC UA projects are PKI projects"), Global Discovery Server governance, user authentication vs. node-level Role Permissions authorization, PubSub security, OPC UA over TSN/FX, architecture placement, and detection signatures; resolves the Planned manifest entry referenced by Zero-Trust-in-OT.md, OT-Protocols.md, Logging.md, Monitoring.md, Identity-Management.md, PKI.md, Certificates.md, MFA.md and PlantSCADA.md |
