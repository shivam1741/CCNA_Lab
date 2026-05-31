
# CCNA 200-301 — Day 2 Weak Topics Notes
> Questions wrong: 12, 19, 20, 25, 28, 33, 35, 36, 37, 38, 43, 44, 46, 49
> Score: 36/50 (72%) — Target: 85%+

---

## Q12 — VLSM (Variable Length Subnet Masking)

**Question:** What does VLSM allow network engineers to do?
**Your mistake:** Likely confused with CIDR or route summarization.
**Correct Answer:** Use different subnet masks within the same major network.

### What is VLSM?
VLSM allows you to divide a network into subnets of **different sizes** — instead of giving every subnet the same mask, you tailor each subnet to its actual need.

**Example:**
```
Network: 192.168.1.0/24

Without VLSM: every subnet = /26 (62 hosts each) — wasteful
With VLSM:
  - HQ LAN:     192.168.1.0/25   → 126 hosts
  - Branch LAN: 192.168.1.128/27 → 30 hosts
  - WAN link:   192.168.1.160/30 → 2 hosts (point-to-point)
```

**Key point:** VLSM = efficient IP usage. No waste.

---

## Q19 — STP PortFast

**Question:** What feature does PortFast provide on Cisco switches?
**Your mistake:** May have confused with RSTP or trunk behavior.
**Correct Answer:** Immediate transition to forwarding state on access ports.

### What is PortFast?
Normal STP takes ~30 seconds (Listening → Learning → Forwarding).
PortFast **skips** Listening and Learning — port goes straight to Forwarding.

**Use only on:**
- End device ports (PCs, printers, servers)
- Never on trunk ports or switch-to-switch links!

**Config:**
```
Switch(config-if)# spanning-tree portfast
```

**BPDU Guard** (pair with PortFast):
```
Switch(config-if)# spanning-tree bpduguard enable
```
If a BPDU is received on a PortFast port → port shuts down (err-disabled). Prevents accidental loops.

---

## Q20 — Administrative Distance (AD)

**Question:** What is administrative distance?
**Your mistake:** May have confused with metric/hop count.
**Correct Answer:** Trustworthiness of a routing information source (lower = better).

### AD Table — Memorize This!

| Source | AD |
|---|---|
| Connected | 0 |
| Static | 1 |
| EIGRP summary | 5 |
| eBGP | 20 |
| EIGRP internal | 90 |
| OSPF | 110 |
| IS-IS | 115 |
| RIP | 120 |
| EIGRP external | 170 |
| iBGP | 200 |
| Unknown/unreachable | 255 |

**AD vs Metric:**
- AD = used to choose BETWEEN routing protocols
- Metric = used to choose BETWEEN routes within the SAME protocol

---

## Q25 — DHCP Initial Request (Source Address)

**Question:** Which address does a DHCP client use before it has an IP?
**Your mistake:** May have chosen 255.255.255.255 or 127.0.0.1.
**Correct Answer:** Source = `0.0.0.0`, Destination = `255.255.255.255`

### DHCP DORA Process

```
Client                          Server
  |                               |
  |--- DHCPDISCOVER ------------->|  src: 0.0.0.0  dst: 255.255.255.255
  |<-- DHCPOFFER -----------------|  server offers an IP
  |--- DHCPREQUEST -------------->|  client requests offered IP
  |<-- DHCPACK ------------------|  server confirms

```

**Ports:** Client uses UDP 68, Server uses UDP 67.

---

## Q28 — OSPF DR (Designated Router)

**Question:** What is a DR in OSPF?
**Your mistake:** May have confused with ABR or ASBR.
**Correct Answer:** A router elected on multi-access networks to reduce OSPF adjacencies.

### Why DR/BDR?
On Ethernet (multi-access), without DR every router would form full adjacency with every other router = n(n-1)/2 adjacencies. Too many!

**With DR/BDR:**
- All routers form adjacency ONLY with DR and BDR
- DR/BDR collect and flood LSAs on behalf of the segment

**Election:**
1. Highest OSPF Priority wins (default = 1, range 0–255)
2. Tie → Highest Router ID wins
3. Priority 0 = never becomes DR/BDR

```
Router(config-if)# ip ospf priority 100
```

**OSPF neighbor states:** Down → Init → 2-Way → Exstart → Exchange → Loading → Full

