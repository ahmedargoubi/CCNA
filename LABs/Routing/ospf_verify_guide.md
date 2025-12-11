# Verify Single-Area OSPFv2 Complete Guide

## Lab Overview

This lab focuses on verifying an existing OSPF network using various show commands. You'll analyze neighbor relationships, routing tables, and OSPF parameters, then add a new branch office LAN to the existing network.

## Network Topology

### Main Network (Enterprise)
- **R1**: Enterprise router with Internet access (G0/1: 64.100.54.6/30)
- **R2**: Enterprise router (central hub)
- **R3**: Branch office router
- **R4**: New branch office router (to be added)

### Connections
- **R1** ↔ **R2**: Serial link via 172.16.3.0/30
- **R1** ↔ **R3**: Serial link via 192.168.10.4/30
- **R2** ↔ **R3**: Serial link via 192.168.10.8/30
- **R3** ↔ **R4**: Will connect via Ethernet (192.168.1.0/24)
- **R1** → **ISP**: G0/1 (64.100.54.4/30)

### Access Credentials
- **Enterprise routers (R1, R2)**: Username: `BranchAdmin`, Password: `Branch1234`
- **Branch routers (R3, R4)**: Full access (enable mode)

---

## Understanding the Lab Structure

### Your Role
You are the **branch office network administrator** with:
- ✅ Full access to R3 and R4 (your branch routers)
- ⚠️ Read-only access to R1 and R2 (enterprise routers)
- 🎯 Task: Verify OSPF, then add new wireless LAN

### Why Limited Access?
In real networks:
- Branch admins don't have full control of core routers
- Security and change management require separation
- This simulates real-world administrative boundaries

---

## Essential OSPF Verification Commands

Before starting, familiarize yourself with these commands:

| Command | Purpose |
|---------|---------|
| `show ip interface brief` | Quick interface status overview |
| `show ip route` | Complete routing table |
| `show ip route ospf` | Only OSPF-learned routes |
| `show ip ospf neighbor` | OSPF neighbor adjacencies |
| `show ip protocols` | Routing protocol configuration |
| `show ip ospf` | OSPF process information |
| `show ip ospf interface` | OSPF interface details |
| `show ip ospf neighbor detail` | Detailed neighbor information |

---

## Part 1: Verify Existing OSPFv2 Network

### Initial Setup

**Wait for STP convergence:**
- Link lights will be amber initially (STP blocking/learning)
- Use **Fast Forward Time** button in Packet Tracer
- Continue only when all links are **green**
- This takes about 50 seconds in real-time

---

## Step 1: Verify OSPFv2 Operation on R1

### Login to R1

```bash
Router> enable
Username: BranchAdmin
Password: Branch1234
```

**Note:** You won't have full access - you're in privileged EXEC mode but can't enter config mode.

---

### Execute Show IP Route

```bash
R1# show ip route
```

**Expected Output:**
```
Gateway of last resort is 172.16.3.2 to network 0.0.0.0

     172.16.0.0/16 is variably subnetted, 5 subnets, 3 masks
C       172.16.1.0/24 is directly connected, GigabitEthernet0/0
L       172.16.1.1/32 is directly connected, GigabitEthernet0/0
O       172.16.2.0/24 [110/65] via 172.16.3.2, 00:02:18, Serial0/0/0
C       172.16.3.0/30 is directly connected, Serial0/0/0
L       172.16.3.1/32 is directly connected, Serial0/0/0
O       192.168.1.0/24 [110/65] via 192.168.10.6, 00:02:18, Serial0/0/1
     192.168.10.0/24 is variably subnetted, 3 subnets, 2 masks
C       192.168.10.4/30 is directly connected, Serial0/0/1
L       192.168.10.5/32 is directly connected, Serial0/0/1
O       192.168.10.8/30 [110/128] via 172.16.3.2, 00:02:18, Serial0/0/0
                        [110/128] via 192.168.10.6, 00:02:18, Serial0/0/1
O*E2    0.0.0.0/0 [110/1] via 172.16.3.2, 00:02:18, Serial0/0/0
```

---

### Answer These Questions:

**Q1: How did router R1 receive the default route?**

**Answer: Through OSPF as an External Type 2 (E2) route, propagated from another router using the `default-information originate` command.**

