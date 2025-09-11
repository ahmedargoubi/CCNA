# CCNA Lab: Configure NAT for IPv4 - Complete Guide

## Lab Overview

### Objectives
- Build network topology and configure basic device settings
- Configure and verify Dynamic NAT using a pool of addresses
- Configure and verify PAT (Port Address Translation) with pool and interface overload
- Configure and verify Static NAT for server access

### Topology Diagram
```
Internet (Simulated)
         |
    [R2] Lo1: 209.165.200.1/27
         |
         | G0/0/0: 209.165.200.225/29
         |
         | G0/0/0: 209.165.200.230/29  
    [R1 - Gateway]
         |
         | G0/0/1: 192.168.1.1/24
         |
    [S1]-----[S2]
  192.168.1.11  192.168.1.12
    |             |
  PC-A         PC-B
192.168.1.2   192.168.1.3
```

### Addressing Table
| Device | Interface | IP Address | Subnet Mask |
|--------|-----------|------------|-------------|
| R1 | G0/0/0 | 209.165.200.230 | 255.255.255.248 |
| R1 | G0/0/1 | 192.168.1.1 | 255.255.255.0 |
| R2 | G0/0/0 | 209.165.200.225 | 255.255.255.248 |
| R2 | Lo1 | 209.165.200.1 | 255.255.255.224 |
| S1 | VLAN 1 | 192.168.1.11 | 255.255.255.0 |
| S2 | VLAN 1 | 192.168.1.12 | 255.255.255.0 |
| PC-A | NIC | 192.168.1.2 | 255.255.255.0 |
| PC-B | NIC | 192.168.1.3 | 255.255.255.0 |

### IP Address Allocation
- **WAN Link**: 209.165.200.224/29 (8 addresses)
  - Network: 209.165.200.224
  - R2 G0/0/0: 209.165.200.225
  - Available for NAT: 209.165.200.226-209.165.200.229
  - R1 G0/0/0: 209.165.200.230
  - Broadcast: 209.165.200.231

---

## Background: Understanding NAT Types

### Dynamic NAT
- **One-to-one** temporary mapping from a pool
- Each internal host gets a unique public IP
- **Limitation**: Pool can be exhausted
- **Timeout**: 24 hours by default

### PAT (Port Address Translation) / NAT Overload
- **Many-to-one** mapping using port numbers
- Multiple internal hosts share one public IP
- **Advantage**: Maximizes IP address utilization
- **Timeout**: 1 minute for ICMP, varies by protocol

### Static NAT
- **Permanent one-to-one** mapping
- Used for servers requiring consistent external access
- **No timeout**: Mapping persists until removed

---

## Part 1: Build the Network and Configure Basic Device Settings

### Step 1: Cable the Network
- Connect R1 G0/0/0 to R2 G0/0/0 (crossover or straight-through cable)
- Connect R1 G0/0/1 to S1 (straight-through cable)
- Connect PC-A to S1, PC-B to S2
- Connect S1 to S2 (if needed for redundancy)

### Step 2: Configure R1 (Gateway Router)

```cisco
Router> enable
Router# configure terminal

! Basic settings
Router(config)# hostname R1
Router(config)# no ip domain-lookup
Router(config)# enable secret class
Router(config)# service password-encryption

! Console configuration
Router(config)# line console 0
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# exit

! VTY configuration
Router(config)# line vty 0 15
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# exit

! Banner
Router(config)# banner motd #Unauthorized access prohibited#

! Interface configuration
Router(config)# interface gigabitethernet 0/0/0
Router(config-if)# ip address 209.165.200.230 255.255.255.248
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface gigabitethernet 0/0/1
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

! Default route to R2
Router(config)# ip route 0.0.0.0 0.0.0.0 209.165.200.225

! Save configuration
Router(config)# exit
Router# copy running-config startup-config
```

### Step 3: Configure R2 (ISP Router)

```cisco
Router> enable
Router# configure terminal

! Basic settings
Router(config)# hostname R2
Router(config)# no ip domain-lookup
Router(config)# enable secret class
Router(config)# service password-encryption

! Console and VTY (same as R1)
Router(config)# line console 0
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# exit

Router(config)# line vty 0 15
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# exit

! Banner
Router(config)# banner motd #Unauthorized access prohibited#

! Interface configuration
Router(config)# interface gigabitethernet 0/0/0
Router(config-if)# ip address 209.165.200.225 255.255.255.248
Router(config-if)# no shutdown
Router(config-if)# exit

! Loopback interface (simulates Internet)
Router(config)# interface loopback 1
Router(config-if)# ip address 209.165.200.1 255.255.255.224
Router(config-if)# exit

! Save configuration
Router(config)# exit
Router# copy running-config startup-config
```

### Step 4: Configure Switches S1 and S2

