# Case Study 001: OT/ICS Network Segmentation with Proxmox Firewall



## Overview



This project demonstrates the implementation and validation of network segmentation controls within a virtualized OT/ICS homelab environment.



The objective was to move the environment from a relatively unrestricted communication model to a **default-deny architecture**, where communication between OT systems is permitted only when explicitly required.



The environment was built and tested using Proxmox VE firewall controls, separate network segments, and service-specific rules.



The project includes:



- Baseline network and service discovery

- Before-and-after connectivity validation

- Default-deny firewall policies

- Explicit allow rules for required OT communications

- Firewall packet counter validation

- Evidence collection for portfolio and documentation purposes



---



## Lab Architecture



The OT/ICS environment consists of multiple logical network segments representing common industrial control system functions.



| System | Role | IP Address / Network |

|---|---|---|

| PLC Controller | Simulated industrial controller | 192.168.10.100 |

| HMI/SCADA | Supervisory control interface | 192.168.20.100 |

| Historian Database | Process and historical data storage | 192.168.20.101 |

| Engineering Workstation | Administrative and engineering access | 192.168.30.0/24 |

| Monitoring System | Centralized monitoring/logging | 192.168.40.100 |



The segmentation design separates industrial functions and limits communication based on operational requirements.



---



## Security Objective



The primary objective was to implement a **least-privilege communication model**.



Instead of allowing unrestricted communication between systems, each protected workload uses a default inbound deny policy:



```text

policy_in: DROP

```



Only explicitly authorized traffic is permitted.



This approach reduces unnecessary lateral movement opportunities between systems and demonstrates the principle of network segmentation commonly used in OT/ICS environments.



---



## Allowed Communication Paths



### HMI/SCADA to PLC



The HMI is allowed to communicate with the PLC using Modbus TCP.



```text

Source: 192.168.20.100

Destination: 192.168.10.100

Protocol: TCP

Port: 502

```



Firewall rule:



```text

IN ACCEPT -source 192.168.20.100 -p tcp -dport 502

```



This allows the supervisory system to communicate with the PLC while blocking other unsolicited inbound connections.



---



### PLC to Historian



The PLC is allowed to communicate with the Historian database using PostgreSQL.



```text

Source: 192.168.10.100

Destination: 192.168.20.101

Protocol: TCP

Port: 5432

```



Firewall rule:



```text

IN ACCEPT -source 192.168.10.100 -p tcp -dport 5432

```



Only the authorized PLC is allowed to access the Historian database service.



---



### OT Systems to Monitoring



OT systems are allowed to communicate with the centralized monitoring system on TCP port 514.



Authorized source networks include:



```text

192.168.10.0/24

192.168.20.0/24

192.168.30.0/24

192.168.40.0/24

```



Example firewall rule:



```text

IN ACCEPT -source 192.168.10.0/24 -p tcp -dport 514

```



This permits authorized systems to reach the monitoring service while maintaining a default-deny posture for other inbound connections.



---



### Engineering Workstation Administrative Access



Administrative SSH access is permitted from the Engineering Workstation network.



```text

Source: 192.168.30.0/24

Protocol: TCP

Port: 22

```



Example firewall rule:



```text

IN ACCEPT -source 192.168.30.0/24 -p tcp -dport 22

```



This provides controlled management access without exposing SSH access to all network segments.



---



## Firewall Policies



The implemented Proxmox firewall policies are stored in:



```text

configs/firewall/network-segmentation/

```



The policies include:



- `100-hmi-policy.fw`

- `101-plc-policy.fw`

- `102-historian-policy.fw`

- `104-monitor-policy.fw`



Each policy uses a default inbound deny posture with explicit allow rules.



---



## Validation Methodology



The implementation was validated using:



1. Baseline network and service discovery

2. Post-firewall validation

3. Direct TCP connectivity tests

4. Proxmox firewall status verification

5. Firewall packet counter inspection

6. Firewall rule inspection



The goal was to verify that:



- Required OT communication continued to function.

- Unauthorized communication was blocked.

- Firewall rules were actively processing traffic.

- The resulting controls were documented with reproducible evidence.



