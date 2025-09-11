# CCNA Lab: Configuring Dynamic and Static NAT - Complete Guide

## Lab Overview

### Objectives
- Build network topology and verify connectivity
- Configure and verify Static NAT (one-to-one mapping)
- Configure and verify Dynamic NAT (many-to-many mapping from a pool)
- Understand NAT operation and troubleshooting



### Addressing Table
| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|-----------------|
| Gateway | G0/1 | 192.168.1.1 | 255.255.255.0 | N/A |
| Gateway | S0/0/1 | 209.165.201.18 | 255.255.255.252 | N/A |
| ISP | S0/0/0 (DCE) | 209.165.201.17 | 255.255.255.252 | N/A |
| ISP | Lo0 | 192.31.7.1 | 255.255.255.255 | N/A |
| PC-A | NIC | 192.168.1.20 | 255.255.255.0 | 192.168.1.1 |
| PC-B | NIC | 192.168.1.21 | 255.255.255.0 | 192.168.1.1 |

### NAT Address Allocation
- **Public IP Range**: 209.165.200.224/27 (30 usable addresses)
- **Static NAT Pool**: 209.165.200.225 to 209.165.200.241 (17 addresses)
- **Dynamic NAT Pool**: 209.165.200.242 to 209.165.200.254 (13 addresses)

---

## Background: Understanding NAT

### What is NAT?
**Network Address Translation (NAT)** allows devices on a private network to communicate with devices on a public network (like the Internet) by translating private IP addresses to public IP addresses.

### Why Use NAT?
- **IPv4 Address Conservation**: Reduces the need for public IP addresses
- **Security**: Hides internal network structure
- **Flexibility**: Allows internal network changes without affecting external connectivity

### Types of NAT
1. **Static NAT**: One-to-one mapping (permanent)
2. **Dynamic NAT**: Many-to-many mapping from a pool (temporary)
3. **PAT/NAPT**: Many-to-one mapping using port numbers (not covered in this lab)

---

## Part 1: Build the Network and Verify Connectivity

### Step 1: Cable the Network

**Physical Connections:**
- Connect ISP S0/0/0 to Gateway S0/0/1 (serial cable)
- Connect Gateway G0/1 to Switch (Ethernet cable)
- Connect PC-A and PC-B to Switch (Ethernet cables)
- Console cables for configuration access

### Step 2: Configure PC Hosts

**PC-A Configuration:**
```
IP Address: 192.168.1.20
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.1.1
```

**PC-B Configuration:**
```
IP Address: 192.168.1.21
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.1.1
```

### Step 3: Initialize Equipment

1. **Erase startup configurations**
2. **Reload all devices**
3. **Verify clean state**

### Step 4: Configure Basic Settings

#### Gateway Router Configuration

```cisco
Router> enable
Router# configure terminal

! Disable DNS lookup
Router(config)# no ip domain-lookup

! Configure hostname
Router(config)# hostname Gateway

! Configure interfaces
Router(config)# interface gigabitethernet 0/1
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface serial 0/0/1
Router(config-if)# ip address 209.165.201.18 255.255.255.252
Router(config-if)# no shutdown
Router(config-if)# exit

! Configure passwords
Router(config)# enable secret class
Router(config)# line console 0
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# logging synchronous
Router(config-line)# exit

Router(config)# line vty 0 4
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# exit
```

#### ISP Router Configuration

```cisco
Router> enable
Router# configure terminal

! Disable DNS lookup
Router(config)# no ip domain-lookup

! Configure hostname
Router(config)# hostname ISP

! Configure interfaces
Router(config)# interface serial 0/0/0
Router(config-if)# ip address 209.165.201.17 255.255.255.252
Router(config-if)# clock rate 128000
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface loopback 0
Router(config-if)# ip address 192.31.7.1 255.255.255.255
Router(config-if)# exit

! Configure passwords
Router(config)# enable secret class
Router(config)# line console 0
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# logging synchronous
Router(config-line)# exit

Router(config)# line vty 0 4
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# exit
```

### Step 5: Create Simulated Web Server on ISP

```cisco
ISP(config)# username webuser privilege 15 secret webpass
ISP(config)# ip http server
ISP(config)# ip http authentication local
```

### Step 6: Configure Static Routing

#### ISP Router - Static Route to Internal Network

