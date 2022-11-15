R1: static IP as Wireguard server
```
### Interface config
configure

set interfaces ethernet eth0 address dhcp
set interfaces ethernet eth0 description 'OUTSIDE'

set interfaces ethernet eth1 address '192.168.90.1/24'
set interfaces ethernet eth1 description 'INSIDE'

set service ssh port '22'

commit;save

### DHCP quick-start
set service dhcp-server shared-network-name LAN subnet 192.168.90.0/24 default-router '192.168.90.1'
set service dhcp-server shared-network-name LAN subnet 192.168.90.0/24 name-server '192.168.90.1'
set service dhcp-server shared-network-name LAN subnet 192.168.90.0/24 domain-name 'vyos.local'
set service dhcp-server shared-network-name LAN subnet 192.168.90.0/24 lease '86400'
set service dhcp-server shared-network-name LAN subnet 192.168.90.0/24 range 0 start 192.168.90.9
set service dhcp-server shared-network-name LAN subnet 192.168.90.0/24 range 0 stop '192.168.90.250'

set service dns forwarding cache-size '0'
set service dns forwarding listen-address '192.168.90.1'
set service dns forwarding allow-from '192.168.90.0/24'

commit;save

### NAT

set nat source rule 100 outbound-interface 'eth0'
set nat source rule 100 source address '192.168.90.0/24'
set nat source rule 100 translation address masquerade

### Firewall
set firewall name OUTSIDE-IN default-action 'drop'
set firewall name OUTSIDE-IN rule 10 action 'accept'
set firewall name OUTSIDE-IN rule 10 state established 'enable'
set firewall name OUTSIDE-IN rule 10 state related 'enable'

set firewall name OUTSIDE-LOCAL default-action 'drop'
set firewall name OUTSIDE-LOCAL rule 10 action 'accept'
set firewall name OUTSIDE-LOCAL rule 10 state established 'enable'
set firewall name OUTSIDE-LOCAL rule 10 state related 'enable'
set firewall name OUTSIDE-LOCAL rule 20 action 'accept'
set firewall name OUTSIDE-LOCAL rule 20 icmp type-name 'echo-request'
set firewall name OUTSIDE-LOCAL rule 20 protocol 'icmp'
set firewall name OUTSIDE-LOCAL rule 20 state new 'enable'

set firewall name OUTSIDE-LOCAL rule 30 action 'drop'
set firewall name OUTSIDE-LOCAL rule 30 destination port '22'
set firewall name OUTSIDE-LOCAL rule 30 protocol 'tcp'
set firewall name OUTSIDE-LOCAL rule 30 recent count '4'
set firewall name OUTSIDE-LOCAL rule 30 recent time '60'
set firewall name OUTSIDE-LOCAL rule 30 state new 'enable'

set firewall name OUTSIDE-LOCAL rule 31 action 'accept'
set firewall name OUTSIDE-LOCAL rule 31 destination port '22'
set firewall name OUTSIDE-LOCAL rule 31 protocol 'tcp'
set firewall name OUTSIDE-LOCAL rule 31 state new 'enable'

set interfaces ethernet eth0 firewall in name 'OUTSIDE-IN'
set interfaces ethernet eth0 firewall local name 'OUTSIDE-LOCAL'

commit;save
```

```

```
