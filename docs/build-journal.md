# Build Journal

Real-time documentation of homelab construction, decisions, and lessons learned.

\---

## 2026-07-27 – OT Network Foundation

### Objectives

* Establish Proxmox host networking foundation
* Create isolated network segments following Purdue Model (Level 0–3)
* Deploy five Ubuntu 24.04 VMs with assigned roles
* Verify connectivity across all zones
* Establish Windows-to-OT network routing
* Create foundation for OT application deployment

### Completed

#### Proxmox Host Networking Setup

**Management Bridge (vmbr0)**

* Configured Linux bridge on physical NIC (nic0)
* Assigned static IP: 192.168.1.250/24
* Gateway: 192.168.1.1
* Status: ✅ Accessible from Windows via SSH

**Isolated OT Network Bridges**

|Bridge|Network|Purpose|Status|
|-|-|-|-|
|vmbr10|192.168.10.0/24|PLC/Field Control (Level 0)|✅ Active|
|vmbr20|192.168.20.0/24|HMI/SCADA/Historian (Level 1)|✅ Active|
|vmbr30|192.168.30.0/24|Engineering/Management (Level 2)|✅ Active|
|vmbr40|192.168.40.0/24|Monitoring/Security (Level 3)|✅ Active|

**IPv4 Forwarding**

* Enabled forwarding to allow inter-zone routing
* Required for centralized monitoring and management traffic
* Configuration: `/proc/sys/net/ipv4/ip\_forward = 1`

#### VM Deployment

Five Ubuntu 24.04 LTS VMs created with assigned roles:

|VMID|Hostname|Network|IP Address|Role|
|-|-|-|-|-|
|100|hmi-scada|vmbr20|192.168.20.100|SCADA HMI \& Supervisory Control|
|101|plc-controller|vmbr10|192.168.10.100|Field-level PLC control|
|102|historian-db|vmbr20|192.168.20.101|Process data historian|
|103|workstation-eng|vmbr30|192.168.30.100|Engineering/configuration access|
|104|monitor-wireshark|vmbr40|192.168.40.100|Network monitoring \& IDS|

#### Connectivity Verification

**Proxmox to All VMs**

```
Proxmox (192.168.1.250) → All VMs via SSH
- VMID 100 (192.168.20.100): ✅ Ping, SSH
- VMID 101 (192.168.10.100): ✅ Ping, SSH
- VMID 102 (192.168.20.101): ✅ Ping, SSH
- VMID 103 (192.168.30.100): ✅ Ping, SSH
- VMID 104 (192.168.40.100): ✅ Ping, SSH
```

**Windows to All VMs**

```
Windows (192.168.1.0/24) → OT Networks via static routes
- Route to 192.168.10.0/24 (PLC): ✅ Ping, connectivity verified
- Route to 192.168.20.0/24 (HMI): ✅ Ping, connectivity verified
- Route to 192.168.30.0/24 (Eng): ✅ Ping, connectivity verified
- Route to 192.168.40.0/24 (Mon): ✅ Ping, connectivity verified
```

#### Windows Persistent Routes

Added static routes to Windows routing table to persist across reboots:

```powershell
route add 192.168.10.0 mask 255.255.255.0 192.168.1.250 -p
route add 192.168.20.0 mask 255.255.255.0 192.168.1.250 -p
route add 192.168.30.0 mask 255.255.255.0 192.168.1.250 -p
route add 192.168.40.0 mask 255.255.255.0 192.168.1.250 -p
```

Routes verified with `route print` and tested with `ping` to each zone gateway.

#### Inter-Zone Communication

Verified that VMs can reach each other across zones:

* VM101 (PLC) can reach VM100 (HMI) via 192.168.20.100 ✅
* VM100 (HMI) can reach VM104 (Monitor) via 192.168.40.100 ✅
* VM103 (Engineering) can reach all zones ✅

### Lessons Learned

#### 1\. Bridge Configuration — IP Placement is Critical

**Issue:** Initial management connectivity was unreliable. SSH sessions would hang, then succeed intermittently.

**Root Cause:** Management IP was assigned directly to the physical NIC (`nic0`) instead of the Linux bridge (`vmbr0`).

**Correct Approach:**

```
Physical NIC (no IP):
iface nic0 inet manual
    # No IP assignment — NIC only carries bridge traffic

Linux Bridge (owns the IP):
auto vmbr0
iface vmbr0 inet static
    address 192.168.1.250/24
    gateway 192.168.1.1
    bridge-ports nic0
```

