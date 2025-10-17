enable
configure terminal

hostname BR1

! ===== Interface para MLS3 (P2P) =====
interface GigabitEthernet0/0
description Link to MLS3 Warsaw
ip address 192.168.177.90 255.255.255.252
no shutdown
exit

! ===== Rotas Estáticas para Redes Internas (via MLS3) =====

! Rota para VLAN 10 - STAFF
ip route 192.168.177.0 255.255.255.224 192.168.177.89

! Rota para VLAN 20 - ACCOUNTING
ip route 192.168.177.32 255.255.255.224 192.168.177.89

! Rota para VLAN 30 - HR
ip route 192.168.177.64 255.255.255.240 192.168.177.89

! Rota para VLAN 40 - USERS
ip route 192.168.176.0 255.255.255.0 192.168.177.89

! ===== Rota Default (para Internet/ISP) =====
ip route 0.0.0.0 0.0.0.0 192.0.2.110

end

write memory