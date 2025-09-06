# Complete Guide: Configuring IPv4 Static and Default Routes

## Table of Contents
1. [Lab Overview](#lab-overview)
2. [Topology and Addressing](#topology-and-addressing)
3. [Required Equipment](#required-equipment)
4. [Part 1: Setup and Initialization](#part-1-setup-and-initialization)
5. [Part 2: Basic Configuration](#part-2-basic-configuration)
6. [Part 3: Static Route Configuration](#part-3-static-route-configuration)
7. [Part 4: Default Route Configuration](#part-4-default-route-configuration)
8. [Troubleshooting and Verification](#troubleshooting-and-verification)
9. [Key Commands Reference](#key-commands-reference)
10. [Reflection Questions](#reflection-questions)

## Lab Overview

### Objectives
- Set up network topology and initialize devices
- Configure basic device settings and verify connectivity
- Configure different types of static routes:
  - Recursive static routes
  - Directly connected static routes
- Configure and verify default routes
- Remove and modify static routes

### Background
A router uses a routing table to determine where to send packets. Initially, the routing table contains only directly connected networks. To communicate with distant networks, routes must be manually configured and added to the routing table.



### Addressing Table
| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|-----------------|
| R1 | G0/1 | 192.168.0.1 | 255.255.255.0 | N/A |
| R1 | S0/0/1 | 10.1.1.1 | 255.255.255.252 | N/A |
| R3 | G0/1 | 192.168.1.1 | 255.255.255.0 | N/A |
| R3 | S0/0/0 (DCE) | 10.1.1.2 | 255.255.255.252 | N/A |
| R3 | Lo0 | 209.165.200.225 | 255.255.255.224 | N/A |
| R3 | Lo1 | 198.133.219.1 | 255.255.255.0 | N/A |
| PC-A | NIC | 192.168.0.10 | 255.255.255.0 | 192.168.0.1 |
| PC-C | NIC | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |

## Required Equipment
- 2 Routers (Cisco 1941 with Cisco IOS Release 15.2(4)M3 or comparable)
- 2 Switches (Cisco 2960 with Cisco IOS Release 15.0(2) or comparable)
- 2 PCs (Windows with terminal emulation program)
- Console cables for device configuration
- Ethernet and serial cables as shown in topology

## Part 1: Setup and Initialization

### Step 1: Cable the Network
1. Connect devices according to the topology diagram
2. Use appropriate cable types:
   - Console cables for configuration access
   - Ethernet cables for LAN connections
   - Serial cables for WAN connections

### Step 2: Initialize Devices
1. Erase startup configurations on all routers and switches
2. Reload all devices to ensure clean startup

```bash
Router> enable
Router# erase startup-config
Router# reload
```

## Part 2: Basic Configuration

### Step 1: Configure PC Interfaces
Configure each PC with the appropriate IP address, subnet mask, and default gateway according to the addressing table.

**PC-A Configuration:**
- IP Address: 192.168.0.10
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.0.1

**PC-C Configuration:**
- IP Address: 192.168.1.10
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.1.1

### Step 2: Configure Basic Router Settings

#### Router R1 Configuration
```bash
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# no ip domain-lookup
R1(config)# enable secret class
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# logging synchronous
R1(config-line)# exit
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# copy running-config startup-config
```

#### Router R3 Configuration
```bash
Router> enable
Router# configure terminal
Router(config)# hostname R3
R3(config)# no ip domain-lookup
R3(config)# enable secret class
R3(config)# line console 0
R3(config-line)# password cisco
R3(config-line)# login
R3(config-line)# logging synchronous
R3(config-line)# exit
R3(config)# line vty 0 4
R3(config-line)# password cisco
R3(config-line)# login
R3(config-line)# exit
R3(config)# copy running-config startup-config
```

### Step 3: Configure IP Addresses on Router Interfaces

#### Router R1 Interface Configuration
```bash
R1(config)# interface gigabitethernet 0/1
R1(config-if)# ip address 192.168.0.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# interface serial 0/0/1
R1(config-if)# ip address 10.1.1.1 255.255.255.252
R1(config-if)# no shutdown
R1(config-if)# exit
```

#### Router R3 Interface Configuration
```bash
R3(config)# interface gigabitethernet 0/1
R3(config-if)# ip address 192.168.1.1 255.255.255.0
R3(config-if)# no shutdown
R3(config-if)# exit
R3(config)# interface serial 0/0/0
R3(config-if)# ip address 10.1.1.2 255.255.255.252
R3(config-if)# clock rate 128000
R3(config-if)# no shutdown
R3(config-if)# exit
R3(config)# interface loopback 0
R3(config-if)# ip address 209.165.200.225 255.255.255.224
R3(config-if)# exit
R3(config)# interface loopback 1
R3(config-if)# ip address 198.133.219.1 255.255.255.0
R3(config-if)# exit
```

### Step 4: Verify Basic Connectivity

#### Test LAN Connectivity
1. From PC-A, ping the default gateway (192.168.0.1)
2. From PC-C, ping the default gateway (192.168.1.1)
3. From R1, ping R3's serial interface (10.1.1.2)

#### Test Connectivity to Distant Networks
At this point, the following pings should **FAIL**:
- From PC-A to PC-C (192.168.1.10)
- From PC-A to Lo0 (209.165.200.225)
- From PC-A to Lo1 (198.133.219.1)

**Why do these fail?** The routers only know about directly connected networks and have no routes to distant networks.

### Step 5: Examine Routing Tables

#### View R1 Routing Table
```bash
R1# show ip route
```

**Expected Output:**
- 10.1.1.0/30 (directly connected via Serial0/0/1)
- 192.168.0.0/24 (directly connected via GigabitEthernet0/1)

**Missing Networks:** 192.168.1.0, 198.133.219.0, 209.165.200.224

#### View R3 Routing Table
```bash
R3# show ip route
```

**Expected Output:**
- 10.1.1.0/30 (directly connected via Serial0/0/0)
- 192.168.1.0/24 (directly connected via GigabitEthernet0/1)
- 198.133.219.0/24 (directly connected via Loopback1)
- 209.165.200.224/27 (directly connected via Loopback0)

**Missing Networks:** 192.168.0.0

## Part 3: Static Route Configuration

### Understanding Static Route Types

#### 1. Recursive Static Route
- Uses next-hop IP address
- Router performs multiple routing table lookups
- Syntax: `ip route network-address subnet-mask next-hop-ip`

#### 2. Directly Connected Static Route
- Uses exit interface
- Router resolves forwarding decision in one lookup
- Syntax: `ip route network-address subnet-mask exit-interface`

### Step 1: Configure Recursive Static Route

#### On Router R1
Configure a static route to the 192.168.1.0 network using R3's serial IP as next-hop:

```bash
R1(config)# ip route 192.168.1.0 255.255.255.0 10.1.1.2
```

#### Verify the Route
```bash
R1# show ip route
```

**Expected Output:**
```
S    192.168.1.0/24 [1/0] via 10.1.1.2
```

#### Test Connectivity
From PC-A, ping PC-C (192.168.1.10) - This should **FAIL** because R3 doesn't have a return route to 192.168.0.0.

### Step 2: Configure Directly Connected Static Route

#### On Router R3
Configure a static route to the 192.168.0.0 network using the exit interface:

```bash
R3(config)# ip route 192.168.0.0 255.255.255.0 serial 0/0/0
```

#### Verify the Route
```bash
R3# show ip route
```

**Expected Output:**
```
S    192.168.0.0/24 is directly connected, Serial0/0/0
```

#### Test Connectivity
From PC-A, ping PC-C (192.168.1.10) - This should now **SUCCEED**.

### Step 3: Configure Additional Static Routes

#### Configure Routes to Loopback Networks on R1

**Option 1: Using Exit Interface**
```bash
R1(config)# ip route 198.133.219.0 255.255.255.0 serial 0/0/1
R1(config)# ip route 209.165.200.224 255.255.255.224 serial 0/0/1
```

**Option 2: Using Next-Hop IP**
```bash
R1(config)# ip route 198.133.219.0 255.255.255.0 10.1.1.2
R1(config)# ip route 209.165.200.224 255.255.255.224 10.1.1.2
```

#### Verify Configuration
```bash
R1# show ip route
```

#### Test Connectivity
From PC-A, ping 198.133.219.1 and 209.165.200.225 - Both should succeed.

### Step 4: Remove Static Routes

#### Remove Loopback Routes from R1
```bash
R1(config)# no ip route 209.165.200.224 255.255.255.224
R1(config)# no ip route 198.133.219.0 255.255.255.0
```

**Note:** You can remove a static route without specifying the next-hop or exit interface.

#### Verify Removal
```bash
R1# show ip route
```

The loopback network routes should no longer appear in the routing table.

## Part 4: Default Route Configuration

### Understanding Default Routes
A default route identifies the gateway to send packets when no specific route exists. It uses the destination 0.0.0.0/0 (quad zero route).

### Configure Default Route on R1

```bash
R1(config)# ip route 0.0.0.0 0.0.0.0 serial 0/0/1
```

### Verify Default Route

```bash
R1# show ip route
```

**Expected Output:**
```
Gateway of last resort is 0.0.0.0 to network 0.0.0.0
S*   0.0.0.0/0 is directly connected, Serial0/0/1
```

### Test Default Route Functionality

From PC-A, test connectivity to:
- 209.165.200.225 (should succeed)
- 198.133.219.1 (should succeed)

These pings succeed because the default route forwards packets to unknown destinations through the serial interface.

## Troubleshooting and Verification

### Common Commands for Verification

#### Interface Status
```bash
Router# show ip interface brief
```

#### Routing Table
```bash
Router# show ip route
Router# show ip route static
```

#### Specific Route Information
```bash
Router# show ip route 192.168.1.0
```

#### Connectivity Testing
```bash
Router# ping destination-ip
Router# traceroute destination-ip
```

### Common Issues and Solutions

#### Issue 1: Ping Fails Between PCs
**Possible Causes:**
- Missing return route on destination router
- Incorrect static route configuration
- Interface down

**Solution:**
- Verify static routes on both routers
- Check interface status
- Ensure proper IP addressing

#### Issue 2: Static Route Not Appearing in Routing Table
**Possible Causes:**
- Incorrect syntax in static route command
- Exit interface is down
- Next-hop IP address unreachable

**Solution:**
- Verify command syntax
- Check interface status with `show ip interface brief`
- Verify next-hop reachability

#### Issue 3: Default Route Not Working
**Possible Causes:**
- Incorrect default route configuration
- Exit interface down
- Conflicting routes

**Solution:**
- Verify default route syntax: `ip route 0.0.0.0 0.0.0.0 [next-hop or exit-interface]`
- Check interface status
- Review routing table for conflicts

## Key Commands Reference

### Static Route Configuration
```bash
# Recursive static route
ip route network subnet-mask next-hop-ip

# Directly connected static route
ip route network subnet-mask exit-interface

# Default route with next-hop
ip route 0.0.0.0 0.0.0.0 next-hop-ip

# Default route with exit interface
ip route 0.0.0.0 0.0.0.0 exit-interface

# Remove static route
no ip route network subnet-mask [next-hop-ip | exit-interface]
```

### Verification Commands
```bash
# Show routing table
show ip route

# Show only static routes
show ip route static

# Show interface status
show ip interface brief

# Test connectivity
ping destination-ip
traceroute destination-ip
```

### Basic Configuration Commands
```bash
# Set hostname
hostname name

# Disable DNS lookup
no ip domain-lookup

# Set enable password
enable secret password

# Configure console access
line console 0
password password
login
logging synchronous

# Configure VTY access
line vty 0 4
password password
login

# Save configuration
copy running-config startup-config
```

## Reflection Questions

### 1. New Network Configuration
**Question:** A new network 192.168.3.0/24 is connected to interface G0/0 on R1. What commands could be used to configure a static route to that network from R3?

**Possible Answers:**
```bash
# Option 1: Next-hop IP
R3(config)# ip route 192.168.3.0 255.255.255.0 10.1.1.1

# Option 2: Exit interface
R3(config)# ip route 192.168.3.0 255.255.255.0 serial 0/0/0

# Option 3: Default route
R3(config)# ip route 0.0.0.0 0.0.0.0 serial 0/0/0
```

### 2. Directly Connected vs. Recursive Static Routes
**Question:** Is there a benefit to configuring a directly connected static route instead of a recursive static route?

**Answer:** Yes, directly connected static routes allow the routing table to resolve the exit interface in a single lookup, while recursive static routes require two lookups (first to find the next-hop, then to find the exit interface for that next-hop).

### 3. Importance of Default Routes
**Question:** Why is it important to configure a default route on a router?

**Answer:** A default route prevents the router from dropping packets destined for unknown networks. Without a default route, packets to networks not in the routing table are discarded, breaking connectivity to distant networks.

## Best Practices

### Route Configuration
1. Use directly connected static routes for point-to-point links when possible
2. Implement default routes on edge routers
3. Document all static routes for maintenance purposes
4. Regularly verify routing tables after configuration changes

### Security Considerations
1. Use strong passwords for device access
2. Disable unused interfaces
3. Implement access control lists where appropriate
4. Regularly update device firmware

### Troubleshooting Methodology
1. Verify physical connectivity first
2. Check IP addressing and subnet masks
3. Examine routing tables systematically
4. Test connectivity step-by-step from source to destination
5. Use traceroute to identify where packets are dropped

## Conclusion

This lab demonstrates the fundamental concepts of static routing in IPv4 networks. By completing this exercise, you have learned to:

- Configure different types of static routes
- Understand the difference between recursive and directly connected routes
- Implement default routing for unknown destinations
- Troubleshoot common routing issues
- Verify network connectivity using various commands

Static routing is essential for small networks and specific routing scenarios where dynamic routing protocols may not be necessary or desired. The concepts learned in this lab form the foundation for understanding more complex routing protocols and network designs.
