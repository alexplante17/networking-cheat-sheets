# OSPF (Open Shortest Path First) Cheat Sheet

## Basic Concepts

### Areas
- Area 0 (Backbone): Required for all OSPF networks
- Standard Area: Receives all LSAs
- Stub Area: No external routes
- Totally Stubby Area: Only default route from ABR
- Not-So-Stubby Area (NSSA): Allows external routes conversion

### Router Types
- Internal Router (IR): All interfaces in same area
- Area Border Router (ABR): Connects multiple areas
- Autonomous System Boundary Router (ASBR): Connects to external networks
- Backbone Router: Has interface in Area 0

### OSPF Packet Types
1. Hello: Neighbor discovery/maintenance
2. DBD (Database Description): Summarizes database contents
3. LSR (Link State Request): Requests specific LSAs
4. LSU (Link State Update): Sends requested LSAs
5. LSAck (Link State Acknowledgment): Confirms LSA receipt

### Network Types
- Point-to-Point
- Broadcast (Ethernet)
- Non-Broadcast Multi-Access (NBMA)
- Point-to-Multipoint
- Point-to-Multipoint Non-Broadcast

## Essential Commands (Cisco IOS)

### Basic Configuration
```
router ospf <process-id>
network <ip-address> <wildcard-mask> area <area-id>
router-id <router-id>
```

### Interface Configuration
```
ip ospf hello-interval <seconds>
ip ospf dead-interval <seconds>
ip ospf priority <0-255>
ip ospf cost <cost>
```

### Authentication
```
# MD5 Authentication
interface <interface>
ip ospf message-digest-key <key-id> md5 <password>
ip ospf authentication message-digest

# Simple Password
interface <interface>
ip ospf authentication
ip ospf authentication-key <password>
```

### Area Configuration
```
# Stub Area
area <area-id> stub

# Totally Stubby Area
area <area-id> stub no-summary

# NSSA
area <area-id> nssa
```

### Verification Commands
```
show ip ospf neighbor
show ip ospf database
show ip ospf interface
show ip route ospf
show ip ospf border-routers
debug ip ospf events
debug ip ospf packet
```

## Important Timers

- Hello Interval: 10 seconds (Ethernet), 30 seconds (NBMA)
- Dead Interval: 4 × Hello Interval
- LSA Refresh: Every 30 minutes
- SPF Calculation: 5 seconds (initial), 10 seconds (maximum)

## OSPF States

1. Down
2. Init
3. Two-Way
4. ExStart
5. Exchange
6. Loading
7. Full

## LSA Types

1. Router LSA (Type 1)
2. Network LSA (Type 2)
3. Network Summary LSA (Type 3)
4. ASBR Summary LSA (Type 4)
5. External LSA (Type 5)
6. NSSA External LSA (Type 7)

## Best Practices

1. Always configure router-id manually
2. Use loopback interfaces for stability
3. Implement authentication in production
4. Summarize routes when possible
5. Plan area design carefully
6. Monitor OSPF database size
7. Use passive interfaces for security
8. Adjust timers based on network needs

## Troubleshooting Tips

1. Verify neighbor adjacencies
2. Check area configurations match
3. Confirm network type matches
4. Validate authentication settings
5. Review MTU settings
6. Check timer consistency
7. Monitor CPU and memory usage
8. Verify routing table entries
