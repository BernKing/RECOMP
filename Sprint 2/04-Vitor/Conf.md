# EIGRP Configuration 
Configure EIGRP in the Oporto tunnel interface, Munich's tunnel interface, and local 
networks. Do the same for the Vault. 
Configure Routing redistribution in Oporto between OSPF and EIGRP to achieve full 
connectivity. 

Test connectivity between the hosts in different locations. 

# Port Security Configuration 

## Security Breach Report (colocar imagem)
 ***The Warsaw branch reported a security 
breach, unauthorised personnel had access 
to one of the available ports in the branch.  
As a result, the RECOMP Corporation has 
decided to implement port security in every 
branch.***


Configure and activate port security to shutdown the interface if more than 2 MAC 
addresses are registered in an individual interface for VLAN 10, 20, 30, and 40. Make 
sure that the interface registers every MAC address that is recognised. 

### REDES
OPorto - 10.23.52.0/22 
Warsaw - 192.168.176.0/23 
Munich - 172.25.64.0/23
vault - 10.31.83.0/24
VTP domain - RECOMP2526M1B01
DHCP domain - RECOMP2526M1B01.recomp.com 