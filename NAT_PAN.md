# NAT & PAT 

## What is NAT?

**NAT = Network Address Translation**

It is used to translate **private IP addresses** (inside network) into **public IP** (given by ISP) so that devices can access the internet.

### Why NAT is required?

* Private IPs **cannot** directly access the internet.
* Public IPv4 addresses are limited.
* NAT allows thousands of devices to use **a single public IP**.

## NAT Terminology (Very Important)

### **Inside Local**

Private IP of device inside your LAN
Example: `192.168.1.20`

### **Inside Global**

Public IP assigned by router to the inside device
Example: `203.0.113.10`

### **Outside Local**

IP assigned to an outside device **from your perspective** (rarely used)

### **Outside Global**

Real public IP of the outside device
(Example: Google server IP)


## 3. NAT Areas (Inside vs Outside)

NAT requires interface marking:

### **Inside Interface**

Private side

 ```
 interface g0/0
 ip nat inside
 ```

### **Outside Interface**

Public side

 ```
 interface g0/1
 ip nat outside
 ```

These commands define **network direction**, not NAT type.


## Types of NAT

### **A) Static NAT**

* 1 Private IP ↔ 1 Public IP
* Permanent mapping
* Used for servers

Example:

```
ip nat inside source static 192.168.1.20 203.0.113.20
```

### **B) Dynamic NAT**

* Uses a pool of public IPs
* Inside IP is dynamically mapped to **any free public IP**
* Still **1:1 mapping**

Example:

```
ip nat pool MYPOOL 203.0.113.10 203.0.113.20 netmask 255.255.255.0
ip nat inside source list 1 pool MYPOOL
```


### **C) PAT (Port Address Translation) – “NAT Overload”**

* **MOST COMMON**
* Many private IPs → One public IP
* Differentiated using **Port Numbers**

Example:

```
ip nat inside source list 1 interface g0/1 overload
```

## Why Use PAT?

Without **overload**, only **ONE** inside device can use the public IP at a time.

With overload:
> 1000+ devices   
> share same public IP   
> router assigns unique **port numbers**


## PAT Configuration (CCNA Lab)
![Cisco lab](https://github.com/shivam1741/CCNA_Lab/blob/Image/Screenshot%202025-12-25%20113654.png)

### **Step 1: Assign inside & outside interfaces**

 ```
 interface g 0/0/0
 ip nat inside

 interface g 0/0/1
 ip nat outside
 ```

### **Step 2: Create ACL to define inside private network**

```
access-list 1 permit 10.1.1.0 0.0.0.255
```

* **0.0.0.255** = wildcard mask (opposite of subnet mask)
* Here we can use ```any``` instead of ```10.1.1.0 0.0.0.255```



### **Step 3: NAT Overload (PAT)**

```
ip nat inside source list 1 interface g 0/0/1 overload
```
***Breakdown of this command:***

* **ip nat inside** → NAT traffic direction
* **source** → translate inside source IP
* **list 1** → use ACL 1
* **interface g 0/0/1** → use public IP of this interface
* **overload** → allow many private IPs to share one public IP
  using unique port numbers


## What Is Wildcard Mask (like 0.0.0.255)?**

Wildcard mask is opposite of subnet mask.

* Subnet mask: `255.255.255.0`
* Wildcard mask: `0.0.0.255`

Wildcard is used in **ACLs** to match a range of IPs.


## NAT without Overload (Dynamic NAT)

```
ip nat inside source list 1 pool MYPOOL
```

**Limitation:**
* ONLY ONE private device can go out per public IP.


## How PAT (Overload) Actually Works Internally

Example:
PC1 → 192.168.1.10
PC2 → 192.168.1.20
PC3 → 192.168.1.30

All go to internet using public IP: 203.0.113.10

Router creates translations like:

| Inside Local | Inside Global (Public IP) | Port |
| ------------ | ------------------------- | ---- |
| 192.168.1.10 | 203.0.113.10              | 1025 |
| 192.168.1.20 | 203.0.113.10              | 1026 |
| 192.168.1.30 | 203.0.113.10              | 1027 |

Same IP → different port numbers.


## Useful NAT Verification Commands

### Show NAT translations

```
show ip nat translations
```

### Show NAT statistics

```
show ip nat statistics
```



