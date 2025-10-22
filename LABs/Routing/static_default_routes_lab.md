# IPv4/IPv6 Static and Default Routes Lab - Complete Guide

## Lab Overview

**Objective:** Configure various types of static routes (default, floating static, and host routes) for both IPv4 and IPv6 protocols.

**Topology:**
```
      LAN 1                    ISP1
  (192.168.10.0/28)          (198.0.0.0/24)
  (2001:db8:1:10::/64)       (2001:db8:f:f::/64)
         |                         |
       PC-A                   Customer Server
         |                         |
         |                         |
    Edge_Router -------- ISP1 (Primary Path)
    (Dual-Stack)          |
         |                |
         |          [Internet Cloud]
         |                |
    Edge_Router -------- ISP2 (Backup Path)
         |                         |
         |                         |
       PC-B                   Customer Server
         |                         |
      LAN 2                    ISP2
  (192.168.11.0/27)          (198.0.0.0/24)
  (2001:db8:1:11::/64)       (2001:db8:f:f::/64)
```

**Key Concepts:**
- **Primary routes**: Through ISP1 (AD = 1)
- **Floating backup routes**: Through ISP2 (AD = 5)
- **Dual-stack**: Both IPv4 and IPv6 configured

---

## Part 1: Configure IPv4 Static and Floating Static Default Routes

### Understanding Default Routes

**Default Route (0.0.0.0/0):**
- Matches ALL destinations
- Gateway of last resort
- Used when no specific route exists

**Floating Static Route:**
- Backup route with higher AD
- Only activates when primary fails
- AD = 5 (higher than primary AD = 1)

---

### Step 1: Configure IPv4 Static Default Route

**On Edge_Router - Primary route through ISP1:**

```cisco
Edge_Router> enable
Edge_Router# configure terminal
Edge_Router(config)# ip route 0.0.0.0 0.0.0.0 Serial0/0/0
```

**Breakdown:**
- **0.0.0.0 0.0.0.0**: Default route (matches everything)
- **Serial0/0/0**: Directly connected (exit interface to ISP1)
- **No AD specified**: Uses default AD = 1 (primary)

**Why directly connected?**
- Lab requirement specifies "directly connected"
- Point-to-point serial link
- More efficient (no recursive lookup)

---

### Step 2: Configure IPv4 Floating Static Default Route

**On Edge_Router - Backup route through ISP2:**

```cisco
Edge_Router(config)# ip route 0.0.0.0 0.0.0.0 Serial0/0/1 5
```

**Breakdown:**
- **0.0.0.0 0.0.0.0**: Default route
- **Serial0/0/1**: Exit interface to ISP2
- **5**: Administrative distance (higher = backup)

**How it works:**
```
Normal: Primary (AD=1) active, Floating (AD=5) inactive
ISP1 fails: Primary disappears, Floating (AD=5) becomes active
ISP1 returns: Primary (AD=1) returns, Floating goes back to backup
```

**Verify configuration:**

```cisco
Edge_Router# show ip route
```

**Expected output:**
```
Gateway of last resort is 0.0.0.0 to network 0.0.0.0
S*   0.0.0.0/0 is directly connected, Serial0/0/0
```

**Note:** Floating route won't appear until primary fails!

---

## Part 2: Configure IPv6 Static and Floating Static Default Routes

### Understanding IPv6 Default Routes

**IPv6 Default Route (::/0):**
- Equivalent to 0.0.0.0/0 in IPv4
- All zeros compressed to ::
- Prefix length = 0

---

### Step 1: Configure IPv6 Static Default Route

**On Edge_Router - Primary route through ISP1:**

```cisco
Edge_Router(config)# ipv6 route ::/0 2001:db8:a:1::1
```

