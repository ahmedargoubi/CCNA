# Layer 3 Switching and Inter-VLAN Routing Lab Guide

## Lab Overview

### Topology
```
[Cloud] 209.165.200.226
    |
   G0/2 (Routed Port)
    |
  [MLS] - Multilayer Switch
    | G0/1 (Trunk)
    |
   [S1] - Layer 2 Switch (Trunks to S2, S3)
    |
VLANs: 10 (Staff), 20 (Student), 30 (Faculty), 99 (Management)
    |
  PCs in each VLAN
```

### Key Addressing

| Device | Interface | IPv4 Address | IPv6 Address |
|--------|-----------|--------------|--------------|
| MLS | VLAN 10 | 192.168.10.254/24 | 2001:db8:acad:10::1/64 |
| MLS | VLAN 20 | 192.168.20.254/24 | 2001:db8:acad:20::1/64 |
| MLS | VLAN 30 | 192.168.30.254/24 | 2001:db8:acad:30::1/64 |
| MLS | VLAN 99 | 192.168.99.254/24 | N/A |
| MLS | G0/2 | 209.165.200.225/30 | 2001:db8:acad:a::1/64 |

### Objectives
- Configure routed port on Layer 3 switch
- Configure SVIs for Inter-VLAN routing
- Enable IP routing (IPv4 and IPv6)
- Configure trunking on multilayer switch

---

## Part 1: Configure Layer 3 Switching

### Concept: Routed Port

**What is a Routed Port?**
- Converts Layer 2 switchport to Layer 3 interface
- Acts like router interface
- Can have IP address
- No VLAN assignment

**Command:** `no switchport`

---

### Step 1: Configure G0/2 as Routed Port

```cisco
MLS> enable
MLS# configure terminal
MLS(config)# interface gigabitEthernet 0/2
MLS(config-if)# no switchport
MLS(config-if)# ip address 209.165.200.225 255.255.255.252
MLS(config-if)# no shutdown
MLS(config-if)# exit
```

**What This Does:**
- `no switchport` - Converts to Layer 3 interface (routed port)
- `ip address` - Assigns IP like a router interface
- Now behaves as router port, not switch port

**Important:** By default, switch interfaces are Layer 2. `no switchport` makes it Layer 3.

---

### Step 2: Verify Connectivity

```cisco
MLS# ping 209.165.200.226

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 209.165.200.226, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms
```

**Verification:**
✓ Ping succeeds = routed port working
✓ Layer 3 connectivity to Cloud established

---

## Part 2: Configure Inter-VLAN Routing (IPv4)

### Step 1: Add VLANs

```cisco
MLS(config)# vlan 10
MLS(config-vlan)# name Staff
MLS(config-vlan)# exit

MLS(config)# vlan 20
MLS(config-vlan)# name Student
MLS(config-vlan)# exit

MLS(config)# vlan 30
MLS(config-vlan)# name Faculty
MLS(config-vlan)# exit

MLS(config)# vlan 99
MLS(config-vlan)# name Management
MLS(config-vlan)# exit
```

**Note:** Names are case-sensitive in Packet Tracer scoring.

---

### Step 2: Configure SVIs (Switched Virtual Interfaces)

**What is SVI?**
- Virtual interface representing a VLAN
- Default gateway for devices in that VLAN
- Enables Inter-VLAN routing

**Configure all four SVIs:**

```cisco
! VLAN 10 SVI
MLS(config)# interface vlan 10
MLS(config-if)# ip address 192.168.10.254 255.255.255.0
MLS(config-if)# no shutdown
MLS(config-if)# exit

! VLAN 20 SVI
MLS(config)# interface vlan 20
MLS(config-if)# ip address 192.168.20.254 255.255.255.0
MLS(config-if)# no shutdown
MLS(config-if)# exit

! VLAN 30 SVI
MLS(config)# interface vlan 30
MLS(config-if)# ip address 192.168.30.254 255.255.255.0
MLS(config-if)# no shutdown
MLS(config-if)# exit

! VLAN 99 SVI (Management)
MLS(config)# interface vlan 99
MLS(config-if)# ip address 192.168.99.254 255.255.255.0
MLS(config-if)# no shutdown
MLS(config-if)# exit
```

**Remember:** Always use `no shutdown` on SVIs!

---

### Step 3: Configure Trunking on MLS

**Multilayer Switch Trunk Configuration:**