---



## Validation Results



### HMI to PLC — Modbus TCP



The HMI successfully connected to the PLC on TCP port 502.



```text

HMI-to-PLC-502 exit code: 0

```



Exit code `0` indicates that the TCP connection succeeded.



This confirms that the required Modbus TCP communication path remained available after segmentation.



---



### HMI to PLC — SSH



SSH access from the HMI to the PLC was tested.



```text

HMI-to-PLC-22 exit code: 124

```



Exit code `124` indicates that the connection attempt timed out.



This demonstrates that SSH access from the HMI was blocked by the segmentation policy.



---



### HMI to Historian — PostgreSQL



A connection attempt from the HMI to the Historian on TCP port 5432 was tested.



```text

HMI-to-Historian-5432 exit code: 124

```



The connection timed out, demonstrating that the HMI was not authorized to access the Historian database.



---



### PLC to Historian — Restricted Services



Connection attempts from the PLC to the Historian were tested on SSH and PostgreSQL.



```text

PLC-to-Historian-22

PLC-to-Historian-5432

```



The validation evidence captures the results of these tests.



---



### OT Systems to Monitoring



Connectivity to the centralized monitoring system on TCP port 514 was successfully validated.



```text

PLC-to-Monitor-514 exit code: 0

HMI-to-Monitor-514 exit code: 0

Historian-to-Monitor-514 exit code: 0

```



These successful tests confirm that the required monitoring communication remained operational.



---



## Firewall Counter Validation



The Proxmox firewall was verified as enabled and running:



```text

Status: enabled/running

```



iptables counters were also inspected to confirm that traffic matched the intended firewall rules.



The validation included counters for:



- PLC Modbus TCP traffic

- Historian PostgreSQL traffic

- Monitoring TCP/514 traffic

- SSH management traffic

- Default deny processing



This provided additional validation that the firewall configuration was not merely present, but actively processing traffic.



---



## Evidence



Post-implementation validation evidence is stored in:



```text

evidence/after-validation/

```



The evidence includes connectivity tests:



- `plc-to-historian-22.txt`

- `plc-to-historian-5432.txt`

- `plc-to-monitor-514.txt`

- `hmi-to-plc-22.txt`

- `hmi-to-plc-502.txt`

- `hmi-to-historian-5432.txt`

- `hmi-to-monitor-514.txt`

- `historian-to-monitor-514.txt`



Firewall validation artifacts include:



- `firewall-status.txt`

- `plc-firewall-counters.txt`

- `historian-firewall-counters.txt`

- `monitor-firewall-counters.txt`

- `pvefw-forward-counters.txt`



These artifacts provide reproducible evidence of the post-implementation network segmentation state.



---



## Key Takeaways



This project demonstrates practical application of:



- Network segmentation

- Default-deny firewall architecture

- Least privilege

- Service-specific access controls

- Controlled administrative access

- OT communication path validation

- Security control testing

- Evidence collection

- Before-and-after validation



Rather than simply applying firewall rules, the implementation was tested to confirm that required industrial communication remained functional while unnecessary communication paths were restricted.



---



## Future Improvements



Potential future enhancements include:



- Adding additional PLCs and OT assets

- Introducing an Industrial DMZ

- Implementing centralized logging

- Adding IDS/IPS monitoring

- Integrating Zeek or Suricata

- Capturing network traffic for analysis

- Mapping segmentation controls to NIST SP 800-82 guidance

- Mapping controls to NIST SP 800-53 control families

- Adding alerting through Prometheus and Grafana

- Simulating OT attack scenarios and validating segmentation effectiveness



---



## Technologies Used



- Proxmox VE

- Linux

- Proxmox Firewall

- iptables

- Nmap

- SSH

- TCP connectivity testing

- Modbus TCP

- PostgreSQL

- Kali Linux



---



## Portfolio Context



This project was developed as part of an OT/ICS cybersecurity homelab focused on building practical experience with industrial network architecture, segmentation, monitoring, and cybersecurity controls.



The project emphasizes the ability to design, implement, validate, and document a security control rather than simply deploying technology.



