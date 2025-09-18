# CCNA Lab Guide: Basic Spanning Tree Protocol (STP)

## Lab Overview
**Lab 5.5.1: Basic Spanning Tree Protocol**

This lab demonstrates the fundamental concepts of Spanning Tree Protocol (STP) IEEE 802.1D, including root bridge election, port states, and topology change responses.

## Network Topology
```
    PC1 ──── S1 ──── S2 ──── PC2
             │  \   /  │     PC3
             │   \ /   │     PC4
             └──── S3 ──┘
```

## Equipment Required
- 3 Cisco Switches (2960 or similar)
- 4 PCs or laptops
- Console cables
- Ethernet cables
- Terminal emulation software

## IP Addressing Table
| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|-----------------|
| S1 | VLAN 1 | 172.17.10.1 | 255.255.255.0 | N/A |
| S2 | VLAN 1 | 172.17.10.2 | 255.255.255.0 | N/A |
| S3 | VLAN 1 | 172.17.10.3 | 255.255.255.0 | N/A |
| PC1 | NIC | 172.17.10.21 | 255.255.255.0 | 172.17.10.254 |
| PC2 | NIC | 172.17.10.22 | 255.255.255.0 | 172.17.10.254 |
| PC3 | NIC | 172.17.10.23 | 255.255.255.0 | 172.17.10.254 |
| PC4 | NIC | 172.17.10.27 | 255.255.255.0 | 172.17.10.254 |

## Learning Objectives
Upon completion of this lab, you will understand:
- Default behavior of Spanning Tree Protocol (STP)
- Root bridge election process
- Port roles and states in STP
- How STP responds to topology changes
- The importance of STP in preventing loops

## Lab Tasks

### Task 1: Basic Switch Configuration

#### Step 1: Physical Setup
1. **Cable the network** according to the topology diagram
2. **Connect console cables** to all three switches
3. **Power on** all devices

#### Step 2: Reset Switches to Default State
Execute these commands on **all three switches**:
```bash
# Enter privileged mode
Switch> enable

# Clear NVRAM and delete VLAN database
Switch# erase startup-config
Switch# delete vlan.dat
Switch# reload

# After reload, verify default VLAN configuration
Switch# show vlan
```

#### Step 3: Configure Basic Settings
Configure these settings on **all switches** (example for S1):

```bash
# Enter global configuration mode
Switch> enable
Switch# configure terminal

# Set hostname (S1, S2, S3 respectively)
Switch(config)# hostname S1

# Security configurations
S1(config)# enable secret class
S1(config)# no ip domain-lookup

# Console access
S1(config)# line console 0
S1(config-line)# password cisco
S1(config-line)# login

# VTY (Telnet/SSH) access
S1(config-line)# line vty 0 15
S1(config-line)# password cisco
S1(config-line)# login
S1(config-line)# end

# Save configuration
S1# copy running-config startup-config
```

### Task 2: Network Preparation

#### Step 1: Disable All Ports
On **all switches**, disable all ports initially:
```bash
S1(config)# interface range fa0/1-24
S1(config-if-range)# shutdown
S1(config-if-range)# interface range gi0/1-2
S1(config-if-range)# shutdown
```

#### Step 2: Configure Access Ports
Enable and configure access ports for end devices:

**On S1:**
```bash
S1(config)# interface fa0/3
S1(config-if)# switchport mode access
S1(config-if)# no shutdown
```

**On S2:**
```bash
S2(config)# interface range fa0/6, fa0/11, fa0/18
S2(config-if-range)# switchport mode access
S2(config-if-range)# no shutdown
```

#### Step 3: Configure Trunk Ports
Enable trunk ports for inter-switch connections:

**On all switches:**
```bash
S1(config)# interface range fa0/1, fa0/2
S1(config-if-range)# switchport mode trunk
S1(config-if-range)# no shutdown

S2(config)# interface range fa0/1, fa0/2
S2(config-if-range)# switchport mode trunk
S2(config-if-range)# no shutdown

S3(config)# interface range fa0/1, fa0/2
S3(config-if-range)# switchport mode trunk
S3(config-if-range)# no shutdown
```

#### Step 4: Configure Management Interfaces
Set up VLAN 1 interfaces for management:

```bash
# S1 Configuration
S1(config)# interface vlan1
S1(config-if)# ip address 172.17.10.1 255.255.255.0
S1(config-if)# no shutdown

# S2 Configuration
S2(config)# interface vlan1
S2(config-if)# ip address 172.17.10.2 255.255.255.0
S2(config-if)# no shutdown

# S3 Configuration
S3(config)# interface vlan1
S3(config-if)# ip address 172.17.10.3 255.255.255.0
S3(config-if)# no shutdown
```

#### Step 5: Verify Connectivity
Test connectivity between switches:
```bash
S1# ping 172.17.10.2
S1# ping 172.17.10.3
S2# ping 172.17.10.3
```

