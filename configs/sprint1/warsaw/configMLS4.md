# Configuração MLS4 (Multilayer Switch 4)

## Configuração VTP

```
en
conf t

vtp domain RECOMP2526M1B01
vtp password 6252pmocer
vtp mode server
vtp version 2
```

## Configuração de Port-Channel (Modo Switch)

### Port-Channel 1 (Fa0/1-2)
```
interface range Fa0/1-2
 channel-group 1 mode active
 exit

interface port-channel 1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 50
 switchport trunk allowed vlan 1-4094
 switchport trunk allowed vlan remove 99
 exit
```

### Port-Channel 3 (Fa0/3-4)
```
interface range Fa0/3-4
 channel-group 3 mode active
 exit

interface port-channel 3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 50
 switchport trunk allowed vlan 1-4094
 switchport trunk allowed vlan remove 99
 exit

end
```

## Configuração Spanning Tree

```
en
conf t

spanning-tree mode rapid-pvst

spanning-tree vlan 10 root primary
spanning-tree vlan 20 root primary

spanning-tree vlan 30 root secondary
spanning-tree vlan 40 root secondary

end
```

## Configuração IP Routing e Interfaces VLAN

```
en
conf t

ip routing
```

### Interface VLAN 10 (STAFF)
```
interface vlan 10
ip address 192.168.177.2 255.255.255.224
standby 10 ip 192.168.177.1
standby 10 priority 110
standby 10 preempt
no shutdown
exit
```

### Interface VLAN 20 (ACCOUNTING)
```
interface vlan 20
ip address 192.168.177.34 255.255.255.224
standby 20 ip 192.168.177.33
standby 20 priority 110
standby 20 preempt
no shutdown
exit
```

### Interface VLAN 30 (HR)
```
interface vlan 30
ip address 192.168.177.66 255.255.255.240
standby 30 ip 192.168.177.65
standby 30 priority 90
no shutdown
exit
```

### Interface VLAN 40 (USERS)
```
interface vlan 40
ip address 192.168.176.2 255.255.255.0
standby 40 ip 192.168.176.1
standby 40 priority 90
no shutdown
exit

end
```

## Reconfiguração Port-Channel 1 para Layer 3

```
en
conf t

no interface port-channel 1

interface range fa0/1-2
no switchport
no channel-group 1
exit

interface range fa0/1-2
channel-group 1 mode active
exit

interface port-channel 1
no switchport
ip address 192.168.177.82 255.255.255.252
no shutdown
exit

end
```

## Configuração DHCP

### Endereços Excluídos
```
en
conf t

ip dhcp excluded-address 192.168.177.1 192.168.177.3
ip dhcp excluded-address 192.168.177.33 192.168.177.35
ip dhcp excluded-address 192.168.177.65 192.168.177.67
ip dhcp excluded-address 192.168.176.1 192.168.176.3
```

### Pool VLAN 10 (STAFF)
```
ip dhcp pool VLAN10
network 192.168.177.0 255.255.255.224
default-router 192.168.177.1
dns-server 8.8.8.8
domain-name RECOMP2526M1B01.recomp.com
exit
```

### Pool VLAN 20 (ACCOUNTING)
```
ip dhcp pool VLAN20
network 192.168.177.32 255.255.255.224
default-router 192.168.177.33
dns-server 8.8.8.8
domain-name RECOMP2526M1B01.recomp.com
exit
```

### Pool VLAN 30 (HR)
```
ip dhcp pool VLAN30
network 192.168.177.64 255.255.255.240
default-router 192.168.177.65
dns-server 8.8.8.8
domain-name RECOMP2526M1B01.recomp.com
exit
```

### Pool VLAN 40 (USERS)
```
ip dhcp pool VLAN40
network 192.168.176.0 255.255.255.0
default-router 192.168.176.1
dns-server 8.8.8.8
domain-name RECOMP2526M1B01.recomp.com
exit

end
```

---

## Resumo das Configurações

### Spanning Tree

| VLAN | Função | Prioridade |
|------|--------|------------|
| 10 | Root Primary | - |
| 20 | Root Primary | - |
| 30 | Root Secondary | - |
| 40 | Root Secondary | - |

### HSRP (Hot Standby Router Protocol)

| VLAN | IP Real | IP Virtual | Prioridade | Preempt |
|------|---------|------------|------------|---------|
| 10 | 192.168.177.2 | 192.168.177.1 | 110 | Sim |
| 20 | 192.168.177.34 | 192.168.177.33 | 110 | Sim |
| 30 | 192.168.177.66 | 192.168.177.65 | 90 | Não |
| 40 | 192.168.176.2 | 192.168.176.1 | 90 | Não |

### Port-Channel (Layer 3)

| Port-Channel | Interfaces | Endereço IP |
|--------------|------------|-------------|
| Po1 | Fa0/1-2 | 192.168.177.82/30 |

### Pools DHCP

| Pool | Network | Gateway | DNS |
|------|---------|---------|-----|
| VLAN10 | 192.168.177.0/27 | 192.168.177.1 | 8.8.8.8 |
| VLAN20 | 192.168.177.32/27 | 192.168.177.33 | 8.8.8.8 |
| VLAN30 | 192.168.177.64/28 | 192.168.177.65 | 8.8.8.8 |
| VLAN40 | 192.168.176.0/24 | 192.168.176.1 | 8.8.8.8 |

### VTP

- **Domínio:** RECOMP2526M1B01
- **Password:** 6252pmocer
- **Modo:** Server
- **Versão:** 2