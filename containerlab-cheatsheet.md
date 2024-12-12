# Containerlab Cheat Sheet

## Installation
```bash
# Install on Linux
bash -c "$(curl -sL https://get.containerlab.dev)"

# Verify installation
containerlab version

```

## Basic Commands

### Import new container appliance
```bash
# pull latest available release from public registry
docker pull ghcr.io/nokia/srlinux

# Import a downloaded  file
docker import xyz.tar

```
### Lab Management
```bash
# Deploy lab topology
containerlab deploy -t topology.yml

# Deploy with debug logs
containerlab deploy -t topology.yml --debug

# Destroy lab
containerlab destroy -t topology.yml

# Graph topology
containerlab graph -t topology.yml
```

### Container Operations
```bash
# Get container names
docker ps

# Connect to appliance via docker console
docker exec -it <container-name/id> bash
docker exec -it <container-name/id> sr_cli

# Connect to appliance via SSH
SSH ssh admin@<container-name>

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
- `arista_ceos`: Arista cEOS
- `nokia_srlinux`: Nokia SR Linux
- `juniper_crpd`: Juniper cRPD
- `sonic-vs`: SONiC Container
- `cisco_n9kv`: Cisco Nexus 9000v
- `cisco_xrd`: Cisco XRd

## Advanced Features

### Node Configuration
```yaml
# Custom management IP
mgmt_ipv4: 172.20.20.100

# Custom startup configuration
startup-config: router1.cfg

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

## Troubleshooting

### Common Issues
```bash
# Check container status
docker ps -a

# View container logs
docker logs <container-name>

# List available docker image
docker image ls

# Check when container will be healthy
watch -n 1 "docker ps"
```


## SR-Linux

### Image

```bash
# pull latest available release
docker pull ghcr.io/nokia/srlinux
```

### Interfaces naming

Naming convention:

ethernet-1/1 - first ethernet interface on line card 1
ethernet-1/2 - second interface on line card 1

Breakout interfaces will have the mapped Linux interface name eX-Y-Z where Z is the breakout port number. 

### Credentials

admin:NokiaSrl1!

### Ressources 

: Navigating SR-Linux:  https://learn.srlinux.dev/blog/2024/navigating-sr-linux/
: Get started: https://learn.srlinux.dev                       
: Container:   https://go.srlinux.dev/container-image          
: Docs:        https://doc.srlinux.dev/24-7                    
: Rel. notes:  https://doc.srlinux.dev/rn24-7-2                
: YANG:        https://yang.srlinux.dev/v24.7.2                
: Discord:     https://go.srlinux.dev/discord                 
: Contact:     https://go.srlinux.dev/contact-sales   


## SONiC

https://containerlab.dev/manual/kinds/sonic-vs/#__tabbed_1_2
Grab docker-sonic-vs.gz from https://sonic.software/

Load the image

docker load < docker-sonic.vs.gz