```cisco
ISP(config)# ip route 209.165.200.224 255.255.255.224 209.165.201.18
```

#### Gateway Router - Default Route to ISP

```cisco
Gateway(config)# ip route 0.0.0.0 0.0.0.0 209.165.201.17
```

### Step 7: Save Configurations

```cisco
Gateway# copy running-config startup-config
ISP# copy running-config startup-config
```

### Step 8: Verify Network Connectivity

#### Test Local Connectivity
```bash
# From PC-A
ping 192.168.1.1

# From PC-B  
ping 192.168.1.1
```

#### Verify Routing Tables
```cisco
Gateway# show ip route
ISP# show ip route
```

**Expected Gateway Routing Table:**
```
Gateway codes: L - local, C - connected, S - static
      192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.1.0/24 is directly connected, GigabitEthernet0/1
L        192.168.1.1/32 is directly connected, GigabitEthernet0/1
      209.165.201.16/30 is variably subnetted, 2 subnets, 2 masks
C        209.165.201.16/30 is directly connected, Serial0/0/1
L        209.165.201.18/32 is directly connected, Serial0/0/1
S*   0.0.0.0/0 [1/0] via 209.165.201.17
```

---

## Part 2: Configure and Verify Static NAT

### Understanding Static NAT

**Static NAT** creates a permanent one-to-one mapping between:
- **Inside Local**: Private IP address (192.168.1.20)
- **Inside Global**: Public IP address (209.165.200.225)

This is ideal for servers that need consistent external access.

### Step 1: Configure Static Mapping

```cisco
Gateway(config)# ip nat inside source static 192.168.1.20 209.165.200.225
```

### Step 2: Specify NAT Interfaces

```cisco
! Configure inside interface
Gateway(config)# interface gigabitethernet 0/1
Gateway(config-if)# ip nat inside
Gateway(config-if)# exit

! Configure outside interface  
Gateway(config)# interface serial 0/0/1
Gateway(config-if)# ip nat outside
Gateway(config-if)# exit
```

### Step 3: Test Static NAT Configuration

#### View NAT Translation Table

```cisco
Gateway# show ip nat translations
```

**Expected Output:**
```
Pro Inside global      Inside local       Outside local      Outside global
--- 209.165.200.225    192.168.1.20      ---                ---
```

#### Lab Questions and Analysis

**Q: What is the translation of Inside local host address?**
- **Answer**: 192.168.1.20 = 209.165.200.225

**Q: The Inside global address is assigned by?**
- **Answer**: Network administrator (manually configured)

**Q: The Inside local address is assigned by?**
- **Answer**: DHCP server or manual configuration on local network

#### Test with Ping from PC-A

```bash
# From PC-A
ping 192.31.7.1
```

**Check NAT Table After Ping:**
```cisco
Gateway# show ip nat translations
```

**Expected Output:**
```
Pro Inside global         Inside local          Outside local         Outside global
icmp 209.165.200.225:1   192.168.1.20:1      192.31.7.1:1         192.31.7.1:1
--- 209.165.200.225      192.168.1.20        ---                   ---
```

**Q: What port number was used in ICMP exchange?**
- **Answer**: Port 1 (ICMP identifier)

#### Test with Telnet from PC-A

```bash
# From PC-A
telnet 192.31.7.1
```

**Check NAT Table After Telnet:**
```cisco
Gateway# show ip nat translations
```

**Expected Output:**
```
Pro Inside global         Inside local          Outside local         Outside global
tcp 209.165.200.225:1034 192.168.1.20:1034   192.31.7.1:23        192.31.7.1:23
--- 209.165.200.225      192.168.1.20        ---                   ---
```

**Lab Questions:**
- **Q: What protocol was used?** TCP
- **Q: Inside global/local port:** 1034
- **Q: Outside global/local port:** 23 (Telnet)

#### Test Reverse Connectivity (ISP to PC-A)

```bash
# From ISP router
ping 209.165.200.225
```

This should successfully reach PC-A through static NAT.

#### View NAT Statistics

```cisco
Gateway# show ip nat statistics
```

**Sample Output Analysis:**
```
Total active translations: 2 (1 static, 1 dynamic; 1 extended)
Peak translations: 2
Outside interfaces: Serial0/0/1
Inside interfaces: GigabitEthernet0/1
Hits: 39 Misses: 0
```

---

