# Proxmox Networking: Bridge Configuration & Management IP Placement

**Document:** Troubleshooting & Lessons Learned  
**Affected System:** Proxmox VE 8.x, Ubuntu 24.04 VMs  
**Last Updated:** 2026-07-27

---

## Overview

This document covers two critical networking issues encountered during Proxmox OT homelab setup and their resolutions:

1. **Bridge Configuration:** Management IP placement and why it matters
2. **ARP Conflicts:** How stale ARP entries can mimic routing failures

Both issues presented similar symptoms (unreliable connectivity) but required different solutions. Understanding the distinction is essential for systematic OT network troubleshooting.

---

## Problem #1: Unreliable Management Connectivity

### Symptoms

- SSH connections to Proxmox host (192.168.1.250) would hang intermittently
- Some sessions succeeded immediately; others hung for 30+ seconds before connecting
- Ping worked, but SSH was unreliable
- Restarting networking (`systemctl restart networking`) temporarily fixed the issue
- Problem recurred after several hours

### Initial Investigation

Assumed the problem was routing-related or a firewall issue. Checked:
- Proxmox firewall rules (disabled to rule them out)
- Routing table (`ip route show`)
- Network interface status (`ip link show`)

All appeared normal. The issue persisted.

### Root Cause: Incorrect Bridge Configuration

**The Problem:**
Management IP was assigned directly to the physical NIC instead of to the Linux bridge.

**Incorrect Configuration (`/etc/network/interfaces`):**
```
auto nic0
iface nic0 inet static
    address 192.168.1.250/24
    gateway 192.168.1.1
    # Bridge configuration missing or incorrect
```

**Why This Fails:**
- The physical NIC handles Layer 2 (MAC) forwarding to the bridge members
- If the NIC owns the management IP, traffic destined for 192.168.1.250 may bypass the bridge logic
- When the kernel processes bridging operations, management traffic gets caught in the middle, causing delays or failures
- This becomes especially problematic under load or when bridge members are actively forwarding traffic

### Solution: Assign IP to the Bridge

**Correct Configuration (`/etc/network/interfaces`):**
```
# Physical NIC — carries bridge traffic, no IP
auto nic0
iface nic0 inet manual
    # No IP assignment
    # NIC is enslaved to the bridge below

# Linux Bridge — owns the management IP
auto vmbr0
iface vmbr0 inet static
    address 192.168.1.250/24
    gateway 192.168.1.1
    bridge-ports nic0
    # This bridge contains:
    # - nic0 (uplink to physical network)
    # - VM network interfaces (tap devices added automatically by Proxmox)
```

**Why This Works:**
- The bridge is the logical aggregation point for all network traffic
- By owning the management IP, the bridge ensures consistent, priority handling of management traffic
- VM traffic and management traffic both flow through the same logical interface, avoiding race conditions
- Proxmox can reliably communicate with VMs and external networks

### Implementation Steps

1. SSH into Proxmox host
2. Edit `/etc/network/interfaces`:
   ```bash
   vi /etc/network/interfaces
   ```
3. Remove any IP assignment from the physical NIC (`nic0`)
4. Ensure the bridge section looks like the "Correct Configuration" above
5. Apply changes without rebooting:
   ```bash
   systemctl restart networking
   ```
6. Verify connectivity:
   ```bash
   ping 8.8.8.8
   ssh root@192.168.1.250  # From a different host
   ```

### Verification

After fixing the configuration:
- SSH connections are immediate and reliable
- No more hanging or delayed connections
- Networking survives extended uptime without issues
- Restarting networking is no longer necessary

---

## Problem #2: ARP Conflicts & MAC Address Table Confusion

### Symptoms

- Windows intermittently connected to the "wrong" device on the network
- Ping to 192.168.1.250 (Proxmox) sometimes worked, sometimes didn't
- The failure was random—not consistently reproducible
- Restarting Windows networking fixed the issue temporarily
- Problem recurred unpredictably

### Why This Looks Like a Routing Problem

When ARP (Address Resolution Protocol) entries are stale or conflicting:
- Windows thinks 192.168.1.250 belongs to MAC address `AA:BB:CC:DD:EE:00` (the old device)
- It sends traffic to that MAC address
- The old device doesn't know what to do with the traffic (or sends it to the wrong place)
- From the user's perspective, "I can't reach 192.168.1.250" — looks like a routing failure

But the actual problem is Layer 2 (MAC address resolution), not Layer 3 (routing).

### Investigation

Checked ARP tables on Windows:
```powershell
arp -a
```

Output showed:
```
Interface: 192.168.1.xxx on Interface 0x...
  Internet Address      Physical Address      Type
  192.168.1.250         aa-bb-cc-dd-ee-00     static  ← OLD/STALE
  192.168.1.250         11-22-33-44-55-66     dynamic ← CORRECT (Proxmox)
```

**Two different MAC addresses for the same IP address** — this is the ARP conflict.

### Root Cause

A previous device on the network had IP 192.168.1.250 with MAC `aa-bb-cc-dd-ee-00`. After that device was removed, Windows' ARP cache retained the old entry. When a new device (Proxmox) took over 192.168.1.250 with a different MAC address, the conflict persisted.

### Resolution

**Option 1: Clear ARP Cache (Immediate Fix)**
```powershell
arp -d *  # Clear all ARP entries
```

Windows will re-learn the correct MAC address the next time it pings 192.168.1.250.

