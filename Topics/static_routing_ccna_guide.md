# Static Routing - CCNA Complete Guide

## Table of Contents
1. [Types of Static Routes](#types-of-static-routes)
2. [Next-Hop Options](#next-hop-options)
3. [IPv4 Static Route Configuration](#ipv4-static-route-configuration)
4. [IPv6 Static Route Configuration](#ipv6-static-route-configuration)
5. [Default Static Routes](#default-static-routes)
6. [Floating Static Routes](#floating-static-routes)
7. [Static Host Routes](#static-host-routes)
8. [Quick Reference](#quick-reference)

---

## Types of Static Routes

### What is a Static Route?

**Static Route**: A manually configured route that tells the router how to forward packets to a specific destination network.

**Why use Static Routes?**
- Simple networks
- Stub networks (only one way out)
- Default routes to ISP
- Backup routes (floating static)
- More secure (no routing protocol overhead)
- Predictable path selection

---

### Four Types of Static Routes

#### 1. Standard Static Route

**Definition:** Route to a specific remote network

**When to use:**
- Routing to a specific destination network
- Most common type of static route

**Example:**
```
Network: 172.16.1.0/24 is accessible via next-hop
```

---

#### 2. Default Static Route

**Definition:** Route that matches ALL destinations (0.0.0.0/0 or ::/0)

**When to use:**
- Connection to ISP
- Stub networks (only one exit)
- Last resort route when no specific match

**Example:**
```
"If you don't know where to send it, send it here!"
Used for Internet connectivity
```

---

#### 3. Floating Static Route

**Definition:** Backup static route with higher administrative distance

**When to use:**
- Backup path for primary route (static or dynamic)
- Activated only if primary route fails
- Load balancing (with equal AD)

**Example:**
```
Primary route: AD = 1 (active)
Floating route: AD = 5 (backup, inactive until primary fails)
```

---

#### 4. Summary Static Route

**Definition:** Single route representing multiple networks

**When to use:**
- Reduce routing table size
- Summarize multiple networks into one route
- Improves router performance

**Example:**
```
Instead of:
- 172.16.1.0/24
- 172.16.2.0/24
- 172.16.3.0/24

Use summary: 172.16.0.0/22 (covers all three)
```

---

## Next-Hop Options

### Three Ways to Specify Next-Hop

When configuring a static route, you can specify the destination in three ways:

---

### 1. Next-Hop Route (Recursive)

**Only next-hop IP address specified**

**Characteristics:**
- Router must perform recursive lookup
- Finds exit interface by looking up next-hop IP
- Additional routing table lookup required

**IPv4 Example:**
```cisco
ip route 172.16.1.0 255.255.255.0 10.0.0.2
         └─destination─┘ └─mask──┘ └next-hop IP┘
```

**IPv6 Example:**
```cisco
ipv6 route 2001:db8:1::/64 2001:db8:acad::2
```

**Advantages:**
- Works with broadcast networks (Ethernet)
- Flexible (works if exit interface changes)

**Disadvantages:**
- Recursive lookup takes processing time
- Less efficient than directly connected

**When to use:** Broadcast networks (Ethernet, Wi-Fi)

---

### 2. Directly Connected Static Route

**Only exit interface specified**

**Characteristics:**
- No next-hop IP, only interface
- Router sends packets out specified interface
- Treats destination as directly connected

**IPv4 Example:**
```cisco
ip route 172.16.1.0 255.255.255.0 Serial0/0/0
         └─destination─┘ └─mask──┘ └exit interface┘
```

**IPv6 Example:**
```cisco
ipv6 route 2001:db8:1::/64 Serial0/0/0
```

**Advantages:**
- No recursive lookup needed
- Faster processing
- Simple configuration

**Disadvantages:**
- Only works with point-to-point links
- On Ethernet, creates proxy ARP issues

**When to use:** Point-to-point connections (Serial, PPP)

---

### 3. Fully Specified Static Route

**Both exit interface AND next-hop IP specified**

**Characteristics:**
- Most specific configuration
- No recursive lookup needed
- Router knows exactly where to send packets

**IPv4 Example:**
```cisco
ip route 172.16.1.0 255.255.255.0 GigabitEthernet0/0 10.0.0.2
         └─destination─┘ └─mask──┘ └─exit interface─┘ └next-hop IP┘
```

**IPv6 Example:**
```cisco
ipv6 route 2001:db8:1::/64 GigabitEthernet0/0 2001:db8:acad::2
```

**Advantages:**
- Most reliable
- Works with all network types
- No ambiguity
- Required for link-local IPv6 next-hops

**Disadvantages:**
- More typing
- Must update both if topology changes

**When to use:**
- Best practice for all configurations
- Required for IPv6 with link-local addresses
- Ethernet networks
- Any production environment

---

## IPv4 Static Route Configuration

### Command Syntax

```cisco
Router(config)# ip route network-address subnet-mask {ip-address | exit-intf [ip-address]} [distance]
```

### Parameters Explained

| Parameter | Description |
|-----------|-------------|
| **network-address** | Destination network IP address |
| **subnet-mask** | Subnet mask of destination network |
| **ip-address** | Next-hop router IP address |
| **exit-intf** | Exit interface to use |
| **exit-intf ip-address** | Both exit interface and next-hop (fully specified) |
| **distance** | Administrative distance (1-255), default is 1 |

---

### IPv4 Configuration Examples

#### Example 1: Next-Hop Route (Recursive)

**Scenario:** Route to 172.16.1.0/24 via next-hop 10.0.0.2

```cisco
Router(config)# ip route 172.16.1.0 255.255.255.0 10.0.0.2
```

**Verification:**
```cisco
Router# show ip route
S    172.16.1.0/24 [1/0] via 10.0.0.2
```

---

#### Example 2: Directly Connected Route

**Scenario:** Route to 192.168.2.0/24 out Serial0/0/0

```cisco
Router(config)# ip route 192.168.2.0 255.255.255.0 Serial0/0/0
```

**Verification:**
```cisco
Router# show ip route
S    192.168.2.0/24 is directly connected, Serial0/0/0
```

---

#### Example 3: Fully Specified Route (Best Practice)

**Scenario:** Route to 10.1.1.0/24 via Gi0/0 and next-hop 192.168.1.2

```cisco
Router(config)# ip route 10.1.1.0 255.255.255.0 GigabitEthernet0/0 192.168.1.2
```

**Verification:**
```cisco
Router# show ip route
S    10.1.1.0/24 [1/0] via 192.168.1.2, GigabitEthernet0/0
```

---

#### Example 4: Default Static Route

**Scenario:** Send all unknown traffic to ISP at 203.0.113.1

```cisco
Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

**Explanation:**
- **0.0.0.0** = Any network
- **0.0.0.0** = Any mask (matches everything!)
- Gateway of last resort

**Verification:**
```cisco
Router# show ip route
Gateway of last resort is 203.0.113.1 to network 0.0.0.0
S*   0.0.0.0/0 [1/0] via 203.0.113.1
```

**The asterisk (*) indicates default route!**

---

#### Example 5: Floating Static Route

**Scenario:** Backup route with AD of 10 (primary has AD of 1)

```cisco
! Primary route (AD = 1, default)
Router(config)# ip route 172.16.1.0 255.255.255.0 10.0.0.2

! Floating backup route (AD = 10)
Router(config)# ip route 172.16.1.0 255.255.255.0 10.0.0.3 10
```

**How it works:**
- Both routes to same destination
- Lower AD = preferred (primary uses AD 1)
- Higher AD = backup (only used if primary fails)

---

#### Example 6: Summary Static Route

**Scenario:** Summarize 192.168.1.0/24 through 192.168.3.0/24

```cisco
! Instead of three routes:
! ip route 192.168.1.0 255.255.255.0 10.0.0.2
! ip route 192.168.2.0 255.255.255.0 10.0.0.2
! ip route 192.168.3.0 255.255.255.0 10.0.0.2

! Use one summary route:
Router(config)# ip route 192.168.0.0 255.255.252.0 10.0.0.2
```

**Benefits:**
- Fewer routing table entries
- Reduced memory usage
- Faster lookup

---

## IPv6 Static Route Configuration

### Command Syntax

```cisco
Router(config)# ipv6 route ipv6-prefix/prefix-length {ipv6-address | exit-intf [ipv6-address]} [distance]
```

### Parameters Explained

| Parameter | Description |
|-----------|-------------|
| **ipv6-prefix** | Destination IPv6 network address |
| **prefix-length** | Prefix length (like /64) |
| **ipv6-address** | Next-hop IPv6 address |
| **exit-intf** | Exit interface |
| **exit-intf ipv6-address** | Fully specified (required for link-local) |
| **distance** | Administrative distance (1-255) |

---

### IPv6 Configuration Examples

#### Example 1: Next-Hop IPv6 Route

**Scenario:** Route to 2001:db8:1::/64 via next-hop 2001:db8:acad::2

```cisco
Router(config)# ipv6 route 2001:db8:1::/64 2001:db8:acad::2
```

---

#### Example 2: Directly Connected IPv6 Route

**Scenario:** Route to 2001:db8:2::/64 out Gi0/0

```cisco
Router(config)# ipv6 route 2001:db8:2::/64 GigabitEthernet0/0
```

---

#### Example 3: Fully Specified IPv6 Route

**Scenario:** Route using link-local address (MUST be fully specified!)

```cisco
Router(config)# ipv6 route 2001:db8:3::/64 GigabitEthernet0/0 fe80::2
```

**Important:** Link-local addresses (fe80::/10) require exit interface!

---

#### Example 4: IPv6 Default Static Route

**Scenario:** Send all unknown IPv6 traffic to ISP

```cisco
Router(config)# ipv6 route ::/0 2001:db8:feed::1
```

**Explanation:**
- **::/0** = All IPv6 addresses (default route)
- Gateway of last resort for IPv6

---

#### Example 5: IPv6 Floating Static Route

**Scenario:** Backup IPv6 route with AD of 10

```cisco
! Primary route
Router(config)# ipv6 route 2001:db8:1::/64 2001:db8:acad::2

! Floating backup
Router(config)# ipv6 route 2001:db8:1::/64 2001:db8:acad::3 10
```

---

## Default Static Routes

### IPv4 Default Route

**Quad-zero route:**

```cisco
Router(config)# ip route 0.0.0.0 0.0.0.0 {next-hop | exit-intf}
```

**Common use cases:**
- Small office to ISP
- Stub networks
- Lab environments

**Example:**
```cisco
! To ISP
Router(config)# ip route 0.0.0.0 0.0.0.0 Serial0/0/0

! Or with next-hop
Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

---

### IPv6 Default Route

**Double-colon zero:**

```cisco
Router(config)# ipv6 route ::/0 {next-hop | exit-intf}
```

**Example:**
```cisco
! To ISP
Router(config)# ipv6 route ::/0 2001:db8:feed::1

! Or fully specified
Router(config)# ipv6 route ::/0 Serial0/0/0 fe80::1
```

---

## Floating Static Routes

### What is a Floating Static Route?

**Definition:** Backup static route that becomes active only when primary route fails.

**How it works:**
- Uses **administrative distance (AD)** to determine preference
- Lower AD = preferred route
- Higher AD = backup route (floats in routing table)

---

### Administrative Distance Values

| Route Source | Default AD |
|--------------|------------|
| Connected | 0 |
| **Static** | **1** |
| EIGRP Summary | 5 |
| eBGP | 20 |
| EIGRP | 90 |
| OSPF | 110 |
| IS-IS | 115 |
| RIP | 120 |
| iBGP | 200 |

**Lower AD = More trusted**

---

### Floating Static Route Configuration

#### IPv4 Floating Static Route

```cisco
! Primary static route (AD = 1, default)
Router(config)# ip route 172.16.1.0 255.255.255.0 10.0.0.2

! Floating backup (AD = 5, only if primary fails)
Router(config)# ip route 172.16.1.0 255.255.255.0 10.0.0.3 5
```

**Verification (primary active):**
```cisco
Router# show ip route
S    172.16.1.0/24 [1/0] via 10.0.0.2
```

**After primary fails:**
```cisco
Router# show ip route
S    172.16.1.0/24 [5/0] via 10.0.0.3
```

---

#### IPv6 Floating Static Route

```cisco
! Primary route
Router(config)# ipv6 route 2001:db8:1::/64 2001:db8:acad::2

! Floating backup (AD = 10)
Router(config)# ipv6 route 2001:db8:1::/64 2001:db8:acad::3 10
```

---

### Use Cases for Floating Static Routes

**1. Backup for dynamic routing:**
```cisco
! OSPF route (AD = 110)
! automatically learned

! Floating static backup (AD = 120)
Router(config)# ip route 192.168.1.0 255.255.255.0 10.0.0.5 120
```

**2. Dual ISP connections:**
```cisco
! Primary ISP (AD = 1)
Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1

! Backup ISP (AD = 10)
Router(config)# ip route 0.0.0.0 0.0.0.0 198.51.100.1 10
```

**3. MPLS with Internet backup:**
```cisco
! Primary MPLS (AD = 1)
Router(config)# ip route 10.0.0.0 255.0.0.0 172.16.1.1

! Backup Internet (AD = 5)
Router(config)# ip route 10.0.0.0 255.0.0.0 203.0.113.1 5
```

---

## Static Host Routes

### What is a Host Route?

**Definition:** Static route to a single host (not a network)

**Characteristics:**
- /32 subnet mask (IPv4) or /128 prefix (IPv6)
- Points to one specific IP address
- Most specific route possible

---

### IPv4 Host Route

**Syntax:**
```cisco
Router(config)# ip route host-ip 255.255.255.255 {next-hop | exit-intf}
```

**Example:**
```cisco
! Route to specific host 192.168.1.100
Router(config)# ip route 192.168.1.100 255.255.255.255 10.0.0.2
```

**Verification:**
```cisco
Router# show ip route
S    192.168.1.100/32 [1/0] via 10.0.0.2
                 └─ /32 indicates host route
```

---

### IPv6 Host Route

**Syntax:**
```cisco
Router(config)# ipv6 route host-ipv6/128 {next-hop | exit-intf}
```

**Example:**
```cisco
! Route to specific host
Router(config)# ipv6 route 2001:db8:1::100/128 2001:db8:acad::2
```

---

### Use Cases for Host Routes

**1. Management access:**
```cisco
! Route to specific server
ip route 192.168.1.50 255.255.255.255 10.0.0.2
```

**2. Load balancing:**
```cisco
! Send specific host traffic via different path
ip route 10.1.1.100 255.255.255.255 Serial0/0/0
```

**3. Testing/Troubleshooting:**
```cisco
! Temporarily route one host differently
ip route 172.16.1.10 255.255.255.255 192.168.1.254
```

---

## Verification Commands

### Essential Show Commands

```cisco
! Show routing table
show ip route
show ipv6 route

! Show only static routes
show ip route static
show ipv6 route static

! Show specific route
show ip route 172.16.1.0
show ipv6 route 2001:db8:1::

! Show brief routing info
show ip route summary
show ipv6 route summary

! Show static route configuration
show running-config | include ip route
show running-config | include ipv6 route
```

---

### Reading Routing Table Output

**IPv4 routing table:**
```
S    172.16.1.0/24 [1/0] via 10.0.0.2
│    └─network──┘  │ │      └next-hop┘
│                  │ └─metric
│                  └─AD (administrative distance)
└─S = Static route

Codes:
S - static
* - default route
```

**IPv6 routing table:**
```
S   2001:db8:1::/64 [1/0]
     via 2001:db8:acad::2, GigabitEthernet0/0
```

---

## Quick Reference

### Static Route Decision Table

| Scenario | Route Type | Command Example |
|----------|----------|-----------------|
| **Route to specific network** | Standard | `ip route 172.16.1.0 255.255.255.0 10.0.0.2` |
| **Route to Internet/unknown** | Default | `ip route 0.0.0.0 0.0.0.0 203.0.113.1` |
| **Backup route** | Floating | `ip route 172.16.1.0 255.255.255.0 10.0.0.3 10` |
| **Multiple networks** | Summary | `ip route 192.168.0.0 255.255.252.0 10.0.0.2` |
| **Single host** | Host | `ip route 192.168.1.100 255.255.255.255 10.0.0.2` |
| **Point-to-point link** | Directly connected | `ip route 172.16.1.0 255.255.255.0 Serial0/0/0` |
| **Best practice (any)** | Fully specified | `ip route 172.16.1.0 255.255.255.0 Gi0/0 10.0.0.2` |

---

### Administrative Distance Quick Reference

```
Connected:    0  (most trusted)
Static:       1  (default for static routes)
EIGRP:       90
OSPF:       110
RIP:        120
External:   170+

For floating static: Use AD > primary route's AD
```

---

### IPv4 vs IPv6 Command Comparison

| Task | IPv4 | IPv6 |
|------|------|------|
| **Standard route** | `ip route 172.16.1.0 255.255.255.0 10.0.0.2` | `ipv6 route 2001:db8:1::/64 2001:db8:acad::2` |
| **Default route** | `ip route 0.0.0.0 0.0.0.0 203.0.113.1` | `ipv6 route ::/0 2001:db8:feed::1` |
| **Fully specified** | `ip route ... Gi0/0 10.0.0.2` | `ipv6 route ... Gi0/0 fe80::2` |
| **Floating route** | `ip route ... 10.0.0.3 10` | `ipv6 route ... 2001:db8::3 10` |
| **Host route** | `/32 mask` | `/128 prefix` |

---

## Configuration Best Practices

✅ **Use fully specified routes** (exit-intf + next-hop) in production  
✅ **Document your static routes** with comments or descriptions  
✅ **Use consistent AD values** for floating routes  
✅ **Verify connectivity** after configuration  
✅ **Test failover** before production deployment  
✅ **Use summary routes** when possible to reduce table size  
✅ **Configure default routes** pointing to ISP  
✅ **Backup configurations** before making changes  

❌ **Avoid recursive lookups** (use fully specified instead)  
❌ **Don't use directly connected** on Ethernet (proxy ARP issues)  
❌ **Don't forget to enable IPv6** routing (`ipv6 unicast-routing`)  
❌ **Don't use same AD** for primary and backup routes  

---

## Remember This!

🔑 **Static route AD = 1** (default)  
🔑 **Lower AD = preferred route**  
🔑 **Floating static = backup route** (higher AD)  
🔑 **0.0.0.0/0 or ::/0 = default route**  
🔑 **Fully specified = best practice** (exit-intf + next-hop)  
🔑 **/32 or /128 = host route** (single device)  
🔑 **Summary routes** reduce routing table size  
🔑 **IPv6 link-local next-hops** require exit interface  
🔑 **Static routes don't change** automatically  
🔑 **Always verify** with show ip route