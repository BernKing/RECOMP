# Configuração Switches Layer 2

## Configuração de Trunk (Interfaces Fa0/1-2)

```
en
conf t

interface range fa0/1-2
switchport mode trunk
switchport trunk allowed vlan except 99
switchport trunk native vlan 50
exit

end
```

## Configuração VTP e Portas de Acesso

```
en
conf t

vtp domain RECOMP2526M1B01
vtp password 6252pmocer
vtp mode client
vtp version 2
```

### VLAN 10 - STAFF (Fa0/3-5)
```
interface range fa0/3-5
switchport mode access
switchport access vlan 10
spanning-tree portfast
no shutdown
exit
```

### VLAN 20 - ACCOUNTING (Fa0/6-10)
```
interface range fa0/6-10
switchport mode access
switchport access vlan 20
spanning-tree portfast
no shutdown
exit
```

### VLAN 30 - HR (Fa0/11-15)
```
interface range fa0/11-15
switchport mode access
switchport access vlan 30
spanning-tree portfast
no shutdown
exit
```

### VLAN 40 - USERS (Fa0/16-20)
```
interface range fa0/16-20
switchport mode access
switchport access vlan 40
spanning-tree portfast
no shutdown
exit
```

### VLAN 99 - BLACKHOLE (Portas Não Utilizadas)

#### FastEthernet (Fa0/21-24)
```
interface range fa0/21-24
switchport mode access
switchport access vlan 99
shutdown
exit
```

#### GigabitEthernet (Gig0/1-2)
```
interface range gig0/1-2
switchport mode access
switchport access vlan 99
shutdown
exit

end
```

---

## Resumo das Configurações

### VTP

- **Domínio:** RECOMP2526M1B01
- **Password:** 6252pmocer
- **Modo:** Client
- **Versão:** 2

### Distribuição de Portas por VLAN

| Interfaces | VLAN | Nome | Estado | PortFast |
|------------|------|------|--------|----------|
| Fa0/1-2 | Trunk | - | Ativo | Não |
| Fa0/3-5 | 10 | STAFF | Ativo | Sim |
| Fa0/6-10 | 20 | ACCOUNTING | Ativo | Sim |
| Fa0/11-15 | 30 | HR | Ativo | Sim |
| Fa0/16-20 | 40 | USERS | Ativo | Sim |
| Fa0/21-24 | 99 | BLACKHOLE | Desligado | Não |
| Gig0/1-2 | 99 | BLACKHOLE | Desligado | Não |

### Trunk Configuration

- **VLAN Nativa:** 50
- **VLANs Permitidas:** 1-4094 (exceto 99)

### Segurança

- Todas as portas não utilizadas são colocadas na VLAN 99 (BLACKHOLE) e desligadas
- PortFast ativado em todas as portas de acesso para conectividade rápida de dispositivos finais

### VLANs da Rede

| VLAN | Nome | Descrição |
|------|------|-----------|
| 10 | STAFF | Pessoal |
| 20 | ACCOUNTING | Contabilidade |
| 30 | HR | Recursos Humanos |
| 40 | USERS | Utilizadores |
| 50 | NATIVE | VLAN Nativa para Trunks |
| 99 | BLACKHOLE | Portas não utilizadas (desligadas) |

### Notas Importantes

- Os switches Layer 2 funcionam como **VTP Clients**, recebendo as VLANs dos switches MLS3, MLS4 e MLS5 (que são VTP Servers)
- **PortFast** está ativado em todas as portas de acesso para permitir que os dispositivos finais conectem mais rapidamente à rede
- **Segurança:** Todas as portas não utilizadas estão administrativamente desligadas (shutdown) e atribuídas à VLAN 99