# EtherChannel Configuration Lab - Complete Guide

## Lab Overview

**Objective:** Configure three types of EtherChannel links between switches using PAgP, LACP, and negotiated LACP protocols.

**Topology:**
```
        S1
       /  \
  PAgP/    \LACP
     /      \
   S3 ------ S2
    (Negotiated LACP)
```

**Port Channel Configuration:**

| Channel | Switches | Ports | Protocol |
|---------|----------|-------|----------|
| **1** | S1-S3 | F0/21, F0/22 | PAgP (desirable) |
| **2** | S1-S2 | G0/1, G0/2 | LACP (active) |
| **3** | S2-S3 | F0/23, F0/24 | Negotiated LACP (passive/active) |

---

## Part 1: Configure Basic Switch Settings

### Step 1: Assign Hostnames

**Switch 1:**
```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname S1
S1(config)# exit
```

**Switch 2:**
```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname S2
S2(config)# exit
```

**Switch 3:**
```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname S3
S3(config)# exit
```

---

### Step 2: Verify Port Status Before Configuration

**Check interface status on each switch:**

```cisco
S1# show interfaces | include Ethernet
S1# show interface status
S1# show interfaces trunk
```

**What to look for:**
- Port operational status (up/down)
- Speed and duplex settings
- Current VLAN assignments
- Trunk status

---

### Step 3: Configure Trunk Ports

**Configure all ports that will be used for EtherChannels as trunks:**

**On S1:**
```cisco
S1(config)# interface range f0/21-22
S1(config-if-range)# switchport mode trunk
S1(config-if-range)# exit

S1(config)# interface range g0/1-2
S1(config-if-range)# switchport mode trunk
S1(config-if-range)# exit
```

**On S2:**
```cisco
S2(config)# interface range g0/1-2
S2(config-if-range)# switchport mode trunk
S2(config-if-range)# exit

S2(config)# interface range f0/23-24
S2(config-if-range)# switchport mode trunk
S2(config-if-range)# exit
```

**On S3:**
```cisco
S3(config)# interface range f0/21-22
S3(config-if-range)# switchport mode trunk
S3(config-if-range)# exit

S3(config)# interface range f0/23-24
S3(config-if-range)# switchport mode trunk
S3(config-if-range)# exit
```

**Verify trunks:**
```cisco
S1# show interfaces trunk
```

---

## Part 2: Configure EtherChannel with Cisco PAgP

### What is PAgP?

**PAgP (Port Aggregation Protocol):**
- Cisco proprietary protocol
- Negotiates EtherChannel formation
- Two modes: **desirable** (active) and **auto** (passive)

### Step 1: Configure Port Channel 1 (S1 ↔ S3)

**Configure on S1:**

```cisco
S1# configure terminal

! Shutdown interfaces first (important!)
S1(config)# interface range f0/21-22
S1(config-if-range)# shutdown

! Add to channel group 1 with PAgP desirable mode
S1(config-if-range)# channel-group 1 mode desirable

! Bring interfaces back up
S1(config-if-range)# no shutdown
S1(config-if-range)# exit

! Configure the logical port-channel interface as trunk
S1(config)# interface port-channel 1
S1(config-if)# switchport mode trunk
S1(config-if)# exit
```

**Expected message:**
```
Creating a port-channel interface Port-channel 1
```

**Configure on S3:**

```cisco
S3# configure terminal

! Shutdown interfaces first
S3(config)# interface range f0/21-22
S3(config-if-range)# shutdown

! Add to channel group 1 with PAgP desirable mode
S3(config-if-range)# channel-group 1 mode desirable

! Bring interfaces back up
S3(config-if-range)# no shutdown
S3(config-if-range)# exit

! Configure the logical port-channel interface as trunk
S3(config)# interface port-channel 1
S3(config-if)# switchport mode trunk
S3(config-if)# exit
```

---

### Step 2: Verify Port Channel 1 Status

**Verify on S1:**

```cisco
S1# show etherchannel summary
```

**Expected Output:**
```
Flags:  D - down        P - in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port

Number of channel-groups in use: 1
Number of aggregators:           1

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------
1      Po1(SU)       PAgP        F0/21(P)  F0/22(P)
```

