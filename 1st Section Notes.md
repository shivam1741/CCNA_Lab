# CCNA 200-301 — Section 1: Network Fundamentals
> Complete notes with examples, scenarios, and exam tips
> Topics: OSI Model, TCP/IP Model, TCP vs UDP, ARP, ICMP, DNS, DHCP, Ports, Encapsulation

---

## 1. OSI Model — The 7 Layers

### Mnemonic (top to bottom):
> **"All People Seem To Need Data Processing"**
> Application → Presentation → Session → Transport → Network → Data Link → Physical

| Layer | Number | Name | PDU | Devices | Key Protocols |
|---|---|---|---|---|---|
| 7 | Application | Application | Data | - | HTTP, HTTPS, FTP, SSH, DNS, DHCP, SMTP |
| 6 | Presentation | Presentation | Data | - | SSL/TLS, JPEG, MPEG, ASCII |
| 5 | Session | Session | Data | - | NetBIOS, RPC, SQL |
| 4 | Transport | Transport | Segment | - | TCP, UDP |
| 3 | Network | Network | Packet | Router | IP, ICMP, ARP (debated) |
| 2 | Data Link | Data Link | Frame | Switch, Bridge | Ethernet, 802.1Q, PPP |
| 1 | Physical | Physical | Bit | Hub, Cable, NIC | Ethernet cables, fiber, Wi-Fi signals |

### Key Rules to Remember:
- **Encapsulation** = adding headers as data travels DOWN the layers (sender side)
- **De-encapsulation** = removing headers as data travels UP the layers (receiver side)
- Each layer only communicates with the layer directly above and below it
- Routers operate at **Layer 3** — they strip the Layer 2 frame and rebuild it at each hop
- Switches operate at **Layer 2** — they read MAC addresses only

### Scenario Example:
> You send an email. Your email app (Layer 7) passes data down. Transport (Layer 4) breaks it into segments and adds TCP headers. Network (Layer 3) adds IP headers making packets. Data Link (Layer 2) adds MAC headers making frames. Physical (Layer 1) converts to bits and sends over the wire.

---

## 2. TCP/IP Model — 4 Layers

| TCP/IP Layer | OSI Equivalent | Protocols |
|---|---|---|
| Application | Layers 5, 6, 7 | HTTP, DNS, DHCP, FTP, SSH, SMTP |
| Transport | Layer 4 | TCP, UDP |
| Internet | Layer 3 | IP, ICMP, ARP |
| Network Access | Layers 1, 2 | Ethernet, Wi-Fi, PPP |

> Think of TCP/IP as a simplified version of OSI. The exam tests both — know how they map to each other.

---

## 3. TCP vs UDP — Know This Cold

| Feature | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented (3-way handshake) | Connectionless |
| Reliability | Reliable — acknowledgements, retransmission | Unreliable — no ACKs |
| Ordering | Ordered delivery (sequence numbers) | No ordering |
| Speed | Slower (overhead) | Faster (low overhead) |
| Flow control | Yes | No |
| Use cases | HTTP, HTTPS, FTP, SSH, Telnet, SMTP | DNS, DHCP, VoIP, Video streaming, SNMP |

### TCP Three-Way Handshake:
```
Client                    Server
  |-------- SYN ---------->|   "I want to connect, seq=100"
  |<------- SYN-ACK -------|   "OK, seq=200, ack=101"
  |-------- ACK ---------->|   "Got it, ack=201"
  |<====== DATA FLOWS ====>|
```

### TCP Four-Way Termination:
```
Client                    Server
  |-------- FIN ---------->|
  |<-------- ACK ----------|
  |<-------- FIN ----------|
  |--------- ACK -------->|
```

### Scenario:
> A VoIP call uses UDP. Why? Because in a phone call, a slightly dropped packet is better than a delayed one. TCP retransmission would cause noticeable lag. UDP sacrifices reliability for speed.

---

## 4. Encapsulation & PDUs

### Encapsulation (Sender — going DOWN):
```
Application Layer  →  DATA
Transport Layer    →  SEGMENT  (TCP/UDP header + Data)
Network Layer      →  PACKET   (IP header + Segment)
Data Link Layer    →  FRAME    (MAC header + Packet + Trailer)
Physical Layer     →  BITS     (electrical/optical signals)
```

### De-encapsulation (Receiver — going UP):
```
Physical   →  BITS received
Data Link  →  checks MAC, strips frame header → PACKET
Network    →  checks IP, strips IP header → SEGMENT
Transport  →  checks port, strips TCP header → DATA
Application → application receives data
```

### Memory trick for PDUs:
> **"Do Some People Fear Birthdays?"**
> Data → Segment → Packet → Frame → Bits (top to bottom)

---

## 5. Common Port Numbers — Must Memorise

