# Configuring and Verifying Extended IPv4 ACLs - Complete Lab Guide

## Lab Overview

This lab demonstrates the configuration and implementation of Extended Access Control Lists (ACLs) which provide granular control over network traffic. Unlike standard ACLs that only filter on source addresses, extended ACLs can filter based on source/destination addresses, protocols, and port numbers.



## Addressing Table

| Device | Interface | IP Address      | Subnet Mask     | Default Gateway |
|--------|-----------|-----------------|-----------------|-----------------|
| R1     | G0/1      | 192.168.10.1    | 255.255.255.0  | N/A            |
| R1     | Lo0       | 192.168.20.1    | 255.255.255.0  | N/A            |
| R1     | S0/0/0    | 10.1.1.1        | 255.255.255.252| N/A            |
| ISP    | S0/0/0    | 10.1.1.2        | 255.255.255.252| N/A            |
| ISP    | S0/0/1    | 10.2.2.2        | 255.255.255.252| N/A            |
| ISP    | Lo0       | 209.165.200.225 | 255.255.255.224| N/A            |
| ISP    | Lo1       | 209.165.201.1   | 255.255.255.224| N/A            |
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
3. **Part 3**: Configure and verify extended numbered and named ACLs
4. **Part 4**: Modify and verify extended ACLs

## Security Policies to Implement

1. Allow web traffic from 192.168.10.0/24 network to any network
2. Allow SSH connection to R3 serial interface from PC-A
3. Allow users on 192.168.10.0/24 network access to 192.168.20.0/24 network
4. Allow web traffic from 192.168.30.0/24 network to R1 and ISP's 209.165.200.224/27 network only

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

### Step 2: Configure Router R1

```cisco
Router> enable
Router# configure terminal
Router(config)# no ip domain-lookup
Router(config)# hostname R1
Router(config)# enable secret class

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

# Configure console and VTY access
Router(config)# line con 0
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# logging synchronous
Router(config-line)# exit

Router(config)# line vty 0 4
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# logging synchronous
Router(config-line)# exit

# Enable web server for HTTP access
Router(config)# ip http server
Router(config)# ip http authentication local
Router(config)# username admin privilege 15 secret class

Router(config)# copy running-config startup-config
```

### Step 3: Configure Router ISP

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname ISP
Router(config)# no ip domain-lookup
Router(config)# enable secret class

# Configure interfaces
Router(config)# interface loopback 0
Router(config-if)# ip address 209.165.200.225 255.255.255.224
Router(config-if)# exit

Router(config)# interface loopback 1
Router(config-if)# ip address 209.165.201.1 255.255.255.224
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

# Configure access
Router(config)# line con 0
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# logging synchronous
Router(config-line)# exit

Router(config)# line vty 0 4
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# logging synchronous
Router(config-line)# exit

# Enable web server
Router(config)# ip http server
Router(config)# ip http authentication local
Router(config)# username admin privilege 15 secret class

Router(config)# copy running-config startup-config
```

### Step 4: Configure Router R3

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname R3
Router(config)# no ip domain-lookup
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

# Configure SSH access
Router(config)# ip domain-name cisco.com
Router(config)# crypto key generate rsa modulus 1024
Router(config)# username admin privilege 15 secret class

Router(config)# line con 0
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# logging synchronous
Router(config-line)# exit

Router(config)# line vty 0 4
Router(config-line)# login local
Router(config-line)# transport input ssh
Router(config-line)# exit

# Enable web server
Router(config)# ip http server
Router(config)# ip http authentication local

Router(config)# copy running-config startup-config
```

### Step 5: Configure OSPF Routing

#### On R1:
```cisco
R1(config)# router ospf 1
R1(config-router)# network 192.168.10.0 0.0.0.255 area 0
R1(config-router)# network 192.168.20.0 0.0.0.255 area 0
R1(config-router)# network 10.1.1.0 0.0.0.3 area 0
R1(config-router)# exit
```

#### On ISP:
```cisco
ISP(config)# router ospf 1
ISP(config-router)# network 209.165.200.224 0.0.0.31 area 0
ISP(config-router)# network 209.165.201.0 0.0.0.31 area 0
ISP(config-router)# network 10.1.1.0 0.0.0.3 area 0
ISP(config-router)# network 10.2.2.0 0.0.0.3 area 0
ISP(config-router)# exit
```

#### On R3:
```cisco
R3(config)# router ospf 1
R3(config-router)# network 192.168.30.0 0.0.0.255 area 0
R3(config-router)# network 192.168.40.0 0.0.0.255 area 0
R3(config-router)# network 10.2.2.0 0.0.0.3 area 0
R3(config-router)# exit
```

### Step 6: Verify Connectivity (Before ACLs)

**Test all connectivity before applying ACLs:**

