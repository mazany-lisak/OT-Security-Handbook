# Contributing to the OT Security Handbook

This corpus is only as useful as its internal consistency: a single source of truth per topic, working relative links, accurate metadata, and a front matter schema every document shares. This guide is the checklist for keeping it that way — for a human contributor or an AI assistant editing the corpus.

Before changing anything, read [README.md](README.md) for the conventions this guide assumes (single source of truth, vendor neutrality above `08-Technologies`, legacy-vs-modern framing, relative links, basename uniqueness).

---

## 1 — Before you write anything

**Check whether the material already belongs somewhere else.** The single most common mistake in a corpus like this is re-explaining a topic another document already owns. Before adding content:

1. Search `INDEX.md` / `index.yaml` for the topic by keyword.
2. If a document owns it, **link to it** instead of repeating it — even a partial overlap should become a one-line summary plus a link, not a parallel explanation.
3. If you're genuinely extending an owned topic (new detail, not duplicate detail), edit that document rather than creating a new one.
4. Only create a new document when the topic is genuinely new ground — a new standard, a new architectural strategy, a new operational discipline, or a new vendor/product.

**Decide the layer.** Match the numbered layer to the content, not to convenience:

* Principles/mindset applicable everywhere → `00-Core`.
* A legal obligation → `01-Legislation`.
* A standard or reference model → `02-Standards`.
* An architectural strategy (how to design) → `03-Architecture`.
* Network-layer implementation → `04-Network`.
* Identity-layer implementation → `05-Identity`.
* Vulnerability scoring/intelligence → `06-Vulnerability-Management`.
* Attacker-behaviour modelling → `07-Threat-Intelligence`.
* A specific vendor product → `08-Technologies/Vendor/Product/`.
* An operate/maintain discipline → `09-Operations`.

If you're unsure between two layers, prefer the one that keeps the document **vendor-neutral** unless it genuinely belongs in `08-Technologies`.

---

## 2 — Authoring a new document

Start from [10-Templates/Document-Template.md](10-Templates/Document-Template.md) — it carries the exact front-matter schema and section skeleton described below. Do not build a new document's structure from scratch.

### 2.1 Front matter

Fill in every field; nothing is optional except `vendor`/`product` (08-Technologies only):

| Field | Rule |
|---|---|
| `id` | kebab-case, derived from the filename, stable for the document's life even if `title` changes |
| `title` | human-readable, quoted |
| `category` | must equal the top-level layer name (e.g. `Standards`, `Operations`, `Technologies`) |
| `layer` | the full directory path of this document, quoted |
| `vendor` / `product` | 08-Technologies only; omit elsewhere |
| `version` | `1.0.0` for a new document — see § 4 for bump rules on edits |
| `status` | `Stable` for a complete document; `Draft` only if genuinely incomplete; `Planned` is set by the manifest, not the document itself (a Planned entry has no file yet — see § 5) |
| `language` | `en`, `cs`, or `mixed` |
| `last_reviewed` | today's date |
| `summary` | 1–3 sentences, written for retrieval — this is what shows up in `INDEX.md` and is often the only context a RAG chunk carries |
| `keywords` | mix English and Czech/vendor/abbreviation terms deliberately; this is what makes cross-lingual and acronym search work |

### 2.2 Document structure

Follow the shape every existing document uses (see the template for the literal skeleton): **Purpose → Scope/Related Documents → numbered body sections → Common Mistakes → Architect Notes → Decision Support → AI Guidance → Sources/Grounding → Related Documents → Revision History.**

For **08-Technologies platform documents** specifically, match the depth and structure of an existing peer document in the same category before writing:

* A **modern, security-capable platform** (native user management, secure protocols, signed firmware) → structure around *phased hardening of what it offers*. Use a current PLC or SCADA document as the reference shape.
* A **legacy/end-of-life platform with no native security** → structure around *security-capability reality, compensating controls, containment and migration* — do not write a hardening-phase document for a device that cannot be hardened. Use a legacy-controller document as the reference shape.
* A **data-plane / aggregation / management-plane platform** (historian, OPC aggregator, device-management platform) — a different threat model again: usually broad read access or bidirectional conduit risk rather than direct process control. Frame the threat model around what the platform's *role* actually exposes, not a generic template.
* Two products in the **same family** (e.g. a base model and its high-end/redundant sibling) may use a **delegating, differential document**: the second document summarises shared material with a link to the first and concentrates entirely on what is genuinely different (scale, redundancy, additional capability). Do not re-derive shared threat models — this creates drift.

### 2.3 The AI Guidance section

Every document ends with explicit, topic-specific instructions for how an LLM should use it: what to emphasise, what to delegate elsewhere, what never to assert without verification (version-specific ports, defaults, PSIRT advisories). Write this section last, after you know what the document actually says — it should read as a summary an assistant can act on, not a restatement of the whole document.

### 2.4 Verification notes

Any claim about a specific version, port, default setting, firmware capability, end-of-life date, or vendor advisory history must be flagged for verification against current vendor sources — either inline or in a `> **Verification note.**` callout near the top. This corpus does not assert version-specific facts as permanent truth; products change, this text does not update itself.

---

## 3 — Linking

* **Always use relative paths**, never bare filenames: `[IEC62443.md](../02-Standards/IEC62443.md)`, not `[IEC62443.md](IEC62443.md)`. Compute the path relative to the *linking* document's own location in the tree.
* **File basenames must stay corpus-unique.** This is what makes the flat cross-referencing between distant layers reliable — never create two files with the same name in different folders.
* **Link both directions where it matters.** If document A gains a meaningful relationship to document B (a new platform references its management plane, a new legacy controller references its modern successor), add the link in both `Related Documents` lists, not just one.
* **Directory names with spaces** (e.g. `08-Technologies/Schneider Electric/...`) must be percent-encoded in links (`Schneider%20Electric`) — check this explicitly; it's the most common mechanical error when adding cross-links by hand.
* Before finishing, **grep the corpus for the new document's basename** to find every place that should now link to it instead of describing the topic inline.