**Breakdown:**
- **::/0**: IPv6 default route (all zeros/0)
- **2001:db8:a:1::1**: Next-hop IP (ISP1's S0/0/0 address)
- **Next-hop style**: As specified in lab requirements

**Why next-hop instead of directly connected?**
- Lab requirement: "configure a next hop static default route"
- Uses ISP1's interface IP as next-hop

---

### Step 2: Configure IPv6 Floating Static Default Route

**On Edge_Router - Backup route through ISP2:**

```cisco
Edge_Router(config)# ipv6 route ::/0 2001:db8:a:2::1 5
```

**Breakdown:**
- **::/0**: IPv6 default route
- **2001:db8:a:2::1**: Next-hop IP (ISP2's S0/0/1 address)
- **5**: Administrative distance (backup)

**Don't forget to enable IPv6 routing:**

```cisco
Edge_Router(config)# ipv6 unicast-routing
```

**Verify configuration:**

```cisco
Edge_Router# show ipv6 route
```

**Expected output:**
```
S   ::/0 [1/0]
     via 2001:db8:a:1::1
```

---

## Part 3: Configure IPv4 Static and Floating Static Routes to Internal LANs

### Network Information

**LAN 1:**
- IPv4: 192.168.10.16/28 (192.168.10.16 - 192.168.10.31)
- Connected to Edge_Router G0/0

**LAN 2:**
- IPv4: 192.168.11.32/27 (192.168.11.32 - 192.168.11.63)
- Connected to Edge_Router G0/1

---

### Step 1: Configure IPv4 Static Routes to Internal LANs

**On ISP1 - Routes to Edge_Router LANs:**

#### Route to LAN 1

```cisco
ISP1> enable
ISP1# configure terminal
ISP1(config)# ip route 192.168.10.16 255.255.255.240 10.10.10.2
```

**Breakdown:**
- **192.168.10.16**: LAN 1 network address
- **255.255.255.240**: /28 subnet mask
- **10.10.10.2**: Next-hop (Edge_Router S0/0/0 IP)

#### Route to LAN 2

```cisco
ISP1(config)# ip route 192.168.11.32 255.255.255.224 10.10.10.2
```

**Breakdown:**
- **192.168.11.32**: LAN 2 network address
- **255.255.255.224**: /27 subnet mask
- **10.10.10.2**: Next-hop (Edge_Router S0/0/0 IP)

---

### Step 2: Configure IPv4 Floating Static Routes to Internal LANs

**On ISP1 - Backup routes through ISP2:**

#### Floating Route to LAN 1

```cisco
ISP1(config)# ip route 192.168.10.16 255.255.255.240 Serial0/0/1 5
```

**Breakdown:**
- **192.168.10.16 255.255.255.240**: LAN 1 network
- **Serial0/0/1**: Exit interface toward ISP2 (directly connected)
- **5**: AD for floating static

**Wait, how does this reach Edge_Router through ISP2?**
- ISP1 → ISP2 (via Serial0/0/1)
- ISP2 → Edge_Router (ISP2 needs routes too!)
- This is backup path when ISP1-Edge_Router link fails

#### Floating Route to LAN 2

```cisco
ISP1(config)# ip route 192.168.11.32 255.255.255.224 Serial0/0/1 5
```

**Verify:**

```cisco
ISP1# show ip route
```

**Expected output:**
```
S    192.168.10.16/28 [1/0] via 10.10.10.2
S    192.168.11.32/27 [1/0] via 10.10.10.2
```

---

## Part 4: Configure IPv6 Static and Floating Static Routes to Internal LANs

### Network Information

**LAN 1 IPv6:**
- Network: 2001:db8:1:10::/64
- Connected to Edge_Router G0/0

**LAN 2 IPv6:**
- Network: 2001:db8:1:11::/64
- Connected to Edge_Router G0/1

---

### Step 1: Configure IPv6 Static Routes to Internal LANs

**On ISP1 - Routes to Edge_Router LANs:**

#### Route to LAN 1

```cisco
ISP1(config)# ipv6 route 2001:db8:1:10::/64 2001:db8:a:1::2
```

**Breakdown:**
- **2001:db8:1:10::/64**: LAN 1 IPv6 network
- **2001:db8:a:1::2**: Next-hop (Edge_Router S0/0/0 IPv6)

#### Route to LAN 2

```cisco
ISP1(config)# ipv6 route 2001:db8:1:11::/64 2001:db8:a:1::2
```

**Breakdown:**
- **2001:db8:1:11::/64**: LAN 2 IPv6 network
- **2001:db8:a:1::2**: Next-hop (Edge_Router S0/0/0 IPv6)

---

### Step 2: Configure IPv6 Floating Static Routes to Internal LANs

**On ISP1 - Backup routes through ISP2:**

#### Floating Route to LAN 1

```cisco
ISP1(config)# ipv6 route 2001:db8:1:10::/64 2001:db8:f:f::2 5
```

**Breakdown:**
- **2001:db8:1:10::/64**: LAN 1 IPv6 network
- **2001:db8:f:f::2**: Next-hop (ISP2's G0/0 IPv6 - path through ISP2)
- **5**: AD for floating static

**Important:** This goes through ISP2's G0/0 interface (shared segment with ISP1)

#### Floating Route to LAN 2

```cisco
ISP1(config)# ipv6 route 2001:db8:1:11::/64 2001:db8:f:f::2 5
```

**Verify:**

```cisco
ISP1# show ipv6 route
```

**Expected output:**
```
S   2001:db8:1:10::/64 [1/0]
     via 2001:db8:a:1::2
S   2001:db8:1:11::/64 [1/0]
     via 2001:db8:a:1::2
```

---

## Part 5: Configure Host Routes

### Understanding Host Routes

**Host Route:**
- Route to ONE specific device (/32 for IPv4, /128 for IPv6)
- Most specific route possible
- Higher priority than network routes

**Customer Server:**
- IPv4: 198.0.0.10/24
- IPv6: 2001:db8:f:f::10/64
- Located in ISP network (Internet cloud)

---

### Step 1: Configure IPv4 Host Routes

**On Edge_Router - Primary host route:**

```cisco
Edge_Router(config)# ip route 198.0.0.10 255.255.255.255 Serial0/0/0
```

**Breakdown:**
- **198.0.0.10**: Customer server IP
- **255.255.255.255**: /32 mask (HOST route!)
- **Serial0/0/0**: Directly connected to ISP1

**On Edge_Router - Floating host route:**

```cisco
Edge_Router(config)# ip route 198.0.0.10 255.255.255.255 Serial0/0/1 5
```

**Breakdown:**
- **198.0.0.10**: Customer server IP
- **255.255.255.255**: /32 mask
- **Serial0/0/1**: Directly connected to ISP2 (backup)
- **5**: AD for floating static

**Why host routes?**
- Customer server frequently accessed
- Need specific, reliable path
- Redundancy with floating backup

---

### Step 2: Configure IPv6 Host Routes

**On Edge_Router - Primary host route:**

```cisco
Edge_Router(config)# ipv6 route 2001:db8:f:f::10/128 2001:db8:a:1::1
```

**Breakdown:**
- **2001:db8:f:f::10/128**: Customer server IPv6 (/128 = HOST!)
- **2001:db8:a:1::1**: Next-hop (ISP1 S0/0/0)

**On Edge_Router - Floating host route:**

```cisco
Edge_Router(config)# ipv6 route 2001:db8:f:f::10/128 2001:db8:a:2::1 5
```

**Breakdown:**
- **2001:db8:f:f::10/128**: Customer server IPv6
- **2001:db8:a:2::1**: Next-hop (ISP2 S0/0/1)
- **5**: AD for floating static

**Verify:**

```cisco
Edge_Router# show ip route
Edge_Router# show ipv6 route
```

**Look for:**
```
IPv4: S    198.0.0.10/32 is directly connected, Serial0/0/0
IPv6: S   2001:db8:f:f::10/128 [1/0] via 2001:db8:a:1::1
```

---

## Complete Configuration Summary

### Edge_Router Complete Configuration

```cisco
! Enable IPv6 routing
ipv6 unicast-routing

! IPv4 Default Routes
ip route 0.0.0.0 0.0.0.0 Serial0/0/0
ip route 0.0.0.0 0.0.0.0 Serial0/0/1 5

! IPv6 Default Routes
ipv6 route ::/0 2001:db8:a:1::1
ipv6 route ::/0 2001:db8:a:2::1 5

! IPv4 Host Routes
ip route 198.0.0.10 255.255.255.255 Serial0/0/0
ip route 198.0.0.10 255.255.255.255 Serial0/0/1 5

! IPv6 Host Routes
ipv6 route 2001:db8:f:f::10/128 2001:db8:a:1::1
ipv6 route 2001:db8:f:f::10/128 2001:db8:a:2::1 5
```

---

### ISP1 Complete Configuration

```cisco
! Enable IPv6 routing
ipv6 unicast-routing

! IPv4 Routes to Internal LANs
ip route 192.168.10.16 255.255.255.240 10.10.10.2
ip route 192.168.11.32 255.255.255.224 10.10.10.2

! IPv4 Floating Routes
ip route 192.168.10.16 255.255.255.240 Serial0/0/1 5
ip route 192.168.11.32 255.255.255.224 Serial0/0/1 5

! IPv6 Routes to Internal LANs
ipv6 route 2001:db8:1:10::/64 2001:db8:a:1::2
ipv6 route 2001:db8:1:11::/64 2001:db8:a:1::2

! IPv6 Floating Routes
ipv6 route 2001:db8:1:10::/64 2001:db8:f:f::2 5
ipv6 route 2001:db8:1:11::/64 2001:db8:f:f::2 5
```

---

## Verification and Testing

### Step 1: Verify Routing Tables

**On Edge_Router:**

```cisco
Edge_Router# show ip route
Edge_Router# show ipv6 route
```

**Look for:**
- Default routes (S* or S with ::/0)
- Host routes (/32 or /128)
- Primary routes active (AD = 1)

---

### Step 2: Test Connectivity

**From PC-A (LAN 1):**

```
C:\> ping 198.0.0.10
Reply from 198.0.0.10 ✅

C:\> ping 2001:db8:f:f::10
Reply from 2001:db8:f:f::10 ✅
```

**From PC-B (LAN 2):**

```
C:\> ping 198.0.0.10
Reply from 198.0.0.10 ✅

C:\> ping 2001:db8:f:f::10
Reply from 2001:db8:f:f::10 ✅
```

---

### Step 3: Test Failover (Optional)

**Simulate ISP1 failure:**

1. Delete link between Edge_Router and ISP1
2. Wait a few seconds
3. Test connectivity again

**Expected result:**
- Floating routes (AD=5) become active
- Connectivity maintained through ISP2
- Traffic automatically rerouted

**Verify floating routes active:**

```cisco
Edge_Router# show ip route
```

**Look for routes with [5/0] - these are floating routes now active!**

---

## Essential Verification Commands

```cisco
! IPv4 routing table
show ip route
show ip route static

! IPv6 routing table
show ipv6 route
show ipv6 route static

! Specific route lookup
show ip route 198.0.0.10
show ipv6 route 2001:db8:f:f::10

! Interface status
show ip interface brief
show ipv6 interface brief

! Routing summary
show ip route summary
show ipv6 route summary
```

---

## Troubleshooting Guide

### Problem: Cannot ping Customer Server

**Checklist:**
```
1. Verify default routes configured on Edge_Router
   show ip route | include 0.0.0.0
   
2. Verify host routes configured
   show ip route 198.0.0.10
   
3. Check interface status
   show ip interface brief
   
4. Verify IPv6 routing enabled
   show run | include ipv6 unicast-routing
   
5. Test with traceroute
   tracert 198.0.0.10
```

---

### Problem: Floating routes not working

**Check:**
```
1. AD value = 5 (higher than primary)
   show run | include ip route
   
2. Primary route exists and is active
   show ip route
   
3. Remove primary route to test floating
   Delete link or shutdown interface
```

---

### Problem: IPv6 not working

**Check:**
```
1. IPv6 routing enabled globally
   ipv6 unicast-routing
   
2. IPv6 addresses configured on interfaces
   show ipv6 interface brief
   
3. IPv6 routes configured
   show ipv6 route
```

---

## Lab Completion Checklist

### Part 1: IPv4 Default Routes ✅
- [ ] Primary default route via ISP1 (S0/0/0, directly connected)
- [ ] Floating default route via ISP2 (S0/0/1, AD=5, directly connected)

### Part 2: IPv6 Default Routes ✅
- [ ] Primary default route via ISP1 (next-hop 2001:db8:a:1::1)
- [ ] Floating default route via ISP2 (next-hop 2001:db8:a:2::1, AD=5)

### Part 3: IPv4 Routes to LANs ✅
- [ ] ISP1 route to LAN 1 (192.168.10.16/28, next-hop)
- [ ] ISP1 route to LAN 2 (192.168.11.32/27, next-hop)
- [ ] ISP1 floating to LAN 1 (via S0/0/1, AD=5, directly connected)
- [ ] ISP1 floating to LAN 2 (via S0/0/1, AD=5, directly connected)

### Part 4: IPv6 Routes to LANs ✅
- [ ] ISP1 route to LAN 1 (2001:db8:1:10::/64, next-hop)
- [ ] ISP1 route to LAN 2 (2001:db8:1:11::/64, next-hop)
- [ ] ISP1 floating to LAN 1 (via 2001:db8:f:f::2, AD=5)
- [ ] ISP1 floating to LAN 2 (via 2001:db8:f:f::2, AD=5)

### Part 5: Host Routes ✅
- [ ] IPv4 host route to server (198.0.0.10/32, directly connected)
- [ ] IPv4 floating host route (via S0/0/1, AD=5)
- [ ] IPv6 host route to server (2001:db8:f:f::10/128, next-hop)
- [ ] IPv6 floating host route (next-hop 2001:db8:a:2::1, AD=5)

### Final Testing ✅
- [ ] PC-A can ping Customer Server (IPv4 and IPv6)
- [ ] PC-B can ping Customer Server (IPv4 and IPv6)
- [ ] Failover works when primary link removed
- [ ] Connectivity restored when primary link returns

---

## Key Takeaways

🔑 **Default routes**: 0.0.0.0/0 (IPv4) and ::/0 (IPv6)  
🔑 **Floating static AD**: Must be higher than primary (use 5)  
🔑 **Host routes**: /32 (IPv4) or /128 (IPv6) for single device  
🔑 **Directly connected**: Uses exit interface only  
🔑 **Next-hop**: Uses next-hop IP address  
🔑 **IPv6 routing**: Must enable with `ipv6 unicast-routing`  
🔑 **Primary vs Backup**: Lower AD = active, higher AD = backup  
🔑 **Redundancy**: Floating routes provide automatic failover  
🔑 **Verification**: Use show ip route and show ipv6 route  
🔑 **Testing**: Ping and traceroute to verify connectivity