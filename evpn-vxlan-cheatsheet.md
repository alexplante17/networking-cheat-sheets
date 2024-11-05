# EVPN VXLAN Cheat Sheet

## Basic Concepts

### VXLAN Components
- VNI (VXLAN Network Identifier): 24-bit segment ID
- VTEP (VXLAN Tunnel Endpoint): Encapsulation/Decapsulation point
- NVE (Network Virtualization Edge): Interface for VXLAN
- VNID: Virtual Network Identifier

### EVPN Route Types
1. Type-1: Ethernet Auto-Discovery
2. Type-2: MAC/IP Advertisement
3. Type-3: Inclusive Multicast Ethernet Tag
4. Type-4: Ethernet Segment Route
5. Type-5: IP Prefix Route

### Service Types
- VLAN-Based Service
- VLAN-Aware Bundle Service
- VLAN-Bundle Service

## Configuration Examples (Cisco Nexus)

### Underlay Configuration
```
# Enable Features
feature ospf
feature bgp
feature vn-segment-vlan-based
feature nv overlay
feature interface-vlan

# Interface Config
interface Ethernet1/1
  description Underlay
  no switchport
  ip address 192.168.1.1/30
  ip ospf network point-to-point
  no ip ospf passive-interface
  ip router ospf 1 area 0.0.0.0
  no shutdown

# OSPF Config
router ospf 1
  router-id 10.1.1.1
```

### BGP Configuration
```
# BGP Underlay
router bgp 65001
  router-id 10.1.1.1
  neighbor 192.168.1.2
    remote-as 65001
    update-source loopback0
    address-family l2vpn evpn
      send-community extended
      route-reflector-client

# EVPN Address Family
  address-family l2vpn evpn
    retain route-target all
```

### VXLAN Configuration
```
# Create NVE Interface
interface nve1
  no shutdown
  source-interface loopback1
  host-reachability protocol bgp

# Configure VNI
  member vni 10000
    suppress-arp
    ingress-replication protocol bgp

# VLAN to VNI Mapping
vlan 100
  vn-segment 10000

# L2VNI Configuration
evpn
  vni 10000 l2
    rd auto
    route-target import auto
    route-target export auto
```

### L3 VXLAN Configuration
```
# L3 VNI
vlan 999
  vn-segment 900001

# L3 VRF Configuration
vrf context tenant-1
  vni 900001
  rd auto
  address-family ipv4 unicast
    route-target both auto evpn

interface Vlan999
  no shutdown
  vrf member tenant-1
  ip forward
```

## Verification Commands

### Underlay Verification
```
show ip ospf neighbors
show ip route
show ip bgp summary
show running-config bgp
```

### Overlay Verification
```
show nve peers
show nve vni
show mac address-table
show bgp l2vpn evpn summary
show bgp l2vpn evpn
show l2route evpn mac all
show l2route evpn mac-ip all
```

### VXLAN Verification
```
show nve vni
show nve peers
show vxlan interface
show vxlan internal interface
show vxlan internal vlan
```

## Common Configurations

### Anycast Gateway
```
fabric forwarding anycast-gateway-mac 0000.2222.3333

interface Vlan100
  no shutdown
  vrf member tenant-1
  ip address 10.1.1.1/24
  fabric forwarding mode anycast-gateway
```

### MLAG/vPC Configuration
```
vpc domain 1
  peer-switch
  peer-keepalive destination 10.1.1.2
  peer-gateway
  layer3 peer-router
```

## Multi-Site Configuration
```
# Border Leaf Config
evpn multisite border-gateway 1
  delay-restore time 180

interface nve1
  multisite border-gateway interface loopback100
```

## Best Practices

### Design
1. Use Spine-Leaf topology
2. Implement MLAG/vPC for redundancy
3. Use anycast gateway
4. Plan VNI allocation
5. Configure proper MTU

### Performance
```
# Enable BFD
bfd interval 50 min_rx 50 multiplier 3

# Hardware Configuration
hardware access-list tcam region racl 512
hardware access-list tcam region arp-ether 256 double-wide
```

### Security
1. Implement VTEP authentication
2. Use route-maps for filtering
3. Configure storm control
4. Enable ARP suppression

## Troubleshooting Tips

### Common Issues
1. MTU mismatches
2. BGP peering issues
3. VNI misconfigurations
4. Underlay routing problems
5. VTEP reachability

### Resolution Steps
1. Verify underlay connectivity
2. Check BGP EVPN peering
3. Validate VNI mappings
4. Confirm anycast gateway
5. Check MTU settings
6. Verify NVE interface status

## EVPN Route Format
```
[1]:[ESI]:[EthTag]:[Labels] - Type-1
[2]:[MAC]:[IPLength]:[IP]:[ESI]:[Labels] - Type-2
[3]:[EthTag]:[IPLength]:[OrigIP] - Type-3
[4]:[ESI]:[IPLength]:[OrigIP] - Type-4
[5]:[ESI]:[EthTag]:[IPLength]:[IP] - Type-5
```

## Multi-Tenancy
```
# VRF Configuration
vrf context tenant-1
  vni 900001
  rd auto
  address-family ipv4 unicast
    route-target both auto evpn

# VLAN Configuration
vlan 100-200
  vn-segment 10000-10100
```

## Advanced Features

### Symmetric IRB
```
router bgp 65001
  vrf tenant-1
    address-family ipv4 unicast
      advertise l2vpn evpn
```

### EVPN Multi-Homing
```
evpn
  evi 100
    ethernet-segment 1
      esi 0000.0000.0000.0001.0001
      multi-homing all-active
```
