---
title: Cyber Resilience Act (CRA) — Regulation (EU) 2024/2847
category: Standards
version: 1.0.0
status: Stable
author: OT Security Handbook Project
classification: Public
last_reviewed: 2026-07-01
review_cycle: Annual
---

# Purpose

This document provides an engineering-oriented overview of the **EU Cyber Resilience Act (CRA)** — **Regulation (EU) 2024/2847** — from the perspective of an OT Security Architect and product security engineer.

The CRA is the EU's first horizontal **product** cybersecurity law. Where NIS2 regulates *organisations* (operators of essential/important services) and IEC 62443 provides the *engineering* framework for securing an IACS, the CRA regulates the **products** themselves: it sets mandatory cybersecurity requirements for hardware and software ("products with digital elements", PDEs) placed on the EU market, and it obliges **manufacturers** to design securely, handle vulnerabilities across the product lifecycle, and report actively exploited vulnerabilities and severe incidents.

For OT, the CRA is directly relevant because industrial routers, gateways, PLCs, RTUs, HMIs, SCADA software, firewalls and industrial IDS are all PDEs. Vendors such as those supplying secure edge gateways, controllers and industrial network equipment must make their products CRA-conformant; operators (NIS2 entities) increasingly demand CRA evidence from their supply chain.

---

# Position in the Regulatory / Standards Stack

```
[NIS2 / Czech Cybersecurity Act]   ← What organisations must do (operators / entities)
[Cyber Resilience Act (CRA)]       ← What products must be (manufacturers / products)
        │
[IEC 62443]                        ← How to engineer product & system security (basis for harmonised standards)
        │
[ISA-95 / Purdue Model]            ← What exists / where it sits (functional & network-trust models)
```

NIS2 and the CRA are **two sides of the same policy**: NIS2 secures the *entities* that operate systems; the CRA secures the *products* those systems are built from. A single company can be subject to both — as a NIS2 entity *and* as a CRA manufacturer.

---

# Related Documents

| Document | Relationship |
|----------|-------------|
| [NIS2.md](NIS2.md) | Sister EU framework — regulates entities/operators; CRA regulates the products they buy and build |
| [Czech-Cybersecurity-Act.md](Czech-Cybersecurity-Act.md) | Czech transposition of NIS2. Note: the **CRA is a Regulation** — directly applicable, **not** transposed nationally |
| [IEC62443.md](IEC62443.md) | Engineering framework; IEC 62443-4-1/-4-2 are expected to underpin CRA harmonised standards (presumption of conformity) |
| [ISA95.md](ISA95.md) | Functional model — supports the product/asset inventory needed for CRA scoping |
| [ICR-2734-Overview.md](ICR-2734-Overview.md) | Concrete PDE example — an industrial secure edge gateway subject to the CRA (see *Relevance to OT/ICS*) |

---

# What Is the CRA?

Regulation (EU) 2024/2847, the **Cyber Resilience Act**, is a **horizontal regulatory framework** applying to hardware and software products with digital elements made available on the Union market — including both final products and components placed separately on the market, and their **remote data processing solutions**.

Its objectives are to:

* ensure products placed on the EU market are **designed, developed and produced to be secure** ("secure by design / secure by default");
* require manufacturers to operate **vulnerability handling** processes across the whole product lifecycle;
* improve **transparency** so users can factor cybersecurity into purchasing and use;
* create a **single internal-market regime** (CE marking) instead of fragmented national rules.

Key legal characteristics:

* It is a **Regulation**, so it is **directly applicable** in all Member States with **no national transposition** (unlike the NIS2 *Directive*, transposed in the Czech Republic by Zákon č. 264/2025 Sb. — see [Czech-Cybersecurity-Act.md](Czech-Cybersecurity-Act.md)).
* It is a **New Legislative Framework (NLF)** product law: conformity is demonstrated through an **EU Declaration of Conformity** and the **CE marking**, with **market surveillance** by national authorities.
* Legislative path: proposed 15 Sep 2022; adopted by Council 10 Oct 2024; published in the OJ 20 Nov 2024; **entered into force 10 December 2024**.

---

# Scope — "Products with Digital Elements" (PDEs)

A **product with digital elements** is almost any software or hardware product (and its remote data processing solutions) whose intended or reasonably foreseeable use includes a **direct or indirect logical or physical data connection** to a device or network.