## Part 3: Configure and Verify Dynamic NAT

### Understanding Dynamic NAT

**Dynamic NAT** assigns public IP addresses from a pool on a first-come, first-served basis. Multiple private addresses can be translated, but each gets a temporary public address.

### Step 1: Clear Previous NAT Entries

```cisco
Gateway# clear ip nat translation *
Gateway# clear ip nat statistics
```

### Step 2: Define Access Control List (ACL)

```cisco
Gateway(config)# access-list 1 permit 192.168.1.0 0.0.0.255
```

**Purpose**: Identifies which internal addresses can use NAT

### Step 3: Verify NAT Interface Configuration

```cisco
Gateway# show ip nat statistics
```

Ensure inside/outside interfaces are still configured correctly.

### Step 4: Define Public IP Address Pool

```cisco
Gateway(config)# ip nat pool public_access 209.165.200.242 209.165.200.254 netmask 255.255.255.224
```

**Pool Details:**
- **Name**: public_access
- **Range**: 209.165.200.242 to 209.165.200.254 (13 addresses)
- **Netmask**: 255.255.255.224 (/27)

### Step 5: Link ACL to NAT Pool

```cisco
Gateway(config)# ip nat inside source list 1 pool public_access
```

**Important**: Pool name is case-sensitive!

### Step 6: Test Dynamic NAT Configuration

#### Test from PC-B

```bash
# From PC-B
ping 192.31.7.1
```

**Check NAT Table:**
```cisco
Gateway# show ip nat translations
```

**Expected Output:**
```
Pro Inside global         Inside local          Outside local         Outside global
--- 209.165.200.225      192.168.1.20         ---                   ---
icmp 209.165.200.242:1   192.168.1.21:1      192.31.7.1:1         192.31.7.1:1
--- 209.165.200.242      192.168.1.21         ---                   ---
```

**Lab Questions:**
- **Q: What is translation of PC-B's inside local address?**
  - **Answer**: 192.168.1.21 = 209.165.200.242
- **Q: What port number was used in ICMP exchange?**
  - **Answer**: Port 1

#### Test HTTP Access from PC-B

```bash
# From PC-B - Open web browser
# Navigate to: 192.31.7.1
# Login: webuser / webpass
```

**Check NAT Table After HTTP:**
```cisco
Gateway# show ip nat translations
```

**Expected Output:**
```
Pro Inside global         Inside local          Outside local         Outside global
--- 209.165.200.225      192.168.1.20         ---                   ---
tcp 209.165.200.242:1038 192.168.1.21:1038   192.31.7.1:80         192.31.7.1:80
tcp 209.165.200.242:1039 192.168.1.21:1039   192.31.7.1:80         192.31.7.1:80
[Multiple TCP connections for HTTP]
--- 209.165.200.242      192.168.1.21         ---                   ---
```

**Lab Questions:**
- **Q: What protocol was used?** TCP
- **Q: Inside port numbers:** Various high ports (1038, 1039, etc.)
- **Q: Outside port number:** 80 (HTTP)
- **Q: Well-known port and service:** Port 80, HTTP service

#### View NAT Statistics

```cisco
Gateway# show ip nat statistics
```

**Sample Output:**
```
Total active translations: 3 (1 static, 2 dynamic; 1 extended)
Dynamic mappings:
-- Inside Source
[Id: 1] access-list 1 pool public_access refcount 2
pool public_access: netmask 255.255.255.224
  start 209.165.200.242 end 209.165.200.254
  type generic, total addresses 13, allocated 1 (7%), misses 0
```

### Step 7: Remove Static NAT and Test

#### Remove Static NAT Entry

```cisco
Gateway(config)# no ip nat inside source static 192.168.1.20 209.165.200.225
Static entry in use, do you want to delete child entries? [no]: yes
```

#### Clear and Test

```cisco
Gateway# clear ip nat translation *
Gateway# clear ip nat statistics
```

#### Test Both PCs

```bash
# From both PC-A and PC-B
ping 192.31.7.1
```

**Check Final NAT Table:**
```cisco
Gateway# show ip nat translations
```

**Expected Output:**
```
Pro Inside global         Inside local          Outside local         Outside global
icmp 209.165.200.243:512 192.168.1.20:512    192.31.7.1:512       192.31.7.1:512
--- 209.165.200.243      192.168.1.20         ---                   ---
icmp 209.165.200.242:512 192.168.1.21:512    192.31.7.1:512       192.31.7.1:512
--- 209.165.200.242      192.168.1.21         ---                   ---
```