1. From PC-A, ping PC-C: `ping 192.168.30.3` ✅
2. From PC-A, ping R3 loopback: `ping 192.168.40.1` ✅
3. From PC-A, ping ISP loopbacks: `ping 209.165.200.225` and `ping 209.165.201.1` ✅
4. Test web access from PC-A to ISP: `http://209.165.200.225` ✅
5. Test web access from PC-C to R1: `http://10.1.1.1` ✅

---

## Part 3: Configure and Verify Extended ACLs

### Extended ACL Fundamentals

**Extended ACL Ranges:**
- Numbered: 100-199 and 2000-2699
- Can filter on: source/destination IP, protocols, port numbers
- Best practice: Place close to source

### Step 1: Configure Numbered Extended ACL on R1

**Objective:** Implement security policies 1 and 2:
- Allow web traffic from 192.168.10.0/24 to any network
- Allow SSH from PC-A to R3 (10.2.2.1)

#### Configure ACL 100:
```cisco
R1(config)# access-list 100 remark Allow Web & SSH Access
R1(config)# access-list 100 permit tcp host 192.168.10.3 host 10.2.2.1 eq 22
R1(config)# access-list 100 permit tcp 192.168.10.0 0.0.0.255 any eq 80
```

**Understanding the commands:**
- `eq 22`: SSH protocol port
- `eq 80`: HTTP protocol port
- `any any`: Any source to any destination

#### Apply ACL to Interface:
```cisco
R1(config)# interface s0/0/0
R1(config-if)# ip access-group 100 out
R1(config-if)# exit
```

**Why S0/0/0 outbound?**
- Placed close to source (192.168.10.0/24 network)
- Prevents blocking access to local networks (192.168.20.0/24)

#### Verify ACL 100:

**View ACL configuration:**
```cisco
R1# show access-lists
Extended IP access list 100
    10 permit tcp host 192.168.10.3 host 10.2.2.1 eq 22 (22 matches)
    20 permit tcp any any eq www (111 matches)
```

**View interface application:**
```cisco
R1# show ip interface s0/0/0
Serial0/0/0 is up, line protocol is up
  Outgoing access list is 100
  Inbound access list is not set
```

#### Test ACL 100:

**Test 1 - Web Access (should work):**
```
PC-A: Open browser → http://209.165.200.225
```
✅ **Expected Result:** Success

**Test 2 - SSH Access (should work):**
```
PC-A: ssh admin@10.2.2.1
```
✅ **Expected Result:** Success

**Test 3 - Ping Test (should fail):**
```
PC-A> ping 10.2.2.1
```
❌ **Expected Result:** Failure due to implicit deny any

### Step 2: Configure Named Extended ACL on R3

**Objective:** Implement security policy 4:
- Allow web traffic from 192.168.30.0/24 to R1 (10.1.1.1) and ISP's 209.165.200.224/27

#### Configure Named ACL:
```cisco
R3(config)# ip access-list extended WEB-POLICY
R3(config-ext-nacl)# permit tcp 192.168.30.0 0.0.0.255 host 10.1.1.1 eq 80
R3(config-ext-nacl)# permit tcp 192.168.30.0 0.0.0.255 209.165.200.224 0.0.0.31 eq 80
R3(config-ext-nacl)# exit
```

#### Apply Named ACL:
```cisco
R3(config)# interface s0/0/1
R3(config-if)# ip access-group WEB-POLICY out
R3(config-if)# exit
```

#### Verify Named ACL:

**Check interface application:**
```cisco
R3# show ip interface s0/0/1
Serial0/0/1 is up, line protocol is up
  Outgoing access list is WEB-POLICY
  Inbound access list is not set
```

#### Test Named ACL:

**Test 1 - Allowed web access:**
```
PC-C: http://209.165.200.225
```
✅ **Expected Result:** Success

**Test 2 - Allowed web access to R1:**
```
PC-C: http://10.1.1.1
```
✅ **Expected Result:** Success

**Test 3 - Blocked web access:**
```
PC-C: http://209.165.201.1
```
❌ **Expected Result:** Failure (not in allowed range)

**Test 4 - Blocked ping:**
```
PC-C> ping 192.168.10.3
```
❌ **Expected Result:** Failure (only web traffic allowed)

---

## Part 4: Modify and Verify Extended ACLs

### Scenario
Management wants to allow ALL traffic between the 192.168.10.0/24 and 192.168.30.0/24 networks.

### Step 1: Modify ACL 100 on R1

#### Check current ACL:
```cisco
R1# show access-lists 100
Extended IP access list 100
    10 permit tcp host 192.168.10.3 host 10.2.2.1 eq 22
    20 permit tcp any any eq www
```

#### Add new line to ACL:
```cisco
R1(config)# ip access-list extended 100
R1(config-ext-nacl)# 30 permit ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
R1(config-ext-nacl)# exit
```

#### Verify modification:
```cisco
R1# show access-lists 100
Extended IP access list 100
    10 permit tcp host 192.168.10.3 host 10.2.2.1 eq 22
    20 permit tcp any any eq www
    30 permit ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
```

### Step 2: Modify WEB-POLICY ACL on R3

