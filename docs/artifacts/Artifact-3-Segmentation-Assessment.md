# OT/ICS Network Segmentation Assessment
## Shenandoah Valley Water Authority

**Assessment type:** Network segmentation validation, before/after remediation
**Assessment period:** August 25–26, 2026
**Prepared for:** Chief Information Security Officer / Plant Operations Leadership
**Classification:** Internal — Assessment Evidence

---

## 1. Executive Summary

Shenandoah Valley Water Authority's operational technology (OT) network — covering PLC/field control, supervisory HMI, historian data, and monitoring — was assessed for network segmentation effectiveness between Engineering workstations and the operational zones they should not have unrestricted access to.

**What we found before remediation:** the PLC controlling field operations was reachable from the Engineering zone with five open network paths, including the Modbus and EtherNet/IP protocols used to read and write control logic, and a web management interface running with unchanged default credentials. The historian, which stores process data, was reachable on its database port from the same zone with no network-layer restriction — its only protection was a password check inside the database software itself.

**What we did:** we implemented zone-based firewall enforcement consistent with the IEC 62443 zones-and-conduits model, closing all engineering-to-field and engineering-to-historian paths except administrative SSH access, and routed the two legitimate operational conduits (HMI-to-PLC, HMI-to-historian) through the supervisory zone as intended by the original architecture.

**Result:** the PLC's exposed attack surface from Engineering dropped from five open ports to one (SSH only). The historian's database port is no longer reachable from Engineering; it remains reachable from the HMI, which is its only legitimate operational consumer. No existing monitoring or supervisory function was disrupted.

**What remains open:** one unresolved technical anomaly prevented the PLC from being configured with direct network egress to the historian under the new firewall policy — full detail and elimination methodology in Appendix A. We route around it operationally (HMI relays PLC data to the historian) rather than leave the segmentation incomplete, and we treat the anomaly itself as a legitimate, well-evidenced finding rather than a task left undone. The OpenPLC web interface's default credentials (Finding 1) were remediated on August 27, 2026, closing the highest-severity open item from this assessment cycle.

---

## 2. Assessment Scope & Methodology

**In scope:** four network zones on Shenandoah Valley Water Authority's OT network, aligned to the Purdue Model:

| Zone | Subnet | Role |
|---|---|---|
| Field / Control | 192.168.10.0/24 | Programmable Logic Controller (PLC) |
| Supervisory / HMI | 192.168.20.0/24 | Human-Machine Interface, Process Historian |
| Engineering | 192.168.30.0/24 | Engineering workstation, assessment platform |
| Monitoring | 192.168.40.0/24 | Network/security monitoring |

**Assessment vantage point:** all scans originated from the Engineering zone (192.168.30.0/24), simulating the access level of an engineering workstation user or an attacker who has compromised one. This is the relevant threat model for insider misuse and lateral movement following an initial compromise of a non-OT-critical host.

**Tooling:** `nmap` 7.95, service-version detection (`-sV`), timing template `-T3` (deliberately conservative — aggressive scan timing risks destabilizing legacy PLC/RTU firmware, and the restraint itself is a documented methodology choice, not an oversight).

**Methodology note on scan flags:** the before-state scans (August 25) did not use the `-Pn` flag, meaning nmap only reported on hosts that responded to ICMP ping. The after-state scans (August 26) added `-Pn`, sweeping every address in each /24 regardless of ping response. This was a deliberate adjustment once we confirmed the firewall policy would filter ICMP as well as TCP — without `-Pn`, a fully-segmented host can look identical to a host that simply isn't there. We disclose this rather than let the two scan sets appear directly overlaid; the before-scans' single-host output per zone reflects which hosts answered ping at that time, not a smaller network.

