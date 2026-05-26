# Day_1 Practice and only those Questions or topic in which I faced problem.
---
### Which IPv6 address type is equivalent to IPv4 private addresses?
  (A). Global Unicast    
  (B). Link Local   
  (C). Unique Local   
  (D). Multicast

  ## The main IPv6 address types and their equivalent in IPv4:

| IPv6 Type                      | Purpose                                | IPv4 Equivalent                        |
| ------------------------------ | -------------------------------------- | -------------------------------------- |
| **Unicast**                    | One-to-one communication               | Normal IP address                      |
| **Multicast**                  | One-to-many communication              | Multicast                              |
| **Anycast**                    | One-to-nearest device                  | No direct common IPv4 equivalent       |
| **Link-Local**                 | Communication within same network/link | APIPA (`169.254.x.x`)                  |
| **Unique Local Address (ULA)** | Private internal network               | Private IP (`192.168.x.x`, `10.x.x.x`) |
| **Global Unicast**             | Public internet address                | Public IPv4 address                    |
| **Loopback**                   | Device talks to itself                 | `127.0.0.1`                            |
| **Unspecified Address**        | Means “no address assigned”            | `0.0.0.0`                              |
| **Broadcast**                  | Not used in IPv6                       | IPv4 Broadcast                         |

Important point:

* IPv6 does **not** use broadcast.
* It uses multicast instead.

Examples:

### IPv4 vs IPv6 examples

| Purpose     | IPv4          | IPv6        |
| ----------- | ------------- | ----------- |
| Loopback    | `127.0.0.1`   | `::1`       |
| Unspecified | `0.0.0.0`     | `::`        |
| Private IP  | `192.168.1.1` | `fc00::/7`  |
| APIPA       | `169.254.x.x` | `fe80::/10` |
| Public IP   | `8.8.8.8`     | `2000::/3`  |

Most important IPv6 types for interviews:

1. Global Unicast
2. Link-Local
3. Unique Local
4. Multicast
5. Anycast
---
---
### What is the purpose of ICMP?
A) Assign IP addresses   
B) Send error messages and operational info about IP networks   
C) Encrypt network traffic   
D) Resolve hostnames to IP addresses

## ICMP (Internet Control Message Protocol)

### Its main job:

* send error messages
* provide network operational information

* It does NOT carry normal user data like:
  * videos
  * messages
  * websites

* Instead it helps diagnose network issues.


### Where ICMP works

ICMP works at:

```
Network Layer (Layer 3)
```

along with IP.

Think:

* IP = delivers packet
* ICMP = reports network conditions/problems


### Real life example

Suppose your PC sends packet to a server.

Possible problems:

* destination unreachable
* router down
* TTL expired
* network congested

How will your PC know?

Using ICMP messages.


### ICMP messages   

***1. Echo Request & Echo Reply***
Used by:
```
ping
```
Process:

```
Your PC → ICMP Echo Request
Server → ICMP Echo Reply
```
If reply comes:

* network is reachable


***2. Destination Unreachable***

If router cannot deliver packet:

Router sends:

```
ICMP Destination Unreachable
```

Reasons:

* host down
* port closed
* network unavailable


***3. Time Exceeded***

Related to TTL.

If TTL becomes 0:

* router drops packet
* sends ICMP Time Exceeded

Used by:

```
traceroute
```


### Important thing

ICMP is encapsulated inside IP packet.

Structure:

```
IP Header + ICMP Header + Data
```

So ICMP depends on IP.


### Is ICMP TCP or UDP?

Neither.

ICMP is its own protocol.

| Protocol | Purpose                     |
| -------- | --------------------------- |
| TCP      | reliable communication      |
| UDP      | fast communication          |
| ICMP     | control and error reporting |

---
---
### What does the TTL field in an IP header prevent?
A) Duplicate packets   
B) Routing loops causing infinite packet forwarding  
C) Packet fragmentation   
D) IP spoofing

