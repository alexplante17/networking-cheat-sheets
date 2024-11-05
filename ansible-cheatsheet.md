# Ansible Cheat Sheet

First, here's a brief overview of key Ansible concepts:

Control Node: The machine where Ansible is installed and from which playbooks are run.
Managed Nodes: The target machines (hosts) that Ansible manages.
Inventory: A list of managed nodes, which can be grouped and nested.
Playbooks: YAML files that define the desired state using tasks and roles.
Tasks: Individual units of work that Ansible executes.
Roles: Reusable bundles of tasks, files, and variables.
Modules: Programs that execute on target hosts to implement desired state.
Variables: Values that can be reused across playbooks and templates.
Facts: Information gathered about managed nodes.
Handlers: Tasks that only run when notified by other tasks.

## Installation & Setup
```bash
# Install Ansible
pip install ansible

# Check version
ansible --version

# Generate SSH key
ssh-keygen -t rsa -b 4096
```

## Inventory Structure
```ini
# Simple inventory (/etc/ansible/hosts)
[webservers]
web1.example.com
web2.example.com

[dbservers]
db1.example.com
db2.example.com

# With variables
[webservers]
web1.example.com ansible_host=192.168.1.10 http_port=8080
web2.example.com ansible_host=192.168.1.11 http_port=8080

# Groups of groups
[production:children]
webservers
dbservers
```

## Basic Commands
```bash
# Ping all hosts
ansible all -m ping

# Run command on hosts
ansible webservers -m command -a "uptime"

# Run playbook
ansible-playbook playbook.yml

# List hosts
ansible all --list-hosts

# Gather facts
ansible all -m setup

# Check syntax
ansible-playbook playbook.yml --syntax-check

# Dry run
ansible-playbook playbook.yml --check
```

## Playbook Structure
```yaml
---
- name: Basic Playbook
  hosts: webservers
  become: true
  vars:
    http_port: 80
    
  tasks:
    - name: Install Apache
      yum:
        name: httpd
        state: present
        
    - name: Start Apache
      service:
        name: httpd
        state: started
        enabled: yes
        
  handlers:
    - name: restart apache
      service:
        name: httpd
        state: restarted
```

## Common Modules
```yaml
# File operations
- file:
    path: /etc/file.conf
    state: touch
    mode: '0644'

# Package management
- yum:
    name: nginx
    state: present

# Service management
- service:
    name: nginx
    state: started
    enabled: yes

# Copy files
- copy:
    src: files/file.conf
    dest: /etc/file.conf

# Template files
- template:
    src: templates/nginx.conf.j2
    dest: /etc/nginx/nginx.conf

# User management
- user:
    name: john
    state: present
    groups: admin
```

## Variables
```yaml
# In playbook
vars:
  http_port: 80
  max_clients: 200

# In vars file (vars/main.yml)
---
http_port: 80
max_clients: 200

# Command line
ansible-playbook playbook.yml -e "http_port=8080"

# In inventory
[webservers:vars]
http_port=80
```

## Conditionals and Loops
```yaml
# Conditionals
- name: Install Apache on RedHat
  yum: name=httpd state=present
  when: ansible_os_family == "RedHat"

# Loops
- name: Create users
  user:
    name: "{{ item }}"
    state: present
  loop:
    - john
    - jane
    - bob

# With dictionaries
- name: Create users with details
  user:
    name: "{{ item.name }}"
    groups: "{{ item.groups }}"
  loop:
    - { name: 'john', groups: 'admin' }
    - { name: 'jane', groups: 'dev' }
```

## Role Structure
```
roles/
  rolename/
    defaults/      # Default variables
      main.yml
    files/         # Static files
    handlers/      # Handlers
      main.yml
    meta/          # Role metadata
      main.yml
    tasks/         # Tasks
      main.yml
    templates/     # Jinja2 templates
    vars/          # Role variables
      main.yml
```

## Tags
```yaml
# In tasks
- name: Install Apache
  yum: name=httpd state=present
  tags: 
    - apache
    - webserver

# Run specific tags
ansible-playbook playbook.yml --tags "apache"

# Skip specific tags
ansible-playbook playbook.yml --skip-tags "apache"
```

## Best Practices
1. Use version control
2. Keep secrets encrypted with ansible-vault
3. Use roles for reusability
4. Name tasks descriptively
5. Use handlers for service restarts
6. Test playbooks with --check
7. Use variables for environment-specific values
8. Document playbooks and roles

## Vault Commands
```bash
# Create encrypted file
ansible-vault create secrets.yml

# Edit encrypted file
ansible-vault edit secrets.yml

# Encrypt existing file
ansible-vault encrypt secrets.yml

# Decrypt file
ansible-vault decrypt secrets.yml

# Run playbook with vault
ansible-playbook playbook.yml --ask-vault-pass
```

## Debug Commands
```yaml
# Debug module
- debug:
    var: some_variable
    
- debug:
    msg: "The value is {{ some_variable }}"

# Verbose output
ansible-playbook playbook.yml -vvv
```

## Common Patterns
```bash
# Target all hosts
ansible all -m ping

# Target specific groups
ansible webservers:dbservers -m ping

# Target specific hosts
ansible web1.example.com -m ping

# Exclude hosts
ansible all:!webservers -m ping

# Intersect groups
ansible webservers:&staging -m ping
```