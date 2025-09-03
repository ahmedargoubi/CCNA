# Complete Router-on-a-Stick Inter-VLAN Routing Lab Guide

## Lab Overview

This lab demonstrates how to configure Router-on-a-Stick Inter-VLAN routing using a Cisco router and switches. The setup allows communication between different VLANs through a single router interface using 802.1Q trunking.

### Topology Information

- **Router:** R1 (Cisco 4221)
- **Switches:** S1 and S2 (Cisco 2960)
- **PCs:** PC-A and PC-B

### Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|----------------|
| R1 | G0/0/1.3 | 192.168.3.1 | 255.255.255.0 | N/A |
| R1 | G0/0/1.4 | 192.168.4.1 | 255.255.255.0 | N/A |
| R1 | G0/0/1.8 | N/A | N/A | N/A |
| S1 | VLAN 3 | 192.168.3.11 | 255.255.255.0 | 192.168.3.1 |
| S2 | VLAN 3 | 192.168.3.12 | 255.255.255.0 | 192.168.3.1 |
| PC-A | NIC | 192.168.3.3 | 255.255.255.0 | 192.168.3.1 |
| PC-B | NIC | 192.168.4.3 | 255.255.255.0 | 192.168.4.1 |

### VLAN Table

| VLAN | Name | Interface Assigned |
|------|------|-------------------|
| 3 | Management | S1: VLAN 3, S2: VLAN 3, S1: F0/6 |
| 4 | Operations | S2: F0/18 |
| 7 | ParkingLot | S1: F0/2-4, F0/7-24, G0/1-2; S2: F0/2-17, F0/19-24, G0/1-2 |
| 8 | Native | N/A |

---

## Part 1: Build the Network and Configure Basic Device Settings

### Step 2: Configure Basic Settings for Router R1

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# no ip domain-lookup
R1(config)# enable secret class
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# service password-encryption
R1(config)# banner motd #
Enter TEXT message. End with the character '#'.
Unauthorized access is strictly prohibited. #
R1(config)# exit
R1# copy running-config startup-config
R1# clock set 14:30:00 03 September 2025
```

### Step 3: Configure Basic Settings for Switch S1

```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname S1
S1(config)# no ip domain-lookup
S1(config)# enable secret class
S1(config)# line console 0
S1(config-line)# password cisco
S1(config-line)# login
S1(config-line)# exit
S1(config)# line vty 0 15
S1(config-line)# password cisco
S1(config-line)# login
S1(config-line)# exit
S1(config)# service password-encryption
S1(config)# banner motd #
Enter TEXT message. End with the character '#'.
Unauthorized access is strictly prohibited. #
S1(config)# exit
S1# clock set 14:30:00 03 September 2025
S1# copy running-config startup-config
```

### Configure Basic Settings for Switch S2

```cisco
Switch> enable
Switch# configure terminal
Switch(config)# hostname S2
S2(config)# no ip domain-lookup
S2(config)# enable secret class
S2(config)# line console 0
S2(config-line)# password cisco
S2(config-line)# login
S2(config-line)# exit
S2(config)# line vty 0 15
S2(config-line)# password cisco
S2(config-line)# login
S2(config-line)# exit
S2(config)# service password-encryption
S2(config)# banner motd #
Enter TEXT message. End with the character '#'.
Unauthorized access is strictly prohibited. #
S2(config)# exit
S2# clock set 14:30:00 03 September 2025
S2# copy running-config startup-config
```

### Step 4: Configure PC Hosts

**PC-A Configuration:**
- IP Address: 192.168.3.3
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.3.1

**PC-B Configuration:**
- IP Address: 192.168.4.3
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.4.1

---

## Part 2: Create VLANs and Assign Switch Ports

### Step 1: Create VLANs on Switch S1

```cisco
S1# configure terminal
S1(config)# vlan 3
S1(config-vlan)# name Management
S1(config-vlan)# exit
S1(config)# vlan 4
S1(config-vlan)# name Operations
S1(config-vlan)# exit
S1(config)# vlan 7
S1(config-vlan)# name ParkingLot
S1(config-vlan)# exit
S1(config)# vlan 8
S1(config-vlan)# name Native
S1(config-vlan)# exit

