# vyos-ipsec-vpn-site-to-site-vti

```
192.168.1.1/24 (LAN-eth1) -------1.1.1.1   (PA-WAN-eth0) R1 --- vti (192.168.224.1)
                                        ||             ||
192.168.2.1/24 (LAN-eth1) -------2.2.2.2 (VyOS-WAN-eth0) R2 --- vti (192.168.224.2)
```
#### R2
```
set system offload ipsec enable
set vpn ipsec esp-group vyos-pa-esp lifetime 3600
set vpn ipsec esp-group vyos-pa-esp mode tunnel
set vpn ipsec esp-group vyos-pa-esp pfs dh-group 14
set vpn ipsec esp-group vyos-pa-esp proposal 1 encryption aes256
set vpn ipsec esp-group vyos-pa-esp proposal 1 hash sha1
set vpn ipsec ike-group vyos-pa-ike key-exchange ikev1
set vpn ipsec ike-group vyos-pa-ike lifetime 86400
set vpn ipsec ike-group vyos-pa-ike proposal 1 dh-group 14
set vpn ipsec ike-group vyos-pa-ike proposal 1 encryption aes256
set vpn ipsec ike-group vyos-pa-ike proposal 1 hash sha256
set vpn ipsec ipsec-interfaces interface eth0
set vpn ipsec logging log-modes any
set vpn ipsec site-to-site peer 1.1.1.1 authentication id 2.2.2.2
set vpn ipsec site-to-site peer 1.1.1.1 authentication mode pre-shared-secret
set vpn ipsec site-to-site peer 1.1.1.1 authentication pre-shared-secret 123456
set vpn ipsec site-to-site peer 1.1.1.1 authentication remote-id 1.1.1.1
set vpn ipsec site-to-site peer 1.1.1.1 connection-type initiate
set vpn ipsec site-to-site peer 1.1.1.1 default-esp-group vyos-pa-esp
set vpn ipsec site-to-site peer 1.1.1.1 ike-group vyos-pa-ike
set vpn ipsec site-to-site peer 1.1.1.1 local-address 2.2.2.2
set vpn ipsec site-to-site peer 1.1.1.1 vti bind vti0
set vpn ipsec site-to-site peer 1.1.1.1 vti esp-group vyos-pa-esp

set interfaces vti vti0 address 192.168.224.2/24

set protocols static route 192.168.1.0/24 next-hop 192.168.224.1

set firewall name <vyos-wan-interface-to-local-ruleset> rule 20 action accept
set firewall name <vyos-wan-interface-to-local-ruleset> rule 20 destination group address-group ADDRv4_<vyos-wan-interface>
set firewall name <vyos-wan-interface-to-local-ruleset> rule 20 log enable
set firewall name <vyos-wan-interface-to-local-ruleset> rule 20 protocol esp
set firewall name <vyos-wan-interface-to-local-ruleset> rule 20 source address <pa-wan-address>
```
