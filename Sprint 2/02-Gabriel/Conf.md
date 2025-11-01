# OSPF Configuration 
Configure the OSPF router-id in the Oporto and Warsaw routers to 1.1.1.1 and 2.2.2.1 
respectively. In the Oporto the MLSs configure the following router-ids MLS1 1.1.1.2 e 
MLS2 1.1.1.3. In Warsaw the MLSs configure the following router-ids MLS3 2.2.2.2 e 
MLS4 2.2.2.2 and MLS5 2.2.2.3. 
Configure OSPF in Oporto attaching the previously created tunnel interface to Warsaw 
to area 0 and Oporto's local networks to area 1. Don't forget to configure OSPF in 
Oporto's MLSs. 
Configure OSPF in Warsaw attaching the previously created tunnel interface to Oporto 
to area 0 and Warsaw's local networks to area 2. 

# PAT Configuration 
Configure PAT to allow all users to access the INTERNET. 
Test connectivity between hosts and Google. 