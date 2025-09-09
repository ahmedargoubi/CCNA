# EtherChannel Configuration Lab Guide

## Topology
```
PC-A ---- S1 ---- S2 ---- PC-B
          |       |
          |       |
          S3 ---- PC-C
```

## Addressing Table

| Device | Interface | IP Address    | Subnet Mask     |
|--------|-----------|---------------|-----------------|
| S1     | VLAN 99   | 192.168.99.11 | 255.255.255.0  |
| S2     | VLAN 99   | 192.168.99.12 | 255.255.255.0  |
| S3     | VLAN 99   | 192.168.99.13 | 255.255.255.0  |
| PC-A   | NIC       | 192.168.10.1  | 255.255.255.0  |
| PC-B   | NIC       | 192.168.10.2  | 255.255.255.0  |
| PC-C   | NIC       | 192.168.10.3  | 255.255.255.0  |

## Objectives

- **Part 1**: Configure basic switch settings
- **Part 2**: Configure PAgP (Port Aggregation Protocol)
- **Part 3**: Configure LACP (Link Aggregation Control Protocol)

## Background/Scenario

Link aggregation allows the creation of logical links composed of a minimum of two physical links. This increases throughput beyond using a single physical link and provides redundancy in case one of the links fails.

In this lab, you will configure EtherChannel, a form of link aggregation used in switched networks. You will configure EtherChannel using both PAgP and LACP protocols.

**Note**: PAgP is a Cisco proprietary protocol that can only be used on Cisco switches and licensed vendor switches that support PAgP. LACP is an IEEE 802.3ad link aggregation protocol that is vendor-neutral.

LACP allows Cisco switches to manage Ethernet channels between 802.3ad compliant switches. You can configure up to 16 ports to form a channel. Eight ports are in active mode and the other eight in standby mode. If one of the active ports fails, a standby port becomes active. Standby mode only works for LACP, not for PAgP.

## Required Resources

- 3 switches (Cisco 2960 with Cisco IOS 15.0(2) lanbasek9 image or similar)
- 3 PCs (Windows 7, Vista, or XP with terminal emulation program like Tera Term)
- Console cables to configure Cisco IOS devices via console ports
- Ethernet cables as per topology

## Part 1: Configure Basic Switch Settings

### Step 1: Cable the network according to topology
Attach the devices according to the topology diagram and connect the cables as needed.

### Step 2: Initialize and restart the switches
Ensure all switches are reset to factory defaults.

### Step 3: Configure basic settings for each switch

For each switch (S1, S2, S3), configure the following:

```cisco
! Disable DNS lookup
Switch(config)# no ip domain-lookup

! Configure hostname according to topology
Switch(config)# hostname S1  ! (or S2, S3 as appropriate)

! Encrypt plain text passwords
S1(config)# service password-encryption

! Create MOTD banner
S1(config)# banner motd #
Unauthorized access is prohibited!
#

! Set privileged EXEC password
S1(config)# enable secret class

! Configure console password
S1(config)# line console 0
S1(config-line)# password cisco
S1(config-line)# login
S1(config-line)# logging synchronous
S1(config-line)# exit

! Configure VTY password
S1(config)# line vty 0 15
S1(config-line)# password cisco
S1(config-line)# login
S1(config-line)# exit

! Create and configure VLANs
S1(config)# vlan 99
S1(config-vlan)# name Management
S1(config-vlan)# exit

S1(config)# vlan 10
S1(config-vlan)# name Staff
S1(config-vlan)# exit

! Configure management VLAN interface
S1(config)# interface vlan 99
S1(config-if)# ip address 192.168.99.11 255.255.255.0  ! Use appropriate IP for each switch
S1(config-if)# no shutdown
S1(config-if)# exit

! Configure access ports for PCs (adjust port numbers as needed)
! For S1 - PC-A connection
S1(config)# interface f0/6
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 10
S1(config-if)# no shutdown
S1(config-if)# exit

! Shutdown unused ports (example for ports f0/5, f0/7-24)
S1(config)# interface range f0/5, f0/7-24
S1(config-if-range)# shutdown
S1(config-if-range)# exit

! Save configuration
S1# copy running-config startup-config
```

**Repeat similar configuration for S2 and S3 with their respective IP addresses and connected PC ports.**

### Step 4: Configure PCs
Configure PC IP addresses according to the addressing table:
- PC-A: 192.168.10.1/24
- PC-B: 192.168.10.2/24
- PC-C: 192.168.10.3/24

## Part 2: Configure PAgP

PAgP is a Cisco proprietary protocol for link aggregation. In Part 2, a link between S1 and S3 will be configured using PAgP.

### Step 1: Configure PAgP on S1 and S3

For the link between S1 and S3, configure ports on S1 with PAgP desirable mode and ports on S3 with PAgP auto mode.

**On S1:**
```cisco
S1(config)# interface range f0/3-4
S1(config-if-range)# channel-group 1 mode desirable
! Creating a port-channel interface Port-channel 1
S1(config-if-range)# no shutdown
```

**On S3:**
```cisco
S3(config)# interface range f0/3-4
S3(config-if-range)# channel-group 1 mode auto
! Creating a port-channel interface Port-channel 1
S3(config-if-range)# no shutdown
```