**Key indicators:**
- **Po1(SU)**: S = Layer 2, U = In use
- **PAgP**: Protocol being used
- **F0/21(P) F0/22(P)**: P = In port-channel

**Verify trunk status:**

```cisco
S1# show interfaces trunk
```

**Expected Output:**
```
Port        Mode         Encapsulation  Status        Native vlan
Po1         on           802.1q         trunking      1
G0/1        on           802.1q         trunking      1
G0/2        on           802.1q         trunking      1
```

**Additional verification:**

```cisco
S1# show spanning-tree

! Look for Po1 in the output
```

**Troubleshooting if it doesn't work:**
```cisco
! Shutdown and bring back up
S1(config)# interface range f0/21-22
S1(config-if-range)# shutdown
S1(config-if-range)# no shutdown
```

---

## Part 3: Configure 802.3ad LACP EtherChannel

### What is LACP?

**LACP (Link Aggregation Control Protocol):**
- IEEE 802.3ad open standard
- Industry standard (works with non-Cisco devices)
- Two modes: **active** (actively negotiates) and **passive** (waits for negotiation)

### Step 1: Configure Port Channel 2 (S1 ↔ S2)

**Configure on S1:**

```cisco
S1# configure terminal

! Shutdown interfaces first
S1(config)# interface range g0/1-2
S1(config-if-range)# shutdown

! Add to channel group 2 with LACP active mode
S1(config-if-range)# channel-group 2 mode active

! Bring interfaces back up
S1(config-if-range)# no shutdown
S1(config-if-range)# exit

! Configure the logical port-channel interface as trunk
S1(config)# interface port-channel 2
S1(config-if)# switchport mode trunk
S1(config-if)# exit
```

**Configure on S2:**

```cisco
S2# configure terminal

! Shutdown interfaces first
S2(config)# interface range g0/1-2
S2(config-if-range)# shutdown

! Add to channel group 2 with LACP active mode
S2(config-if-range)# channel-group 2 mode active

! Bring interfaces back up
S2(config-if-range)# no shutdown
S2(config-if-range)# exit

! Configure the logical port-channel interface as trunk
S2(config)# interface port-channel 2
S2(config-if)# switchport mode trunk
S2(config-if)# exit
```

---

### Step 2: Verify Port Channel 2 Status

**Verify on S1 and S2:**

```cisco
S1# show etherchannel summary
```

**Expected Output:**
```
Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------
1      Po1(SU)       PAgP        F0/21(P)  F0/22(P)
2      Po2(SU)       LACP        G0/1(P)   G0/2(P)
```

**Detailed verification:**

```cisco
S1# show etherchannel 2 port-channel
```

**Check protocol:**

```cisco
S1# show etherchannel protocol
```

**Expected Output:**
```
Channel-group listing:
----------------------
Group: 1
----------
Protocol: PAgP

Group: 2
----------
Protocol: LACP
```

---

## Part 4: Configure Redundant EtherChannel Link

### Understanding EtherChannel Modes

**Mode options for channel-group command:**

| Mode | Protocol | Behavior |
|------|----------|----------|
| **active** | LACP | Actively negotiate (unconditionally) |
| **passive** | LACP | Wait for LACP device (conditionally) |
| **desirable** | PAgP | Actively negotiate (unconditionally) |
| **auto** | PAgP | Wait for PAgP device (conditionally) |
| **on** | None | Force on (no negotiation) |

**Compatible mode combinations:**

