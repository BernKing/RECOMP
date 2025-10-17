enable
configure terminal

hostname MLS3

! ===== IP ROUTING =====
ip routing

! ===== VTP - TRANSPARENT (não precisa de VLANs, só quero que encaminhe) =====
vtp mode transparent
vtp domain RECOMP2526M1B01
vtp password 6252pmocer
vtp version 2


! ===== Port-Channel 1 para MLS4 (Layer 3) =====
interface range fa0/1-2
shutdown
no switchport
channel-group 1 mode active
no shutdown
interface port-channel 1
ip address 192.168.177.81 255.255.255.252
description Link to MLS4
no shutdown
exit



! ===== Port-Channel 2 para MLS5 (Layer 3) =====
interface range fa0/3-4
shutdown
no switchport
channel-group 2 mode active
no shutdown
interface port-channel 2
ip address 192.168.177.85 255.255.255.252
description Link to MLS5
no shutdown
exit

! ===== Interface para BR1 (Layer 3) =====
interface gig0/1
no switchport
ip address 192.168.177.89 255.255.255.252
description Link to BR1
no shutdown
exit

! ===== ROTAS ESTÁTICAS =====
! Rotas para VLANs via MLS4 (192.168.177.82)
ip route 192.168.177.0 255.255.255.224 192.168.177.82
ip route 192.168.177.32 255.255.255.224 192.168.177.82

! Rotas para VLANs via MLS5 (192.168.177.86)
ip route 192.168.177.64 255.255.255.240 192.168.177.86
ip route 192.168.176.0 255.255.255.0 192.168.177.86

! Rota padrão para Internet via BR1
ip route 0.0.0.0 0.0.0.0 192.168.177.90

end

! Salvar configuração
write memory