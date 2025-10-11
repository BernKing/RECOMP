enable
configure terminal

hostname MLS4

! ===== VTP =====
vtp domain RECOMP2526M1B01
vtp password 6252pmocer
vtp mode server
vtp version 2

! ===== VLANs =====
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

! ===== Spanning Tree =====
spanning-tree mode rapid-pvst
spanning-tree vlan 10 root primary
spanning-tree vlan 20 root primary
spanning-tree vlan 30 root secondary
spanning-tree vlan 40 root secondary

! ===== IP Routing =====
ip routing

! ===== Port-Channel 1 para MLS3 (Layer 3) =====
interface range fa0/1-2
shutdown
no switchport
channel-group 1 mode active
no shutdown
interface port-channel 1
ip address 192.168.177.82 255.255.255.252
description Link to MLS3
no shutdown
exit



! ===== Port-Channel 3 para MLS5 (Layer 2 Trunk) =====
interface range fa0/3-4
shutdown
channel-group 3 mode active
no shutdown
interface port-channel 3
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk native vlan 50
switchport trunk allowed vlan 10,20,30,40,50
switchport trunk allowed vlan remove 99
no shutdown
exit

! ===== Trunks restantes L2 =====
interface range fa0/6-8
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk native vlan 50
switchport trunk allowed vlan 10,20,30,40,50
switchport trunk allowed vlan remove 99
no shutdown
exit


! ===== SVIs com HSRP =====
! VLAN 10 (STAFF) - Active
interface vlan 10
ip address 192.168.177.2 255.255.255.224
standby 10 ip 192.168.177.1
standby 10 priority 150
standby 10 preempt
no shutdown
exit

! VLAN 20 (ACCOUNTING) - Active
interface vlan 20
ip address 192.168.177.34 255.255.255.224
standby 20 ip 192.168.177.33
standby 20 priority 150
standby 20 preempt
no shutdown
exit

! VLAN 30 (HR) - Standby
interface vlan 30
ip address 192.168.177.66 255.255.255.240
standby 30 ip 192.168.177.65
standby 30 priority 100
no shutdown
exit

! VLAN 40 (USERS) - Standby
interface vlan 40
ip address 192.168.176.2 255.255.255.0
standby 40 ip 192.168.176.1
standby 40 priority 100
no shutdown
exit

! ===== DHCP Excluded Addresses =====
ip dhcp excluded-address 192.168.177.1 192.168.177.3
ip dhcp excluded-address 192.168.177.33 192.168.177.35
ip dhcp excluded-address 192.168.177.65 192.168.177.67
ip dhcp excluded-address 192.168.176.1 192.168.176.3

! ===== DHCP Pools =====
ip dhcp pool VLAN10
network 192.168.177.0 255.255.255.224
default-router 192.168.177.1
dns-server 8.8.8.8
domain-name RECOMP2526M1B01.recomp.com
exit

ip dhcp pool VLAN20
network 192.168.177.32 255.255.255.224
default-router 192.168.177.33
dns-server 8.8.8.8
domain-name RECOMP2526M1B01.recomp.com
exit

ip dhcp pool VLAN30
network 192.168.177.64 255.255.255.240
default-router 192.168.177.65
dns-server 8.8.8.8
domain-name RECOMP2526M1B01.recomp.com
exit

ip dhcp pool VLAN40
network 192.168.176.0 255.255.255.0
default-router 192.168.176.1
dns-server 8.8.8.8
domain-name RECOMP2526M1B01.recomp.com
exit

! ===== Rota Padrão para Internet via MLS3 =====
ip route 0.0.0.0 0.0.0.0 192.168.177.81

end

write memory