| Protocol | Port | Transport |
|---|---|---|
| FTP (data) | 20 | TCP |
| FTP (control) | 21 | TCP |
| SSH | 22 | TCP |
| Telnet | 23 | TCP |
| SMTP | 25 | TCP |
| DNS | 53 | UDP (queries), TCP (zone transfers) |
| DHCP Server | 67 | UDP |
| DHCP Client | 68 | UDP |
| HTTP | 80 | TCP |
| HTTPS | 443 | TCP |
| SNMP | 161 | UDP |
| SNMP Traps | 162 | UDP |
| Syslog | 514 | UDP |
| RDP | 3389 | TCP |

### Exam Tip:
> If a question says "secure" → think HTTPS (443) or SSH (22)
> If a question says "management/monitoring" → think SNMP (161/162)
> If a question says "log messages" → think Syslog (514)

---

## 6. ARP — Address Resolution Protocol

### What it does:
ARP maps a **known IP address** to an **unknown MAC address**.

### Why it's needed:
- IP packets need to be wrapped in Ethernet frames
- Ethernet frames use MAC addresses, not IP addresses
- Before sending a frame, the sender must know the destination MAC

### ARP Process:
```
Host A (192.168.1.10) wants to send to Host B (192.168.1.20)

Step 1: Host A checks its ARP cache — not found
Step 2: Host A broadcasts ARP Request:
        "Who has 192.168.1.20? Tell 192.168.1.10"
        (Destination MAC = FF:FF:FF:FF:FF:FF — broadcast)

Step 3: Host B receives the broadcast, replies:
        "I am 192.168.1.20, my MAC is AA:BB:CC:DD:EE:FF"
        (Unicast reply directly to Host A)

Step 4: Host A stores result in ARP cache
Step 5: Host A sends the frame to AA:BB:CC:DD:EE:FF
```

### When destination is on a different network:
```
Host A (192.168.1.10/24) → wants to reach 10.0.0.5

Host A knows 10.0.0.5 is NOT on its subnet
Host A ARPs for its DEFAULT GATEWAY MAC (not the destination)
Host A sends frame to gateway MAC — gateway routes it onward
```

### Gratuitous ARP:
- A device ARPs for its OWN IP address after receiving it
- Purpose: detect IP address conflicts
- If another device replies → "IP address conflict detected" warning

### ARP Cache:
```
show ip arp          (Cisco router command)
arp -a               (Windows/Linux command)
```

### Scenario:
> A user's PC just booted. It pings the gateway 192.168.1.1. Even though the user knows the gateway IP, the PC must first ARP for the gateway's MAC address. Only then can it build an Ethernet frame to send the ICMP echo request.

---

## 7. ICMP — Internet Control Message Protocol

### What it does:
ICMP provides **error reporting** and **network diagnostics**. It does NOT use port numbers.

### Key ICMP Message Types:

| Type | Name | Triggered by |
|---|---|---|
| 0 | Echo Reply | Response to ping |
| 3 | Destination Unreachable | No route to host/port closed |
| 5 | Redirect | Better route available |
| 8 | Echo Request | Ping command |
| 11 | Time Exceeded | TTL reached 0 (used by Traceroute) |

### Ping (Echo Request / Echo Reply):
```
PC> ping 8.8.8.8

Sending 5 ICMP echo requests to 8.8.8.8:
!!!!!    → 5 replies received (! = success, . = timeout)

Success rate is 100 percent, round-trip min/avg/max = 12/15/18 ms
```

### TTL (Time to Live):
- Each router decrements TTL by 1
- TTL = 0 → router drops packet + sends ICMP Time Exceeded back to source
- Prevents packets looping forever
- Default TTL values: Windows = 128, Linux = 64, Cisco IOS = 255

### Traceroute — How it works:
```
Traceroute exploits TTL:

Probe 1: TTL=1 → Router 1 drops it → sends Time Exceeded → reveals Router 1 IP
Probe 2: TTL=2 → Router 1 forwards → Router 2 drops → reveals Router 2 IP
Probe 3: TTL=3 → reaches destination → destination sends Echo Reply

Result:
1   192.168.1.1    2ms    (your gateway)
2   10.0.0.1       8ms    (ISP router)
3   8.8.8.8        15ms   (destination)
```

### Scenario:
> A user pings 8.8.8.8 and gets "Destination Unreachable — Network Unreachable". This is an ICMP Type 3 message. It means the router has no route to reach 8.8.8.8 — likely the default route is missing or the ISP link is down.

### Common ICMP Scenarios on Exam:

| Symptom | ICMP Message | Meaning |
|---|---|---|
| ping times out | No reply | Firewall blocking ICMP or host down |
| "Network Unreachable" | Type 3, Code 0 | No route to network |
| "Host Unreachable" | Type 3, Code 1 | Route exists but host not responding |
| "Port Unreachable" | Type 3, Code 3 | Host up but port/service not listening |
| traceroute shows * * * | Time Exceeded blocked | Firewall filtering ICMP TTL exceeded |

