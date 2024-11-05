# IS-IS (Intermediate System to Intermediate System) Cheat Sheet

## Basic Concepts

### Level Types
- Level 1 (L1): Intra-area routing
- Level 2 (L2): Inter-area routing
- Level 1-2: Both intra and inter-area routing

### System Types
- Level 1 Router: Intra-area only
- Level 2 Router: Inter-area only
- Level 1-2 Router: Both levels

### Network Types
- Broadcast
- Point-to-Point

### Addressing
- Network Entity Title (NET):
  ```
  Area-ID.System-ID.NSEL
  49.0001.1234.5678.9012.00
  ```
  - Area-ID: Variable length (1-13 bytes)
  - System-ID: 6 bytes
  - NSEL: Always 00 for routers

### PDU Types
1. Hello (IIH)
   - Level 1 LAN IIH
   - Level 2 LAN IIH
   - Point-to-Point IIH
2. Link State PDU (LSP)
   - Level 1 LSP
   - Level 2 LSP
3. Complete Sequence Numbers PDU (CSNP)
4. Partial Sequence Numbers PDU (PSNP)

## Essential Commands (Cisco IOS)

### Basic Configuration
```
router isis [process-tag]
net network-entity-title
is-type {level-1 | level-1-2 | level-2-only}
```

### Interface Configuration
```
interface <type><number>
ip router isis [process-tag]
isis circuit-type {level-1 | level-1-2 | level-2-only}
isis metric <metric> {level-1 | level-2}
isis priority <priority> {level-1 | level-2}
```

### Authentication
```
# Interface-level authentication
interface <type><number>
isis authentication mode {md5 | text} [level-1 | level-2]
isis authentication key-chain <keychain-name> [level-1 | level-2]

# Area authentication
router isis
authentication mode {md5 | text} [level-1 | level-2]
authentication key-chain <keychain-name> [level-1 | level-2]
```

### Route Leaking
```
router isis
redistribute isis ip level-2 into level-1
redistribute isis ip level-1 into level-2
```

## Verification Commands
```
show clns neighbors
show isis neighbors
show isis database
show isis topology
show ip route isis
show isis spf-log
show clns interface
show isis interface
```

## Troubleshooting Commands
```
debug isis adj-packets
debug isis spf-events
debug isis update-packets
debug isis nsf
```

## Important Timers

### Hello Timers
- Default hello interval: 10 seconds
- Default hold time: 30 seconds

### LSP Timers
- Maximum LSP lifetime: 1200 seconds
- LSP refresh interval: 900 seconds
- LSP generation interval: 5 seconds
- LSP retransmission interval: 5 seconds

## IS-IS States
1. Down
2. Initializing
3. Up

## Metric Types
1. Narrow Metrics (Original)
   - 6 bits (0-63)
2. Wide Metrics (Extended)
   - 24 bits (0-16777215)

## Best Practices

### Design
1. Use meaningful area addressing
2. Plan L1/L2 boundaries carefully
3. Use wide metrics
4. Implement authentication
5. Use loopback interfaces
6. Consider route summarization

### Configuration
```
# Enable wide metrics
metric-style wide

# Configure reference bandwidth
router isis
metric-style wide
auto-cost reference-bandwidth 100000

# Enable BFD
interface <type><number>
isis bfd
```

### Security
1. Enable authentication
2. Use passive interfaces
3. Implement route filtering
4. Control LSP database size

## Advanced Features

### Multi-Topology Routing
```
router isis
address-family ipv4 multicast
topology base
exit-address-family
```

### Mesh Groups
```
interface <type><number>
isis mesh-group <group-number>
```

### Fast Convergence
```
router isis
nsf cisco
ispf level-1-2
fast-flood
```

## Troubleshooting Tips

### Common Issues
1. NET ID misconfiguration
2. Area mismatch
3. Authentication failures
4. MTU mismatch
5. Level type mismatches

### Resolution Steps
1. Verify NET configuration
2. Check area boundaries
3. Validate authentication
4. Confirm interface MTU
5. Check neighbor adjacencies
6. Verify route propagation
7. Monitor LSP database
8. Check SPF calculations

## Migration Tips
1. Enable wide metrics first
2. Transition level types gradually
3. Implement authentication last
4. Test route leaking carefully
5. Monitor database size
6. Verify route redistribution

## Performance Tuning
```
# SPF Tuning
router isis
spf-interval maximum-wait [connected | level-1 | level-2] secs

# LSP Generation Tuning
lsp-gen-interval maximum-wait [level-1 | level-2] msecs

# PRC Tuning
prc-interval maximum-wait [level-1 | level-2] msecs
```
