# Router on a stick lab:
* Firstly we will understand what is actual meaning of "Router on a stick"
> “Router on a Stick” is a networking setup where one physical router interface is used to route traffic between multiple VLANs (Virtual Local Area Networks) using subinterfaces.


> Normally, routers connect different networks using separate physical interfaces (like Gig0/0, Gig0/1, etc.).
But sometimes, you have only one physical connection between the router and a switch — yet you still want to route traffic between multiple VLANs.

So, we create logical (virtual) interfaces called subinterfaces on that single physical port.

* ***Examples:***   
Suppose you have:   
   > VLAN 10 → Sales   
   > VLAN 20 → HR   
   > VLAN 30 → IT

  Instead of using 3 router ports, you do this:   
  ***Router configuration:***

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