In scope, for example: operating systems, firmware, applications and libraries; routers, modems, switches; industrial gateways, PLCs, RTUs, HMIs; firewalls and IDS/IPS (including industrial); IoT/IIoT devices; and the **remote data processing solutions** integral to a product (e.g. a cloud backend a device depends on to function).

Both **final products** and **components placed separately** on the market are covered. "Placed on the market" is assessed **per individual unit**, which matters for the transition (see *Timeline*).

# Exclusions (Article 2(2))

Products already governed by sector-specific EU law, or otherwise carved out, are excluded — chiefly:

* **Medical devices** and **in-vitro diagnostics** (Regulations (EU) 2017/745 and 2017/746);
* **Motor vehicles** (type-approval Regulation (EU) 2019/2144);
* **Civil aviation** products (Regulation (EU) 2018/1139);
* **Marine equipment** (Directive 2014/90/EU);
* Products developed **exclusively for national security or defence**, or to process **classified information**;
* **Spare parts** that replace identical components;
* **Non-commercial open-source software** (see *Open-Source Software Stewards*);
* Pure **cloud computing / SaaS** not sold as a PDE (though remote data processing *integral* to a product is in scope, and SaaS may be caught under NIS2 instead).

---

# Timeline — Key Dates (phased application)

> The most common mistake is to treat the CRA as a single "2027 deadline." It is **phased**, and the **first binding obligation lands in 2026** and reaches products already on the market.

| Date | Milestone |
|------|-----------|
| 20 Nov 2024 | Published in the Official Journal as Regulation (EU) 2024/2847 |
| **10 Dec 2024** | **Entry into force** (clock starts; no immediate manufacturer obligations) |
| **11 Jun 2026** | Chapter IV applies — Member States designate notifying authorities; the **notified-body / conformity-assessment framework** becomes operational |
| **11 Sep 2026** | **Article 14 reporting obligations apply** — manufacturers must report actively exploited vulnerabilities and severe incidents via the **ENISA Single Reporting Platform**. **Applies to products already on the market** (Art. 69(3)) |
| ~Q3 2026 onward | First CRA **harmonised standards** expected (CEN/CENELEC + ETSI under mandate M/606; ~41 standards) |
| **11 Dec 2027** | **Full application** — essential requirements (Annex I), conformity assessment, technical documentation, **CE marking**, SBOM, support-period and all remaining obligations. From this date, non-conformant PDEs **cannot be placed on the EU market** |

Products **placed on the market before 11 Dec 2027** are generally exempt from the full requirements **unless substantially modified** after that date — but **reporting obligations (Art. 14) still apply to them** from 11 Sep 2026.

---

# Economic Operators and Roles

* **Manufacturer** — bears the primary obligations: secure design, conformity assessment, technical documentation, CE marking, vulnerability handling, reporting, and providing security updates for the support period.
* **Authorised representative** — appointed by written mandate to perform defined manufacturer tasks (e.g. cooperation with market surveillance).
* **Importer** — places on the EU market a PDE manufactured outside the Union; must verify the manufacturer met its obligations.
* **Distributor** — makes a PDE available; must act with due care regarding conformity.
* **Open-Source Software (OSS) Steward** — a new, lighter-touch category for legal persons that systematically support the development of OSS intended for commercial products but not themselves the manufacturer. Stewards face a proportionate regime and are **not subject to administrative fines**. Once an entity carries OSS into a commercial product it places on the market, it becomes the **manufacturer** for what it ships.

---

# Product Classification (risk-based → drives conformity route)

All PDEs must meet the **same essential requirements (Annex I)**. Classification changes only the **conformity assessment route** (how conformity is proven). The **core function** determines the class; where several fit, the **stricter** applies. Categories are technically described in Commission Implementing Regulation (EU) **2025/2392** (28 Nov 2025).

| Class | Basis | Examples | Conformity route |
|------|-------|----------|------------------|
| **Default / unclassified** (~90% of PDEs) | Not listed in Annex III or IV | Smart speakers, storage drives, office/media software, simple connected sensors, general-purpose libraries, smart toasters | **Self-assessment** (Module A); may voluntarily use a stricter route |
| **Important — Class I** (Annex III, Part I) | Functions critical to cybersecurity or significant disruption risk | Identity/access management, password managers, VPNs, network management, SIEM, boot managers, PKI issuers, browsers, **routers/modems/switches**, **OS**, microcontrollers with security functions, smart-home security devices (locks, cameras), smart toys | Apply **harmonised standards** → self-assess; **otherwise third-party** (notified body) |
| **Important — Class II** (Annex III, Part II) | Higher risk | Hypervisors, container runtimes, **firewalls, IPS/IDS (including industrial)**, tamper-resistant microprocessors/microcontrollers | **Third-party conformity assessment** (notified body) — Module B+C or H |
| **Critical** (Annex IV) | Critical dependencies for NIS2 essential entities / critical supply chains | Hardware devices with security boxes, **Hardware Security Modules (HSMs)**, **smart meter gateways**, smartcards / secure elements | Commission may **mandate EU cybersecurity certification (EUCC) at level 'substantial' or higher**; otherwise Class II rules |

