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
