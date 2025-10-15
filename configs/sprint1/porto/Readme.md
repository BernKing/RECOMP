


## 1. Startup configurations and verifications ##
### Configurar Ligação ao Service Provider(SP) ###
```
Oporto(config)#inter g0/0/0
Oporto(config-if)#ip address dhcp
Oporto(config-if)#no shut

Oporto#ping 8.8.8.8

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms

```


*** Configuração Ponto a Ponto do Router aos MLS1 e MLS2 ***


```
Oporto(config)#interface g0/0
Oporto(config-if)#ip address 10.23.55.193 255.255.255.252
Oporto(config-if)#no shutdown

Oporto(config)#inter g0/1
Oporto(config-if)#ip address 10.23.55.197 255.255.255.252
Oporto(config-if)#no shutdown
```




## 2. Address Scheme ##

### 2.1. HQ networks ###

*** Tabela de endereços de rede ***

| Rede                         | Endereço de rede   | Endereço de broadcast | Máscara            | Primeiro endereço válido | Último endereço válido |
|------------------------------|--------------------|-----------------------|--------------------|-------------------------|-----------------------|
| USERS - 500 nodes VLAN 40    | 10.23.52.0         | 10.23.53.255          | 255.255.254.0      | 10.23.52.1              | 10.23.53.254          |
| ACOUTING - 200 nodes VLAN 20 | 10.23.54.0         | 10.23.54.255          | 255.255.255.0      | 10.23.54.1              | 10.23.54.254          |
| HR - 100 nodes VLAN 30       | 10.23.55.0         | 10.23.55.127          | 255.255.255.128    | 10.23.55.1              | 10.23.55.126          |
| STAFF - 50 nodes VLAN 10     | 10.23.55.128       | 10.23.55.191          | 255.255.255.192    | 10.23.55.129            | 10.23.55.190          |
| MLS1-Router - 2 nodes                  | 10.23.55.192       | 10.23.55.195          | 255.255.255.252    | 10.23.55.193            | 10.23.55.194          |
| MLS2-Router - 2 nodes                  | 10.23.55.196       | 10.23.55.199          | 255.255.255.252    | 10.23.55.197            | 10.23.55.198          |


## 3. Configure HQ networks. ##
### 3.1. VLAN'S configuration and connections ###

- Start by configuring the link aggregation between HQ-MLS1 and HQ
MLS2 as a trunk allowing all VLANs. 

### Configurar Link Agregation MLS1 ###

```


interface range FastEthernet0/1-2
 description TRUNK Link-Agregation to MLS2
 switchport trunk native vlan 50
 switchport trunk allowed vlan 1-98,100-1005
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode active
 channel-protocol lacp


interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 50
 switchport trunk allowed vlan 1-98,100-1005
 no shutdown

MLS1#show etherchannel summary
Flags:  D - down        P - in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 1
Number of aggregators:           1

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

1      Po1(SU)           LACP   Fa0/1(P) Fa0/2(P) 


```

### Configurar Link Agregation MLS2 ###

```
interface range FastEthernet0/1-2
 description TRUNK Link-Agregation to MLS2
 switchport trunk native vlan 50
 switchport trunk allowed vlan 1-98,100-1005
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode active
 channel-protocol lacp


interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 50
 switchport trunk allowed vlan 1-98,100-1005
 no shutdown

MLS2#show ether summary
Flags:  D - down        P - in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 1
Number of aggregators:           1

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

1      Po1(SU)           LACP   Fa0/1(P) Fa0/2(P) 

```


 - Configure all layer 2 links as trunks, also allowing all VLANs to pass 
except for the VLAN 99.
 - Configure all trunks to use VLAN 50 as its native VLAN. 

### Trunk MLS1 ##