> **OT note:** industrial **firewalls and industrial intrusion-detection systems** are expressly in **Annex III (Important)**; **routers/switches, operating systems and security microcontrollers** also appear as Important. Many OT products will therefore need harmonised-standard or notified-body routes, not simple self-assessment. See *Relevance to OT/ICS*.

---

# Essential Cybersecurity Requirements (Annex I, Part I)

Products must be made available with **no known exploitable vulnerabilities** and a **secure-by-default** configuration, and — on the basis of a **product risk assessment** — must (as applicable):

* protect against unauthorised access via appropriate **authentication, identity and access management**;
* protect the **confidentiality** of stored/transmitted/processed data (e.g. encryption);
* protect the **integrity** of data, commands, configuration and the product itself;
* process only **data adequate, relevant and limited** to the purpose (**data minimisation**);
* protect **availability** and resilience against denial-of-service;
* minimise the product's **attack surface** and apply **exploit-mitigation** techniques;
* provide **security-related logging and monitoring** of relevant activity;
* enable **secure updates**, and allow users to **securely remove data and settings** (reset);
* ensure vulnerabilities can be **addressed through security updates**, including **automatic updates** with an easy opt-out/postpone where appropriate.

---

# Vulnerability Handling Requirements (Annex I, Part II)

Across the whole **support period**, manufacturers must:

* **identify and document** vulnerabilities and components — including a **Software Bill of Materials (SBOM)** in a commonly used, machine-readable format covering at least the top-level dependencies;
* **address and remediate** vulnerabilities **without delay**, providing **free security updates**;
* apply **effective and regular testing** and reviews;
* once a fix is available, **publicly disclose** information about the vulnerability (description and remediation guidance);
* operate a **coordinated vulnerability disclosure (CVD)** policy and provide a **contact address** for reporting;
* **securely distribute** updates and **disseminate them without delay and free of charge**, with advisories.

## Support Period

Manufacturers must provide security updates for a **support period reflecting the product's expected use time**, which is **expected to be at least five years** unless the product is reasonably expected to be in use for a shorter time. The period must be stated to buyers.

## SBOM and Documentation Retention

The SBOM sits within the **technical documentation (Annex VII)**, which must be retained for **at least 10 years** after the product is placed on the market (or the support period, if longer).

---

# Conformity Assessment and CE Marking

Conformity is demonstrated through the applicable **conformity assessment procedure** for the product's class (self-assessment Module A for default and standards-compliant Important Class I; third-party Modules B+C/H for Class II and non-standards routes; EUCC certification where mandated for Critical). The manufacturer then draws up the **EU Declaration of Conformity (Annex V)** and affixes the **CE marking**.

* **Presumption of conformity**: products conforming to cited **harmonised standards** (CEN/CENELEC, ETSI; mandate M/606) — or to a relevant **EU cybersecurity certification scheme (EUCC)** — are presumed to meet the corresponding essential requirements.
* The CRA's CE marking is **additive**: it is placed alongside compliance with other CE legislation (LVD, EMC, RED, Machinery), not instead of it.
* CE marking is the **visible outcome**; the underlying **risk assessment, vulnerability handling and technical documentation** are what substantiate it.

---

# Reporting Obligations (Article 14)

From **11 September 2026**, manufacturers must notify **two event types** through the **ENISA Single Reporting Platform**, which routes to the relevant **national CSIRT** and ENISA:

