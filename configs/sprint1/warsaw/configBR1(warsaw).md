# Configuração Router BR1

## Configuração Básica
```
en
conf t
hostname BR1
```

## Configuração de Interfaces

### Interface GigabitEthernet0/0 - Ligação ao MLS3
```
interface GigabitEthernet0/0
ip address 192.168.177.89 255.255.255.252
no shutdown
exit
```

## Rotas Estáticas

### Rotas para as redes internas (via MLS3)
```
! Rota para VLAN 10 - STAFF

ip route 192.168.177.0 255.255.255.224 192.168.177.90

! Rota para VLAN 20 - ACCOUNTING

ip route 192.168.177.32 255.255.255.224 192.168.177.90

! Rota para VLAN 30 - HR

ip route 192.168.177.64 255.255.255.240 192.168.177.90

! Rota para VLAN 40 - USERS

ip route 192.168.176.0 255.255.255.0 192.168.177.90

! Rota para Port-Channel 1 MLS3-MLS4

ip route 192.168.177.80 255.255.255.252 192.168.177.90

! Rota para Port-Channel 2 MLS3-MLS5

ip route 192.168.177.84 255.255.255.252 192.168.177.90

! Rota default

! ip route 0.0.0.0 0.0.0.0 192.0.2.110
```


---

## Resumo das Configurações

### Interfaces Configuradas

| Interface | Endereço IP | Máscara | Descrição |
|-----------|-------------|---------|-----------|
| GigabitEthernet0/0 | 192.168.177.89 | 255.255.255.252 | Ligação P2P para MLS3 |

### Ligação P2P

| Origem | IP Local | IP Remoto | Destino |
|--------|----------|-----------|---------|
| BR1 Gi0/0 | 192.168.177.89 | 192.168.177.89 | MLS3 Gi0/1 |

### Rotas Estáticas Configuradas

| Rede Destino | Máscara | Next Hop | Descrição |
|--------------|---------|----------|-----------|
| 192.168.177.0 | 255.255.255.224 | 192.168.177.89 | VLAN 10 - STAFF |
| 192.168.177.32 | 255.255.255.224 | 192.168.177.89 | VLAN 20 - ACCOUNTING |
| 192.168.177.64 | 255.255.255.240 | 192.168.177.89 | VLAN 30 - HR |
| 192.168.176.0 | 255.255.255.0 | 192.168.177.89 | VLAN 40 - USERS |
| 192.168.177.80 | 255.255.255.252 | 192.168.177.89 | Port-Channel 1 (MLS3-MLS4) |
| 192.168.177.84 | 255.255.255.252 | 192.168.177.89 | Port-Channel 2 (MLS3-MLS5) |