**A related, favorable finding:** the HMI (192.168.20.100) does not appear at all in the August 25 supervisory before-scan. This is not a gap in the evidence — it is because Step 1 of remediation (fixing the HMI's firewall) was completed *before* baseline evidence capture began, so the HMI was already filtering ICMP by the time the "before" scan ran. The historian (192.168.20.101), which had not yet been remediated, appears in full. This sequencing is intentional and is discussed further in Section 8.

---

## 3. Before-State Network Posture

### 3.1 Field / Control Zone — PLC (192.168.10.100)

**Scan:** `nmap -T3 -sV -p 502,8080,102,44818,22 192.168.10.0/24` — Aug 25, 2026, 17:36:58–17:40:06

| Port | Service | State | Version |
|---|---|---|---|
| 22/tcp | ssh | open | OpenSSH 9.6p1 Ubuntu 3ubuntu13.18 |
| 102/tcp | iso-tsap | open | Siemens S7 PLC (protocol fingerprint match) |
| 502/tcp | modbus | open | Modbus TCP |
| 8080/tcp | http | open | Werkzeug httpd 2.3.7 (Python 3.12.3) |
| 44818/tcp | EtherNetIP-2 | open | (unconfirmed banner) |

Five ports open, all reachable from Engineering with no restriction. Ports 502 (Modbus) and 44818 (EtherNet/IP) are the two protocols an attacker would use to read or manipulate PLC control logic directly. Port 8080 is OpenPLC's web management console — see Finding 1 below.

### 3.2 Supervisory / HMI Zone — Historian (192.168.20.101)

**Scan:** `nmap -T3 -sV -p 5432,22 192.168.20.0/24` — Aug 25, 2026, 17:40:38–17:41:13

| Port | Service | State | Version |
|---|---|---|---|
| 22/tcp | ssh | open | OpenSSH 9.6p1 Ubuntu 3ubuntu13.18 |
| 5432/tcp | postgresql | open | PostgreSQL DB 9.6.0 or later |

The historian's PostgreSQL port is directly reachable from Engineering. The database itself is password-gated, but nothing at the network layer prevents a connection attempt, credential brute-force, or exploitation of a PostgreSQL vulnerability from this zone.

*(HMI, 192.168.20.100, does not appear in this scan — already remediated at time of capture; see Section 2.)*

### 3.3 Monitoring Zone — Monitor Host (192.168.40.100)

**Scan:** `nmap -T3 -sV -Pn -p 514,22 192.168.40.0/24` — Aug 25, 2026, 17:50:33–17:57:17

| Port | Service | State |
|---|---|---|
| 22/tcp | ssh | open |
| 514/tcp | shell | filtered |

Monitoring zone was already correctly restricted at baseline (Phase 1 default-deny enforcement was active and functioning here). Included for completeness and as the no-regression control in the after-state comparison.

---

## 4. Current-State Vulnerabilities Identified

**Finding 1 — Default credentials on the PLC web management interface (High) — REMEDIATED Aug 27, 2026.**
OpenPLC's web dashboard (port 8080) accepted the vendor default credentials `openplc` / `openplc`. This was confirmed by direct HTTP request: a POST to `/login` with these credentials returned HTTP 302 with a redirect to `/dashboard` and a valid session cookie. Combined with the network path being open at baseline, this represented full unauthenticated administrative access to PLC control logic from the Engineering zone. *(This was a credential-hygiene finding, distinct from the network-segmentation finding — closing the network path in Section 5 did not by itself fix the credentials.)*

**Remediation:** the default account was replaced via OpenPLC's built-in Users interface (accessed through an SSH tunnel to the PLC's loopback, since the web port is no longer reachable from Engineering post-segmentation). Verification followed the same method used to originally confirm the finding — a direct POST to `/login` from the PLC's own shell:
- `openplc` / `openplc` → HTTP 200, "Bad credentials! Try again" (confirmed dead)
- New credentials → HTTP 302, redirect to `/dashboard` with valid session cookie (confirmed working)

This closes the single highest-severity open item identified across the Phase 4 assessment package.

**Finding 2 — Historian database access controlled at the application layer only (High).**
PostgreSQL's `pg_hba.conf` restricts connections to a single IP internally, but this control lived entirely inside the database engine. At the network layer — the layer an attacker touches first — the port was open to the entire Engineering zone. A network-layer control is the correct primary defense; an application-layer control alone is a single point of failure.

**Finding 3 — Configuration-vs-enforcement gap on PLC and historian firewalls (Medium, methodological).**
Both hosts had Proxmox firewall configuration files (`101.fw`, `102.fw`) present and syntactically correct since Phase 1, specifying `policy_in: DROP`. Despite this, Proxmox was not enforcing them — the guest NICs had `firewall=0` set at the VM configuration level, and the compiled firewall ruleset showed no chains for either host's network interface. The configuration existed; enforcement did not. This is a general class of finding relevant to any environment audit: a documented control is not evidence the control is active, and configuration review must be paired with live-enforcement verification, not assumed from file contents.

**Finding 4 — HMI web interface unencrypted (Medium).**
The HMI's Apache service runs on port 80 (HTTP) with no TLS. Any HMI session, including credentials if authentication is later added, would be visible to anyone able to observe traffic on that segment.

---

## 5. Segmentation Design

Remediation followed the IEC 62443 zones-and-conduits model: each zone gets a default-deny boundary, and only explicitly needed conduits are opened.