1. any **actively exploited vulnerability** contained in the product;
2. any **severe incident** having an impact on the security of the product (e.g. compromise of the manufacturer's update-distribution channel).

**Deadlines:**

| Stage | Deadline |
|------|----------|
| **Early warning** | within **24 hours** of becoming aware |
| **Notification** (initial assessment + corrective/mitigating measures) | within **72 hours** |
| **Final report** | **14 days** after a corrective/security update is available (vulnerability); **1 month** after the notification (severe incident) |

Any natural or legal person may also report vulnerabilities, threats, incidents and near-misses **voluntarily** via the platform. Microenterprises and small enterprises **may not be fined** for missing the 24-hour deadline.

---

# Penalties (Article 64)

| Tier | Maximum fine |
|------|--------------|
| Breach of the **essential requirements (Annex I)** and manufacturer obligations (**Art. 13**) and **reporting (Art. 14)** | **€15 million or 2.5% of total worldwide annual turnover**, whichever is higher |
| Breach of **other obligations** | €10 million or 2% of worldwide annual turnover |
| Supplying **incorrect, incomplete or misleading information** to notified bodies / market surveillance | €5 million or 1% of worldwide annual turnover |

The full penalty regime becomes enforceable with general application on **11 Dec 2027**. Before then, exposure includes market-surveillance ordering powers, civil liability and reputational risk. **OSS stewards** are not subject to administrative fines.

---

# Relationship with NIS2

The CRA and NIS2 are **complementary and mutually reinforcing**:

* **NIS2** ([NIS2.md](NIS2.md)) obliges **entities** to manage cyber risk (Art. 21) and report incidents (Art. 23). Its supply-chain measures (Art. 21(2)(d)) push entities to demand secure products.
* **CRA** obliges **manufacturers** to make those products secure and to report product vulnerabilities/incidents.
* **Critical products (Annex IV)** are explicitly framed as **critical dependencies for NIS2 essential entities**.
* A vendor can be **both**: a NIS2 entity (as an operator) and a CRA manufacturer (for what it ships). The reporting channels differ — NIS2 incident reporting (entity → national CSIRT under the Czech Act) vs. CRA product reporting (manufacturer → ENISA Single Reporting Platform).

# Relationship with IEC 62443

The CRA states *what* products must achieve; **IEC 62443** ([IEC62443.md](IEC62443.md)) provides *how*. The harmonised standards underpinning CRA presumption of conformity are widely expected to build on:

* **IEC 62443-4-1** — Secure product development lifecycle (maps to Annex I Part II vulnerability handling / secure development);
* **IEC 62443-4-2** — Technical security requirements for components (maps to Annex I Part I: identification & authentication, integrity, confidentiality, restricted data flow, resource availability).

A manufacturer with an established 62443-4-1 process and 62443-4-2-aligned components (e.g. an IEC 62443-4-2 SL-aligned industrial device) is well positioned for CRA conformity, though **62443 alignment supports but does not by itself replace CRA conformity assessment**.

# Relationship with RED DA and Other CE Legislation

* **RED Delegated Regulation (EU) 2022/30** currently imposes cybersecurity requirements on internet-connected radio equipment (applicable from 1 Aug 2025). During the transition the two regimes coexist; **from 11 Dec 2027 the CRA absorbs the RED cybersecurity requirements** for products in both scopes, becoming the single reference. This directly affects cellular/wireless OT gateways.
* The CRA adds to, and does not replace, **LVD, EMC, RED and the Machinery Regulation (EU) 2023/1230** in the CE stack.
* Adjacent instruments: **EU Cybersecurity Act / EUCC** (certification used for presumption of conformity), **Cyber Solidarity Act**, and the **AI Act** (a high-risk AI product may face both).

---

# Relevance to OT / ICS

Most OT field and edge equipment are PDEs and fall in scope. Practical implications:

* **Industrial routers, cellular/edge gateways, PLCs, RTUs, HMIs** → typically **Important (Annex III)**; those providing **firewall or industrial IDS/IPS** functions can reach **Class II** (notified-body assessment). A device marketed as a *secure edge gateway* with VPN/firewall is unlikely to qualify for simple self-assessment.
* **Worked example** — an industrial secure edge gateway such as the **[ICR-2734](ICR-2734-Overview.md)**: as a connected industrial router/gateway it is a PDE; its RED-DA cybersecurity obligations converge into the CRA from Dec 2027; and its **S1 / IEC 62443-4-2 SL1-aligned** variant and secure development process directly support CRA Annex I evidence and (via forthcoming harmonised standards) presumption of conformity. See the ICR-2734 knowledge base for the product-side controls (secure boot, signed/encrypted firmware, SBOM-relevant Router-Apps inventory, coordinated vulnerability handling, support-period firmware).
* **Operators (NIS2 entities)** should treat **CRA conformity (CE + Declaration of Conformity + SBOM + support-period commitments)** as a **procurement and supply-chain control** — demanding it satisfies NIS2 Art. 21(2)(d) and the Czech Act supply-chain domain.
* **Legacy OT** (pre-2027 devices) is largely grandfathered for full requirements but is still reachable by **reporting obligations** and by **substantial-modification** triggers — track this in vulnerability/asset management.

---

# Regulatory Verification & Audit Mapping (CRA lens)

| CRA obligation | IEC 62443 ([IEC62443.md](IEC62443.md)) | NIS2 ([NIS2.md](NIS2.md)) | Czech Act 264/2025 ([Czech-Cybersecurity-Act.md](Czech-Cybersecurity-Act.md)) |
|---|---|---|---|
| Secure by design/default (Annex I Part I) | 62443-4-2 FR1–FR7; 62443-4-1 | — (product-side) | — (product-side) |
| Vulnerability handling / CVD / updates (Annex I Part II) | 62443-4-1 (DM/SUM practices) | Art. 21 — vulnerability handling | Domain 8 — Vulnerability management |
| SBOM / component inventory | 62443-4-1 SM | Art. 21 — asset/supply-chain | Domain 2 — Asset mgmt; Domain 11 — Supply chain |
| Product incident/vulnerability reporting (Art. 14) | — | Art. 23 (entity reporting — distinct channel) | Domain 9 — Incident reporting (entity side) |
| Buying conformant products (procurement) | Component SL-C ≥ zone SL-T | Art. 21(2)(d) — supply chain | Domain 11 — Supply chain |

> **Audit-defensible artifact (manufacturer):** technical documentation (Annex VII) = risk assessment + Annex I control evidence + SBOM + vulnerability-handling records + Declaration of Conformity. **(operator):** CRA conformity evidence held per supplied product, linked to the asset inventory (ISA-95-structured — see [ISA95.md](ISA95.md)).

---

# Common Misconceptions

* *"It's a single 2027 deadline."* No — **reporting starts 11 Sep 2026** and reaches products already on the market.
* *"A tool/scanner makes us compliant."* No — the CRA is largely a **process and documentation** obligation; tools only produce evidence.
* *"CE marking is the requirement."* CE is the **visible outcome**; the risk assessment, vulnerability handling and documentation are the substance.
* *"Open source is exempt."* Non-commercial OSS is out and stewards get a lighter regime, but once OSS is shipped in a commercial product, the shipper is the **manufacturer**.
* *"NIS2 already covers us."* NIS2 covers the **entity**; the CRA covers the **product** — different obligations, different reporting channels.
* *"Self-assessment always applies."* Only for default and standards-compliant Important Class I; **Class II and Critical need third parties / certification**.
* *"62443 certification = CRA compliance."* It **supports** conformity (and future presumption via harmonised standards) but does not replace CRA conformity assessment.

---

# Decision Support

Before acting on the CRA, determine:

1. **Applicability** — is the product a PDE, and is it excluded (Art. 2(2))?
2. **Role** — are we manufacturer, importer, distributor, authorised representative, or OSS steward?
3. **Class** — default, Important Class I/II, or Critical (by core function; stricter wins)?
4. **Conformity route** — self-assessment, harmonised standards, notified body, or EUCC?
5. **Annex I gap** — which essential and vulnerability-handling requirements are unmet?
6. **Support period & SBOM** — is a ≥5-year update commitment and machine-readable SBOM in place?
7. **Reporting readiness** — can we meet the **24h/72h/14-day** Article 14 workflow by **11 Sep 2026**?
8. **Documentation** — is Annex VII technical documentation (retained 10 years) being built now?
9. **Procurement (operators)** — are we demanding CRA conformity from suppliers as a NIS2 supply-chain control?

---

# Architect / Manufacturer Notes

The CRA turns "secure by design" from a slogan into a **legal product requirement** backed by CE marking and turnover-based fines. For OT vendors the biggest shifts are the **lifecycle** commitments — a defined **support period with free security updates**, a working **coordinated vulnerability disclosure** process, an **SBOM**, and the **24-hour reporting** muscle — none of which can be retrofitted at the last minute. The **first hard date is September 2026 (reporting)**, not December 2027. Manufacturers with a mature **IEC 62443-4-1** process and **62443-4-2-aligned** components have a substantial head start; operators should convert CRA conformity into a concrete **procurement gate**. Treat the CRA and NIS2 as one programme viewed from the product and the entity side respectively.

---

# AI Guidance

When discussing the CRA:

* Frame it as the EU's **product** cybersecurity law (manufacturers/products), complementary to **NIS2** (entities/operators) and implemented in engineering terms via **IEC 62443**.
* State the **phased timeline** accurately: entry into force **10 Dec 2024**; conformity-assessment framework **11 Jun 2026**; **reporting 11 Sep 2026** (reaches existing products); **full application 11 Dec 2027**.
* Be precise that the CRA is a **Regulation** (directly applicable, **no** national transposition) — unlike the NIS2 Directive (Czech transposition = Zákon 264/2025 Sb.).
* Distinguish the **classes** (default / Important I / Important II / Critical) and that they change the **conformity route**, not the essential requirements.
* Emphasise the **process/documentation** nature: risk assessment, vulnerability handling, **SBOM**, support period, CVD, technical documentation (Annex VII, 10 years), Declaration of Conformity + CE.
* Give the **reporting deadlines** (24h / 72h / 14 days) and the **ENISA Single Reporting Platform**; note the microenterprise/OSS-steward fine carve-outs.
* Quote penalties as **up to €15M or 2.5% of worldwide turnover** for the top tier, and reference **Art. 14/64** and **Annexes I/III/IV**.
* For OT, note that **industrial firewalls/IDS, routers, OS and security microcontrollers** are Important products, and that **RED DA cybersecurity requirements converge into the CRA from Dec 2027**.
* Do not overstate: 62443 or tooling **supports** but does not **replace** CRA conformity assessment; always advise product-specific legal/conformity analysis.

---

# Glossary

* **CRA** — Cyber Resilience Act, Regulation (EU) 2024/2847.
* **PDE** — Product with Digital Elements.
* **Annex I** — essential cybersecurity requirements (Part I) + vulnerability handling (Part II).
* **Annex III / IV** — Important (Class I/II) / Critical product categories.
* **Annex V / VII** — EU Declaration of Conformity / technical documentation.
* **CVD** — Coordinated Vulnerability Disclosure.
* **SBOM** — Software Bill of Materials.
* **EUCC** — EU Common Criteria-based cybersecurity certification scheme.
* **NLF** — New Legislative Framework (EU product-law model: DoC + CE + market surveillance).
* **Single Reporting Platform** — ENISA-operated platform for CRA Art. 14 notifications.
* **OSS steward** — legal person supporting commercial OSS development under a lighter CRA regime.
* **Support period** — the time a manufacturer must provide security updates (expected ≥5 years).

# Sources (verified)

- **Regulation (EU) 2024/2847 (Cyber Resilience Act)** — EUR-Lex official text; **Commission Implementing Regulation (EU) 2025/2392** (28 Nov 2025, technical description of important/critical categories).
- **European Commission — Shaping Europe's Digital Future**: CRA policy page and *"Summary of the legislative text"* (entry into force 10 Dec 2024; Chapter IV 11 Jun 2026; reporting 11 Sep 2026; full application 11 Dec 2027; scope, roles, OSS stewards, penalty carve-outs).
- **ENISA** — CRA implementation materials and the **Single Reporting Platform**; European Vulnerability Database (EUVD).
- **CEN/CENELEC & ETSI** — mandate **M/606** harmonised-standards programme (~41 standards; first expected ~Q3 2026).
- Legal/industry analyses (Hogan Lovells, Pillsbury, Trilateral Research, activeMind.legal, Secure-by-Design Handbook, NewTec, Applus+) for classification examples, exclusions, penalty tiers and the RED-DA→CRA transition.

> Firmware, standards and guidance evolve; treat specifics as a snapshot and defer to the current EUR-Lex text, Commission implementing/delegated acts and ENISA guidance for authoritative, up-to-date detail.

---

# Revision History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | 2026-07-01 | Initial release: scope and exclusions; phased timeline (2024→2027); economic-operator roles incl. OSS stewards; product classification (default/Important I-II/Critical) with conformity routes and OT examples; Annex I essential + vulnerability-handling requirements; SBOM and support period; conformity assessment & CE marking; Article 14 reporting (24h/72h/14d) via ENISA Single Reporting Platform; Article 64 penalty tiers; relationships with NIS2, IEC 62443, RED DA and the CE stack; OT/ICS relevance with ICR-2734 worked example; regulatory verification & audit mapping; misconceptions, decision support and AI guidance; cross-linked to NIS2.md, IEC62443.md, Czech-Cybersecurity-Act.md and ISA95.md |
