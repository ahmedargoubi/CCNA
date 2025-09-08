# Configuring and Verifying Standard IPv4 ACLs - Complete Lab Guide

## Lab Overview

This lab focuses on implementing network security through Access Control Lists (ACLs) in a multi-router topology. You'll learn to configure, apply, and verify both numbered and named standard ACLs to control traffic flow between different network segments.



## Addressing Table

| Device | Interface | IP Address      | Subnet Mask     | Default Gateway |
|--------|-----------|-----------------|-----------------|-----------------|
| R1     | G0/1      | 192.168.10.1    | 255.255.255.0  | N/A            |
| R1     | Lo0       | 192.168.20.1    | 255.255.255.0  | N/A            |
| R1     | S0/0/0    | 10.1.1.1        | 255.255.255.252| N/A            |
| ISP    | S0/0/0    | 10.1.1.2        | 255.255.255.252| N/A            |
| ISP    | S0/0/1    | 10.2.2.2        | 255.255.255.252| N/A            |
| ISP    | Lo0       | 209.165.200.225 | 255.255.255.224| N/A            |
| R3     | G0/1      | 192.168.30.1    | 255.255.255.0  | N/A            |
| R3     | Lo0       | 192.168.40.1    | 255.255.255.0  | N/A            |
| R3     | S0/0/1    | 10.2.2.1        | 255.255.255.252| N/A            |
| S1     | VLAN 1    | 192.168.10.11   | 255.255.255.0  | 192.168.10.1   |
| S3     | VLAN 1    | 192.168.30.11   | 255.255.255.0  | 192.168.30.1   |
| PC-A   | NIC       | 192.168.10.3    | 255.255.255.0  | 192.168.10.1   |
| PC-C   | NIC       | 192.168.30.3    | 255.255.255.0  | 192.168.30.1   |

## Lab Objectives

1. **Part 1**: Set up topology and initialize devices
2. **Part 2**: Configure devices and verify connectivity
3. **Part 3**: Configure and verify standard numbered and named ACLs
4. **Part 4**: Modify a standard ACL

## Required Equipment

- 3 Routers (Cisco 1941 or comparable)
- 2 Switches (Cisco 2960 or comparable)
- 2 PCs
- Console and Ethernet cables

---

## Part 1: Set Up Topology and Initialize Devices

### Step 1: Physical Setup
1. Cable the network according to the topology diagram
2. Connect console cables to all devices
3. Power on all devices

### Step 2: Initialize Devices
1. Erase startup configurations on all routers and switches:
   ```
   Router> enable
   Router# erase startup-config
   Router# reload
   ```

---

## Part 2: Configure Devices and Verify Connectivity

### Step 1: Configure PC IP Addresses

**PC-A Configuration:**
- IP Address: 192.168.10.3
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.10.1

**PC-C Configuration:**
- IP Address: 192.168.30.3
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.30.1

### Step 2: Configure Basic Router Settings

#### Router R1 Configuration:
```cisco
Router> enable
Router# configure terminal
Router(config)# no ip domain-lookup
Router(config)# hostname R1
Router(config)# service password-encryption
Router(config)# enable secret class
Router(config)# banner motd #
Unauthorized access is strictly prohibited. #
Router(config)# line con 0
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# logging synchronous
Router(config-line)# exit
Router(config)# line vty 0 4
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# exit

# Configure interfaces
Router(config)# interface loopback 0
Router(config-if)# ip address 192.168.20.1 255.255.255.0
Router(config-if)# exit

Router(config)# interface g0/1
Router(config-if)# ip address 192.168.10.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface s0/0/0
Router(config-if)# ip address 10.1.1.1 255.255.255.252
Router(config-if)# clock rate 128000
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# copy running-config startup-config
```

#### Router ISP Configuration:
```cisco
Router> enable
Router# configure terminal
Router(config)# hostname ISP
Router(config)# enable secret class

# Configure interfaces
Router(config)# interface loopback 0
Router(config-if)# ip address 209.165.200.225 255.255.255.224
Router(config-if)# exit

Router(config)# interface s0/0/0
Router(config-if)# ip address 10.1.1.2 255.255.255.252
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface s0/0/1
Router(config-if)# ip address 10.2.2.2 255.255.255.252
Router(config-if)# clock rate 128000
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# copy running-config startup-config
```

#### Router R3 Configuration:
```cisco
Router> enable
Router# configure terminal
Router(config)# hostname R3
Router(config)# enable secret class

# Configure interfaces
Router(config)# interface loopback 0
Router(config-if)# ip address 192.168.40.1 255.255.255.0
Router(config-if)# exit

Router(config)# interface g0/1
Router(config-if)# ip address 192.168.30.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface s0/0/1
Router(config-if)# ip address 10.2.2.1 255.255.255.252
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# copy running-config startup-config
```

### Step 3: Configure Switch Settings (Optional)

#### Switch S1 Configuration:
```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname S1
Switch(config)# enable secret class
Switch(config)# interface vlan 1
Switch(config-if)# ip address 192.168.10.11 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit
Switch(config)# ip default-gateway 192.168.10.1
Switch(config)# copy running-config startup-config
```