# Configure management interface
S1(config)# interface vlan 3
S1(config-if)# ip address 192.168.3.11 255.255.255.0
S1(config-if)# no shutdown
S1(config-if)# exit
S1(config)# ip default-gateway 192.168.3.1

# Assign unused ports to ParkingLot VLAN
S1(config)# interface range f0/2-4, f0/7-24, g0/1-2
S1(config-if-range)# switchport mode access
S1(config-if-range)# switchport access vlan 7
S1(config-if-range)# shutdown
S1(config-if-range)# exit
```

### Create VLANs on Switch S2

```cisco
S2# configure terminal
S2(config)# vlan 3
S2(config-vlan)# name Management
S2(config-vlan)# exit
S2(config)# vlan 4
S2(config-vlan)# name Operations
S2(config-vlan)# exit
S2(config)# vlan 7
S2(config-vlan)# name ParkingLot
S2(config-vlan)# exit
S2(config)# vlan 8
S2(config-vlan)# name Native
S2(config-vlan)# exit

# Configure management interface
S2(config)# interface vlan 3
S2(config-if)# ip address 192.168.3.12 255.255.255.0
S2(config-if)# no shutdown
S2(config-if)# exit
S2(config)# ip default-gateway 192.168.3.1

# Assign unused ports to ParkingLot VLAN
S2(config)# interface range f0/2-17, f0/19-24, g0/1-2
S2(config-if-range)# switchport mode access
S2(config-if-range)# switchport access vlan 7
S2(config-if-range)# shutdown
S2(config-if-range)# exit
```

### Step 2: Assign VLANs to Correct Switch Interfaces

**On Switch S1:**
```cisco
S1(config)# interface f0/6
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 3
S1(config-if)# exit
```

**On Switch S2:**
```cisco
S2(config)# interface f0/18
S2(config-if)# switchport mode access
S2(config-if)# switchport access vlan 4
S2(config-if)# exit
```

**Verify VLAN configuration:**
```cisco
S1# show vlan brief
S2# show vlan brief
```

---

## Part 3: Configure 802.1Q Trunk Between Switches

### Step 1: Configure Trunk Interface F0/1

**On Switch S1:**
```cisco
S1(config)# interface f0/1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk native vlan 8
S1(config-if)# switchport trunk allowed vlan 3,4,8
S1(config-if)# exit
```

**On Switch S2:**
```cisco
S2(config)# interface f0/1
S2(config-if)# switchport mode trunk
S2(config-if)# switchport trunk native vlan 8
S2(config-if)# switchport trunk allowed vlan 3,4,8
S2(config-if)# exit
```

**Verify trunk configuration:**
```cisco
S1# show interfaces trunk
S2# show interfaces trunk
```

### Step 2: Configure S1's Trunk Interface F0/5

**On Switch S1:**
```cisco
S1(config)# interface f0/5
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk native vlan 8
S1(config-if)# switchport trunk allowed vlan 3,4,8
S1(config-if)# exit
S1(config)# exit
S1# copy running-config startup-config
```

**Save configuration on S2:**
```cisco
S2# copy running-config startup-config
```

**Verify trunk configuration:**
```cisco
S1# show interfaces trunk
```

### Question Answer:
**Why does F0/5 not appear in the list of trunks?**

**Answer:** F0/5 does not appear in the list of active trunks because there is no device connected to the other end of the link that is configured for trunking. The router interface G0/0/1 has not been configured yet, so the trunk is not operationally up. Trunks only show as active when both ends are properly configured and the link is established.

---

## Part 4: Configure Inter-VLAN Routing on Router

```cisco
R1# configure terminal