**Observation**: Both PCs now use dynamic NAT addresses from the pool.

---

## Troubleshooting NAT

### Common Commands

```cisco
! View NAT translations
show ip nat translations

! View NAT statistics  
show ip nat statistics

! Clear NAT translations
clear ip nat translation *

! Clear NAT statistics
clear ip nat statistics

! Debug NAT (use carefully)
debug ip nat

! View running NAT configuration
show running-config | include nat
```

### Common Issues and Solutions

#### Issue 1: NAT Not Working
**Symptoms**: Private addresses visible externally
**Solutions**:
- Verify inside/outside interface configuration
- Check ACL permits correct addresses
- Ensure NAT pool has available addresses

#### Issue 2: Static NAT Conflicts
**Symptoms**: Static NAT entry won't configure
**Solutions**:
- Remove conflicting dynamic entries
- Clear NAT translation table
- Verify IP address ranges don't overlap

#### Issue 3: Dynamic NAT Pool Exhausted
**Symptoms**: New connections fail
**Solutions**:
- Increase pool size
- Reduce NAT timeout values
- Clear expired translations

---

## Configuration Summary

### Complete Gateway Router Configuration

```cisco
hostname Gateway
no ip domain-lookup
!
interface GigabitEthernet0/1
 ip address 192.168.1.1 255.255.255.0
 ip nat inside
 no shutdown
!
interface Serial0/0/1
 ip address 209.165.201.18 255.255.255.252
 ip nat outside
 no shutdown
!
ip route 0.0.0.0 0.0.0.0 209.165.201.17
!
ip nat pool public_access 209.165.200.242 209.165.200.254 netmask 255.255.255.224
ip nat inside source list 1 pool public_access
!
access-list 1 permit 192.168.1.0 0.0.0.255
!
enable secret class
line console 0
 password cisco
 login
 logging synchronous
line vty 0 4
 password cisco
 login
```

### Complete ISP Router Configuration

```cisco
hostname ISP
no ip domain-lookup
!
interface Serial0/0/0
 ip address 209.165.201.17 255.255.255.252
 clock rate 128000
 no shutdown
!
interface Loopback0
 ip address 192.31.7.1 255.255.255.255
!
ip route 209.165.200.224 255.255.255.224 209.165.201.18
!
username webuser privilege 15 secret webpass
ip http server
ip http authentication local
!
enable secret class
line console 0
 password cisco
 login
 logging synchronous
line vty 0 4
 password cisco
 login
```

---

## Reflection Questions

### 1. Why would NAT be used in a network?

**Primary Reasons:**
- **IPv4 Address Conservation**: Allows multiple devices to share fewer public IP addresses
- **Cost Reduction**: Reduces need for multiple public IP addresses from ISP
- **Security Enhancement**: Hides internal network topology from external networks
- **Network Flexibility**: Allows internal addressing changes without affecting external connectivity
- **Regulatory Compliance**: Some organizations require internal address hiding

### 2. What are the limitations of NAT?

**Technical Limitations:**
- **Performance Impact**: Additional processing overhead for address translation
- **Application Compatibility**: Some applications embedding IP addresses may break
- **End-to-End Connectivity**: Breaks the principle of end-to-end Internet connectivity
- **Protocol Issues**: Some protocols (like IPSec) have difficulty with NAT

**Operational Limitations:**
- **Troubleshooting Complexity**: Makes network debugging more difficult
- **Logging Challenges**: Requires correlation between internal and external addresses
- **Connection Limits**: Limited by NAT table size and available ports
- **Asymmetric Routing**: Can cause issues with complex network topologies

---

## Key Takeaways

1. **Static NAT** provides permanent one-to-one mappings ideal for servers
2. **Dynamic NAT** provides temporary many-to-many mappings from a pool
3. **Interface designation** (inside/outside) is crucial for NAT operation
4. **ACLs control** which addresses can use dynamic NAT
5. **NAT pools** must be properly sized for expected concurrent connections
6. **Troubleshooting NAT** requires understanding the translation table and statistics

This lab demonstrates essential NAT concepts that are fundamental to modern networking, especially in IPv4 environments where address conservation is critical.