**MLS1 trunk F0/3**
```
MLS1(config)# interface fastEthernet 0/3
MLS1(config-if)# switchport trunk encapsulation dot1q
MLS1(config-if)# switchport mode trunk
MLS1(config-if)# switchport trunk native vlan 50
MLS1(config-if)#switchport trunk allowed vlan 1-98,100-1005

```
**SW1 trunk F0/3**
```
SW1(config)# interface fastEthernet 0/3
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk native vlan 50
SW1(config-if)#switchport trunk allowed vlan 1-98,100-1005
```
**MLS1 trunk F0/4**
```
MLS1(config)# interface fastEthernet 0/4
MLS1(config-if)# switchport trunk encapsulation dot1q
MLS1(config-if)# switchport mode trunk
MLS1(config-if)# switchport trunk native vlan 50
MLS1(config-if)#switchport trunk allowed vlan 1-98,100-1005
```
**SW1 Trunk F0/4**
```
SW1(config)# interface fastEthernet 0/4
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk native vlan 50
SW1(config-if)#switchport trunk allowed vlan 1-98,100-1005
```

### Trunk MLS2 ##

**MLS2 trunk F0/3**
```
MLS2(config)# interface fastEthernet 0/3
MLS2(config-if)# switchport trunk encapsulation dot1q
MLS2(config-if)# switchport mode trunk
MLS2(config-if)# switchport trunk native vlan 50
MLS2(config-if)#switchport trunk allowed vlan 1-98,100-1005

```
**SW1 trunk F0/3**
```
SW2(config)# interface fastEthernet 0/3
SW2(config-if)# switchport mode trunk
SW2(config-if)# switchport trunk native vlan 50
SW2(config-if)#switchport trunk allowed vlan 1-98,100-1005
```

**MLS2 trunk F0/4**
```
MLS2(config)# interface fastEthernet 0/4
MLS2(config-if)# switchport trunk encapsulation dot1q
MLS2(config-if)# switchport mode trunk
MLS2(config-if)# switchport trunk native vlan 50
MLS2(config-if)#switchport trunk allowed vlan 1-98,100-1005
```
**SW2 Trunk F0/4**

```
SW2(config)# interface fastEthernet 0/4
SW2(config-if)# switchport mode trunk
SW2(config-if)# switchport trunk native vlan 50
SW2(config-if)#switchport trunk allowed vlan 1-98,100-1005
```


- Configure the MLS switches as VTP Servers using the VTP domain of 
RECOMP2526TTTGG (TTT=class, GG=group number), the password 
6252pmocer and all the L2 switches as VTP Clients. 

### VTP Servers/clients###

```
MLS1(config)vtp mode server
MLS1(config)vtp domain RECOMP2526M1B01
MLS1(config)vtp password 6252pmocer
MLS1(config)vtp version 2

MLS2(config)vtp mode server
MLS2(config)vtp domain RECOMP2526M1B01
MLS2(config)vtp password 6252pmocer
MLS2(config)vtp version 2

SW1(config)vtp mode client
SW1(config)vtp domain RECOMP2526M1B01
SW1(config)vtp password 6252pmocer
SW1(config)vtp version 2

SW2(config)vtp mode client
SW2(config)vtp domain RECOMP2526M1B01
SW2(config)vtp password 6252pmocer
SW2(config)vtp version 2
```

- Configure the following VLANs in one of the VTP server switches: VLAN 
10 (STAFF), VLAN 20 (ACCOUNTING), VLAN 30 (HR), VLAN 40 
(USERS), VLAN 50 (NATIVE), VLAN 99 (BLACKHOLE). 

*** Configurar VLAN MLS1***