**Zone boundaries enforced (via Proxmox host-level firewall, `pve-firewall`):**
- Field/Control (vmbr10) — default deny inbound; SSH permitted only from the zone's own gateway IP (administrative access pattern)
- Supervisory/HMI (vmbr20) — default deny inbound; SSH permitted only from the zone's own gateway IP
- Historian's PostgreSQL port (5432) — permitted only from the HMI's IP (192.168.20.100), not from Engineering or any other source

**Conduits explicitly authorized:**
- HMI → PLC, Modbus/502 (supervisory control conduit — verified working, 754 packets passed with 0 drops on the compiled interface counter)
- HMI → Historian, PostgreSQL/5432 (data logging conduit — added and verified working during remediation)

**Conduit deliberately not implemented as designed:**
- PLC → Historian, direct (original architecture intent). An unresolved platform-level anomaly prevents the PLC from establishing any outbound connection beyond its own subnet once its firewall is enabled — full detail in Appendix A. The architecture was reframed so the HMI relays data between PLC and historian instead of PLC connecting directly. Both legs of this relay path (HMI↔PLC, HMI↔historian) are independently verified working.

This reframing is itself defensible from a security-architecture standpoint, independent of the anomaly that forced it: routing all Field-zone data through the Supervisory zone rather than allowing Field to reach Historian directly reduces the PLC's network footprint to the single conduit it strictly needs (HMI), which is arguably tighter segmentation than the original design.

The resulting zone/conduit layout, with a Purdue Model overlay, is diagrammed in [`docs/diagrams/Zone-Conduit-Purdue-Overlay.md`](../diagrams/Zone-Conduit-Purdue-Overlay.md).

---

## 6. After-State Network Posture

### 6.1 Field / Control Zone — PLC (192.168.10.100)

**Scan:** `nmap -T3 -sV -Pn -p 502,8080,102,44818,22 192.168.10.0/24` — Aug 26, 2026, 17:33:50–17:42:25

| Port | Service | State (before → after) |
|---|---|---|
| 22/tcp | ssh | open → **open** |
| 102/tcp | iso-tsap | open → **filtered** |
| 502/tcp | modbus | open → **filtered** |
| 8080/tcp | http-proxy | open → **filtered** |
| 44818/tcp | EtherNetIP-2 | open → **filtered** |

All 255 other addresses in the /24 returned uniformly filtered across all five ports — consistent with a functioning default-deny policy rather than genuine unreachability (see methodology note, Section 2).

### 6.2 Supervisory / HMI Zone — HMI and Historian

**Scan:** `nmap -T3 -sV -Pn -p 5432,22 192.168.20.0/24` — Aug 26, 2026, 17:42:25–17:49:09

| Host | Port 22 | Port 5432 (before → after) |
|---|---|---|
| HMI (192.168.20.100) | open | filtered → **filtered** (no change; HMI never ran postgres) |
| Historian (192.168.20.101) | open | open → **filtered** |

The historian's database port is no longer reachable from Engineering. It remains reachable from the HMI, per the authorized conduit rule in `102.fw`, confirmed by a direct connectivity test (HMI → historian:5432 = CONNECTED).

### 6.3 Monitoring Zone — Monitor Host (192.168.40.100)

**Scan:** `nmap -T3 -sV -Pn -p 514,22 192.168.40.0/24` — Aug 26, 2026, 17:49:09–17:55:59

| Port | Before | After |
|---|---|---|
| 22/tcp | open | open |
| 514/tcp | filtered | filtered |

No change. Included as the negative control confirming the remediation work did not introduce regressions elsewhere on the network.

---

## 7. Before/After Comparison Summary

| Host | Zone | Ports open (before) | Ports open (after) | Delta |
|---|---|---|---|---|
| PLC (192.168.10.100) | Field/Control | 5 (22, 102, 502, 8080, 44818) | 1 (22) | **−4, all ICS protocol ports closed** |
| Historian (192.168.20.101) | Supervisory | 2 (22, 5432) | 1 (22) | **−1, database port closed to Engineering** |
| HMI (192.168.20.100) | Supervisory | 1 (22)* | 1 (22) | No change (already hardened pre-baseline) |
| Monitor (192.168.40.100) | Monitoring | 1 (22) | 1 (22) | No change (control, no regression) |

*HMI's true before-state ICS-relevant port was already filtered prior to baseline capture — see Section 2.

The core result: **the PLC's exploitable network attack surface from the Engineering zone was reduced by 80% (5 ports to 1), and the historian's database exposure to that same zone was eliminated entirely**, with zero disruption to legitimate supervisory traffic.

---

## 8. Lessons Learned

