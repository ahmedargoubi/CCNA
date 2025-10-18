# STP Evolution - CCNA Guide

## Table of Contents
1. [Different Versions of STP](#1-different-versions-of-stp)
2. [RSTP Concepts](#2-rstp-concepts)
3. [RSTP Port States and Port Roles](#3-rstp-port-states-and-port-roles)
4. [PortFast and BPDU Guard](#4-portfast-and-bpdu-guard)

---

## 1. Different Versions of STP

### STP Evolution Timeline

```
1985 → 1998 → 2001 → 2004
 STP    RSTP   MSTP   PVST+/Rapid PVST+
```

### Four Main STP Versions

#### 1. STP (Spanning Tree Protocol) - 802.1D

**Original standard (1985)**

```
Characteristics:
- One spanning tree for entire network
- Slow convergence (30-50 seconds)
- Three port states: Blocking, Listening, Learning, Forwarding
- Uses BPDU every 2 seconds
```

**Convergence Time:**
```
Port failure detected → 20 seconds (max age)
Listening state → 15 seconds
Learning state → 15 seconds
Total: ~50 seconds! (Too slow!)
```

#### 2. PVST+ (Per-VLAN Spanning Tree Plus)

**Cisco proprietary enhancement**

```
Characteristics:
- Separate spanning tree per VLAN
- Better load balancing
- Still slow convergence (30-50 seconds)
- More CPU/memory usage (one instance per VLAN)
```

**Example:**
```
VLAN 10: Root bridge on Switch1
VLAN 20: Root bridge on Switch2

Load balancing between switches!
```

#### 3. RSTP (Rapid Spanning Tree Protocol) - 802.1w

**IEEE standard (2001) - Fast convergence**

```
Characteristics:
- One spanning tree for entire network
- FAST convergence (typically < 5 seconds)
- New port roles and states
- Backward compatible with 802.1D STP
```

**Convergence Time:**
```
Link failure: Immediate (alternate port ready)
New port: 3-6 seconds
Much faster than STP!
```

#### 4. Rapid PVST+

**Cisco proprietary (combines RSTP + PVST+)**

```
Characteristics:
- Rapid convergence per VLAN
- Separate instance per VLAN
- Default on Cisco switches
- Best of both worlds: Speed + Load balancing
```

### Comparison Table

| Feature | STP (802.1D) | PVST+ | RSTP (802.1w) | Rapid PVST+ |
|---------|--------------|-------|---------------|-------------|
| **Standard** | IEEE | Cisco | IEEE | Cisco |
| **Instances** | 1 | 1 per VLAN | 1 | 1 per VLAN |
| **Convergence** | 30-50 sec | 30-50 sec | < 5 sec | < 5 sec |
| **Port States** | 5 | 5 | 3 | 3 |
| **Load Balancing** | No | Yes | No | Yes |
| **Cisco Default** | No | No | No | **Yes** ✅ |

### Which to Use?

**For CCNA:**
- **Rapid PVST+** is the default on Cisco switches
- Focus on understanding **RSTP** concepts
- Know the differences between all versions

---

## 2. RSTP Concepts

### Key Improvements Over STP

**RSTP = Faster convergence + Simplified operation**

### 1. Faster Convergence

**How RSTP achieves speed:**

```
STP: Waits for timers (50 seconds)
     ↓
RSTP: Active proposals and agreements (< 5 seconds)
      Direct negotiation between switches!
```

**RSTP Convergence:**
```
Link failure:
- Alternate port → Forwarding (immediate!)
- No waiting for timers

New link:
- Proposal/Agreement handshake
- Port to forwarding in seconds
```

### 2. Port Roles (Enhanced)

**RSTP has MORE port roles than STP:**

| Port Role | Description | Forwards Data? |
|-----------|-------------|----------------|
| **Root Port** | Best path to root bridge | Yes ✅ |
| **Designated Port** | Forwarding port on segment | Yes ✅ |
| **Alternate Port** | Backup path to root | No ❌ (Ready!) |
| **Backup Port** | Backup for same segment | No ❌ |

**Visual:**
```
Root Bridge
    ↓
[Switch A]
    ↓ ← Root Port (best path)
[Switch B]
    ↓ ← Designated Port (forwarding)
    ↓ 
[Switch C] ← Has alternate port (backup ready!)
```

**Alternate Port (KEY CONCEPT):**
```
Normal path: Switch C → Switch B → Root
Alternate path: Switch C → Switch A → Root (standby)

If Switch B fails:
Alternate port → Forwarding immediately!
No 30-second delay!
```

### 3. Port States (Simplified)

**STP had 5 states, RSTP has only 3:**

| RSTP State | STP Equivalent | Forwards Data? | Learns MAC? |
|------------|----------------|----------------|-------------|
| **Discarding** | Blocking, Listening, Disabled | No | No |
| **Learning** | Learning | No | Yes |
| **Forwarding** | Forwarding | Yes | Yes |

**Simplified transition:**
```
STP: Blocking → Listening → Learning → Forwarding (50s)
RSTP: Discarding → Learning → Forwarding (< 5s)
```

### 4. Proposal and Agreement

**How RSTP negotiates quickly:**

**Step 1: Proposal**
```
Switch A (root) → Switch B: "I'm the root, accept me as designated?"
```

**Step 2: Agreement**
```
Switch B: Blocks all non-edge ports
Switch B → Switch A: "Agreed! You're designated."
Switch A: Port → Forwarding
```

**Step 3: Propagation**
```
Switch B: Now sends proposals to downstream switches
Process repeats quickly
```

**Result: Rapid convergence without timers!**

### 5. Edge Ports

**Ports connected to end devices (PCs, servers)**

```
Edge Port:
- Directly to end device
- No risk of loops
- Immediately → Forwarding
- No proposal/agreement needed

Like PortFast in STP (covered later)
```

### 6. Link Types

**RSTP recognizes link types:**

| Link Type | Description | Example |
|-----------|-------------|---------|
| **Point-to-Point** | Full-duplex between switches | Switch ←→ Switch |
| **Shared** | Half-duplex (hub) | Switch ←→ Hub |
| **Edge** | Connected to end device | Switch ←→ PC |

**Point-to-Point links use proposal/agreement**
**Shared links use old STP timers**

---

## 3. RSTP Port States and Port Roles

### Port States (Detailed)

#### Discarding State

**Replaces three STP states:**
- Blocking
- Listening  
- Disabled

**Characteristics:**
```
- Does NOT forward data frames
- Does NOT learn MAC addresses
- Receives BPDUs only
- Can be operational or non-operational
```

**When used:**
```
- Alternate ports (backup paths)
- Backup ports
- Disabled ports
- Ports waiting to transition
```

#### Learning State

**Same as STP Learning:**

```
- Does NOT forward data frames
- DOES learn MAC addresses (builds MAC table)
- Prepares for forwarding
- Brief transitional state
```

**Duration:** Very short (typically few seconds)

#### Forwarding State

**Active data transfer:**

```
- DOES forward data frames ✅
- DOES learn MAC addresses ✅
- Fully operational
- Participates in network
```

### Port Roles (Detailed)

#### 1. Root Port

**Best path TO root bridge**

```
Each non-root switch has ONE root port
- Lowest path cost to root bridge
- Always in Forwarding state
- Receives BPDUs from root direction
```

**Selection criteria:**
```
1. Lowest path cost to root
2. Lowest sender bridge ID
3. Lowest sender port ID
```

#### 2. Designated Port

**Best path FROM segment to root**

```
One per network segment
- Forwarding state
- Sends BPDUs onto segment
- Lowest cost path from this segment to root
```

**Every segment has ONE designated port**

#### 3. Alternate Port (NEW in RSTP)

**Backup path to root bridge**

```
- Alternative path if root port fails
- Discarding state (ready to activate)
- Receives superior BPDUs from another switch
- Can immediately transition to root port
```

**Key advantage:**
```
Root port fails → Alternate port immediately becomes root port
NO 30-second delay!
```

**Example:**
```
        Root Bridge
        ↓         ↓
    Switch A   Switch B
        ↓         ↓
      [Switch C]
       RP ↑    ↑ AP (Alternate - Ready!)
       
Root port fails → Alternate immediately active!
```

#### 4. Backup Port (NEW in RSTP)

**Backup designated port on same segment**

```
- Rare scenario
- Occurs when switch has multiple connections to same segment
- Discarding state
- Backup if designated port fails
```

**Example:**
```
Switch
 ↓   ↓
  Hub  (same segment)
  
One port: Designated
Other port: Backup (discarding)
```

### Port State and Role Summary

**Quick reference:**

```
Root Port:          Forwarding
Designated Port:    Forwarding
Alternate Port:     Discarding (ready to become root)
Backup Port:        Discarding (ready to become designated)
```

---

## 4. PortFast and BPDU Guard

### PortFast

**Purpose:** Immediately bring access ports to forwarding state

#### What is PortFast?

```
Normal STP:
Port up → Listening (15s) → Learning (15s) → Forwarding
Total: 30-50 seconds

With PortFast:
Port up → Forwarding (immediately!)
```

**Use case:**
```
PC plugs into switch:
- No PortFast: Wait 30-50 seconds for network
- With PortFast: Instant network access!
```

#### When to Use PortFast

**✅ Use on:**
- Ports connected to PCs
- Ports connected to servers
- Ports connected to printers
- Any port to END DEVICE

**❌ NEVER use on:**
- Ports connected to switches
- Ports connected to routers
- Trunk ports
- Any port to NETWORK DEVICE

**Why?** 
```
PortFast bypasses loop protection!
Connecting a switch to PortFast port = LOOP!
```

#### PortFast Configuration

**Per-interface:**
```cisco
Switch(config)# interface FastEthernet0/1
Switch(config-if)# spanning-tree portfast
%Warning: portfast should only be enabled on ports connected to a single host.
Switch(config-if)# exit
```

**Global (all access ports):**
```cisco
Switch(config)# spanning-tree portfast default
```

**Verify:**
```cisco
Switch# show spanning-tree interface fa0/1 portfast
```

---

### BPDU Guard

**Purpose:** Protect against unauthorized switches

#### What is BPDU Guard?

```
BPDU received on PortFast port:
- Should NEVER happen! (only end devices)
- Indicates switch or misconfiguration
- BPDU Guard → Shuts down port (err-disabled)
```

**Protection:**
```
User plugs in rogue switch:
- Switch sends BPDU
- BPDU Guard detects it
- Port immediately disabled
- Network protected!
```

#### How BPDU Guard Works

**Normal scenario (good):**
```
PC → Switch Port (PortFast + BPDU Guard)
No BPDUs received → Port stays up ✅
```

**Security violation:**
```
Rogue Switch → Switch Port (PortFast + BPDU Guard)
BPDU received → Port err-disabled ❌
Administrator must manually recover
```

#### BPDU Guard Configuration

**Per-interface:**
```cisco
Switch(config)# interface FastEthernet0/1
Switch(config-if)# spanning-tree portfast
Switch(config-if)# spanning-tree bpduguard enable
Switch(config-if)# exit
```

**Global (all PortFast ports):**
```cisco
Switch(config)# spanning-tree portfast bpduguard default
! Applies to all PortFast-enabled ports
```

**Best practice: Use global command**

#### Recovering from err-disabled

**Port shutdown by BPDU Guard:**

```cisco
Switch# show interfaces status err-disabled

Port      Name               Status       Reason
Fa0/1     PC1                err-disabled bpduguard

! Manually recover:
Switch(config)# interface FastEthernet0/1
Switch(config-if)# shutdown
Switch(config-if)# no shutdown
```

**Or configure auto-recovery:**
```cisco
Switch(config)# errdisable recovery cause bpduguard
Switch(config)# errdisable recovery interval 300
! Auto-recovery after 300 seconds
```

---

### PortFast vs BPDU Guard

| Feature | PortFast | BPDU Guard |
|---------|----------|------------|
| **Purpose** | Fast convergence for end devices | Security protection |
| **Action** | Bypasses listening/learning | Disables port if BPDU received |
| **Use on** | Access ports to end devices | Same as PortFast |
| **Typical config** | Together on access ports | Together with PortFast |

### Best Practice Configuration

**Standard access port configuration:**

```cisco
! Per interface
interface FastEthernet0/1
 description PC Connection
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 spanning-tree bpduguard enable
 
! Or globally for all access ports
spanning-tree portfast default
spanning-tree portfast bpduguard default
```

---

## Quick Reference

### STP Versions

```
STP (802.1D):      Slow, 1 instance, original
PVST+:             Slow, per-VLAN, Cisco
RSTP (802.1w):     Fast, 1 instance, IEEE
Rapid PVST+:       Fast, per-VLAN, Cisco (default) ✅
```

### RSTP Port States

```
Discarding:  Not forwarding, not learning
Learning:    Not forwarding, learning MACs
Forwarding:  Forwarding + learning
```

### RSTP Port Roles

```
Root Port:       Best path to root (forwarding)
Designated Port: Best path from segment (forwarding)
Alternate Port:  Backup to root (discarding, ready!)
Backup Port:     Backup designated (discarding)
```

### Key Commands

```cisco
! View spanning tree
show spanning-tree
show spanning-tree vlan 10
show spanning-tree summary

! Configure PortFast
spanning-tree portfast default
interface fa0/1
 spanning-tree portfast

! Configure BPDU Guard
spanning-tree portfast bpduguard default
interface fa0/1
 spanning-tree bpduguard enable

! View port status
show spanning-tree interface fa0/1
show interfaces status
show spanning-tree interface fa0/1 portfast
```

---

## Remember This!

✅ **Rapid PVST+ is Cisco default** (fast + per-VLAN)  
✅ **RSTP converges in < 5 seconds** (vs 30-50s for STP)  
✅ **RSTP has 3 port states** (Discarding, Learning, Forwarding)  
✅ **Alternate port = Backup root port** (ready immediately)  
✅ **PortFast = Immediate forwarding** for end devices  
✅ **BPDU Guard = Security** (disables port if BPDU received)  
✅ **PortFast + BPDU Guard = Best practice** for access ports  
✅ **NEVER use PortFast on trunk/switch ports** (creates loops!)  
✅ **Err-disabled ports need manual recovery** (or auto-recovery config)