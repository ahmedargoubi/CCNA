# Complete Guide: Configuring IPv6 Static and Default Routes Lab

## Lab Overview

This lab teaches you how to configure IPv6 addressing, static routes, and default routes on Cisco routers. You'll work with a network that uses only IPv6 addressing and learn three different types of IPv6 routing configurations.

### Network Topology
- **Router R1** connected to **PC-A** via switch
- **Router R3** connected to **PC-C** via switch
- **R1** and **R3** connected via serial link

### Learning Objectives
1. Configure IPv6 addressing on routers and PCs
2. Understand IPv6 SLAAC (Stateless Address Auto-configuration)
3. Configure three types of IPv6 static routes:
   - Directly connected static routes
   - Recursive static routes
   - Default static routes

---

## Equipment Required
- 2 Cisco 1941 routers (or compatible)
- 2 Cisco 2960 switches (or compatible)
- 2 PCs with Windows
- Console cables
- Ethernet and serial cables

---

## Part 1: Network Setup and Basic IPv6 Configuration

### Step 1: Physical Setup
1. Cable the network according to the topology
2. Connect console cables to routers for configuration
3. Ensure all devices are powered on and accessible

### Step 2: Router IPv6 Configuration

#### Configure Router R1:

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# ipv6 unicast-routing
```

**Configure Interfaces:**
```cisco
R1(config)# interface gigabitethernet 0/1
R1(config-if)# ipv6 address 2001:DB8:ACAD:A::/64 eui-64
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface serial 0/0/1
R1(config-if)# ipv6 address FC00::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
```

#### Configure Router R3:

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname R3
R3(config)# ipv6 unicast-routing
```

**Configure Interfaces:**
```cisco
R3(config)# interface gigabitethernet 0/1
R3(config-if)# ipv6 address 2001:DB8:ACAD:B::/64 eui-64
R3(config-if)# no shutdown
R3(config-if)# exit

R3(config)# interface serial 0/0/0
R3(config-if)# ipv6 address FC00::2/64
R3(config-if)# clock rate 128000
R3(config-if)# no shutdown
R3(config-if)# exit
```

### Step 3: PC IPv6 Configuration

#### On both PC-A and PC-C:

1. **Access Network Settings:**
   - Go to Start Menu → Control Panel → Network and Sharing Center
   - Click "Change adapter settings"

2. **Configure Network Adapter:**
   - Double-click "Local Area Connection"
   - Click "Properties"
   - **Uncheck** "Internet Protocol Version 4 (TCP/IPv4)"
   - **Check** "Internet Protocol Version 6 (TCP/IPv6)"
   - Click "Properties"

3. **Enable SLAAC:**
   - Select "Obtain an IPv6 address automatically"
   - Select "Obtain DNS server address automatically"
   - Click OK

### Step 4: Verify IPv6 Configuration

#### On PC-A and PC-C:
```cmd
C:\> ipconfig /all
```

