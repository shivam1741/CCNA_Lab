# CCNA 200-301 — Section 2: IP Addressing & Subnetting
> Complete notes with examples, scenarios, and exam tips
> Topics: IPv4 Classes, Private IPs, Subnetting, VLSM, NAT/PAT, IPv6, Special Addresses

---

## 1. IPv4 Address Structure

An IPv4 address is **32 bits** written as 4 octets in dotted decimal:
```
192  .  168  .   1  .  10
11000000.10101000.00000001.00001010
```

- Each octet = 8 bits (0–255)
- Total addresses = 2^32 = ~4.3 billion

---

## 2. IPv4 Address Classes

| Class | First Octet Range | Default Mask | Network/Host bits | Use |
|---|---|---|---|---|
| A | 1–126 | /8 (255.0.0.0) | 8 network / 24 host | Large networks |
| B | 128–191 | /16 (255.255.0.0) | 16 network / 16 host | Medium networks |
| C | 192–223 | /24 (255.255.255.0) | 24 network / 8 host | Small networks |
| D | 224–239 | N/A | N/A | Multicast |
| E | 240–255 | N/A | N/A | Experimental/Reserved |

> Note: 127.x.x.x is reserved for loopback — NOT Class A usable range!

---

## 3. RFC 1918 Private IP Ranges — Memorise!

| Class | Private Range | CIDR | Usable Hosts |
|---|---|---|---|
| A | 10.0.0.0 – 10.255.255.255 | 10.0.0.0/8 | ~16 million |
| B | 172.16.0.0 – 172.31.255.255 | 172.16.0.0/12 | ~1 million |
| C | 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 | ~65,000 |

Private IPs are NOT routable on the internet — NAT translates them to public IPs.

---

## 4. Special IPv4 Addresses

| Address | Purpose |
|---|---|
| 0.0.0.0 | Unspecified — DHCP source before IP assigned |
| 127.0.0.1 | Loopback — tests local TCP/IP stack |
| 169.254.0.0/16 | APIPA — auto-assigned when DHCP fails |
| 255.255.255.255 | Limited broadcast — stays in local subnet |
| x.x.x.0 | Network address — cannot assign to host |
| x.x.x.255 (/24) | Directed broadcast — cannot assign to host |

---

## 5. Subnet Mask & CIDR

A subnet mask defines which bits are **network bits** (1s) and which are **host bits** (0s).

### Subnet Mask Quick Reference Table — Memorise!

| CIDR | Subnet Mask | Block Size | Subnets from /24 | Usable Hosts |
|---|---|---|---|---|
| /24 | 255.255.255.0 | 256 | 1 | 254 |
| /25 | 255.255.255.128 | 128 | 2 | 126 |
| /26 | 255.255.255.192 | 64 | 4 | 62 |
| /27 | 255.255.255.224 | 32 | 8 | 30 |
| /28 | 255.255.255.240 | 16 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 32 | 6 |
| /30 | 255.255.255.252 | 4 | 64 | 2 |
| /31 | 255.255.255.254 | 2 | 128 | 0* |
| /32 | 255.255.255.255 | 1 | 256 | 1* |

> /31 = 2 addresses, RFC 3021 allows for point-to-point (no network/broadcast)
> /32 = single host route

### Key Formulas:
```
Block size       = 256 - last non-255 octet of subnet mask
Usable hosts     = 2^(host bits) - 2
Number of subnets = 2^(subnet bits borrowed)
```

---

## 6. Subnetting — The Block Size Method

### Step-by-step process:

**Example: Host IP = 192.168.10.200/28**

```
Step 1: Find block size
        /28 mask = 255.255.255.240
        Block size = 256 - 240 = 16

Step 2: List subnet boundaries (multiples of block size)
        .0, .16, .32, .48, .64, .80, .96, .112,
        .128, .144, .160, .176, .192, .208, .224, .240

Step 3: Find which subnet .200 falls in
        .192 ≤ .200 < .208
        → Subnet = 192.168.10.192/28

Step 4: Results
        Network address  = 192.168.10.192
        Broadcast address = 192.168.10.207  (next subnet - 1)
        Valid host range  = 192.168.10.193 – 192.168.10.206
        Usable hosts      = 14
```