**LACP:**
- active ↔ active ✅
- active ↔ passive ✅
- passive ↔ passive ❌ (Won't form!)

**PAgP:**
- desirable ↔ desirable ✅
- desirable ↔ auto ✅
- auto ↔ auto ❌ (Won't form!)

---

### Step 1: Configure Port Channel 3 (S2 ↔ S3)

**This uses "negotiated LACP" - one active, one passive**

**Configure on S2 (passive mode):**

```cisco
S2# configure terminal

! Shutdown interfaces first
S2(config)# interface range f0/23-24
S2(config-if-range)# shutdown

! Add to channel group 3 with LACP passive mode
S2(config-if-range)# channel-group 3 mode passive

! Bring interfaces back up
S2(config-if-range)# no shutdown
S2(config-if-range)# exit

! Configure the logical port-channel interface as trunk
S2(config)# interface port-channel 3
S2(config-if)# switchport mode trunk
S2(config-if)# exit
```

**Configure on S3 (active mode):**

```cisco
S3# configure terminal

! Shutdown interfaces first
S3(config)# interface range f0/23-24
S3(config-if-range)# shutdown

! Add to channel group 3 with LACP active mode
S3(config-if-range)# channel-group 3 mode active

! Bring interfaces back up
S3(config-if-range)# no shutdown
S3(config-if-range)# exit

! Configure the logical port-channel interface as trunk
S3(config)# interface port-channel 3
S3(config-if)# switchport mode trunk
S3(config-if)# exit
```

**Why this works:**
```
S2 (passive): "I'll use LACP if you initiate"
S3 (active): "I want to use LACP!"
Result: EtherChannel forms! ✅
```

---

### Step 2: Verify Port Channel 3 Status

**Verify on S2:**

```cisco
S2# show etherchannel summary
```

**Expected Output:**
```
Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------
2      Po2(SU)       LACP        G0/1(P)   G0/2(P)
3      Po3(SU)       LACP        F0/23(P)  F0/24(P)
```

**Verify on S3:**

```cisco
S3# show etherchannel summary
```

**Expected Output:**
```
Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------
1      Po1(SU)       PAgP        F0/21(P)  F0/22(P)
3      Po3(SU)       LACP        F0/23(P)  F0/24(P)
```

---

### Step 3: Check Spanning Tree Status

**EtherChannel doesn't prevent STP - verify STP status:**

```cisco
S1# show spanning-tree active
```

**Sample Output:**
```
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0001.436E.8494
             Cost        9
             Port        27(Port-channel1)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     000A.F313.2395
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- ----------------
Po1              Root FWD 9         128.27   Shr
Po2              Altn BLK 3         128.28   Shr  ← BLOCKED!
```

**Problem:** Po2 (Gigabit ports) are blocked by STP!

---

### Step 4: Fix Spanning Tree to Use Gigabit Ports

**Make S1 the root bridge for VLAN 1:**

**Method 1 (Recommended):**
```cisco
S1(config)# spanning-tree vlan 1 root primary
```

**Method 2 (Manual):**
```cisco
S1(config)# spanning-tree vlan 1 priority 24576
```

**Wait for STP to reconverge (or use fast-forward in Packet Tracer)**

**Verify the change:**

```cisco
S1# show spanning-tree active
```

**Expected Output:**
```
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- ----------------
Po1              Desg FWD 9         128.27   Shr
Po2              Desg FWD 3         128.28   Shr  ← Now FORWARDING!
```

**Why this works:**
- S1 becomes root bridge
- Root bridge forwards on all ports
- Gigabit ports (Po2) now in forwarding state
- Better bandwidth utilization

---

## Complete Configuration Summary

### S1 Final Configuration

```cisco
! VLANs and basic config
hostname S1

! Port Channel 1 (PAgP to S3)
interface range f0/21-22
 switchport mode trunk
 channel-group 1 mode desirable
 
interface port-channel 1
 switchport mode trunk

! Port Channel 2 (LACP to S2)
interface range g0/1-2
 switchport mode trunk
 channel-group 2 mode active
 
interface port-channel 2
 switchport mode trunk

! Spanning tree optimization
spanning-tree vlan 1 root primary
```

### S2 Final Configuration

```cisco
! VLANs and basic config
hostname S2

! Port Channel 2 (LACP to S1)
interface range g0/1-2
 switchport mode trunk
 channel-group 2 mode active
 
interface port-channel 2
 switchport mode trunk

! Port Channel 3 (LACP to S3)
interface range f0/23-24
 switchport mode trunk
 channel-group 3 mode passive
 
interface port-channel 3
 switchport mode trunk
```

### S3 Final Configuration

```cisco
! VLANs and basic config
hostname S3

! Port Channel 1 (PAgP to S1)
interface range f0/21-22
 switchport mode trunk
 channel-group 1 mode desirable
 
interface port-channel 1
 switchport mode trunk

! Port Channel 3 (LACP to S2)
interface range f0/23-24
 switchport mode trunk
 channel-group 3 mode active
 
interface port-channel 3
 switchport mode trunk
```

---

## Essential Verification Commands

```cisco
! Show all EtherChannels
show etherchannel summary

! Show specific channel group
show etherchannel 1 summary

! Show port-channel details
show etherchannel port-channel

! Show protocol used
show etherchannel protocol

! Show individual port status
show etherchannel 1 port

! Show interfaces in EtherChannel
show interfaces port-channel 1

! Show trunk status
show interfaces trunk

! Show spanning tree
show spanning-tree
show spanning-tree active
```

---

## EtherChannel Configuration Guidelines

### Critical Rules

1. **Same speed and duplex** on all interfaces
2. **Same VLAN** (access) or **same trunk config** (trunk ports)
3. **Same native VLAN** on trunk ports
4. **Shutdown interfaces** before configuring channel-group
5. **Maximum 8 interfaces** per EtherChannel

### Mode Compatibility

**LACP modes:**
```
✅ active + active
✅ active + passive
❌ passive + passive (Won't form!)
```

**PAgP modes:**
```
✅ desirable + desirable
✅ desirable + auto
❌ auto + auto (Won't form!)
```

**Mode "on":**
```
⚠️  on + on (Works but no negotiation!)
❌ on + any other mode (Won't form!)
```

### Channel-Group Numbers

**Important:** Channel-group numbers are **local** to each switch!

```
Valid configuration:
S1: channel-group 1 ↔ S2: channel-group 5
(Po1 on S1 talks to Po5 on S2)

But for clarity, use same numbers!
```

---

## Troubleshooting Guide

### Problem: EtherChannel won't form

**Checklist:**
```
1. Check modes are compatible
   show etherchannel summary

2. Verify same speed/duplex
   show interfaces status

3. Check trunk configuration matches
   show interfaces trunk

4. Verify native VLAN matches
   show interfaces trunk

5. Shutdown and no shutdown interfaces
   interface range f0/21-22
   shutdown
   no shutdown
```

### Problem: Ports in err-disabled state

**Cause:** EtherChannel Misconfig Guard detected mismatch

**Solution:**
```cisco
! Check error
show interfaces status err-disabled

! Fix configuration mismatch
! Then recover:
interface range f0/21-22
 shutdown
 no shutdown
```

### Problem: Some ports not joining

**Check port configuration:**
```cisco
show run interface f0/21
! Verify:
! - switchport mode trunk
! - channel-group X mode [mode]
! - Same settings as other ports
```

---

## Lab Completion Checklist

✅ **Part 1:**
- [ ] Assigned hostnames to all switches
- [ ] Verified port status
- [ ] Configured all ports as trunks

✅ **Part 2:**
- [ ] Configured Port Channel 1 (S1-S3) with PAgP
- [ ] Used "desirable" mode on both sides
- [ ] Verified with show etherchannel summary
- [ ] Confirmed protocol is PAgP

✅ **Part 3:**
- [ ] Configured Port Channel 2 (S1-S2) with LACP
- [ ] Used "active" mode on both sides
- [ ] Verified with show etherchannel summary
- [ ] Confirmed protocol is LACP

✅ **Part 4:**
- [ ] Configured Port Channel 3 (S2-S3) with negotiated LACP
- [ ] Used "passive" on S2, "active" on S3
- [ ] Verified EtherChannel formed successfully
- [ ] Checked spanning tree status
- [ ] Configured S1 as root bridge
- [ ] Verified Po2 in forwarding state

---

## Key Takeaways

🔑 **EtherChannel = Multiple physical links = One logical link**  
🔑 **PAgP = Cisco proprietary** (desirable/auto)  
🔑 **LACP = IEEE standard** (active/passive)  
🔑 **Always shutdown interfaces** before channel-group config  
🔑 **Configure port-channel interface** as trunk  
🔑 **Compatible modes required** for formation  
🔑 **STP still runs** on EtherChannel  
🔑 **Benefits**: Bandwidth, redundancy, load balancing  
🔑 **Channel-group numbers** are local to switch