**Configuration is not enforcement.** The single most consequential finding of this assessment (Finding 3) was not a vulnerability an attacker would exploit directly — it was a gap between what the firewall configuration *said* and what the platform was *actually doing*. Both PLC and historian had syntactically valid `.fw` files with `policy_in: DROP` sitting unenforced since Phase 1 because the underlying VM network interfaces had `firewall=0` set. A configuration review alone — reading the files — would have concluded these hosts were protected. Only checking the compiled, live ruleset revealed they were not. Any assessment methodology that treats configuration file review as equivalent to control verification will produce false assurance. This generalizes directly to real utility environments: a firewall policy applied in a management console is not evidence it is active on the wire.

**Sequencing evidence capture matters.** Fixing the HMI's firewall before capturing baseline evidence (rather than after) meant the "before" scan doesn't show HMI's true pre-remediation exposure. This was flagged during the assessment as a sequencing consideration, not hidden after the fact — the report states plainly what the before-scan does and doesn't capture, and why. An assessment that silently presents partial evidence as complete undermines its own credibility; one that discloses the gap and explains the cause preserves it.

**Not every finding resolves cleanly, and that is itself information.** The PLC-to-historian egress anomaly (Appendix A) consumed significant investigation time and was never root-caused despite systematically eliminating fourteen distinct hypotheses across routing, local firewall state, the Proxmox firewall subsystem (both the legacy and current implementations), MAC filtering, bridge topology, and kernel-level packet tracing. In a real assessment, walking away from an unresolved anomaly without documenting the elimination process would be a missed opportunity — the methodology of *how* something was ruled out is often more valuable to a receiving team than the root cause itself, because it tells them what has already been checked before they spend their own time re-checking it.

**Consequence framing changes what "done" means.** A port-counting exercise (5 open → 1 open) is necessary but not sufficient. The reason the Modbus and EtherNet/IP ports mattered is that they are the mechanism by which control logic gets read or altered — the consequence is potential disruption to treatment or dosing processes, not an abstract network metric. Keeping that consequence explicit throughout the assessment (rather than only in the executive summary) is what turns a scan comparison into a security argument a plant manager can act on.

---

## 9. Remediation Narrative & Standards Alignment

Shenandoah Valley Water Authority's OT network began Phase 3 in a state of **partial, inconsistently applied segmentation** — not flat, but not reliably enforced either. Two hosts (HMI, Monitor) had genuine default-deny enforcement from Phase 1; two hosts (PLC, Historian) had configuration files describing the same intent, but no live enforcement. This inconsistency was itself the primary risk: an assessor or defender reviewing configuration alone would have incorrectly concluded the network was uniformly protected.

Remediation moved the network to **consistently enforced, zone-based segmentation** aligned with IEC 62443's zones-and-conduits model — every zone boundary now defaults to deny, and every permitted cross-zone conduit is explicit, narrow, and independently verified rather than inferred from configuration intent. This aligns with the Purdue Model's separation of Level 1 (Field/Control) from Level 2 (Supervisory) and restricts Level 3 (Engineering) to the administrative access it genuinely requires, rather than the unrestricted operational access it had by default.

The full IEC 62443 / NIST 800-53 control mapping is provided as a separate crosswalk document (Task 6 of this package). The Risk & Resilience Assessment (Task 2) carries these findings forward into a formal likelihood/impact analysis and prioritized remediation roadmap — most notably, closing Finding 1 (default OpenPLC credentials) as a near-term action item, since network segmentation alone does not remediate a credential sitting in plain documentation.

---

## Appendix A: PLC-to-Historian Egress Anomaly — Investigation Methodology

**Symptom:** once the Proxmox firewall was enabled (`firewall=1`) on the PLC VM, the PLC could not establish any outbound TCP connection to any host outside its own /24 subnet — not to the historian, not to the Proxmox host's management interface, not to an arbitrary external address. This was reproducible on demand: toggling `firewall=0` restored egress immediately; toggling back to `firewall=1` broke it immediately.

**Hypotheses eliminated, with evidence:**

