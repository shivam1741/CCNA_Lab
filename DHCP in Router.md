## How can we assign DHCP to router or switch(L3), we use following command for this:

1) ``` ip dhcp pool <MYPOOL> ``` = Router needs a “pool name” to store DHCP settings.
2) ``` network <192.168.1.0 255.255.255.0> ``` = Router must know which subnet to allocate addresses from.
3) ``` default-router <192.168.1.1> ``` = PCs need a default gateway to go outside their network.
4) ``` ip dhcp excluded-address <192.168.1.1 192.168.1.10> ``` = Some IPs are used for router, servers, CCTV, etc. They must not be assigned to clients.
5) ``` interface fa0/0 + ip address ``` = Configures router’s interface with a static IP.   
Reason: DHCP needs an active router interface to hand out IPs. Without this, no DHCP will work.
