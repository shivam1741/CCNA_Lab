# Inter-VLAN Routing using Router-on-a-Stick with DHCP
## Project Overview
* This is a well-designed project where multiple networking concepts were implemented together.
* In this lab, I used a switch, a router, and multiple end devices.
* I created three VLANs on the switch, and each VLAN contained at least two end devices.
* To assign IP addresses to all end devices in different VLANs, I configured the router to function as a DHCP server.

## Cisco Lab
![Cisco](https://github.com/shivam1741/CCNA_Lab/blob/Image/Screenshot%202025-12-13%20100812.png)
### Configuration of VLANs

* In this lab, I created three VLANs on a switch. To configure the VLANs, I entered the switch’s global configuration mode and created VLAN 10, assigning it the name NOC.
* Similarly, I created VLAN 20 and named it SOC, and VLAN 30 named HR.
  ```
  vlan 10
  name NOC

  vlan 20
  name SOC

  vlan 30
  name HR
  ```
### Configuration of Router-on-a-Stick

* I configured Router-on-a-Stick on the router to enable communication between different VLANs (Inter-VLAN routing).
* For this configuration, I entered the router’s global configuration mode and accessed the interface connected to the switch using ```int g0/0/0```. I then enabled the interface using the ```no shut``` command and returned to global configuration mode.
* Next, I divided this physical interface into three subinterfaces and assigned IP addresses to each subinterface individually.
  ```
  int Gig 0/0/0.10
  encapsulation dot1Q 10
  ip address 192.168.1.1 255.255.255.192

  interface Gig 0/0/0.20
  encapsulation dot1Q 20
  ip address 192.168.1.66 255.255.255.192

  interface Gig 0/0/0.30
  encapsulation dot1Q 30
  ip address 192.168.1.130 255.255.255.192
  ```

### Configuration of DHCP on Router for All Three VLANs

* In this configuration, I assigned IP addresses to all end devices in each VLAN using the DHCP concept.
* I created three separate DHCP pools on the router—VLAN_10, VLAN_20, and VLAN_30—corresponding to each VLAN, so that IP addresses could be automatically assigned to all devices within their respective VLANs.
  ```
  ip Dhcp pool VLAN_10
  network 192.168.1.0 255.255.255.192
  default-router 192.168.1.1
  dns-server 8.8.8.8(optinal)

  ip Dhcp pool VLAN_10
  network 192.168.1.64 255.255.255.192
  default-router 192.168.1.66
  dns-server 8.8.8.8(optinal)

  ip Dhcp pool VLAN_10
  network 192.168.1.128 255.255.255.192
  default-router 192.168.1.130
  dns-server 8.8.8.8(optinal)
  ```
### Configuration of Trunk Port

* For this configuration, I entered the switch’s global configuration mode and accessed the interface connected to the router using int g1/0/1.
* I then configured this interface as a trunk port using the following commands to allow traffic from multiple VLANs to pass between the switch and the router.
  ```
  int g 1/0/1
  switchport mode trunk
  switchport trunk allowed vlan 10,20,30
  ```
