# Network Segmentation — Proxmox Firewall Policies

These are the per-VM `pve-firewall` policy files as deployed on the Proxmox host
(`/etc/pve/firewall/<vmid>.fw`), supporting the segmentation work in
[Artifact 3](../../../docs/artifacts/Artifact-3-Segmentation-Assessment.md) and
[Case Study 001](../../../docs/case-studies/001-network-segmentation/).

| File | VM | Zone | Inbound policy |
|---|---|---|---|
| `100-hmi-policy.fw` | 100 hmi-scada | Supervisory | default-deny; SSH from Supervisory gateway and Engineering |
| `101-plc-policy.fw` | 101 plc-controller | Field / Control | default-deny; Modbus/502 from HMI; SSH from Field gateway and Engineering |
| `102-historian-policy.fw` | 102 historian-db | Supervisory | default-deny; PostgreSQL/5432 from PLC and HMI; SSH from Supervisory gateway and Engineering |
| `104-monitor-policy.fw` | 104 monitor-wireshark | Monitoring | default-deny; syslog/514 from all four OT zones; SSH from Engineering |

The `192.168.<zone>.1` SSH allow rules exist because the Proxmox host sources
connections to its guests from the bridge-local gateway IP, not the management
IP.

**Provenance:** re-exported from live host state on 2026-08-30 via
`ssh pve "cat /etc/pve/firewall/<n>.fw"`. An earlier snapshot committed 2026-08-19
predated the HMI→historian conduit (`102`) and the gateway-SSH rules (`101`,
`102`), and omitted `100` entirely. Regenerate this set whenever the deployed
policy changes.
