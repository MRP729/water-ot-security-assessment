# Portfolio State

**Generated on:** 2026-08-30 (updated four times — see revision notes below)
**Scope:** the `Status` column was filled in as of the 5th update (2026-08-31); all 12 artifacts are Complete. Earlier revisions of this file left it as `TBD` by design — see the revision notes below for how that changed.

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
>
> **Revision note (4th update):** Phase 5 (packaging) began. The `docs/artifacts/` set is
> unchanged at 12 files — the artifact table below is still current — but three things outside
> that directory changed and are not reflected in it: `README.md` was rewritten as the portfolio
> front door (task 5.2); `docs/diagrams/Zone-Conduit-Purdue-Overlay.md` was added (task 5.1); and
> a Finding 2/3 status inconsistency was logged in `FINDINGS-REGISTER.md` for the 5.4 consistency
> pass. `docs/build-journal.md` was reflowed to fix escaped-markdown corruption (no content
> change). Commits `f770e2f`, `5de7ffc`, and the one finalizing this update.
>
> **Revision note (5th update, 2026-08-31): Phase 5 complete.** All of 5.1–5.4 closed. The
> `Status` column below is now filled in — all 12 artifacts are complete. Artifact 3, RRA,
> and `FINDINGS-REGISTER.md` were substantively re-edited this update (PLC-to-historian
> egress anomaly re-verified and root-caused as Finding 3.1; Findings 2 and 3 formally marked
> Remediated; Appendix B evidence index corrected; status vocabulary for Findings 6.1–7.3
> normalized). Dates and word counts below are refreshed accordingly. The `git log --oneline
> -20` and `git status --short` blocks further down are captures from the 3rd update
> (pre-Phase-5) and are retained as historical snapshots, not current state — see the note
> appended after each. Commits `f770e2f` through `a5ef209` (9 commits).

---

## Artifacts

| Filename | H1 Title | Word count | Last commit | Status |
|---|---|---:|---|---|
| `AWWA-Assessment-Shenandoah-Valley-Water-Authority.md` | AWWA Cybersecurity Self-Assessment | 1409 | 2026-08-27 | Complete |
| `Artifact-3-Segmentation-Assessment.md` | OT/ICS Network Segmentation Assessment | 3819 | 2026-08-31 | Complete |
| `Artifact-4-Asset-Inventory-Criticality.md` | OT Asset Inventory & Criticality Analysis | 1554 | 2026-08-27 | Complete |
| `Artifact-5-ATTCK-Oldsmar-Case-Study.md` | MITRE ATT&CK for ICS Incident Mapping | 1499 | 2026-08-27 | Complete |
| `Artifact-6-Mirroring-Investigation-Handoff.md` | Port Mirroring Investigation — Artifact 6 (Suricata Threat Detection) | 2271 | 2026-08-30 | Complete (paused investigation, documented as intentional) |
| `Artifact-6-Threat-Detection-Assessment.md` | OT Threat Detection Assessment | 3127 | 2026-08-30 | Complete |
| `Artifact-7-Water-Sector-Cyber-Risk-Package.md` | Water Sector Cyber Risk Package | 2291 | 2026-08-30 | Complete |
| `Artifact-8-Client-Deliverable-Report.md` | OT Cybersecurity Assessment | 2708 | 2026-08-30 | Complete |
| `Crosswalk-NIST80053-IEC62443-Shenandoah-Valley.md` | NIST 800-53 / IEC 62443 Crosswalk | 880 | 2026-08-27 | Complete |
| `EPA-Checklist-Shenandoah-Valley-Water-Authority.md` | EPA Cybersecurity Checklist Assessment | 1485 | 2026-08-27 | Complete |
| `ERP-Shenandoah-Valley-Water-Authority.md` | Emergency Response Plan — OT/ICS Environment | 1242 | 2026-08-30 | Complete |
| `RRA-Shenandoah-Valley-Water-Authority.md` | Risk & Resilience Assessment | 1894 | 2026-08-31 | Complete |

Word counts are `wc -w` over the raw markdown, so they include markdown syntax, table
pipes, and link targets — treat them as relative magnitude, not prose length.

Last-commit dates are `git log -1 --format=%ad --date=short -- <file>`, refreshed as of the
5th update (2026-08-31). Artifact 3 and the RRA's 2026-08-31 dates reflect the PLC-to-historian
egress anomaly re-verification and the Finding 2/3 remediation status update landing in the
same session. The ERP's date (2026-08-30, previously misrecorded here as 2026-08-26) reflects
its edit alongside Artifact 6a during the earlier finding-identifier-convention update.

Note: filenames use three different naming conventions now — five are
`<Type>-Shenandoah-Valley-...`, six are `Artifact-<n>-...`, and Artifact 6 alone has
**two** files sharing its number (`Artifact-6-Threat-Detection-Assessment.md`, the primary
deliverable, and `Artifact-6-Mirroring-Investigation-Handoff.md`, a linked but separate
investigation record it explicitly defers to in its Section 7). No file is named
`Artifact-1` or `Artifact-2`.
**RESOLVED:** Artifact 1 is the CISA training certificates (external evidence, not a written
artifact — see `docs/training-certificates/`); Artifact 2 is
`docs/operations-linkage/ICS401V-Operations-Linkage.md`, the operations-to-assessment reasoning
document. Both exist; neither uses the `Artifact-N` filename convention because they predate it
or serve a different structural role than the numbered assessment deliverables.

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

The repository contained 19 commits at the time of the 3rd update. As of the 5th update
(2026-08-31), the repository has 29 commits — the 10 added since are Phase 5 packaging and
consistency work (`f770e2f` through `a5ef209`), not reflected in the `-20` list above, which
is retained as a pre-Phase-5 historical snapshot rather than refreshed. See `git log --oneline`
directly for current history.

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

Note (2026-08-31): the GitHub account was renamed `MRP729` -> `MosesPerodin`; the repository's canonical URL is now `https://github.com/MosesPerodin/water-ot-security-assessment.git` (GitHub redirects the old URL). The capture above reflects the remote as configured at generation time.
```

---

## Not assessed

The following remain out of scope and are **not** represented above:

- Any file outside `docs/artifacts/` — including `README.md`, `LICENSE`, `configs/`, and
  the remainder of `docs/`
- Evidence directories, `.nmap` / `.xml` / `.gnmap` / `.pcap` files, and Suricata rule files