```cisco
MLS(config)# interface gigabitEthernet 0/1
MLS(config-if)# switchport trunk encapsulation dot1q
MLS(config-if)# switchport mode trunk
MLS(config-if)# switchport trunk native vlan 99
MLS(config-if)# exit
```

**Command Breakdown:**

**`switchport trunk encapsulation dot1q`**
- Multilayer switches may support ISL or 802.1Q
- Must specify encapsulation type first
- Use 802.1Q (industry standard)

**`switchport mode trunk`**
- Configures port as trunk

**`switchport trunk native vlan 99`**
- Sets native VLAN to 99

**Difference from Layer 2 Switch:**
- Layer 2 switch: Just `switchport mode trunk`
- Layer 3 switch: Must add `switchport trunk encapsulation dot1q` FIRST

---

### Step 4: Configure Trunking on S1

```cisco
S1(config)# interface gigabitEthernet 0/1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk native vlan 99
S1(config-if)# exit
```

**Note:** Layer 2 switch doesn't need encapsulation command.

---

### Step 5: Enable IP Routing

**Critical Step!**

**Check routing status:**
```cisco
MLS# show ip route
```

**Question:** Are there any active routes?

**Answer:** NO - No routes shown because routing is disabled by default.

**Enable IP Routing:**
```cisco
MLS(config)# ip routing
```

**This command:**
- Enables Layer 3 routing on the switch
- Creates routing table
- Allows Inter-VLAN routing
- **REQUIRED for Inter-VLAN routing to work**

---

**Verify Routing Enabled:**
```cisco
MLS# show ip route

Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       <output omitted>

Gateway of last resort is not set

C    192.168.10.0/24 is directly connected, Vlan10
C    192.168.20.0/24 is directly connected, Vlan20
C    192.168.30.0/24 is directly connected, Vlan30
C    192.168.99.0/24 is directly connected, Vlan99
     209.165.200.0/30 is subnetted, 1 subnets
C       209.165.200.224 is directly connected, GigabitEthernet0/2
```

**Verification:**
✓ All VLANs show as directly connected (C)
✓ Routed port (G0/2) also connected
✓ Routing table populated

---

### Step 6: Verify End-to-End Connectivity

**Test Within VLANs:**

```
PC0 (VLAN 10) → ping 192.168.10.2 (PC3)
PC0 → ping 192.168.10.254 (Gateway)

PC1 (VLAN 20) → ping 192.168.20.2 (PC4)
PC1 → ping 192.168.20.254 (Gateway)

PC2 (VLAN 30) → ping 192.168.30.2 (PC5)
PC2 → ping 192.168.30.254 (Gateway)
```

**Test Management VLAN:**
```
S1 (VLAN 99) → ping 192.168.99.2 (S2)
S1 → ping 192.168.99.254 (MLS)
```

**Test Inter-VLAN Routing:**
```
PC0 (VLAN 10) → ping 192.168.20.1 (PC1 in VLAN 20)
PC1 (VLAN 20) → ping 192.168.30.1 (PC2 in VLAN 30)
```

**Test External Connectivity:**
```
Any PC → ping 209.165.200.226 (Cloud)
```

**Result:** All pings should succeed! ✓

---

## Part 3: Configure IPv6 Inter-VLAN Routing

### Step 1: Enable IPv6 Routing

```cisco
MLS(config)# ipv6 unicast-routing
```

**What This Does:**
- Enables IPv6 routing on switch
- Required for IPv6 Inter-VLAN routing
- Similar to `ip routing` for IPv4

---

### Step 2: Configure IPv6 on SVIs

**Add IPv6 addresses to existing SVIs:**

```cisco
! VLAN 10 SVI
MLS(config)# interface vlan 10
MLS(config-if)# ipv6 address 2001:db8:acad:10::1/64
MLS(config-if)# exit

! VLAN 20 SVI
MLS(config)# interface vlan 20
MLS(config-if)# ipv6 address 2001:db8:acad:20::1/64
MLS(config-if)# exit

! VLAN 30 SVI
MLS(config)# interface vlan 30
MLS(config-if)# ipv6 address 2001:db8:acad:30::1/64
MLS(config-if)# exit
```

**Note:** 
- Same SVIs now have both IPv4 and IPv6 addresses
- Dual-stack configuration

---

### Step 3: Configure IPv6 on G0/2

```cisco
MLS(config)# interface gigabitEthernet 0/2
MLS(config-if)# ipv6 address 2001:db8:acad:a::1/64
MLS(config-if)# exit
```

---

### Step 4: Verify IPv6 Routing Table

