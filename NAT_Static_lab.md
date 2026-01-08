# NAT Static Lab
* In this lab we'll learn all the steps and commands using that we can configure static NAT.
![Static_NAT](https://github.com/shivam1741/CCNA_Lab/blob/Image/Screenshot%202025-12-31%20120045.png)

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
### 
