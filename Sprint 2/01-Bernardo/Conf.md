# Routing configuration between branches 
 - Check the routing table and statically configure a default route with the same gateway received from the DHCP server in Oporto and Warsaw routers. 
 - Configure default routes in both MLSs pointing to the Oporto router interfaces and do the same for Warsaw MLS3, MLS4 and MLS5. 
# GRE configuration 
 - Define three IP address subnets with a /30 mask from the 10.0.0.0/8 network, one for each connection between Oporto/Warsaw, Oporto/Munich and Oporto/The Vault. 
 - To establish direct connections between the HQ and the Warsaw, Munich and the Vault branches to bypass public Internet routing for internal traffic. 
 - Configure a GRE tunnels between Oporto-Warsaw, Oporto-Munich and Oporto/The Vault using the tree subnets created previously. 

