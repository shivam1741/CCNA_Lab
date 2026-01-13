# ACL (Access-list) concept
## What is ACL?
* ACL (Access Control List) is a list of rules used in networking devices like routers and firewalls to control network traffic.
* These rules decide which traffic is allowed (permit) and which traffic is blocked (deny).
* ACL is processed **top to bottom**
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
1. **Standard ACL**
    * Filters ONLY source IP address.
    * Cannot filter destination IP, port, or protocol.
    * Standard ACL Ranges from 1 to 99.
    * **1. Example – Block a network:**

   ```
   access-list 1 deny 192.168.1.0 0.0.0.255
   access-list 1 permit any
   ```

   * **2. Example – Block a single host:**

   ```
   access-list 10 deny host 192.168.1.50
   access-list 10 permit any
   ```

   * **Apply to interface:**

    ```
    interface g0/1
    ip access-group 1 out
    ```
2. **Extended ACL**
   * Filters **source IP, destination IP, protocol, and port.**
   * Extended ACL ranges from 100 to 199.
   * **1. Example – Block HTTP traffic:**
   ```
   access-list 101 deny tcp 192.168.1.0 0.0.0.255 any eq 80
   access-list 101 permit ip any any
   ```
   * **2. Example – Allow only SSH:**

    ```
    access-list 110 permit tcp any any eq 22
    access-list 110 deny ip any any
    ```

   * **3. Example – Block Ping:**
    ```
    access-list 120 deny icmp any any
    access-list 120 permit ip any any
    ```

    * **Apply to interface:**

    ```
    interface g0/0
    ip access-group 101 in
    ```
----

# ACL Lab


