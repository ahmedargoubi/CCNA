# CCNA Lab 2.8.2: Challenge Static Route Configuration

## Overview

This lab focuses on subnetting a given network address space and configuring static routes to enable communication between networks that are not directly connected. You will work with a hub-and-spoke topology where proper routing configuration is essential for end-to-end connectivity.

## Learning Objectives

Upon completion of this lab, you will be able to:
- Subnet an address space given specific requirements
- Assign appropriate addresses to interfaces and document them
- Cable a network according to the topology diagram
- Perform basic router configurations
- Configure and activate Serial and Ethernet interfaces
- Determine appropriate static, summary, and default routes
- Test and verify configurations
- Document the network implementation



## Final Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|----------------|
| BRANCH | Fa0/0 | 192.168.2.193 | 255.255.255.192 | N/A |
| BRANCH | S0/0/0 | 192.168.2.129 | 255.255.255.192 | N/A |
| HQ | Fa0/0 | 192.168.2.65 | 255.255.255.192 | N/A |
| HQ | S0/0/0 | 192.168.2.130 | 255.255.255.192 | N/A |
| HQ | S0/0/1 | 209.165.201.2 | 255.255.255.252 | N/A |
| ISP | Fa0/0 | 209.165.200.225 | 255.255.255.224 | N/A |
| ISP | S0/0/1 | 209.165.201.1 | 255.255.255.252 | N/A |
| PC1 | NIC | 192.168.2.253 | 255.255.255.192 | 192.168.2.193 |
| PC2 | NIC | 192.168.2.94 | 255.255.255.192 | 192.168.2.65 |
| Web Server | NIC | 209.165.200.253 | 255.255.255.224 | 209.165.200.225 |

---

## Task 1: Subnet the Address Space

### Step 1: Network Requirements Analysis

Given network: **192.168.2.0/24**
Requirement: Support **60 hosts** per subnet

### Step 2: Subnetting Calculations

**Questions and Answers:**

1. **How many subnets are needed?** 3 subnets (plus 1 for future expansion)

2. **Subnet calculations:**
   - To support 60 hosts: Need at least 6 host bits (2^6 = 64 addresses, 62 usable)
   - Original /24 network uses 24 network bits
   - New subnet mask: /26 (24 network + 2 subnet bits)
   - Subnet mask in decimal: **255.255.255.192**

3. **Subnet addresses:**
   - Subnet 0: **192.168.2.0/26** (reserved for future expansion)
   - Subnet 1: **192.168.2.64/26** (HQ LAN)
   - Subnet 2: **192.168.2.128/26** (WAN link HQ-BRANCH)
   - Subnet 3: **192.168.2.192/26** (BRANCH LAN)

4. **Usable hosts per subnet:** 62 hosts

### Step 3: Subnet Assignment

- **Subnet 1 (192.168.2.64/26)**: Assign to HQ LAN
- **Subnet 2 (192.168.2.128/26)**: Assign to WAN link between HQ and BRANCH
- **Subnet 3 (192.168.2.192/26)**: Assign to BRANCH LAN
- **Subnet 0 (192.168.2.0/26)**: Reserved for future expansion

---

## Task 2: Determine Interface Addresses

### Address Assignment Rules

1. **HQ LAN interface**: First valid host in subnet 1 = **192.168.2.65**
2. **PC2**: Last valid host in subnet 1 = **192.168.2.94**
3. **BRANCH WAN interface**: First valid host in subnet 2 = **192.168.2.129**
4. **HQ WAN interface**: Second valid host in subnet 2 = **192.168.2.130**
5. **BRANCH LAN interface**: First valid host in subnet 3 = **192.168.2.193**
6. **PC1**: Last valid host in subnet 3 = **192.168.2.253**

---

## Task 3: Basic Router Configuration

### Standard Configuration for All Routers

