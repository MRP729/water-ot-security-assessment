# Portfolio State

**Generated on:** 2026-08-30
**Scope:** skeleton only. The `Status` column is deliberately left as `TBD` for every row —
completion state was **not** assessed or inferred. Fill it in manually.

---

## Artifacts

| Filename | H1 Title | Word count | Last commit | Status |
|---|---|---:|---|---|
| `AWWA-Assessment-Shenandoah-Valley-Water-Authority.md` | AWWA Cybersecurity Self-Assessment | 1409 | 2026-08-27 | TBD |
| `Artifact-3-Segmentation-Assessment.md` | OT/ICS Network Segmentation Assessment | 3463 | 2026-08-27 | TBD |
| `Artifact-4-Asset-Inventory-Criticality.md` | OT Asset Inventory & Criticality Analysis | 1554 | 2026-08-27 | TBD |
| `Artifact-5-ATTCK-Oldsmar-Case-Study.md` | MITRE ATT&CK for ICS Incident Mapping | 1499 | 2026-08-27 | TBD |
| `Crosswalk-NIST80053-IEC62443-Shenandoah-Valley.md` | NIST 800-53 / IEC 62443 Crosswalk | 880 | 2026-08-27 | TBD |
| `EPA-Checklist-Shenandoah-Valley-Water-Authority.md` | EPA Cybersecurity Checklist Assessment | 1485 | 2026-08-27 | TBD |
| `ERP-Shenandoah-Valley-Water-Authority.md` | Emergency Response Plan — OT/ICS Environment | 1223 | 2026-08-26 | TBD |
| `RRA-Shenandoah-Valley-Water-Authority.md` | Risk & Resilience Assessment | 1825 | 2026-08-27 | TBD |

Word counts are `wc -w` over the raw markdown, so they include markdown syntax, table
pipes, and link targets — treat them as relative magnitude, not prose length.

Last-commit dates are `git log -1 --format=%ad --date=short -- <file>`.

Note: filenames use two different conventions — five are `<Type>-Shenandoah-Valley-...`
and three are `Artifact-<n>-...`. `Artifact-3-Segmentation-Assessment.md` is referenced by
the other documents as "Artifact 3"; the `Shenandoah`-prefixed files are referenced by
type name (e.g. "the RRA", "the crosswalk"). No file is named `Artifact-1` or `Artifact-2`.
**UNVERIFIED — confirm whether Artifacts 1 and 2 exist elsewhere or were never produced.**

---

## `git log --oneline -20`

```
7db1082 Add Artifact 4 (Asset Inventory & Criticality) and Artifact 5 (MITRE ATT&CK Oldsmar case study)
7335f30 Update Finding 1 status to remediated across portfolio (OpenPLC default credential fix, Aug 27 2026)
0b0824f Add NIST 800-53 / IEC 62443 crosswalk to portfolio
a284456 Add EPA cybersecurity checklist assessment to portfolio
29620b8 Add AWWA cybersecurity self-assessment to portfolio
f126d24 Add Emergency Response Plan to portfolio
fc5ab48 Add Artifact 3 and RRA to portfolio
e37dae0 Merge training certificates and operations-linkage notes from portfolio repo
166bca4 Update build journal with network segmentation phase
7bb4d58 Update README documentation structure
3c67296 Add OT network segmentation case study
b11ed46 Initial commit: OT/ICS homelab foundation with Proxmox networking and troubleshooting documentation
```

The repository contains 12 commits in total; `-20` returns all of them.

---

## `git status --short`

Captured at generation time, **before** the files produced by this task were staged:

```
?? docs/project-knowledge/
?? tools/
```

After generation, `tools/gen-lab-topology.sh` and the three files in
`docs/project-knowledge/` were staged but **not committed**, per instruction. See the
"Repository state" section of the task report for the post-staging status.

---

## `git remote -v`

```
origin	https://github.com/MRP729/water-ot-security-assessment.git (fetch)
origin	https://github.com/MRP729/water-ot-security-assessment.git (push)
```

---

## Not assessed

The following were explicitly out of scope for this skeleton and are **not** represented above:

- Completion or quality status of any artifact (the `Status` column is `TBD` by design)
- Any file outside `docs/artifacts/` — including `README.md`, `LICENSE`, `configs/`, and
  the remainder of `docs/`
- Evidence directories, `.nmap` / `.xml` / `.gnmap` / `.pcap` files, and Suricata rule files