**Why This Matters:** The Linux bridge is the logical interface that coordinates traffic across all bridge members. By assigning the management IP to the bridge instead of the physical NIC, traffic is handled consistently and reliably.

#### 2\. ARP Conflicts Can Mimic Routing Failures

**Issue:** Windows sometimes connected to the wrong device on the network, causing intermittent connectivity to the Proxmox host.

**Root Cause:** ARP cache contained stale entries (duplicate IP addresses from a previous network configuration on another device).

**Resolution:** Cleared ARP cache and verified no duplicate IPs existed on the network.

**Key Insight:** Connectivity that works intermittently is often NOT a routing problem—it's usually a Layer 2 (MAC/ARP) issue. When troubleshooting network connectivity, validate each layer independently before moving up the stack.

#### 3\. Windows Requires Explicit Routes to Non-Adjacent Subnets

**Issue:** Windows couldn't reach OT network subnets (192.168.10.0/24, etc.) even though Proxmox had routing configured.

**Root Cause:** Windows only knows how to reach 192.168.1.0/24 directly. To reach other subnets, it needs static routes that say "to reach 192.168.10.0/24, send traffic to 192.168.1.250 (Proxmox)."

**Resolution:** Added persistent static routes using `route add ... -p`:

```powershell
route add 192.168.10.0 mask 255.255.255.0 192.168.1.250 -p
```

**Why -p Flag Matters:** Without `-p`, routes disappear after reboot. With `-p`, they persist in the registry and survive restarts.

#### 4\. Purdue Model Zones Reflect Real Operational Boundaries

**Insight:** When designing the four zones (PLC, HMI, Engineering, Monitoring), I realized these aren't arbitrary security boundaries—they reflect actual operational responsibilities:

* **Level 0 (PLC Network):** Only running the controller. No external communication needed.
* **Level 1 (HMI/SCADA):** Operators and historians. Real-time process data lives here.
* **Level 2 (Engineering):** Where configuration changes happen. Network isolation prevents accidents.
* **Level 3 (Monitoring):** Security tools and centralized logging. Isolated from operational zones to prevent compromise.

This segmentation is both a security control AND an operational necessity.

#### 5\. Validate Each Layer Independently

When a system doesn't work end-to-end, don't assume the entire stack is broken. Test systematically:

1. **Layer 1 (Physical):** Are cables connected? (Virtual equivalent: are bridges configured?)
2. **Layer 2 (MAC/ARP):** Can devices find each other? (Check ARP table, verify no duplicates)
3. **Layer 3 (Routing):** Can packets reach non-adjacent subnets? (Test with ping, check routing tables)
4. **Layer 4+ (Applications):** Once L1-L3 work, troubleshoot SSH, HTTP, etc.

This was the most valuable troubleshooting lesson: many "network problems" are actually solved by ensuring each layer works in isolation.

### Next Steps

1. **Historian VM Configuration** – Set up PostgreSQL or InfluxDB for time-series process data
2. **Engineering Workstation Hardening** – Configure access controls, logging, configuration management
3. **Monitoring VM Setup** – Deploy Zeek for network flow analysis, prepare for Suricata IDS
4. **OpenPLC Deployment** – Install on VM101, configure basic ladder logic and Modbus TCP
5. **SCADA Platform Selection** – Evaluate SCADA-LTS, Ignition, or other platforms for VM100
6. **All-Zone Connectivity Verification** – End-to-end packet capture and analysis before applications
7. **Security Baseline** – Firewall rules, access control matrix, compliance with IEC 62443

### Timeline \& Velocity

* **Session Start:** 2026-07-21
* **Foundation Complete:** 2026-07-27 (6 days)
* **Planned Completion (full lab):** 2026-08-17

Ahead of original 12-week schedule. Prioritizing network foundation before application deployment is paying off—troubleshooting will be significantly faster once OT software is running.

\---

**Session Status:** ✅ Complete  
**Next Review:** After Historian VM configuration  
**Issues/Blockers:** None



\---



\## 2026-08-19 - OT/ICS Network Segmentation



With the network foundation established and unrestricted inter-zone connectivity validated, the next phase focused on implementing security controls between the OT/ICS network zones.



The objective was to move from an unrestricted routing environment to a \*\*default-deny segmentation model\*\* using the Proxmox VE firewall.



This phase was documented separately as \*\*Case Study 001: OT/ICS Network Segmentation\*\*. The case study includes the firewall policies, connectivity validation, packet counter evidence, and before-and-after testing results.



