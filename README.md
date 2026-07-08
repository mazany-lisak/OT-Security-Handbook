# OT Security Handbook

A structured, vendor-aware knowledge base on Operational Technology (OT/ICS) cybersecurity — written for use as an **LLM knowledge base / RAG corpus** and as a **human-readable reference** for OT security architects, engineers and consultants.

It covers the full stack from regulatory obligation (NIS2, the Czech Cybersecurity Act) through standards and architecture (IEC 62443, ISA-95, Purdue, Zero Trust, Defense in Depth) to operational discipline (patch/change/vulnerability management, incident response, backup and recovery) and vendor-specific platform hardening (Siemens, Schneider Electric, B&R, Advantech, Moxa, AVEVA, PTC, Softing).

> **Current state:** 69 documents, 0 planned, 0 broken internal links. See [INDEX.md](INDEX.md) for the full catalogue and [index.yaml](index.yaml) for the machine-readable manifest.

---

## Who this is for

* **AI assistants / RAG pipelines** answering OT security questions — retrieve by topic, cite the source document, follow delegation links rather than duplicating content.
* **OT security architects and consultants** who want a consistent, cross-referenced reference instead of scattered vendor PDFs and blog posts.
* **Engineers and asset owners** hardening a specific platform (a PLC family, a SCADA system, a remote-access gateway) who need both the *why* (architecture, standards) and the *how* (phased hardening, decision checklists) in one place.

This is a **reference corpus, not a compliance certification**. It states engineering principles and defensible defaults; it does not replace a qualified risk assessment, a lawyer, or the vendor's own current documentation for the exact hardware/firmware/software version in front of you.

---

## How the corpus is organised

Documents live in a numbered directory tree. The number is the reading order for a human working top-down; for retrieval, treat each layer as an independent topic area.

| Layer | Contents |
|---|---|
| `00-Core` | Mindset, principles and decision method the whole handbook assumes — read this first. The canonical Safety → Availability → Integrity → Confidentiality priority hierarchy lives here. |
| `01-Legislation` | Legal obligations: NIS2 and the Czech national transposition. |
| `02-Standards` | IEC 62443, ISA-95, the Purdue Model, the EU Cyber Resilience Act — what to protect and where it sits. |
| `03-Architecture` | The three complementary architectural strategies: Defense in Depth, Risk-Based Security, Zero Trust. |
| `04-Network` | Network trust boundaries and their enforcement: segmentation, firewalls, protocols, remote access. |
| `05-Identity` | Identity as the primary trust boundary: identity management, PKI/certificates, MFA, PAM. |
| `06-Vulnerability-Management` | The scoring/intelligence inputs — CVE, CVSS, EPSS, CISA KEV. |
| `07-Threat-Intelligence` | Attacker-behaviour models: MITRE ATT&CK for ICS, the ICS Kill Chain. |
| `08-Technologies` | Vendor/product platform references, organised `Vendor/Product/Document.md`, realising the vendor-neutral guidance above in specific hardware and software. |
| `09-Operations` | Operate/maintain disciplines across the system lifecycle: change, configuration, patch and vulnerability management, monitoring, logging, backup, disaster recovery, incident response. |
| `10-Templates` | The canonical front-matter/section template for authoring new documents. |

Every document also carries a **`category`** in its front matter equal to its layer name, so the tree and the metadata never disagree.

---

## Reading a document

Every document uses the same shape:

1. **YAML front matter** — machine-readable metadata (see schema below).
2. **Purpose / Scope** — what this document is for, and what it deliberately delegates elsewhere.
3. **Numbered body sections** — architecture, threat model, hardening, identity, communications, monitoring, backup/recovery, as relevant to the topic.
4. **Common Mistakes**, **Architect Notes**, **Decision Support** — practitioner-facing synthesis.
5. **AI Guidance** — explicit instructions for how an LLM should use this document (what to emphasise, what to delegate, what never to assert from memory).
6. **Sources / Grounding** — the standards and vendor documentation behind the content, with an explicit note that the current vendor/standard text is authoritative for anything version-specific.
7. **Related Documents** and **Revision History**.

