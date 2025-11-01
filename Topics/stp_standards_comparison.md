# STP Standards Comparison - CCNA Quick Guide

## STP Standards Overview

| Standard | Name | Vendor | Instances | Convergence | VLAN Support |
|----------|------|--------|-----------|-------------|--------------|
| **802.1D** | STP | IEEE | 1 | 30-50 sec | No (CST) |
| **PVST+** | Per-VLAN STP+ | Cisco | 1 per VLAN | 30-50 sec | Yes |
| **802.1w** | RSTP | IEEE | 1 | 1-6 sec | No (CST) |
| **Rapid PVST+** | Rapid Per-VLAN STP+ | Cisco | 1 per VLAN | 1-6 sec | Yes |
| **802.1s** | MSTP | IEEE | Multiple | 1-6 sec | Yes (grouped) |

---

## 1. STP (802.1D) - Original

**Key Features**:
- IEEE standard (works on all vendors)
- ONE instance for ALL VLANs (CST - Common Spanning Tree)
- Slow convergence: 30-50 seconds
- Uses Blocking, Listening, Learning, Forwarding states

**Port States**: Blocking → Listening (15s) → Learning (15s) → Forwarding  
**Convergence**: 30-50 seconds

**Problem**: No per-VLAN optimization, slow failover

---

## 2. PVST+ (Per-VLAN Spanning Tree Plus)

**Key Features**:
- **Cisco proprietary**
- One STP instance **per VLAN**
- Slow convergence: 30-50 seconds
- Load balancing possible (different root per VLAN)

**Advantage**: Can optimize paths for different VLANs  
**Disadvantage**: Still slow convergence

**Example**:
```
VLAN 10: Root = Switch A
VLAN 20: Root = Switch B
(Load balancing across switches)
```

---

## 3. RSTP (802.1w) - Rapid STP

**Key Features**:
- IEEE standard
- ONE instance for all VLANs
- **Fast convergence: 1-6 seconds**
- New port roles and states
- Backward compatible with 802.1D

**Port States**: Discarding → Learning → Forwarding (faster)

**New Port Roles**:
- Root Port
- Designated Port
- **Alternate Port** (backup to root)
- **Backup Port** (backup designated)

**Key Improvement**: Sub-second failover

---

## 4. Rapid PVST+ (Rapid Per-VLAN STP+)

**Key Features**:
- **Cisco proprietary**
- **One RSTP instance per VLAN**
- **Fast convergence: 1-6 seconds**
- Combines benefits of PVST+ and RSTP
- **Most commonly used on Cisco switches**

**Best of both worlds**:
- ✅ Fast convergence (RSTP)
- ✅ Per-VLAN optimization (PVST+)

**Default on most Cisco switches**

---

## 5. MSTP (802.1s) - Multiple Spanning Tree

**Key Features**:
- IEEE standard
- Groups multiple VLANs into **instances**
- Fast convergence: 1-6 seconds
- Reduces CPU load vs PVST+

**How it works**:
```
Instance 1: VLANs 10, 20, 30
Instance 2: VLANs 40, 50, 60
(Instead of 6 separate instances)
```

**Advantage**: Fewer STP instances = less overhead  
**Disadvantage**: More complex configuration

---

## Quick Comparison

### Speed
- **Slow** (30-50s): 802.1D, PVST+
- **Fast** (1-6s): RSTP, Rapid PVST+, MSTP

### VLAN Awareness
- **No**: 802.1D, RSTP (one instance for all)
- **Yes**: PVST+, Rapid PVST+, MSTP

### Vendor
- **IEEE (standard)**: 802.1D, RSTP, MSTP
- **Cisco**: PVST+, Rapid PVST+

---

## Configuration Commands

### Check Current STP Mode
```cisco
Switch# show spanning-tree summary
```

### Change STP Mode
```cisco
! 802.1D (original STP)
Switch(config)# spanning-tree mode stp

! PVST+
Switch(config)# spanning-tree mode pvst

! Rapid PVST+ (recommended)
Switch(config)# spanning-tree mode rapid-pvst

! MSTP
Switch(config)# spanning-tree mode mst
```

---

## Which to Use?

### For CCNA/Real World:
**Rapid PVST+** (most common choice)
- Fast convergence
- Per-VLAN optimization
- Works on Cisco switches
- Default on modern Cisco switches

### When to Use Others:
- **802.1D**: Legacy networks only
- **PVST+**: Legacy Cisco (upgrade to Rapid PVST+)
- **RSTP**: Multi-vendor standard environment
- **MSTP**: Very large networks (1000+ VLANs)

---

## CCNA Exam Key Points

**Know**:
- 802.1D = slow (30-50s), one instance
- PVST+ = slow (30-50s), per-VLAN, Cisco
- RSTP = fast (1-6s), one instance, IEEE
- Rapid PVST+ = fast (1-6s), per-VLAN, Cisco, **most common**
- MSTP = fast (1-6s), grouped VLANs, IEEE

**Common Questions**:
- "Which is fastest?" → RSTP/Rapid PVST+/MSTP (all 1-6s)
- "Which supports per-VLAN?" → PVST+, Rapid PVST+, MSTP
- "Which is Cisco proprietary?" → PVST+, Rapid PVST+
- "Default on Cisco switches?" → Usually Rapid PVST+
- "Which for load balancing?" → PVST+ or Rapid PVST+

---

## Summary Chart

```
Slow (30-50s)                Fast (1-6s)
     |                            |
  802.1D ────────────→        RSTP (802.1w)
     |                            |
  PVST+ ─────────────→    Rapid PVST+ ⭐
                                  |
                               MSTP (802.1s)
```

**⭐ Rapid PVST+ = Most commonly used**

---

*For CCNA: Focus on understanding Rapid PVST+ - it's the most common in production!*