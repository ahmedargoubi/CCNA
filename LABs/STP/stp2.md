# Lab 5.5.2: Challenge Spanning Tree Protocol - Complete Guide

## Overview
This lab demonstrates the configuration and behavior of Spanning Tree Protocol (STP) in a switched network environment. You'll learn to configure VLANs, VTP, standard STP, and Rapid STP while observing convergence times and optimization techniques.

## Learning Objectives
- Cable a network according to the topology diagram
- Configure basic switch settings and VLANs
- Configure VLAN Trunking Protocol (VTP)
- Observe and explain default STP (802.1D) behavior
- Modify spanning tree root placement
- Configure Rapid STP (802.1w) and observe improvements

## Network Topology
```
    S1 (Root for VLANs 1,10,20,30)
   /  \
  /    \
S2      S3 (Root for VLAN 99)
 \      /
  \    /
   PC1-PC3
```

## Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|-----------------|
| S1 | VLAN 99 | 172.17.99.11 | 255.255.255.0 | N/A |
| S2 | VLAN 99 | 172.17.99.12 | 255.255.255.0 | N/A |
| S3 | VLAN 99 | 172.17.99.13 | 255.255.255.0 | N/A |
| PC1 | NIC | 172.17.10.21 | 255.255.255.0 | 172.17.10.12 |
| PC2 | NIC | 172.17.20.22 | 255.255.255.0 | 172.17.20.12 |
| PC3 | NIC | 172.17.30.23 | 255.255.255.0 | 172.17.30.12 |

## Port Assignments (Switch S2)

| Ports | Assignment | Network |
|-------|------------|---------|
| Fa0/1-0/4 | 802.1q Trunks (Native VLAN 99) | 172.17.99.0/24 |
| Fa0/5-0/10 | VLAN 30 – Guest (Default) | 172.17.30.0/24 |
| Fa0/11-0/17 | VLAN 10 – Faculty/Staff | 172.17.10.0/24 |
| Fa0/18-0/24 | VLAN 20 – Students | 172.17.20.0/24 |

---

## Task 1: Prepare the Network

### Step 1: Cable the Network
- Connect switches according to the topology diagram
- Use FastEthernet ports Fa0/1-Fa0/4 for inter-switch connections
- Set up console connections to all three switches

### Step 2: Clear Existing Configurations
Execute the following commands on each switch:

```bash
# Clear NVRAM and VLAN database
Switch# erase startup-config
Switch# delete vlan.dat
Switch# reload

# Verify default VLAN configuration
Switch# show vlan
```

### Step 3: Disable All Ports Initially
On each switch (S1, S2, S3):

```bash
Switch(config)# interface range fa0/1-24
Switch(config-if-range)# shutdown
Switch(config-if-range)# interface range gi0/1-2
Switch(config-if-range)# shutdown
```

### Step 4: Re-enable User Ports on S2
```bash
S2(config)# interface range fa0/6, fa0/11, fa0/18
S2(config-if-range)# switchport mode access
S2(config-if-range)# no shutdown
```

---

## Task 2: Basic Switch Configuration

Configure each switch with the following settings:

```bash
# Basic configuration (repeat for S1, S2, S3)
Switch> enable
Switch# configure terminal
Switch(config)# hostname S1  # (Change to S2, S3 accordingly)
Switch(config)# enable secret class
Switch(config)# no ip domain-lookup
Switch(config)# line console 0
Switch(config-line)# password cisco
Switch(config-line)# login
Switch(config-line)# line vty 0 15
Switch(config-line)# password cisco
Switch(config-line)# login
Switch(config-line)# end
Switch# copy running-config startup-config
```

---

## Task 3: Configure Host PCs

Configure the Ethernet interfaces on each PC with the addresses from the addressing table:
- PC1: 172.17.10.21/24, Gateway: 172.17.10.12
- PC2: 172.17.20.22/24, Gateway: 172.17.20.12
- PC3: 172.17.30.23/24, Gateway: 172.17.30.12

