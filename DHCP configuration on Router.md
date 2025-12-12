# DHCP configuration on Router
## Firstly we'll understand it
### What is DHCP?
* DHCP = Dynamic Host Configuration Protocol
* It is a service that automatically gives IP address to devices in a network.
* Without DHCP → we must manually assign IP to every PC
* With DHCP → PC gets IP automatically in 3–5 seconds

### Why do we need DHCP?
* Imagine 200 computers in an office.
* Assigning IP manually to all → waste of time, errors, duplicates.
* DHCP solves this by automatically giving:
     * IP address
     * Subnet mask
     * Default gateway
     * DNS servers

### Using DHCP in a Network
For providing DHCP services in a network, we can either use a dedicated DHCP server or configure a router or Layer 3 switch to function as a DHCP server after enabling the required settings.
### DHCP configuration on Router(Cisco Project)
![DHCP configuration on Router](https://github.com/shivam1741/CCNA_Lab/blob/Image/Screenshot%202025-12-12%20105724.png)
1. ***Assigning IP to router port:***    
* Firstly I assigned the IP to the router for this I came to configure mode and then went to the port where the router is connected using ```int g 0/0/0 ``` after configured IP to that using ```IP Add 192.168.1.1 255.255.255.192```.
    ```
    configure
    int g 0/0/0
    IP Add 192.168.1.1 255.255.255.192
    ```
2. ***Configure DHCP to router***
* For this firstly I created DHCP pool name using ```ip dhcp pool <NAME>``` and then told DHCP which network to use for assigning IPs using ```network 192.168.1.0 255.255.255.192``` after that told DHCP which is the default gateway for all device whom it is going to assign IPs using ```default-router 192.168.1.1```, then we could assign DNS if we needed using ```dns-server 8.8.8.8```.   
   ```
   ip dhcp pool <NAME>
   network 192.168.1.0 255.255.255.192
   default-router 192.168.1.1
   dns-server 8.8.8.8(optinal)
   ```
* After that we can exclude some IP range(If needed) that we don't want to be assigned to devices using ```ip dhcp ip dhcp excluded-address <starting ip > <ending ip>```.
* It can be done after coming back to configure mode.  
   ```
   ip dhcp ip dhcp excluded-address <starting ip > <ending ip>
   ```
  