```
MLS1(config)#vlan 10
MLS1(config-vlan)#name STAFF
MLS1(config-vlan)#vlan 20
MLS1(config-vlan)#name ACCOUNTING
MLS1(config-vlan)#vlan 30
MLS1(config-vlan)#name HR
MLS1(config-vlan)#vlan 40
MLS1(config-vlan)#name USERS
MLS1(config-vlan)#vlan 50
MLS1(config-vlan)#name NATIVE
MLS1(config-vlan)#vlan 99
MLS1(config-vlan)#name BLACKHOLE


vlan 10
name STAFF
vlan 20
name ACCOUNTING
vlan 30
name HR
vlan 40
name USERS
vlan 50
name NATIVE
vlan 99
name BLACKHOLE
```






### 3.2. Rapid Spanning Tree Protocol (STP) 

 - Force MLS1 to become the ROOT bridge for VLAN 10 and 20 and the 
secondary root bridge for VLAN 30 and 40 and to use rapid-STP. 
 - MLS2 should have the opposite configuration of MLS1 regarding the STP 
position. 



```
MLS1(config)#spanning-tree mode rapid-pvst
MLS1(config)#spanning-tree vlan 10 root primary
MLS1(config)#spanning-tree vlan 20 root primary
MLS1(config)#spanning-tree vlan 30 root secondary
MLS1(config)#spanning-tree vlan 40 root secondary

MLS2(config)#spanning-tree mode rapid-pvst
MLS2(config)#spanning-tree vlan 30 root primary
MLS2(config)#spanning-tree vlan 40 root primary
MLS2(config)#spanning-tree vlan 10 root secondary
MLS2(config)#spanning-tree vlan 20 root secondary

```




```

MLS1#show spanning-tree vlan 10
VLAN0010
  Spanning tree enabled protocol rstp
  Root ID    Priority    24586
             Address     0005.5E45.6D00
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    24586  (priority 24576 sys-id-ext 10)
             Address     0005.5E45.6D00
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/3            Desg FWD 19        128.3    P2p
Fa0/4            Desg FWD 19        128.4    P2p

MLS2#show spanning-tree vlan 10
VLAN0010
  Spanning tree enabled protocol ieee
  Root ID    Priority    24586
             Address     0005.5E45.6D00
             Cost        38
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32778  (priority 32768 sys-id-ext 10)
             Address     0001.64A8.8569
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/3            Altn BLK 19        128.3    P2p
Fa0/4            Root FWD 19        128.4    P2p



```

### 3.3. Configure HSRP ###


- Configure the necessary switched virtual interface (SVI) for each VLAN in 
each MLS. Assing the correct address and configure HSRP. 

- Match the active HSRP MLS for each VLAN with the MLS that is acting as 
the root bridge for the same VLAN. 


*** (SVI) MLS1 e MLS2 ***

*** MLS1 ***
```
interface vlan 10
 ip address 10.23.55.129 255.255.255.192
 standby 10 ip 10.23.55.190
 standby 10 priority 150
 standby 10 preempt
interface vlan 20
 ip address 10.23.54.1 255.255.255.0
 standby 20 ip 10.23.54.254
 standby 20 priority 150
 standby 20 preempt
interface vlan 30
 ip address 10.23.55.2 255.255.255.128
 standby 30 ip 10.23.55.126
 standby 30 priority 100
 standby 30 preempt
interface vlan 40
 ip address 10.23.52.2 255.255.254.0
 standby 40 ip 10.23.53.254
 standby 40 priority 100
 standby 40 preempt

 ```
