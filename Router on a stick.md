# Router on a stick lab:

### Firstly we will understand what is actual meaning of "Router on a stick"
   > “Router on a Stick” is a networking setup where one physical router interface is used to route traffic between multiple VLANs (Virtual Local Area Networks) using    subinterfaces.


   > Normally, routers connect different networks using separate physical interfaces (like Gig0/0, Gig0/1, etc.).
But sometimes, you have only one physical connection between the router and a switch — yet you still want to route traffic between multiple VLANs.

So, we create logical (virtual) interfaces called subinterfaces on that single physical port.    
***To understant it well we have other definition too.***

### What is Router-on-a-Stick?
   * It is a configuration where:
   * A Layer 2 switch has multiple VLANs (e.g., VLAN 10, 20, 30).
   * A router performs inter-VLAN routing using one physical interface.
   * The router interface is divided into subinterfaces (virtual interfaces).
   * The link between switch ↔ router is a trunk port carrying all VLAN traffic.
     

### Why do we need it?
   * Because VLANs cannot communicate with each other directly.
   * A normal switch cannot route VLAN traffic—it only switches frames.
   * So we use a router (or Layer 3 switch), and router-on-a-stick is the cheapest method when you don’t have a Layer 3 switch.


### Router configuration Example:
 * ***Examples:***   
 Suppose you have:   
    > VLAN 10 → Sales   
    > VLAN 20 → HR   
    > VLAN 30 → IT

  Instead of using 3 router ports, we do this: 

  ```
   interface g0/0
   no shutdown

   interface g0/0.10
   encapsulation dot1Q 10
   ip address 192.168.10.1 255.255.255.0

   interface g0/0.20
   encapsulation dot1Q 20
   ip address 192.168.20.1 255.255.255.0
   
   interface g0/0.30
   encapsulation dot1Q 30
   ip address 192.168.30.1 255.255.255.0

   ```
