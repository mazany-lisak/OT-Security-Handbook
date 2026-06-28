---

title: ISA-95 Overview

category: Standards

version: 1.0.0

status: Stable

author: OT Security Handbook Project

classification: Public

last_reviewed: 2026-06-28

## review_cycle: Annual

# Purpose

This document provides an engineering-oriented overview of the ISA-95 standard from the perspective of an OT Security Architect.

Rather than focusing on manufacturing processes alone, this document explains how ISA-95 helps define responsibilities, information flows and integration between Operational Technology (OT) and enterprise business systems.

ISA-95 is not a cybersecurity standard.

It is a reference model that supports the design of interoperable industrial systems.

---

# What is ISA-95?

ISA-95 is an international standard for the integration of enterprise and control systems.

Its primary objective is to standardize communication and information exchange between business systems and manufacturing operations.

ISA-95 provides a common language for engineers, automation specialists, software developers and business stakeholders.

---

# Why ISA-95 Matters

Modern industrial environments depend on information flowing between production systems and enterprise applications.

Examples include:

* ERP receiving production data.
* MES scheduling manufacturing orders.
* SCADA providing process information.
* Historians collecting operational data.
* Maintenance systems receiving equipment status.

Without a common architectural model, these integrations quickly become difficult to maintain.

---

# Engineering Philosophy

ISA-95 focuses on **business processes and information exchange** rather than network architecture.

It answers questions such as:

* Which system owns production scheduling?
* Where should production orders originate?
* Which systems collect production data?
* Which applications calculate business KPIs?
* How should production information move between systems?

Cybersecurity architectures should respect these functional boundaries.

---

# Functional Levels

ISA-95 aligns closely with the Purdue Model but emphasizes business responsibilities rather than trust boundaries.

```mermaid
flowchart TB

L4[Business Planning & Logistics]

L3[Manufacturing Operations]

L2[Monitoring & Supervisory Control]

L1[Basic Control]

L0[Physical Process]
```

The model defines who performs which business function rather than prescribing security controls.

---

# Level 4 – Business Planning

Typical systems include:

* ERP
* Supply Chain Management
* Financial Systems
* Customer Order Management

Responsibilities:

* Business planning
* Resource management
* Financial reporting
* Customer orders

---

# Level 3 – Manufacturing Operations

Typical systems include:

* MES
* Manufacturing Execution Systems
* Quality Management
* Production Scheduling
* Maintenance Management
* Historians

Responsibilities:

* Production execution
* Quality
* Maintenance
* Performance analysis
* Manufacturing workflows

This level forms the bridge between enterprise planning and industrial control.

---

# Levels 0–2

These levels represent operational automation.

Typical systems include:

* PLCs
* RTUs
* HMIs
* SCADA
* Sensors
* Actuators

ISA-95 generally assumes these systems provide production data rather than business decisions.

---

# Information Flows

ISA-95 defines logical information exchange rather than physical communication.

Typical flows include:

Enterprise → Manufacturing

* Production orders
* Recipes
* Work schedules

Manufacturing → Enterprise

* Production reports
* Equipment status
* Quality information
* Inventory updates
* Performance metrics

Information ownership should always be clearly defined.

---

# Relationship with Purdue

ISA-95 and the Purdue Model complement each other.

| Purdue Model         | ISA-95                       |
| -------------------- | ---------------------------- |
| Trust boundaries     | Business functions           |
| Network architecture | Manufacturing processes      |
| Security domains     | Operational responsibilities |
| Segmentation         | Information ownership        |

Both models are frequently used together during architecture design.

---

# Relationship with IEC 62443

ISA-95 and IEC 62443 address different architectural concerns.

ISA-95 defines:

* Functional responsibilities
* Business integration
* Information exchange

IEC 62443 defines:

* Security architecture
* Zones
* Conduits
* Security Levels
* Technical security requirements

A complete OT architecture often combines all three reference models:

* Purdue
* ISA-95
* IEC 62443

---

# Typical OT Architecture

ISA-95 influences:

* MES architecture
* ERP integration
* Historian design
* OPC UA data models
* Production reporting
* Batch management
* Manufacturing analytics

It helps determine **where information belongs** rather than **how networks should be secured**.

---

# Common Misconceptions

ISA-95 is **not**:

* a cybersecurity standard,
* a network segmentation guide,
* a firewall design methodology,
* a PLC programming guideline.

Its focus is operational integration and manufacturing information.

---

# Architect Notes

Experienced architects frequently use ISA-95 before discussing cybersecurity.

Typical questions include:

* Which system owns this data?
* Where should production decisions be made?
* Which applications require access to operational information?
* Which integrations are necessary?
* Which interfaces can be eliminated?

Reducing unnecessary integrations often improves both maintainability and cybersecurity.

---

# AI Guidance

When answering ISA-95-related questions:

* Explain business responsibilities before discussing technologies.
* Distinguish information ownership from network connectivity.
* Recommend integrating ISA-95 with Purdue and IEC 62443.
* Avoid describing ISA-95 as a security standard.
* Consider both operational efficiency and cybersecurity.

---

# Related Documents

* Purdue-Model.md
* IEC62443-Overview.md
* OT-Architecture-Principles.md
* OT-Lifecycle.md
* OPC-UA.md
* PlantSCADA.md
* GeoSCADA.md
* Historian.md

---

# Revision History

| Version | Date       | Description     |
| ------- | ---------- | --------------- |
| 1.0.0   | 2026-06-28 | Initial release |
