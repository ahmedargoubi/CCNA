# OSPF DR and BDR Determination Guide

## Lab Overview

This lab explores the OSPF Designated Router (DR) and Backup Designated Router (BDR) election process on multi-access networks. You'll observe how roles change during network events and learn how to control elections using OSPF priorities.

## Network Topology

- **RA**: G0/0 = 192.168.1.1/24, Loopback0 = 192.168.31.11/32
- **RB**: G0/0 = 192.168.1.2/24, Loopback0 = 192.168.31.22/32
- **RC**: G0/0 = 192.168.1.3/24, Loopback0 = 192.168.31.33/32

All three routers connect to the same switch on the 192.168.1.0/24 network.

---

## Understanding DR and BDR

### Why Do We Need DR and BDR?

On multi-access networks (like Ethernet), without a DR:
- Every router would form adjacencies with every other router
- On a network with **n** routers, this creates **n(n-1)/2** adjacencies
- Example: 5 routers = 10 adjacencies, 10 routers = 45 adjacencies
- Excessive LSA flooding wastes bandwidth

### The Solution: DR and BDR

- **DR (Designated Router)**: Central point for LSA exchange
- **BDR (Backup Designated Router)**: Backup in case DR fails
- **DROTHER**: All other routers (not DR or BDR)

### How It Works

1. All routers form FULL adjacency with DR and BDR only
2. DROTHERs form 2-WAY adjacency with each other (not FULL)
3. DROTHERs send updates to DR (224.0.0.6 multicast)
4. DR floods updates to all routers (224.0.0.5 multicast)
5. If DR fails, BDR immediately becomes DR

---

## DR/BDR Election Process

### Election Rules (in order of priority)

1. **Highest OSPF Priority** wins (default = 1, range = 0-255)
   - Priority 0 = Cannot become DR/BDR
2. **Highest Router ID** (if priorities are equal)
3. **Highest Loopback IP** (used as Router ID if not manually set)
4. **Highest active interface IP** (if no loopback exists)

### Critical Election Behaviors

⚠️ **IMPORTANT: Elections are NOT preemptive!**
- Once a DR is elected, it remains DR until it fails
- A new router with higher priority joining the network does NOT trigger re-election
- The only way to force re-election is to reset the OSPF process or reboot routers

---

## Part 1: Examine DR and BDR Changing Roles

### Step 1: Wait for STP Convergence

When you open Packet Tracer:
- Link lights will be **amber** for 50 seconds
- This is **Spanning Tree Protocol (STP)** preventing loops
- Use **Fast Forward Time** button to skip the wait

---

### Step 2: Verify Initial OSPF Neighbor States

```bash
RA# show ip ospf neighbor
RB# show ip ospf neighbor
RC# show ip ospf neighbor
```

#### Expected Initial Output

**From RA's perspective:**
```
Neighbor ID      Pri   State           Dead Time   Address         Interface
192.168.31.33    2     FULL/DR         00:00:35    192.168.1.3     GigabitEthernet0/0
192.168.31.22    1     FULL/BDR        00:00:35    192.168.1.2     GigabitEthernet0/0
```

**From RB's perspective:**
```
Neighbor ID      Pri   State           Dead Time   Address         Interface
192.168.31.11    1     FULL/DROTHER    00:00:36    192.168.1.1     GigabitEthernet0/0
192.168.31.33    2     FULL/DR         00:00:36    192.168.1.3     GigabitEthernet0/0
```

**From RC's perspective:**
```
Neighbor ID      Pri   State           Dead Time   Address         Interface
192.168.31.11    1     FULL/DROTHER    00:00:39    192.168.1.1     GigabitEthernet0/0
192.168.31.22    1     FULL/BDR        00:00:38    192.168.1.2     GigabitEthernet0/0
```

#### Answer These Questions:

**Q1: Which router is the DR?**

**Answer: RC (Router ID 192.168.31.33)**

RC has priority 2, which is higher than RA and RB (both priority 1).

---

**Q2: Which router is the BDR?**

**Answer: RB (Router ID 192.168.31.22)**