*** MLS2 ***
 ```
interface vlan 10
 ip address 10.23.55.130 255.255.255.192
 standby 10 ip 10.23.55.190
 standby 10 priority 100
 standby 10 preempt
interface vlan 20
 ip address 10.23.54.2 255.255.255.0
 standby 20 ip 10.23.54.254
 standby 20 priority 100
 standby 20 preempt
interface vlan 30
 ip address 10.23.55.1 255.255.255.128
 standby 30 ip 10.23.55.126
 standby 30 priority 150
 standby 30 preempt
interface vlan 40
 ip address 10.23.52.1 255.255.254.0
 standby 40 ip 10.23.53.254
 standby 40 priority 150
 standby 40 preempt

 ```

 ```
MLS1#show standby brief
                     P indicates configured to preempt.
                     |
Interface   Grp  Pri P State    Active          Standby         Virtual IP
Vl10        10   150 P Active   local           10.23.55.130    10.23.55.190   
Vl20        20   150 P Active   local           10.23.54.2      10.23.54.254   
Vl30        30   100 P Standby  10.23.55.1      local           10.23.55.126   
Vl40        40   100 P Standby  10.23.52.1      local           10.23.53.254 

MLS2#show standby brief

                     P indicates configured to preempt.
                     |
Interface   Grp  Pri P State    Active          Standby         Virtual IP
Vl10        10   100 P Standby  10.23.55.129    local           10.23.55.190   
Vl20        20   100 P Standby  10.23.54.1      local           10.23.54.254   
Vl30        30   150 P Active   local           10.23.55.2      10.23.55.126   
Vl40        40   150 P Active   local           10.23.52.2      10.23.53.254   

```
- Configure routed ports on both MLSs. 

MLS1

```

MLS1(config)#interface g0/1
MLS1(config-if)#no switchport
MLS1(config-if)#ip address 10.23.55.194 255.255.255.252
MLS1(config-if)#no shutdown


```
MLS2

```

MLS2(config)#interface g0/1
MLS2(config-if)#no switchport
MLS2(config-if)#ip address 10.23.55.198 255.255.255.252
MLS2(config-if)#no shutdown


```

### 3.4. Configure Layer 2 Switch ###


*** Configurar VLANs SW1 ***


```
SW1(config)#interface Range FastEthernet0/5-8
SW1(config-if-range)#switchport access vlan 10
SW1(config-if-range)#interface Range FastEthernet0/9-12
SW1(config-if-range)#switchport access vlan 20
SW1(config-if-range)#interface Range FastEthernet0/13-16
SW1(config-if-range)#switchport access vlan 30
SW1(config-if-range)#interface Range FastEthernet0/17-20
SW1(config-if-range)#switchport access vlan 40
SW1(config-if-range)#interface Range Fa0/1, Fa0/2, Fa0/21, Fa0/22, Fa0/23, Fa0/24, Gig0/1, Gig0/2
SW1(config-if-range)#switchport access vlan 99
SW1(config-if-range)#shutdown



*** Configurar VLANs SW2 ***



SW2(config)#interface Range FastEthernet0/5-8
SW2(config-if-range)#switchport access vlan 10
SW2(config-if-range)#interface Range FastEthernet0/9-12
SW2(config-if-range)#switchport access vlan 20
SW2(config-if-range)#interface Range FastEthernet0/13-16
SW2(config-if-range)#switchport access vlan 30
SW2(config-if-range)#interface Range FastEthernet0/17-20
SW2(config-if-range)#switchport access vlan 40
SW2(config-if-range)#interface Range Fa0/1, Fa0/2, Fa0/21, Fa0/22, Fa0/23, Fa0/24, Gig0/1, Gig0/2
SW2(config-if-range)#switchport access vlan 99
SW2(config-if-range)#shutdown


```

### 3.5. Configure DHCP ###


- Configure duplicated DHCP pools in both MLS for each VLAN using the 
address scheme created.