```cisco
MLS# show ipv6 route

IPv6 Routing Table - 10 entries
Codes: C - Connected, L - Local, S - Static, R - RIP, B - BGP
       <output omitted>

S   ::/0 [1/0]
     via 2001:DB8:ACAD:A::2, GigabitEthernet0/2
C   2001:DB8:ACAD:A::/64 [0/0]
     via ::, GigabitEthernet0/2
L   2001:DB8:ACAD:A::1/128 [0/0]
     via ::, GigabitEthernet0/2
C   2001:DB8:ACAD:10::/64 [0/0]
     via ::, Vlan10
L   2001:DB8:ACAD:10::1/128 [0/0]
     via ::, Vlan10
C   2001:DB8:ACAD:20::/64 [0/0]
     via ::, Vlan20
L   2001:DB8:ACAD:20::1/128 [0/0]
     via ::, Vlan20
C   2001:DB8:ACAD:30::/64 [0/0]
     via ::, Vlan30
L   2001:DB8:ACAD:30::1/128 [0/0]
     via ::, Vlan30
L   FF00::/8 [0/0]
     via ::, Null0
```

**Understanding IPv6 Routes:**

**C (Connected):**
- Networks directly attached
- Example: `2001:DB8:ACAD:10::/64` via Vlan10

**L (Local):**
- Interface's own address (/128)
- Example: `2001:DB8:ACAD:10::1/128`

**S (Static):**
- Default route to Cloud
- `::/0` via `2001:DB8:ACAD:A::2`

---

### Step 5: Verify IPv6 Connectivity

**Test Within VLANs (IPv6):**

```
PC3 (VLAN 10) → ping 2001:db8:acad:10::1 (Gateway)
PC4 (VLAN 20) → ping 2001:db8:acad:20::1 (Gateway)
PC5 (VLAN 30) → ping 2001:db8:acad:30::1 (Gateway)
```

**Test Inter-VLAN Routing (IPv6):**

```
PC3 → ping 2001:db8:acad:20::2 (PC4 in VLAN 20)
PC4 → ping 2001:db8:acad:30::2 (PC5 in VLAN 30)
PC5 → ping 2001:db8:acad:10::2 (PC3 in VLAN 10)
```

**Test External Connectivity (IPv6):**

```
PC3 → ping 2001:db8:acad:a::2 (Cloud)
```

**Result:** All IPv6 pings succeed! ✓

---

## Configuration Summary

### Complete MLS Configuration

```cisco
! Enable routing
ip routing
ipv6 unicast-routing

! Create VLANs
vlan 10
 name Staff
vlan 20
 name Student
vlan 30
 name Faculty
vlan 99
 name Management

! Configure SVIs (IPv4 + IPv6)
interface vlan 10
 ip address 192.168.10.254 255.255.255.0
 ipv6 address 2001:db8:acad:10::1/64
 no shutdown

interface vlan 20
 ip address 192.168.20.254 255.255.255.0
 ipv6 address 2001:db8:acad:20::1/64
 no shutdown

interface vlan 30
 ip address 192.168.30.254 255.255.255.0
 ipv6 address 2001:db8:acad:30::1/64
 no shutdown

interface vlan 99
 ip address 192.168.99.254 255.255.255.0
 no shutdown

! Configure routed port
interface gigabitEthernet 0/2
 no switchport
 ip address 209.165.200.225 255.255.255.252
 ipv6 address 2001:db8:acad:a::1/64
 no shutdown

! Configure trunk
interface gigabitEthernet 0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
```

---

## Key Concepts

### Routed Port vs SVI

| Feature | Routed Port | SVI |
|---------|-------------|-----|
| **Command** | `no switchport` | `interface vlan X` |
| **Purpose** | Connect to router/WAN | Inter-VLAN routing |
| **Layer** | Layer 3 only | Layer 2 + Layer 3 |
| **IP Address** | Yes | Yes |
| **VLAN** | No VLAN | Represents one VLAN |

### Layer 2 vs Layer 3 Switch Trunk

| Feature | Layer 2 Switch | Layer 3 Switch |
|---------|---------------|----------------|
| **Encapsulation** | Not required | `switchport trunk encapsulation dot1q` |
| **Mode** | `switchport mode trunk` | `switchport mode trunk` |
| **Order** | N/A | Encapsulation FIRST, then mode |

---

## Verification Commands

### Check Routing

```cisco
! IPv4
show ip route
show ip interface brief
show running-config | include ip routing

! IPv6
show ipv6 route
show ipv6 interface brief
show running-config | include ipv6 unicast-routing
```

