# RIP and EIGRP - CCNA Quick Guide

## RIP (Routing Information Protocol)

### Key Facts
- Distance vector IGP
- **Metric**: Hop count (max 15 hops)
- **AD**: 120
- Updates every 30 seconds
- **Multicast**: 224.0.0.9 (RIPv2)

### Versions
- **RIPv1**: Classful, no VLSM, broadcasts
- **RIPv2**: Supports VLSM/CIDR, multicasts

### Configuration
```cisco
router rip
version 2
network 10.0.0.0
no auto-summary
passive-interface g0/1
```

### Default Route
```cisco
ip route 0.0.0.0 0.0.0.0 [next-hop]
default-information originate
```

---

## EIGRP (Enhanced Interior Gateway Routing Protocol)

### Key Facts
- Advanced distance vector IGP
- **Metric**: Bandwidth + Delay
- **AD**: 90 (internal), 170 (external)
- **Multicast**: 224.0.0.10
- Only IGP with unequal-cost load balancing
- Fast convergence

### Configuration
```cisco
router eigrp [AS-number]
network 10.0.0.0 0.255.255.255
no auto-summary
```

### Wildcard Masks
- Inverted subnet masks
- 0 = must match, 1 = don't care
- /24 = 0.0.0.255, /30 = 0.0.0.3

### Key Terms
- **Feasible Distance (FD)**: This router's metric to destination
- **Reported Distance (RD)**: Neighbor's metric to destination
- **Successor**: Best route (lowest FD)
- **Feasible Successor**: Backup route meeting feasibility condition

### Feasibility Condition
**RD < Successor's FD** (prevents loops)

### Unequal-Cost Load Balancing
```cisco
variance 2  # Use routes up to 2x successor's FD
```

---

## Quick Comparison

| | RIP | EIGRP |
|---|---|---|
| **Metric** | Hop count | Bandwidth + Delay |
| **Max Hops** | 15 | Unlimited |
| **AD** | 120 | 90/170 |
| **Multicast** | 224.0.0.9 | 224.0.0.10 |
| **Load Balancing** | Equal-cost only | Equal + Unequal-cost |

---

## Must Memorize
- **RIP AD**: 120
- **EIGRP AD**: 90 (internal), 170 (external)
- **RIP multicast**: 224.0.0.9
- **EIGRP multicast**: 224.0.0.10
- **RIP max hops**: 15
- **Feasibility condition**: RD < FD