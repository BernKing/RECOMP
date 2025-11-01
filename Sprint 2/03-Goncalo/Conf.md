# ACLs 
Block all spoofing, as far as possible in all the routers (Oporto, Warsaw, Munich). Internal 
spoofing from local networks. External spoofing in traffic incoming from the Internet.  
All traffic directed to the routers (with a destination IPv4 node address belonging to the 
routers) is to be blocked, except for the traffic required for the current features to work 
(GRE, DHCP, OSPF, EIGRP, among others…). 
The remaining traffic passing through the router should be allowed. 