You should see messages indicating the interfaces are coming up and the port-channel is formed.

### Step 2: Examine the configuration on the ports

Check the current configuration using the following commands:

```cisco
S1# show run interface f0/3
S1# show interfaces f0/3 switchport
```

### Step 3: Verify that the ports have been aggregated

```cisco
S1# show etherchannel summary
```

**Expected output:**
```
Group  Port-channel  Protocol    Ports
------+-------------+-----------+-----------------------------------------------
1      Po1(SU)       PAgP        Fa0/3(P)  Fa0/4(P)
```

**Question**: What do the SU and P flags mean in the EtherChannel summary?
- **SU**: S = Layer 2, U = In use
- **P**: Bundled in port-channel

### Step 4: Configure trunk ports

Once ports are aggregated, commands applied to the port-channel interface affect all bundled links.

**Configure Po1 as trunk on both S1 and S3:**

```cisco
S1(config)# interface port-channel 1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk native vlan 99

S3(config)# interface port-channel 1
S3(config-if)# switchport mode trunk
S3(config-if)# switchport trunk native vlan 99
```

### Step 5: Verify trunk configuration

**a. Check interface configurations:**
```cisco
S1# show run interface f0/3
S1# show run interface f0/4
S1# show run interface port-channel 1
```

**b. Verify trunk and spanning-tree:**
```cisco
S1# show interfaces trunk
S1# show spanning-tree
```

## Part 3: Configure LACP

LACP is an open standard protocol for link aggregation developed by IEEE. In Part 3, the link between S1 and S2 and the link between S2 and S3 will be configured using LACP.

### Step 1: Configure LACP between S1 and S2

**On S1:**
```cisco
S1(config)# interface range f0/1-2
S1(config-if-range)# switchport mode trunk
S1(config-if-range)# switchport trunk native vlan 99
S1(config-if-range)# channel-group 2 mode active
! Creating a port-channel interface Port-channel 2
S1(config-if-range)# no shutdown
```

**On S2:**
```cisco
S2(config)# interface range f0/1-2
S2(config-if-range)# switchport mode trunk
S2(config-if-range)# switchport trunk native vlan 99
S2(config-if-range)# channel-group 2 mode passive
! Creating a port-channel interface Port-channel 2
S2(config-if-range)# no shutdown
```

### Step 2: Verify port aggregation

Use the following command to verify:
```cisco
S1# show etherchannel summary
```

**Question**: What protocol does Po2 use for link aggregation? Which ports are bundled to form Po2?
- **Protocol**: LACP
- **Ports**: Fa0/1 and Fa0/2
- **Verification command**: `show etherchannel summary`

### Step 3: Configure LACP between S2 and S3

**On S2:**
```cisco
S2(config)# interface range f0/3-4
S2(config-if-range)# switchport mode trunk
S2(config-if-range)# switchport trunk native vlan 99
S2(config-if-range)# channel-group 3 mode active
! Creating a port-channel interface Port-channel 3
S2(config-if-range)# no shutdown
```

**On S3:**
```cisco
S3(config)# interface range f0/1-2
S3(config-if-range)# switchport mode trunk
S3(config-if-range)# switchport trunk native vlan 99
S3(config-if-range)# channel-group 3 mode passive
! Creating a port-channel interface Port-channel 3
S3(config-if-range)# no shutdown
```

**b. Verify EtherChannel formation:**
```cisco
S2# show etherchannel summary
S3# show etherchannel summary
```

### Step 4: Verify end-to-end connectivity

Test connectivity between all devices in the same VLAN using ping commands. If connectivity fails, troubleshoot until end-to-end connectivity is achieved.

**Note**: You may need to disable PC firewalls to ping between PCs.

## Verification Commands

### Key verification commands:
- `show etherchannel summary` - Shows EtherChannel status and member ports
- `show interfaces trunk` - Displays trunk port information
- `show spanning-tree` - Shows spanning-tree information including port costs
- `show run interface <interface-id>` - Shows running configuration for specific interface
- `show interfaces <interface-id> switchport` - Shows switchport configuration details

### EtherChannel Modes:

**PAgP Modes:**
- `desirable` - Actively attempts to form EtherChannel
- `auto` - Passively waits for PAgP negotiation

**LACP Modes:**
- `active` - Actively attempts to form EtherChannel
- `passive` - Passively waits for LACP negotiation

## General Reflection

**Question**: What could prevent EtherChannel formation?

**Common issues that prevent EtherChannel formation:**
- Mismatched protocols (PAgP vs LACP)
- Incompatible modes (both sides passive)
- Different VLAN configurations on member ports
- Speed/duplex mismatches
- Different native VLAN configurations
- STP port states
- Different switchport modes (access vs trunk)
- Maximum number of EtherChannels exceeded

## Troubleshooting Tips

1. Ensure all member ports have identical configurations
2. Verify protocol compatibility between switches
3. Check that at least one side is in active/desirable mode
4. Confirm VLAN configurations match on both ends
5. Use `show etherchannel summary` to check port states
6. Verify physical connectivity and cable integrity
7. Check for STP blocking states that might affect formation