---

## Task 4: Configure VLANs

### Step 1: Configure VTP

| Switch | VTP Mode | VTP Domain | VTP Password |
|--------|----------|------------|--------------|
| S1 | Server | Lab5 | cisco |
| S2 | Client | Lab5 | cisco |
| S3 | Client | Lab5 | cisco |

**Configure S1 (VTP Server):**
```bash
S1(config)# vtp mode server
S1(config)# vtp domain Lab5
S1(config)# vtp password cisco
```

**Configure S2 and S3 (VTP Clients):**
```bash
S2(config)# vtp mode client
S2(config)# vtp domain Lab5
S2(config)# vtp password cisco
```

### Step 2: Configure Trunk Links
On all switches (S1, S2, S3):

```bash
Switch(config)# interface range fa0/1-4
Switch(config-if-range)# switchport mode trunk
Switch(config-if-range)# switchport trunk native vlan 99
Switch(config-if-range)# no shutdown
```

### Step 3: Create VLANs (On VTP Server S1)

| VLAN ID | VLAN Name |
|---------|-----------|
| 99 | management |
| 10 | faculty-staff |
| 20 | students |
| 30 | guest |

```bash
S1(config)# vlan 99
S1(config-vlan)# name management
S1(config-vlan)# exit
S1(config)# vlan 10
S1(config-vlan)# name faculty-staff
S1(config-vlan)# exit
S1(config)# vlan 20
S1(config-vlan)# name students
S1(config-vlan)# exit
S1(config)# vlan 30
S1(config-vlan)# name guest
```

### Step 4: Verify VLAN Distribution
```bash
S2# show vlan brief
S3# show vlan brief
```

### Step 5: Configure Management Interface
On each switch:

```bash
# S1
S1(config)# interface vlan99
S1(config-if)# ip address 172.17.99.11 255.255.255.0
S1(config-if)# no shutdown

# S2
S2(config)# interface vlan99
S2(config-if)# ip address 172.17.99.12 255.255.255.0
S2(config-if)# no shutdown

# S3
S3(config)# interface vlan99
S3(config-if)# ip address 172.17.99.13 255.255.255.0
S3(config-if)# no shutdown
```

**Test connectivity:**
```bash
S1# ping 172.17.99.12
S1# ping 172.17.99.13
S2# ping 172.17.99.13
```

### Step 6: Assign Access Ports to VLANs (On S2)
```bash
S2(config)# interface range fa0/5-10
S2(config-if-range)# switchport access vlan 30
S2(config-if-range)# interface range fa0/11-17
S2(config-if-range)# switchport access vlan 10
S2(config-if-range)# interface range fa0/18-24
S2(config-if-range)# switchport access vlan 20
```

---

## Task 5: Configure Spanning Tree

### Step 1: Examine Default STP Configuration
```bash
# View spanning tree for all VLANs
S1# show spanning-tree

# View specific VLAN
S1# show spanning-tree vlan 99
```

**Key observations:**
- Each VLAN has its own spanning tree instance (PVST+)
- Root selection is based on lowest Bridge ID (Priority + MAC address)
- Default priority is 32768 + VLAN ID

### Step 2: Analysis Questions
Based on the output, answer:
1. **Bridge ID Priority for VLAN 99:** All switches have 32867 (32768 + 99)
2. **Root Switch:** Determined by lowest MAC address when priorities are equal
3. **Blocking Ports:** Non-root switches will have some ports in blocking state
4. **Root Election:** Lowest Bridge ID wins (Priority first, then MAC address)

---

## Task 6: Optimize STP (Root Bridge Selection)

### Configure Root Bridges for Load Balancing
**Set S1 as root for VLANs 1, 10, 20, 30:**
```bash
S1(config)# spanning-tree vlan 1 priority 4096
S1(config)# spanning-tree vlan 10 priority 4096
S1(config)# spanning-tree vlan 20 priority 4096
S1(config)# spanning-tree vlan 30 priority 4096
```