---

## Q33 — Route Summarization

**Question:** What is route summarization?
**Your mistake:** May have confused with filtering or load balancing.
**Correct Answer:** Combining multiple specific routes into one advertisement.

### Example
```
Without summarization:
  192.168.0.0/24
  192.168.1.0/24
  192.168.2.0/24
  192.168.3.0/24

With summarization:
  192.168.0.0/22  ← covers all 4 networks
```

**Benefits:**
- Smaller routing tables
- Less routing update traffic
- Hides topology instability (if a subnet flaps, summary stays stable)

**How to find summary:**
1. Write networks in binary
2. Find the common bits
3. Count common bits = prefix length

---

## Q35 — STP (Purpose)

**Question:** What is the purpose of Spanning Tree Protocol?
**Your mistake:** May have picked VLAN segmentation or routing answer.
**Correct Answer:** Prevent Layer 2 loops in switched networks.

### Why Layer 2 loops are deadly
Without STP, a broadcast frame would loop forever between switches:
- Broadcast storm → consumes all bandwidth
- MAC table instability → switch can't learn MAC locations
- Switch CPU overload → network death

### STP Process
1. Elect Root Bridge (lowest Bridge ID = Priority + MAC)
2. Each non-root switch finds its Root Port (best path to Root)
3. Each segment elects a Designated Port
4. All other ports → Blocking state

**Default priorities:** All switches start at 32768. Lower wins.
```
Switch(config)# spanning-tree vlan 1 priority 4096
```

---

## Q36 — DHCP Protocol

**Question:** Which protocol automatically assigns IP addresses?
**Your mistake:** May have picked ARP or ICMP.
**Correct Answer:** DHCP (Dynamic Host Configuration Protocol)

### What DHCP assigns:
- IP address
- Subnet mask
- Default gateway
- DNS server address
- Lease time

**Ports:** UDP 67 (server), UDP 68 (client)

**Cisco DHCP server config:**
```
ip dhcp pool LAN
  network 192.168.1.0 255.255.255.0
  default-router 192.168.1.1
  dns-server 8.8.8.8
  lease 7

ip dhcp excluded-address 192.168.1.1 192.168.1.10
```

---

## Q37 — ECMP (Equal Cost Multi-Path)

**Question:** What is ECMP?
**Your mistake:** May have confused with failover or route summarization.
**Correct Answer:** Load balancing traffic across multiple paths with equal routing metrics.

### ECMP in action
```
Router has two paths to 10.0.0.0/8:
  Path 1: via 192.168.1.1 — cost 100
  Path 2: via 192.168.2.1 — cost 100

Both appear in routing table → traffic load-balanced across both
```

**OSPF ECMP:** Up to 16 equal-cost paths (default 4)
```
Router(config-router)# maximum-paths 8
```

**Per-flow vs per-packet:**
- Per-flow (default): same session always uses same path (preserves TCP order)
- Per-packet: alternate packet by packet (can cause out-of-order issues)

---

## Q38 — Hub vs Switch

**Question:** Main difference between hub and switch?
**Your mistake:** May have mixed up layers or capabilities.
**Correct Answer:** Hub broadcasts to all ports; switch forwards to specific MAC address.

### Comparison Table

| Feature | Hub | Switch |
|---|---|---|
| OSI Layer | Layer 1 | Layer 2 |
| Forwarding | Broadcasts to all | Unicast to destination MAC |
| Collision domain | One big domain | Per port |
| Intelligence | None | MAC address table |
| Duplex | Half duplex only | Full duplex |
| VLANs | No | Yes |

**Hubs are obsolete** — you will never see one in a modern network. But CCNA exam still tests this concept.

---

## Q43 — VLAN Hopping Attack

**Question:** What is a VLAN hopping attack?
**Your mistake:** May have picked broadcast flooding or DHCP interception.
**Correct Answer:** Accessing traffic on a different VLAN by exploiting trunk port misconfiguration.

### Two methods of VLAN hopping:

**Method 1 — Switch Spoofing:**
Attacker sends DTP frames to trick switch into forming a trunk → attacker can access all VLANs.

**Prevention:**
```
switchport mode access          ← disable DTP
switchport nonegotiate          ← disable DTP negotiation
```

**Method 2 — Double Tagging:**
Attacker adds two 802.1Q tags. Outer tag = native VLAN (stripped by first switch), inner tag = target VLAN.

