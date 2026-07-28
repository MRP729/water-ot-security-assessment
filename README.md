# OT/ICS Cybersecurity Homelab

A production-grade OT/ICS network testbed designed to model water utility infrastructure. This repository documents the design, implementation, and security hardening of an isolated, segmented industrial control system network using Purdue Model architecture.

**Audience:** OT/ICS Security Engineers, Infrastructure Engineers, and Security Assessment Professionals

---

## Project Goal

Demonstrate hands-on engineering capability in OT/ICS network design, segmentation, threat detection, and security assessment through a fully documented, working homelab environment.

This is not a tutorial reproduction. Every design decision, troubleshooting session, and configuration change is documented with clear justification of WHY the approach was chosen.

---

## Architecture Overview

### Purdue Model Implementation

The homelab follows the NIST Purdue Model (ISA-95) with four functional zones:

- **Level 0 (Field/Control):** PLC-based process control, sensor/actuator interfaces
- **Level 1 (Supervisory Control):** SCADA HMI, historian, real-time monitoring
- **Level 2 (Operations Management):** Engineering workstations, configuration access
- **Level 3 (Enterprise Integration):** Monitoring, logging, security analytics

### Network Topology

```
Proxmox Host (192.168.1.250)
│
├─ vmbr0 (Management) → 192.168.1.0/24
│
├─ vmbr10 (PLC Network) → 192.168.10.0/24
│   └─ VM101: PLC Controller (192.168.10.100)
│
├─ vmbr20 (HMI/SCADA) → 192.168.20.0/24
│   ├─ VM100: HMI/SCADA (192.168.20.100)
│   └─ VM102: Historian/Database (192.168.20.101)
│
├─ vmbr30 (Engineering) → 192.168.30.0/24
│   └─ VM103: Engineering Workstation (192.168.30.100)
│
└─ vmbr40 (Monitoring/Security) → 192.168.40.0/24
    └─ VM104: IDS/Monitoring (192.168.40.100)
```

Each zone is isolated on its own bridge with configurable inter-zone routing and access control.

---

## Current Status

### Completed

- **Proxmox infrastructure:** Host networking, bridge configuration, IPv4 forwarding
- **Network segmentation:** Five isolated Purdue Model zones with static routing
- **VM deployment:** Five Ubuntu 24.04 LTS VMs with assigned roles
- **Connectivity verification:** Ping and SSH tested across all zones from Proxmox and Windows
- **Persistent routing:** Windows static routes to OT subnets configured and validated
- **CISA ICS training:** ICS300 and ICS401V certifications completed

### In Progress

- Historian/database VM configuration
- Engineering workstation hardening
- Monitoring VM setup (Zeek, Suricata)
- OpenPLC deployment and configuration
- SCADA platform selection and deployment

### Future Roadmap

- Network segmentation case study (before/after packet captures)
- Threat detection tuning (Zeek, Suricata)
- ICS-specific vulnerability assessment
- Security controls validation
- Incident response simulation

---

## Technologies & Skills

### Infrastructure
- Proxmox VE (hypervisor, network segmentation)
- Linux bridge networking (vmbr0–vmbr40)
- Ubuntu 24.04 LTS
- IPv4 routing, netplan configuration
- SSH key-based authentication

### OT/ICS
- OpenPLC (planned)
- SCADA platforms (evaluation in progress)
- Modbus TCP
- Historian databases
- HMI design principles

### Security & Monitoring
- Zeek (network flow analysis)
- Suricata (network IDS)
- Wireshark (packet capture & analysis)
- nmap (device discovery)
- Host-based firewall configuration

### Methodology
- Purdue Model (ISA-95) architecture
- NIST 800-82 (OT security guidelines)
- IEC 62443 (zone/conduit design)
- Risk assessment and segmentation validation

---

## Documentation Structure

### Architecture & Design
- **[Network Topology](docs/network-topology.md)** – Detailed network layout, IP addressing, routing logic
- **[Architecture Decision Log](docs/architecture.md)** – Why each design choice was made

### Build Journal
- **[2026-07-27 – OT Network Foundation](docs/build-journal.md#2026-07-27)** – Initial networking setup and verification

### Troubleshooting & Lessons Learned
- **[Proxmox Networking](docs/troubleshooting/001-proxmox-networking.md)** – Bridge configuration, management IP placement, ARP conflicts
- **[Routing & Inter-Zone Communication](docs/troubleshooting/002-routing.md)** – IPv4 forwarding, static routes, Windows integration
- **[PLC Network Configuration](docs/troubleshooting/003-plc-networking.md)** – VM-to-VM connectivity, netplan gotchas

### Configuration Templates
- **[Proxmox Network Configuration](configs/proxmox/)** – Bridge and NIC configurations
- **[Netplan Configuration](configs/netplan/)** – Ubuntu static IP and routing examples
- **[Firewall Rules](configs/firewall/)** – Inter-zone access control examples

---

## Key Insights

### Networking Foundation First
Before deploying OT applications (OpenPLC, SCADA), establish a solid network foundation. Issues with routing and bridging are invisible to application-layer troubleshooting.

### Purdue Model Segmentation is Operational Necessity, Not Just Security
Segmentation isn't just a security control — it's how modern OT systems are actually designed. The four zones reflect real operational responsibilities and failure isolation.

### Bridge IP Placement Matters
Management connectivity reliability depends on assigning the management IP to the Linux bridge, not the physical NIC. This was the most impactful lesson learned during initial setup.

### Context Matters in OT Troubleshooting
A problem that looks like a routing failure might be an ARP cache issue. A connectivity failure might be a firewall rule. Each layer must be validated independently.

---

## How to Use This Repository

### For Hiring Managers / Technical Interviewers
- Read the [Architecture Overview](#architecture-overview) and [Network Topology](docs/network-topology.md) to understand the system design
- Review the [Build Journal](docs/build-journal.md) to see real-time problem-solving and decision-making
- Check the [Troubleshooting](docs/troubleshooting/) folder to see how complex problems are diagnosed and resolved
- Review [Configuration Templates](configs/) to see infrastructure-as-code practices

### For OT/ICS Engineers
- Use the [Troubleshooting Documentation](docs/troubleshooting/) as a reference for common homelab issues
- Adapt the [Network Topology](docs/network-topology.md) for your own lab setup
- Review [Configuration Templates](configs/) as examples for Proxmox, netplan, and firewall rules

---

## Skills Demonstrated

✓ **OT/ICS Fundamentals** – Purdue Model, zone design, process control concepts  
✓ **Network Engineering** – Segmentation, routing, bridge configuration, firewall rules  
✓ **Linux System Administration** – Ubuntu, netplan, IPv4 routing, firewall  
✓ **Virtualization** – Proxmox, VM deployment, network isolation  
✓ **Security Assessment** – Threat detection, vulnerability scanning, compliance frameworks  
✓ **Documentation** – Technical writing, architecture documentation, troubleshooting procedures  
✓ **Problem-Solving** – Root cause analysis, layer-by-layer investigation, systematic validation  

---

## Credentials & Certifications

- **CISA ICS300** (July 2026) – [Certificate](../credentials/CISA-ICS300-Certificate.pdf.pdf)
- **CISA ICS401V** (July 2026) – [Certificate](../credentials/CISA-ICS401V-Certificate.pdf.pdf)

---

## License

[MIT License](LICENSE)

---

## Contact & Collaboration

This repository is part of a broader OT/ICS cybersecurity portfolio. For questions about the lab design, methodology, or security posture, reach out via GitHub Issues.

---

**Last Updated:** 2026-07-27  
**Lab Status:** Active Development