---

## 8. DNS — Domain Name System

### What it does:
DNS resolves **human-readable hostnames** to **IP addresses**.

**Port:** UDP 53 (queries) | TCP 53 (zone transfers)

### DNS Resolution Process:
```
User types: www.google.com

Step 1: PC checks local DNS cache (ipconfig /displaydns)
Step 2: PC checks hosts file (C:\Windows\System32\drivers\etc\hosts)
Step 3: PC sends DNS query to configured DNS server (UDP port 53)
Step 4: DNS server checks its cache
Step 5: If not cached → DNS server queries root servers → TLD servers → authoritative server
Step 6: DNS server returns IP address to PC
Step 7: PC caches the result (TTL-based)
Step 8: PC now initiates TCP connection to the resolved IP
```

### DNS Record Types:

| Record | Purpose | Example |
|---|---|---|
| A | Hostname → IPv4 address | www.cisco.com → 198.133.219.25 |
| AAAA | Hostname → IPv6 address | www.cisco.com → 2001:420::1 |
| CNAME | Alias → another hostname | mail.cisco.com → smtp.cisco.com |
| MX | Mail server for domain | cisco.com → mail.cisco.com |
| PTR | IP → Hostname (reverse DNS) | 198.133.219.25 → www.cisco.com |
| NS | Authoritative name server | cisco.com → ns1.cisco.com |

### Scenario:
> A user can ping 8.8.8.8 successfully but cannot open www.google.com in a browser. What is the issue?
> Answer: DNS resolution is failing. IP connectivity works (ping to IP succeeds) but the hostname cannot be resolved. Check DNS server configuration — likely the DNS server IP is wrong or the DNS server is unreachable.

---

## 9. DHCP — Dynamic Host Configuration Protocol

### What it does:
DHCP automatically provides hosts with:
- IP address
- Subnet mask
- Default gateway
- DNS server address
- Lease time

**Ports:** UDP 67 (server) | UDP 68 (client)

### DHCP DORA Process — Memorise This!
```
Client                              DHCP Server
  |                                      |
  |--- DHCP DISCOVER ------------------>|   src: 0.0.0.0  dst: 255.255.255.255
  |    "Anyone there? I need an IP"      |   (broadcast — no IP yet)
  |                                      |
  |<-- DHCP OFFER ----------------------|   src: 192.168.1.1  dst: 255.255.255.255
  |    "I offer you 192.168.1.50"        |
  |                                      |
  |--- DHCP REQUEST ------------------->|   src: 0.0.0.0  dst: 255.255.255.255
  |    "I accept 192.168.1.50"           |   (still broadcast — other servers need to know)
  |                                      |
  |<-- DHCP ACK ------------------------|   src: 192.168.1.1  dst: 192.168.1.50
  |    "Confirmed! Lease = 8 days"       |
  |                                      |
  |  [Client configures IP and starts using it]
```

### DHCP Lease Renewal:
- At 50% of lease time → client tries to renew with same server (unicast)
- At 87.5% of lease time → client broadcasts renewal request to any server
- At 100% → lease expires, client must get new IP via full DORA

### APIPA — Automatic Private IP Addressing:
- If DHCP server unreachable → Windows auto-assigns 169.254.0.0/16
- PC picks random address in range, uses Gratuitous ARP to check for conflicts
- 169.254.x.x allows local LAN communication ONLY — no internet access
- Seeing 169.254.x.x = DHCP server is unreachable!

### Cisco DHCP Server Configuration:
```
Router(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10
Router(config)# ip dhcp pool OFFICE
Router(dhcp-config)# network 192.168.1.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.1.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# lease 8
```

### DHCP Relay Agent:
- DHCP broadcasts don't cross routers
- If DHCP server is on a different subnet, configure a DHCP relay agent
```
Router(config-if)# ip helper-address 10.0.0.5   ← IP of DHCP server
```
This converts the broadcast DHCP Discover to a unicast packet sent to the DHCP server.

### Scenario:
> A new PC is connected to the network but shows IP 169.254.45.100. The network admin checks and finds the DHCP server is online. What should be checked?
> Answer: Check if the PC's subnet has a DHCP relay agent configured (ip helper-address). The DHCP server may be on a different subnet and broadcasts are not reaching it. Also check if the DHCP pool is exhausted.

---

## 10. SNMP & Syslog

### SNMP (Simple Network Management Protocol):
- Used to **monitor and manage** network devices
- NMS (Network Management System) polls agents on devices
- Agents send **traps** (alerts) to NMS when something happens
- **UDP 161** = NMS queries agent | **UDP 162** = agent sends trap to NMS
- Versions: SNMPv1 (plain text), SNMPv2c (community strings), SNMPv3 (encrypted + authenticated)