#### S1 Configuration:
```cisco
Switch> enable
Switch# configure terminal

! Basic settings
Switch(config)# hostname S1
Switch(config)# no ip domain-lookup
Switch(config)# enable secret class
Switch(config)# service password-encryption

! Console and VTY configuration
Switch(config)# line console 0
Switch(config-line)# password cisco
Switch(config-line)# login
Switch(config-line)# exit

Switch(config)# line vty 0 15
Switch(config-line)# password cisco
Switch(config-line)# login
Switch(config-line)# exit

! Banner
Switch(config)# banner motd #Unauthorized access prohibited#

! VLAN 1 IP address
Switch(config)# interface vlan 1
Switch(config-if)# ip address 192.168.1.11 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit

! Default gateway
Switch(config)# ip default-gateway 192.168.1.1

! Shutdown unused interfaces
Switch(config)# interface range fastethernet 0/3-24
Switch(config-if-range)# shutdown
Switch(config-if-range)# exit

! Save configuration
Switch# copy running-config startup-config
```

#### S2 Configuration:
```cisco
! Same as S1 but with different IP
Switch(config)# hostname S2
Switch(config)# interface vlan 1
Switch(config-if)# ip address 192.168.1.12 255.255.255.0
```

### Step 5: Configure PC Hosts

**PC-A:**
```
IP Address: 192.168.1.2
Subnet Mask: 255.255.255.0  
Default Gateway: 192.168.1.1
```

**PC-B:**
```
IP Address: 192.168.1.3
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.1.1
```

---

## Part 2: Configure and Verify Dynamic NAT

### Understanding Dynamic NAT Pool
- **Pool Range**: 209.165.200.226 to 209.165.200.228 (3 addresses)
- **One-to-one mapping**: Each internal device gets one public IP
- **Limitation**: Only 3 devices can access Internet simultaneously

### Step 1: Configure Dynamic NAT on R1

```cisco
R1# configure terminal

! Define access list (which hosts can be translated)
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255

! Create NAT pool
R1(config)# ip nat pool PUBLIC_ACCESS 209.165.200.226 209.165.200.228 netmask 255.255.255.248

! Configure NAT translation (linking ACL and pool)
R1(config)# ip nat inside source list 1 pool PUBLIC_ACCESS

! Define inside interface
R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# ip nat inside
R1(config-if)# exit

! Define outside interface
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# ip nat outside
R1(config-if)# exit
```

### Step 2: Test and Verify Dynamic NAT

#### Test 1: PC-B to Internet

```bash
# From PC-B
ping 209.165.200.1
```

**Check NAT Table:**
```cisco
R1# show ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
--- 209.165.200.226    192.168.1.3       ---                ---
icmp 209.165.200.226:1 192.168.1.3:1     209.165.200.1:1    209.165.200.1:1
```

**Lab Questions:**
- **Q: What was PC-B's inside local address translated to?**
  - **Answer**: 192.168.1.3 → 209.165.200.226
- **Q: What type of NAT address is the translated address?**
  - **Answer**: Inside Global address (first available from pool)

#### Test 2: PC-A to Internet

```bash
# From PC-A
ping 209.165.200.1
```

**Check NAT Table:**
```cisco
R1# show ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
--- 209.165.200.227    192.168.1.2       ---                ---
--- 209.165.200.226    192.168.1.3       ---                ---
icmp 209.165.200.227:1 192.168.1.2:1     209.165.200.1:1    209.165.200.1:1
icmp 209.165.200.226:1 192.168.1.3:1     209.165.200.1:1    209.165.200.1:1
```

#### Test 3: S1 to Internet

```cisco
# From S1
ping 209.165.200.1
```

**Check NAT Table:**
```cisco
R1# show ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
--- 209.165.200.227    192.168.1.2       ---                ---
--- 209.165.200.226    192.168.1.3       ---                ---  
--- 209.165.200.228    192.168.1.11      ---                ---
icmp 209.165.200.226:1 192.168.1.3:1     209.165.200.1:1    209.165.200.1:1
icmp 209.165.200.228:0 192.168.1.11:0    209.165.200.1:0    209.165.200.1:0
```

#### Test 4: Pool Exhaustion (S2 to Internet)

```cisco
# From S2
ping 209.165.200.1
```

**Expected Error:**
```
%NAT-6-ADDR_ALLOC_FAILURE: Address allocation failed; pool 1 may be exhausted [2]
```

**Reason**: Only 3 addresses in pool, 4th device cannot get translation.

#### Understanding NAT Timeouts

```cisco
R1# show ip nat translations verbose
Pro Inside global      Inside local       Outside local      Outside global
--- 209.165.200.226    192.168.1.3       ---                ---
create: 09/23/19 15:35:27, use: 09/23/19 15:35:27, timeout: 23:56:42
```