**Detailed explanation:**
- The route code `O*E2` indicates:
  - `O` = OSPF
  - `*` = Candidate default route
  - `E2` = External Type 2
- This means another router created a static default route and advertised it through OSPF
- The `default-information originate` command was used on that router

---

**Q2: From which router did R1 receive the default route?**

**Answer: R2 (172.16.3.2)**

**How we know:**
- The default route shows: `via 172.16.3.2`
- 172.16.3.2 is R2's Serial 0/0/0 interface
- R2 is connected to the Internet and originated the default route

---

**Q3: How can you filter the output of show ip route to show only the routes learned through OSPF?**

**Answer: Use the command `show ip route ospf`**

```bash
R1# show ip route ospf

O       172.16.2.0/24 [110/65] via 172.16.3.2, 00:05:32, Serial0/0/0
O       192.168.1.0/24 [110/65] via 192.168.10.6, 00:05:32, Serial0/0/1
O       192.168.10.8/30 [110/128] via 172.16.3.2, 00:05:32, Serial0/0/0
                        [110/128] via 192.168.10.6, 00:05:32, Serial0/0/1
O*E2    0.0.0.0/0 [110/1] via 172.16.3.2, 00:05:32, Serial0/0/0
```

**Other useful filters:**
- `show ip route | include O` - Shows lines containing "O"
- `show ip route | begin Gateway` - Shows from "Gateway" line onwards
- `show ip route | section ospf` - Shows OSPF section only

---

### Verify OSPF Neighbors on R1

```bash
R1# show ip ospf neighbor
```

**Expected Output:**
```
Neighbor ID      Pri   State           Dead Time   Address         Interface
192.168.10.6     0     FULL/  -        00:00:38    192.168.10.6    Serial0/0/1
172.16.3.2       0     FULL/  -        00:00:31    172.16.3.2      Serial0/0/0
```

---

### Answer These Questions:

**Q4: Which routers have formed adjacencies with router R1?**

**Answer: R2 and R3**

**Details:**
- **R2**: Address 172.16.3.2 (Neighbor ID 172.16.3.2)
- **R3**: Address 192.168.10.6 (Neighbor ID 192.168.10.6)

---

**Q5: What are the router IDs and state of the routers shown in the command output?**

**Answer:**
- **R2**: Router ID = 172.16.3.2, State = FULL
- **R3**: Router ID = 192.168.10.6, State = FULL

**Understanding the states:**
- **FULL** means adjacency is completely established
- **Dash (-)** after FULL means point-to-point link (no DR/BDR)
- **Priority 0** confirms point-to-point network type

---

**Q6: Are all of the adjacent routers shown in the output?**

**Answer: Yes, all adjacent routers are shown.**

**Explanation:**
- R1 only has direct connections to R2 and R3
- Both appear in the neighbor table
- R1 is NOT directly connected to R4 (if R4 were connected)
- OSPF only shows directly connected neighbors, not all routers in the domain

**Key concept:** OSPF neighbor table shows only routers on directly connected networks, not the entire OSPF topology.

---

### Test Connectivity to ISP

From PC1 command prompt:
```bash
C:\> ping 64.100.54.5
```

**Expected:** Success! ✓

**If it fails:**
- OSPF may still be converging
- Run `clear ip ospf process` on all routers:
  ```bash
  R1# clear ip ospf process
  Reset ALL OSPF processes? [no]: yes
  ```
- Wait 30 seconds and retry ping

---

## Step 2: Verify OSPFv2 Operation on R2

### Login to R2

```bash
Router> enable
Username: BranchAdmin
Password: Branch1234
```

---

### Verify R2's Routing Table

```bash
R2# show ip route
```

**Q7: How did router R2 learn the default route to the ISP?**

**Answer: R2 has a static default route configured directly, NOT learned through OSPF.**

**Explanation:**
- R2's routing table should show:
  ```
  S*   0.0.0.0/0 [1/0] via 64.100.54.5
  ```
  or
  ```
  S*   0.0.0.0/0 is directly connected, GigabitEthernet0/1
  ```
- The `S*` code means **Static** default route
- R2 is the **originating router** for the default route
- R2 then propagates this via OSPF to other routers (they see it as O*E2)

**Key distinction:**
- **R2 sees**: S* (Static)
- **R1, R3 see**: O*E2 (OSPF External)

