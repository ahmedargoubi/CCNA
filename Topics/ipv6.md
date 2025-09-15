# Complete IPv6 Guide for CCNA

## Table of Contents
1. [Hexadecimal Review](#hexadecimal-review)
2. [Why IPv6?](#why-ipv6)
3. [IPv6 Basics](#ipv6-basics)
4. [IPv6 Address Types](#ipv6-address-types)
5. [IPv6 Address Configuration](#ipv6-address-configuration)
6. [IPv6 Header](#ipv6-header)
7. [Neighbor Discovery Protocol (NDP)](#neighbor-discovery-protocol-ndp)
8. [IPv6 Routing](#ipv6-routing)

---

## Hexadecimal Review

### Hexadecimal (Base 16)
- Uses digits 0-9 and letters A-F
- A=10, B=11, C=12, D=13, E=14, F=15
- Each hex digit represents 4 binary bits

### Conversion Examples
| Hex | Binary | Decimal |
|-----|--------|---------|
| 0   | 0000   | 0       |
| 8   | 1000   | 8       |
| A   | 1010   | 10      |
| F   | 1111   | 15      |

---

## Why IPv6?

### IPv4 Address Exhaustion
- **IPv4**: 2³² = 4,294,967,296 addresses
- **IPv6**: 2¹²⁸ = 340,282,366,920,938,463,463,374,607,431,768,211,456 addresses
- IANA declared IPv4 exhaustion on September 24, 2015
- IPv6 is the long-term solution to address exhaustion

### Short-term Solutions (IPv4)
- VLSM (Variable Length Subnet Masking)
- Private IP addresses
- NAT/PAT
- These are temporary fixes, not permanent solutions

---

## IPv6 Basics

### IPv6 Address Structure
- **128 bits total** (compared to IPv4's 32 bits)
- Written as **8 groups** of **4 hexadecimal digits**
- Groups separated by **colons (:)**
- Uses **slash notation** for prefix length

**Example:**
```
2001:0DB8:0000:0001:0000:0000:0000:0001/64
```

### IPv6 Address Abbreviation Rules

#### 1. Remove Leading Zeros
```
2001:0DB8:0000:0001:0000:0000:0000:0001
Becomes: 2001:DB8:0:1:0:0:0:1
```

#### 2. Use Double Colon (::) for Consecutive Zeros
- Can only be used **ONCE** per address
- Replaces the **longest** string of consecutive zero groups
- If equal lengths exist, use :: for the **leftmost** group

```
2001:DB8:0000:0000:0000:0000:0000:0001
Becomes: 2001:DB8::1
```

#### 3. Use Lowercase Letters
```
2001:DB8:ABCD:EF12::1
Should be: 2001:db8:abcd:ef12::1
```

### Expanding IPv6 Addresses

To expand a shortened address:
1. Replace :: with appropriate number of zero groups
2. Add leading zeros to make each group 4 digits

**Example:**
```
2001:DB8::1
Expands to: 2001:0DB8:0000:0000:0000:0000:0000:0001
```

### Finding IPv6 Prefix

#### Typical IPv6 Allocation
- **ISP to Enterprise**: /48 block
- **Subnet length**: /64
- **Available for subnetting**: 16 bits (48 to 64)
- **Host portion**: 64 bits

```
2001:DB8:1234:ABCD:1111:2222:3333:4444/64
         |      |         |
    Global    Subnet    Host
   Prefix   (16 bits) (64 bits)
   (48 bits)
```

---

## IPv6 Address Types

### 1. Global Unicast Addresses
- **Public** IPv6 addresses
- **Globally unique** and routable on the Internet
- Originally defined as 2000::/3 block
- Now: all addresses **not reserved** for other purposes

**Structure:**
```
| Global Routing Prefix | Subnet ID | Interface ID |
|      48 bits          | 16 bits   |   64 bits    |
```

### 2. Unique Local Addresses (ULA)
- **Private** IPv6 addresses (like IPv4 private addresses)
- **Cannot** be routed over the Internet
- Use address block **FC00::/7**
- **Must** use FD as first two digits (8th bit set to 1)

**Structure:**
```
| Prefix | L | Global ID | Subnet ID | Interface ID |
| FC00   | 1 | 40 bits   | 16 bits   |   64 bits    |
```

**Example:**
```
FD00:1234:5678:0001::1/64
```

### 3. Link-Local Addresses
- **Automatically generated** on IPv6-enabled interfaces
- Used for **same subnet communication only**
- **Not routed** between subnets
- Address block: **FE80::/10**
- Always begins with **FE8**

**Structure:**
```
FE80:0000:0000:0000:Interface-ID/64
Abbreviated: FE80::Interface-ID/64
```

**Common Uses:**
- Routing protocol neighbor relationships
- Next-hop addresses for static routes
- Neighbor Discovery Protocol (NDP)

### 4. Multicast Addresses
- **One-to-many** communication
- Address range: **FF00::/8**
- **No broadcast** in IPv6 (multicast replaces broadcast)

#### Important Multicast Addresses
| Address | Description |
|---------|-------------|
| FF02::1 | All nodes (replaces IPv4 broadcast) |
| FF02::2 | All routers |
| FF02::5 | All OSPF routers |
| FF02::6 | All OSPF designated routers |
| FF02::9 | All RIP routers |
| FF02::A | All EIGRP routers |

#### Multicast Scopes
| Scope | Address | Description |
|-------|---------|-------------|
| Interface-local | FF01 | Doesn't leave the device |
| Link-local | FF02 | Stays in local subnet |
| Site-local | FF05 | Single physical location |
| Organization-local | FF08 | Entire organization |
| Global | FF0E | No boundaries |

### 5. Anycast Addresses
- **One-to-nearest** communication
- Multiple devices configured with **same address**
- Traffic routed to **nearest** device (by routing metric)
- No specific address range (uses unicast addresses)

**Configuration:**
```
R1(config-if)# ipv6 address 2001:db8:1:1::99/128 anycast
```

### 6. Special Addresses
- **:: (unspecified)** - equivalent to 0.0.0.0
- **::1 (loopback)** - equivalent to 127.0.0.1
- **::/0** - default route (equivalent to 0.0.0.0/0)

---

## IPv6 Address Configuration

### Basic Configuration Commands
```bash
# Enable IPv6 routing globally
Router(config)# ipv6 unicast-routing

# Configure IPv6 address on interface
Router(config)# interface g0/0
Router(config-if)# ipv6 address 2001:db8::1/64
Router(config-if)# no shutdown

# Enable IPv6 on interface (generates link-local)
Router(config-if)# ipv6 enable
```

### EUI-64 Configuration
EUI-64 converts 48-bit MAC address to 64-bit interface identifier:

1. **Split MAC**: AA:BB:CC:DD:EE:FF → AA:BB:CC | DD:EE:FF
2. **Insert FFFE**: AA:BB:CC:FF:FE:DD:EE:FF
3. **Flip 7th bit**: AA becomes A8 (if 7th bit was 0)

**Configuration:**
```bash
Router(config-if)# ipv6 address 2001:db8:1:1::/64 eui-64
```

### SLAAC (Stateless Address Auto-configuration)
```bash
Router(config-if)# ipv6 address autoconfig
```
- Device learns prefix via Router Advertisement
- Automatically generates interface ID

---

## IPv6 Header

### IPv6 Header Structure
- **Fixed length**: 40 bytes
- **Simpler** than IPv4 header

| Field | Size | Description |
|-------|------|-------------|
| Version | 4 bits | Always 6 |
| Traffic Class | 8 bits | QoS marking |
| Flow Label | 20 bits | Traffic flow identification |
| Payload Length | 16 bits | Payload size in bytes |
| Next Header | 8 bits | Next protocol (TCP/UDP) |
| Hop Limit | 8 bits | Like IPv4 TTL |
| Source Address | 128 bits | Source IPv6 address |
| Destination Address | 128 bits | Destination IPv6 address |

---

## Neighbor Discovery Protocol (NDP)

### NDP Functions
- **Replaces ARP** in IPv6
- Uses **ICMPv6** messages
- Uses **multicast** instead of broadcast

### NDP Message Types

#### 1. Neighbor Solicitation (NS)
- **ICMPv6 Type 135**
- Like ARP request
- Sent to **solicited-node multicast address**

#### 2. Neighbor Advertisement (NA)
- **ICMPv6 Type 136**
- Like ARP reply
- Response to NS message

#### 3. Router Solicitation (RS)
- **ICMPv6 Type 133**
- Sent to **FF02::2** (all routers)
- Requests router information

#### 4. Router Advertisement (RA)
- **ICMPv6 Type 134**
- Sent to **FF02::1** (all nodes)
- Announces router presence and network information

### Solicited-Node Multicast Address
Generated from unicast address:
```
FF02::1:FF + last 24 bits of unicast address

Example:
Unicast: 2001:db8::1234:5678
Solicited-node: FF02::1:FF34:5678
```

### Duplicate Address Detection (DAD)
- Checks if address is already in use
- Sends NS to its own address
- If no response → address is unique
- If response received → address conflict

---

## IPv6 Routing

### Enable IPv6 Routing
```bash
Router(config)# ipv6 unicast-routing
```
- IPv4 routing: **enabled by default**
- IPv6 routing: **disabled by default**

### IPv6 Static Routes

#### 1. Recursive Static Route
```bash
Router(config)# ipv6 route 2001:db8:2::/64 2001:db8:1::2
```

#### 2. Directly Attached Static Route
```bash
Router(config)# ipv6 route 2001:db8:2::/64 g0/0
```
**Note**: Cannot use directly attached routes on Ethernet interfaces

#### 3. Fully Specified Static Route
```bash
Router(config)# ipv6 route 2001:db8:2::/64 g0/0 2001:db8:1::2
```

#### 4. Default Route
```bash
Router(config)# ipv6 route ::/0 2001:db8:1::1
```

#### 5. Link-Local Next Hop
```bash
Router(config)# ipv6 route 2001:db8:2::/64 g0/0 fe80::1
```
**Must** specify exit interface with link-local next hop

### IPv6 Route Types in Routing Table
- **C** - Connected routes (automatically added)
- **L** - Local routes (interface addresses)
- **S** - Static routes
- Link-local addresses **not** added to routing table

---

## Key CCNA IPv6 Commands

### Configuration Commands
```bash
ipv6 unicast-routing                    # Enable IPv6 routing
ipv6 address ADDRESS/PREFIX             # Configure IPv6 address
ipv6 address PREFIX/LENGTH eui-64       # Configure with EUI-64
ipv6 address autoconfig                 # Enable SLAAC
ipv6 enable                            # Enable IPv6 (generates link-local)
ipv6 route DEST/PREFIX NEXT-HOP        # IPv6 static route
```

### Verification Commands
```bash
show ipv6 interface brief              # Show IPv6 interfaces
show ipv6 interface INTERFACE          # Detailed interface info
show ipv6 route                        # IPv6 routing table
show ipv6 neighbors                    # IPv6 neighbor table
ping ipv6 ADDRESS                      # IPv6 ping
traceroute ipv6 ADDRESS               # IPv6 traceroute
```

---

## Summary for CCNA

### Must Know:
1. **Address types**: Global unicast, unique local, link-local, multicast
2. **Address abbreviation** rules and expansion
3. **EUI-64** process and configuration
4. **NDP** message types and functions
5. **Static routing** configuration
6. **Basic configuration** commands
7. **Verification** commands

### Key Differences from IPv4:
- **No broadcast** (uses multicast)
- **NDP replaces ARP**
- **Larger address space** (128 vs 32 bits)
- **Simplified header**
- **Auto-configuration** capabilities
- **Built-in security** features