**Key Point**: Dynamic NAT entries timeout after **24 hours** by default.

### Step 3: Clear NAT for Next Section

```cisco
R1# clear ip nat translations *
R1# clear ip nat statistics
```

---

## Part 3: Configure and Verify PAT (Port Address Translation)

### Understanding PAT
PAT allows multiple internal devices to share a single public IP address using different port numbers. This dramatically increases the number of devices that can access the Internet.

### Step 1: Configure PAT to Pool

#### Remove Previous NAT Command

```cisco
R1(config)# no ip nat inside source list 1 pool PUBLIC_ACCESS
```

#### Add PAT Command with Overload

```cisco
R1(config)# ip nat inside source list 1 pool PUBLIC_ACCESS overload
```

**Key Difference**: The `overload` keyword enables PAT.

### Step 2: Test PAT to Pool

#### Test 1: PC-B with PAT

```bash
# From PC-B
ping 209.165.200.1
```

**Check NAT Table:**
```cisco
R1# show ip nat translations
Pro Inside global         Inside local          Outside local         Outside global
icmp 209.165.200.226:1   192.168.1.3:1        209.165.200.1:1       209.165.200.1:1
```

**Lab Questions:**
- **Q: What was PC-B's inside local address translated to?**
  - **Answer**: 192.168.1.3 → 209.165.200.226:1
- **Q: What type of NAT address is the translated address?**
  - **Answer**: Inside Global address with port number (PAT)
- **Q: What's different from regular NAT?**
  - **Answer**: Only shows active translations (1 minute timeout vs 24 hours)

#### Test 2: Multiple Device PAT

```bash
# From PC-A and PC-B simultaneously
ping -t 209.165.200.1
```

**Check NAT Table:**
```cisco
R1# show ip nat translations
Pro Inside global         Inside local          Outside local         Outside global
icmp 209.165.200.226:1   192.168.1.2:1        209.165.200.1:1       209.165.200.1:1
icmp 209.165.200.226:2   192.168.1.3:1        209.165.200.1:1       209.165.200.1:2
```

**Lab Question:**
- **Q: How does the router keep track of what replies go where?**
  - **Answer**: Using port numbers - each session gets a unique port number mapping

### Step 3: Configure PAT to Interface (Most Common)

#### Remove Pool-based PAT

```cisco
R1(config)# no ip nat inside source list 1 pool PUBLIC_ACCESS overload
R1(config)# no ip nat pool PUBLIC_ACCESS
```

#### Configure PAT to Outside Interface

```cisco
R1(config)# ip nat inside source list 1 interface gigabitethernet 0/0/0 overload
```

**Advantage**: Uses the interface IP address, no wasted public IPs.

### Step 4: Test PAT to Interface

#### Single Device Test

```bash
# From PC-B
ping 209.165.200.1
```

**Check NAT Table:**
```cisco
R1# show ip nat translations
Pro Inside global         Inside local          Outside local         Outside global
icmp 209.165.200.230:1   192.168.1.3:1        209.165.200.1:1       209.165.200.1:1
```

**Key Observation**: Inside Global address is now the interface IP (209.165.200.230).

#### Multiple Device Test

```bash
# From PC-A and PC-B
ping -t 209.165.200.1

# From S1 and S2
ping 209.165.200.1 repeat 2000
```

**Check NAT Table:**
```cisco
R1# show ip nat translations
Pro Inside global         Inside local          Outside local         Outside global
icmp 209.165.200.230:3   192.168.1.11:1       209.165.200.1:1       209.165.200.1:3
icmp 209.165.200.230:2   192.168.1.2:1        209.165.200.1:1       209.165.200.1:2  
icmp 209.165.200.230:4   192.168.1.3:1        209.165.200.1:1       209.165.200.1:4
icmp 209.165.200.230:1   192.168.1.12:1       209.165.200.1:1       209.165.200.1:1
```

**Result**: All devices share the same Inside Global IP with different port numbers.

---

## Part 4: Configure and Verify Static NAT

### Understanding Static NAT
Static NAT creates a permanent one-to-one mapping, typically used for servers that need consistent external access.

### Step 1: Clear Current Translations

```cisco
R1# clear ip nat translations *
R1# clear ip nat statistics
```

### Step 2: Configure Static NAT

```cisco
R1(config)# ip nat inside source static 192.168.1.2 209.165.200.229
```

**Mapping**: PC-A (192.168.1.2) ↔ 209.165.200.229

### Step 3: Test Static NAT

#### Verify Static Mapping

```cisco
R1# show ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
--- 209.165.200.229    192.168.1.2       ---                ---
```

#### Test External Access to PC-A

```bash
# From R2
ping 209.165.200.229
```