### More Examples:

| Host IP | Mask | Network | Broadcast | Host Range |
|---|---|---|---|---|
| 10.0.0.65 | /26 | 10.0.0.64 | 10.0.0.127 | .65–.126 |
| 172.16.5.200 | /25 | 172.16.5.128 | 172.16.5.255 | .129–.254 |
| 192.168.1.130 | /27 | 192.168.1.128 | 192.168.1.159 | .129–.158 |
| 10.1.1.50 | /30 | 10.1.1.48 | 10.1.1.51 | .49–.50 |

---

## 7. VLSM — Variable Length Subnet Masking

VLSM allows **different subnet sizes** within the same major network. Most efficient use of IPs.

### Rule: Always allocate LARGEST subnet first!

**Example:**
```
Available: 192.168.1.0/24
Requirements:
  VLAN A: 100 hosts → needs /25 (126 hosts)
  VLAN B: 50 hosts  → needs /26 (62 hosts)
  VLAN C: 20 hosts  → needs /27 (30 hosts)
  WAN link: 2 hosts → needs /30 (2 hosts)

Allocation (largest first):
  VLAN A = 192.168.1.0/25    (.0 – .127)
  VLAN B = 192.168.1.128/26  (.128 – .191)
  VLAN C = 192.168.1.192/27  (.192 – .223)
  WAN    = 192.168.1.224/30  (.224 – .227)
  Remaining: .228 – .255 (spare)
```

### VLSM Scenario:
> A company needs 5 subnets from 10.0.0.0/24:
> - Sales: 50 hosts → /26 (62 hosts) = 10.0.0.0/26
> - IT: 25 hosts → /27 (30 hosts) = 10.0.0.64/27
> - HR: 10 hosts → /28 (14 hosts) = 10.0.0.96/28
> - Mgmt: 5 hosts → /29 (6 hosts) = 10.0.0.112/29
> - WAN: 2 hosts → /30 (2 hosts) = 10.0.0.120/30

---

## 8. Route Summarization

Combining multiple specific routes into one summary advertisement.

### How to find the summary route:
```
Networks to summarize:
  10.0.0.0/24
  10.0.1.0/24
  10.0.2.0/24
  10.0.3.0/24

Step 1: Write third octet in binary:
  0 = 00000000
  1 = 00000001
  2 = 00000010
  3 = 00000011

Step 2: Find common bits: 000000xx → 6 common bits
Step 3: Prefix = /16 (first 2 octets) + 6 bits = /22

Summary route = 10.0.0.0/22
(covers 10.0.0.0 through 10.0.3.255)
```

### Quick tip: 
Number of networks = 2^n → borrow n bits from prefix
4 networks = 2^2 → /24 - 2 = /22 ✓

---

## 9. NAT — Network Address Translation

### Types of NAT:

| Type | Description | Use Case |
|---|---|---|
| Static NAT | One private IP ↔ One public IP (1:1) | Servers that need permanent public IP |
| Dynamic NAT | Pool of public IPs assigned on demand | Multiple hosts, multiple public IPs |
| PAT (NAT Overload) | Many private IPs → One public IP (port-based) | Home/office internet (most common) |

### PAT (How it works):
```
Internal Host A (192.168.1.10:50001) → Router NAT → 203.0.113.5:50001 → Internet
Internal Host B (192.168.1.20:50002) → Router NAT → 203.0.113.5:50002 → Internet
Internal Host C (192.168.1.30:50003) → Router NAT → 203.0.113.5:50003 → Internet

All three hosts share ONE public IP (203.0.113.5)
Differentiated by unique source PORT numbers
Router keeps NAT translation table to map return traffic correctly
```