### Task 3: Configure Host PCs
Configure each PC with the appropriate IP settings from the addressing table.

### Task 4: Examine Spanning Tree Operation

#### Step 1: Display STP Information
On each switch, examine the spanning tree status:
```bash
S1# show spanning-tree
S2# show spanning-tree  
S3# show spanning-tree
```

#### Step 2: Analyze STP Output
**Key Information to Identify:**

1. **Bridge ID Components:**
   - **Priority:** Default 32768 + VLAN ID (32769 for VLAN 1)
   - **MAC Address:** Unique identifier for each switch

2. **Root Bridge:**
   - Switch with lowest Bridge ID becomes root
   - If priorities are equal, lowest MAC address wins

3. **Port Roles:**
   - **Root Port (Root):** Best path to root bridge
   - **Designated Port (Desg):** Best path on each segment
   - **Alternate Port (Altn):** Backup path (blocked state)

4. **Port States:**
   - **FWD (Forwarding):** Port is active and forwarding frames
   - **BLK (Blocking):** Port is blocked to prevent loops

#### Step 3: Answer Analysis Questions
Based on your output, determine:

1. **Bridge ID Priority** for each switch (typically 32769)
2. **Root Bridge** (switch with lowest MAC address)
3. **Blocked Ports** (if any)
4. **STP Election Process** understanding

### Task 5: Observe Topology Changes

#### Step 1: Enable STP Debugging
On all switches:
```bash
S1# debug spanning-tree events
S2# debug spanning-tree events
S3# debug spanning-tree events
```

#### Step 2: Simulate Link Failure
Shutdown a port on the root switch:
```bash
S1(config)# interface fa0/1
S1(config-if)# shutdown
```

#### Step 3: Monitor STP Convergence
Observe the debug output showing:
- **Root bridge changes**
- **Port state transitions**
- **Topology change notifications**

**Expected Port State Transitions:**
1. **Blocking → Listening** (15 seconds)
2. **Listening → Learning** (15 seconds)  
3. **Learning → Forwarding** (Total: 30 seconds)

#### Step 4: Verify New Topology
Check the new spanning tree configuration:
```bash
S2# show spanning-tree
S3# show spanning-tree
```

## Understanding STP Concepts

### Bridge ID Priority Calculation
```
Bridge ID Priority = Bridge Priority + System ID Extension
Example: 32768 + 1 (VLAN 1) = 32769
```

### Root Bridge Election Process
1. **Compare Bridge Priority** (lower wins)
2. **If tied, compare MAC Address** (lower wins)
3. **Root bridge designation** is network-wide per VLAN

### Port Selection Criteria
1. **Root Path Cost** (lower is better)
2. **Sender Bridge ID** (lower is better)
3. **Sender Port ID** (lower is better)

### STP Timers
- **Hello Time:** 2 seconds (BPDU transmission interval)
- **Forward Delay:** 15 seconds (Listening and Learning states)
- **Max Age:** 20 seconds (BPDU timeout)

## Common STP States and Roles

### Port States
| State | Description | Duration |
|-------|-------------|----------|
| Blocking | Receives BPDUs only | Permanent until topology change |
| Listening | Processes BPDUs, no MAC learning | 15 seconds |
| Learning | Learns MAC addresses | 15 seconds |
| Forwarding | Full operation | Permanent |
| Disabled | Port shutdown | N/A |

### Port Roles
| Role | Description |
|------|-------------|
| Root Port | Best path to root bridge |
| Designated Port | Forwards on network segment |
| Alternate Port | Backup to root port |
| Backup Port | Backup to designated port |

## Troubleshooting Tips

### Common Issues
1. **Incorrect Root Bridge:** Check Bridge IDs and priorities
2. **Slow Convergence:** Normal behavior (30-50 seconds)
3. **Unexpected Blocking:** Verify cable connections and costs
4. **No Connectivity:** Check for disabled ports or wrong VLAN

### Verification Commands
```bash
# Basic STP information
show spanning-tree

# Detailed interface information  
show spanning-tree interface fastethernet 0/1

# Root bridge information
show spanning-tree root

# STP statistics
show spanning-tree detail

# Port costs and priorities
show spanning-tree interface fastethernet 0/1 detail
```

## Lab Questions and Answers

### Analysis Questions
1. **What determines the root bridge?**
   - Lowest Bridge ID (Priority + MAC Address)

2. **Why are some ports blocked?**
   - To prevent switching loops in redundant topology

3. **How long does STP convergence take?**
   - Approximately 30-50 seconds (default timers)

4. **What happens during a topology change?**
   - STP recalculates best paths and adjusts port states

## Summary
This lab demonstrates:
- **STP prevents loops** in redundant switched networks
- **Root bridge election** based on Bridge ID
- **Port roles and states** maintain loop-free topology
- **Convergence time** is significant with 802.1D STP
- **Topology changes** trigger STP recalculation

Understanding these concepts is crucial for managing switched networks and is fundamental for CCNA certification.