---

### Verify OSPF Interface on R2's LAN

```bash
R2# show ip ospf interface g0/0
```

**Expected Output:**
```
GigabitEthernet0/0 is up, line protocol is up
  Internet address is 172.16.2.1/24, Area 0
  Process ID 1, Router ID 172.16.3.2, Network Type BROADCAST, Cost: 1
  Transmit Delay is 1 sec, State DR, Priority 1
  Designated Router (ID) 172.16.3.2, Interface address 172.16.2.1
  No backup designated router on this network
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
  Hello due in 00:00:03
  Index 1/1, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 0, maximum is 0
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 0, Adjacent neighbor count is 0
  Suppress hello for 0 neighbor(s)
```

---

### Answer These Questions:

**Q8: What type of OSPF network is attached to this interface?**

**Answer: BROADCAST**

**Explanation:**
- Ethernet interfaces default to BROADCAST network type
- Broadcast networks use DR/BDR elections
- Uses multicast addresses 224.0.0.5 and 224.0.0.6

**Network type comparison:**
| Interface Type | Default OSPF Network Type |
|----------------|---------------------------|
| Ethernet/FastEthernet/Gigabit | BROADCAST |
| Serial (HDLC/PPP) | POINT-TO-POINT |
| Frame Relay | NON-BROADCAST |

---

**Q9: Are OSPF hello packets being sent out this interface? Explain.**

**Answer: No, OSPF hello packets are NOT being sent out this interface.**

**Evidence from output:**
```
Suppress hello for 0 neighbor(s)
```

**Why:**
- G0/0 is configured as a **passive interface**
- Passive interfaces don't send OSPF hellos
- This saves bandwidth and CPU on LAN interfaces with no OSPF neighbors (only hosts)
- The network is still advertised in OSPF, but no protocol traffic is sent

**Configuration that caused this:**
```bash
R2(config-router)# passive-interface g0/0
```

**Look for "Hello due in" line:**
- If you see `Hello due in 00:00:03`, hellos ARE being sent
- If this line is missing or shows suppression, hellos are NOT being sent

---

### Test Connectivity from PC2

From PC2 command prompt:
```bash
C:\> ping 192.168.10.6
```

**Q10: Is it successful?**

**Answer: Yes, the ping should be successful.**

**Path:** PC2 → R2 → R3 (192.168.10.6)

**This confirms:**
- R2 can reach R3
- OSPF routing is working
- End-to-end connectivity exists

---

## Step 3: Verify OSPFv2 Operation on R3

### Verify OSPF Protocol Configuration

```bash
R3# show ip protocols
```

**Expected Output:**
```
Routing Protocol is "ospf 1"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 192.168.10.6
  Number of areas in this router is 1. 1 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
    192.168.1.0 0.0.0.255 area 0
    192.168.10.4 0.0.0.3 area 0
    192.168.10.8 0.0.0.3 area 0
  Routing Information Sources:
    Gateway         Distance      Last Update
    172.16.3.1           110      00:15:23
    172.16.3.2           110      00:15:23
  Distance: (default is 110)
```

---

**Q11: Router R3 is routing for which networks?**

**Answer: R3 is routing for three networks:**
1. **192.168.1.0/24** (G0/0 - LAN with PC3)
2. **192.168.10.4/30** (S0/0/0 - link to R1)
3. **192.168.10.8/30** (S0/0/1 - link to R2)

**How to identify:**
Look for the "Routing for Networks:" section in `show ip protocols` output.

**Additional network (not in OSPF yet):**
- **192.168.11.0/24** (G0/1) - This will be where R4 connects

---

### Verify OSPF Neighbor Details

```bash
R3# show ip ospf neighbor detail
```