```
ip dhcp pool STAFF
network 10.23.55.128 255.255.255.192
default-router 10.23.55.190
dns-server 8.8.8.8
domain-name RECOMP2526M1B01.recomp.com
ip dhcp excluded-address 10.23.55.129 10.23.55.130
ip dhcp excluded-address 10.23.55.190 10.23.55.190
ip dhcp pool ACCOUNTING
network 10.23.54.0 255.255.255.0
default-router 10.23.54.254
dns-server 8.8.8.8
domain-name RECOMP2526M1B01.recomp.com
ip dhcp excluded-address 10.23.54.1 10.23.54.2
ip dhcp excluded-address 10.23.54.254 10.23.54.254
ip dhcp pool HR
network 10.23.55.0 255.255.255.128
default-router 10.23.55.126
dns-server 8.8.8.8
domain-name RECOMP2526M1B01.recomp.com
ip dhcp excluded-address 10.23.55.1 10.23.55.2
ip dhcp excluded-address 10.23.55.126 10.23.55.126
ip dhcp pool USERS
network 10.23.52.0 255.255.254.0
default-router 10.23.53.254
dns-server 8.8.8.8
domain-name RECOMP2526M1B01.recomp.com
ip dhcp excluded-address 10.23.52.1 10.23.52.2
ip dhcp excluded-address 10.23.53.254 10.23.53.254





```
- Excluded the already used addresses on the configuration. 



#### Configurações para roteamento ####



*** MLS ****

*** Default route do MLS1 para Interface do Roter ***

```
MLS1(config)#ip route 0.0.0.0 0.0.0.0 10.23.55.193 

```
*** Default route do MLS2 para Interface do Roter ***


```
MLS2(config)#ip route 0.0.0.0 0.0.0.0 10.23.55.197


```

*** ROUTER ***


*** ACL NAT ROUTER ***

```
ip nat inside source list 1 interface GigabitEthernet0/0/0 overload
access-list 1 permit 10.23.55.128 0.0.0.63
access-list 1 permit 10.23.54.0 0.0.0.255
access-list 1 permit 10.23.55.0 0.0.0.127
access-list 1 permit 10.23.52.0 0.0.1.255

Oporto(config)#interface GigabitEthernet0/0
Oporto(config-if)# ip nat inside
Oporto(config)#interface GigabitEthernet0/1
Oporto(config-if)# ip nat inside
Oporto(config-if)#interface GigabitEthernet0/0/0
Oporto(config-if)#ip nat outside

```

*** ROTAS Estáticas do ROUTER ***

```



ip route 10.23.52.0 255.255.252.0 10.23.55.194 
ip route 10.23.52.0 255.255.252.0 10.23.55.198 5
ip route 0.0.0.0 0.0.0.0 GigabitEthernet0/0/0


```





***  VLAN 10 *** 

```
 C:\>tracert 8.8.8.8

Tracing route to 8.8.8.8 over a maximum of 30 hops: 

  1   231 ms    17 ms     63 ms     10.23.55.129
  2   161 ms    *         76 ms     10.23.55.193
  3   191 ms    94 ms     87 ms     209.165.200.158
  4   *         77 ms     171 ms    8.8.8.8

Trace complete.


```

*** VLAN 20 ***

```
C:\>tracert 8.8.8.8

Tracing route to 8.8.8.8 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      10.23.54.1
  2   0 ms      0 ms      1 ms      10.23.55.193
  3   0 ms      1 ms      0 ms      209.165.200.158
  4   0 ms      0 ms      0 ms      8.8.8.8

Trace complete.

```
***  VLAN 30 *** 

```
C:\>tracert 8.8.8.8

Tracing route to 8.8.8.8 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      10.23.55.1
  2   0 ms      0 ms      0 ms      10.23.55.193
  3   0 ms      0 ms      0 ms      209.165.200.158
  4   0 ms      0 ms      0 ms      8.8.8.8

Trace complete.


```
*** VLAN 40 ***

```
C:\>tracert 8.8.8.8

Tracing route to 8.8.8.8 over a maximum of 30 hops: 

  1   10 ms     0 ms      0 ms      10.23.52.1
  2   0 ms      0 ms      0 ms      10.23.55.193
  3   0 ms      0 ms      0 ms      209.165.200.158
  4   0 ms      0 ms      0 ms      8.8.8.8

Trace complete.


```


