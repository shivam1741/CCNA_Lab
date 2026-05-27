
# VTP (VLAN Trunking Protocol) 

## 1. What is VTP?

VTP = VLAN Trunking Protocol

Used to automatically share VLAN information between Cisco switches.

Without VTP:

* VLAN must be created manually on every switch.

With VTP:

* Create VLAN once on server.
* Other switches learn automatically.


## 2. VTP Works On

Trunk links only

Usually:

* 802.1Q trunks


## 3. VTP Modes

| Mode        | Create VLAN | Learn VLAN | Forward VTP |
| ----------- | ----------- | ---------- | ----------- |
| Server      | Yes         | Yes        | Yes         |
| Client      | No          | Yes        | Yes         |
| Transparent | Local only  | No         | Yes         |



### 1. VTP Server

* Main controlling switch
* Can create/delete VLANs
* Sends VLAN database to others

Default Cisco switch mode:

```
VTP Server
```


### 2. VTP Client

* Cannot create VLANs
* Learns VLAN database from server
* Follows server updates


### 3. VTP Transparent Mode

MOST IMPORTANT CONFUSION 

Transparent mode:
* forwards VTP advertisements
* allows local VLAN creation
* does NOT synchronize VLAN database
* does NOT participate in VTP synchronization



## 4. Why Companies Use Transparent Mode

Because default switch mode is:

```
VTP Server
```

which can be dangerous.

Transparent mode protects network from:

* accidental VLAN overwrite
* revision number problems
* unwanted synchronization



## 5. Important Real-World Understanding

When companies say:

> “We don’t use VTP”

They usually mean:
no automatic VLAN synchronization

But they may still configure:

```
vtp mode transparent
```

for safety.


## 6. Revision Number

Used to identify newer VLAN database.

Higher revision number = newer database.

Example:

| Action         | Revision |
| -------------- | -------- |
| Start          | 0        |
| Create VLAN 10 | 1        |
| Create VLAN 20 | 2        |


## 7. VTP Danger

If a switch with:

* same VTP domain
* higher revision number

joins network,

then entire VLAN database may get overwritten.

This is called a VTP disaster/bomb.



## 8. Why Transparent Mode Helps

If all switches are transparent:

```
vtp mode transparent
```

then:

* no switch acts as client
* no switch accepts synchronization
* accidental overwrite prevented

## 9. Transparent Mode in Middle

Example:

```
Server ---- Transparent ---- Client
```

Transparent switch:

* forwards advertisements
* but does not synchronize itself

So client still receives updates.



## 10. Transparent Mode is OPTIONAL

VTP can work without transparent switch.

Example:

```
Server ---- Client
```

works perfectly.

Transparent is only needed when:

* a switch should pass updates
* but should not participate.


## 11. Requirements for VTP

All switches should have:
* same VTP domain
* compatible VTP version
* trunk links
* same password (if configured)

## 12. Common Configurations

### Server

```
vtp domain OFFICE
vtp mode server
```

---

### Client

```
vtp domain OFFICE
vtp mode client
```

---

### Transparent

```
vtp mode transparent
```


## 13. Important Interview Line

> Transparent mode is part of VTP, but it does not participate in VLAN synchronization. It only forwards VTP advertisements while maintaining its own local VLAN database.

---
