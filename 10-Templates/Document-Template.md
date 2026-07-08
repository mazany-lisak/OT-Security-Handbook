---
id: <kebab-case-slug>                # stable identifier; never changes even if title does
title: "<Document Title>"
category: <Core|Legislation|Standards|Architecture|Network|Identity|Vulnerability-Management|Threat-Intelligence|Technologies|Operations>
layer: "<NN-Layer[/Vendor/Product]>" # directory path of this document within the tree
# vendor: "<Vendor>"                 # 08-Technologies only
# product: "<Product>"               # 08-Technologies only
version: 1.0.0                       # semver; bump minor for content changes, patch for editorial
status: Draft                        # Draft | Stable | Planned
author: OT Security Handbook Project
classification: Public
language: en                         # en | cs | mixed
last_reviewed: YYYY-MM-DD
review_cycle: Annual
summary: >-
  One to three sentences describing what this document covers and what question it answers.
  Used for retrieval context and for generating INDEX.md / index.yaml.
keywords: [english term, český termín, abbreviation, synonym]
---

# Purpose

What this document is for, in two or three short paragraphs. State explicitly what is in scope
and which related documents own the neighbouring topics (single source of truth — link, do not repeat).

---

# Scope

Bullet list of covered topics; explicit delegation notes for excluded ones
(e.g. "Zones and conduits are owned by [IEC62443.md](../02-Standards/IEC62443.md)").

---

# <Main Content Sections>

Prefer self-contained sections: a heading plus its text should remain understandable when
retrieved as an isolated chunk, so repeat the subject noun instead of "it/this" where cheap.
Use relative links to other documents; never bare basenames.

---

# Common Mistakes

---

# Decision Support

Numbered checklist of questions to answer before acting.

---

# AI Guidance

Topic-specific instructions for LLM/RAG usage of this document
(what to emphasise, what to delegate, safety caveats).

---

# Sources / Grounding

Authoritative standards/references, with the note that normative detail lives in the standards.

---

# Related Documents

* Use relative links within the tree, e.g. `[IEC62443.md](../02-Standards/IEC62443.md)` — never bare basenames.

---

# Revision History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0.0 | YYYY-MM-DD | Initial release |