**Expected Output Elements:**
- IPv6 global unicast address (2001:db8:acad:a:xxxx:xxxx:xxxx:xxxx)
- Link-local IPv6 address (fe80::xxxx:xxxx:xxxx:xxxx)
- Default gateway (router's link-local address)

#### Test Local Connectivity:
```cmd
C:\> ping -6 [default-gateway-address]
```

#### On Routers - Verify Interface Status:
```cisco
R1# show ipv6 interface brief
R1# show ipv6 interface
R1# show ipv6 route
```

---

## Part 2: IPv6 Static Route Configuration

At this point, PC-A cannot ping PC-C because routers don't know about remote networks. You'll configure static routes to enable connectivity.

### Method 1: Directly Connected Static Routes

A directly connected static route specifies the outgoing interface.

#### Configure on R1:
```cisco
R1(config)# ipv6 route 2001:DB8:ACAD:B::/64 serial 0/0/1
```

#### Configure on R3:
```cisco
R3(config)# ipv6 route 2001:DB8:ACAD:A::/64 serial 0/0/0
```

#### Verify Configuration:
```cisco
R1# show ipv6 route
```

**Look for:** `S 2001:DB8:ACAD:B::/64 [1/0] via Serial0/0/1, directly connected`

#### Test Connectivity:
```cmd
C:\> ping -6 [PC-C-IPv6-address]
```

### Method 2: Recursive Static Routes

A recursive static route specifies the next-hop IPv6 address instead of the outgoing interface.

#### Remove Previous Routes and Add Recursive Routes:

**On R1:**
```cisco
R1(config)# no ipv6 route 2001:DB8:ACAD:B::/64 serial 0/0/1
R1(config)# ipv6 route 2001:DB8:ACAD:B::/64 FC00::2
```

**On R3:**
```cisco
R3(config)# no ipv6 route 2001:DB8:ACAD:A::/64 serial 0/0/0
R3(config)# ipv6 route 2001:DB8:ACAD:A::/64 FC00::1
```

#### Verify Configuration:
```cisco
R1# show ipv6 route
```

**Look for:** `S 2001:DB8:ACAD:B::/64 [1/0] via FC00::2`

### Method 3: Default Static Routes

A default route matches any destination network not specifically listed in the routing table.

#### Configure Default Routes:

**On R1:**
```cisco
R1(config)# no ipv6 route 2001:DB8:ACAD:B::/64 FC00::2
R1(config)# ipv6 route ::/0 serial 0/0/1
```

**On R3:**
```cisco
R3(config)# no ipv6 route 2001:DB8:ACAD:A::/64 FC00::1
R3(config)# ipv6 route ::/0 serial 0/0/0
```

#### Verify Configuration:
```cisco
R1# show ipv6 route
```

**Look for:** `S ::/0 [1/0] via Serial0/0/1, directly connected`

---

## Key IPv6 Concepts Explained

### IPv6 Address Types

1. **Global Unicast Address:**
   - Format: 2001:DB8:ACAD:A::/64
   - Routable on the internet
   - Similar to public IPv4 addresses

2. **Link-Local Address:**
   - Format: FE80::/64
   - Used for local network communication
   - Automatically generated on IPv6-enabled interfaces

3. **Unique Local Address:**
   - Format: FC00::/7
   - Private addressing for internal networks
   - Similar to RFC 1918 addresses in IPv4

### IPv6 Multicast Groups

- **FF02::1** - All nodes on local network
- **FF02::2** - All routers on local network
- **FF02::1:FFxx:xxxx** - Solicited-node multicast (for neighbor discovery)

### EUI-64 Process

When using `eui-64`, the router creates the interface identifier portion of the IPv6 address using the MAC address:
1. Takes the 48-bit MAC address
2. Inserts FFFE in the middle
3. Flips the 7th bit
4. Creates a 64-bit interface identifier

---

## Troubleshooting Tips

### Common Issues and Solutions

1. **PC not receiving IPv6 address:**
   - Verify `ipv6 unicast-routing` is enabled on router
   - Check that interface is not shutdown
   - Ensure IPv6 is enabled on PC interface

2. **Pings failing between PCs:**
   - Check routing table: `show ipv6 route`
   - Verify static routes are configured on both routers
   - Disable Windows firewall if necessary

3. **Interface showing as down:**
   - Use `no shutdown` command
   - Check physical connections
   - Verify correct interface names

### Useful Verification Commands

```cisco
# Interface status
show ipv6 interface brief
show ipv6 interface [interface-name]

# Routing information
show ipv6 route
show ipv6 route static

# IPv6 neighbors (similar to ARP in IPv4)
show ipv6 neighbors

# General IPv6 information
show ipv6 protocols
```

---

## Lab Completion Checklist

- [ ] Both routers have IPv6 unicast routing enabled
- [ ] All interfaces are configured with correct IPv6 addresses
- [ ] PCs are configured for SLAAC and receiving IPv6 addresses
- [ ] PC-A can ping its default gateway (R1)
- [ ] PC-C can ping its default gateway (R3)
- [ ] Static routes are configured on both routers
- [ ] PC-A can successfully ping PC-C
- [ ] Routing tables show correct static route entries

---

## Understanding Route Types Summary

| Route Type | Command Format | When to Use |
|------------|----------------|-------------|
| **Directly Connected** | `ipv6 route network/prefix interface` | Point-to-point links, when you want to specify exact outgoing interface |
| **Recursive** | `ipv6 route network/prefix next-hop-address` | When you want to specify the next router's address |
| **Default** | `ipv6 route ::/0 interface-or-next-hop` | To route all unknown destinations to a single path |

This lab demonstrates the fundamental concepts of IPv6 routing and prepares you for more advanced IPv6 networking topics. The skills learned here are essential for modern network administration as IPv6 adoption continues to grow worldwide.