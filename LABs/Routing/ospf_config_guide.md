# OSPF Configuration Complete Guide

## Lab Overview

This lab covers configuring OSPFv2 (Open Shortest Path First version 2) on three routers in a point-to-point topology. You'll learn three different methods of enabling OSPF on interfaces and how to optimize OSPF by configuring passive interfaces.

## Network Topology

- **R1** connects to R2 and R3 via serial links, with a LAN network 192.168.10.0/24
- **R2** connects to R1 and R3 via serial links, with a LAN network 192.168.20.0/24
- **R3** connects to R1 and R2 via serial links, with a LAN network 192.168.30.0/24

---

## Part 1: Configure Router IDs

### Why Router IDs Matter
The OSPF router ID uniquely identifies each router in the OSPF domain. It's used in the election of the Designated Router (DR) and Backup Designated Router (BDR).

### Configuration Steps

**On R1:**
```
R1(config)# router ospf 10
R1(config-router)# router-id 1.1.1.1
```

**On R2:**
```
R2(config)# router ospf 10
R2(config-router)# router-id 2.2.2.2
```

**On R3:**
```
R3(config)# router ospf 10
R3(config-router)# router-id 3.3.3.3
```

**Notes:**
- Process ID `10` is locally significant (doesn't need to match on other routers)
- Router IDs are manually set for consistency and predictability
- Format follows IPv4 address structure but doesn't need to be an actual interface IP

---

## Part 2: Configure OSPF Networks

### Method 1: R1 - Network Statements with Wildcard Masks

This method uses the network address and wildcard mask to enable OSPF on matching interfaces.

#### Pre-Configuration Questions (Step 1)

Before configuring R1, answer these questions to understand the configuration:

**Q1: How many statements are required to configure OSPF to route all the networks attached to router R1?**

**Answer: 3 statements**

R1 has three networks attached:
- 192.168.10.0/24 (G0/0/0 - LAN)
- 10.1.1.0/30 (S0/1/0 - to R2)
- 10.1.1.4/30 (S0/1/1 - to R3)

---

**Q2: The LAN attached to router R1 has a /24 mask. What is the equivalent of this mask in dotted decimal representation?**

**Answer: 255.255.255.0**

The /24 notation means the first 24 bits are network bits (all 1s), and the last 8 bits are host bits (all 0s).

---

**Q3: Subtract the dotted decimal subnet mask from 255.255.255.255. What is the result?**

**Answer: 0.0.0.255**

Calculation:
```
  255.255.255.255
- 255.255.255.0
-----------------
  0.  0.  0. 255
```

This is the **wildcard mask** for /24 networks.

---

**Q4: What is the dotted decimal equivalent of the /30 subnet mask?**

**Answer: 255.255.255.252**

The /30 notation means the first 30 bits are network bits. In the last octet:
- Binary: 11111100
- Decimal: 252

---

**Q5: Subtract the dotted decimal representation of the /30 mask from 255.255.255.255. What is the result?**

**Answer: 0.0.0.3**

Calculation:
```
  255.255.255.255
- 255.255.255.252
-----------------
  0.  0.  0.  3
```

This is the **wildcard mask** for /30 networks.

---

#### Understanding Wildcard Masks

**For /24 networks:**
- Subnet mask: 255.255.255.0
- Wildcard calculation: 255.255.255.255 - 255.255.255.0 = 0.0.0.255

**For /30 networks:**
- Subnet mask: 255.255.255.252
- Wildcard calculation: 255.255.255.255 - 255.255.255.252 = 0.0.0.3

#### R1 Configuration

```
R1(config)# router ospf 10
R1(config-router)# network 192.168.10.0 0.0.0.255 area 0
R1(config-router)# network 10.1.1.0 0.0.0.3 area 0
R1(config-router)# network 10.1.1.4 0.0.0.3 area 0
```

**Explanation:**
- First statement: Enables OSPF on G0/0/0 (192.168.10.1/24)
- Second statement: Enables OSPF on S0/1/0 (10.1.1.1/30)
- Third statement: Enables OSPF on S0/1/1 (10.1.1.5/30)
- All networks are in Area 0 (backbone area)

#### Verification

```
R1# show running-config | section ospf
R1# show ip protocols
```

---

### Method 2: R2 - Interface IPs with Quad-Zero Masks

This method uses the exact interface IP address with a 0.0.0.0 wildcard mask, matching only that specific interface.

#### R2 Configuration

```
R2(config)# router ospf 10
R2(config-router)# network 192.168.20.1 0.0.0.0 area 0
R2(config-router)# network 10.1.1.2 0.0.0.0 area 0
R2(config-router)# network 10.1.1.9 0.0.0.0 area 0
```

**Explanation:**
- First statement: Enables OSPF on G0/0/0 (192.168.20.1/24)
- Second statement: Enables OSPF on S0/1/0 (10.1.1.2/30)
- Third statement: Enables OSPF on S0/1/1 (10.1.1.9/30)
- Quad-zero mask (0.0.0.0) means "match this exact IP only"

**Advantages:**
- More precise control
- Easier to see which specific interfaces are included
- No wildcard mask calculations needed

---

### Method 3: R3 - Direct Interface Configuration

This method configures OSPF directly on the interface, without using network statements.

#### R3 Configuration

**On GigabitEthernet 0/0/0:**
```
R3(config)# interface g0/0/0
R3(config-if)# ip ospf 10 area 0
```

**On Serial 0/1/0:**
```
R3(config)# interface s0/1/0
R3(config-if)# ip ospf 10 area 0
```

**On Serial 0/1/1:**
```
R3(config)# interface s0/1/1
R3(config-if)# ip ospf 10 area 0
```

**Explanation:**
- Process ID must match the OSPF process (10)
- Area ID must be specified (0)
- No network statements needed in router configuration mode

**Advantages:**
- Most explicit and clear method
- Immediately shows which interfaces run OSPF
- Easier troubleshooting

---

## Part 3: Configure Passive Interfaces

### Why Use Passive Interfaces?

OSPF sends Hello packets and routing updates out all OSPF-enabled interfaces. On LAN interfaces with no OSPF neighbors, this traffic:
- Wastes bandwidth
- Wastes CPU cycles
- Creates unnecessary security exposure
- Provides no benefit

### Which Interfaces Should Be Passive?

**LAN Interfaces (no OSPF routers attached):**
- R1: G0/0/0 (connects to PC1)
- R2: G0/0/0 (connects to PC2)
- R3: G0/0/0 (connects to PC3)

**Serial Interfaces should NOT be passive** (they connect to other OSPF routers)

### Configuration

**On R1:**
```
R1(config)# router ospf 10
R1(config-router)# passive-interface g0/0/0
```

**On R2:**
```
R2(config)# router ospf 10
R2(config-router)# passive-interface g0/0/0
```

**On R3:**
```
R3(config)# router ospf 10
R3(config-router)# passive-interface g0/0/0
```

### What Happens?
- The network is still advertised in OSPF
- No OSPF Hello packets are sent out the interface
- OSPF routing updates are not sent out the interface
- The router still listens for OSPF packets (though none are expected)

---

## Part 4: Verification Commands

### Check OSPF Neighbors

```
R1# show ip ospf neighbor
```

**Expected output:**
- You should see 2 neighbors on R1 (R2 and R3)
- Each neighbor shows: Neighbor ID, Priority, State (FULL), Dead Time, Interface

### Check OSPF-Enabled Interfaces

```
R1# show ip ospf interface
R1# show ip ospf interface brief
```

**What to verify:**
- All expected interfaces are running OSPF
- Process ID is correct (10)
- Area is correct (0)
- Passive interfaces show as passive

### Check OSPF Protocol Status

```
R1# show ip protocols
```

**What to verify:**
- Router ID is correct
- Networks being advertised
- Passive interfaces listed
- OSPF neighbors

### Check OSPF Routes

```
R1# show ip route ospf
```

**Expected routes:**
- R1 should learn about 192.168.20.0/24 and 192.168.30.0/24
- R2 should learn about 192.168.10.0/24 and 192.168.30.0/24
- R3 should learn about 192.168.10.0/24 and 192.168.20.0/24

### Check Running Configuration

```
R1# show running-config | section ospf
```

Displays only OSPF-related configuration lines.

### Detailed OSPF Information

```
R1# show ip ospf
R1# show ip ospf database
```

---

## Testing Connectivity

### From PCs

**Test from PC1:**
```
PC1> ping 192.168.20.10
PC1> ping 192.168.30.10
```

**Test from PC2:**
```
PC2> ping 192.168.10.10
PC2> ping 192.168.30.10
```

**Test from PC3:**
```
PC3> ping 192.168.10.10
PC3> ping 192.168.20.10
```

All pings should succeed if OSPF is configured correctly.

---

## Troubleshooting Tips

### Neighbors Not Forming

1. **Check if OSPF is enabled on both interfaces:**
   ```
   show ip ospf interface brief
   ```

2. **Verify interfaces are up:**
   ```
   show ip interface brief
   ```

3. **Check for matching area numbers:**
   - Both sides of a link must be in the same area

4. **Verify network types match:**
   ```
   show ip ospf interface s0/1/0
   ```

### Missing Routes

1. **Verify OSPF neighbors are in FULL state:**
   ```
   show ip ospf neighbor
   ```

2. **Check if networks are being advertised:**
   ```
   show ip protocols
   ```

3. **Look at OSPF database:**
   ```
   show ip ospf database
   ```

### Configuration Errors

**To remove incorrect network statements:**
```
R1(config-router)# no network 10.1.1.0 0.0.0.3 area 0
```

**To change router ID (requires restart):**
```
R1(config-router)# no router-id 1.1.1.1
R1(config-router)# router-id 1.1.1.2
R1# clear ip ospf process
```

---

## Quick Reference Tables

### Wildcard Mask Calculations

| Subnet Mask | CIDR | Wildcard Mask |
|-------------|------|---------------|
| 255.255.255.252 | /30 | 0.0.0.3 |
| 255.255.255.0 | /24 | 0.0.0.255 |
| 255.255.0.0 | /16 | 0.0.255.255 |
| 255.0.0.0 | /8 | 0.255.255.255 |

**Formula:** 255.255.255.255 - Subnet Mask = Wildcard Mask

### OSPF Network Types

| Type | Hello Timer | Dead Timer | DR/BDR Election |
|------|-------------|------------|-----------------|
| Point-to-Point | 10 sec | 40 sec | No |
| Broadcast | 10 sec | 40 sec | Yes |

### Three Methods Comparison

| Method | Configuration Location | Precision | Use Case |
|--------|----------------------|-----------|----------|
| Network + Wildcard | Router config mode | Medium | Classic OSPF, subnet-based |
| Network + Quad-zero | Router config mode | High | IP-specific matching |
| Interface command | Interface config mode | Highest | Most explicit, easiest to audit |

---

## Key Concepts Summary

- **Process ID**: Locally significant number identifying the OSPF process
- **Router ID**: Unique identifier for each router in OSPF domain
- **Area**: Logical grouping of OSPF networks (Area 0 is backbone)
- **Wildcard Mask**: Inverse of subnet mask, determines which IPs match
- **Passive Interface**: Suppresses OSPF hello packets while still advertising the network
- **Network Statement**: Enables OSPF on interfaces with matching IPs
- **FULL State**: Indicates OSPF neighbors have fully synchronized databases

---

## Lab Completion Checklist

- [ ] All three routers have OSPF process 10 enabled
- [ ] Router IDs configured: R1=1.1.1.1, R2=2.2.2.2, R3=3.3.3.3
- [ ] All networks advertised in OSPF
- [ ] R1 uses network statements with wildcard masks
- [ ] R2 uses network statements with quad-zero masks
- [ ] R3 uses interface configuration method
- [ ] All LAN interfaces (G0/0/0) configured as passive
- [ ] Each router has 2 OSPF neighbors in FULL state
- [ ] All PCs can ping each other
- [ ] Verification commands show correct OSPF operation