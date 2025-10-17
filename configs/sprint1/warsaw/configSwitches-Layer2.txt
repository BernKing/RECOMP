enable
configure terminal

! ===== VTP Configuration =====
vtp domain RECOMP2526M1B01
vtp password 6252pmocer
vtp mode client
vtp version 2

! ===== Configuração Global de Spanning Tree =====
spanning-tree mode rapid-pvst

! ===== Trunk Ports (Fa0/1-2) =====
interface range fa0/1-2
description Trunk to MLS4 and MLS5
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk native vlan 50
switchport trunk allowed vlan 10,20,30,40,50
no shutdown
exit

! ===== VLAN 10 - STAFF (Fa0/3-5) =====
interface range fa0/3-5
description STAFF VLAN
switchport mode access
switchport access vlan 10
spanning-tree portfast
spanning-tree bpduguard enable
no shutdown
exit

! ===== VLAN 20 - ACCOUNTING (Fa0/6-10) =====
interface range fa0/6-10
description ACCOUNTING VLAN
switchport mode access
switchport access vlan 20
spanning-tree portfast
spanning-tree bpduguard enable
no shutdown
exit

! ===== VLAN 30 - HR (Fa0/11-15) =====
interface range fa0/11-15
description HR VLAN
switchport mode access
switchport access vlan 30
spanning-tree portfast
spanning-tree bpduguard enable
no shutdown
exit

! ===== VLAN 40 - USERS (Fa0/16-20) =====
interface range fa0/16-20
description USERS VLAN
switchport mode access
switchport access vlan 40
spanning-tree portfast
spanning-tree bpduguard enable
no shutdown
exit

! ===== VLAN 99 - BLACKHOLE (Portas Não Utilizadas) =====
! FastEthernet (Fa0/21-24)
interface range fa0/21-24
description UNUSED - BLACKHOLE
switchport mode access
switchport access vlan 99
shutdown
exit

! GigabitEthernet (Gig0/1-2)
interface range gig0/1-2
description UNUSED - BLACKHOLE
switchport mode access
switchport access vlan 99
shutdown
exit

end

write memory