# Activate the main interface
R1(config)# interface g0/0/1
R1(config-if)# no shutdown
R1(config-if)# exit

# Configure sub-interface for VLAN 3 (Management)
R1(config)# interface g0/0/1.3
R1(config-subif)# description Management Network
R1(config-subif)# encapsulation dot1q 3
R1(config-subif)# ip address 192.168.3.1 255.255.255.0
R1(config-subif)# exit

# Configure sub-interface for VLAN 4 (Operations)
R1(config)# interface g0/0/1.4
R1(config-subif)# description Operations Network
R1(config-subif)# encapsulation dot1q 4
R1(config-subif)# ip address 192.168.4.1 255.255.255.0
R1(config-subif)# exit

# Configure sub-interface for VLAN 8 (Native)
R1(config)# interface g0/0/1.8
R1(config-subif)# description Native VLAN
R1(config-subif)# encapsulation dot1q 8 native
R1(config-subif)# exit

R1(config)# exit
```

**Verify sub-interfaces:**
```cisco
R1# show ip interface brief
```

---

## Part 5: Verify Inter-VLAN Routing

### Step 1: Tests from PC-A

All the following tests should be successful:

1. **Ping from PC-A to its default gateway:**
   ```
   C:\> ping 192.168.3.1
   ```

2. **Ping from PC-A to PC-B:**
   ```
   C:\> ping 192.168.4.3
   ```

3. **Ping from PC-A to S2:**
   ```
   C:\> ping 192.168.3.12
   ```

### Step 2: Test from PC-B

**From PC-B command prompt:**
```
C:\> tracert 192.168.3.3
```

### Question Answer:
**What intermediate IP addresses are shown in the results?**

**Answer:** The tracert command from PC-B (192.168.4.3) to PC-A (192.168.3.3) will show the following intermediate IP address:

1. 192.168.4.1 (R1's G0/0/1.4 sub-interface - PC-B's default gateway)
2. 192.168.3.3 (PC-A - final destination)

The router's IP address (192.168.4.1) appears as the intermediate hop because the traffic must go through the router to route between VLANs 4 and 3.

---

## Key Commands Summary

### VLAN Configuration
- `vlan [number]` - Create VLAN
- `name [vlan-name]` - Name the VLAN
- `switchport access vlan [number]` - Assign port to VLAN
- `show vlan brief` - Display VLAN information

### Trunk Configuration
- `switchport mode trunk` - Configure interface as trunk
- `switchport trunk native vlan [number]` - Set native VLAN
- `switchport trunk allowed vlan [list]` - Specify allowed VLANs
- `show interfaces trunk` - Display trunk information

### Router Sub-interface Configuration
- `interface g0/0/1.[vlan-id]` - Create sub-interface
- `encapsulation dot1q [vlan-id]` - Configure 802.1Q encapsulation
- `encapsulation dot1q [vlan-id] native` - Configure native VLAN
- `show ip interface brief` - Display interface status

### Verification Commands
- `ping [ip-address]` - Test connectivity
- `tracert [ip-address]` - Trace route path
- `show running-config` - Display current configuration
- `show ip route` - Display routing table

---

## Troubleshooting Tips

1. **No connectivity between VLANs:**
   - Check sub-interface configuration on router
   - Verify trunk configuration on switches
   - Ensure native VLAN matches on all trunk links

2. **Trunk not working:**
   - Verify both sides are configured as trunk
   - Check native VLAN configuration
   - Ensure allowed VLANs include required VLANs

3. **PC cannot reach default gateway:**
   - Verify PC IP configuration
   - Check VLAN assignment on switch port
   - Confirm sub-interface IP address on router

4. **Switch management not accessible:**
   - Verify switch has IP address configured
   - Check default gateway configuration
   - Ensure management VLAN has connectivity to router

---

This completes the Router-on-a-Stick Inter-VLAN Routing lab configuration. All devices should now be able to communicate across VLANs through the router's sub-interfaces.