---

## 4 — Versioning

Semantic versioning, `MAJOR.MINOR.PATCH`, starting at `1.0.0`:

* **Patch bump** (`1.0.0 → 1.0.1`) — mechanical/editorial changes: fixing a broken link, migrating front-matter schema, adding a cross-link, correcting a typo, updating `last_reviewed`. No change to the substantive guidance.
* **Minor bump** (`1.0.0 → 1.1.0`) — substantive content changes: new sections, expanded threat model, restructured guidance, a scope change (e.g. delegating a section to a new document), significant rewrite.
* **Major bump** — reserved for a fundamental reconception of the document (rare; discuss before doing this).

Every version bump gets a new row in **Revision History** describing what changed and why — write it for the next person auditing the corpus, not as a commit-message afterthought.

---

## 5 — Planned documents

A **Planned** entry is a document the corpus already references (by relative link) but that doesn't exist yet as a file. This lets other documents link to it correctly in advance, and lets you see exactly what's owed to the corpus.

**Adding a Planned entry** (when you write a document that needs to reference something not yet written):

1. Link to it normally, at its correct future path in the tree.
2. Add it to `index.yaml` under `planned:` with `id`, reserved `path`, a one-line `summary` of what it will cover, and `referenced_by` listing every document that links to it.
3. It will appear in the **Planned documents** table in `INDEX.md`.

**Resolving a Planned entry** (when you write the document):

1. Create the file at exactly the reserved path — every existing link to it will now resolve with no further edits needed.
2. Remove its entry from `planned:` in `index.yaml`.
3. Add it to `documents:` in `index.yaml` and to the appropriate table in `INDEX.md` (or regenerate both — § 6).
4. Check whether any document that links to it should now say more, now that real content exists on the other end.

---

## 6 — Keeping INDEX.md and index.yaml consistent

These two files are the corpus's manifest and must stay in sync with the actual tree after every change:

* Every document in the tree appears in `index.yaml` under `documents:` with its current `id`, `path`, `category`, `version`, `language`, `summary` and `keywords` (plus `vendor`/`product` for 08-Technologies).
* Every dangling-but-intentional reference appears under `planned:` with its reserved path and `referenced_by` list.
* `INDEX.md` is a human-readable rendering of the same data, grouped by layer, plus the Planned table and the Conventions section.
* The header statistics (`N documents · M planned`) must match the actual counts.

If you maintain this corpus with scripting/automation, regenerate both files programmatically from the front matter of every document rather than hand-editing them — hand-edited manifests drift from reality within a few changes. If editing by hand, treat the two files as a single atomic change together with whatever document prompted the update.

---

## 7 — Editing an existing document

1. Read the whole document first — check whether your change duplicates something already covered elsewhere in it, or contradicts the delegation notes in its `Scope` section.
2. Make the edit; keep the section structure intact unless the restructuring itself is the point of the change.
3. Update `summary`/`keywords` in the front matter if the change affects what the document is *about*, not just its detail.
4. Bump the version per § 4 and add a Revision History row.
5. If the edit changes what the document delegates or owns, check every document that links to it for consistency.
6. Update `index.yaml`/`INDEX.md` (§ 6) — at minimum the version and summary for this document.

---

## 8 — Style notes

* **Prose over bullet-listed apologetics.** Say what the right architecture is; don't hedge every sentence.
* **Capabilities before products** outside `08-Technologies` — describe what a control must do, not which vendor sells it, except as an illustrative, clearly-labelled example.
* **Consequence-first framing for legacy/insecure platforms.** Don't soften the fact that a device has no native security; state it plainly in a "capability reality" comparison, then spend the rest of the document on what actually compensates.
* **Historical/precedent grounding is welcome when factual and relevant** (e.g. citing a well-documented public incident to justify a design rule) — cite it matter-of-factly, from established public reporting, never speculatively.
* **Never invent version-specific facts.** If you don't know the exact default port, access-level name, or firmware capability for the version in question, say so and point to vendor documentation rather than guessing plausibly.
* **Write the `AI Guidance` section as instructions, not summary** — it should tell an assistant what to *do* with the document, not repeat what the document *says*.

---

## 9 — Quick checklist before calling a change complete

- [ ] Checked for an existing owner of this topic before writing new content
- [ ] Used the template / matched the depth of the correct peer-document pattern (modern-hardening / legacy-compensating / data-plane)
- [ ] Front matter complete, `category` matches `layer`, `summary` and `keywords` are retrieval-useful and bilingual where relevant
- [ ] All links are relative paths, correctly percent-encoded, to corpus-unique basenames
- [ ] Version bumped per § 4, Revision History row added
- [ ] Version-specific claims flagged for vendor verification, not asserted as permanent fact
- [ ] `AI Guidance` section written as actionable instructions
- [ ] Cross-links added in both directions where a new relationship was created
- [ ] `index.yaml` and `INDEX.md` updated (including `planned:` if applicable)
- [ ] Corpus-wide check for other documents that should now link here instead of repeating the topic

---

## 10 — Licensing of contributions

This corpus is licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) — see [LICENSE](LICENSE). By contributing, you agree that your contribution is licensed under the same terms, and that it may be attributed to you (or kept anonymous, at your request) in the way the rest of the corpus attributes its authorship. Do not contribute content you don't have the right to license this way — in particular, do not paste substantial verbatim text from vendor manuals, standards bodies, or other copyrighted sources; summarise and cite per the existing `Sources / Grounding` convention instead.