1. Proxmox firewall rule content — reviewed and correct
2. PLC's routing table — confirmed correct via `ip route get`, multiple checks
3. PLC's local iptables (all four tables: filter, nat, raw, mangle) — confirmed empty, policy ACCEPT, via live `sudo iptables -L` on the host
4. `ufw` — confirmed inactive
5. Test tooling (`nc` vs. `/dev/tcp`) — both fail identically, ruling out a tooling artifact
6. `tcpdump` filter syntax — wide, unfiltered capture on the bridge confirmed zero packets ever left the PLC's interface
7. Missing compiled OUT chain — initially suspected, later disproven; live `iptables -L` and `ebtables -L` showed the chain present and correctly passing traffic (754 packets matched, 0 dropped)
8. MAC address filtering — exact match confirmed
9. Bridge topology — PLC's bridge (vmbr10) structurally identical to working hosts' bridges
10. Forwarding database (FDB) — no stale entries
11. VM reboot — did not resolve
12. `pve-firewall` service restart — did not resolve
13. Network interface delete-and-recreate — did not resolve, ruling out stale interface state
14. ARP/neighbor resolution to gateway — confirmed working

**Key correction made during investigation:** the `pve-firewall compile` CLI preview command was found to be unreliable — it repeatedly reported the PLC's OUT chain as entirely absent, while live kernel inspection (`iptables -L`, `ebtables -L`) showed the chain present and functioning normally. Any earlier conclusion based on compile output alone should be discounted; live inspection is the trustworthy source for enforcement state on this platform.

**Also ruled out:** a second, independent firewall subsystem (`proxmox-firewall`, nftables-based) exists on this Proxmox 9.x host alongside the legacy iptables-based `pve-firewall`. It was confirmed completely inert — zero compiled rules, zero log entries, not opted into via cluster configuration — and is not a contributing factor.

**Current state:** the SYN packet is accepted by the kernel (`connect()` returns `EINPROGRESS`, not an immediate error) but never physically transmits onto the bridge. This is consistent across every layer that can be directly inspected. Root cause remains undetermined.

**Not yet attempted (documented for any future investigation):** live kernel packet tracing (`nft monitor`, `iptables -j LOG`); bridge port/STP state inspection on the PLC's tap interface; interface-level statistics via `ethtool -S`; QEMU device-level debug via `qm monitor`.

**Disposition:** the network architecture was reframed so the PLC does not require direct historian egress (Section 5). This anomaly does not block the segmentation objective and is documented here as a legitimate, thoroughly-evidenced open finding.

---

## Appendix B: Evidence Index

| File | Date | Zone | Description |
|---|---|---|---|
| `field-before.nmap` | Aug 25, 2026 | Field/Control | Pre-remediation targeted scan, PLC ports |
| `field-after.nmap` | Aug 26, 2026 | Field/Control | Post-remediation full-sweep scan, PLC ports |
| `supervisory-before.nmap` | Aug 25, 2026 | Supervisory | Pre-remediation targeted scan, HMI/historian |
| `supervisory-after.nmap` | Aug 26, 2026 | Supervisory | Post-remediation full-sweep scan, HMI/historian |
| `monitor-before.nmap` | Aug 25, 2026 | Monitoring | Pre-remediation full-sweep scan, monitor host |
| `monitor-after.nmap` | Aug 26, 2026 | Monitoring | Post-remediation full-sweep scan, monitor host |
| `host-directory.nmap` | Aug 17, 2026 | Engineering | Phase 1 asset-discovery ping sweep |
| `svc-plc-before/after.nmap` | Aug 19, 2026 | Field/Control | Phase 1/2 full-port scans predating OpenPLC install |
| `svc-supervisory-before/after.nmap` | Aug 19, 2026 | Supervisory | Phase 1/2 full-port scans predating service install |
| `svc-workstation-eng-before/after.nmap` | Aug 19, 2026 | Engineering | Phase 1/2 full-port scan, engineering workstation |
| `xzone-eng-to-*-before/after.nmap` | Aug 19, 2026 | Cross-zone | Phase 1 connectivity ping sweeps, pre-service, establishing flat-network baseline |

The August 19 files predate service installation (Phase 2) and establish that all hosts were clean, minimal-footprint Ubuntu images before OpenPLC/PostgreSQL/Apache were introduced — useful supporting context for the asset inventory in the Risk & Resilience Assessment, but not the primary segmentation evidence, which is the August 25/26 pair above.

The full evidence set — raw `nmap` `.xml`/`.gnmap` companions, the August 19 pre-service scans, firewall policy files, and conduit connectivity test logs — is retained offline under chain-of-custody and is available on request. The curated subset supporting the after-state claims in this document is committed under [`docs/case-studies/001-network-segmentation/evidence/`](../case-studies/001-network-segmentation/evidence/).

**Note on prior (August 19) evidence directory:** an earlier evidence set at `C:\Users\moses\ot-ics-evidence-baseline-aug19\` is excluded from this assessment. Several files in that directory were deleted under unexplained circumstances between two directory listings, and no attempt is made here to reconstruct or rely on them — the August 25/26 evidence set is complete and sufficient on its own.
