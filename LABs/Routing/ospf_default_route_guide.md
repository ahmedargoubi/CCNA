# Propagate Default Route in OSPFv2 Guide

## Lab Overview

This lab demonstrates how to configure and propagate a default route in OSPF. You'll learn why internal networks need a default route to reach the Internet and how OSPF can distribute this route to all routers in the domain.


**Key Point:** Only R2 has direct connectivity to the Internet!

---

## Understanding Default Routes

### What is a Default Route?

A **default route** (0.0.0.0/0) is a "catch-all" route that matches any destination network not explicitly in the routing table.

**Think of it as:** "If you don't know where to send this packet, send it here!"

### Why Do We Need Default Routes?

**Problem:**
- Internet has millions of routes
- Internal routers can't store all Internet routes
- Routers need a way to reach unknown destinations

**Solution:**
- Configure a default route pointing to the Internet gateway
- All unknown traffic (like web servers) gets forwarded to the Internet

### Default Route Notation

```
0.0.0.0/0  or  0.0.0.0 0.0.0.0
```

- **Network:** 0.0.0.0
- **Mask:** 0.0.0.0 (matches everything)
- **Longest prefix match:** /0 is the shortest prefix, used as last resort

---

## Part 1: Propagate a Default Route

### Step 1: Test Initial Connectivity to Web Server

Before configuring the default route, test connectivity from internal PCs.

#### From PC1:
```bash
C:\> ping 64.100.1.2
```

#### From PC2:
```bash
C:\> ping 64.100.1.2
```

#### From PC3:
```bash
C:\> ping 64.100.1.2
```

---

### Answer These Questions:

**Q1: Were any of the pings successful?**

**Answer: No, none of the pings were successful.**

All three PCs will fail to reach the web server at 64.100.1.2.

---

**Q2: What message did you receive, and which device issued the message?**

**Answer: "Destination host unreachable" - issued by the default gateway router (R1, R2, or R3 depending on which PC).**

**Example messages:**
- From PC1: Message from 172.16.1.1 (R1)
- From PC2: Message from 172.16.2.1 (R2)
- From PC3: Message from 192.168.1.1 (R3)

**Why this happens:**
- PCs send packets to their default gateway
- Gateway router looks in routing table for 64.100.1.0/24
- No route exists for this network
- Router responds with "Destination host unreachable"

---

### Step 1b: Examine Routing Tables

Check the routing tables on all three routers:

```bash
R1# show ip route
R2# show ip route
R3# show ip route
```

**Q3: What statement is present in the routing tables that indicates that the pings to the Web Server will fail?**

**Answer: "Gateway of last resort is not set" OR there is no default route (0.0.0.0/0) in the routing table.**

---

## Step 2: Configure Default Route on R2

R2 is the **only router** with Internet connectivity, so it needs the default route.

### Why R2?

- This interface is the gateway to all external networks
- All Internet-bound traffic must exit through R2

### Configure the Default Static Route

```bash
R2(config)# ip route 0.0.0.0 0.0.0.0 Serial0/1/0
```

**Command Breakdown:**
- `ip route` - Create a static route
- `0.0.0.0` - Destination network (all networks)
- `0.0.0.0` - Subnet mask (match everything)
- `Serial0/0/1` - Exit interface (to Internet)

### ⚠️ Expected Warning

You may see this warning:
```
%Default route without gateway, if not a point-to-point interface, may impact performance
```

**Why this warning appears:**
- For non-point-to-point links, router needs to do ARP for every destination
- This can slow down routing

**Can you ignore it?**
- Yes! Serial 0/0/1 IS a point-to-point link
- Warning doesn't apply to our topology

---

### Verify Default Route on R2

```bash
R2# show ip route
```

**Expected output:**
```
Gateway of last resort is 0.0.0.0 to network 0.0.0.0   ← Now set!

S*   0.0.0.0/0 is directly connected, Serial0/1/0
     172.16.0.0/16 is variably subnetted, 4 subnets
C       172.16.2.0/24 is directly connected, GigabitEthernet0/0
...
```

**Notice:**
- ✅ Gateway of last resort is now set
- ✅ `S*` indicates a static default route
- ✅ Points to Serial 0/1/0

---

### Test from R2

From R2, you should now be able to ping the web server:

```bash
R2# ping 64.100.1.2
```

**Expected:** Success! ✓

**But** other routers (R1 and R3) still can't reach the Internet because they don't have the default route yet.

