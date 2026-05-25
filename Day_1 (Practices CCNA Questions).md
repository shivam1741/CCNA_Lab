# Day_1 Practice and only those Questions or topic in which I faced problem.
---
### 1. Which IPv6 address type is equivalent to IPv4 private addresses?
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
