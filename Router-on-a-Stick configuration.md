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

### Cisco Project 
![router on a stick](https://github.com/shivam1741/CCNA_Lab/blob/Image/Screenshot%202025-12-11%20184822.png) 
1. ***Creating VLANs on the Switch***   
In this project, I first divided a switch into two VLANs (VLAN 10 and VLAN 20).   
After entering global configuration mode, I created each VLAN:
    ```
    vlan 10
    name IT_support
    exit
    ```
    ```
    vlan 20
    name NOC_Engineer
    exit
    ```
2. ***Assigning Ports to VLANs***   
Now it's time to tell the interface port that which interface port comes in which vlan for this we need use ```interface range g 1/0/2-5``` then ```switchport mode access``` after that ```switchport access vlan 10```, And I did the same for vlan 20 ```int range g 1/0/6-8``` then ```switchport mode access``` after that ```switchport access vlan 20```.
    ```
    interface range g 1/0/2-5
    switchport mode access
    switchport access vlan 10
    ```
   For VLAN 20   
    ```
    int range g 1/0/6-8   
    switchport mode access   
    switchport access vlan 20
    ```

3. ***Configuring Router-on-a-Stick (Inter-VLAN Routing)***    
After this I did router on a stick means divide a interface into two subinterface for different vlan so they can communicate easily, I went to the interface where the router is connected to switch (trunk port) using command ```int g 0/0/0``` and using the command ```no shut``` I turned the interface ON and then I came back to configure mode after that I divided the interface into two subinterface using ```int g 0/0/0.10``` and then ```encapsulation dot1Q 10``` after that assigned IP to the subinterface then came to configure mode and did same for the 2nd subinterface.
    
   ```
    int g 0/0/0
    no shut
    exit // come back to configure mode
    int g 0/0/0.10 // created a subinterface for vlan 10
    encapsulation dot1Q 10
    ip adderess 192.168.10.1 255.255.255.0 // assigned ip to subinterface
   ```
   For 2nd subinterface   
    
   ```
    exit // come back to configure mode   
    int g 0/0/0.20 // created a subinterface for vlan 20   
    encapsulation dot1Q 20   
    ip adderess 192.168.20.1 255.255.255.0 // assigned ip to subinterface
   ```


 4. ***Configuring DHCP Servers for Each VLAN***   
 Actually here I used DHCP servers for each VLAN so I needed to tell which DHCP is for which vlan so for that I assigned    the port which was connected to DHCP to vlan 10 and did the same for 2nd DHCP server.
  
     ```
     int g 1/0/8
     switchport mode access
     switchport access vlan 10
     ```  
     For 2nd DHCP Server
     ```
     int g 1/0/9
     switchport mode access
     switchport access vlan 20
     ```
5. ***Configuring the Trunk Port (Most Important Step)***    
Now the last step is configuring a trunk or setting a switchport to trunk mode in a switch, for that after going to configure mode I went to that interface port from where trunk is connected.
    ```
    interface GigabitEthernet 1/0/1
    switchport mode trunk
    switchport trunk allowed vlan 10,20
    ```

## Note
### Why is encapsulation dot1Q needed?
* In Router-on-a-Stick, one physical router interface is connected to a switch trunk port.
* Multiple VLANs share the same physical link
* Router must know which packet belongs to which VLAN
* ```dot1Q``` tagging solves this by adding a VLAN ID tag inside the Ethernet frame.

### Important points to remember
* encapsulation dot1Q is used only on sub-interfaces
* It is mandatory for Router-on-a-Stick
* One sub-interface = one VLAN
* Without this command, Inter-VLAN routing will not work

### What is VLAN Tagging?
* VLAN tagging is a technique used to identify which VLAN a data frame belongs to when multiple VLANs share the same physical network link.
* In simple words:   
> VLAN tagging puts a VLAN ID label inside a data frame so devices know which VLAN the data belongs to.

### Why VLAN Tagging Is Needed
Normally, a network cable can carry traffic for only one VLAN.   
But in real networks:

* One cable often carries traffic for many VLANs
* Example: Switch ↔ Switch or Switch ↔ Router links   
To avoid confusion, VLAN tagging is used.