Among routers with priority 1, RB has a higher Router ID (192.168.31.22) than RA (192.168.31.11).

---

**Q3: What is the OSPF state of router RA?**

**Answer: DROTHER**

RA is neither DR nor BDR, so it's a DROTHER (all other routers).

---

### Step 3: Enable OSPF Adjacency Debugging

```bash
RA# debug ip ospf adj
RB# debug ip ospf adj
```

**What this shows:**
- OSPF neighbor state changes
- DR/BDR election messages
- Adjacency formation process

---

### Step 4: Disable RC's Interface (Current DR Fails)

```bash
RC(config)# interface g0/0
RC(config-if)# shutdown
```

**Wait 30 seconds** for dead timers to expire (default = 40 seconds on broadcast networks).

#### What Happens?

1. RA and RB detect RC is down (dead timer expires)
2. RA and RB lose their DR
3. BDR (RB) automatically promotes to DR
4. New BDR election occurs between remaining routers

**Q4: According to the debug output, which router was elected DR and which router was elected BDR?**

**Answer:**
- **DR: RB** (was previously BDR, now promoted)
- **BDR: RA** (only remaining router, becomes BDR)

**Debug output shows:**
```
OSPF-1 ADJ   Gi0/0: Backup seen Event before WAIT timer
OSPF-1 ADJ   Gi0/0: DR/BDR election
OSPF-1 ADJ   Gi0/0: Elect BDR 192.168.31.11
OSPF-1 ADJ   Gi0/0: Elect DR 192.168.31.22
```

---

### Step 5: Restore RC's Interface (Former DR Returns)

```bash
RC(config)# interface g0/0
RC(config-if)# no shutdown
```

Wait for convergence and check neighbor states:

```bash
RC# show ip ospf neighbor

Neighbor ID      Pri   State           Dead Time   Address         Interface
192.168.31.22    1     FULL/DR         00:00:34    192.168.1.2     GigabitEthernet0/0
192.168.31.11    1     FULL/BDR        00:00:34    192.168.1.1     GigabitEthernet0/0
```

**Q5: Did DR and BDR roles change? Explain.**

**Answer: No, the roles did NOT change.**

**Explanation:**
- RC rejoins the network but sees RB is already DR and RA is already BDR
- **OSPF elections are NOT preemptive**
- Even though RC has higher priority (2), it cannot overthrow the existing DR
- RC becomes a DROTHER
- This prevents network instability from constant re-elections

**Key Principle:** Once elected, a DR remains DR until it fails or the OSPF process is reset.

---

### Step 6: Disable RB's Interface (Current DR Fails Again)

```bash
RB(config)# interface g0/0
RB(config-if)# shutdown
```

Wait 30 seconds for dead timers to expire.

**Q6: According to the debug output on RA, which router was elected DR and which router was elected BDR?**

**Answer:**
- **DR: RA** (was BDR, now promoted to DR)
- **BDR: RC** (only remaining router, becomes BDR)

**What happened:**
1. Current DR (RB) went down
2. Current BDR (RA) immediately promoted to DR
3. RC (the only other router) became BDR

---

### Step 7: Restore RB's Interface Again

```bash
RB(config)# interface g0/0
RB(config-if)# no shutdown
```

**Q7: Wait for the new DR/BDR elections to occur. Did DR and BDR roles change? Explain.**

**Answer: No, the roles did NOT change.**

**Explanation:**
- RA remains DR
- RC remains BDR
- RB becomes a DROTHER
- Again, elections are NOT preemptive
- RB cannot reclaim its former DR role by just rejoining

---

**Q8: What is the status of router RC now?**

**Answer: BDR (Backup Designated Router)**

Use command:
```bash
RC# show ip ospf interface g0/0
```

Look for: `Designated Router (ID) 192.168.31.11, Interface address 192.168.1.1`
and `Backup Designated router (ID) 192.168.31.33, Interface address 192.168.1.3`

---

### Step 8: Turn Off Debugging

```bash
RA# undebug all
RB# undebug all
```