#### Check current ACL:
```cisco
R3# show access-lists WEB-POLICY
Extended IP access list WEB-POLICY
    10 permit tcp 192.168.30.0 0.0.0.255 host 10.1.1.1 eq www
    20 permit tcp 192.168.30.0 0.0.0.255 209.165.200.224 0.0.0.31 eq www
```

#### Add new line to ACL:
```cisco
R3(config)# ip access-list extended WEB-POLICY
R3(config-ext-nacl)# 30 permit ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
R3(config-ext-nacl)# exit
```

#### Verify modification:
```cisco
R3# show access-lists WEB-POLICY
Extended IP access list WEB-POLICY
    10 permit tcp 192.168.30.0 0.0.0.255 host 10.1.1.1 eq www
    20 permit tcp 192.168.30.0 0.0.0.255 209.165.200.224 0.0.0.31 eq www
    30 permit ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
```

### Step 3: Test Modified ACLs

**Test PC-to-PC connectivity:**
```
PC-A> ping 192.168.30.3
```
✅ **Expected Result:** Success

```
PC-C> ping 192.168.10.3
```
✅ **Expected Result:** Success

**Why did the ACLs work immediately?**
The ACLs remain applied to their interfaces automatically. No need to reapply the `ip access-group` command.

---

## Extended ACL Syntax Reference

### Basic Extended ACL Syntax
```cisco
access-list {100-199|2000-2699} {permit|deny} protocol source source-wildcard destination destination-wildcard [operator port]
```

### Named Extended ACL Syntax
```cisco
ip access-list extended {name}
{permit|deny} protocol source source-wildcard destination destination-wildcard [operator port]
```

### Common Protocol Examples
```cisco
# Allow HTTP traffic
permit tcp any any eq 80

# Allow HTTPS traffic
permit tcp any any eq 443

# Allow SSH traffic
permit tcp any host 10.1.1.1 eq 22

# Allow all IP traffic
permit ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255

# Allow specific host
permit tcp host 192.168.10.3 any eq 80
```

### Port Number Reference
| Service | Port | Protocol |
|---------|------|----------|
| HTTP    | 80   | TCP      |
| HTTPS   | 443  | TCP      |
| SSH     | 22   | TCP      |
| Telnet  | 23   | TCP      |
| FTP     | 21   | TCP      |
| DNS     | 53   | TCP/UDP  |
| TFTP    | 69   | UDP      |

---

## Troubleshooting Extended ACLs

### Common Show Commands
```cisco
# View all ACLs
show access-lists

# View specific numbered ACL
show access-lists 100

# View specific named ACL
show access-lists WEB-POLICY

# View interface ACL application
show ip interface s0/0/0

# View ACL hit counters
show access-lists (includes match counters)
```

### ACL Placement Guidelines

**Extended ACLs:**
- Place **close to the source**
- More specific rules first
- Remember implicit deny any

**Interface Direction:**
- **Inbound**: Filters traffic entering the interface
- **Outbound**: Filters traffic leaving the interface

### Common Troubleshooting Steps

1. **Verify ACL syntax** with `show access-lists`
2. **Check interface application** with `show ip interface`
3. **Review ACL hit counters** for traffic matching
4. **Test with specific protocols** (ping, telnet, ssh, http)
5. **Consider implicit deny any** at end of ACL

---

## Lab Reflection Questions

### 1. Why is careful planning required for ACLs?
ACLs can unintentionally block legitimate traffic. Improper placement or syntax can cause network connectivity issues and security gaps.

### 2. Standard vs Extended ACLs - Which is better?

**Standard ACLs:**
- ✅ Simple to configure
- ✅ Good for basic source-based filtering
- ❌ Limited granularity (source address only)

**Extended ACLs:**
- ✅ Granular control (source, destination, protocols, ports)
- ✅ Flexible security policies
- ❌ More complex to configure and troubleshoot

**Answer:** Both have their place. Use the simplest ACL that meets your security requirements.

### 3. Why don't OSPF updates get blocked by ACLs?
OSPF hello packets and routing updates originate from the router interfaces themselves, not from traffic passing through the router. ACLs filter traffic passing through the router, not traffic generated by the router.

---

## Best Practices Summary

1. **Plan before implementing** - Document security requirements
2. **Test connectivity first** - Verify network functions before applying ACLs
3. **Place extended ACLs close to source** - More efficient processing
4. **Use descriptive names** - For better documentation and management
5. **Order matters** - More specific rules first
6. **Monitor ACL hit counters** - Verify ACLs are working as expected
7. **Document changes** - Use remark statements for clarity

---

## Key Concepts Learned

- **Extended ACL configuration** and syntax
- **Numbered vs named ACLs** comparison
- **ACL placement strategies** for optimal performance
- **Protocol and port filtering** techniques
- **ACL modification** methods
- **Testing and verification** procedures
- **Troubleshooting** common ACL issues

This lab provides the foundation for implementing sophisticated network security policies using Cisco extended ACLs.
