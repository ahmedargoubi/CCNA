
# IPv6 – CCNA Complete Study Guide

This guide covers all the IPv6 topics you need for the **CCNA Exam**.  
It includes theory, configuration, and exam-focus points.

---

## 1. Why IPv6?
- IPv4 exhaustion: **2^32 addresses (~4.3 billion)** not enough.
- Short-term fixes: **VLSM, Private IP, NAT**.
- Long-term solution: **IPv6 (128-bit addresses)**.
- IPv4 address distribution: IANA → RIRs → ISPs → Companies.
- IPv6 provides **virtually unlimited addresses** (2^128).

---

## 2. IPv6 Address Basics
- IPv6 address = **128 bits** (8 groups × 16 bits).
- Written in **hexadecimal**, separated by `:`.
- Uses **prefix length** (e.g., `/64` instead of subnet mask).

### Example:
```
2001:0db8:85a3:0000:0000:8a2e:0370:7334/64
```

---

## 3. IPv6 Address Shortening Rules
1. **Remove leading zeros** in each 16-bit block:
   - `2001:0db8:0000:0001::1` → `2001:db8:0:1::1`
2. **Replace consecutive all-zero blocks with `::` (only once)**:
   - `2001:0db8:0000:0000:0000:0000:0000:0001` → `2001:db8::1`
3. RFC rules:
   - Always use lowercase hex (`a–f`).
   - If two equal choices for `::`, compress the **leftmost**.

---

## 4. IPv6 Address Expansion
Expand by filling missing groups with zeros:
```
2001:db8::1 → 2001:0db8:0000:0000:0000:0000:0000:0001
```

---

## 5. IPv6 Prefixes
- Enterprise usually gets a **/48** block.
- Subnets typically use **/64** (16 bits for subnetting).
- Host portion = last 64 bits.

---

## 6. IPv6 Address Types

### a) **Global Unicast**
- Equivalent to IPv4 Public addresses.
- Routable on the internet.
- Range: **2000::/3** (2000 – 3fff).

### b) **Unique Local (ULA)**
- Equivalent to IPv4 Private addresses.
- Range: **fc00::/7**, usually **fd00::/8**.
- Used internally, not routable on the internet.

### c) **Link-Local**
- Automatically assigned to every IPv6-enabled interface.
- Range: **fe80::/10**.
- Required for IPv6 communication on a link.
- Used for **next-hop routing, NDP, OSPFv3 adjacencies**.

### d) **Multicast**
- Replaces IPv4 broadcast (no broadcast in IPv6).
- Range: **ff00::/8**.
- Types:
  - **ff02::1** → All nodes (link-local).
  - **ff02::2** → All routers (link-local).
  - **ff02::5** → OSPFv3 routers.
  - **ff02::9** → RIPng routers.

### e) **Anycast**
- One-to-one-of-many.
- Same IP configured on multiple routers.
- Packets go to **nearest** router.

### f) **Special Addresses**
- `::` → Unspecified (IPv4 = 0.0.0.0).
- `::1` → Loopback (IPv4 = 127.0.0.1).
- `::/0` → Default route.

---

## 7. EUI-64 Addressing
- Converts **48-bit MAC** → **64-bit interface ID**.
- Steps:
  1. Split MAC into two halves.
  2. Insert **FFFE** in the middle.
  3. Flip the **7th bit** of the first byte.

### Example:
```
MAC: 78-2B-CB-AC-08-67
EUI-64: 7A2B:CBFF:FEAC:0867
```

---

## 8. IPv6 Neighbor Discovery Protocol (NDP)
- Replaces ARP in IPv6.
- Uses **ICMPv6** + multicast.

### Messages:
- **NS (Neighbor Solicitation)** → ICMPv6 type 135.
- **NA (Neighbor Advertisement)** → ICMPv6 type 136.
- **RS (Router Solicitation)** → ICMPv6 type 133.
- **RA (Router Advertisement)** → ICMPv6 type 134.

### Functions:
- Address resolution (like ARP).
- Router discovery (find IPv6 routers).
- SLAAC (automatic address configuration).
- DAD (Duplicate Address Detection).

---

## 9. SLAAC (Stateless Address Autoconfiguration)
- Host learns prefix from **RA messages**.
- Host generates interface ID (EUI-64 or random).
- Command:  
  ```
  R1(config-if)# ipv6 address autoconfig
  ```

---

## 10. IPv6 Routing

### Enable IPv6 Routing:
```
R1(config)# ipv6 unicast-routing
```

### Configure IPv6 on Interface:
```
R1(config)# int g0/0
R1(config-if)# ipv6 address 2001:db8:0:0::1/64
R1(config-if)# no shutdown
```

### Static Routing:

**Recursive Static Route:**
```
R1(config)# ipv6 route 2001:db8:0:3::/64 2001:db8:0:12::2
```

**Fully Specified Static Route:**
```
R1(config)# ipv6 route 2001:db8:0:3::/64 g0/0 2001:db8:0:12::2
```

**Default Route:**
```
R1(config)# ipv6 route ::/0 2001:db8:0:12::2
```

**Host Route:**
```
R1(config)# ipv6 route 2001:db8:0:1::100/128 2001:db8:0:12::2
```

---

## 11. IPv6 Header
- Fixed size: **40 bytes**.
- Fields:
  - **Version (4 bits)** → always `6`.
  - **Traffic Class (8 bits)** → QoS marking.
  - **Flow Label (20 bits)** → identify flow of packets.
  - **Payload Length (16 bits)** → length of L4 segment.
  - **Next Header (8 bits)** → TCP/UDP/etc.
  - **Hop Limit (8 bits)** → like IPv4 TTL.
  - **Source Address (128 bits)**.
  - **Destination Address (128 bits)**.

---

## 12. Key Exam Tips
- No broadcast in IPv6 (use multicast instead).
- Link-local required for IPv6 routing (next-hop must include interface).
- Always know **ff02::1 (all nodes)** and **ff02::2 (all routers)**.
- Remember address ranges:  
  - Global Unicast → 2000::/3  
  - ULA → fc00::/7 (fd00::/8)  
  - Link-Local → fe80::/10  
  - Multicast → ff00::/8  
- Static routes in Ethernet need **next-hop + interface**.
- Always enable `ipv6 unicast-routing` on routers.

---

# ✅ End of IPv6 CCNA Study Guide