**Set S3 as root for VLAN 99:**
```bash
S3(config)# spanning-tree vlan 99 priority 4096
```

### Verify Changes
```bash
S1# show spanning-tree vlan 99
S3# show spanning-tree vlan 99
```

**Benefits of root selection:**
- Load distribution across multiple trunk links
- Better utilization of available bandwidth
- Improved redundancy planning

---

## Task 7: Observe STP Convergence (802.1D)

### Preparation
1. Configure PC3 with IP 172.17.99.23/24
2. Reassign S2 port Fa0/6 to VLAN 99:
   ```bash
   S2(config)# interface fa0/6
   S2(config-if)# switchport access vlan 99
   ```

### Monitor Topology Changes
1. Enable STP debugging on S1:
   ```bash
   S1# debug spanning-tree events
   ```

2. Start continuous ping from PC3:
   ```bash
   PC3> ping -t 172.17.99.11
   ```

3. Disconnect trunk cables on S1 (Fa0/1 and Fa0/3)

### Expected Results
- **Convergence time:** 30-50 seconds
- **States:** Listening → Learning → Forwarding
- **Service interruption:** Significant downtime during convergence

---

## Task 8: Configure Rapid STP (802.1w)

Enable Rapid STP on all switches:
```bash
# Configure on S1, S2, and S3
Switch(config)# spanning-tree mode rapid-pvst
```

**Verify configuration:**
```bash
Switch# show spanning-tree summary
```

---

## Task 9: Observe RSTP Convergence

Repeat the convergence test from Task 7:

1. Restore disconnected cables
2. Enable debugging and start continuous ping
3. Disconnect trunk cables again

### Expected Results with RSTP
- **Convergence time:** Less than 1 second
- **Service interruption:** Minimal or no packet loss
- **Improved states:** Proposal/Agreement mechanism

**Key RSTP improvements:**
- Faster convergence through proposal/agreement
- Backup and alternate port roles
- Edge port recognition
- Backward compatibility with 802.1D

---

## Task 10: Cleanup

1. Erase configurations:
   ```bash
   Switch# erase startup-config
   Switch# delete vlan.dat
   Switch# reload
   ```

2. Disconnect and store cabling
3. Restore PC network settings

---

## Key Concepts Summary

### STP Fundamentals
- **Purpose:** Prevents switching loops in redundant topologies
- **Operation:** Blocks redundant paths, maintains loop-free topology
- **Root Bridge:** Central reference point, sends BPDUs
- **Port States:** Blocking, Listening, Learning, Forwarding

### PVST+ Features
- Per-VLAN spanning tree instances
- VLAN-based load balancing
- Independent root bridges per VLAN

### RSTP Improvements
- Faster convergence (sub-second)
- Enhanced port roles and states
- Proposal/Agreement mechanism
- Backward compatibility

### Best Practices
1. **Plan root bridge placement** in distribution layer
2. **Configure primary and secondary roots** manually
3. **Use RSTP** for faster convergence
4. **Monitor STP topology** changes
5. **Document VLAN-to-root mappings** for load balancing

---

## Troubleshooting Tips

### Common Issues
- **Trunk negotiation failures:** Verify trunk configuration on both ends
- **VTP synchronization problems:** Check domain name and password case-sensitivity
- **STP convergence delays:** Consider RSTP implementation
- **VLAN assignment errors:** Verify port-to-VLAN mappings

### Verification Commands
```bash
# STP Status
show spanning-tree
show spanning-tree vlan [vlan-id]
show spanning-tree summary

# VLAN Information
show vlan brief
show interfaces trunk

# VTP Status
show vtp status
show vtp password
```

This completes the comprehensive guide for the Spanning Tree Protocol lab. The configuration demonstrates both traditional STP limitations and the improvements offered by Rapid STP in modern network environments.