# CCNA Automation – Ansible, Puppet, and Chef

## 1. Introduction to Configuration Management in Networking

**Configuration Management Tools** are automation systems that allow network engineers to:
- Push configuration changes to multiple devices at once
- Ensure configurations match company standards
- Deploy new devices quickly and consistently
- Reduce errors from manual CLI configuration

Common tools in the CCNA syllabus:
- **Ansible**
- **Puppet**
- **Chef**

---

## 2. Configuration Drift

**Definition:**  
Configuration drift happens when devices slowly deviate from the standard configuration due to individual manual changes over time.

**Causes:**
- Engineers making temporary changes during troubleshooting
- Lack of centralized configuration records
- Failure to update templates after changes

**Effects:**
- Inconsistency between devices
- Security vulnerabilities
- Harder troubleshooting

**Prevention:**
- Use configuration templates
- Centralized backups
- Automation tools like Ansible, Puppet, or Chef

---

## 3. Configuration Provisioning

**Definition:**  
Applying configuration to devices (including new deployments).

**Traditional approach:**
- Connect to each device via **SSH**
- Manually paste configurations
- **Problem:** Time-consuming, error-prone, not scalable

**Automation approach:**
- Define templates & variables
- Use automation tools to push configurations in bulk

---

## 4. Ansible – The CCNA Focus

### 4.1 Overview
- **Owner:** Red Hat
- **Language:** Written in Python
- **Agentless:** No software needed on the device
- **Connection method:** SSH (or APIs)
- **Model:** Push (Control Node pushes configs to Managed Nodes)

---

### 4.2 Ansible Architecture

- **Control Node**: The machine running Ansible commands
- **Managed Nodes**: Devices being configured
- **Modules**: Pre-written scripts Ansible uses to perform tasks
- **Inventory File**: Lists devices & groups
- **Playbooks**: YAML files with automation instructions
- **Templates**: Configuration blueprints (Jinja2 format)
- **Variables**: Dynamic values inserted into templates

---

### 4.3 Key Ansible Files

| File Type  | Format  | Purpose |
|------------|---------|---------|
| Inventory  | INI/YAML| List devices, groups, variables |
| Playbook   | YAML    | Define automation tasks |
| Template   | Jinja2  | Base config with placeholders |
| Variables  | YAML    | Store values for placeholders |

---

### 4.4 Example Ansible Inventory (YAML)
```yaml
all:
  hosts:
    r1:
      ansible_host: 192.168.1.1
      ansible_user: admin
    r2:
      ansible_host: 192.168.1.2
      ansible_user: admin
  children:
    routers:
      hosts:
        r1:
        r2:
    switches:
      hosts:
        sw1:
          ansible_host: 192.168.1.10
          ansible_user: admin
```

---

### 4.5 Example Ansible Playbook (YAML)

```yaml
---
- name: Configure Cisco Routers
  hosts: all
  gather_facts: no
  tasks:
    - name: Configure hostname
      ios_config:
        lines:
          - hostname {{ inventory_hostname }}
    
    - name: Configure interface
      ios_config:
        lines:
          - ip address {{ ip_address }} {{ subnet_mask }}
          - no shutdown
        parents: interface {{ interface_name }}
    
    - name: Save configuration
      ios_command:
        commands:
          - write memory
```

---

### 4.6 Example Template (Jinja2)

```jinja2
hostname {{ hostname }}
!
interface {{ interface }}
 ip address {{ ip }} {{ mask }}
 no shutdown
!
router ospf {{ ospf_process }}
 network {{ network }} {{ wildcard }} area {{ area }}
!
access-list {{ acl_number }} permit {{ permitted_network }}
```

---

### 4.7 Example Variables File (YAML)

```yaml
# Variables for Router 1
hostname: R1-CORE
interface: GigabitEthernet0/1
ip: 192.168.1.1
mask: 255.255.255.0
ospf_process: 1
network: 192.168.1.0
wildcard: 0.0.0.255
area: 0
acl_number: 10
permitted_network: 192.168.0.0 0.0.255.255
```

---

### 4.8 Ansible Commands

**Run a playbook:**
```bash
ansible-playbook -i inventory.yml playbook.yml
```

**Run ad-hoc command:**
```bash
ansible all -i inventory.yml -m ios_command -a "commands='show version'"
```

**Check syntax:**
```bash
ansible-playbook --syntax-check playbook.yml
```

---

### 4.9 Ansible Modules for Cisco

- **ios_config**: Configure Cisco IOS devices
- **ios_command**: Execute commands on IOS devices
- **ios_facts**: Gather facts from IOS devices
- **nxos_config**: Configure Nexus devices
- **asa_config**: Configure ASA firewalls

