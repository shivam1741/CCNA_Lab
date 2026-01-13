# **Spanning Tree Protocol (STP) Concept**
## What is STP?
STP (Spanning Tree Protocol) is a Layer-2 protocol that prevents switching loops in a network with multiple switches.
  


## Why Loops Are Dangerous

* Causes broadcast storms
* MAC table keeps flipping
* Switch CPU becomes overloaded
* Network becomes unusable


## When Do We Need STP?

* When more than one switch is connected
* When multiple paths exist between switches
* Not required on a single switch


## STP Port States

1. **Blocking**
2. **Listening** – 15 sec
3. **Learning** – 15 sec
4. **Forwarding**

Total delay = **30 seconds** (when PortFast is OFF)

## What Each State Does

* ***Blocking***

   * Does not forward frames
   * Receives BPDUs
   * Used to prevent loops

* ***Listening (15 sec)***

   * Exchanges BPDUs
   * Detects loops
   * Decides:
      > Root port   
      > Designated port   
      > Blocked port

* ***Learning (15 sec)***

   * Learns MAC addresses
   * Still does not forward traffic

* **Forwarding**

    * Port forwards normal traffic


## Loop Detection Logic

* During **Listening**, switches compare BPDUs
* STP decides which path is best
* Ports that would cause loops are put into **Blocking**

--> Blocking happens **after Listening makes the decision**


## STP Timers

* Listening: 15 sec
* Learning: 15 sec
  Total: **30 sec**


## PortFast

Command:

```
spanning-tree portfast
```

* Skips Listening and Learning
* Port immediately becomes Forwarding
* Used only for: PC, Laptop, Server ports
* Not used for switch-to-switch connections

With PortFast: **0 sec delay**


## What Happens If a Loop Is Found?

* STP blocks one port
* That port goes to Blocking state
* Loop is removed
----

# Note(Extra)
## Broadcast Domain

A broadcast domain is the area where every device receives broadcast/flooded frames.

* Switch → does not break broadcast domains
* Router → breaks broadcast domains
* Each VLAN = one broadcast domain


## Collision Domain

* Each switch port = 1 collision domain
* Hubs share collision domain
* Switches eliminate collisions
----


## Summary

* STP prevents loops using BPDU packets
* Loop detection happens in Listening state
* Blocking happens after STP decides a port will cause a loop
* Without PortFast = 30 sec delay
* With PortFast = 0 sec delay
----

# Spanning Tree Protocol (STP) Lab