### Cisco NAT Config (PAT):
```
ip access-list standard INSIDE_HOSTS
  permit 192.168.1.0 0.0.0.255

ip nat inside source list INSIDE_HOSTS interface GigabitEthernet0/1 overload

interface GigabitEthernet0/0
  ip nat inside

interface GigabitEthernet0/1
  ip nat outside
```

### Verify NAT:
```
show ip nat translations
show ip nat statistics
```

---

## 10. IPv6 Basics

### Why IPv6?
- IPv4 = 2^32 = 4.3 billion addresses — EXHAUSTED
- IPv6 = 2^128 = 340 undecillion addresses — effectively unlimited

### IPv6 Address Format:
```
Full:        2001:0DB8:0000:0000:0000:0000:0000:0001
Compressed:  2001:DB8::1

Rules:
1. Remove leading zeros in each group: 0DB8 → DB8
2. Replace ONE consecutive all-zero group sequence with ::
3. :: can only appear ONCE in an address
```

### IPv6 Address Types:

| Type | Prefix | Description |
|---|---|---|
| Global Unicast | 2000::/3 | Public routable (like IPv4 public) |
| Link-local | FE80::/10 | Auto-configured, local link only |
| Unique Local | FC00::/7 | Private (like RFC 1918) |
| Multicast | FF00::/8 | One-to-many (replaces broadcast) |
| Loopback | ::1/128 | Local loopback (like 127.0.0.1) |
| Unspecified | ::/128 | No address assigned |

### IPv6 Special Facts:
- NO broadcast in IPv6 — uses multicast instead
- FF02::1 = All nodes (like 255.255.255.255)
- FF02::2 = All routers
- Link-local automatically configured on every interface
- SLAAC = Stateless Address Autoconfiguration (no DHCP needed)

---

## 11. Longest Prefix Match — Most Important Routing Rule

**When multiple routes match a destination, the MOST SPECIFIC (longest prefix) wins — regardless of AD or metric.**

```
Routing table:
  10.0.0.0/8      via 192.168.1.1  (OSPF)
  10.1.0.0/16     via 192.168.1.2  (EIGRP)
  10.1.1.0/24     via 192.168.1.3  (Static)
  0.0.0.0/0       via 192.168.1.4  (Default)

Packet destined for 10.1.1.50:
  Matches /8   ✓
  Matches /16  ✓
  Matches /24  ✓ ← WINNER (most specific)
  Matches /0   ✓

Router uses: 10.1.1.0/24 via 192.168.1.3
```

---

## 12. Full Scenario Bank — Exam Style

### Scenario 1: Find subnet details
```
Q: Host IP = 172.20.100.200/22. Find network address.
A: /22 = block size 4 in third octet.
   Subnets: .96,.100,.104 (multiples of 4)
   Wait — 100 is a multiple of 4 (100÷4=25) so:
   Subnets: ...96, 100, 104...
   But /22 covers 4 third-octet values:
   172.20.96.0 covers .96.x, .97.x, .98.x, .99.x
   172.20.100.0? No — 100÷4=25 exactly so .100 starts new subnet
   172.20.100.0/22 covers .100.x through .103.x
   .200 in fourth octet of .100. → Network = 172.20.100.0/22
   Broadcast = 172.20.103.255
```

### Scenario 2: Can hosts communicate directly?
```
Q: Host A = 10.0.0.65/26, Host B = 10.0.0.130/26. Same subnet?
A: /26 block size = 64
   Subnets: .0–.63, .64–.127, .128–.191, .192–.255
   .65 → in .64/26 subnet
   .130 → in .128/26 subnet
   DIFFERENT subnets → need router to communicate!
```

### Scenario 3: VLSM allocation
```
Q: Subnet 192.168.0.0/24 for: 100 hosts, 50 hosts, 20 hosts, 2 hosts
A: Largest first:
   100 hosts → /25 (126h) = 192.168.0.0/25   (.0–.127)
   50 hosts  → /26 (62h)  = 192.168.0.128/26 (.128–.191)
   20 hosts  → /27 (30h)  = 192.168.0.192/27 (.192–.223)
   2 hosts   → /30 (2h)   = 192.168.0.224/30 (.224–.227)
```

