# Findings Register

**Generated on:** 2026-08-30
**Scope of scan:** `docs/artifacts/*.md` only (8 files). Evidence directories,
`.nmap` / `.xml` / `.gnmap` / `.pcap` files, and Suricata rule files were **not** read.
**Method:** identifiers are reported as the raw string used in each document. They have
not been normalised, renumbered, or reconciled.

---

## Register

| Finding ID | Title | Status as written | Referenced in (file:line) |
|---|---|---|---|
| `Finding 1` | Default credentials on the PLC web management interface | **Conflicting across docs.** `REMEDIATED Aug 27, 2026` (Artifact-3:90) · `High → Remediated (Aug 27, 2026)` (RRA:48) · `Not yet remediated` … `Finding 1 open` (RRA:79) · `closing Finding 1 … as a near-term action item` (Artifact-3:203) | `Artifact-3-Segmentation-Assessment.md`:21, 60, 90, 203<br>`AWWA-Assessment-Shenandoah-Valley-Water-Authority.md`:34, 63, 112<br>`Crosswalk-NIST80053-IEC62443-Shenandoah-Valley.md`:16, 33<br>`EPA-Checklist-Shenandoah-Valley-Water-Authority.md`:29, 94<br>`ERP-Shenandoah-Valley-Water-Authority.md`:58, 76<br>`RRA-Shenandoah-Valley-Water-Authority.md`:7*, 48, 50*, 79, 89, 90, 99 |
| `Finding 2` | Historian database access controlled at the application layer only | `(High)` (Artifact-3:99) · `High` (RRA:49) | `Artifact-3-Segmentation-Assessment.md`:99<br>`RRA-Shenandoah-Valley-Water-Authority.md`:7*, 49, 50* |
| `Finding 3` | Configuration-vs-enforcement gap on PLC and historian firewalls | `(Medium, methodological)` (Artifact-3:102) · `**Medium** (methodological)` (RRA:50) | `Artifact-3-Segmentation-Assessment.md`:102, 187<br>`AWWA-Assessment-Shenandoah-Valley-Water-Authority.md`:34, 46, 50<br>`Crosswalk-NIST80053-IEC62443-Shenandoah-Valley.md`:19<br>`EPA-Checklist-Shenandoah-Valley-Water-Authority.md`:43<br>`RRA-Shenandoah-Valley-Water-Authority.md`:7*, 50, 102, 104 |
| `Finding 4` | HMI web interface unencrypted | `(Medium)` (Artifact-3:105) · `**Medium**` (RRA:51) · `remains open` (RRA:91) | `Artifact-3-Segmentation-Assessment.md`:105<br>`Crosswalk-NIST80053-IEC62443-Shenandoah-Valley.md`:20<br>`EPA-Checklist-Shenandoah-Valley-Water-Authority.md`:39<br>`RRA-Shenandoah-Valley-Water-Authority.md`:7*, 51, 91, 101 |
| `(New)` | Unresolved PLC-to-historian egress anomaly (Artifact 3, Appendix A) | `**Low / Informational**` (RRA:52) · `a legitimate, thoroughly-evidenced open finding` (Artifact-3:236) | `RRA-Shenandoah-Valley-Water-Authority.md`:52, 103<br>`Artifact-3-Segmentation-Assessment.md`:21, 191, 236 |

\* Reference occurs in a **plural or range form** (`Findings 1–4`, `Findings 1 and 2`) rather than
as a discrete identifier — see inconsistency #2 below.

---

## Identifier inconsistencies

### 1. `Finding 1` carries three mutually exclusive statuses, including within a single document

This is the most consequential inconsistency in the set. `RRA-Shenandoah-Valley-Water-Authority.md`
states both positions internally:

- **RRA:48** — `**High → Remediated (Aug 27, 2026)**`
- **RRA:79** — `**Not yet remediated** — Finding 1 open, tracked in Section 6 roadmap`
- **RRA:90** — `Finding 1 (default OpenPLC credentials) has been remediated`
- **RRA:99** — `~~Change OpenPLC default credentials~~ **✅ Complete (Aug 27, 2026)**`

`Artifact-3-Segmentation-Assessment.md` also contains both framings:

- **Artifact-3:90** — `**Finding 1 — … (High) — REMEDIATED Aug 27, 2026.**`
- **Artifact-3:203** — `…closing **Finding 1** (default OpenPLC credentials) as a **near-term action item**…`

RRA:79 and Artifact-3:203 appear to be pre-remediation text that was not updated when commit
`7335f30` ("Update Finding 1 status to remediated across portfolio") swept the rest of the
portfolio. **UNVERIFIED — confirm which is correct against live PLC state; this register
records only what the documents say, and the documents disagree.**

### 2. Three different reference formats for the same findings

| Format | Example | Location |
|---|---|---|
| Discrete singular | `Finding 1` | 20 occurrences across 6 files |
| Range | `Artifact 3 (Findings 1–4)` | RRA:7 |
| Plural pair | `root enabler of Findings 1 and 2's network reachability` | RRA:50 |

The range and plural forms are substantive references — RRA:7 is the document's entire statement
of source evidence — but neither is machine-findable by a search for the discrete identifier.
A tool grepping `Finding 1` will silently miss both.

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
cross-referenced, and `(New)` will not remain meaningful as the portfolio ages.

### 4. Two artifacts contain no finding references whatsoever

| File | Finding references |
|---|---|
| `Artifact-4-Asset-Inventory-Criticality.md` | none |
| `Artifact-5-ATTCK-Oldsmar-Case-Study.md` | none |

Both were added in the most recent commit (`7db1082`). Whether this is expected — an asset
inventory and an external case study may legitimately not cite assessment findings — or a
missing linkage is a judgement call.
**UNVERIFIED — confirm whether these two artifacts are intended to reference the findings register.**

### 5. `Finding 2` is defined but barely carried forward

`Finding 2` (High severity) appears only in its Artifact 3 definition and the RRA vulnerability
table. Unlike Findings 1, 3, and 4, it is **not** referenced in the AWWA assessment, the EPA
checklist, or the NIST/IEC crosswalk. Findings 1, 3, and 4 each appear in at least two of those
three compliance documents.
**UNVERIFIED — confirm whether Finding 2 was intentionally excluded from compliance mapping or
was omitted.**

### 6. Severity strings are not formatted consistently

The same severity is written as a parenthetical in the Artifact 3 heading (`(High)`,
`(Medium, methodological)`) and as bold markdown in the RRA table (`**High**`,
`**Medium** (methodological)`). Semantically equivalent, but no single canonical form exists,
so exact-string matching across documents will not work.

---

## Scan coverage note

All 8 files in `docs/artifacts/` were scanned:

```
AWWA-Assessment-Shenandoah-Valley-Water-Authority.md
Artifact-3-Segmentation-Assessment.md
Artifact-4-Asset-Inventory-Criticality.md
Artifact-5-ATTCK-Oldsmar-Case-Study.md
Crosswalk-NIST80053-IEC62443-Shenandoah-Valley.md
EPA-Checklist-Shenandoah-Valley-Water-Authority.md
ERP-Shenandoah-Valley-Water-Authority.md
RRA-Shenandoah-Valley-Water-Authority.md
```

No canonical findings register exists in `docs/artifacts/`; `Artifact-3-Segmentation-Assessment.md`
is the de facto source of definitions (it is the only file that defines all four numbered findings
with titles and severities) and is cited as such by the RRA (`Finding (ref. Artifact 3)`, RRA:46).