\### Objectives



\- Capture baseline connectivity before firewall restrictions

\- Identify required communications between OT/ICS zones

\- Implement default-deny firewall policies

\- Explicitly allow required OT communications

\- Validate permitted and restricted traffic

\- Verify firewall rule enforcement using packet counters

\- Collect technical evidence for the implementation



\### Security Approach



The segmentation design followed a \*\*default-deny\*\* approach.



Rather than allowing unrestricted communication between all OT zones, firewall policies were applied to restrict traffic and permit only the communications required for the lab environment.



The implementation focused on the following systems:



\- \*\*VM101 - PLC Controller\*\*

\- \*\*VM102 - Historian/Database\*\*

\- \*\*VM104 - Monitoring/Security\*\*



Firewall policies were created to control communication between the PLC, HMI/SCADA, Historian, and Monitoring zones.



\### Validation Testing



Direct TCP connectivity testing was performed to validate the segmentation controls.



Testing included required and restricted communications involving services such as:



\- SSH - TCP 22

\- Modbus TCP - TCP 502

\- Syslog/monitoring traffic - TCP 514

\- PostgreSQL/database traffic - TCP 5432



Examples of validation scenarios included:



\- HMI to PLC communication

\- HMI to Historian communication

\- HMI to Monitoring communication

\- PLC to Historian communication

\- PLC to Monitoring communication

\- Historian to Monitoring communication



The results were collected and preserved as technical evidence.



\### Firewall Counter Validation



Firewall packet counters were reviewed after connectivity testing to confirm that traffic was matching the intended firewall rules.



Evidence was collected for:



\- PLC firewall rules

\- Historian firewall rules

\- Monitoring firewall rules

\- Proxmox firewall forwarding rules



This provided an additional validation layer beyond simply confirming whether a TCP connection succeeded or failed.



\### Key Outcome



The OT/ICS environment transitioned from unrestricted inter-zone communication to a controlled segmentation architecture based on explicit communication requirements.



The completed implementation demonstrates:



\- Baseline network discovery

\- Default-deny firewall enforcement

\- Explicit allow rules

\- OT service validation

\- Direct TCP testing

\- Firewall packet counter analysis

\- Technical evidence collection



\### Documentation and Evidence



The full implementation is documented in:



`docs/case-studies/001-network-segmentation/`



Firewall policy examples are located in:



`configs/firewall/network-segmentation/`



Validation evidence is located in:



`docs/case-studies/001-network-segmentation/evidence/after-validation/`



\### Lessons Learned



1\. \*\*Baseline testing must happen before restrictions are applied\*\*



Capturing the unrestricted network state before implementing firewall policies made it possible to clearly demonstrate the effect of segmentation.



Without baseline evidence, it would be more difficult to distinguish between a firewall restriction and a pre-existing connectivity problem.



2\. \*\*Default-deny requires understanding legitimate communication paths\*\*



Segmentation cannot be implemented effectively by simply blocking traffic.



Each allowed communication path must have an operational justification. This requires identifying which systems need to communicate, which ports and protocols are required, and which paths should remain blocked.



3\. \*\*Connectivity testing alone is not sufficient\*\*



A successful or failed TCP connection shows the outcome, but firewall packet counters provide additional evidence that traffic is matching the intended policy.



Using both methods strengthened the validation of the implementation.



4\. \*\*Documentation is part of the security control validation\*\*



The firewall configuration, testing results, and packet counter evidence were preserved alongside the case study.



This creates a repeatable record showing what was implemented, why it was implemented, and how the results were validated.



\### Current Status



\*\*Completed:\*\*



\- Network foundation

\- Purdue Model zone implementation

\- Inter-zone routing

\- Windows static routing

\- Baseline connectivity validation

\- Default-deny OT/ICS network segmentation

\- Firewall policy implementation

\- Connectivity testing

\- Firewall counter validation

\- Case Study 001 documentation and evidence collection



\*\*Next Phase:\*\*



\- Configure Historian/Database services

\- Deploy OpenPLC on the PLC Controller

\- Select and deploy a SCADA/HMI platform

\- Harden the Engineering Workstation

\- Establish monitoring visibility with Zeek and Suricata

\- Perform ICS-specific security assessment and detection testing



\---



Session Status: Active Development  

Current Phase: OT/ICS Application and Monitoring Deployment  

Issues/Blockers: None