### Scenario 4: Invalid IP assignment
```
Q: Engineer assigns 192.168.1.0/24 to router interface. Users can't reach it.
A: .0 is the NETWORK ADDRESS — cannot be assigned to a host/interface!
   Fix: assign 192.168.1.1/24 or 192.168.1.254/24
```

### Scenario 5: Point-to-point WAN link
```
Q: What mask for a WAN link between exactly 2 routers?
A: /30 — gives 4 addresses: network, 2 usable hosts, broadcast
   10.0.0.0/30: Network=.0, Hosts=.1 and .2, Broadcast=.3
   Never use /29 or larger — wasteful for 2-device links
```

### Scenario 6: NAT/PAT
```
Q: 500 internal users, 1 public IP, all browsing internet simultaneously. How?
A: PAT (NAT overload) — all users share 1 public IP, differentiated by
   unique source port numbers in the NAT translation table.
```

### Scenario 7: Broadcast address assigned
```
Q: Router interface = 10.0.0.3/30. Connectivity issues.
A: /30 subnet 10.0.0.0/30: Network=.0, Hosts=.1,.2, Broadcast=.3
   .3 is the BROADCAST address — cannot be assigned!
   Fix: use 10.0.0.1/30 or 10.0.0.2/30
```

### Scenario 8: Longest prefix match
```
Q: Routes: 10.0.0.0/8, 10.5.0.0/16, 10.5.5.0/24. Packet to 10.5.5.100.
A: All three match. /24 is most specific → router uses 10.5.5.0/24.
   Longest prefix ALWAYS wins — before checking AD or metric.
```

---

## 13. Quick Revision — Flash Card Style

| Question | Answer |
|---|---|
| /24 usable hosts | 254 |
| /25 usable hosts | 126 |
| /26 usable hosts | 62 |
| /27 usable hosts | 30 |
| /28 usable hosts | 14 |
| /29 usable hosts | 6 |
| /30 usable hosts | 2 |
| /26 subnet mask | 255.255.255.192 |
| /27 subnet mask | 255.255.255.224 |
| /28 subnet mask | 255.255.255.240 |
| /30 subnet mask | 255.255.255.252 |
| Class A private range | 10.0.0.0/8 |
| Class B private range | 172.16.0.0/12 |
| Class C private range | 192.168.0.0/16 |
| APIPA range | 169.254.0.0/16 |
| IPv6 loopback | ::1 |
| IPv6 link-local prefix | FE80::/10 |
| IPv6 all-nodes multicast | FF02::1 |
| IPv6 total bits | 128 |
| PAT = | NAT overload — many hosts, one public IP |
| VLSM rule | Allocate largest subnet first |
| Longest prefix match | Most specific route always wins |
| Broadcast address formula | Network address + block size - 1 |
| Block size formula | 256 - last subnet mask octet value |

---

## 14. Exam Tips for Section 2

1. **Block size method** — master this and subnetting becomes fast. Block size = 256 - mask octet.
2. **VLSM** — always start with the LARGEST requirement first to avoid overlap.
3. **Network and broadcast** — network address (all host bits = 0) and broadcast (all host bits = 1) are NEVER assignable to hosts.
4. **Point-to-point links** — always use /30 (2 usable hosts). Never waste a /24 on a 2-router link.
5. **Longest prefix match** — the most specific route wins, period. AD and metric don't matter here.
6. **PAT** — this is how virtually every home and office internet connection works. One public IP, many users, unique port numbers.
7. **/22 and higher** — when prefix is in the third octet, block size applies to the THIRD octet value.
8. **IPv6** — know the address types, compression rules, and that there is NO broadcast in IPv6.

---

*CCNA 200-301 Section 2 Notes — ready for GitHub*
*Next: Section 3 — Switching & VLANs*