**Expected Output:**
```
Neighbor 172.16.3.1, interface address 192.168.10.5
    In the area 0 via interface Serial0/0/0
    Neighbor priority is 0, State is FULL, 6 state changes
    DR is 0.0.0.0 BDR is 0.0.0.0
    Options is 0x12 in Hello (E-bit, L-bit)
    Options is 0x52 in DBD (E-bit, L-bit, O-bit)
    LLS Options is 0x1 (LR)
    Dead timer due in 00:00:32
    Neighbor is up for 00:17:45
    Index 1/1, retransmission queue length 0, number of retransmission 1
    First 0x0(0)/0x0(0) Next 0x0(0)/0x0(0)
    Last retransmission scan length is 1, maximum is 1
    Last retransmission scan time is 0 msec, maximum is 0 msec

Neighbor 172.16.3.2, interface address 192.168.10.9
    In the area 0 via interface Serial0/0/1
    Neighbor priority is 0, State is FULL, 6 state changes
    DR is 0.0.0.0 BDR is 0.0.0.0
    Options is 0x12 in Hello (E-bit, L-bit)
    Options is 0x52 in DBD (E-bit, L-bit, O-bit)
    LLS Options is 0x1 (LR)
    Dead timer due in 00:00:35
    Neighbor is up for 00:17:48
    Index 2/2, retransmission queue length 0, number of retransmission 1
    First 0x0(0)/0x0(0) Next 0x0(0)/0x0(0)
    Last retransmission scan length is 1, maximum is 1
    Last retransmission scan time is 0 msec, maximum is 0 msec
```

---

**Q12: What is the neighbor priority shown for the OSPF neighbor routers? This value is the default.**

**Answer: Priority 0 for both neighbors (R1 and R2).**

**Explanation:**
- Look for: `Neighbor priority is 0`
- Priority 0 means the router CANNOT become DR or BDR
- This is because these are **point-to-point** serial links
- Point-to-point links don't need DR/BDR
- Priority is automatically set to 0 on point-to-point interfaces

**Priority meanings:**
| Priority | Meaning |
|----------|---------|
| 0 | Cannot become DR/BDR |
| 1 | Default for broadcast/NBMA networks |
| 2-255 | Higher priority, more likely to be DR |

---

### Test Connectivity to ISP

From PC3 command prompt:
```bash
C:\> ping 64.100.54.5
```

**Q13: Is it successful?**

**Answer: Yes, the ping should be successful.**

**Path:** PC3 → R3 → R1 or R2 → ISP

**This confirms:**
- Default route is propagated to R3
- R3 can reach the Internet
- Complete OSPF routing is functional

---

## Part 2: Add New Branch Office LAN

### Step 1: Verify OSPFv2 Configuration on R4

R4 is pre-configured but not yet connected to the network.

```bash
R4# show run | begin router ospf
```

**Expected Output:**
```
router ospf 1
 router-id 192.168.11.1
 passive-interface GigabitEthernet0/0/1
 network 192.168.1.0 0.0.0.255 area 0
 network 192.168.11.0 0.0.0.255 area 0
!
```

---

**Q14: Which interface is configured to not send OSPF update packets?**

**Answer: GigabitEthernet0/0/1 (G0/0/1)**

**Explanation:**
- The command `passive-interface GigabitEthernet0/0/1` prevents OSPF hellos
- G0/0/1 connects to the wireless LAN (192.168.11.0/24)
- No OSPF routers on that LAN, only wireless clients
- Saves bandwidth and improves security

**R4's interfaces:**
- **G0/0/0**: Connects to R3 (192.168.1.0/24) - OSPF active
- **G0/0/1**: Connects to wireless clients (192.168.11.0/24) - OSPF passive

---

## Step 2: Connect Branch Office Router to Network

### Physical Connection

**Task:** Connect R4 to the existing network

**Cable type needed:** **Straight-through Ethernet cable**

**Connection:**
- From: **R4 GigabitEthernet0/0/0**
- To: **S3 (Switch) - any available port**

**Why this works:**
- R3's G0/0 is already connected to S3
- Both R3 and R4 are on 192.168.1.0/24 subnet
- They'll discover each other via OSPF on this shared segment

---

### Verify OSPF Adjacency on R4

After connecting the cable, wait 10-20 seconds for OSPF to converge.

```bash
R4# show ip ospf neighbor
```

**Expected Output:**
```
Neighbor ID      Pri   State           Dead Time   Address         Interface
192.168.10.6     1     FULL/BDR        00:00:35    192.168.1.1     GigabitEthernet0/0/0
```

---

**Q15: What state is displayed for router R3?**

**Answer: FULL/BDR**

**Breakdown:**
- **FULL** = Full adjacency established
- **BDR** = R3 is the Backup Designated Router

**Why FULL/BDR?**
- This is an Ethernet segment (broadcast network type)
- Broadcast networks require DR/BDR election
- R3 became BDR
- This is different from serial links which show FULL/- (no DR/BDR)

