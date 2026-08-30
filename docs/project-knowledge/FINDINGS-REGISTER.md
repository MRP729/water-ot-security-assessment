# Findings Register

**Generated on:** 2026-08-30 (updated — see revision note below)
**Scope of scan:** `docs/artifacts/*.md` only (10 files as of this update). Evidence
directories, `.nmap` / `.xml` / `.gnmap` / `.pcap` files, and Suricata rule files were
**not** read.
**Method:** identifiers are reported as the raw string used in each document. They have
not been normalised, renumbered, or reconciled.

> **Revision note:** this file was first generated 2026-08-30 covering 5 findings across
> 8 artifacts. It is now updated to add `Finding 6.1`–`6.4` from the new Artifact 6
> deliverables, and to close out inconsistency #1 below, which is now resolved at the
> source rather than merely observed.

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

### 7. Artifact 6 introduces two additional, unreconciled identifier schemes — **NEW**

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

---

## Scan coverage note

All 10 files in `docs/artifacts/` were scanned as of this update:

```
AWWA-Assessment-Shenandoah-Valley-Water-Authority.md
Artifact-3-Segmentation-Assessment.md
Artifact-4-Asset-Inventory-Criticality.md
Artifact-5-ATTCK-Oldsmar-Case-Study.md
Artifact-6-Mirroring-Investigation-Handoff.md
Artifact-6-Threat-Detection-Assessment.md
Crosswalk-NIST80053-IEC62443-Shenandoah-Valley.md
EPA-Checklist-Shenandoah-Valley-Water-Authority.md
ERP-Shenandoah-Valley-Water-Authority.md
RRA-Shenandoah-Valley-Water-Authority.md
```

No canonical findings register exists in `docs/artifacts/`; `Artifact-3-Segmentation-Assessment.md`
remains the de facto source of definitions for Findings 1–4 (it is the only file that defines all
four with titles and severities, and is cited as such by the RRA — `Finding (ref. Artifact 3)`,
RRA:46). `Artifact-6-Threat-Detection-Assessment.md` is now the de facto source of definitions for
Findings 6.1–6.4, by the same logic — it is the only file that defines them.
