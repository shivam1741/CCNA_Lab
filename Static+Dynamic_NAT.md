# Static and Dynamic combined
* In this lab we are going to configure both static and dynamic NAT.
![Static+Dynamic](https://github.com/shivam1741/CCNA_Lab/blob/Image/Screenshot%202026-01-08%20121845.png)
## Configuration of Static NAT
### Configuration of router ports
* Firstly I configured the IP to the both ports of the router and told which port would be the outside NAT or inside NAT.  
***After going to configure mode*** 
   ```|
   int g 0/0/0
   ip add 10.1.1.254 255.255.255.0

   ip nat inside
   ```

   ```
   int g 0/0/1
   ip add 8.8.8.100 255.255.255.0

   ip nat outside
   ```
### Configuration of the route 
* Although we don’t need to tell the route to the router unless we have two or more routers, many people think why we need it here since there is only one router. This doubt is correct, but there is an Internet server (which acts like an ISP or a router), and that is why we need to tell the route to the router.    
***After going back to configure mode***
   ```
   ip route 0.0.0.0 0.0.0.0 8.8.8.8
   ```
 * ***Meaning of this command***
    > 0.0.0.0 (destination network) --> If a packet’s destination network matches this route, send it to the next router.     
    > 0.0.0.0 (subnet mask)    
    > 8.8.8.8 (next-hop IP)   

   Here this command tells the router to send all traffic for unknown destinations to the ISP router at 8.8.8.8.
### Configuration of the static NAT
* We'll configure Static NAT so that outside PC can access the HTTP and FTP servers.
* **For HTTP**   
  ***After going back to configure mode*** 
   ```
   ip nat inside source static tcp 10.1.1.100 80 8.8.8.200 80
   ```
* When we use ping from the command prompt to access the HTTP server, it will not work because ping uses ICMP packets. In this NAT command, we have specified that the HTTP server should be accessed only using TCP on port 80. Therefore, this NAT rule works only for HTTP traffic, and it will work only when we access the server through a web browser, not through ping.
* This command means that only TCP packets using port 80 coming from 10.1.1.100 are translated to the public IP 8.8.8.200 on port 80.
* **For FTP**    
***After going back to configure mode***
   ```
   ip nat inside source static 10.1.1.101 8.8.8.201
   ```
* Here, we are not specifying any particular protocol or port, so this NAT rule applies to all types of packets. Any traffic coming from the inside local IP address 10.1.1.101 will be translated to the inside global IP address 8.8.8.201, regardless of the protocol.

## Configuration of Dynamic NAT

  
