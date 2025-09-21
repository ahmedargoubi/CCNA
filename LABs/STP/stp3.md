# Lab 5.5.3: Troubleshooting Spanning Tree Protocol - Complete Guide

## Overview
This lab addresses a common network issue: congested trunks in a redundant switched network. You'll learn to implement PVST+ (Per-VLAN Spanning Tree Plus) load balancing to utilize all available trunk links efficiently.

## Problem Statement
- **Issue**: Network congestion during peak usage
- **Cause**: Only 3 out of 6 configured trunks are forwarding traffic
- **Solution**: Implement per-VLAN load balancing using PVST+



## Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|-----------------|
| S1 | VLAN 99 | 172.17.99.11 | 255.255.255.0 | N/A |
| S2 | VLAN 99 | 172.17.99.12 | 255.255.255.0 | N/A |
| S3 | VLAN 99 | 172.17.99.13 | 255.255.255.0 | N/A |
| PC1 | NIC | 172.17.10.21 | 255.255.255.0 | 172.17.10.1 |
| PC2 | NIC | 172.17.20.22 | 255.255.255.0 | 172.17.20.1 |
| PC3 | NIC | 172.17.30.23 | 255.255.255.0 | 172.17.30.1 |

## Port Assignments (Switch S2)

| Ports | Assignment | Network |
|-------|------------|---------|
| Fa0/1-0/4 | 802.1q Trunks (Native VLAN 99) | 172.17.99.0/24 |
| Fa0/5-0/10 | VLAN 30 – Guest | 172.17.30.0/24 |
| Fa0/11-0/17 | VLAN 10 – Faculty/Staff | 172.17.10.0/24 |
| Fa0/18-0/24 | VLAN 20 – Students | 172.17.20.0/24 |

---

## Task 1: Network Preparation

### Step 1: Physical Setup
1. Cable the network according to the topology
2. Use FastEthernet ports Fa0/1-Fa0/4 for trunk connections
3. Set up console connections to all three switches

### Step 2: Clear Existing Configurations
```bash
# On each switch
Switch# erase startup-config
Switch# delete vlan.dat
Switch# reload
```

### Step 3: Apply Initial Configurations

#### S1 Configuration
```bash
hostname S1
enable secret class
no ip domain-lookup
!
vtp mode server
vtp domain Lab5
vtp password cisco
!
vlan 99
name Management
exit
!
vlan 10
name Faculty/Staff
exit
!
vlan 20
name Students
exit
!
vlan 30
name Guest
exit
!
interface FastEthernet0/1
switchport trunk native vlan 99
switchport mode trunk
no shutdown
!
interface FastEthernet0/2
switchport trunk native vlan 99
switchport mode trunk
no shutdown
!
interface FastEthernet0/3
switchport trunk native vlan 99
switchport mode trunk
no shutdown
!
interface FastEthernet0/4
switchport trunk native vlan 99
switchport mode trunk
no shutdown
!
interface range FastEthernet0/5-24
shutdown
!
interface GigabitEthernet0/1
shutdown
!
interface GigabitEthernet0/2
shutdown
!
interface Vlan99
ip address 172.17.99.11 255.255.255.0
no shutdown
!
line con 0
logging synchronous
password cisco
login
line vty 0
no login
line vty 1 4
password cisco
login
line vty 5 15
password cisco
login
!
end
```

#### S2 Configuration
```bash
hostname S2
!
enable secret class
no ip domain-lookup
!
vtp mode client
vtp domain Lab5
vtp password cisco
!
interface FastEthernet0/1
switchport trunk native vlan 99
switchport mode trunk
no shutdown
!
interface FastEthernet0/2
switchport trunk native vlan 99
switchport mode trunk
no shutdown
!
interface FastEthernet0/3
switchport trunk native vlan 99
switchport mode trunk
no shutdown
!
interface FastEthernet0/4
switchport trunk native vlan 99
switchport mode trunk
no shutdown
!
interface range FastEthernet0/5-10
switchport access vlan 30
switchport mode access
!
interface range FastEthernet0/11-17
switchport access vlan 10
switchport mode access
!
interface range FastEthernet0/18-24
switchport access vlan 20
switchport mode access
!
interface fa0/6
no shutdown
interface fa0/11
no shutdown
interface fa0/18
no shutdown
!
interface Vlan99
ip address 172.17.99.12 255.255.255.0
no shutdown
!
line con 0
password cisco
logging synchronous
login
line vty 0 4
password cisco
login
line vty 5 15
password cisco
login
end
```