Apply these configurations to BRANCH, HQ, and ISP routers:

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname [BRANCH/HQ/ISP]
Router(config)# no ip domain-lookup
Router(config)# enable secret class
Router(config)# banner motd #Unauthorized Access Prohibited#
Router(config)# line console 0
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# logging synchronous
Router(config-line)# exec-timeout 15 0
Router(config-line)# exit
Router(config)# line vty 0 4
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# logging synchronous
Router(config-line)# exec-timeout 15 0
Router(config-line)# exit
```

---

## Task 4: Interface Configuration

### BRANCH Router Configuration

```cisco
BRANCH(config)# interface fa0/0
BRANCH(config-if)# ip address 192.168.2.193 255.255.255.192
BRANCH(config-if)# no shutdown
BRANCH(config-if)# exit
BRANCH(config)# interface s0/0/0
BRANCH(config-if)# ip address 192.168.2.129 255.255.255.192
BRANCH(config-if)# clock rate 64000
BRANCH(config-if)# no shutdown
BRANCH(config-if)# exit
BRANCH(config)# copy running-config startup-config
```

### HQ Router Configuration

```cisco
HQ(config)# interface fa0/0
HQ(config-if)# ip address 192.168.2.65 255.255.255.192
HQ(config-if)# no shutdown
HQ(config-if)# exit
HQ(config)# interface s0/0/0
HQ(config-if)# ip address 192.168.2.130 255.255.255.192
HQ(config-if)# no shutdown
HQ(config-if)# exit
HQ(config)# interface s0/0/1
HQ(config-if)# ip address 209.165.201.2 255.255.255.252
HQ(config-if)# clock rate 64000
HQ(config-if)# no shutdown
HQ(config-if)# exit
HQ(config)# copy running-config startup-config
```

### ISP Router Configuration

```cisco
ISP(config)# interface fa0/0
ISP(config-if)# ip address 209.165.200.225 255.255.255.224
ISP(config-if)# no shutdown
ISP(config-if)# exit
ISP(config)# interface s0/0/1
ISP(config-if)# ip address 209.165.201.1 255.255.255.252
ISP(config-if)# no shutdown
ISP(config-if)# exit
ISP(config)# copy running-config startup-config
```

---

## Task 5: Configure PC and Server Addresses

### PC1 Configuration
- IP Address: **192.168.2.253**
- Subnet Mask: **255.255.255.192**
- Default Gateway: **192.168.2.193**

### PC2 Configuration
- IP Address: **192.168.2.94**
- Subnet Mask: **255.255.255.192**
- Default Gateway: **192.168.2.65**

### Web Server Configuration
- IP Address: **209.165.200.253**
- Subnet Mask: **255.255.255.224**
- Default Gateway: **209.165.200.225**

---

## Task 6: Verify Next-Hop Connectivity

### Test Adjacent Device Connectivity

```cisco
# From BRANCH to HQ
BRANCH# ping 192.168.2.130

# From HQ to ISP
HQ# ping 209.165.201.1

# From PC1 to BRANCH
PC1> ping 192.168.2.193

# From PC2 to HQ
PC2> ping 192.168.2.65

# From Web Server to ISP
Web Server> ping 209.165.200.225
```

---

## Task 7: Configure Static Routing on BRANCH

### Step 1: Analyze BRANCH Routing Requirements

**Networks present in BRANCH routing table:**
- 192.168.2.128/26 (connected WAN link)
- 192.168.2.192/26 (connected LAN)

**Networks missing from BRANCH routing table:**
- 192.168.2.64/26 (HQ LAN)
- 209.165.201.0/30 (HQ-ISP link)
- 209.165.200.224/27 (ISP LAN)

**Analysis:** BRANCH is a stub router with only one path out. A default route is the most efficient solution.

### Step 2: Configure Default Route on BRANCH

```cisco
BRANCH(config)# ip route 0.0.0.0 0.0.0.0 serial0/0/0
```

### Step 3: Verify Configuration

```cisco
BRANCH# show ip route
```

**Note:** At this point, PC1 cannot ping PC2 because HQ doesn't know how to route back to the BRANCH LAN.

---

## Task 8: Configure Static Routing on HQ

### Step 1: Analyze HQ Routing Requirements

**Networks present in HQ routing table:**
- 192.168.2.128/26 (connected WAN link to BRANCH)
- 192.168.2.64/26 (connected LAN)
- 209.165.201.0/30 (connected WAN link to ISP)

**Networks missing from HQ routing table:**
- 192.168.2.192/26 (BRANCH LAN)
- 209.165.200.224/27 (ISP LAN)

**Analysis:** HQ needs two routes:
1. Static route to BRANCH LAN
2. Default route to ISP for internet traffic

### Step 2: Configure Static Route to BRANCH LAN

```cisco
HQ(config)# ip route 192.168.2.192 255.255.255.192 serial0/0/0
```

### Step 3: Configure Default Route to ISP

```cisco
HQ(config)# ip route 0.0.0.0 0.0.0.0 209.165.201.1
```

### Step 4: Verify Configuration

```cisco
HQ# show ip route
```

**Note:** Now PC1 can ping PC2, but neither can ping the Web Server because ISP doesn't know about the internal networks.

---

## Task 9: Configure Static Routing on ISP

### Step 1: Analyze ISP Routing Requirements

**Networks present in ISP routing table:**
- 209.165.201.0/30 (connected link to HQ)
- 209.165.200.224/27 (connected LAN)

**Networks missing from ISP routing table:**
- 192.168.2.64/26 (HQ LAN)
- 192.168.2.128/26 (HQ-BRANCH WAN link)
- 192.168.2.192/26 (BRANCH LAN)

**Analysis:** A single summary route can cover all missing networks.

### Step 2: Configure Summary Static Route

All missing networks can be summarized as 192.168.2.0/24:

```cisco
ISP(config)# ip route 192.168.2.0 255.255.255.0 209.165.201.2
```

### Step 3: Verify Configuration

```cisco
ISP# show ip route
```

---

## Task 10: Final Verification

### Connectivity Tests

All the following tests should be successful:

```cisco
# From PC2 to PC1
PC2> ping 192.168.2.253

