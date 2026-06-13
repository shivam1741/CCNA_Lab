# CCNA 200-301 — Section 3: Switching, VLANs & STP
> Complete Notes | Commands | Scenarios | Practice Questions

---

## 📚 TABLE OF CONTENTS
1. [Ethernet & Switching Basics](#1-ethernet--switching-basics)
2. [VLANs](#2-vlans)
3. [Trunk Links & 802.1Q](#3-trunk-links--8021q)
4. [VTP — VLAN Trunking Protocol](#4-vtp--vlan-trunking-protocol)
5. [Inter-VLAN Routing](#5-inter-vlan-routing)
6. [Spanning Tree Protocol (STP)](#6-spanning-tree-protocol-stp)
7. [Rapid PVST+](#7-rapid-pvst)
8. [EtherChannel](#8-etherchannel)
9. [Port Security](#9-port-security)
10. [Key Commands Cheat Sheet](#10-key-commands-cheat-sheet)
11. [Scenario-Based Questions](#11-scenario-based-questions)

---

## 1. Ethernet & Switching Basics

### How a Switch Works
- Switch operates at **Layer 2 (Data Link)**
- Makes forwarding decisions based on **destination MAC address**
- Maintains a **MAC Address Table** (also called CAM table)
- Each switch **port = its own collision domain**
- All ports in same VLAN = **same broadcast domain**

### MAC Address Table Learning Process
1. Frame arrives on a port
2. Switch records **source MAC + port** in MAC table
3. Checks destination MAC:
   - **Known** → forwards only to that port (unicast)
   - **Unknown** → floods to all ports except source (unknown unicast flood)
   - **Broadcast** → floods to all ports in same VLAN

### Switch vs Hub vs Router
| Device | Layer | Collision Domain | Broadcast Domain |
|--------|-------|-----------------|-----------------|
| Hub | L1 | All ports share 1 | All ports share 1 |
| Switch | L2 | 1 per port | 1 per VLAN |
| Router | L3 | 1 per port | 1 per interface |

### Key Commands — Basic Switch
```
show mac address-table              → View MAC address table
show mac address-table dynamic      → Only dynamic entries
show interfaces                     → Interface stats
show interfaces FastEthernet 0/1    → Specific interface
show ip interface brief             → All interfaces summary
show version                        → IOS version, uptime
```

---

## 2. VLANs

### What is a VLAN?
- **Virtual LAN** — logically segments a switch into multiple broadcast domains
- Devices in different VLANs **cannot communicate** without a router or Layer 3 switch
- VLANs improve **security, performance, and manageability**

### VLAN Ranges
| Range | Type | Description |
|-------|------|-------------|
| VLAN 1 | Default | Default VLAN, cannot be deleted |
| 2–1001 | Normal | User-created VLANs |
| 1002–1005 | Reserved | Legacy (FDDI, Token Ring) |
| 1006–4094 | Extended | Requires VTP transparent/off mode |

### VLAN Types
- **Data VLAN** — carries user traffic
- **Voice VLAN** — carries VoIP traffic (marked with CoS 5)
- **Management VLAN** — used for switch management (SSH/Telnet)
- **Native VLAN** — untagged VLAN on trunk link (default: VLAN 1)

### Access Port Configuration
```
SW1(config)# interface FastEthernet 0/1
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
SW1(config-if)# switchport nonegotiate        → Disable DTP
SW1(config-if)# spanning-tree portfast        → Enable PortFast
SW1(config-if)# no shutdown
```

### Voice VLAN Configuration
```
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10     → Data VLAN
SW1(config-if)# switchport voice vlan 20      → Voice VLAN
```

### VLAN Creation & Verification
```
SW1(config)# vlan 10
SW1(config-vlan)# name SALES
SW1(config-vlan)# exit

SW1(config)# vlan 20
SW1(config-vlan)# name HR

show vlan brief                   → All VLANs and assigned ports
show vlan id 10                   → Specific VLAN detail
show interfaces FastEthernet0/1 switchport  → Port VLAN info
```

### ⚠️ Important VLAN Facts for Exam
- VLAN 1 is the **default native VLAN** — best practice to change it
- Cisco recommends **not using VLAN 1** for any traffic
- VLANs **must exist** on a switch before being assigned to a port
- Deleting a VLAN doesn't remove the port assignment — those ports become **inactive**

---

## 3. Trunk Links & 802.1Q

### What is a Trunk?
- A trunk link carries traffic for **multiple VLANs** between switches, routers, or servers
- Uses **IEEE 802.1Q** encapsulation to tag frames with a 4-byte VLAN header
- The **native VLAN** is sent **untagged** on trunk links

### 802.1Q Tag Structure
```
| Destination MAC | Source MAC | 802.1Q Tag (4 bytes) | EtherType | Data | FCS |
                                  ↓
                  | TPID (0x8100) | PCP | DEI | VLAN ID (12 bits) |
                  VLAN ID supports 0–4094 (0 and 4095 reserved)
```

### Trunk Configuration
```
SW1(config)# interface GigabitEthernet 0/1
SW1(config-if)# switchport trunk encapsulation dot1q   → (on older switches)
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk native vlan 99        → Change native VLAN
SW1(config-if)# switchport trunk allowed vlan 10,20,30 → Restrict allowed VLANs
SW1(config-if)# switchport nonegotiate                 → Disable DTP
```

### Trunk Verification
```
show interfaces trunk                      → All trunk ports info
show interfaces GigabitEthernet0/1 trunk  → Specific trunk
show interfaces switchport                 → Detailed switchport info
```

### DTP — Dynamic Trunking Protocol
- Cisco proprietary — automatically negotiates trunk links
- **Security risk** — disable on all access ports with `switchport nonegotiate`

| DTP Mode | Behavior |
|----------|----------|
| dynamic auto | Becomes trunk only if other side initiates |
| dynamic desirable | Actively tries to become trunk |
| trunk | Always trunk |
| access | Always access port |
| nonegotiate | Disables DTP |

### Native VLAN Mismatch — Security Risk
- If both ends of a trunk have **different native VLANs** → STP errors + potential **VLAN hopping**
- Always match native VLAN on both ends
- Best practice: use an **unused VLAN** as native VLAN (e.g., VLAN 99)

---

## 4. VTP — VLAN Trunking Protocol

### What is VTP?
- Cisco proprietary protocol
- Propagates VLAN configuration across switches via trunk links
- Switches must be in same **VTP domain** to share VLAN info

### VTP Modes
| Mode | Create VLANs | Sync DB | Forward Updates |
|------|-------------|---------|----------------|
| Server | ✅ Yes | ✅ Yes | ✅ Yes |
| Client | ❌ No | ✅ Yes | ✅ Yes |
| Transparent | ✅ Yes (local only) | ❌ No | ✅ Yes |
| Off | ✅ Yes (local only) | ❌ No | ❌ No |

### VTP Configuration
```
SW1(config)# vtp mode server
SW1(config)# vtp domain CCNA-LAB
SW1(config)# vtp password cisco123
SW1(config)# vtp version 2

show vtp status       → VTP mode, domain, revision number
show vtp counters     → VTP advertisement stats
```

### ⚠️ VTP Danger — Revision Number
- A switch with **higher VTP revision number** can wipe out all VLANs on the network if added
- Always **reset revision number** before adding a new switch (change domain name or set to transparent)

---

## 5. Inter-VLAN Routing

### Why Needed?
- Devices in different VLANs are in different subnets
- Need a **Layer 3 device** to route between them

### Method 1: Router-on-a-Stick (ROAS)
- One physical link from switch to router configured as trunk
- Router creates **subinterfaces** — one per VLAN

```
Router Configuration:
R1(config)# interface GigabitEthernet 0/0.10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip address 192.168.10.1 255.255.255.0

R1(config)# interface GigabitEthernet 0/0.20
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip address 192.168.20.1 255.255.255.0

R1(config)# interface GigabitEthernet 0/0.99
R1(config-subif)# encapsulation dot1Q 99 native   → Native VLAN subinterface

Switch — connect port to router as trunk:
SW1(config)# interface GigabitEthernet 0/1
SW1(config-if)# switchport mode trunk
```

### Method 2: Layer 3 Switch with SVIs
- More scalable, higher performance than ROAS
- Switch routes traffic internally — no need for external router

```
SW1(config)# ip routing                          → Enable L3 routing

SW1(config)# interface vlan 10
SW1(config-if)# ip address 192.168.10.1 255.255.255.0
SW1(config-if)# no shutdown

SW1(config)# interface vlan 20
SW1(config-if)# ip address 192.168.20.1 255.255.255.0
SW1(config-if)# no shutdown

show ip route        → Verify routing table on L3 switch
```

### ROAS vs Layer 3 Switch
| Feature | ROAS | L3 Switch |
|---------|------|-----------|
| Cost | Lower | Higher |
| Performance | Limited by uplink | Wire-speed routing |
| Scalability | Poor (single link) | Excellent |
| Config complexity | Medium | Medium |

---

## 6. Spanning Tree Protocol (STP)

### Why STP?
- Redundant links = potential **Layer 2 loops**
- Loops cause: **broadcast storms, MAC table instability, multiple frame copies**
- STP blocks redundant paths — keeps one active path, others in standby

### STP Standards
| Standard | Name | Notes |
|----------|------|-------|
| 802.1D | Original STP | Slow convergence (30–50 sec) |
| PVST+ | Cisco per-VLAN STP | One STP instance per VLAN |
| 802.1w | Rapid STP (RSTP) | Fast convergence |
| Rapid PVST+ | Cisco Rapid per-VLAN | Default on modern Cisco switches |
| 802.1s | MSTP | Maps multiple VLANs to fewer instances |

### STP Port Roles
| Role | Description |
|------|-------------|
| Root Port | Best path toward Root Bridge (one per non-root switch) |
| Designated Port | Forwarding port on each segment (one per segment) |
| Alternate Port | Blocked backup path (RSTP) |
| Backup Port | Redundant port on same segment (RSTP) |
| Disabled | Admin shutdown |

### STP Port States (802.1D)
| State | Forward Frames | Learn MACs | Duration |
|-------|---------------|-----------|---------|
| Blocking | ❌ | ❌ | 20 sec (Max Age) |
| Listening | ❌ | ❌ | 15 sec (Forward Delay) |
| Learning | ❌ | ✅ | 15 sec (Forward Delay) |
| Forwarding | ✅ | ✅ | Indefinite |
| Disabled | ❌ | ❌ | Admin down |

> Total convergence time: **30–50 seconds** in 802.1D

### Root Bridge Election
1. All switches start claiming to be Root Bridge
2. Exchange **BPDUs (Bridge Protocol Data Units)**
3. Switch with **lowest Bridge ID** wins
4. **Bridge ID = Priority (2 bytes) + MAC Address (6 bytes)**
5. Default priority = **32768** (+ VLAN ID in PVST+)
6. Tie on priority → **lowest MAC address** wins

### Root Bridge Configuration (Manual)
```
SW1(config)# spanning-tree vlan 10 priority 4096       → Set manually
SW1(config)# spanning-tree vlan 10 root primary        → Auto sets to lowest
SW1(config)# spanning-tree vlan 10 root secondary      → Sets to 28672
```

### Path Cost (for Root Port election)
| Link Speed | STP Cost |
|-----------|----------|
| 10 Mbps | 100 |
| 100 Mbps | 19 |
| 1 Gbps | 4 |
| 10 Gbps | 2 |

### STP Verification Commands
```
show spanning-tree                     → All VLANs STP info
show spanning-tree vlan 10             → VLAN-specific STP
show spanning-tree summary             → STP mode and counts
show spanning-tree interface Gi0/1     → Per-interface STP detail
```

### PortFast & BPDU Guard
```
PortFast — Skip Listening & Learning, go directly to Forwarding
(Use ONLY on access ports connected to end devices — never on switch uplinks)

SW1(config-if)# spanning-tree portfast          → On specific port
SW1(config)# spanning-tree portfast default     → Enable on all access ports globally

BPDU Guard — Shuts port if BPDU received (protects against rogue switches)
SW1(config-if)# spanning-tree bpduguard enable  → On specific port
SW1(config)# spanning-tree portfast bpduguard default → Globally on PortFast ports
```

### BPDU Filter
```
SW1(config-if)# spanning-tree bpdufilter enable  → Stops sending/receiving BPDUs
⚠️ Use carefully — can create loops if misconfigured
```

### Root Guard
```
SW1(config-if)# spanning-tree guard root   → Prevents port from becoming root port
Use on ports facing downstream switches to protect root bridge placement
```

---

## 7. Rapid PVST+

### Key Improvements over 802.1D
- Convergence in **1–2 seconds** (vs 30–50 sec)
- New port roles: **Alternate** and **Backup**
- New port states: only **Discarding, Learning, Forwarding** (3 states vs 5)
- Uses **proposal/agreement** handshake instead of waiting for timers

### RSTP Port States
| RSTP State | 802.1D Equivalent |
|-----------|-------------------|
| Discarding | Blocking + Listening |
| Learning | Learning |
| Forwarding | Forwarding |

### RSTP Port Types
- **Edge port** = PortFast port (connected to end device)
- **Point-to-point** = Full-duplex link to another switch
- **Shared** = Half-duplex link (rare today)

### Configure Rapid PVST+
```
SW1(config)# spanning-tree mode rapid-pvst    → Enable Rapid PVST+

show spanning-tree | include IEEE             → Verify mode
```

---

## 8. EtherChannel

### What is EtherChannel?
- Bundles **2–8 physical links** into one logical link
- Provides **higher bandwidth + redundancy**
- STP sees it as a **single link** — no blocking
- Load balances traffic across member links

### EtherChannel Protocols
| Protocol | Type | Notes |
|----------|------|-------|
| LACP | IEEE 802.3ad (Open Standard) | Preferred, works with any vendor |
| PAgP | Cisco Proprietary | Cisco-only environments |
| Static (on) | Manual | No negotiation |

### LACP Modes
| Mode | Behavior |
|------|----------|
| active | Actively sends LACP packets |
| passive | Responds to LACP, doesn't initiate |
> active + active ✅ | active + passive ✅ | passive + passive ❌

### PAgP Modes
| Mode | Behavior |
|------|----------|
| desirable | Actively sends PAgP packets |
| auto | Responds to PAgP, doesn't initiate |
> desirable + desirable ✅ | desirable + auto ✅ | auto + auto ❌

### EtherChannel Configuration (LACP)
```
SW1(config)# interface range GigabitEthernet 0/1 - 2
SW1(config-if-range)# channel-group 1 mode active      → LACP active
SW1(config-if-range)# exit

SW1(config)# interface port-channel 1
SW1(config-if)# switchport mode trunk                  → Configure logical interface
SW1(config-if)# switchport trunk allowed vlan 10,20,30
```

### EtherChannel Configuration (Static)
```
SW1(config)# interface range GigabitEthernet 0/1 - 2
SW1(config-if-range)# channel-group 1 mode on
```

### EtherChannel Verification
```
show etherchannel summary          → Status of all EtherChannels
show etherchannel 1 detail         → Detailed EtherChannel 1 info
show interfaces port-channel 1     → Logical interface stats
show lacp neighbor                 → LACP neighbor info
show pagp neighbor                 → PAgP neighbor info
```

### ⚠️ EtherChannel Requirements
- All member ports must have **same speed, duplex, VLAN config**
- If any mismatch exists → EtherChannel fails
- STP treats port-channel as single interface

### EtherChannel Load Balancing
```
show etherchannel load-balance     → View current method
SW1(config)# port-channel load-balance src-dst-mac  → Change method
```
Methods: src-mac, dst-mac, src-dst-mac, src-ip, dst-ip, src-dst-ip

---

## 9. Port Security

### What is Port Security?
- Restricts which MAC addresses can use a switchport
- Prevents **MAC flooding attacks** and **unauthorized devices**

### Port Security Configuration
```
SW1(config)# interface FastEthernet 0/1
SW1(config-if)# switchport mode access               → Must be access port
SW1(config-if)# switchport port-security             → Enable port security
SW1(config-if)# switchport port-security maximum 2   → Max 2 MACs allowed
SW1(config-if)# switchport port-security mac-address 0011.2233.4455  → Static MAC
SW1(config-if)# switchport port-security mac-address sticky          → Sticky learning
SW1(config-if)# switchport port-security violation restrict          → Violation mode
```

### Violation Modes
| Mode | Drops Frames | Sends Syslog | Increments Counter | Shuts Port |
|------|-------------|-------------|-------------------|-----------|
| protect | ✅ | ❌ | ❌ | ❌ |
| restrict | ✅ | ✅ | ✅ | ❌ |
| shutdown | ✅ | ✅ | ✅ | ✅ (err-disabled) |

> Default violation mode = **shutdown**

### Recover from err-disabled
```
SW1(config-if)# shutdown
SW1(config-if)# no shutdown

OR auto-recovery:
SW1(config)# errdisable recovery cause psecure-violation
SW1(config)# errdisable recovery interval 300    → 300 seconds
```

### Port Security Verification
```
show port-security                        → Summary of all ports
show port-security interface Fa0/1        → Specific port detail
show port-security address                → Secure MAC addresses
```

---

## 10. Key Commands Cheat Sheet

### VLAN Commands
```
vlan 10                                  Create VLAN
name SALES                               Name VLAN
show vlan brief                          View all VLANs
switchport mode access                   Set access mode
switchport access vlan 10                Assign VLAN
switchport voice vlan 20                 Assign voice VLAN
```

### Trunk Commands
```
switchport mode trunk                    Set trunk mode
switchport trunk encapsulation dot1q     Set encapsulation
switchport trunk native vlan 99          Set native VLAN
switchport trunk allowed vlan 10,20,30   Restrict VLANs
switchport nonegotiate                   Disable DTP
show interfaces trunk                    Verify trunks
```

### STP Commands
```
show spanning-tree                       STP topology
show spanning-tree vlan 10               Per-VLAN STP
spanning-tree mode rapid-pvst            Enable RSTP
spanning-tree vlan 10 root primary       Set root bridge
spanning-tree portfast                   Enable PortFast
spanning-tree bpduguard enable           Enable BPDU Guard
```

### EtherChannel Commands
```
channel-group 1 mode active              LACP active
channel-group 1 mode passive             LACP passive
channel-group 1 mode desirable           PAgP desirable
channel-group 1 mode on                  Static
show etherchannel summary                Verify EtherChannel
```

### Inter-VLAN Routing Commands
```
ip routing                               Enable routing on L3 switch
interface vlan 10                        Create SVI
ip address 192.168.10.1 255.255.255.0    Assign IP to SVI
encapsulation dot1Q 10                   ROAS subinterface
show ip route                            Verify routing table
```

### Port Security Commands
```
switchport port-security                 Enable
switchport port-security maximum 2       Set max MACs
switchport port-security mac-address sticky  Sticky learning
switchport port-security violation shutdown  Set violation mode
show port-security interface Fa0/1       Verify
errdisable recovery cause psecure-violation  Auto recovery
```

---

## 11. Scenario-Based Questions

### 🔴 Scenario 1 — VLAN Connectivity Issue
**Situation:** PC-A (VLAN 10) and PC-B (VLAN 10) are connected to the same switch but cannot ping each other.

**Possible causes & verification:**
```
1. PC-A's port not in VLAN 10 → show vlan brief
2. VLAN 10 not created → show vlan brief
3. Port in wrong mode → show interfaces Fa0/1 switchport
4. Port shutdown → show ip interface brief
```
**Fix:** `switchport mode access` + `switchport access vlan 10` + `no shutdown`

---

### 🔴 Scenario 2 — Trunk Not Passing VLAN Traffic
**Situation:** VLAN 20 traffic is not crossing the trunk between SW1 and SW2.

**Verification:**
```
show interfaces trunk   → Check if VLAN 20 is in "VLANs allowed and active"
```
**Fix:** `switchport trunk allowed vlan add 20`

---

### 🔴 Scenario 3 — Root Bridge in Wrong Location
**Situation:** Access layer switch became Root Bridge instead of core switch.

**Why:** Access switch had lower MAC address and same default priority (32768)

**Fix on core switch:**
```
SW-CORE(config)# spanning-tree vlan 10 root primary
OR
SW-CORE(config)# spanning-tree vlan 10 priority 4096
```
**Protect with Root Guard on access-facing ports:**
```
SW-CORE(config-if)# spanning-tree guard root
```

---

### 🔴 Scenario 4 — EtherChannel Not Forming
**Situation:** Two switches have Gi0/1 and Gi0/2 in a channel-group but EtherChannel shows as down.

**Common causes:**
- Mismatched modes (passive + passive = ❌)
- Different speed/duplex on member ports
- Different VLAN configuration on member ports
- Mismatched trunk settings

**Verification:**
```
show etherchannel summary
show interfaces Gi0/1 switchport
show interfaces Gi0/2 switchport
```

---

### 🔴 Scenario 5 — Inter-VLAN Routing Not Working
**Situation:** PC in VLAN 10 cannot reach PC in VLAN 20. Router-on-a-Stick configured.

**Check:**
```
1. Subinterface encapsulation correct?
   → show interfaces Gi0/0.10
2. Switch trunk to router passing both VLANs?
   → show interfaces trunk
3. PC default gateway pointing to subinterface IP?
4. ip routing enabled? (L3 switch only)
```

---

### 🔴 Scenario 6 — Port Security Violation
**Situation:** Switch port Fa0/1 shows err-disabled. User cannot connect.

**Cause:** Port security violation (unauthorized MAC or exceeded max MACs)

**Check:** `show port-security interface Fa0/1`

**Fix:**
```
SW1(config-if)# shutdown
SW1(config-if)# no shutdown
```
Or update the allowed MAC and re-enable.

---

### 🔴 Scenario 7 — Native VLAN Mismatch
**Situation:** CDP shows "Native VLAN mismatch" error on a trunk link.

**Impact:** STP issues, possible VLAN hopping vulnerability

**Fix:** Make sure both ends match:
```
SW1(config-if)# switchport trunk native vlan 99
SW2(config-if)# switchport trunk native vlan 99   → Must match!
```

---

### 🔴 Scenario 8 — New Switch Wiped VLANs (VTP)
**Situation:** A new switch was added to the network and all VLANs disappeared.

**Cause:** New switch had higher VTP revision number and overwrote the VLAN database.

**Prevention:**
- Always reset VTP revision before adding a switch (set to transparent mode, then back)
- Use VTP version 3 (supports primary server protection)
- Use VTP transparent or off mode to avoid this risk

---

## ⚡ Quick Memory Tips

| Concept | Remember |
|---------|---------|
| Root Bridge | Lowest Bridge ID (priority + MAC) |
| Root Port | Best path TO root (one per switch) |
| Designated Port | Best path FROM segment (one per segment) |
| PortFast | Only on **access** ports — never uplinks |
| Native VLAN | Sent **untagged** on trunk |
| VLAN 1 | Default VLAN — best practice not to use |
| LACP active+passive | ✅ works | passive+passive ❌ |
| Port security default | Shutdown (err-disable) on violation |
| VTP Client | Can't create VLANs locally |
| STP convergence | 802.1D = 30–50 sec, RSTP = 1–2 sec |

---

*CCNA 200-301 Section 3 Notes | Generated for exam prep | Shivam*
