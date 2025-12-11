# Propagate Default Route in OSPFv2 Guide

## Lab Overview

This lab demonstrates how to configure and propagate a default route in OSPF. You'll learn why internal networks need a default route to reach the Internet and how OSPF can distribute this route to all routers in the domain.

## Network Topology

### Router Connections
- **R1** ↔ **R2**: Serial link via 172.16.3.0/30
- **R1** ↔ **R3**: Serial link via 192.168.10.4/30
- **R2** ↔ **R3**: Serial link via 192.168.10.8/30
- **R2** → **Internet**: Serial 0/1/0 (209.165.200.225/27) 🌐

### LAN Networks
- **R1 LAN**: 172.16.1.0/24 (PC1: 172.16.1.2)
- **R2 LAN**: 172.16.2.0/24 (PC2: 172.16.2.2)
- **R3 LAN**: 192.168.1.0/24 (PC3: 192.168.1.2)
- **Web Server**: 64.100.1.2/24 (on the Internet)

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

**Example output from R1:**
```
R1# show ip route

Codes: L - local, C - connected, S - static, O - OSPF, ...

Gateway of last resort is not set   ← THIS IS THE PROBLEM!

     172.16.0.0/16 is variably subnetted, 3 subnets
C       172.16.1.0/24 is directly connected, GigabitEthernet0/0
L       172.16.1.1/32 is directly connected, GigabitEthernet0/0
O       172.16.2.0/24 [110/65] via 172.16.3.2, Serial0/0/0
C       172.16.3.0/30 is directly connected, Serial0/0/0
L       172.16.3.1/32 is directly connected, Serial0/0/0
     192.168.1.0/24 is variably subnetted, 2 subnets
O       192.168.1.0/24 [110/65] via 192.168.10.6, Serial0/0/1
     192.168.10.0/30 is subnetted, 2 subnets
C       192.168.10.4/30 is directly connected, Serial0/0/1
O       192.168.10.8/30 [110/128] via 172.16.3.2, Serial0/0/0
```

**Notice:**
- ❌ No 0.0.0.0/0 route
- ❌ No route to 64.100.1.0/24
- ❌ "Gateway of last resort is not set"
- ✅ Only internal OSPF routes (172.16.x.x and 192.168.x.x)

---

## Step 2: Configure Default Route on R2

R2 is the **only router** with Internet connectivity, so it needs the default route.

### Why R2?

- R2 has Serial 0/1/0 connected to the Internet (209.165.200.225/27)
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
- `Serial0/1/0` - Exit interface (to Internet)

### ⚠️ Expected Warning

You may see this warning:
```
%Default route without gateway, if not a point-to-point interface, may impact performance
```

**Why this warning appears:**
- For non-point-to-point links, router needs to do ARP for every destination
- This can slow down routing

**Can you ignore it?**
- Yes! Serial 0/1/0 IS a point-to-point link
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

### Check R1's Routing Table

```bash
R1# show ip route
```

**Expected output:**
```
Gateway of last resort is 172.16.3.2 to network 0.0.0.0   ← Now set!

O*E2 0.0.0.0/0 [110/1] via 172.16.3.2, 00:00:08, Serial0/0/0
     172.16.0.0/16 is variably subnetted, 3 subnets
C       172.16.1.0/24 is directly connected, GigabitEthernet0/0
L       172.16.1.1/32 is directly connected, GigabitEthernet0/0
O       172.16.2.0/24 [110/65] via 172.16.3.2, Serial0/0/0
...
```

---

### Check R3's Routing Table

```bash
R3# show ip route
```

**Expected output:**
```
Gateway of last resort is 192.168.10.9 to network 0.0.0.0   ← Now set!

O*E2 0.0.0.0/0 [110/1] via 192.168.10.9, 00:08:15, Serial0/0/1
     172.16.0.0/16 is variably subnetted, 2 subnets
O       172.16.1.0/24 [110/129] via 192.168.10.9, Serial0/0/1
O       172.16.2.0/24 [110/65] via 192.168.10.9, Serial0/0/1
...
```

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

### Trace the Path

Use traceroute to see the complete path to the web server:

