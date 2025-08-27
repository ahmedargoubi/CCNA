# Network Address Translation (NAT) - CCNA Complete Guide

## Table of Contents
1. [IPv4 Address Shortage and Private Addresses](#ipv4-address-shortage-and-private-addresses)
2. [Introduction to NAT](#introduction-to-nat)
3. [NAT Terminology](#nat-terminology)
4. [Static NAT](#static-nat)
5. [Dynamic NAT](#dynamic-nat)
6. [PAT (NAT Overload)](#pat-nat-overload)
7. [Configuration Examples](#configuration-examples)
8. [Verification Commands](#verification-commands)
9. [Troubleshooting NAT](#troubleshooting-nat)
10. [Key Exam Points](#key-exam-points)

---

## IPv4 Address Shortage and Private Addresses

### The Problem
- **IPv4 doesn't provide enough addresses** for all devices in the modern world
- Only **4.3 billion** IPv4 addresses available globally
- **Exponential growth** in internet-connected devices

### Short-term Solutions
1. **CIDR** (Classless Inter-Domain Routing) - More efficient address allocation
2. **Private IPv4 Addresses** (RFC 1918) - Reusable internal addresses
3. **NAT** (Network Address Translation) - Allow private addresses to access internet

### Private IPv4 Address Ranges (RFC 1918)

| Class | Network | Subnet Mask | CIDR | Address Range | Total Hosts |
|-------|---------|-------------|------|---------------|-------------|
| **Class A** | 10.0.0.0 | 255.0.0.0 | /8 | 10.0.0.0 - 10.255.255.255 | 16,777,216 |
| **Class B** | 172.16.0.0 | 255.240.0.0 | /12 | 172.16.0.0 - 172.31.255.255 | 1,048,576 |
| **Class C** | 192.168.0.0 | 255.255.0.0 | /16 | 192.168.0.0 - 192.168.255.255 | 65,536 |

### Key Points
- **Private addresses are NOT routable** on the internet
- **Can be reused** in different networks without conflict
- **Must be translated** to public addresses to access internet
- **Free to use** - no registration required

---

## Introduction to NAT

### What is NAT?
**Network Address Translation (NAT)** is used to modify the **source and/or destination IP addresses** of packets.

### Primary Purpose
- **Allow hosts with private IP addresses** to communicate over the internet
- **Conserve public IPv4 addresses** by sharing them among multiple private hosts
- **Provide security** by hiding internal network structure

### Types of NAT
1. **Static NAT** - One-to-one mapping (permanent)
2. **Dynamic NAT** - One-to-one mapping (temporary)
3. **PAT (NAT Overload)** - Many-to-one mapping using ports

---

## NAT Terminology

### Critical Terms to Memorize

| Term | Definition | Location |
|------|------------|----------|
| **Inside Local** | Private IP address of internal host | Internal network |
| **Inside Global** | Public IP address representing internal host | External network |
| **Outside Local** | Public IP address of external host (as seen internally) | Internal view |
| **Outside Global** | Actual public IP address of external host | External network |

### Interface Classifications
- **Inside Interface** - Connected to internal/private network
- **Outside Interface** - Connected to external/public network (usually internet)

### Visual Example
```
Internal Host (192.168.1.10) → Router → Internet Host (8.8.8.8)
    Inside Local         Inside Global    Outside Global
   (192.168.1.10)       (203.0.113.1)      (8.8.8.8)
```

---

## Static NAT

### Characteristics
- **One-to-one mapping** between inside local and inside global addresses
- **Permanently configured** mappings
- **Bidirectional** - external hosts can initiate connections to internal hosts
- **Always uses the same translation** for each internal host

### When to Use Static NAT
- **Servers** that need to be accessible from the internet
- **Devices requiring consistent public IP** addresses
- **Small networks** with sufficient public IP addresses

### Advantages
- **Simple configuration**
- **Predictable translations**
- **Allows inbound connections**
- **No port conflicts**

### Disadvantages
- **Requires one public IP per internal host**
- **Doesn't conserve public IP addresses effectively**
- **More expensive** (requires multiple public IPs)

---

## Dynamic NAT

### Characteristics
- **Temporary one-to-one mappings** created as needed
- **Uses ACL** to identify which traffic should be translated
- **Uses NAT pool** to define available public IP addresses
- **First-come, first-served** basis for address assignment

### How Dynamic NAT Works
1. **Internal host** sends packet to external destination
2. **Router checks ACL** - if permitted, translation occurs
3. **Router assigns** available public IP from NAT pool
4. **Translation entry** created in NAT table
5. **Return traffic** is automatically translated back

### NAT Pool Exhaustion
- **Occurs when all public IPs in pool are in use**
- **New translation requests are dropped**
- **Hosts cannot access external networks**
- **Entries timeout automatically** or can be cleared manually

### ACL Behavior
- **If source IP is PERMITTED** → Translation occurs
- **If source IP is DENIED** → No translation, but packet is NOT dropped
- **Traffic with denied IPs** still forwarded (if routing exists)

---

## PAT (NAT Overload)

### What is PAT?
**Port Address Translation (PAT)** or **NAT Overload** translates **both IP addresses and port numbers**.

### How PAT Works
- **Multiple internal hosts** share single public IP address
- **Unique port numbers** distinguish different communication flows
- **Router maintains table** of translations with port mappings
- **Up to 65,000+ simultaneous connections** per public IP

### PAT vs Dynamic NAT

| Feature | Dynamic NAT | PAT |
|---------|-------------|-----|
| **Mapping** | One-to-one | Many-to-one |
| **Public IPs needed** | Many | Few (often just one) |
| **Port translation** | No | Yes |
| **Scalability** | Limited by public IPs | Very scalable |
| **Cost** | Higher | Lower |

### PAT Configuration Methods
1. **PAT with Pool** - Uses multiple public IPs with port translation
2. **PAT with Interface** - Uses router's outside interface IP

---

## Configuration Examples

### Static NAT Configuration

#### Basic Static NAT
```cisco
! Configure inside and outside interfaces
interface GigabitEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 ip nat inside
 
interface GigabitEthernet0/1
 ip address 203.0.113.1 255.255.255.0
 ip nat outside

! Configure static NAT mappings
ip nat inside source static 192.168.1.10 203.0.113.10
ip nat inside source static 192.168.1.20 203.0.113.20
ip nat inside source static 192.168.1.30 203.0.113.30
```

### Dynamic NAT Configuration

#### Step-by-Step Dynamic NAT
```cisco
! Step 1: Configure interfaces
interface GigabitEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 ip nat inside
 
interface GigabitEthernet0/1
 ip address 203.0.113.1 255.255.255.0
 ip nat outside

! Step 2: Create ACL to identify traffic
access-list 1 permit 192.168.1.0 0.0.0.255

! Step 3: Create NAT pool
ip nat pool PUBLIC_POOL 203.0.113.10 203.0.113.20 netmask 255.255.255.0

! Step 4: Configure dynamic NAT
ip nat inside source list 1 pool PUBLIC_POOL
```

### PAT Configuration

#### PAT with Pool
```cisco
! Configure interfaces
interface GigabitEthernet0/0
 ip nat inside
interface GigabitEthernet0/1
 ip nat outside

! Create ACL
access-list 1 permit 192.168.1.0 0.0.0.255

! Create NAT pool
ip nat pool PAT_POOL 203.0.113.10 203.0.113.15 netmask 255.255.255.0

! Configure PAT with overload
ip nat inside source list 1 pool PAT_POOL overload
```

#### PAT with Interface (Most Common)
```cisco
! Configure interfaces
interface GigabitEthernet0/0
 ip nat inside
interface GigabitEthernet0/1
 ip nat outside

! Create ACL
access-list 1 permit 192.168.1.0 0.0.0.255

! Configure PAT using outside interface
ip nat inside source list 1 interface GigabitEthernet0/1 overload
```

---

## Verification Commands

### Essential NAT Verification Commands

#### Show NAT Translations
```cisco
show ip nat translations
! Shows active translation table entries

show ip nat translations verbose
! Shows detailed translation information including timers
```

#### Show NAT Statistics
```cisco
show ip nat statistics
! Shows NAT statistics including:
! - Total active translations
! - Peak translations
! - Outside interfaces, Inside interfaces
! - Hits, Misses
! - Pool utilization
```

#### Show NAT Configuration
```cisco
show running-config | include nat
! Shows all NAT-related configuration

show ip nat pool
! Shows configured NAT pools and utilization
```

#### Clear NAT Translations
```cisco
clear ip nat translation *
! Clears all dynamic NAT translations

clear ip nat translation inside 192.168.1.10
! Clears specific translation

clear ip nat statistics
! Clears NAT statistics counters
```

### Sample Output Interpretation

#### NAT Translation Table
```
Router#show ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
icmp 203.0.113.10:1   192.168.1.10:1    8.8.8.8:1         8.8.8.8:1
tcp  203.0.113.10:80  192.168.1.20:80   1.1.1.1:12345     1.1.1.1:12345
```

#### NAT Statistics
```
Router#show ip nat statistics
Total active translations: 2 (1 static, 1 dynamic; 1 extended)
Peak translations: 5, occurred 00:02:15 ago
Outside interfaces:
  GigabitEthernet0/1
Inside interfaces:
  GigabitEthernet0/0
Hits: 157  Misses: 12
```

---

## Troubleshooting NAT

### Common NAT Problems

#### 1. Missing Interface Configuration
**Problem**: NAT not working at all
**Solution**: Verify `ip nat inside` and `ip nat outside` on correct interfaces

#### 2. Incorrect ACL
**Problem**: Some hosts not being translated
**Solution**: Check ACL permits correct source networks

#### 3. NAT Pool Exhaustion
**Problem**: New connections failing
**Solution**: 
- Add more IPs to pool
- Clear unused translations
- Consider using PAT

#### 4. Wrong Translation Direction
**Problem**: Return traffic not working
**Solution**: Verify inside/outside interface designation

### Troubleshooting Steps
1. **Verify interface configuration** (`ip nat inside/outside`)
2. **Check ACL permits traffic** (`show access-lists`)
3. **Verify NAT pool has available addresses** (`show ip nat pool`)
4. **Check translation table** (`show ip nat translations`)
5. **Review statistics for hits/misses** (`show ip nat statistics`)
6. **Test with debug** (`debug ip nat`)

---

## Key Exam Points

### Must-Know Concepts
1. **Private IP ranges** (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16)
2. **NAT terminology** (Inside Local, Inside Global, etc.)
3. **Static vs Dynamic vs PAT** differences
4. **Interface inside/outside** designation
5. **ACL permits = translate** (denies = no translation but no drop)

### Configuration Essentials
- **Interface designation** is mandatory
- **ACL identifies** what to translate
- **NAT pool defines** available public IPs
- **overload keyword** enables PAT
- **Static mappings** are bidirectional

### Command Memorization
- `ip nat inside source static [local] [global]`
- `ip nat inside source list [ACL] pool [pool] overload`
- `ip nat inside source list [ACL] interface [interface] overload`
- `show ip nat translations`
- `show ip nat statistics`
- `clear ip nat translation *`

### Exam Tips
1. **Always configure interfaces first** (inside/outside)
2. **Remember ACL logic** - permit = translate
3. **PAT is most common** in real world
4. **Static NAT allows inbound** connections
5. **Pool exhaustion** is a key Dynamic NAT limitation
6. **Port numbers enable** many-to-one mapping in PAT
7. **Verification commands** are heavily tested

### Common Exam Scenarios
- **Configure PAT** for internet access
- **Troubleshoot NAT** translation issues
- **Identify correct** inside/outside interfaces
- **Calculate required** public IP addresses
- **Understand NAT table** entries

---

## Quick Reference Card

### NAT Types Summary
| Type | Ratio | Use Case | Pros | Cons |
|------|-------|----------|------|------|
| **Static** | 1:1 | Servers | Simple, bidirectional | Requires many public IPs |
| **Dynamic** | 1:1 | General internet access | Automatic | Pool exhaustion |
| **PAT** | Many:1 | Most networks | IP conservation | Complex tracking |

### Configuration Templates

**Basic PAT (Most Common)**:
```cisco
interface g0/0
 ip nat inside
interface g0/1
 ip nat outside
access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 interface g0/1 overload
```

**Static NAT (Server)**:
```cisco
ip nat inside source static 192.168.1.100 203.0.113.100
```

Remember: **NAT is about solving IPv4 address shortage** and **enabling private networks to access the internet**!