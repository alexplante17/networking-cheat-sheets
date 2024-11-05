# Containerlab Cheat Sheet

## Installation
```bash
# Install on Linux
bash -c "$(curl -sL https://get.containerlab.dev)"

# Verify installation
containerlab version
```

## Basic Commands

### Lab Management
```bash
# Deploy lab topology
containerlab deploy -t topology.yml

# Destroy lab
containerlab destroy -t topology.yml

# Inspect lab
containerlab inspect -t topology.yml

# Save lab configs
containerlab save -t topology.yml

# Graph topology
containerlab graph -t topology.yml
```

### Container Operations
```bash
# Execute command in container
containerlab exec -t topology.yml --label ceos1 --cmd "show version"

# Connect to container
containerlab connect -t topology.yml --node ceos1

# Get container logs
containerlab logs -t topology.yml --follow node1
```

## Topology File Structure
```yaml
name: basic-ceos-lab

topology:
  nodes:
    ceos1:
      kind: ceos
      image: ceos:4.28.0F
      startup-config: ceos1.cfg
      
    ceos2:
      kind: ceos
      image: ceos:4.28.0F
      startup-config: ceos2.cfg

  links:
    - endpoints: ["ceos1:eth1", "ceos2:eth1"]
```

## Common Node Kinds
- `ceos`: Arista cEOS
- `srl`: Nokia SR Linux
- `crpd`: Juniper cRPD
- `linux`: Linux container
- `cvx`: Cumulus VX
- `sonic-vs`: SONiC virtual switch
- `vr-ros`: MikroTik RouterOS
- `fr-linux`: FRRouting on Linux

## Advanced Features

### Node Configuration
```yaml
# Custom management IP
mgmt_ipv4: 172.20.20.100

# Custom startup configuration
startup-config: router1.cfg

# Custom ports
ports:
  - 8080:80

# Environment variables
env:
  CUSTOM_VAR: value

# Extra mounts
binds:
  - /host/path:/container/path
```

### Link Configuration
```yaml
links:
  # Basic link
  - endpoints: ["node1:eth1", "node2:eth1"]
  
  # Link with custom parameters
  - endpoints: ["node1:eth2", "node2:eth2"]
    mtu: 9000
    unidirectional: false
```

## Best Practices

1. **Naming Conventions**
   - Use meaningful lab names
   - Label nodes based on their function
   - Use consistent interface naming

2. **Resource Management**
   - Set memory limits for nodes
   - Configure CPU allocation
   - Use shared images when possible

3. **Configuration**
   - Keep startup configs in version control
   - Use variables for repeated values
   - Document topology changes

## Troubleshooting

### Common Issues
```bash
# Check container status
docker ps -a

# View container logs
docker logs <container-name>

# Check network connectivity
docker network inspect clab

# Verify node accessibility
ping <node-mgmt-ip>
```

### Debug Commands
```bash
# Deploy with debug logs
containerlab deploy -t topology.yml --debug

# Check system requirements
containerlab system-check

# Gather debug information
containerlab tools cert-info
```

## Environment Variables
```bash
# Set default topology file
export CLAB_TOPO=topology.yml

# Set debug level
export CLAB_DEBUG=1

# Set custom registry
export CLAB_REGISTRY=registry.example.com
```