# TTL, ICMP and traceroute and how they are related

Traceroute basically uses TTL + ICMP together to discover the route packets take through the network.


## First understand TTL properly

Suppose packet starts with:

```
TTL = 5
```

Every router does:

```
TTL = TTL - 1
```

Example:

```
PC → R1 → R2 → R3 → Server
```

Flow:

| Device   | TTL |
| -------- | --- |
| PC sends | 5   |
| R1       | 4   |
| R2       | 3   |
| R3       | 2   |
| Server   | 1   |

If TTL becomes 0 before reaching destination:

* router destroys packet
* sends ICMP Time Exceeded

This is the MOST IMPORTANT part.


## How traceroute uses this

* Traceroute intentionally manipulates TTL.
* It sends packets with very small TTL values.

## Step-by-step traceroute working

Suppose route is:

```
You → R1 → R2 → R3 → Google
```

### Step 1: Send packet with TTL = 1

Packet flow:

```
You → R1
```

At R1:

```
TTL 1 → 0
```

So R1:

* drops packet
* sends ICMP Time Exceeded back

Now your PC learns:

```
First router = R1
```



### Step 2: Send packet with TTL = 2

Flow:

```
You → R1 → R2
```

At R2:

```
TTL 1 → 0
```

R2 sends:

```
ICMP Time Exceeded
```

Now traceroute learns:

```
Second router = R2
```

### Step 3: TTL = 3

Flow:

```
You → R1 → R2 → R3
```

R3 sends ICMP reply.

Traceroute learns:

```
Third router = R3
```



### Step-4 (Eventually destination is reached)

TTL = 4

* Packet reaches Google successfully.
* Now destination replies differently depending on OS/tool.
* Traceroute understands:

```
Destination reached
```

and stops.




## Real example

Linux:

```
traceroute google.com
```

Windows:

```
tracert google.com
```

Output:

```text
1  192.168.1.1
2  10.20.30.1
3  isp-router.net
4  google.com
```

Each line = one router hop.


## Important interview point

Traceroute depends on:

* TTL/Hop Limit
* ICMP replies

Without TTL expiration:
* traceroute cannot map routers.

Without ICMP:
* your system never gets router responses.

## Difference in Linux and Windows
### Windows tracert

Uses:
```
ICMP Echo Requests
```

### Linux traceroute

Traditionally uses:

```
UDP packets
```

But still relies on:

```
ICMP Time Exceeded
```

returned by routers.


## Why some hops show *

Sometimes routers:

* block ICMP
* rate-limit replies
* firewall traceroute

Then output becomes:

```
* * *
```

Does NOT always mean network failure.


## IPv6 version

* TTL is renamed Hop Limit(for IPv6)
* ICMPv6 is used instead of ICMP.
* But concept is Same.

# Answer of the question

The correct answer is:

## **B. Routing loops causing infinite packet forwarding**

## Why?

TTL (Time To Live) is a field inside the IP header that limits how long a packet can travel in a network.

When a packet moves through routers:

```
Each router decreases TTL by 1
```

If TTL becomes:

```
0
```

the router:

* discards the packet
* sends an ICMP Time Exceeded message back


## What problem does this solve?

Sometimes routers can accidentally create a:

```
Routing Loop
```

Example:

```
Router A → Router B → Router C
      ↑               ↓
      ← ← ← ← ← ← ← ←
```

Packet keeps traveling forever in circles.

Without TTL:

* bandwidth would be wasted
* routers would become overloaded
* network congestion would happen

So TTL prevents:

```
Infinite packet forwarding
```

## Example

Suppose packet starts with:

```text id="7xjlwm"
TTL = 4
```

Flow:

| Router  | TTL         |
| ------- | ----------- |
| Sent    | 4           |
| Router1 | 3           |
| Router2 | 2           |
| Router3 | 1           |
| Router4 | 0 → dropped |

Packet dies safely instead of looping forever.

---
---
