# First Hop Redundancy Protocols (FHRP) - CCNA Complete Guide

## Table of Contents
1. [What is FHRP and Why Do We Need It?](#what-is-fhrp-and-why-do-we-need-it)
2. [How FHRP Works](#how-fhrp-works)
3. [HSRP (Hot Standby Router Protocol)](#hsrp-hot-standby-router-protocol)
4. [VRRP (Virtual Router Redundancy Protocol)](#vrrp-virtual-router-redundancy-protocol)
5. [GLBP (Gateway Load Balancing Protocol)](#glbp-gateway-load-balancing-protocol)
6. [FHRP Comparison Chart](#fhrp-comparison-chart)
7. [Configuration Examples](#configuration-examples)
8. [Verification Commands](#verification-commands)
9. [Key Exam Points to Remember](#key-exam-points-to-remember)

---

## What is FHRP and Why Do We Need It?

### The Problem
- Hosts on a network are configured with a **single default gateway**
- If that default gateway router fails, hosts **lose connectivity** to external networks
- Hosts would need **manual reconfiguration** to use a backup router
- This creates a **single point of failure**

### The Solution: FHRP
**First Hop Redundancy Protocol (FHRP)** is a computer networking protocol designed to:
- **Protect the default gateway** used on a subnet
- Allow **two or more routers** to provide backup for that gateway address
- Provide **automatic failover** (usually within seconds)
- Maintain **transparent operation** for end hosts

---

## How FHRP Works

### Core Concepts
1. **Virtual IP Address (VIP)**: A shared IP address used by multiple routers
2. **Virtual MAC Address**: A shared MAC address associated with the VIP
3. **Active/Master Router**: The router currently forwarding traffic
4. **Standby/Backup Router**: The router ready to take over if needed

### Step-by-Step Operation

#### Normal Operation
1. **Two or more routers share a VIP** (Virtual IP Address)
2. **Hosts use the VIP as their default gateway**
3. **Routers communicate** using "Hello" messages
4. **One router becomes ACTIVE**, others become STANDBY
5. **Host sends ARP request** for the VIP MAC address
6. **Active router responds** with the virtual MAC address
7. **Traffic flows** through the active router

#### Failover Process
1. **Active router fails** (stops sending Hello messages)
2. **Standby router detects failure** (Hello timeout)
3. **Standby becomes active** (role transition)
4. **New active router sends Gratuitous ARP** to update switch MAC tables
5. **Traffic seamlessly flows** through the new active router

### Important Notes
- **Non-preemptive by default**: Original active router becomes standby when it returns
- **Preemption can be enabled**: Forces original router to reclaim active role
- **Gratuitous ARP**: ARP reply sent without being requested (broadcast to FFFF.FFFF.FFFF)

---

## HSRP (Hot Standby Router Protocol)

### Key Characteristics
- **Cisco Proprietary** protocol
- Uses **Active** and **Standby** terminology
- Two versions available (v1 and v2)

### HSRP Versions

| Feature | Version 1 | Version 2 |
|---------|-----------|-----------|
| IPv6 Support | No | Yes |
| Max Groups | 255 | 4095 |
| Multicast Address | 224.0.0.2 | 224.0.0.102 |
| Virtual MAC | 0000.0c07.acXX | 0000.0c9f.fXXX |

**XX = HSRP Group Number (in hex for v2)**

### HSRP States
1. **Initial**: Starting state
2. **Learn**: Waiting to hear from active router
3. **Listen**: Knows VIP, not active or standby
4. **Speak**: Participating in election
5. **Standby**: Backup router
6. **Active**: Forwarding traffic

### HSRP Timers
- **Hello Timer**: 3 seconds (default)
- **Hold Timer**: 10 seconds (default)
- **Preempt Delay**: 0 seconds (default)

---

## VRRP (Virtual Router Redundancy Protocol)

### Key Characteristics
- **Open Standard** (RFC 3768)
- Uses **Master** and **Backup** terminology
- **Single version** (VRRPv3 supports IPv6)

### VRRP Specifications
- **Multicast Address**: 224.0.0.18
- **Virtual MAC**: 0000.5e00.01XX (XX = VRRP Group Number)
- **Group Numbers**: 1-255

### VRRP MAC Address Conversion
For group numbers > 99, convert to hexadecimal:
- Group 200 = C8 in hex
- Virtual MAC = 0000.5e00.01c8

### VRRP States
1. **Initialize**: Starting state
2. **Backup**: Standby router
3. **Master**: Active router

### VRRP Timers
- **Advertisement Interval**: 1 second (default)
- **Master Down Interval**: 3 × Advertisement Interval + Skew Time

---

## GLBP (Gateway Load Balancing Protocol)

### Key Characteristics
- **Cisco Proprietary** protocol
- **Load balances** traffic among multiple routers
- More complex than HSRP/VRRP

### GLBP Components
- **AVG (Active Virtual Gateway)**: Elected leader, assigns virtual MAC addresses
- **AVF (Active Virtual Forwarder)**: Up to 4 routers that forward traffic
- **Each AVF** acts as default gateway for portion of hosts

### GLBP Specifications
- **Multicast Address**: 224.0.0.102
- **Virtual MAC**: 0007.b400.XXYY
  - XX = GLBP Group Number
  - YY = AVF Number

### Load Balancing Methods
1. **Round Robin**: Default, cycles through AVFs
2. **Weighted**: Based on configured weights
3. **Host Dependent**: Same host always uses same AVF

---

## FHRP Comparison Chart

| Feature | HSRP v1 | HSRP v2 | VRRP | GLBP |
|---------|---------|---------|------|------|
| **Vendor** | Cisco | Cisco | Open Standard | Cisco |
| **Active Router** | Active | Active | Master | AVG |
| **Standby Router** | Standby | Standby | Backup | AVF |
| **Multicast Address** | 224.0.0.2 | 224.0.0.102 | 224.0.0.18 | 224.0.0.102 |
| **Virtual MAC** | 0000.0c07.acXX | 0000.0c9f.fXXX | 0000.5e00.01XX | 0007.b400.XXYY |
| **Max Groups** | 255 | 4095 | 255 | 1024 |
| **IPv6 Support** | No | Yes | VRRPv3 | No |
| **Load Balancing** | No | No | No | Yes |
| **Hello Timer** | 3 sec | 3 sec | 1 sec | 3 sec |
| **Hold Timer** | 10 sec | 10 sec | 3.6 sec | 10 sec |

---

## Configuration Examples

### HSRP Configuration

#### Router 1 (Primary)
```cisco
interface GigabitEthernet0/1
 ip address 192.168.1.1 255.255.255.0
 standby version 2
 standby 1 ip 192.168.1.254
 standby 1 priority 110
 standby 1 preempt
 standby 1 authentication md5 key-string MyKey123
```

#### Router 2 (Secondary)
```cisco
interface GigabitEthernet0/1
 ip address 192.168.1.2 255.255.255.0
 standby version 2
 standby 1 ip 192.168.1.254
 standby 1 priority 90
 standby 1 authentication md5 key-string MyKey123
```

### VRRP Configuration

#### Router 1 (Master)
```cisco
interface GigabitEthernet0/1
 ip address 192.168.1.1 255.255.255.0
 vrrp 1 ip 192.168.1.254
 vrrp 1 priority 110
 vrrp 1 preempt
 vrrp 1 authentication md5 key-string MyKey123
```

#### Router 2 (Backup)
```cisco
interface GigabitEthernet0/1
 ip address 192.168.1.2 255.255.255.0
 vrrp 1 ip 192.168.1.254
 vrrp 1 priority 90
 vrrp 1 authentication md5 key-string MyKey123
```

### GLBP Configuration

#### Router 1
```cisco
interface GigabitEthernet0/1
 ip address 192.168.1.1 255.255.255.0
 glbp 1 ip 192.168.1.254
 glbp 1 priority 110
 glbp 1 preempt
 glbp 1 load-balancing round-robin
```

#### Router 2
```cisco
interface GigabitEthernet0/1
 ip address 192.168.1.2 255.255.255.0
 glbp 1 ip 192.168.1.254
 glbp 1 priority 90
 glbp 1 load-balancing round-robin
```

---

## Verification Commands

### HSRP Verification
```cisco
show standby                    # Show all HSRP groups
show standby brief              # Brief HSRP status
show standby group 1            # Specific group details
debug standby                   # Debug HSRP operations
```

### VRRP Verification
```cisco
show vrrp                       # Show all VRRP groups
show vrrp brief                 # Brief VRRP status
show vrrp group 1               # Specific group details
debug vrrp                      # Debug VRRP operations
```

### GLBP Verification
```cisco
show glbp                       # Show all GLBP groups
show glbp brief                 # Brief GLBP status
show glbp group 1               # Specific group details
debug glbp                      # Debug GLBP operations
```

---

## Key Exam Points to Remember

### Critical Concepts
1. **FHRP Purpose**: Eliminates single point of failure for default gateway
2. **Virtual IP**: Shared IP address used as default gateway
3. **Virtual MAC**: Shared MAC address for the virtual IP
4. **Gratuitous ARP**: Updates switch MAC tables during failover
5. **Non-preemptive**: Original router doesn't automatically reclaim active role

### Protocol Specifics
- **HSRP**: Cisco proprietary, Active/Standby, two versions
- **VRRP**: Open standard, Master/Backup, single version
- **GLBP**: Cisco proprietary, load balancing capable

### Important Numbers to Memorize
- HSRP v1: 224.0.0.2, 0000.0c07.acXX
- HSRP v2: 224.0.0.102, 0000.0c9f.fXXX
- VRRP: 224.0.0.18, 0000.5e00.01XX
- GLBP: 224.0.0.102, 0007.b400.XXYY

### Configuration Essentials
- **Group numbers** must match on all routers
- **Authentication** should be configured for security
- **Priority** determines active router (higher wins)
- **Preempt** allows automatic role reclaim
- **Timers** can be tuned for faster convergence

### Common Troubleshooting
- Version mismatch between routers
- Group number mismatch
- Authentication mismatch
- Priority conflicts
- Timer mismatches
- Interface status issues

---

## Exam Tips

1. **Understand the problem FHRP solves** - single point of failure
2. **Know the differences** between HSRP, VRRP, and GLBP
3. **Memorize multicast and MAC addresses** for each protocol
4. **Practice configuration** commands for all three protocols
5. **Understand preemption** and when to use it
6. **Know verification commands** to troubleshoot issues
7. **Understand load balancing** concepts in GLBP

Remember: FHRP is about **redundancy and availability** - ensuring hosts always have a path to external networks even when their default gateway fails!