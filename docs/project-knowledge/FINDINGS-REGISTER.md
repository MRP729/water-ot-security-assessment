# Findings Register

**Generated on:** 2026-08-30 (updated three times — see revision notes below)
**Scope of scan:** `docs/artifacts/*.md` only (12 files as of this update). Evidence
directories, `.nmap` / `.xml` / `.gnmap` / `.pcap` files, and Suricata rule files were
**not** read.
**Method:** identifiers are reported as the raw string used in each document. They have
not been normalised, renumbered, or reconciled.

> **Revision note (1st update):** this file was first generated 2026-08-30 covering 5 findings across
> 8 artifacts. It was then updated to add `Finding 6.1`–`6.4` from the new Artifact 6
> deliverables, and to close out inconsistency #1 below, which was resolved at the
> source rather than merely observed.
>
> **Revision note (2nd update):** `Artifact-8-Client-Deliverable-Report.md` was added.
> It did **not** introduce new findings to track — see inconsistency #7, which recorded its
> lettered `Finding A`–`H` as a fourth identifier scheme rather than as new register rows,
> and flagged three of those eight (E, G, H) as content with no numbered equivalent anywhere
> in the portfolio.
>
> **Revision note (3rd update):** `Artifact-7-Water-Sector-Cyber-Risk-Package.md` was added.
> It formally introduces `Finding 7.1`–`7.3`, now added as proper register rows — and, per
> its own Section 9, these are exactly the findings that E, G, and H in Artifact 8 were
> standing in for. That closes the UNVERIFIED item from the 2nd update. Artifact 7 also
> explicitly documents the artifact-scoped numbering convention this register had been
> describing as an inconsistency (#7) — see that entry, now marked resolved-by-convention
> rather than deleted.

---

## Register

| Finding ID | Title | Status as written | Referenced in (file:line) |
|---|---|---|---|
| `Finding 1` | Default credentials on the PLC web management interface | **Remediated (Aug 27, 2026)** — consistent across every reference, no remaining contradiction (see inconsistency #1) | `Artifact-3-Segmentation-Assessment.md`:21, 60, 90, 203<br>`AWWA-Assessment-Shenandoah-Valley-Water-Authority.md`:34, 63, 112<br>`Crosswalk-NIST80053-IEC62443-Shenandoah-Valley.md`:16, 33<br>`EPA-Checklist-Shenandoah-Valley-Water-Authority.md`:29, 94<br>`ERP-Shenandoah-Valley-Water-Authority.md`:58, 76<br>`RRA-Shenandoah-Valley-Water-Authority.md`:7*, 48, 50*, 79, 89, 90, 99 |
| `Finding 2` | Historian database access controlled at the application layer only | `(High)` (Artifact-3:99) · `High` (RRA:49) | `Artifact-3-Segmentation-Assessment.md`:99<br>`RRA-Shenandoah-Valley-Water-Authority.md`:7*, 49, 50* |
| `Finding 3` | Configuration-vs-enforcement gap on PLC and historian firewalls | `(Medium, methodological)` (Artifact-3:102) · `**Medium** (methodological)` (RRA:50) | `Artifact-3-Segmentation-Assessment.md`:102, 187<br>`AWWA-Assessment-Shenandoah-Valley-Water-Authority.md`:34, 46, 50<br>`Crosswalk-NIST80053-IEC62443-Shenandoah-Valley.md`:19<br>`EPA-Checklist-Shenandoah-Valley-Water-Authority.md`:43<br>`RRA-Shenandoah-Valley-Water-Authority.md`:7*, 50, 102, 104<br>`Artifact-6-Threat-Detection-Assessment.md`:63 (cited by name, not status-bearing) |
| `Finding 4` | HMI web interface unencrypted | `(Medium)` (Artifact-3:105) · `**Medium**` (RRA:51) · `remains open` (RRA:91) | `Artifact-3-Segmentation-Assessment.md`:105<br>`Crosswalk-NIST80053-IEC62443-Shenandoah-Valley.md`:20<br>`EPA-Checklist-Shenandoah-Valley-Water-Authority.md`:39<br>`RRA-Shenandoah-Valley-Water-Authority.md`:7*, 51, 91, 101 |
| `(New)` | Unresolved PLC-to-historian egress anomaly (Artifact 3, Appendix A) | `**Low / Informational**` (RRA:52) · `a legitimate, thoroughly-evidenced open finding` (Artifact-3:236) | `RRA-Shenandoah-Valley-Water-Authority.md`:52, 103<br>`Artifact-3-Segmentation-Assessment.md`:21, 191, 236 |
| `Finding 6.1` | Stock `suricata.yaml` referenced a nonexistent capture interface (`eth0` vs. actual `ens18`), causing a 14,000+ restart crash loop while reporting `active` | not stated in `Finding N`/`Open`/`Remediated` vocabulary — narrative status is **Remediated** (interface corrected, zero restarts confirmed over a multi-day period) | `Artifact-6-Threat-Detection-Assessment.md`:51, 63 |
| `Finding 6.2` | Custom detection rule silently not loaded due to a `default-rule-path` resolution mismatch (`/etc/suricata/rules/` vs. actual `/var/lib/suricata/rules`) | narrative status **Remediated** (rule relocated, loaded-rule count confirmed incremented) | `Artifact-6-Threat-Detection-Assessment.md`:59, 63 |
| `Finding 6.3` | Sensor placement behind a default-deny firewall structurally limits visibility to permitted traffic only | **High significance**, stated `❌ Not achieved` in the posture table — open, not a remediation-track item | `Artifact-6-Threat-Detection-Assessment.md`:96, 110, 114, 116, 149, 174, 184, 186, 216 |
| `Finding 6.4` | Stock ET Open ruleset has no rate-based SSH credential-guessing detection | **High significance**, stated `❌ Not achieved` — open, not a remediation-track item | `Artifact-6-Threat-Detection-Assessment.md`:118, 130, 132, 150, 184 |
| `Finding 7.1` | No multi-factor authentication anywhere in the OT environment | `*(Open, High priority)*` | `Artifact-7-Water-Sector-Cyber-Risk-Package.md`:65, 166 |
| `Finding 7.2` | No backup or tested recovery capability for OT systems | `*(Open, High priority)*` | `Artifact-7-Water-Sector-Cyber-Risk-Package.md`:75, 166 |
| `Finding 7.3` | No named accountability or change control for OT cybersecurity | `*(Open, High priority)*` | `Artifact-7-Water-Sector-Cyber-Risk-Package.md`:85, 91, 166 |

\* Reference occurs in a **plural or range form** (`Findings 1–4`, `Findings 1 and 2`) rather than
as a discrete identifier — see inconsistency #2 below.

---

## Identifier inconsistencies

### 1. `Finding 1`'s contradictory status — **RESOLVED, 2026-08-30**

Previously the most consequential inconsistency in the set: `RRA-Shenandoah-Valley-Water-Authority.md`
line 79 read *"Not yet remediated — Finding 1 open, tracked in Section 6 roadmap"*, directly
contradicting lines 48, 90, and 99 of the same document, all of which stated the finding was
remediated Aug 27, 2026.

**Corrected 2026-08-30.** Line 79 now reads:

> `**✅ Remediated (Aug 27, 2026)** — default account replaced and verified; see Section 6 and EPA Checklist item 2.A`

Verified by direct re-scan of the file: no instance of "not yet remediated," "Finding 1 open," or
similar contradictory language remains anywhere in the RRA. All references to `Finding 1`'s status
across the entire portfolio are now consistent. This entry is retained (rather than deleted) as a
record that the inconsistency existed and was corrected, not to imply it is still open.

### 2. Three different reference formats for the same findings

| Format | Example | Location |
|---|---|---|
| Discrete singular | `Finding 1` | 20 occurrences across 6 files |
| Range | `Artifact 3 (Findings 1–4)` | RRA:7 |
| Plural pair | `root enabler of Findings 1 and 2's network reachability` | RRA:50 |

The range and plural forms are substantive references — RRA:7 is the document's entire statement
of source evidence — but neither is machine-findable by a search for the discrete identifier.
A tool grepping `Finding 1` will silently miss both. Unaffected by this update.

### 3. A finding tracked under a placeholder identifier, and under no identifier at all

The PLC-to-historian egress anomaly is referenced **five times under four different labels**,
none of them a stable ID:

| Label used | Location |
|---|---|
| `(New)` — used as the literal ID cell in the vulnerability-mapping table | RRA:52 |
| `Appendix A anomaly` | RRA:103 |
| `one unresolved technical anomaly` | Artifact-3:21 |
| `The PLC-to-historian egress anomaly (Appendix A)` | Artifact-3:191 |
| `a legitimate, thoroughly-evidenced open finding` | Artifact-3:236 |

It is scored and carries a severity (`Low / Informational`, RRA:52) and a roadmap priority
(`5 — Medium-term`, RRA:103), so it is being managed as a finding — but it cannot be
cross-referenced, and `(New)` will not remain meaningful as the portfolio ages. Unaffected by
this update.

### 4. Two artifacts contain no finding references whatsoever — **re-confirmed 2026-08-30**

| File | Finding references |
|---|---|
| `Artifact-4-Asset-Inventory-Criticality.md` | none |
| `Artifact-5-ATTCK-Oldsmar-Case-Study.md` | none |

Re-scanned directly for this update (both files unchanged since first generation). Confirmed
still zero — not a stale result. Whether this is expected — an asset inventory and an external
case study may legitimately not cite assessment findings — or a missing linkage remains a
judgement call.
**UNVERIFIED — confirm whether these two artifacts are intended to reference the findings register.**

### 5. `Finding 2` is defined but barely carried forward

`Finding 2` (High severity) appears only in its Artifact 3 definition and the RRA vulnerability
table. Unlike Findings 1, 3, and 4, it is **not** referenced in the AWWA assessment, the EPA
checklist, or the NIST/IEC crosswalk. Findings 1, 3, and 4 each appear in at least two of those
three compliance documents. Unaffected by this update.
**UNVERIFIED — confirm whether Finding 2 was intentionally excluded from compliance mapping or
was omitted.**

### 6. Severity strings are not formatted consistently

The same severity is written as a parenthetical in the Artifact 3 heading (`(High)`,
`(Medium, methodological)`) and as bold markdown in the RRA table (`**High**`,
`**Medium** (methodological)`). Semantically equivalent, but no single canonical form exists,
so exact-string matching across documents will not work. Unaffected by this update.

### 7. Multiple finding identifier schemes across the portfolio — **RESOLVED BY DOCUMENTED CONVENTION, 2026-08-30**

**This entry was originally flagged as an inconsistency (below, unchanged from when it was
written). It is retained as a historical record, not because a problem still exists.**
`Artifact-7-Water-Sector-Cyber-Risk-Package.md` Section 9 ("Note on Finding Identifier
Conventions") now explicitly documents what this register had been describing as
unreconciled:

> "This portfolio uses artifact-scoped finding identifiers: a finding is numbered by the
> artifact that identified it. Artifact 6 identified Findings 6.1–6.4; this artifact
> identifies Findings 7.1–7.3. Findings 1 through 4 predate this convention and retain bare
> integer identifiers from Artifact 3. They are not renumbered, because they are
> cross-referenced by identifier across eight committed documents and renumbering would
> break those references for no analytical gain. The mirroring investigation handoff uses
> a separate `Fault N` vocabulary deliberately... using distinct terminology prevents them
> from being read as security findings against the assessed environment."
> — Artifact-7-Water-Sector-Cyber-Risk-Package.md:158–164

This resolves the situation the same way a documented design decision resolves an apparent
bug: not by making the multiple schemes disappear, but by establishing that they are
intentional and explaining why. `Finding N` (bare integer, Artifact 3's four), `Finding N.N`
(artifact-scoped, Artifact 6's four and Artifact 7's three), lettered `Finding A`–`H`
(Artifact 8, an audience-specific relabeling, not a fifth investigation), and `Fault N`
(the mirroring handoff, deliberately not called a finding at all) are now four **named,
explained** conventions rather than four unexplained ones. Nothing below needed to change
as a result — the original observations were accurate; only their interpretation as an
"inconsistency" no longer holds.

**Original entry, as written before this resolution:**

Findings 1–4 use bare `Finding N`. Artifact 6 does not continue that series — there is no
`Finding 5` anywhere in the portfolio — and instead introduces:

- **A dotted `Finding <artifact>.<sequence>` scheme** in `Artifact-6-Threat-Detection-Assessment.md`
  (`Finding 6.1` through `Finding 6.4`). This document does cite Findings 1 and 3 by name in
  prose (lines 43, 63, 136), but never as a status-bearing `Finding N` line — it references
  them narratively ("the credential-weakness class documented as Finding 1 in Artifact 3"),
  which incidentally satisfies inconsistency-avoidance rule from the methodology doc (status
  claims should name a discrete finding) but means the two numbering schemes never collide
  or get cross-linked.
- **A wholly separate `Fault N` scheme** (`Fault 1` through `Fault 5`) in
  `Artifact-6-Mirroring-Investigation-Handoff.md`, deliberately *not* called "Finding" at all.
  This appears to be an intentional distinction — a troubleshooting/investigation log using
  different terminology than a formal assessment finding — but it means the portfolio now has
  **three concurrent identifier vocabularies** (`Finding N`, `Finding N.N`, `Fault N`) with no
  stated rule for when to use which.

Also worth noting: unlike Findings 1–4, none of `Finding 6.1`–`6.4` are formally scored with a
severity/status pair from the fixed vocabulary the methodology doc specifies
(`Open | Remediated (date) | Accepted risk | Out of scope`). 6.1 and 6.2 are described in prose
as remediated; 6.3 and 6.4 use `High significance` and a posture-table `❌ Not achieved`, which is
a fourth status vocabulary distinct from all of the above.
**UNVERIFIED — confirm whether this is intentional (Artifact 6 findings are deployment/coverage
findings, a different category from Artifact 3's vulnerability findings) or should be reconciled
to the single fixed vocabulary the methodology doc specifies.**

**Note, 3rd update:** `Finding 7.1`–`7.3` add a fifth variant — `*(Open, High priority)*` — which
overlaps partially with the fixed vocabulary (`Open` matches) but adds a priority qualifier the
methodology doc's vocabulary doesn't define. The UNVERIFIED question above now applies across
Findings 6.1–7.3 collectively, not just 6.1–6.4.

**Extension (2nd update): a fourth scheme, lettered `Finding A`–`H`, in
`Artifact-8-Client-Deliverable-Report.md` Section 4 ("Consolidated Findings").** This is the
executive-audience deliverable and explicitly frames itself as ordering findings "by operational
consequence, not by technical severity score" (line 72) — a deliberate re-presentation for a
non-technical reader, not a new investigation.

**These are not recorded as new register rows, but the mapping is not as clean as a pure
relabeling would suggest — verified by reading each lettered finding against the existing
register, not assumed:**

| Letter | Title | Maps to |
|---|---|---|
| Finding A | Default vendor credentials on the process controller | `Finding 1` (Artifact 3) |
| Finding B | Flat network permitting direct Engineering-to-controller access | `Finding 3` region (the segmentation-enforcement gap Finding 3 documents), also draws on Finding 1's exposure numbers |
| Finding C | Historian database directly exposed | `Finding 2` (Artifact 3) |
| Finding D | No encryption on the operator interface | `Finding 4` (Artifact 3) |
| Finding E | No multi-factor authentication | *(at the time)* no prior register entry. **Resolved, 3rd update: maps to `Finding 7.1`**, formalized in `Artifact-7-Water-Sector-Cyber-Risk-Package.md`:65, confirmed explicitly at :166 |
| Finding F | Intrusion detection coverage gaps | `Finding 6.3` and `Finding 6.4` (Artifact 6), merged into one |
| Finding G | No tested backup or recovery capability | *(at the time)* no prior register entry. **Resolved, 3rd update: maps to `Finding 7.2`**, formalized in `Artifact-7-Water-Sector-Cyber-Risk-Package.md`:75, confirmed explicitly at :166 |
| Finding H | Governance and accountability gaps | *(at the time)* no prior register entry. **Resolved, 3rd update: maps to `Finding 7.3`**, formalized in `Artifact-7-Water-Sector-Cyber-Risk-Package.md`:85, confirmed explicitly at :166 |

So, as of the 2nd update: **A, C, D are 1:1 relabelings; B and F are many-to-one consolidations; E, G,
and H introduced content not yet formalized as a discrete tracked finding under any of the other
schemes.** As of this 3rd update, that last category is closed — Artifact 7 formalized exactly those
three gaps as `Finding 7.1`–`7.3`, and its own Section 9 (quoted above) states the correspondence
explicitly rather than leaving it to inference: *"Findings E, G, and H correspond to Findings 7.1,
7.2, and 7.3 as formalized in this document."* The original UNVERIFIED question — whether E/G/H would
get retroactive numbered entries — is answered: yes, and the sequencing (Artifact 8's executive letter
first, Artifact 7's technical number second) is now visible in the commit history rather than
ambiguous. All eight of Artifact 8's lettered findings now trace to a numbered original: A, C, D, E,
F, G, H to Findings 1, 2, 4, 7.1, 6.3+6.4, 7.2, 7.3 respectively, and B to a synthesis of 1 and 3.

---

## Scan coverage note

All 12 files in `docs/artifacts/` were scanned as of this update:

```
AWWA-Assessment-Shenandoah-Valley-Water-Authority.md
Artifact-3-Segmentation-Assessment.md
Artifact-4-Asset-Inventory-Criticality.md
Artifact-5-ATTCK-Oldsmar-Case-Study.md
Artifact-6-Mirroring-Investigation-Handoff.md
Artifact-6-Threat-Detection-Assessment.md
Artifact-7-Water-Sector-Cyber-Risk-Package.md
Artifact-8-Client-Deliverable-Report.md
Crosswalk-NIST80053-IEC62443-Shenandoah-Valley.md
EPA-Checklist-Shenandoah-Valley-Water-Authority.md
ERP-Shenandoah-Valley-Water-Authority.md
RRA-Shenandoah-Valley-Water-Authority.md
```

There are now three definitional sources in `docs/artifacts/`, each the sole origin for its
numbered range: `Artifact-3-Segmentation-Assessment.md` for Findings 1–4 (cited as such by the
RRA — `Finding (ref. Artifact 3)`, RRA:46), `Artifact-6-Threat-Detection-Assessment.md` for
Findings 6.1–6.4, and `Artifact-7-Water-Sector-Cyber-Risk-Package.md` for Findings 7.1–7.3 (which
also explicitly states this register's identifier-scheme convention — see inconsistency #7).
`Artifact-8-Client-Deliverable-Report.md` defines no new findings of its own — it is a relabeling
of the other three sources for a non-technical audience — and is not a definitional source by
this register's convention, despite containing a "Consolidated Findings" section.