# From PC2 to Web Server
PC2> ping 209.165.200.253

# From PC1 to Web Server
PC1> ping 209.165.200.253
```

### Routing Table Verification

**BRANCH routing table should show:**
- 192.168.2.128/26 (connected)
- 192.168.2.192/26 (connected)
- 0.0.0.0/0 (static default route)

**HQ routing table should show:**
- 192.168.2.64/26 (connected)
- 192.168.2.128/26 (connected)
- 192.168.2.192/26 (static)
- 209.165.201.0/30 (connected)
- 0.0.0.0/0 (static default route)

**ISP routing table should show:**
- 192.168.2.0/24 (static summary)
- 209.165.200.224/27 (connected)
- 209.165.201.0/30 (connected)

---

## Verification Commands

| Command | Purpose |
|---------|---------|
| `show ip route` | Display routing table |
| `show ip interface brief` | Show interface status and IP addresses |
| `ping [destination]` | Test connectivity |
| `traceroute [destination]` | Show path taken by packets |
| `show running-config` | Display current configuration |

---

## Troubleshooting Guide

### Common Issues and Solutions

#### 1. No Connectivity Between PCs
- **Check** interface status with `show ip interface brief`
- **Verify** IP addressing is correct
- **Confirm** static routes are configured properly
- **Test** next-hop connectivity first

#### 2. Partial Connectivity (Some devices can communicate, others cannot)
- **Verify** routing tables on all routers
- **Check** for missing static routes
- **Ensure** return paths exist (routes in both directions)

#### 3. Interface Down/Down Status
- **Check** cable connections
- **Verify** `no shutdown` command was applied
- **Check** clock rate on DCE interfaces

#### 4. Routing Issues
- **Use** `debug ip routing` to troubleshoot route installation
- **Verify** next-hop addresses are reachable
- **Check** subnet masks match between interfaces

---

## Key Concepts Learned

### 1. **VLSM (Variable Length Subnet Masking)**
- Efficient use of IP address space
- Custom subnet sizes based on requirements
- /26 subnets provide 62 usable host addresses

### 2. **Static Route Types**
- **Default Route (0.0.0.0/0)**: Used for stub networks and internet access
- **Network-Specific Routes**: Used for specific destination networks
- **Summary Routes**: Used to reduce routing table size

### 3. **Hub-and-Spoke Topology**
- Central router (HQ) connects multiple branch locations
- Efficient for centralized services
- Requires careful route planning

### 4. **Route Selection Principles**
- **Stub networks** use default routes
- **Hub routers** need specific routes for each spoke
- **ISP routers** use summary routes for customer networks

---

## Reflection Questions and Answers

**Q: If a default static route was not configured on BRANCH, how many individual static routes would be needed?**
**A:** 3 individual routes (to HQ LAN, HQ-ISP link, and ISP LAN)

**Q: If a summary static route was not configured on ISP, how many individual static routes would be needed?**
**A:** 3 individual routes (to HQ LAN, HQ-BRANCH link, and BRANCH LAN)

**Q: What are the advantages of using summary routes?**
**A:** Reduces routing table size, simplifies configuration, improves scalability, and reduces router memory usage.

This lab demonstrates the practical application of subnetting and static routing in a realistic network scenario, emphasizing the importance of proper planning and systematic configuration.
