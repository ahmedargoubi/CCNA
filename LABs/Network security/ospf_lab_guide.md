# OSPF Basic Configuration Lab Guide

## Learning Objectives

Upon completion of this lab, you will be able to:
- Cable a network according to the topology diagram
- Erase startup configuration and reload routers to default state
- Perform basic configuration tasks on routers
- Configure and activate interfaces
- Configure OSPF routing on all routers
- Configure OSPF router IDs
- Verify OSPF routing using show commands
- Configure a static default route
- Propagate default route to OSPF neighbors
- Configure OSPF Hello and Dead Timers
- Configure OSPF on a multi-access network
- Configure OSPF priority
- Understand the OSPF election process
- Document the OSPF configuration

## Lab Overview

This lab consists of two scenarios:
1. **Scenario A**: Basic OSPF Configuration - Learn to configure OSPF using VLSM subnetting
2. **Scenario B**: Multi-access Network Configuration - Learn OSPF election process for DR/BDR selection

## Scenario A: Basic OSPF Configuration

### Network Topology

```
     PC1                    PC2                    PC3
(172.16.1.20/28)      (10.10.10.10/24)      (172.16.1.35/29)
        |                     |                     |
   Fa0/0|               Fa0/0|               Fa0/0|
   172.16.1.17/28      10.10.10.1/24       172.16.1.33/29
        |                     |                     |
        R1                    R2                    R3
        |S0/0/0        S0/0/0|    |S0/0/1    S0/0/0|
        |192.168.10.1/30     |    |192.168.10.9/30 |
        |                    |    |                |
        +--------------------+    +---------+------+
        S0/0/1                            S0/0/1
        192.168.10.5/30                  192.168.10.6/30
                              |        |
                              +--------+
                            192.168.10.10/30
```

### Addressing Table

| Device | Interface | IP Address      | Subnet Mask       | Default Gateway |
|--------|-----------|-----------------|-------------------|-----------------|
| R1     | Fa0/0     | 172.16.1.17     | 255.255.255.240   | N/A             |
|        | S0/0/0    | 192.168.10.1    | 255.255.255.252   | N/A             |
|        | S0/0/1    | 192.168.10.5    | 255.255.255.252   | N/A             |
| R2     | Fa0/0     | 10.10.10.1      | 255.255.255.0     | N/A             |
|        | S0/0/0    | 192.168.10.2    | 255.255.255.252   | N/A             |
|        | S0/0/1    | 192.168.10.9    | 255.255.255.252   | N/A             |
| R3     | Fa0/0     | 172.16.1.33     | 255.255.255.248   | N/A             |
|        | S0/0/0    | 192.168.10.6    | 255.255.255.252   | N/A             |
|        | S0/0/1    | 192.168.10.10   | 255.255.255.252   | N/A             |
| PC1    | NIC       | 172.16.1.20     | 255.255.255.240   | 172.16.1.17     |
| PC2    | NIC       | 10.10.10.10     | 255.255.255.0     | 10.10.10.1      |
| PC3    | NIC       | 172.16.1.35     | 255.255.255.248   | 172.16.1.33     |

## Task 1: Prepare the Network

### Step 1: Cable the Network
- Connect the routers according to the topology diagram
- Ensure all required interfaces are available
- Use appropriate cable types (straight-through for different devices, crossover if needed)

### Step 2: Clear Existing Configurations
```bash
Router> enable
Router# erase startup-config
Router# reload
```

## Task 2: Basic Router Configuration

Configure each router (R1, R2, R3) with the following basic settings:

```bash
Router> enable
Router# configure terminal
Router(config)# hostname [R1/R2/R3]
Router(config)# no ip domain-lookup
Router(config)# enable secret cisco
Router(config)# banner motd "Unauthorized Access Prohibited"
Router(config)# line console 0
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# exit
Router(config)# line vty 0 4
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# exit
```

## Task 3: Configure Interface Addresses

### Configure Router Interfaces

**Router R1:**
```bash
R1(config)# interface fastethernet 0/0
R1(config-if)# ip address 172.16.1.17 255.255.255.240
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface serial 0/0/0
R1(config-if)# ip address 192.168.10.1 255.255.255.252
R1(config-if)# clock rate 64000
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface serial 0/0/1
R1(config-if)# ip address 192.168.10.5 255.255.255.252
R1(config-if)# no shutdown
R1(config-if)# exit
```

