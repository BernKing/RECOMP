# Tuneis GRE
Define three IP address subnets with a /30 mask from the 10.0.0.0/8 network

Três sub-redes /30 criadas para os túneis GRE:
Oporto–Warsaw: 10.0.0.0/30
Oporto–Munich: 10.0.0.4/30
Oporto–Vault: 10.0.0.8/30

Oporto
```bash
interface Tunnel0
  ip address 10.0.0.1 255.255.255.252
  tunnel source GigabitEthernet0/0/0
  tunnel destination 192.0.2.97 255.255.255.240 (! IP Público de Warsaw)

interface Tunnel1
  ip address 10.0.0.5 255.255.255.252
  tunnel source GigabitEthernet0/0/0
  tunnel destination 193.136.60.147 255.255.255.248 (! IP Público de Munich)

interface Tunnel2
  ip address 10.0.0.9 255.255.255.252
  tunnel source GigabitEthernet0/0/0
  tunnel destination 203.0.113.2 255.255.255.252 (! IP Público do Vault)
```
Warsaw
```bash
interface Tunnel0
  ip address 10.0.0.2 255.255.255.252
  tunnel source GigabitEthernet0/0/0
  tunnel destination 209.165.200.129 255.255.255.224 (! IP Público de Oporto)
```
Munich
```bash
interface Tunnel1
  ip address 10.0.0.6 255.255.255.252
  tunnel source GigabitEthernet0/0/0
  tunnel destination 209.165.200.129 255.255.255.224 (! IP Público de Oporto)
```
Vault
```bash
interface Tunnel2
  ip address 10.0.0.10 255.255.255.252
  tunnel source GigabitEthernet0/0/0
  tunnel destination 209.165.200.129 255.255.255.224 (! IP Público do Oporto)
```

# OSPF

**Defina o Router-ID:**

- Oporto: router ospf 1 → router-id 1.1.1.1

- Warsaw: router ospf 1 → router-id 2.2.2.1

**Oporto:**

- Tunnel para Warsaw em área 0: network 10.0.0.0 0.0.0.3 area 0

- Redes locais: network <rede local> area 1

- MLS1/2: IDs: router-id 1.1.1.2 e 1.1.1.3


OSPF no Roteador Oporto:
```bash
router ospf 1
 router-id 1.1.1.1
 network 10.0.0.0 0.0.0.3 area 0          ! tunnel para Warsaw
 network 10.23.52.0 0.0.3.255 area 1      ! redes locais Oporto

```
OSPF nos MLSs de Oporto:
```bash
router ospf 1
 router-id 1.1.1.2      ! MLS1
!
router ospf 1
 router-id 1.1.1.3      ! MLS2

```


**Warsaw:**

- Tunnel para Oporto em área 0: network 10.0.0.0 0.0.0.3 area 0

- Redes locais: área 2

- MLS3/4/5: IDs: 2.2.2.2, 2.2.2.2, 2.2.2.3

OSPF no Roteador Warsaw:
```bash
router ospf 1
 router-id 2.2.2.1
 network 10.0.0.0 0.0.0.3 area 0          ! tunnel para Oporto
 network 192.168.177.0 0.0.0.31 area 2     ! redes locais Warsaw

```
OSPF nos MLSs de Warsaw:
```bash
router ospf 1
 router-id 2.2.2.2      ! MLS3
!
router ospf 1
 router-id 2.2.2.2      ! MLS4
!
router ospf 1
 router-id 2.2.2.3      ! MLS5

```



# EIGRP Configuration 

## Configure EIGRP in the Oporto tunnel interface, Munich's tunnel interface, and local networks. Do the same for the Vault. 

### EIGRP 
**Oporto:10.23.52.0/22**

```bash
!
router eigrp 100
  network 10.0.0.0 0.0.0.3        (! para tunnel Oporto-Warsaw)
  network 10.0.0.4 0.0.0.3        (! para tunnel Oporto-Munich)
  network 10.0.0.8 0.0.0.3        (! para tunnel Oporto-Vault)
  network 10.23.52.0 0.0.3.255    (! rede interna)
!
```

**Warsaw:192.168.176.0/23**

```bash
!
router eigrp 100
  network 10.0.0.0 0.0.0.3       (! para tunnel Warsaw-Oporto)
  network 192.168.176.0 0.0.1.255    (! rede interna)
!
```

**Munich:172.25.64.0/23**

```bash
!
router eigrp 100
  network 10.0.0.4 0.0.0.3       (! para tunnel Munich-Oporto)
  network 172.25.64.0 0.0.1.255    (! rede interna)
!
```
**Vault:10.31.83.0/24**

```bash
!
router eigrp 100
  network 10.0.0.8 0.0.0.3       (! para tunnel Vault-Oporto)
  network 10.31.83.0 0.0.0.255    (! rede interna)

!
```





## Configure Routing redistribution in Oporto between OSPF and EIGRP to achieve full connectivity. 

### Redistribuição de Rotas

No Oporto:

```bash
router ospf 1
  redistribute eigrp 100 subnets
router eigrp 100
  redistribute ospf 1
```

No Oporto:

```bash
router ospf 1
  redistribute eigrp 101 subnets
router eigrp 101
  redistribute ospf 1
```

## Test connectivity between the hosts in different locations. 



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

### Port Security
Para as VLANs 10, 20, 30, 40:

```bash
interface range Fa0/1 - 24
  switchport port-security maximum 2
  switchport port-security violation shutdown
  switchport port-security mac-address sticky
  switchport port-security

```

### REDES
OPorto - 10.23.52.0/22 
Warsaw - 192.168.176.0/23 
Munich - 172.25.64.0/23
vault - 10.31.83.0/24
VTP domain - RECOMP2526M1B01
DHCP domain - RECOMP2526M1B01.recomp.com 