**Should succeed** - external devices can now reach PC-A directly.

#### Verify NAT Table After External Ping

```cisco
R1# show ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
--- 209.165.200.229    192.168.1.2       ---                ---
icmp 209.165.200.229:3 192.168.1.2:3     209.165.200.225:3  209.165.200.225:3
```

**Key Points:**
- Static translation is always present
- Dynamic entries are created for active sessions
- External devices can initiate connections to PC-A

---

## Comprehensive Configuration Summary

### Final R1 Configuration

```cisco
hostname R1
no ip domain-lookup
enable secret class
service password-encryption
!
interface GigabitEthernet0/0/0
 ip address 209.165.200.230 255.255.255.248
 ip nat outside
 no shutdown
!
interface GigabitEthernet0/0/1
 ip address 192.168.1.1 255.255.255.0
 ip nat inside
 no shutdown
!
ip route 0.0.0.0 0.0.0.0 209.165.200.225
!
! For PAT to interface (most common)
ip nat inside source list 1 interface GigabitEthernet0/0/0 overload
!
! For Static NAT (servers)
ip nat inside source static 192.168.1.2 209.165.200.229
!
access-list 1 permit 192.168.1.0 0.0.0.255
!
line console 0
 password cisco
 login
line vty 0 15
 password cisco
 login
!
banner motd #Unauthorized access prohibited#
```

---

## NAT Troubleshooting Guide

### Common Commands

```cisco
! View NAT translations
show ip nat translations

! View detailed translations with timeouts
show ip nat translations verbose

! View NAT statistics
show ip nat statistics

! Clear translations
clear ip nat translations *

! Clear statistics
clear ip nat statistics

! Debug NAT (use carefully)
debug ip nat

! View NAT configuration
show running-config | include nat
```

### Common Issues and Solutions

#### Issue 1: NAT Not Working
**Symptoms**: Internal devices can't reach Internet
**Troubleshooting Steps**:
1. Verify inside/outside interface configuration
   ```cisco
   show ip nat statistics
   ```
2. Check ACL permits correct addresses
   ```cisco
   show access-lists
   ```
3. Verify routing is working without NAT
   ```bash
   ping 209.165.200.225
   ```

#### Issue 2: Pool Exhaustion
**Symptoms**: Some devices can't get NAT translations
**Solutions**:
- Use PAT instead of pure NAT
- Increase pool size
- Implement PAT to interface

#### Issue 3: Static NAT Not Accessible Externally
**Symptoms**: External devices can't reach static NAT address
**Troubleshooting**:
1. Check static NAT configuration
2. Verify routing from external network
3. Check firewall settings on internal device
4. Test with `debug ip nat`

---

## Performance Considerations

### NAT vs PAT Comparison

| Feature | Dynamic NAT | PAT (Overload) |
|---------|-------------|----------------|
| **IP Usage** | 1:1 mapping | Many:1 mapping |
| **Scalability** | Limited by pool size | ~65,000 sessions per IP |
| **Timeout** | 24 hours | 1 minute (ICMP) |
| **Cost** | Requires multiple public IPs | Single public IP |
| **Complexity** | Simple | Port tracking required |

### Best Practices

1. **Use PAT for most scenarios** - maximizes IP efficiency
2. **Reserve Static NAT for servers** - provides consistent access
3. **Size pools appropriately** - consider peak usage
4. **Monitor NAT statistics** - watch for pool exhaustion
5. **Document mappings** - especially static NAT entries

---

## Security Considerations

### NAT Security Benefits
- **Topology Hiding**: Internal network structure not visible externally
- **Unsolicited Inbound Traffic Blocking**: NAT typically blocks inbound connections
- **Address Obfuscation**: Internal addresses not exposed

### Security Limitations
- **Not a Firewall**: NAT provides limited security
- **Outbound Traffic Allowed**: Malware can still initiate outbound connections  
- **Static NAT Exposure**: Static mappings create permanent external access

### Production Recommendations
- **Combine with Firewalls**: NAT should supplement, not replace firewalls
- **Implement Access Lists**: Control what traffic can be translated
- **Monitor Translations**: Log and audit NAT usage
- **Secure Static NAT**: Protect devices with static mappings

---

## Key Takeaways

1. **Dynamic NAT** provides one-to-one temporary mappings but can exhaust addresses
2. **PAT (overload)** maximizes IP efficiency by using port numbers for many-to-one mapping
3. **Static NAT** creates permanent mappings essential for server access
4. **Interface PAT** is the most common implementation in production networks
5. **Proper planning** of NAT pools and policies is crucial for network scalability
6. **NAT troubleshooting** requires understanding translation tables and statistics

This lab provides hands-on experience with all major NAT types used in production networks, from basic dynamic NAT to advanced PAT implementations.