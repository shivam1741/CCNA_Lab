# Router on a stick lab:
* Firstly we will understand what is actual meaning of "Router on a stick"
> “Router on a Stick” is a networking setup where one physical router interface is used to route traffic between multiple VLANs (Virtual Local Area Networks) using subinterfaces.


> Normally, routers connect different networks using separate physical interfaces (like Gig0/0, Gig0/1, etc.).
But sometimes, you have only one physical connection between the router and a switch — yet you still want to route traffic between multiple VLANs.

So, we create logical (virtual) interfaces called subinterfaces on that single physical port.
