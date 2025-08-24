# Routing Fundamentals - Complete CCNA Guide

## Table of Contents
1. [What is Routing?](#what-is-routing)
2. [How Routers Work](#how-routers-work)
3. [Routing Methods](#routing-methods)
4. [Types of Routes](#types-of-routes)
5. [Routing Table Structure](#routing-table-structure)
6. [Route Selection Process](#route-selection-process)
7. [Administrative Distance](#administrative-distance)
8. [Metric and Path Selection](#metric-and-path-selection)
9. [Network Types](#network-types)
10. [Routing Process Step-by-Step](#routing-process-step-by-step)
11. [Common Routing Scenarios](#common-routing-scenarios)
12. [CCNA Exam Key Points](#ccna-exam-key-points)

---

## What is Routing?

### Definition
**ROUTING** is the process that routers use to determine the path that IP packets should take over a network to reach their destination.

### Key Components
- **Routers** - Network devices that forward packets between networks
- **Routing Tables** - Database containing all known network destinations
- **Packets** - Data units being forwarded through the network
- **Next-Hop** - The next router or destination in the path

### The Basic Process
1. **Packet Reception** - Router receives an IP packet
2. **Destination Analysis** - Examines the destination IP address
3. **Route Lookup** - Searches routing table for best match
4. **Forwarding Decision** - Determines next-hop or interface
5. **Packet Transmission** - Forwards packet toward destination

---

## How Routers Work

### Routing Table Function
- **ROUTERS store routes** to all their known destinations in a **ROUTING TABLE**
- **When ROUTERS receive PACKETS**, they look in the ROUTING TABLE to find the best route to forward that packet

### Router Decision Process
A **ROUTE** tells the **ROUTER**:
1. **To send a packet to Destination X**, you should send the packet to **next-hop Y**
2. **If the Destination is directly connected** to the router, send the packet **directly to the destination**
3. **If the Destination is the router's own IP address**, **receive the packet for yourself** (don't forward it)

### Layer 3 Operation
- Routers operate at **Layer 3 (Network Layer)** of the OSI model
- They make forwarding decisions based on **IP addresses**
- Each interface on a router belongs to a **different network/subnet**

---

## Routing Methods

There are **two main routing methods** that routers use to learn routes:

### 1. Dynamic Routing
- **ROUTERS use Dynamic Routing Protocols** (e.g., OSPF, EIGRP, RIP) to share routing information with each other **automatically**
- **Protocols build routing tables** dynamically
- **Automatic updates** when network topology changes
- **Best for large networks** with frequent changes

#### Common Dynamic Routing Protocols:
| Protocol | Type | Metric | Characteristics |
|----------|------|--------|-----------------|
| **RIP** | Distance Vector | Hop Count | Simple, max 15 hops |
| **EIGRP** | Advanced Distance Vector | Composite | Cisco proprietary, fast convergence |
| **OSPF** | Link State | Cost | Open standard, scalable |
| **BGP** | Path Vector | Path Attributes | Internet routing protocol |

### 2. Static Routing
- A **network engineer/admin manually configures routes** on the router
- **No automatic updates** - manual maintenance required
- **More secure** - no routing protocol traffic
- **Best for small networks** or specific routing requirements

#### Static Route Characteristics:
- **Predictable paths** - always use configured route
- **Lower overhead** - no protocol traffic
- **Administrative burden** - manual updates needed
- **No automatic failover** - unless configured

---

## Types of Routes

### 1. Directly Connected Routes
- **Networks directly attached** to router interfaces
- **Automatically added** when interface is up and configured
- **Administrative Distance: 0** (most trusted)
- **Example**: Interface G0/0 with IP 192.168.1.1/24

### 2. Static Routes
- **Manually configured** by network administrator
- **Administrative Distance: 1** (highly trusted)
- **Types**:
  - **Standard static route**: Specific destination network
  - **Default static route**: 0.0.0.0/0 (route of last resort)
  - **Summary static route**: Summarizes multiple networks

### 3. Dynamic Routes
- **Learned through routing protocols**
- **Administrative Distance varies** by protocol
- **Automatically updated** when topology changes
- **Examples**: OSPF routes, EIGRP routes, RIP routes

### 4. Default Route
- **Route of last resort** (0.0.0.0/0)
- **Used when no specific route** exists for destination
- **Common in edge/gateway routers**
- **Points toward ISP or core network**

---

## Routing Table Structure

### Routing Table Entry Components
Each route contains:
1. **Destination Network** - Target network address and subnet mask
2. **Next-Hop Address** - IP address of next router in path
3. **Outgoing Interface** - Local interface to use for forwarding
4. **Administrative Distance** - Trustworthiness of route source
5. **Metric** - Cost/distance to reach destination
6. **Route Source** - How route was learned (C, S, O, D, etc.)

### Route Source Codes
```
C - Connected (directly connected network)
S - Static (manually configured route)
O - OSPF (Open Shortest Path First)
D - EIGRP (Enhanced Interior Gateway Routing Protocol)
R - RIP (Routing Information Protocol)
B - BGP (Border Gateway Protocol)
* - Default route candidate
```

### Example Routing Table Entry
```
O    192.168.10.0/24 [110/65] via 10.0.0.2, 00:05:30, Serial0/0/0
│    │                 │  │    │           │          │
│    │                 │  │    │           │          └─ Outgoing Interface
│    │                 │  │    │           └─ Age of route
│    │                 │  │    └─ Next-hop IP address
│    │                 │  └─ Metric (cost)
│    │                 └─ Administrative Distance
│    └─ Destination network
└─ Route source (OSPF)
```

---

## Route Selection Process

### Longest Prefix Match
When multiple routes match a destination:
1. **Choose the route with the longest subnet mask** (most specific)
2. **More specific routes take precedence** over less specific ones
3. **Example**: 192.168.1.100 matches both 192.168.0.0/16 and 192.168.1.0/24
4. **Result**: Router chooses 192.168.1.0/24 (longer prefix /24 vs /16)

### Route Selection Hierarchy
1. **Longest Prefix Match** (most important)
2. **Administrative Distance** (if prefixes equal)
3. **Metric** (if AD equal)
4. **Load Balancing** (if metrics equal)

---

## Administrative Distance

### Definition
**Administrative Distance (AD)** is a rating of trustworthiness for routing information sources. **Lower values are more trusted**.

### Common Administrative Distances
| Route Source | Administrative Distance | Trustworthiness |
|--------------|------------------------|-----------------|
| **Connected** | 0 | Most trusted |
| **Static** | 1 | Highly trusted |
| **EIGRP** | 90 | Very trusted |
| **OSPF** | 110 | Trusted |
| **RIP** | 120 | Less trusted |
| **External EIGRP** | 170 | Least trusted |
| **Unknown** | 255 | Never used |

### AD in Action
If router has multiple routes to same destination:
- **192.168.1.0/24 via EIGRP** (AD = 90)
- **192.168.1.0/24 via OSPF** (AD = 110)
- **192.168.1.0/24 via RIP** (AD = 120)

**Result**: Router chooses **EIGRP route** (lowest AD = 90)

---

## Metric and Path Selection

### What is Metric?
**Metric** is the cost value used by routing protocols to determine the best path when **Administrative Distance is equal**.

### Protocol-Specific Metrics

#### RIP Metric
- **Hop Count** - Number of routers to destination
- **Maximum**: 15 hops (16 = unreachable)
- **Simple but inefficient** - doesn't consider bandwidth

#### EIGRP Metric
- **Composite Metric** based on:
  - **Bandwidth** (primary factor)
  - **Delay** (primary factor)
  - **Reliability** (optional)
  - **Load** (optional)
  - **MTU** (not used in calculation)

#### OSPF Metric
- **Cost** based on bandwidth
- **Formula**: Cost = Reference Bandwidth / Interface Bandwidth
- **Default Reference**: 100 Mbps
- **Lower cost = better path**

### Load Balancing
When **multiple paths have equal cost**:
- **Equal-Cost Load Balancing** - Traffic shared across paths
- **Unequal-Cost Load Balancing** - Some protocols support (EIGRP)
- **Maximum paths configurable** (typically 4-16)

---

## Network Types

### LAN (Local Area Network)
- **Small geographic area** (building, campus)
- **High-speed connections** (Ethernet, WiFi)
- **Low latency and high reliability**
- **Examples**: Office networks, home networks

### WAN (Wide Area Network)  
**WAN (Wide Area Network) = network that extends over a large geographic area**

#### WAN Characteristics:
- **Connects LANs across cities/countries**
- **Lower speeds than LAN** (historically)
- **Higher latency and cost**
- **Examples**: Internet, MPLS networks, leased lines

#### WAN Technologies:
- **Leased Lines** (T1, T3, E1, E3)
- **Frame Relay** (legacy)
- **MPLS** (Multi-Protocol Label Switching)
- **Internet VPN** (Site-to-Site, Remote Access)
- **Satellite** and **Cellular** connections

---

## Routing Process Step-by-Step

### Complete Packet Forwarding Process

#### Step 1: Packet Reception
- Router receives frame on interface
- **Checks destination MAC address** (should be router's MAC)
- **Extracts IP packet** from frame
- **Decrements TTL** by 1

#### Step 2: Routing Table Lookup
- **Examines destination IP** in packet header
- **Searches routing table** for matching entries
- **Applies longest prefix match** rule
- **Selects best route** based on AD and metric

#### Step 3: Next-Hop Determination
Router determines:
- **Directly connected**: Send directly to destination
- **Remote network**: Forward to next-hop router
- **Local router**: Process packet locally (don't forward)

#### Step 4: ARP Resolution (if needed)
- **Check ARP table** for next-hop MAC address
- **Send ARP request** if MAC unknown
- **Wait for ARP reply** with MAC address

#### Step 5: Frame Encapsulation
- **Encapsulate IP packet** in new frame
- **Set source MAC** to outgoing interface MAC
- **Set destination MAC** to next-hop MAC
- **Forward frame** out appropriate interface

---

## Common Routing Scenarios

### Scenario 1: Simple Static Routing
```
Network Topology:
PC1 (192.168.1.10) --- R1 --- R2 --- PC2 (192.168.2.10)
                    G0/0   S0/0 S0/1   G0/0
```

**Required Static Routes:**
```bash
# On R1
ip route 192.168.2.0 255.255.255.0 10.0.0.2

# On R2  
ip route 192.168.1.0 255.255.255.0 10.0.0.1
```

### Scenario 2: Default Route Configuration
```bash
# Configure default route pointing to ISP
ip route 0.0.0.0 0.0.0.0 203.0.113.1

# Or using exit interface
ip route 0.0.0.0 0.0.0.0 Serial0/0/0
```

### Scenario 3: Multiple Path Selection
```
Two paths to 192.168.10.0/24:
- Path 1: EIGRP route (AD=90, Metric=2816)
- Path 2: OSPF route (AD=110, Metric=65)

Result: EIGRP selected (lower AD)
```

---

## CCNA Exam Key Points

### Must-Know Concepts
1. **Routing vs. Switching**
   - Routing operates at Layer 3 (IP addresses)
   - Switching operates at Layer 2 (MAC addresses)

2. **Routing Table Lookup Process**
   - Longest prefix match is most important
   - Administrative Distance breaks ties
   - Metric determines best path within protocol

3. **Static vs. Dynamic Routing**
   - Static: Manual configuration, no protocol overhead
   - Dynamic: Automatic updates, protocol overhead

4. **Route Types and Sources**
   - Connected (AD=0), Static (AD=1), Dynamic (varies)
   - Understand route source codes (C, S, O, D, R)

5. **Default Routes**
   - 0.0.0.0/0 network
   - Route of last resort
   - Common in edge routers

### Common Exam Questions
- **Routing table interpretation** - Read and understand entries
- **Route selection scenarios** - Which route is chosen and why
- **Static route configuration** - Syntax and implementation
- **Troubleshooting routing issues** - Missing or incorrect routes
- **Administrative Distance comparison** - Route preference

### Commands to Remember
```bash
# View routing table
show ip route

# Configure static route  
ip route [destination] [mask] [next-hop]

# Configure default route
ip route 0.0.0.0 0.0.0.0 [next-hop]

# Show specific route details
show ip route [network]
```

### Memory Aids
**"Longest Prefix Wins"** - Most specific route is always chosen

**"Lower AD = More Trusted"** - Connected(0) > Static(1) > EIGRP(90) > OSPF(110) > RIP(120)

**"Routing Rule of 3"**: 
1. **Receive** the packet
2. **Look up** in routing table  
3. **Forward** to next hop

---

## Summary

Routing is fundamental to network operation and heavily tested on CCNA exams. Key takeaways:

- **Routers forward packets** based on destination IP addresses
- **Routing tables contain** all known network destinations  
- **Two main methods**: Static (manual) and Dynamic (automatic)
- **Route selection hierarchy**: Longest prefix → AD → Metric
- **Understanding packet flow** is crucial for troubleshooting

Master these concepts through hands-on practice and routing table analysis!