# Portfolio State

**Generated on:** 2026-08-30 (updated three times — see revision notes below)
**Scope:** skeleton only. The `Status` column is deliberately left as `TBD` for every row —
completion state was **not** assessed or inferred. Fill it in manually.

> **Revision note (1st update):** this file was first generated 2026-08-30 covering 8 artifacts. It was
> then updated to cover 10 files following the addition of Artifacts 4, 5, and 6 (two files), and to
> reflect `RRA-Shenandoah-Valley-Water-Authority.md`'s Aug 27 credential-remediation correction (see
> `FINDINGS-REGISTER.md` inconsistency #1).
>
> **Revision note (2nd update):** covered 11 files following the addition of
> `Artifact-8-Client-Deliverable-Report.md`. Flagged that no `Artifact-7` existed anywhere in the
> repository as an open `UNVERIFIED` item.
>
> **Revision note (3rd update):** now covers 12 files following the addition of
> `Artifact-7-Water-Sector-Cyber-Risk-Package.md`. **The Artifact-7 gap flagged in the 2nd update is
> resolved** — the file now exists. Retained below as a dated note (consistent with how the Finding 1
> contradiction in inconsistency #1 was handled) rather than deleted outright, since the gap was real
> at the time it was flagged.

---

## Artifacts

| Filename | H1 Title | Word count | Last commit | Status |
|---|---|---:|---|---|
| `AWWA-Assessment-Shenandoah-Valley-Water-Authority.md` | AWWA Cybersecurity Self-Assessment | 1409 | 2026-08-27 | TBD |
| `Artifact-3-Segmentation-Assessment.md` | OT/ICS Network Segmentation Assessment | 3463 | 2026-08-27 | TBD |
| `Artifact-4-Asset-Inventory-Criticality.md` | OT Asset Inventory & Criticality Analysis | 1554 | 2026-08-27 | TBD |
| `Artifact-5-ATTCK-Oldsmar-Case-Study.md` | MITRE ATT&CK for ICS Incident Mapping | 1499 | 2026-08-27 | TBD |
| `Artifact-6-Mirroring-Investigation-Handoff.md` | Port Mirroring Investigation — Artifact 6 (Suricata Threat Detection) | 2189 | 2026-08-30 | TBD |
| `Artifact-6-Threat-Detection-Assessment.md` | OT Threat Detection Assessment | 3127 | 2026-08-30 | TBD |
| `Artifact-7-Water-Sector-Cyber-Risk-Package.md` | Water Sector Cyber Risk Package | 2291 | 2026-08-30 | TBD |
| `Artifact-8-Client-Deliverable-Report.md` | OT Cybersecurity Assessment | 2708 | 2026-08-30 | TBD |
| `Crosswalk-NIST80053-IEC62443-Shenandoah-Valley.md` | NIST 800-53 / IEC 62443 Crosswalk | 880 | 2026-08-27 | TBD |
| `EPA-Checklist-Shenandoah-Valley-Water-Authority.md` | EPA Cybersecurity Checklist Assessment | 1485 | 2026-08-27 | TBD |
| `ERP-Shenandoah-Valley-Water-Authority.md` | Emergency Response Plan — OT/ICS Environment | 1223 | 2026-08-26 | TBD |
| `RRA-Shenandoah-Valley-Water-Authority.md` | Risk & Resilience Assessment | 1832 | 2026-08-30 | TBD |

Word counts are `wc -w` over the raw markdown, so they include markdown syntax, table
pipes, and link targets — treat them as relative magnitude, not prose length.

Last-commit dates are `git log -1 --format=%ad --date=short -- <file>`. The RRA's date
(2026-08-30) reflects the credential-remediation correction landing in this same update;
its content was otherwise last substantively written 2026-08-27.

Note: filenames use three different naming conventions now — five are
`<Type>-Shenandoah-Valley-...`, six are `Artifact-<n>-...`, and Artifact 6 alone has
**two** files sharing its number (`Artifact-6-Threat-Detection-Assessment.md`, the primary
deliverable, and `Artifact-6-Mirroring-Investigation-Handoff.md`, a linked but separate
investigation record it explicitly defers to in its Section 7). No file is named
`Artifact-1` or `Artifact-2`.
**UNVERIFIED — confirm whether Artifacts 1 and 2 exist elsewhere or were never produced.**

**Resolved, 2026-08-30 (3rd update):** the previous version of this note flagged that no
`Artifact-7` existed and the numbering jumped from 6 to 8. `Artifact-7-Water-Sector-Cyber-Risk-Package.md`
has since been added. Its own Section 8 ("Component Document Index") explicitly identifies
itself as the integration layer connecting the RRA, ERP, AWWA assessment, EPA checklist, and
crosswalk — five files that already existed under the `<Type>-Shenandoah-Valley-...` naming
convention, which explains why the gap was never a missing document so much as a
not-yet-written one: Artifact 7 formalizes relationships among files that predated it.

---

## `git log --oneline -20`

```
3cdf67c Add Artifact 7 (Water Sector Cyber Risk Package) - integration document, formalizes Findings 7.1-7.3, establishes finding identifier convention
a9b29c9 Update portfolio state and findings register for Artifact 8
c08b654 Add Artifact 8 (Client Deliverable Report) - executive synthesis of full assessment
100cc15 Resolve Finding 1 status contradiction in RRA; update portfolio state and findings register for Artifacts 4-6
b7f045c Add Artifact 6 (OT Threat Detection Assessment) - Suricata deployment, detection coverage findings, positive control verification
92b9380 Document port mirroring investigation for Artifact 6 (paused, four faults resolved/diagnosed, evidence path pivoted)
ea238f4 Add project-knowledge docs: lab topology, findings register, portfolio state, methodology; redact home-LAN and physical NIC data from topology generator
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

The repository contains 19 commits in total as of this update (was 17 after the 2nd update,
15 after the 1st, 12 at first generation); `-20` returns all of them. The commit finalizing
this 3rd update is not yet reflected in the list above, since `git log` was run before it
was created.

---

## `git status --short`

Captured at generation time, **before** this (3rd) update's changes were staged, and
**after** `Artifact-7-Water-Sector-Cyber-Risk-Package.md` was already committed and pushed
(`3cdf67c`) in a separate, prior commit:

```
(clean — no output)
```

`PORTFOLIO-STATE.md` and `FINDINGS-REGISTER.md` were staged after this snapshot, as a
second, separate commit from the Artifact 7 addition itself — consistent with how the
Artifact 8 index update was sequenced. See the task report for the final post-staging status.

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