Or simply:
```bash
RA# no debug all
RB# no debug all
```

---

## Part 2: Modify OSPF Priority and Force Elections

### Understanding OSPF Priority

- **Default Priority**: 1
- **Range**: 0-255
- **Priority 0**: Router cannot become DR or BDR (useful for low-powered routers)
- **Higher Priority**: More likely to become DR
- **Same Priority**: Highest Router ID wins

---

### Step 1: Configure OSPF Priorities

Set priorities to ensure RA becomes DR:

**On RA:**
```bash
RA(config)# interface g0/0
RA(config-if)# ip ospf priority 200
```

**On RB:**
```bash
RB(config)# interface g0/0
RB(config-if)# ip ospf priority 100
```

**On RC:**
```bash
RC(config)# interface g0/0
RC(config-if)# ip ospf priority 1
```

**Priority Summary:**
- RA: 200 (highest - should be DR)
- RB: 100 (middle - should be BDR)
- RC: 1 (lowest - should be DROTHER)

⚠️ **Note:** Simply changing priority does NOT trigger re-election!

---

### Step 2: Force Election by Resetting OSPF Process

Since elections are NOT preemptive, you must force a new election:

**Method 1: Clear OSPF Process (Preferred)**
```bash
RA# clear ip ospf process
*confirm* yes

RB# clear ip ospf process
*confirm* yes

RC# clear ip ospf process
*confirm* yes
```

**Method 2: Reload Routers (More disruptive)**
```bash
RA# reload
RB# reload
RC# reload
```

**What this does:**
- Resets all OSPF neighbor relationships
- Triggers new DR/BDR election
- Uses new priority values

---

### Step 3: Verify New DR and BDR Elections

Wait 2-3 minutes for convergence (or use Fast Forward Time).

```bash
RA# show ip ospf neighbor
RB# show ip ospf neighbor
RC# show ip ospf neighbor
```

#### Expected Output

**From RA (should be DR now):**
```
Neighbor ID      Pri   State           Dead Time   Address         Interface
192.168.31.22    100   FULL/BDR        00:00:35    192.168.1.2     GigabitEthernet0/0
192.168.31.33    1     FULL/DROTHER    00:00:35    192.168.1.3     GigabitEthernet0/0
```

**From RB (should be BDR now):**
```
Neighbor ID      Pri   State           Dead Time   Address         Interface
192.168.31.11    200   FULL/DR         00:00:35    192.168.1.1     GigabitEthernet0/0
192.168.31.33    1     FULL/DROTHER    00:00:35    192.168.1.3     GigabitEthernet0/0
```

**From RC (should be DROTHER now):**
```
Neighbor ID      Pri   State           Dead Time   Address         Interface
192.168.31.11    200   FULL/DR         00:00:35    192.168.1.1     GigabitEthernet0/0
192.168.31.22    100   FULL/BDR        00:00:35    192.168.1.2     GigabitEthernet0/0
```

**Q9: According to output from the show ip ospf neighbor command, which router is now DR and which router is now BDR?**

**Answer:**
- **DR: RA** (Router ID 192.168.31.11, Priority 200)
- **BDR: RB** (Router ID 192.168.31.22, Priority 100)
- **DROTHER: RC** (Router ID 192.168.31.33, Priority 1)

**Verification:**
- RA has highest priority (200) → DR ✓
- RB has second-highest priority (100) → BDR ✓
- RC has lowest priority (1) → DROTHER ✓

---

## Additional Verification Commands

### Check OSPF Interface Details

```bash
RA# show ip ospf interface g0/0
```

Look for:
```
Internet Address 192.168.1.1/24, Area 0
Process ID 1, Router ID 192.168.31.11, Network Type BROADCAST, Cost: 1
Designated Router (ID) 192.168.31.11, Interface address 192.168.1.1
Backup Designated router (ID) 192.168.31.22, Interface address 192.168.1.2
Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
```

### Check OSPF Interface Brief

```bash
RA# show ip ospf interface brief
```