### Front matter schema

```yaml
id: kebab-case-slug            # stable identifier, independent of title changes
title: "Human-Readable Title"
category: Core|Legislation|Standards|Architecture|Network|Identity|
          Vulnerability-Management|Threat-Intelligence|Technologies|Operations
layer: "NN-Layer[/Vendor/Product]"   # directory path within the tree
vendor: "Vendor Name"           # 08-Technologies only
product: "Product Name"         # 08-Technologies only
version: 1.0.0                  # semver — see CONTRIBUTING.md for bump rules
status: Stable | Draft | Planned
author: OT Security Handbook Project
classification: Public
language: en | cs | mixed
last_reviewed: YYYY-MM-DD
review_cycle: Annual
summary: >-
  One to three sentences — used for retrieval context and for generating
  INDEX.md / index.yaml.
keywords: [english term, český termín, abbreviation, synonym]
```

---

## Key conventions (the load-bearing rules)

* **Single source of truth.** Overlapping topics are owned by exactly one document; every other document links to it instead of repeating it. Examples: protocol security → `OT-Protocols.md`; zones/conduits → `IEC62443.md`; the layered defense model → `Defense-in-Depth.md`; Zero Trust → `Zero-Trust-in-OT.md`; vulnerability prioritisation → `Vulnerability-Management.md`. Before adding new material, check whether it belongs in an existing document instead.
* **Vendor neutrality above 08-Technologies.** Layers `00`–`07` and `09` recommend *capabilities*, not products. Products live exclusively in `08-Technologies`.
* **Legacy vs. modern framing.** A platform with genuine native security (e.g. a current PLC generation) is documented around *hardening what it offers*. A platform without native security (an end-of-life PLC, a bridging device) is documented around *compensating controls, containment and migration* — never pretend a device can be hardened into something it fundamentally is not.
* **Links are relative paths within the tree**, not bare filenames — e.g. `[IEC62443.md](../02-Standards/IEC62443.md)`. File **basenames are corpus-unique**, which is what makes the flat cross-referencing between distant layers possible.
* **Nothing version-specific is asserted from memory.** Ports, defaults, firmware capabilities and PSIRT advisories are flagged for verification against the vendor's current documentation for the exact deployed version.
* **Bilingual where the subject matter requires it.** The Czech Cybersecurity Act document is written bilingually (Czech legal terminology, English structure); `keywords` on every document intentionally include both English and Czech terms to support retrieval in either language.

---

## Using this corpus

* **As a RAG source:** chunk by heading; keep the YAML front matter (or at least `summary`/`keywords`) attached to every chunk for retrieval context; prefer the document a link points to over re-deriving its content.
* **As an LLM skill:** point the assistant at `INDEX.md` first so it has the map, then let it navigate via relative links; the *AI Guidance* section in each document is written specifically for this use.
* **As a human reference:** start at `00-Core`, then follow the layer that matches your question — architecture questions to `02`–`04`, a specific product to `08`, an operational process to `09`.

## Corpus status and gaps

`INDEX.md` lists every document with its current version and a one-line summary, plus a **Planned** table for documents the corpus already references but does not yet contain (their reserved path is listed so links resolve the moment they're written). `index.yaml` is the same information in machine-readable form for tooling. Both are regenerated whenever a document is added, moved, renamed or re-versioned — see [CONTRIBUTING.md](CONTRIBUTING.md).

## Classification and scope of authority

All documents are `classification: Public` and are **foundational/engineering references, not normative text**. Where a document discusses a legal obligation (NIS2, the Czech Cybersecurity Act) or a standard (IEC 62443), it explicitly says so and directs the reader to the current official text for anything that carries legal or certification weight. Nothing in this corpus should be cited as compliance evidence on its own.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to add, edit or restructure documents, the versioning rules, and the checklist for keeping `INDEX.md`/`index.yaml` and cross-links consistent.