#### From PC1:
```bash
C:\> tracert 64.100.1.2

Tracing route to 64.100.1.2 over a maximum of 30 hops:

1    1 ms    0 ms    3 ms    172.16.1.1       [R1 - default gateway]
2    2 ms    1 ms    1 ms    172.16.3.2       [R2 - Internet gateway]
3    1 ms    2 ms    1 ms    209.165.200.226  [ISP router]
4    2 ms    1 ms    2 ms    64.100.1.2       [Web Server]

Trace complete.
```

**Path:** PC1 → R1 → **R2** → Internet → Web Server

---

#### From PC3:
```bash
C:\> tracert 64.100.1.2

Tracing route to 64.100.1.2 over a maximum of 30 hops:

1    1 ms    0 ms    3 ms    192.168.1.1      [R3 - default gateway]
2    2 ms    1 ms    1 ms    192.168.10.9     [R2 - Internet gateway]
3    1 ms    2 ms    1 ms    209.165.200.226  [ISP router]
4    2 ms    1 ms    2 ms    64.100.1.2       [Web Server]

Trace complete.
```

**Path:** PC3 → R3 → **R2** → Internet → Web Server

**Key Observation:** All paths converge at R2, the Internet gateway!

---

## How It All Works Together

### Before Default Route Configuration

```
PC1 (172.16.1.2)
    ↓ [tries to reach 64.100.1.2]
R1 (172.16.1.1)
    ↓ [checks routing table]
    ❌ No route to 64.100.1.0/24
    ❌ No default route
    ↓
❌ "Destination host unreachable"
```

---

### After Default Route Configuration

```
PC1 (172.16.1.2)
    ↓ [tries to reach 64.100.1.2]
R1 (172.16.1.1)
    ↓ [checks routing table]
    ❌ No specific route to 64.100.1.0/24
    ✅ Default route: 0.0.0.0/0 via 172.16.3.2
    ↓ [forwards to R2]
R2 (172.16.3.2)
    ↓ [has direct Internet connection]
    ✅ Forwards to Internet via S0/1/0
    ↓
ISP Router (209.165.200.226)
    ↓
✅ Web Server (64.100.1.2)
```

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

## Verification Command Reference

### Check Default Route Presence

```bash
R1# show ip route | include 0.0.0.0
Gateway of last resort is 172.16.3.2 to network 0.0.0.0
O*E2 0.0.0.0/0 [110/1] via 172.16.3.2, 00:10:23, Serial0/0/0
```

### Check OSPF Protocol Status

```bash
R2# show ip protocols

Routing Protocol is "ospf 1"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 209.165.200.225
  Number of areas in this router is 1. 1 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
    172.16.2.0 0.0.0.255 area 0
    172.16.3.0 0.0.0.3 area 0
    192.168.10.8 0.0.0.3 area 0
  Routing Information Sources:
    Gateway         Distance      Last Update
    192.168.10.5         110      00:15:23
    192.168.1.1          110      00:15:23
  Distance: (default is 110)
  Redistributing External Routes from,
    static with metric mapped to 1  ← Default route being advertised
```

### Test Connectivity

```bash
PC1> ping 64.100.1.2
PC1> tracert 64.100.1.2

R1# ping 64.100.1.2
R1# traceroute 64.100.1.2
```

---

## Troubleshooting Guide

### Problem 1: PCs Still Can't Reach Internet

**Symptoms:**
- Pings to 64.100.1.2 fail
- "Destination host unreachable"

**Checklist:**

1. **Check if R2 has default route:**
   ```bash
   R2# show ip route | include 0.0.0.0
   ```
   Should show: `S* 0.0.0.0/0 is directly connected, Serial0/1/0`

2. **Check if OSPF is advertising default route:**
   ```bash
   R2# show ip protocols
   ```
   Look for: "Redistributing External Routes from, static"

3. **Check if R1/R3 received default route:**
   ```bash
   R1# show ip route | include 0.0.0.0
   ```
   Should show: `O*E2 0.0.0.0/0 [110/1] via ...`