#### S3 Configuration
```bash
hostname S3
!
enable secret class
no ip domain-lookup
!
vtp mode client
vtp domain Lab5
vtp password cisco
!
interface FastEthernet0/1
switchport trunk native vlan 99
switchport mode trunk
no shutdown
!
interface FastEthernet0/2
switchport trunk native vlan 99
switchport mode trunk
no shutdown
!
interface FastEthernet0/3
switchport trunk native vlan 99
switchport mode trunk
no shutdown
!
interface FastEthernet0/4
switchport trunk native vlan 99
switchport mode trunk
no shutdown
!
interface range FastEthernet0/5-10
switchport access vlan 30
switchport mode access
!
interface range FastEthernet0/11-17
switchport access vlan 10
switchport mode access
!
interface range FastEthernet0/18-24
switchport access vlan 20
switchport mode access
!
interface Vlan99
ip address 172.17.99.13 255.255.255.0
no shutdown
!
line con 0
password cisco
login
line vty 0 4
password cisco
login
line vty 5 15
password cisco
login
end
```

---

## Task 2: Configure Host PCs

Configure each PC with the appropriate network settings:
- **PC1**: 172.17.10.21/24, Gateway: 172.17.10.1
- **PC2**: 172.17.20.22/24, Gateway: 172.17.20.1
- **PC3**: 172.17.30.23/24, Gateway: 172.17.30.1

---

## Task 3: Analyze Initial STP State

### Step 1: Examine Current Spanning Tree
Run the following command on each switch:

```bash
Switch# show spanning-tree
```

### Expected Initial Behavior
With default STP configuration:
- One switch becomes root for ALL VLANs (lowest MAC address)
- Only 3 of 6 trunk links are forwarding
- Remaining 3 links are in blocking state
- All VLANs use the same forwarding paths

### Document Current State
Create a table showing port states:

| VLAN | S1 Ports | S2 Ports | S3 Ports |
|------|----------|----------|----------|
| 10 | Fa0/1-4 (Status) | Fa0/1-4 (Status) | Fa0/1-4 (Status) |
| 20 | Fa0/1-4 (Status) | Fa0/1-4 (Status) | Fa0/1-4 (Status) |
| 30 | Fa0/1-4 (Status) | Fa0/1-4 (Status) | Fa0/1-4 (Status) |
| 99 | Fa0/1-4 (Status) | Fa0/1-4 (Status) | Fa0/1-4 (Status) |

**Status codes**: FWD (Forwarding), BLK (Blocking)

---

## Task 4: Implement PVST+ Load Balancing

### Strategy
To achieve load balancing across all six trunks:
1. **VLAN 10**: S1 as root
2. **VLAN 20**: S2 as root  
3. **VLAN 30**: S3 as root
4. **VLAN 99**: Distribute as needed

### Step 1: Configure Root Bridges

#### Set S1 as Root for VLAN 10
```bash
S1(config)# spanning-tree vlan 10 root primary
# OR manually set priority
S1(config)# spanning-tree vlan 10 priority 4096
```

#### Set S2 as Root for VLAN 20
```bash
S2(config)# spanning-tree vlan 20 root primary
# OR manually set priority
S2(config)# spanning-tree vlan 20 priority 4096
```

#### Set S3 as Root for VLAN 30
```bash
S3(config)# spanning-tree vlan 30 root primary
# OR manually set priority
S3(config)# spanning-tree vlan 30 priority 4096
```

#### Configure Secondary Root Bridges (Optional)
```bash
# S2 as secondary root for VLAN 10
S2(config)# spanning-tree vlan 10 root secondary

# S3 as secondary root for VLAN 20
S3(config)# spanning-tree vlan 20 root secondary

# S1 as secondary root for VLAN 30
S1(config)# spanning-tree vlan 30 root secondary
```

### Step 2: Fine-tune with Port Priorities (if needed)
If automatic load balancing isn't achieved, manually adjust port priorities:

```bash
# Example: Prefer certain paths
S1(config)# interface fa0/1
S1(config-if)# spanning-tree vlan 10 port-priority 16

S2(config)# interface fa0/2
S2(config-if)# spanning-tree vlan 20 port-priority 16
```

### Step 3: Verify Load Balancing
After configuration changes, verify the results:

```bash
# Check spanning tree per VLAN
Switch# show spanning-tree vlan 10
Switch# show spanning-tree vlan 20
Switch# show spanning-tree vlan 30

# View summary
Switch# show spanning-tree summary
```

### Expected Results After Load Balancing

