# OT/ICS Cybersecurity Homelab



A production-grade OT/ICS network testbed designed to model water utility infrastructure. This repository documents the design, implementation, and security hardening of an isolated, segmented industrial control system network using Purdue Model architecture.



**Audience:** OT/ICS Security Engineers, Infrastructure Engineers, and Security Assessment Professionals



---



# Project Goal



Demonstrate hands-on engineering capability in OT/ICS network design, segmentation, threat detection, and security assessment through a fully documented, working homelab environment.



This is not a tutorial reproduction. Every design decision, troubleshooting session, and configuration change is documented with clear justification of **why** the approach was chosen.



---



# Architecture Overview



## Purdue Model Implementation



The homelab follows the Purdue Model (ISA-95) with functional zones representing industrial control system operations:



- **Level 0 - Field/Control:** PLC-based process control and sensor/actuator interfaces

- **Level 1 - Supervisory Control:** SCADA HMI, historian, and real-time monitoring

- **Level 2 - Operations Management:** Engineering workstations and configuration access

- **Level 3 - Enterprise Integration:** Monitoring, logging, and security analytics



## Network Topology



```text

Proxmox Host (192.168.1.250)

|

|-- vmbr0 (Management) -> 192.168.1.0/24

|

|-- vmbr10 (PLC Network) -> 192.168.10.0/24

|   \-- VM101: PLC Controller (192.168.10.100)

|

|-- vmbr20 (HMI/SCADA) -> 192.168.20.0/24

|   |-- VM100: HMI/SCADA (192.168.20.100)

|   \-- VM102: Historian/Database (192.168.20.101)

|

|-- vmbr30 (Engineering) -> 192.168.30.0/24

|   \-- VM103: Engineering Workstation (192.168.30.100)

|

\-- vmbr40 (Monitoring/Security) -> 192.168.40.0/24

    \-- VM104: IDS/Monitoring (192.168.40.100)

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



---



## Case Studies



### Case Study 001: OT/ICS Network Segmentation



Implemented and validated a **default-deny OT/ICS network segmentation architecture** using the Proxmox VE firewall.



The project demonstrates:



- Baseline network and service discovery

- Before-and-after connectivity validation

- Default-deny firewall enforcement

- Explicit allow rules for required OT communications

- Direct TCP connectivity testing

- Firewall packet counter validation

- Collection of technical evidence supporting the implementation



**[View Case Study 001: OT/ICS Network Segmentation](docs/case-studies/001-network-segmentation/)**



---



## Future Roadmap



- Threat detection tuning (Zeek, Suricata)

- ICS-specific vulnerability assessment

- Security controls validation

- Incident response simulation



---



## Technologies & Skills



### Infrastructure



- Proxmox VE (hypervisor, network segmentation)

- Linux bridge networking (vmbr0-vmbr40)

- Ubuntu 24.04 LTS

- IPv4 routing and netplan configuration

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

- Wireshark (packet capture and analysis)

- Nmap (device discovery)

- Host-based firewall configuration



### Methodology



- Purdue Model (ISA-95) architecture

- NIST SP 800-82 OT security guidelines

- IEC 62443 zone/conduit design

- Risk assessment and segmentation validation



---



## Documentation Structure



### Architecture & Design



- [**Network Topology**](docs/network-topology.md) - Detailed network layout, IP addressing, and routing logic

- [**Architecture Decision Log**](docs/architecture.md) - Why each design choice was made



### Build Journal



- [**2026-07-27 - OT Network Foundation**](docs/build-journal.md#2026-07-27) - Initial networking setup and verification



### Troubleshooting & Lessons Learned



- [**Proxmox Networking**](docs/troubleshooting/001-proxmox-networking.md) - Bridge configuration, management IP placement, and ARP conflicts

- [**Routing & Inter-Zone Communication**](docs/troubleshooting/002-routing.md) - IPv4 forwarding, static routes, and Windows integration

- [**PLC Network Configuration**](docs/troubleshooting/003-plc-networking.md) - VM-to-VM connectivity and netplan lessons learned



### Configuration Templates



- [**Proxmox Network Configuration**](configs/proxmox/) - Bridge and NIC configurations

- [**Netplan Configuration**](configs/netplan/) - Ubuntu static IP and routing examples

- [**Firewall Rules**](configs/firewall/) - Inter-zone access control examples



---



## Key Insights



### Networking Foundation First



Before deploying OT applications such as OpenPLC and SCADA platforms, establish a solid network foundation. Issues with routing and bridging can be difficult to distinguish from application-layer problems.



### Purdue Model Segmentation is an Operational Necessity, Not Just Security



Segmentation is not just a security control. The zones reflect operational responsibilities and help provide failure isolation between different functions.



### Bridge IP Placement Matters



Management connectivity reliability depends on assigning the management IP to the Linux bridge rather than the physical NIC. This was one of the most impactful lessons learned during the initial setup.



### Context Matters in OT Troubleshooting



A problem that looks like a routing failure might be an ARP cache issue. A connectivity failure might be caused by a firewall rule. Each layer must be validated independently.



---



## How to Use This Repository



### For Hiring Managers / Technical Interviewers



- Read the Architecture Overview and Network Topology to understand the system design

- Review the Build Journal to see real-time problem-solving and decision-making

- Check the [Troubleshooting](docs/troubleshooting/) folder to see how complex problems are diagnosed and resolved

- Review [Configuration Templates](configs/) to see infrastructure-as-code practices



### For OT/ICS Engineers



- Use the [Troubleshooting Documentation](docs/troubleshooting/) as a reference for common homelab issues

- Adapt the [Network Topology](docs/network-topology.md) for your own lab setup

- Review [Configuration Templates](configs/) as examples for Proxmox, netplan, and firewall rules



---



## Skills Demonstrated



- **OT/ICS Fundamentals** - Purdue Model, zone design, and process control concepts

- **Network Engineering** - Segmentation, routing, bridge configuration, and firewall rules

- **Linux System Administration** - Ubuntu, netplan, IPv4 routing, and firewall administration

- **Virtualization** - Proxmox and VM deployment with network isolation

- **Security Assessment** - Threat detection, vulnerability scanning, and compliance frameworks

- **Documentation** - Technical writing, architecture documentation, and troubleshooting procedures

- **Problem-Solving** - Root cause analysis, layer-by-layer investigation, and systematic validation



---



## Credentials & Certifications



- **CISA ICS300** (July 2026)

- **CISA ICS401V** (July 2026)



---



## License



[MIT License](LICENSE)



---



## Contact & Collaboration



This repository is part of a broader OT/ICS cybersecurity portfolio. For questions about the lab design, methodology, or security posture, reach out through GitHub Issues.



---



**Last Updated:** 2026-08-19  

**Lab Status:** Active Development