#### Switch S3 Configuration:
```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname S3
Switch(config)# enable secret class
Switch(config)# interface vlan 1
Switch(config-if)# ip address 192.168.30.11 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit
Switch(config)# ip default-gateway 192.168.30.1
Switch(config)# copy running-config startup-config
```

### Step 4: Configure RIP Routing

#### On R1:
```cisco
R1(config)# router rip
R1(config-router)# version 2
R1(config-router)# network 192.168.10.0
R1(config-router)# network 192.168.20.0
R1(config-router)# network 10.1.1.0
R1(config-router)# exit
```

#### On ISP:
```cisco
ISP(config)# router rip
ISP(config-router)# version 2
ISP(config-router)# network 209.165.200.224
ISP(config-router)# network 10.1.1.0
ISP(config-router)# network 10.2.2.0
ISP(config-router)# exit
```

#### On R3:
```cisco
R3(config)# router rip
R3(config-router)# version 2
R3(config-router)# network 192.168.30.0
R3(config-router)# network 192.168.40.0
R3(config-router)# network 10.2.2.0
R3(config-router)# exit
```

### Step 5: Verify Connectivity

**Test connectivity before applying ACLs:**

1. From PC-A, ping PC-C: `ping 192.168.30.3` ✅ Should succeed
2. From PC-A, ping R3's loopback: `ping 192.168.40.1` ✅ Should succeed
3. From R1, ping PC-C: `ping 192.168.30.3` ✅ Should succeed
4. From R1, ping R3's loopback: `ping 192.168.40.1` ✅ Should succeed

---

## Part 3: Configure and Verify Standard ACLs

### Understanding Standard ACLs

**Standard ACLs:**
- Filter traffic based on **source IP address only**
- Should be placed **close to the destination**
- Use wildcard masks (inverse subnet masks)
- Have an implicit "deny any" at the end

### Step 1: Configure Numbered Standard ACL

**Objective:** Allow traffic from 192.168.10.0/24 and 192.168.20.0/24 networks to access 192.168.30.0/24 network.

**Planning Questions:**
- **Wildcard mask for 192.168.10.0/24:** `0.0.0.255`
- **Best router placement:** R3 (close to destination)
- **Interface and direction:** G0/1 outbound

#### Configure ACL on R3:
```cisco
R3(config)# access-list 1 remark Allow R1 LANs Access
R3(config)# access-list 1 permit 192.168.10.0 0.0.0.255
R3(config)# access-list 1 permit 192.168.20.0 0.0.0.255
R3(config)# access-list 1 deny any
```

#### Apply ACL to Interface:
```cisco
R3(config)# interface g0/1
R3(config-if)# ip access-group 1 out
R3(config-if)# exit
```

#### Verify ACL Configuration:

**View ACL entries:**
```cisco
R3# show access-lists 1
Standard IP access list 1
    10 permit 192.168.10.0, wildcard bits 0.0.0.255
    20 permit 192.168.20.0, wildcard bits 0.0.0.255
    30 deny any
```

**View ACL application:**
```cisco
R3# show ip interface g0/1
GigabitEthernet0/1 is up, line protocol is up
  Internet address is 192.168.30.1/24
  Outgoing access list is 1
  Inbound access list is not set
```

#### Test the ACL:

**Test 1 - PC-A to PC-C (should work):**
```
PC-A> ping 192.168.30.3
```
✅ **Expected Result:** Success

**Test 2 - R1 Loopback to PC-C (extended ping):**
```cisco
R1# ping
Protocol [ip]: 
Target IP address: 192.168.30.3
Source address or interface: 192.168.20.1
```
✅ **Expected Result:** Success

**Test 3 - R1 to PC-C (regular ping):**
```cisco
R1# ping 192.168.30.3
```
❌ **Expected Result:** Failure (source IP 10.1.1.1 not permitted)

### Step 2: Configure Named Standard ACL

**Objective:** 
- Allow traffic from 192.168.40.0/24 network to access 192.168.10.0/24
- Allow only PC-C (192.168.30.3) to access 192.168.10.0/24

**Planning:**
- **Best router placement:** R1 (close to destination)
- **Interface and direction:** G0/1 outbound

#### Configure Named ACL on R1:
```cisco
R1(config)# ip access-list standard BRANCH-OFFICE-POLICY
R1(config-std-nacl)# permit host 192.168.30.3
R1(config-std-nacl)# permit 192.168.40.0 0.0.0.255
R1(config-std-nacl)# exit
```

**Alternative syntax for host:**
```cisco
R1(config-std-nacl)# permit 192.168.30.3 0.0.0.0
```

#### Apply Named ACL:
```cisco
R1(config)# interface g0/1
R1(config-if)# ip access-group BRANCH-OFFICE-POLICY out
R1(config-if)# exit
```

#### Verify Named ACL:

**View ACL:**
```cisco
R1# show access-lists
Standard IP access list BRANCH-OFFICE-POLICY
    10 permit 192.168.30.3
    20 permit 192.168.40.0, wildcard bits 0.0.0.255
```