---

### Verify OSPF Adjacency on R3

```bash
R3# show ip ospf neighbor
```

**Expected Output:**
```
Neighbor ID      Pri   State           Dead Time   Address         Interface
192.168.11.1     1     FULL/DR         00:00:34    192.168.1.2     GigabitEthernet0/0
172.16.3.1       0     FULL/  -        00:00:38    192.168.10.5    Serial0/0/0
172.16.3.2       0     FULL/  -        00:00:31    192.168.10.9    Serial0/0/1
```

---

**Q16: Why is the state of router R4 different than the state of R1 and R2?**

**Answer: The state of R4 is different because:**

1. **R4 is on a broadcast network (Ethernet)**
   - State shows: **FULL/DR** or **FULL/BDR**
   - DR/BDR election occurs on broadcast networks
   - Interface: GigabitEthernet0/0

2. **R1 and R2 are on point-to-point networks (Serial)**
   - State shows: **FULL/-** (dash means no DR/BDR)
   - No DR/BDR needed on point-to-point links
   - Interfaces: Serial0/0/0 and Serial0/0/1

**Summary comparison:**

| Neighbor | Interface Type | Network Type | State Shown | DR/BDR? |
|----------|----------------|--------------|-------------|---------|
| R4 (192.168.11.1) | Ethernet | BROADCAST | FULL/DR | Yes |
| R1 (172.16.3.1) | Serial | POINT-TO-POINT | FULL/- | No |
| R2 (172.16.3.2) | Serial | POINT-TO-POINT | FULL/- | No |

---

### Determine DR/BDR Roles

**On the 192.168.1.0/24 segment:**

**Option 1: R4 is DR, R3 is BDR**
- R4 has higher Router ID (192.168.11.1)
- R3 has lower Router ID (192.168.10.6)

**Option 2: R3 is DR, R4 is BDR**
- If R3 came online first
- Elections are NOT preemptive
- First router becomes DR by default

**Most likely:** R4 is DR because it has the highest Router ID and came online after R3 was already DR-capable.

**To verify who is DR:**
```bash
R3# show ip ospf interface g0/0
```
Look for:
```
Designated Router (ID) 192.168.11.1, Interface address 192.168.1.2
Backup Designated router (ID) 192.168.10.6, Interface address 192.168.1.1
```

---

### Test End-to-End Connectivity

