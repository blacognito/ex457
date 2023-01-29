```bash
! Command: show running-config
! device: S1 (vEOS-lab, EOS-4.29.1F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
username blacognito privilege 15 secret sha512 $6$L1d9xUs/TW8pA4SY$o7bLQUzKwTI/FOMyLyN5Gem/9loWAHEhuEJ9yHof0llOzeA42ncpbIP.Ttv4HzuIq9X0aHRT04xJxHwlLpY.T/
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model ribd
!
hostname S1
!
spanning-tree mode mstp
!
vlan 5
   name FIVE
!
vlan 10
   name TEN
!
management api http-commands
   no shutdown
!
interface Ethernet1
!
interface Ethernet2
!
interface Ethernet3
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Management1
   ip address 192.168.0.106/24
!
no ip routing
!
end
```