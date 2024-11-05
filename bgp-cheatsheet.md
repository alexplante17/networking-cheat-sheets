# BGP (Border Gateway Protocol) Cheat Sheet

## Basic Concepts

### BGP Types
- iBGP: Internal BGP (within same AS)
- eBGP: External BGP (between different AS)
- Confederate BGP: Sub-AS within main AS

### BGP Path Attributes
1. Well-known Mandatory:
   - AS_PATH
   - NEXT_HOP
   - ORIGIN

2. Well-known Discretionary:
   - LOCAL_PREF
   - ATOMIC_AGGREGATE

3. Optional Transitive:
   - COMMUNITY
   - AGGREGATOR

4. Optional Non-transitive:
   - MED (Multi-Exit Discriminator)
   - ORIGINATOR_ID

### BGP Path Selection Process
1. Highest WEIGHT (Cisco specific)
2. Highest LOCAL_PREF
3. Locally originated routes
4. Shortest AS_PATH
5. Lowest origin type (IGP < EGP < Incomplete)
6. Lowest MED
7. eBGP over iBGP
8. Lowest IGP metric to next-hop
9. Oldest route
10. Lowest neighbor BGP RID

## Essential Commands (Cisco IOS)

### Basic Configuration
```
router bgp <AS-number>
neighbor <ip-address> remote-as <AS-number>
neighbor <ip-address> update-source loopback0
network <network> mask <subnet-mask>
```

### iBGP Configuration
```
router bgp <AS-number>
neighbor <ip-address> remote-as <AS-number>
neighbor <ip-address> next-hop-self
neighbor <ip-address> update-source loopback0
```

### Route Reflector Configuration
```
router bgp <AS-number>
neighbor <ip-address> remote-as <AS-number>
neighbor <ip-address> route-reflector-client
```

### Authentication
```
neighbor <ip-address> password <password>
```

### Policy Configuration
```
# Route-maps
route-map <name> permit/deny <sequence>
match ip address prefix-list <name>
set local-preference <value>
set as-path prepend <AS-number>

# Prefix Lists
ip prefix-list <name> seq <number> permit/deny <prefix/length> [ge value] [le value]
```

### Advertisement Control
```
neighbor <ip-address> filter-list <AS-path-ACL> in/out
neighbor <ip-address> prefix-list <prefix-list-name> in/out
neighbor <ip-address> route-map <route-map-name> in/out
```

## Verification Commands
```
show ip bgp
show ip bgp summary
show ip bgp neighbors
show ip bgp <prefix>
show ip bgp regexp <regular-expression>
show ip bgp community <community-number>
show ip bgp paths
show ip bgp cidr-only
```

## Troubleshooting Commands
```
debug ip bgp
debug ip bgp updates
debug ip bgp events
debug ip bgp keepalives
```

## BGP States
1. Idle
2. Connect
3. Active
4. OpenSent
5. OpenConfirm
6. Established

## BGP Timers
- Keepalive: 60 seconds
- Holdtime: 180 seconds
- ConnectRetry: 120 seconds
- MinRouteAdvertisementInterval (MRAI):
  - eBGP: 30 seconds
  - iBGP: 5 seconds

## Best Practices

### Security
1. Implement MD5 authentication
2. Use prefix limits
3. Implement Route Filtering
4. Use TTL security (eBGP)
```
neighbor <ip-address> ttl-security hops <number>
```

### Stability
1. Use loopback interfaces
2. Implement route dampening
3. Use soft reconfiguration
4. Use peer groups
5. Implement maximum-prefix limits

### Performance
1. Use route reflectors for large iBGP meshes
2. Implement route aggregation
3. Use BGP communities for policy control
4. Optimize BGP timers when needed

## Common Community Values
- NO_EXPORT (0xFFFFFF01)
- NO_ADVERTISE (0xFFFFFF02)
- LOCAL_AS (0xFFFFFF03)
- INTERNET (0x0)

## Troubleshooting Tips

### Common Issues
1. TCP connection issues (port 179)
2. AS number mismatch
3. Authentication failures
4. Route advertisement issues
5. Next-hop reachability
6. Policy configuration errors

### Resolution Steps
1. Verify BGP neighbor state
2. Check TCP connectivity
3. Validate AS numbers
4. Verify route advertisements
5. Check routing policies
6. Validate BGP attributes
7. Monitor BGP memory usage
8. Check for route flapping

## Regular Expressions for AS Path Filtering
```
^$ - Locally originated routes
_$ - Routes originated by the specified AS
^AS_ - Routes originated by the specified AS
_AS$ - Routes with specified AS as last hop
_AS_ - Routes passing through specified AS
```