| VLAN | Root Bridge | Primary Forwarding Paths | Blocked Paths |
|------|-------------|-------------------------|---------------|
| 10 | S1 | S1-S2, S1-S3 direct | S2-S3 link |
| 20 | S2 | S2-S1, S2-S3 direct | S1-S3 link |
| 30 | S3 | S3-S1, S3-S2 direct | S1-S2 link |

---

## Task 5: Verification and Testing

### Verify Spanning Tree Configuration
```bash
# View spanning tree status for all VLANs
show spanning-tree

# Check specific VLAN
show spanning-tree vlan 10

# View bridge priorities
show spanning-tree bridge

# Check port roles and states
show spanning-tree interface fa0/1
```

### Test Connectivity
```bash
# From switches, ping management interfaces
S1# ping 172.17.99.12
S1# ping 172.17.99.13

# From PCs, test gateway connectivity
PC1> ping 172.17.10.1
PC2> ping 172.17.20.1
PC3> ping 172.17.30.1
```

### Monitor Traffic Distribution
```bash
# View interface statistics to confirm load balancing
show interfaces fa0/1 | include packets
show interfaces fa0/2 | include packets
show interfaces fa0/3 | include packets
show interfaces fa0/4 | include packets
```

---

## Understanding PVST+ Load Balancing

### How It Works
1. **Separate spanning tree per VLAN**
2. **Different root bridges per VLAN**
3. **Different forwarding paths per VLAN**
4. **Traffic distributed across all available links**

### Benefits
- **Improved bandwidth utilization**
- **Better network performance**
- **Redundancy maintained**
- **Automatic failover capabilities**

### Key Concepts
- **Root Bridge Selection**: Lowest bridge priority wins
- **Port Roles**: Root, Designated, Alternate, Backup
- **Port States**: Forwarding, Blocking, Listening, Learning
- **Load Distribution**: Different VLANs use different paths

---

## Troubleshooting Common Issues

### Issue 1: VLANs Not Learning from VTP
**Symptoms**: Client switches don't see VLANs from server
**Solutions**:
```bash
# Check VTP status
show vtp status

# Verify domain and password match
show vtp password

# Ensure trunk links are operational
show interfaces trunk
```

### Issue 2: Load Balancing Not Working
**Symptoms**: All VLANs still using same paths
**Solutions**:
```bash
# Verify root bridge configuration
show spanning-tree root

# Check if priorities are set correctly
show spanning-tree bridge

# Force recalculation
clear spanning-tree detected-protocols
```

### Issue 3: Connectivity Issues
**Symptoms**: Some VLANs can't communicate
**Solutions**:
```bash
# Check VLAN assignments
show vlan brief

# Verify trunk configuration
show interfaces switchport

# Check access port assignments
show running-config interface fa0/6
```

---

## Lab Completion Checklist

### Required Outcomes
- [ ] All 6 trunk links are carrying traffic
- [ ] Each user VLAN (10, 20, 30) has a different root bridge
- [ ] Load balancing is achieved across available paths
- [ ] All switches participate in forwarding for different VLANs
- [ ] Connectivity is maintained for all VLANs

### Verification Commands
```bash
# Document final configuration
show running-config

# Capture spanning tree state
show spanning-tree summary

# Verify load distribution
show spanning-tree root
```

---

## Task 6: Cleanup

### Save Configurations (Before Cleanup)
```bash
# On each switch
Switch# show running-config > tftp://server/switch-config.txt
# OR copy to notepad for documentation
```

### Reset Lab Environment
```bash
# Clear configurations
Switch# erase startup-config
Switch# delete vlan.dat
Switch# reload

# Physical cleanup
# Disconnect and store cables
# Reset PC network configurations
```

---

## Key Learning Points

### PVST+ Advantages
1. **Per-VLAN optimization** - Each VLAN can have optimal path
2. **Load distribution** - Better utilization of available bandwidth
3. **Scalability** - Supports large switched networks
4. **Backward compatibility** - Works with standard STP

### Best Practices
1. **Plan root bridge placement** - Consider traffic patterns
2. **Use secondary roots** - Provide redundancy
3. **Monitor performance** - Verify load distribution
4. **Document changes** - Keep configuration records

### Real-World Applications
- **Campus networks** with multiple VLANs
- **Data centers** requiring high availability
- **Branch offices** with redundant links
- **Service provider networks** with traffic separation

This lab demonstrates how proper STP configuration can transform an underutilized redundant network into an efficiently load-balanced infrastructure.
