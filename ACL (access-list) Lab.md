# ACL (Access-list) Lab
## What is ACL?
* ACL (Access Control List) is a list of rules used in networking devices like routers and firewalls to control network traffic.
* These rules decide which traffic is allowed (permit) and which traffic is blocked (deny).
* It allowed and blocked traffic based on:
   * Source IP
   * Destination IP
   * Protocol (TCP, UDP, ICMP)
   * Port number (HTTP, FTP, SSH, etc.)

## Where is ACL applied?
* ACL is applied on a router interface in one direction:
   * Inbound → Traffic coming into interface
   * Outbound → Traffic going out of interface
* Rule:
   * ACL checks traffic before routing decision (inbound)
   * ACL checks traffic after routing decision (outbound)

## Types of ACL
1. **Standard ACL:**
    * Filters ONLY source IP address.
    * Cannot filter destination IP, port, or protocol.
    * Standard ACL Ranges from 1 to 99.
    * 
