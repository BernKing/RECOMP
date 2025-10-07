# Configuração MLS3 (Multilayer Switch 3)

## Configuração de VLANs

```
en
conf t

vlan 10
name STAFF
exit

vlan 20
name ACCOUNTING
exit

vlan 30
name HR
exit

vlan 40
name USERS
exit

vlan 50
name NATIVE
exit

vlan 99
name BLACKHOLE
exit
```

## Configuração VTP

```
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

### Port-Channel 2 (Fa0/3-4)
```
interface range Fa0/3-4
 channel-group 2 mode active
exit

interface port-channel 2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 50
 switchport trunk allowed vlan 1-4094
 switchport trunk allowed vlan remove 99
exit
end
```

## Ativação de Routing e Reconfiguração para Modo Layer 3

```
en
conf t

ip routing
```

### Remoção de Port-Channels Anteriores
```
no interface port-channel 1
no interface port-channel 2

interface range fa0/1-2
no switchport
no channel-group 1
exit

interface range fa0/3-4
no switchport
no channel-group 2
exit
```

### Reconfiguração Port-Channel 1 (Layer 3)
```
interface range fa0/1-2
channel-group 1 mode active
exit

interface port-channel 1
no switchport
ip address 192.168.177.81 255.255.255.252
no shutdown
exit
```

### Reconfiguração Port-Channel 2 (Layer 3)
```
interface range fa0/3-4
channel-group 2 mode active
exit

interface port-channel 2
no switchport
ip address 192.168.177.85 255.255.255.252
no shutdown
exit

end
```

## Configuração de Interface P2P para Router BR1

```
en
conf t

ip routing

interface GigabitEthernet0/1
no switchport
ip address 192.168.177.90 255.255.255.252
no shutdown
exit

end
```

---

## Resumo das Configurações

### VLANs Configuradas

| VLAN ID | Nome | Função |
|---------|------|--------|
| 10 | STAFF | Pessoal |
| 20 | ACCOUNTING | Contabilidade |
| 30 | HR | Recursos Humanos |
| 40 | USERS | Utilizadores |
| 50 | NATIVE | VLAN Nativa |
| 99 | BLACKHOLE | VLAN Bloqueada |

### Port-Channels (Layer 3)

| Port-Channel | Interfaces | Endereço IP | Máscara |
|--------------|------------|-------------|---------|
| Po1 | Fa0/1-2 | 192.168.177.81 | 255.255.255.252 |
| Po2 | Fa0/3-4 | 192.168.177.85 | 255.255.255.252 |

### Interface P2P para Router

| Interface | Endereço IP | Máscara | Destino |
|-----------|-------------|---------|---------|
| GigabitEthernet0/1 | 192.168.177.90 | 255.255.255.252 | BR1 (192.168.177.89) |

### VTP

- **Domínio:** RECOMP2526M1B01
- **Password:** 6252pmocer
- **Modo:** Server
- **Versão:** 2