**Router R2:**
```bash
R2(config)# interface fastethernet 0/0
R2(config-if)# ip address 10.10.10.1 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# interface serial 0/0/0
R2(config-if)# ip address 192.168.10.2 255.255.255.252
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# interface serial 0/0/1
R2(config-if)# ip address 192.168.10.9 255.255.255.252
R2(config-if)# clock rate 64000
R2(config-if)# no shutdown
R2(config-if)# exit
```

**Router R3:**
```bash
R3(config)# interface fastethernet 0/0
R3(config-if)# ip address 172.16.1.33 255.255.255.248
R3(config-if)# no shutdown
R3(config-if)# exit

R3(config)# interface serial 0/0/0
R3(config-if)# ip address 192.168.10.6 255.255.255.252
R3(config-if)# clock rate 64000
R3(config-if)# no shutdown
R3(config-if)# exit

R3(config)# interface serial 0/0/1
R3(config-if)# ip address 192.168.10.10 255.255.255.252
R3(config-if)# no shutdown
R3(config-if)# exit
```

### Verify Configuration
```bash
Router# show ip interface brief
```

### Save Configuration
```bash
Router# copy running-config startup-config
```

## Task 4: Configure OSPF on Router R1

```bash
R1(config)# router ospf 1
R1(config-router)# network 172.16.1.16 0.0.0.15 area 0
R1(config-router)# network 192.168.10.0 0.0.0.3 area 0
R1(config-router)# network 192.168.10.4 0.0.0.3 area 0
R1(config-router)# end
```

## Task 5: Configure OSPF on Routers R2 and R3

**Router R2:**
```bash
R2(config)# router ospf 1
R2(config-router)# network 10.10.10.0 0.0.0.255 area 0
R2(config-router)# network 192.168.10.0 0.0.0.3 area 0
R2(config-router)# network 192.168.10.8 0.0.0.3 area 0
R2(config-router)# end
```

**Router R3:**
```bash
R3(config)# router ospf 1
R3(config-router)# network 172.16.1.32 0.0.0.7 area 0
R3(config-router)# network 192.168.10.4 0.0.0.3 area 0
R3(config-router)# network 192.168.10.8 0.0.0.3 area 0
R3(config-router)# end
```

## Task 6: Configure OSPF Router IDs

### Understanding Router ID Priority
OSPF Router ID is determined by:
1. Manually configured router-id command (highest priority)
2. Highest IP address of any loopback interface
3. Highest active IP address on physical interfaces

### Configure Loopback Interfaces

**Router R1:**
```bash
R1(config)# interface loopback 0
R1(config-if)# ip address 10.1.1.1 255.255.255.255
R1(config-if)# exit
```

**Router R2:**
```bash
R2(config)# interface loopback 0
R2(config-if)# ip address 10.2.2.2 255.255.255.255
R2(config-if)# exit
```

**Router R3:**
```bash
R3(config)# interface loopback 0
R3(config-if)# ip address 10.3.3.3 255.255.255.255
R3(config-if)# exit
```

### Reload Routers to Apply New Router IDs
```bash
Router# copy running-config startup-config
Router# reload
```

### Alternative: Manual Router ID Configuration
```bash
R1(config)# router ospf 1
R1(config-router)# router-id 10.4.4.4
R1(config-router)# exit
R1# clear ip ospf process
```

## Task 7: Verify OSPF Operation

### Check OSPF Neighbors
```bash
Router# show ip ospf neighbor
```

### Check OSPF Protocol Information
```bash
Router# show ip protocols
```

### View Router ID Information
```bash
Router# show ip ospf
Router# show ip ospf interface
```

## Task 8: Examine OSPF Routes

```bash
Router# show ip route
```

**Route Code Legend:**
- **O** = OSPF routes
- **C** = Connected routes
- **O*E2** = OSPF External Type 2 (default route)

## Task 9: Configure OSPF Cost

### Understanding OSPF Cost Calculation
OSPF Cost = Reference Bandwidth / Interface Bandwidth
- Default Reference Bandwidth = 100 Mbps (10^8)
- Serial Interface Default = 1544 Kbps

### Method 1: Using Bandwidth Command
```bash
R1(config)# interface serial 0/0/0
R1(config-if)# bandwidth 64
R1(config-if)# interface serial 0/0/1
R1(config-if)# bandwidth 64

R2(config)# interface serial 0/0/0
R2(config-if)# bandwidth 64
R2(config)# interface serial 0/0/1
R2(config-if)# bandwidth 64
```