*** Ligação redundante ***
MLS1:
```
interface Vlan50
 ip address 10.23.55.205 255.255.255.252
 no shutdown
ip route 0.0.0.0 0.0.0.0 10.23.55.206 10
```
MLS2:
```

interface Vlan50
 ip address 10.23.55.206 255.255.255.252
 no shutdown

 ip route 0.0.0.0 0.0.0.0 10.23.55.205 10
 ```



### testes

### G0/1 do router down

VLAN 10
C:\>tracert 8.8.8.8

Tracing route to 8.8.8.8 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      10.23.55.129
  2   0 ms      0 ms      0 ms      10.23.55.193
  3   0 ms      0 ms      0 ms      209.165.200.158
  4   0 ms      0 ms      0 ms      8.8.8.8

Trace complete.

VLAN 20

C:\>tracert 8.8.8.8

Tracing route to 8.8.8.8 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      10.23.55.129
  2   0 ms      0 ms      0 ms      10.23.55.193
  3   0 ms      0 ms      0 ms      209.165.200.158
  4   0 ms      0 ms      0 ms      8.8.8.8

Trace complete.

VLAN 30


C:\>tracert 8.8.8.8

Tracing route to 8.8.8.8 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      10.23.55.1
  2   0 ms      0 ms      0 ms      10.23.55.2
  3   0 ms      0 ms      0 ms      10.23.55.193
  4   0 ms      0 ms      0 ms      209.165.200.158
  5   0 ms      0 ms      0 ms      8.8.8.8

Trace complete.

VLAN 40


C:\>tracert 8.8.8.8

Tracing route to 8.8.8.8 over a maximum of 30 hops: 

  1   *         0 ms      0 ms      10.23.52.1
  2   0 ms      0 ms      0 ms      10.23.52.2
  3   0 ms      0 ms      0 ms      10.23.55.193
  4   0 ms      0 ms      0 ms      209.165.200.158
  5   0 ms      0 ms      0 ms      8.8.8.8

Trace complete.

#### G0/1 do router shutdown


VLAN 10


C:\>tracert 8.8.8.8

Tracing route to 8.8.8.8 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      10.23.55.129
  2   *         0 ms      0 ms      10.23.55.130
  3   0 ms      0 ms      0 ms      10.23.55.197
  4   0 ms      0 ms      0 ms      209.165.200.158
  5   0 ms      0 ms      0 ms      8.8.8.8

Trace complete.


VLAN 20

C:\>tracert 8.8.8.8

Tracing route to 8.8.8.8 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      10.23.54.1
  2   *         0 ms      0 ms      10.23.54.2
  3   *         0 ms      0 ms      10.23.55.197
  4   0 ms      0 ms      0 ms      209.165.200.158
  5   0 ms      0 ms      0 ms      8.8.8.8

Trace complete.

VLAN 30

C:\>tracert 8.8.8.8

Tracing route to 8.8.8.8 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      10.23.55.1
  2   0 ms      0 ms      0 ms      10.23.55.197
  3   0 ms      0 ms      0 ms      209.165.200.158
  4   0 ms      0 ms      0 ms      8.8.8.8

Trace complete.


VLAN 40

C:\>tracert 8.8.8.8

Tracing route to 8.8.8.8 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      10.23.52.1
  2   0 ms      0 ms      0 ms      10.23.55.197
  3   0 ms      0 ms      0 ms      209.165.200.158
  4   0 ms      0 ms      0 ms      8.8.8.8

Trace complete.

### TESTE STANDBY VLAN

*** VLAN 10 ShutDown MLS1 ***
```
MLS2#show standby brief
                     P indicates configured to preempt.
                     |
Interface   Grp  Pri P State    Active          Standby         Virtual IP
Vl10        10   100 P Active   local           unknown         10.23.55.190   
Vl20        20   100 P Standby  10.23.54.1      local           10.23.54.254   
Vl30        30   150 P Active   local           10.23.55.2      10.23.55.126   
Vl40        40   150 P Active   local           10.23.52.2      10.23.53.254  
```