### Syslog:
- Devices send log messages to a central syslog server
- **UDP 514**
- Severity levels 0-7: Emergency, Alert, Critical, Error, Warning, Notice, Informational, Debug
- Mnemonic: **"Every Awful Cisco Engineer Will Need Ice Daily"**

---

## 11. Key Scenario Bank — Exam Style

### Scenario 1: User can't browse internet
```
Symptoms: Can ping default gateway ✓, Cannot ping 8.8.8.8 ✗
Diagnosis: Problem beyond the gateway — ISP link, upstream routing
Fix: Check WAN interface, default route on gateway router
```

### Scenario 2: User can ping IP but not hostname
```
Symptoms: ping 8.8.8.8 works ✓, ping www.google.com fails ✗
Diagnosis: DNS resolution failure
Fix: Check DNS server IP in IP configuration, verify DNS server is reachable
```

### Scenario 3: PC shows 169.254.x.x
```
Symptoms: PC has APIPA address, cannot reach anything
Diagnosis: DHCP server unreachable
Fix: Check DHCP server, check ip helper-address on router if server is remote
```

### Scenario 4: IP address conflict warning
```
Symptoms: Windows shows "IP address conflict detected"
Diagnosis: Two devices have the same IP — detected by Gratuitous ARP
Fix: Find duplicate IP, reassign one, check DHCP excluded addresses
```

### Scenario 5: Traceroute shows * * * from hop 4 onwards
```
Symptoms: Hops 1-3 respond, hop 4+ shows * * *
Diagnosis: Firewall blocking ICMP Time Exceeded messages at hop 4, OR link down beyond hop 4
Fix: If only hop 4 is *, router blocks ICMP but forwards — not a real problem
     If all hops from 4 onwards are *, the network is down beyond hop 4
```

### Scenario 6: ARP request for gateway before first ping
```
Scenario: PC boots, pings 192.168.1.1 (gateway). What happens first?
Answer: PC ARPs for gateway MAC (broadcasts FF:FF:FF:FF:FF:FF asking for 192.168.1.1's MAC).
        Only after receiving ARP reply can it send the ICMP Echo Request.
```

---

## 12. Quick Revision — Flash Card Style

| Question | Answer |
|---|---|
| OSI Layer 3 PDU | Packet |
| OSI Layer 2 PDU | Frame |
| OSI Layer 4 PDU | Segment |
| Protocol that resolves IP to MAC | ARP |
| Protocol that resolves hostname to IP | DNS |
| Protocol that assigns IP automatically | DHCP |
| DHCP process | DORA (Discover, Offer, Request, Acknowledge) |
| DHCP source IP before getting IP | 0.0.0.0 |
| DHCP destination before getting IP | 255.255.255.255 |
| APIPA range | 169.254.0.0/16 |
| ICMP used by ping | Type 8 (request), Type 0 (reply) |
| ICMP used by traceroute | Type 11 (Time Exceeded) |
| DNS port | UDP 53 |
| HTTPS port | TCP 443 |
| SSH port | TCP 22 |
| SNMP query port | UDP 161 |
| SNMP trap port | UDP 162 |
| Layer that switches operate | Layer 2 |
| Layer that routers operate | Layer 3 |
| TCP handshake steps | SYN → SYN-ACK → ACK |
| When is TCP used over UDP | When reliability matters (HTTP, SSH, FTP) |
| When is UDP used over TCP | When speed matters (DNS, VoIP, streaming) |

---

## 13. Exam Tips for Section 1

1. **OSI layers** — know the number, name, PDU, and device for each layer cold
2. **Port numbers** — at minimum memorise: 20/21 FTP, 22 SSH, 23 Telnet, 25 SMTP, 53 DNS, 67/68 DHCP, 80 HTTP, 443 HTTPS, 161/162 SNMP
3. **ARP** — always resolves IP to MAC, NOT hostname to IP
4. **DHCP DORA** — first two packets use src 0.0.0.0 dst 255.255.255.255
5. **APIPA** — 169.254.x.x always means DHCP failed
6. **Scenario questions** — eliminate wrong answers first. If you can ping IP but not name → DNS. If you can ping gateway but not internet → upstream issue.
7. **TCP vs UDP** — reliable ordered = TCP, fast low-overhead = UDP

---
---

### Which OSI layer adds a TRAILER (not just a header) to the data?   
A. Network layer    
B. Transport layer    
C. Application layer    
D. Data Link layer    
E. Physical layer     
Explanation: The Data Link layer is the ONLY layer that adds both a header AND a trailer. The trailer contains an FCS (Frame Check Sequence) for error detection. All other layers add headers only. This is a very common trick question.