**Note:** There's an implicit "deny any" even though it doesn't appear in the output.

#### Test Named ACL:

**Test 1 - PC-C to PC-A:**
```
PC-C> ping 192.168.10.3
```
✅ **Expected Result:** Success

**Test 2 - R3 G0/1 interface to PC-A (should fail):**
```cisco
R3# ping
Target IP address: 192.168.10.3
Source address or interface: 192.168.30.1
```
❌ **Expected Result:** Failure

**Test 3 - R3 Loopback to PC-A (should work):**
```cisco
R3# ping
Target IP address: 192.168.10.3
Source address or interface: 192.168.40.1
```
✅ **Expected Result:** Success

---

## Part 4: Modify a Standard ACL

### Scenario
Management wants to:
1. Allow 209.165.200.224/27 network full access to 192.168.10.0/24
2. Add explicit "deny any" statement to all ACLs

### Method: Modify ACL In-Place

Instead of removing and recreating the entire ACL, we'll add specific lines.

#### Add New ACL Entries:
```cisco
R1(config)# ip access-list standard BRANCH-OFFICE-POLICY
R1(config-std-nacl)# 30 permit 209.165.200.224 0.0.0.31
R1(config-std-nacl)# 40 deny any
R1(config-std-nacl)# exit
```

#### Verify Modified ACL:
```cisco
R1# show access-lists
Standard IP access list BRANCH-OFFICE-POLICY
    10 permit 192.168.30.3 (8 matches)
    20 permit 192.168.40.0, wildcard bits 0.0.0.255 (5 matches)
    30 permit 209.165.200.224, wildcard bits 0.0.0.31
    40 deny any
```

**Note:** The ACL remains applied to interface G0/1 automatically.

#### Test Modified ACL:
```cisco
ISP# ping
Target IP address: 192.168.10.3
Source address or interface: 209.165.200.225
```
✅ **Expected Result:** Success

---

## Key Concepts and Best Practices

### Wildcard Masks
| Subnet Mask | Wildcard Mask |
|-------------|---------------|
| 255.255.255.0 | 0.0.0.255 |
| 255.255.255.252 | 0.0.0.3 |
| 255.255.255.224 | 0.0.0.31 |

### ACL Placement Rules
- **Standard ACLs:** Place close to destination
- **Extended ACLs:** Place close to source

### Common Show Commands
```cisco
# View all ACLs
show access-lists

# View specific ACL
show access-lists 1
show access-lists BRANCH-OFFICE-POLICY

# View interface ACL application
show ip interface g0/1
show ip interface
```

### ACL Modification Options

**Option 1: Remove and Recreate**
```cisco
R1(config)# no ip access-list standard BRANCH-OFFICE-POLICY
# Then recreate entire ACL
```

**Option 2: Modify In-Place (Recommended)**
```cisco
R1(config)# ip access-list standard BRANCH-OFFICE-POLICY
R1(config-std-nacl)# 30 permit 209.165.200.224 0.0.0.31
```

### Troubleshooting Tips

1. **Always test connectivity BEFORE applying ACLs**
2. **Remember the implicit "deny any" at the end**
3. **Use specific line numbers when modifying ACLs**
4. **Test with extended ping to specify source addresses**
5. **Check ACL hit counters with `show access-lists`**

---

## Lab Reflection Questions

### 1. Why use Extended ACLs?
Standard ACLs only filter based on source address and allow/deny ALL traffic. Extended ACLs provide granular control by filtering based on:
- Source and destination addresses
- Specific protocols (TCP, UDP, ICMP)
- Port numbers
- More complex matching criteria

### 2. Benefits of Named ACLs
- **Descriptive names** for better documentation
- **Easy modification** of specific lines
- **Better organization** in complex networks
- **Reduced errors** when managing multiple ACLs

---

## Router Interface Reference

| Router Model | Ethernet #1 | Ethernet #2 | Serial #1 | Serial #2 |
|--------------|-------------|-------------|-----------|-----------|
| 1800 | Fast Ethernet 0/0 | Fast Ethernet 0/1 | Serial 0/0/0 | Serial 0/0/1 |
| 1900 | Gigabit Ethernet 0/0 | Gigabit Ethernet 0/1 | Serial 0/0/0 | Serial 0/0/1 |
| 2801 | Fast Ethernet 0/0 | Fast Ethernet 0/1 | Serial 0/1/0 | Serial 0/1/1 |
| 2811 | Fast Ethernet 0/0 | Fast Ethernet 0/1 | Serial 0/0/0 | Serial 0/0/1 |
| 2900 | Gigabit Ethernet 0/0 | Gigabit Ethernet 0/1 | Serial 0/0/0 | Serial 0/0/1 |

---

## Summary

This lab provided hands-on experience with:
- Standard IPv4 ACL configuration and application
- Both numbered and named ACL syntax
- ACL placement best practices
- Testing and verification procedures
- In-place ACL modification techniques

Understanding ACLs is crucial for network security and traffic control in enterprise environments.