From Laptop (connected to R4's wireless network):

```bash
C:\> ipconfig
```

Verify Laptop got DHCP address in 192.168.11.0/24 range.

```bash
C:\> ping 172.16.2.2
```

**Q17: Is it successful?**

**Answer: Yes, the ping should be successful.**

**Path:** Laptop → R4 → R3 → R1 or R2 → PC2

**This confirms:**
- R4 successfully joined OSPF
- Routes to 192.168.11.0/24 are being propagated
- Full network connectivity is established
- DHCP is working on the wireless network
- Complete end-to-end routing through multiple hops

---

## Verification Summary

### What We Verified

✅ **OSPF Neighbor Relationships**
- All expected adjacencies are FULL
- Point-to-point vs. broadcast network states
- DR/BDR elections where appropriate

✅ **Route Propagation**
- All networks appear in routing tables
- Default route propagated as O*E2
- Correct next-hops selected

✅ **Interface Configuration**
- Passive interfaces on LAN segments
- Active interfaces on router-to-router links
- Correct network types (broadcast vs. point-to-point)

✅ **Connectivity**
- All PCs can reach each other
- All PCs can reach Internet (ISP router)
- New LAN (R4) integrated successfully

---

## Key OSPF Concepts Demonstrated

### 1. Neighbor States

| State | Meaning | When You See It |
|-------|---------|----------------|
| **FULL/-** | Full adjacency, no DR/BDR | Point-to-point links |
| **FULL/DR** | Full adjacency, neighbor is DR | Broadcast networks |
| **FULL/BDR** | Full adjacency, neighbor is BDR | Broadcast networks |
| **FULL/DROTHER** | Full adjacency, neighbor is neither | You are DR or BDR |
| **2-WAY/DROTHER** | Basic communication only | Both are DROTHERs |

---

### 2. Route Types in Routing Table

| Code | Route Type | Example |
|------|------------|---------|
| **C** | Directly connected | 172.16.1.0/24 is directly connected, G0/0 |
| **L** | Local (interface IP) | 172.16.1.1/32 is directly connected, G0/0 |
| **S** | Static | 0.0.0.0/0 is directly connected, S0/1/0 |
| **O** | OSPF intra-area | 172.16.2.0/24 [110/65] via 172.16.3.2 |
| **O*E2** | OSPF external type 2 (default) | 0.0.0.0/0 [110/1] via 172.16.3.2 |

---

### 3. Network Types

| Interface | Default Type | DR/BDR? | Hello/Dead |
|-----------|-------------|---------|------------|
| Ethernet | BROADCAST | Yes | 10/40 |
| Serial (PPP/HDLC) | POINT-TO-POINT | No | 10/40 |
| Serial (Frame Relay) | NON-BROADCAST | Yes | 30/120 |

---

### 4. Passive Interface Behavior

**Passive Interface:**
- ❌ Does NOT send OSPF Hello packets
- ❌ Does NOT form OSPF adjacencies
- ✅ STILL advertises the network in OSPF
- ✅ Saves bandwidth and CPU
- ✅ Improves security (no routing protocol exposed to hosts)

**Use cases:**
- LAN interfaces with only hosts (no routers)
- DMZ interfaces
- Management interfaces

---

## Complete Command Reference

### Routing Table Commands

```bash
show ip route                    # Complete routing table
show ip route ospf               # Only OSPF routes
show ip route 0.0.0.0           # Show default route
show ip route | include O        # Filter for OSPF lines
show ip route summary            # Route summary statistics
```

### OSPF Neighbor Commands

```bash
show ip ospf neighbor            # Basic neighbor info
show ip ospf neighbor detail     # Detailed neighbor info
show ip ospf neighbor s0/0/0     # Neighbors on specific interface
```

### OSPF Interface Commands

```bash
show ip ospf interface           # All OSPF interfaces
show ip ospf interface brief     # Summary of OSPF interfaces
show ip ospf interface g0/0      # Specific interface details
```

### OSPF Process Commands

```bash
show ip protocols                # Routing protocol summary
show ip ospf                     # OSPF process details
show ip ospf database            # OSPF link-state database
show run | section ospf          # OSPF configuration
show run | begin router ospf     # OSPF config and below
```

### General Interface Commands

```bash
show ip interface brief          # Interface status and IPs
show interface g0/0              # Detailed interface info
show interface status            # Interface up/down status
```

### Troubleshooting Commands

```bash
debug ip ospf adj                # Debug adjacency formation
debug ip ospf events             # Debug OSPF events
clear ip ospf process            # Reset OSPF process
undebug all                      # Disable all debugging
```

---

## Troubleshooting Guide

### Problem: Cannot Login to R1 or R2

**Symptoms:**
- Login fails with wrong username/password

**Solution:**
```
Username: BranchAdmin
Password: Branch1234
```
- Username is case-sensitive
- You only get privileged EXEC, not config mode

---

### Problem: No OSPF Neighbors Showing

**Checklist:**

1. **Verify interface is up:**
   ```bash
   show ip interface brief
   ```

2. **Check OSPF is enabled on interface:**
   ```bash
   show ip ospf interface
   ```

3. **Verify network statement includes interface:**
   ```bash
   show ip protocols
   ```

4. **Check for passive interface:**
   ```bash
   show ip ospf interface | include Passive
   ```

5. **Verify OSPF area matches:**
   - Both sides must be in same area

6. **Check timers match:**
   ```bash
   show ip ospf interface s0/0/0 | include Timer
   ```

---

### Problem: R4 Not Becoming DR

**Why this happens:**
- Elections are NOT preemptive
- R3 may have become DR first
- R4 joins as BDR or DROTHER

**To force re-election:**
```bash
R3# clear ip ospf process
R4# clear ip ospf process
```

Wait 40 seconds for new election.

---

### Problem: Routes Not Appearing in Routing Table

**Checklist:**

1. **Verify OSPF neighbors are FULL:**
   ```bash
   show ip ospf neighbor
   ```

2. **Check OSPF database:**
   ```bash
   show ip ospf database
   ```

3. **Verify area consistency:**
   - All interfaces must be in Area 0 