---

## Step 3: Propagate Default Route in OSPF

Having the default route only on R2 doesn't help R1 and R3. We need to **advertise** this route through OSPF.

### Configure OSPF to Advertise Default Route

```bash
R2(config)# router ospf 1
R2(config-router)# default-information originate
```

**Command Breakdown:**
- `router ospf 1` - Enter OSPF configuration mode (process ID 1)
- `default-information originate` - Advertise the default route to OSPF neighbors

### What This Does

1. R2 checks if it has a default route (0.0.0.0/0) - ✓ Yes
2. R2 generates a **Type 5 LSA** (External LSA) for the default route
3. R2 floods this LSA to all OSPF routers (R1 and R3)
4. R1 and R3 install the default route in their routing tables
5. R1 and R3 now know to send Internet-bound traffic to R2

---

### Understanding the Command Variations

**Option 1: With existing default route (what we used)**
```bash
R2(config-router)# default-information originate
```
- Only advertises if R2 has a default route
- If default route is removed, advertisement stops

**Option 2: Always advertise (even without default route)**
```bash
R2(config-router)# default-information originate always
```
- Advertises default route even if R2 doesn't have one
- Dangerous! Can create black holes
- Use only in specific scenarios

---

## Step 4: Examine Routing Tables on R1 and R3

After propagating the default route, check if R1 and R3 received it.



---



### Understanding the Route Notation

Let's decode `O*E2 0.0.0.0/0 [110/1] via 172.16.3.2, 00:00:08, Serial0/0/0`:

| Symbol | Meaning |
|--------|---------|
| **O** | OSPF route |
| **\*** | Candidate default route (Gateway of Last Resort) |
| **E2** | OSPF External Type 2 |
| **0.0.0.0/0** | Default route (matches all destinations) |
| **[110/1]** | Administrative Distance 110, Metric 1 |
| **via 172.16.3.2** | Next-hop IP (R2's interface) |
| **Serial0/0/0** | Exit interface |

---

### OSPF External Route Types

OSPF has two types of external routes:

| Type | Description | Metric Calculation |
|------|-------------|-------------------|
| **E1** (Type 5, External Type 1) | Metric increases with internal cost | External cost + Internal OSPF cost |
| **E2** (Type 5, External Type 2) | Metric stays constant (default) | Only external cost |

**Default route uses E2** because:
- Simpler metric calculation
- External cost doesn't change as it propagates
- All routers see the same metric (1)

---

## Part 2: Verify Connectivity

Now that the default route is propagated, test connectivity again.

### From PC1:
```bash
C:\> ping 64.100.1.2
```

### From PC2:
```bash
C:\> ping 64.100.1.2
```

### From PC3:
```bash
C:\> ping 64.100.1.2
```

**Expected Result:** All pings should now be SUCCESSFUL! ✓

---



## Key Concepts Summary

### Default Route Basics

- **0.0.0.0/0** matches any destination
- Used as "last resort" when no specific route exists
- Essential for Internet connectivity
- Typically points to ISP or Internet gateway

### OSPF Default Route Propagation

1. **Static default route** configured on edge router (R2)
2. **`default-information originate`** advertises it via OSPF
3. **Type 5 LSA** floods to all OSPF routers
4. **E2 route** appears in downstream routing tables
5. **All internal routers** can now reach Internet

---

### Command Summary

| Command | Purpose | Location |
|---------|---------|----------|
| `ip route 0.0.0.0 0.0.0.0 <interface>` | Create static default route | Edge router (R2) |
| `ip route 0.0.0.0 0.0.0.0 <next-hop-ip>` | Create static default route (next-hop) | Edge router |
| `default-information originate` | Advertise default route in OSPF | OSPF config mode |
| `default-information originate always` | Always advertise (even without route) | OSPF config mode |
| `show ip route` | View routing table | All routers |
| `show ip route 0.0.0.0` | View default route specifically | All routers |
| `show ip protocols` | View OSPF default route settings | All routers |

---



## Summary

This lab taught you:

1. **Default routes** (0.0.0.0/0) provide connectivity to unknown networks
2. **Edge routers** need static default routes to ISP
3. **`default-information originate`** propagates default route via OSPF
4. **O*E2 routes** appear in downstream routing tables
5. **All internal traffic** for Internet flows through the edge router
6. **Gateway of last resort** must be set for Internet connectivity

Understanding default route propagation is essential for connecting enterprise networks to the Internet!