**Option 2: Manual ARP Entry (If Necessary)**
```powershell
arp -s 192.168.1.250 11-22-33-44-55-66
```

Replace `11-22-33-44-55-66` with the actual MAC address of the Proxmox host.

**Option 3: Verify No Duplicate IPs Exist**
```bash
# On Proxmox
ip addr show
# Should show unique IPs only

# Scan the network for duplicates
nmap -sn 192.168.1.0/24 | grep "192.168.1.250"
# Should show only one result
```

### Key Distinction: Layer 2 vs. Layer 3

| Layer | Problem | Symptom | Fix |
|-------|---------|---------|-----|
| **Layer 2 (ARP/MAC)** | Duplicate IPs, stale ARP | Intermittent connectivity, wrong device reached | Clear ARP cache, verify no duplicates |
| **Layer 3 (Routing)** | Missing route, bad gateway | No connectivity to subnet, timeout | Add route, verify gateway reachability |

An ARP problem looks like a routing problem because the packet never reaches the correct destination. But the cause is Layer 2, not Layer 3.

---

## Additional Configuration: IPv4 Forwarding

To allow inter-zone communication (e.g., HMI on 192.168.20.0/24 reaching PLC on 192.168.10.0/24 through Proxmox), enable IPv4 forwarding:

### Persistent Configuration

Edit `/etc/sysctl.conf`:
```
net.ipv4.ip_forward=1
```

Apply immediately:
```bash
sysctl -p
```

Verify:
```bash
cat /proc/sys/net/ipv4/ip_forward
# Output: 1 (means forwarding is enabled)
```

### Why This Matters in OT Networks

- **Without forwarding:** VMs can reach Proxmox but not each other across zones
- **With forwarding:** Proxmox acts as a central routing hub, allowing full mesh connectivity (with firewall rules controlling what's allowed)
- **Security implication:** Forwarding should be enabled, but inter-zone communication should be controlled by firewall rules, not by disabling routing entirely

---

## Windows Static Routes to OT Subnets

Windows must be told explicitly how to reach non-adjacent subnets. This is handled via static routes:

### Add Persistent Routes

```powershell
route add 192.168.10.0 mask 255.255.255.0 192.168.1.250 -p
route add 192.168.20.0 mask 255.255.255.0 192.168.1.250 -p
route add 192.168.30.0 mask 255.255.255.0 192.168.1.250 -p
route add 192.168.40.0 mask 255.255.255.0 192.168.1.250 -p
```

### Verify Routes

```powershell
route print
```

Should show:
```
Active Routes:
Destination        Netmask           Gateway        Interface
192.168.10.0       255.255.255.0     192.168.1.250  192.168.1.xxx
192.168.20.0       255.255.255.0     192.168.1.250  192.168.1.xxx
(and so on)
```

### Test Connectivity

```powershell
ping 192.168.10.100  # PLC VM
ping 192.168.20.100  # HMI VM
```

---

## Lessons Learned

### 1. Bridge IP Placement is Non-Negotiable

Management IP must live on the bridge, not the physical NIC. This is not a preference—it's required for reliable operation in a virtualized environment with multiple bridges.

### 2. Always Test Each Network Layer Independently

When connectivity is unreliable:
1. Start at Layer 1 (physical interfaces, bridge configuration)
2. Move to Layer 2 (ARP, MAC tables)
3. Only then test Layer 3 (routing)

A symptom at one layer often indicates a problem at a lower layer.

### 3. Intermittent Connectivity Usually Means Layer 2

Layer 3 problems are binary—either the route exists or it doesn't. Intermittent failures almost always point to Layer 2 issues (ARP, MAC address learning, spanning tree).

### 4. Validate Assumptions Before Troubleshooting

Before assuming a problem is routing-related:
- Verify bridge configuration is correct
- Check ARP tables for stale entries
- Confirm no duplicate IPs exist on the network

Most "network" problems are actually configuration issues, not failures of the network stack itself.

### 5. Proxmox Networking Docs Are Not Obvious

Proxmox' default documentation doesn't explicitly state where the management IP should live. This is well-known in the Proxmox community, but not obvious from first principles. Document this assumption in your own labs to avoid teaching others the hard way.

---

## Checklist: Proxmox Networking Validation

- [ ] Physical NIC configured with `inet manual` (no IP)
- [ ] Linux bridge (`vmbr0`) configured with static IP
- [ ] Bridge includes correct `bridge-ports` (usually the physical NIC)
- [ ] IPv4 forwarding enabled: `cat /proc/sys/net/ipv4/ip_forward` = 1
- [ ] All isolated bridges (`vmbr10`, `vmbr20`, etc.) created and verified
- [ ] Windows static routes added with `-p` flag for persistence
- [ ] Ping test from Windows to each OT subnet succeeds
- [ ] SSH from Windows to Proxmox succeeds immediately (no hangs)
- [ ] ARP table checked for duplicate entries: `arp -a`
- [ ] No IP address duplicates on the network: `nmap -sn <subnet>`

---

## References

- **Proxmox VE Documentation:** Network Configuration
- **Linux Bridge Man Pages:** `man bridge`, `man brctl`
- **ARP Protocol:** RFC 826
- **IPv4 Forwarding:** `man sysctl`, `/etc/sysctl.conf`

---

**Status:** ✅ Resolved  
**Incident Date:** 2026-07-21 to 2026-07-27  
**Resolution Date:** 2026-07-27  
**Recurrence:** None (stable for 6+ days)