---

### 4.10 Ansible Advantages for Networking

✅ **Agentless** - No software installation on devices  
✅ **Easy syntax** - YAML is human-readable  
✅ **Network modules** - Built-in support for Cisco IOS, NX-OS, ASA  
✅ **Push model** - Immediate configuration deployment  
✅ **Version control** - Integrates with Git  
✅ **Idempotent** - Safe to run multiple times  

---

## 5. Puppet

### 5.1 Overview
- **Language:** Puppet DSL (Domain Specific Language) based on Ruby
- **Model:** Pull (devices request configuration from Puppet Master)
- **Agent-based:** Requires Puppet agent on managed devices
- **Port:** TCP 8140
- **Owner:** Puppet Inc.

### 5.2 Puppet Architecture
- **Puppet Master:** Central server storing configurations
- **Puppet Agent:** Software installed on managed devices
- **Catalog:** Compiled configuration for a specific node
- **Facts:** System information collected by agent

### 5.3 Puppet Files
- **Manifest (.pp):** Defines desired device state
- **Template (.erb):** Used to build dynamic manifests
- **Module:** Collection of manifests, templates, and files

### 5.4 Example Puppet Manifest
```puppet
node 'router1.example.com' {
  cisco_interface { 'GigabitEthernet0/1':
    ensure => present,
    ipv4_address => '192.168.1.1/24',
    shutdown => false,
  }
}
```

### 5.5 Puppet Workflow
1. Agent contacts Master (every 30 minutes by default)
2. Agent sends facts to Master
3. Master compiles catalog based on manifests
4. Master sends catalog to Agent
5. Agent applies configuration changes

---

## 6. Chef

### 6.1 Overview
- **Language:** Ruby DSL
- **Model:** Pull (nodes request configuration from Chef Server)
- **Agent-based:** Requires Chef client on managed devices
- **Port:** TCP 10002
- **Owner:** Chef Software (Progress)

### 6.2 Chef Architecture
- **Chef Server:** Central repository for cookbooks and data
- **Chef Client:** Agent running on managed nodes
- **Workstation:** Where cookbooks are developed
- **Knife:** Command-line tool for managing Chef

### 6.3 Chef Components
- **Resources:** Basic configuration objects (files, packages, services)
- **Recipes:** Instructions for configuring resources
- **Cookbooks:** Collection of related recipes
- **Run-list:** Ordered list of recipes for a node
- **Attributes:** Variables used in recipes

### 6.4 Example Chef Recipe
```ruby
# Configure network interface
template '/tmp/interface_config' do
  source 'interface.erb'
  variables({
    :interface => 'GigabitEthernet0/1',
    :ip_address => '192.168.1.1',
    :subnet_mask => '255.255.255.0'
  })
end

# Apply configuration
execute 'configure_interface' do
  command 'vtysh < /tmp/interface_config'
end
```

---

## 7. Comparison Table

| Feature | Ansible | Puppet | Chef |
|---------|---------|---------|------|
| **Language** | YAML (tasks) + Python | Puppet DSL | Ruby DSL |
| **Model** | Push | Pull | Pull |
| **Agent Required** | ❌ (Agentless) | ✅ | ✅ |
| **Default Port** | SSH (22) | 8140 | 10002 |
| **Owner** | Red Hat | Puppet Inc. | Chef Software |
| **Learning Curve** | Easy | Medium | Hard |
| **Best for** | Network Automation | Infrastructure | Complex Applications |
| **Configuration Format** | Playbooks (YAML) | Manifests (.pp) | Recipes (Ruby) |
| **Execution** | Sequential | Declarative | Sequential |
| **Inventory Management** | Built-in | External Classification | Node Objects |

---

## 8. Use Cases by Tool

### Ansible - Best for:
- Network device configuration
- Ad-hoc tasks and troubleshooting
- Multi-vendor environments
- Quick deployments
- Teams new to automation

### Puppet - Best for:
- Server infrastructure management
- Compliance and governance
- Large-scale deployments
- Configuration drift prevention
- Long-term infrastructure maintenance

### Chef - Best for:
- Application deployment
- Complex infrastructure requirements
- DevOps environments
- Continuous integration/deployment
- Custom automation workflows

---

## 9. CCNA Exam Key Points

### Must Know for CCNA:

**Ansible:**
- ✅ Agentless (uses SSH)
- ✅ Push model
- ✅ YAML playbooks
- ✅ Jinja2 templates
- ✅ Best for network automation

**Puppet:**
- ✅ Agent-based
- ✅ Pull model  
- ✅ TCP port 8140
- ✅ Puppet DSL language
- ✅ 30-minute defaul