### Method 2: Using ip ospf cost Command
```bash
R3(config)# interface serial 0/0/0
R3(config-if)# ip ospf cost 1562
R3(config-if)# interface serial 0/0/1
R3(config-if)# ip ospf cost 1562
```

### Verify OSPF Interface Cost
```bash
Router# show ip ospf interface
```

## Task 10: Configure Default Route Redistribution

### Step 1: Create Simulated ISP Connection
```bash
R1(config)# interface loopback 1
R1(config-if)# ip address 172.30.1.1 255.255.255.252
```

### Step 2: Configure Static Default Route
```bash
R1(config)# ip route 0.0.0.0 0.0.0.0 loopback1
```

### Step 3: Redistribute Default Route in OSPF
```bash
R1(config)# router ospf 1
R1(config-router)# default-information originate
```

### Step 4: Verify Default Route Distribution
```bash
R2# show ip route
```
Look for: `O*E2 0.0.0.0/0 [110/1] via 192.168.10.1`

## Task 11: Advanced OSPF Features

### Adjust Reference Bandwidth
```bash
R1(config)# router ospf 1
R1(config-router)# auto-cost reference-bandwidth 10000

R2(config)# router ospf 1
R2(config-router)# auto-cost reference-bandwidth 10000

R3(config)# router ospf 1
R3(config-router)# auto-cost reference-bandwidth 10000
```

### Configure Hello and Dead Timers
```bash
R1(config)# interface serial 0/0/0
R1(config-if)# ip ospf hello-interval 5
R1(config-if)# ip ospf dead-interval 20

R2(config)# interface serial 0/0/0
R2(config-if)# ip ospf hello-interval 5
R2(config-if)# ip ospf dead-interval 20
```

**Important:** Hello and Dead timers must match on both sides of the link!

### Verify Timer Configuration
```bash
Router# show ip ospf interface serial 0/0/0
```

## Verification Commands Reference

| Command | Purpose |
|---------|---------|
| `show ip ospf neighbor` | Display OSPF neighbors |
| `show ip ospf` | Show OSPF process information |
| `show ip ospf interface` | Display OSPF interface details |
| `show ip protocols` | Show routing protocol configuration |
| `show ip route` | Display routing table |
| `show ip route ospf` | Show only OSPF routes |
| `debug ip ospf adj` | Debug OSPF adjacency formation |

## Common OSPF Network Commands

### Wildcard Mask Calculation
- Subnet Mask: 255.255.255.252 → Wildcard: 0.0.0.3
- Subnet Mask: 255.255.255.240 → Wildcard: 0.0.0.15
- Subnet Mask: 255.255.255.248 → Wildcard: 0.0.0.7
- Subnet Mask: 255.255.255.0 → Wildcard: 0.0.0.255

### OSPF Network Types
- **Point-to-Point**: Serial links, no DR/BDR election
- **Broadcast**: Ethernet links, DR/BDR election occurs
- **Non-Broadcast**: Frame Relay, manual neighbor configuration

## Troubleshooting OSPF

### Common Issues and Solutions

1. **Adjacency Not Forming:**
   - Check Hello/Dead timers match
   - Verify area IDs match
   - Confirm network statements are correct
   - Check access-lists blocking OSPF packets

2. **Router ID Conflicts:**
   - Use unique router IDs
   - Configure loopback interfaces
   - Use router-id command

3. **Missing Routes:**
   - Verify network statements include all interfaces
   - Check OSPF process is running
   - Verify area configurations

### Debug Commands
```bash
Router# debug ip ospf adj
Router# debug ip ospf hello
Router# debug ip ospf packet
Router# undebug all  # To stop debugging
```

## Lab Completion Checklist

- [ ] Basic router configurations completed
- [ ] Interface IP addresses configured and verified
- [ ] OSPF process started on all routers
- [ ] Network statements configured correctly
- [ ] Router IDs configured and verified
- [ ] OSPF adjacencies established
- [ ] Routing tables populated with OSPF routes
- [ ] Default route configured and redistributed
- [ ] OSPF costs adjusted appropriately
- [ ] Hello/Dead timers configured (if required)
- [ ] All configurations saved to startup-config

## Configuration Backup

Remember to document and save all configurations:

```bash
Router# show running-config
Router# show ip route
Router# show ip protocols
Router# show ip ospf neighbor
Router# show ip ospf interface brief
```

Save these outputs to text files for future reference and troubleshooting.

---

This completes the basic OSPF configuration lab. The lab provides hands-on experience with OSPF fundamentals including neighbor relationships, area configuration, route redistribution, and advanced features like cost manipulation and timer adjustments.