### Check SVIs

```cisco
show ip interface brief | include Vlan
show interface vlan 10
show interfaces status
```

### Check Trunk

```cisco
show interface trunk
show interface g0/1 switchport
```

### Check VLANs

```cisco
show vlan brief
show vlan id 10
```

### Check Routed Port

```cisco
show ip interface brief | include Gig
show interface g0/2
show interface g0/2 switchport
```

If routed port configured correctly:
```
GigabitEthernet0/2 is not a switchport
```

---

## Common Issues and Solutions

### Issue 1: Inter-VLAN Routing Not Working

**Symptom:** Devices can ping gateway but not other VLANs

**Check:**
```cisco
MLS# show running-config | include ip routing
```

**Solution:**
```cisco
MLS(config)# ip routing
```

**For IPv6:**
```cisco
MLS(config)# ipv6 unicast-routing
```

---

### Issue 2: SVI Down

**Symptom:** SVI shows down/down

**Check:**
```cisco
MLS# show ip interface brief
Vlan10  192.168.10.254  YES manual down  down
                                    ↑
```

**Common Causes:**
1. SVI shutdown
2. No active ports in VLAN
3. VLAN doesn't exist

**Solutions:**
```cisco
! Check if shutdown
MLS# show running-config interface vlan 10
interface Vlan10
 ip address 192.168.10.254 255.255.255.0
 shutdown    ← Problem!

! Fix:
MLS(config)# interface vlan 10
MLS(config-if)# no shutdown

! Check if VLAN exists:
MLS# show vlan brief

! Check if ports in VLAN:
MLS# show vlan id 10
VLAN Name      Status    Ports
10   Staff     active    Fa0/1, Fa0/2
```

---

### Issue 3: Routed Port Not Working

**Symptom:** Cannot ping through G0/2

**Check:**
```cisco
MLS# show interface g0/2 switchport
GigabitEthernet0/2 is not a switchport
                   ↑
              Should say "not a switchport"
```

**If it says "switchport":**
```cisco
MLS(config)# interface g0/2
MLS(config-if)# no switchport
MLS(config-if)# ip address 209.165.200.225 255.255.255.252
```

---

### Issue 4: Trunk Not Working on MLS

**Symptom:** VLANs not passing through trunk

**Check encapsulation:**
```cisco
MLS# show interface g0/1 switchport
Administrative Trunking Encapsulation: negotiate
                                       ↑
                                  Should be "dot1q"
```

**Fix (must do encapsulation FIRST):**
```cisco
MLS(config)# interface g0/1
MLS(config-if)# switchport trunk encapsulation dot1q
MLS(config-if)# switchport mode trunk
```

---

## Lab Checklist

**Part 1:**
- [ ] Configure G0/2 as routed port (`no switchport`)
- [ ] Assign IP address to G0/2
- [ ] Verify ping to 209.165.200.226

**Part 2 (IPv4):**
- [ ] Create VLANs (10, 20, 30, 99)
- [ ] Configure SVIs with IP addresses
- [ ] Configure trunk on MLS (encapsulation FIRST!)
- [ ] Configure trunk on S1
- [ ] Enable `ip routing`
- [ ] Verify routing table
- [ ] Test connectivity

**Part 3 (IPv6):**
- [ ] Enable `ipv6 unicast-routing`
- [ ] Configure IPv6 on SVIs
- [ ] Configure IPv6 on G0/2
- [ ] Verify IPv6 routing table
- [ ] Test IPv6 connectivity

---

## Key Commands to Remember

```cisco
! Enable routing
ip routing
ipv6 unicast-routing

! Routed port
no switchport

! Trunk on Layer 3 switch
switchport trunk encapsulation dot1q
switchport mode trunk

! SVI
interface vlan <number>
ip address <ip> <mask>
ipv6 address <ipv6>/<prefix>
no shutdown
```

---

## CCNA Exam Tips

**Know:**
- `ip routing` command is REQUIRED for Inter-VLAN routing
- Layer 3 switch trunk needs encapsulation command FIRST
- `no switchport` converts to routed port
- SVIs need `no shutdown`
- SVIs need at least one active port in VLAN

**Common Mistakes:**
- Forgetting `ip routing`
- Wrong order on trunk config (encapsulation must be first)
- Forgetting `no shutdown` on SVIs
- Not creating VLAN before SVI

---

**End of Layer 3 Switching and Inter-VLAN Routing Lab Guide**