4. **Check OSPF neighbor relationships:**
   ```bash
   R2# show ip ospf neighbor
   ```
   All neighbors should be in FULL state

---

### Problem 2: Default Route Not Appearing on R1/R3

**Symptoms:**
- R2 has default route
- R1 and R3 don't show O*E2 route

**Solutions:**

1. **Verify `default-information originate` is configured:**
   ```bash
   R2# show running-config | section router ospf
   router ospf 1
    default-information originate  ← Must be present
   ```

2. **Check OSPF process ID matches:**
   - All routers must use same OSPF process (usually 1)

3. **Verify OSPF area matches:**
   - All interfaces must be in Area 0

4. **Force OSPF to recalculate:**
   ```bash
   R2# clear ip ospf process
   ```

---

### Problem 3: Wrong Gateway of Last Resort

**Symptoms:**
- Default route points to wrong interface/router
- Routing loops occur

**Solutions:**

1. **Check routing table for multiple default routes:**
   ```bash
   R1# show ip route
   ```

2. **Verify administrative distances:**
   - Static route: AD 1
   - OSPF: AD 110
   - Lower AD wins

3. **Remove incorrect static routes:**
   ```bash
   R1(config)# no ip route 0.0.0.0 0.0.0.0 ...
   ```

---

### Problem 4: Intermittent Connectivity

**Symptoms:**
- Pings sometimes work, sometimes fail
- Traceroute shows changing paths

**Possible Causes:**

1. **OSPF adjacency flapping:**
   ```bash
   R2# show ip ospf neighbor
   ```
   Check for neighbor going up/down

2. **Interface flapping:**
   ```bash
   R2# show interface s0/1/0
   ```
   Check interface status

3. **Incorrect metrics causing route flapping:**
   ```bash
   R1# show ip route 0.0.0.0
   ```
   Verify metric is stable

---

## Best Practices

### Production Network Recommendations

1. **Use `default-information originate` without `always`**
   - Only advertises when default route exists
   - Prevents black holes

2. **Configure default route with next-hop IP when possible:**
   ```bash
   R2(config)# ip route 0.0.0.0 0.0.0.0 209.165.200.226
   ```
   - More efficient than exit interface
   - Avoids ARP lookups

3. **Monitor default route propagation:**
   ```bash
   R2# show ip ospf database external
   ```

4. **Document Internet gateway routers clearly**
   - Only edge routers should originate default routes
   - Multiple default routes can cause confusion

5. **Consider redundant Internet connections:**
   - Multiple default routes with different metrics
   - Automatic failover

---

## Advanced Concepts

### Default Route with Metric

You can specify a metric when originating the default route:

```bash
R2(config-router)# default-information originate metric 100
```

This is useful when:
- Multiple routers can originate default routes
- You want to prefer one path over another

### Default Route with Route Map

For conditional advertisement:

```bash
R2(config)# route-map CHECK_INTERNET permit 10
R2(config-route-map)# match interface Serial0/1/0

R2(config)# router ospf 1
R2(config-router)# default-information originate route-map CHECK_INTERNET
```

Only advertises if Serial0/1/0 is up.

### Multiple Internet Gateways

If both R1 and R2 have Internet connections:

```bash
R1(config)# ip route 0.0.0.0 0.0.0.0 <ISP1>
R1(config)# router ospf 1
R1(config-router)# default-information originate metric 10

R2(config)# ip route 0.0.0.0 0.0.0.0 <ISP2>
R2(config)# router ospf 1
R2(config-router)# default-information originate metric 20
```

R1's path preferred due to lower metric.

---

## Lab Completion Checklist

- [ ] Tested initial connectivity (all PCs failed to reach 64.100.1.2)
- [ ] Identified "Gateway of last resort is not set" on R1 and R3
- [ ] Configured static default route on R2
- [ ] Verified default route on R2 with `show ip route`
- [ ] Configured `default-information originate` on R2
- [ ] Verified O*E2 default route on R1
- [ ] Verified O*E2 default route on R3
- [ ] Tested connectivity from all PCs to web server (successful)
- [ ] Used traceroute to verify paths through R2
- [ ] Understand why only R2 originates the default route

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