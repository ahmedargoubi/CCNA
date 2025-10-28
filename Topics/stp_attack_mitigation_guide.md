# STP Attack Mitigation - CCNA Guide

## Overview

**STP Attacks:** Unauthorized devices can manipulate STP to become root bridge, causing network disruption or allowing traffic interception.

**Defense Mechanisms:**
1. **PortFast** - Skip STP states on access ports
2. **BPDU Guard** - Disable ports receiving BPDUs
3. **Root Guard** - Prevent unauthorized root bridges
4. **BPDU Filter** - Block BPDUs on specific ports

---

## 1. PortFast

### What is PortFast?

**Purpose:** Immediately transition access ports to forwarding state (skip Listening/Learning).

**Use on:**
- Ports connected to end devices (PCs, servers, printers)
- Access ports only

**DON'T use on:**
- Trunk ports
- Ports to other switches
- Ports to routers

### Configuration

```cisco
! Per interface
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# spanning-tree portfast

! Global (all access ports)
Switch(config)# spanning-tree portfast default
```

### Verification

```cisco
Switch# show spanning-tree interface fa0/1 portfast
```

---

## 2. BPDU Guard

### What is BPDU Guard?

**Purpose:** Automatically disable ports that receive BPDUs (indicates unauthorized switch).

**How it works:**
- Port receives BPDU → Port goes to **err-disabled** state
- Prevents rogue switches from affecting STP

### Configuration

```cisco
! Per interface
Switch(config)# interface FastEthernet0/1
Switch(config-if)# spanning-tree portfast
Switch(config-if)# spanning-tree bpduguard enable

! Global (all PortFast ports)
Switch(config)# spanning-tree portfast bpduguard default
```

**Best practice:** Use global command for all PortFast-enabled ports.

### Recovery from err-disabled

**Manual recovery:**
```cisco
Switch(config)# interface fa0/1
Switch(config-if)# shutdown
Switch(config-if)# no shutdown
```

**Automatic recovery:**
```cisco
Switch(config)# errdisable recovery cause bpduguard
Switch(config)# errdisable recovery interval 300
```

### Verification

```cisco
Switch# show spanning-tree summary
Switch# show interfaces status err-disabled
```

---

## 3. Root Guard

### What is Root Guard?

**Purpose:** Prevent a port from becoming a root port (prevents unauthorized root bridge).

**Use on:**
- Ports where root bridge should NEVER be
- Typically on distribution layer ports facing access layer

**How it works:**
- Port receives superior BPDU (lower bridge ID) → Port goes to **root-inconsistent** state
- Port blocked until superior BPDUs stop

### Configuration

```cisco
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# spanning-tree guard root
```

**Common scenario:**
```
Distribution Switch (should be root)
    ↓
Access Switch (configure Root Guard on this uplink port)
    ↓
End devices
```

### Verification

```cisco
Switch# show spanning-tree inconsistentports
```

---

## 4. BPDU Filter

### What is BPDU Filter?

**Purpose:** Prevent sending/receiving BPDUs on a port.

**Difference from BPDU Guard:**
- **BPDU Guard:** Disables port if BPDU received
- **BPDU Filter:** Stops BPDUs from being sent/received (port stays up)

### Configuration

```cisco
! Per interface (dangerous - disables STP on port)
Switch(config)# interface FastEthernet0/1
Switch(config-if)# spanning-tree bpdufilter enable

! Global (only on PortFast ports)
Switch(config)# spanning-tree portfast bpdufilter default
```

**⚠️ Warning:** Global is safer - if BPDU received, PortFast is disabled and port acts normally.

### When to use

- Very specific cases only
- Usually NOT recommended
- Can create loops if misused

---

## Comparison Table

| Feature | Purpose | Use On | What Happens |
|---------|---------|--------|--------------|
| **PortFast** | Skip STP states | Access ports to end devices | Immediate forwarding |
| **BPDU Guard** | Block rogue switches | PortFast-enabled ports | Port err-disabled if BPDU received |
| **Root Guard** | Prevent unauthorized root | Ports where root shouldn't be | Port root-inconsistent if superior BPDU |
| **BPDU Filter** | Block all BPDUs | Rarely (specific cases) | No BPDUs sent/received |

---

## Best Practice Configuration

### Standard Access Port (PC connected)

```cisco
interface FastEthernet0/1
 description PC Connection
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 spanning-tree bpduguard enable
```

### Global Best Practice

```cisco
! Enable PortFast on all access ports
spanning-tree portfast default

! Enable BPDU Guard on all PortFast ports
spanning-tree portfast bpduguard default
```

### Distribution to Access Link

```cisco
interface GigabitEthernet0/1
 description Uplink from Access Switch
 switchport mode trunk
 spanning-tree guard root
```

---

## Attack Scenarios & Defense

### Attack 1: Rogue Switch with Lower Bridge ID

**Attack:** User connects switch with priority 0 → becomes root bridge.

**Defense:**
```cisco
! On access ports
spanning-tree portfast
spanning-tree bpduguard enable
```
**Result:** Port disabled immediately when rogue switch sends BPDU.

---

### Attack 2: Unauthorized Root Bridge on Access Layer

**Attack:** Access switch misconfigured as root.

**Defense:**
```cisco
! On distribution switch ports facing access layer
interface gi0/1
 spanning-tree guard root
```
**Result:** Port blocked if access switch tries to become root.

---

## Quick Reference Commands

```cisco
! Configure PortFast
spanning-tree portfast
spanning-tree portfast default

! Configure BPDU Guard
spanning-tree bpduguard enable
spanning-tree portfast bpduguard default

! Configure Root Guard
spanning-tree guard root

! Configure BPDU Filter
spanning-tree bpdufilter enable
spanning-tree portfast bpdufilter default

! Verification
show spanning-tree summary
show spanning-tree interface fa0/1 portfast
show interfaces status err-disabled
show spanning-tree inconsistentports

! Recovery
errdisable recovery cause bpduguard
errdisable recovery interval 300
```

---

## Key Exam Points

✅ **PortFast:** Access ports only, skips Listening/Learning  
✅ **BPDU Guard:** Disables port if BPDU received (err-disabled)  
✅ **Root Guard:** Prevents port from becoming root port  
✅ **BPDU Filter:** Stops BPDUs (rarely used, dangerous)  
✅ **Best practice:** PortFast + BPDU Guard together on access ports  
✅ **Root Guard:** Use on distribution → access links  
✅ **err-disabled recovery:** Can be manual or automatic  
✅ **Global commands:** Safer and easier to manage  

---

## Remember This!

🔑 **PortFast = Fast access** (end devices only)  
🔑 **BPDU Guard = Protection** (shuts down port)  
🔑 **Root Guard = Root protection** (prevents unauthorized root)  
🔑 **Always combine** PortFast + BPDU Guard on access ports  
🔑 **Never use PortFast** on trunk or inter-switch links  
🔑 **err-disabled = Security feature** (manual or auto recovery)  
🔑 **Root Guard = Distribution layer** (facing access)  
🔑 **BPDU Filter = Rarely used** (can create loops)