**Prevention:**
```
switchport trunk native vlan 999  ← change native VLAN to unused VLAN
```

---

## Q44 — Loopback Address

**Question:** What is the IPv4 loopback address?
**Your mistake:** May have picked 0.0.0.0 or 169.254.x.x.
**Correct Answer:** `127.0.0.1` (range: 127.0.0.0/8)

### Special IPv4 Addresses — Must Memorize

| Address | Purpose |
|---|---|
| 0.0.0.0 | Unspecified / DHCP source before IP assigned |
| 127.0.0.1 | Loopback — tests local TCP/IP stack |
| 169.254.x.x | APIPA — auto-assigned when DHCP fails |
| 255.255.255.255 | Limited broadcast (stays in local subnet) |
| x.x.x.255 | Directed broadcast for that subnet |

---

## Q46 — Zero-Day Vulnerability

**Question:** What best describes a zero-day vulnerability?
**Your mistake:** May have confused with a patched vulnerability or timed attack.
**Correct Answer:** A flaw known to attackers but not yet patched by the vendor.

### Security Terms to Know

| Term | Definition |
|---|---|
| Zero-day | Vulnerability with no vendor patch available |
| CVE | Common Vulnerabilities and Exposures — public database |
| Exploit | Code/technique that takes advantage of a vulnerability |
| Patch | Fix released by vendor to close the vulnerability |
| Vulnerability | Weakness in software/hardware |
| Threat | Potential to exploit a vulnerability |
| Risk | Likelihood × Impact |

---

## Q49 — TCP Three-Way Handshake

**Question:** Purpose of TCP three-way handshake?
**Your mistake:** May have picked fragmentation or port assignment.
**Correct Answer:** Establish a reliable connection between two hosts.

### The Handshake

```
Client                    Server
  |                          |
  |-------- SYN ------------>|  "I want to connect, my seq=100"
  |<------- SYN-ACK ---------|  "OK, my seq=200, ack=101"
  |-------- ACK ------------>|  "Got it, ack=201"
  |                          |
  |<====== DATA FLOWS ======>|
```

**Why three steps?**
- SYN: Client announces its sequence number
- SYN-ACK: Server acknowledges + announces its own sequence number
- ACK: Client acknowledges server's sequence number
- Now both sides know each other's starting sequence → reliable ordered delivery

**TCP vs UDP quick comparison:**

| TCP | UDP |
|---|---|
| Connection-oriented | Connectionless |
| Reliable, ordered | Unreliable, no ordering |
| Flow control | No flow control |
| HTTP, HTTPS, FTP, SSH | DNS, DHCP, VoIP, streaming |

---

## Summary — Your Weak Areas

| Topic | Questions Wrong | Action |
|---|---|---|
| Switching (STP, PortFast, VLAN hopping) | Q19, Q35, Q43 | Revise STP states + security |
| IP addressing (VLSM, loopback, DHCP source) | Q12, Q25, Q44 | Practice subnetting daily |
| Routing (AD table, OSPF DR, summarization, ECMP) | Q20, Q28, Q33, Q37 | Memorize AD table |
| Protocols (DHCP, TCP handshake) | Q36, Q49 | Understand DORA + TCP flags |
| Security (VLAN hopping, zero-day) | Q43, Q46 | Read security definitions |
| WAN/General (hub vs switch) | Q38 | Layer comparison revision |

---

## Quick Revision Mnemonics

**OSI Layers (top to bottom):** All People Seem To Need Data Processing
(Application, Presentation, Session, Transport, Network, Data Link, Physical)

**AD values:** "Can Steve Ever Over-power OSPF, IS-IS, RIP?" 
Connected(0) Static(1) EIGRP-summary(5) eBGP(20) OSPF(110) IS-IS(115) RIP(120)

**DHCP DORA:** Discover → Offer → Request → Acknowledge

**TCP flags:** SYN → SYN-ACK → ACK (handshake), FIN → FIN-ACK → ACK (teardown)

**STP port states:** Blocking → Listening → Learning → Forwarding
(Remember: "BL-LF" = "Blocking Loops, Let Frames through")

---

*Generated for CCNA 200-301 exam prep — Day 2 revision*
*Next: Attempt Day 3 paper and aim for 80%+*