Output shows:
```
Interface    PID   Area   IP Address/Mask    Cost  State Nbrs F/C
Gi0/0        1     0      192.168.1.1/24     1     DR    2/2
Lo0          1     0      192.168.31.11/32   1     LOOP  0/0
```

### Check Neighbor Details

```bash
RA# show ip ospf neighbor detail
```

---

## Troubleshooting Tips

### Neighbors Stuck in INIT or 2-WAY

**Problem:** Neighbors not reaching FULL state

**Solutions:**
1. Check for mismatched OSPF areas
2. Verify network types match
3. Check for ACLs blocking OSPF multicast (224.0.0.5, 224.0.0.6)
4. Ensure subnet masks match

### Wrong Router Elected as DR

**Problem:** Router with lower priority became DR

**Cause:** Elections are not preemptive

**Solution:**
```bash
# On all routers
Router# clear ip ospf process
```

### Election Not Happening After Priority Change

**Problem:** Changed priority but nothing changed

**Remember:** Must force re-election!

**Solutions:**
1. `clear ip ospf process` on all routers
2. Shutdown/no shutdown interface
3. Reload router (last resort)

### DR Election Takes Too Long

**Reason:** WAIT timer (default 40 seconds)

**What happens:**
- When interface comes up, router waits 40 seconds
- This allows all routers to discover each other
- Prevents suboptimal elections

**To speed up in lab:**
```bash
Router(config-if)# ip ospf hello-interval 5
Router(config-if)# ip ospf dead-interval 20
```
(Must match on all routers on the segment)

---

## Key Concepts Summary

### DR/BDR Election Rules

1. **Highest OSPF Priority** (0-255, default=1)
2. **Highest Router ID** (if priorities tie)
3. **Elections are NOT preemptive**
4. **Priority 0** = Cannot become DR/BDR

### Neighbor States

| State | Meaning |
|-------|---------|
| **FULL/DR** | Full adjacency with the DR |
| **FULL/BDR** | Full adjacency with the BDR |
| **FULL/DROTHER** | Full adjacency with a DROTHER (you're the DR/BDR) |
| **2-WAY/DROTHER** | 2-way communication only (both are DROTHERs) |

### When Elections Occur

✅ **Election happens when:**
- First routers come online
- DR fails (BDR promotes, new BDR elected)
- OSPF process is reset
- Router is rebooted

❌ **Election does NOT happen when:**
- New router with higher priority joins
- Priority is changed (without reset)
- Router returns after failure

### Network Types and DR/BDR

| Network Type | DR/BDR? | Default Hello | Default Dead |
|--------------|---------|---------------|--------------|
| **Broadcast** | Yes | 10 sec | 40 sec |
| **Non-Broadcast** | Yes | 30 sec | 120 sec |
| **Point-to-Point** | No | 10 sec | 40 sec |
| **Point-to-Multipoint** | No | 30 sec | 120 sec |

---

## Lab Completion Checklist

- [ ] Identified initial DR (RC) and BDR (RB)
- [ ] Understood RA is DROTHER
- [ ] Observed BDR promotion when DR fails
- [ ] Verified elections are NOT preemptive
- [ ] Configured OSPF priorities: RA=200, RB=100, RC=1
- [ ] Forced re-election using `clear ip ospf process`
- [ ] Verified final roles: RA=DR, RB=BDR, RC=DROTHER
- [ ] Understand why priority 0 prevents DR/BDR role
- [ ] Know how to troubleshoot DR/BDR issues

---

## Practice Questions

1. **If you add a fourth router with priority 255, will it become DR immediately?**
   - No! Elections are not preemptive. Must reset OSPF process.

2. **What happens if you set all routers to priority 0?**
   - No DR/BDR elected, all adjacencies remain in 2-WAY state, routing fails.

3. **Can a DROTHER router exchange LSAs with another DROTHER?**
   - No, DROTHERs only exchange LSAs with DR and BDR.

4. **Does BDR forward LSAs to DROTHERs?**
   - No, only DR forwards LSAs. BDR just listens and stays synchronized.

5. **On a point-to-point link, is there a DR election?**
   - No, only two routers exist, no need for DR/BDR.