# EtherChannel Implementation Lab Guide

## Overview

This lab demonstrates the implementation of EtherChannel technology using different protocols and methods. EtherChannel allows multiple physical links to be bundled into a single logical link, providing increased bandwidth and redundancy while preventing bridging loops.

## Learning Objectives

By the end of this lab, you will be able to:
- Configure Dynamic Trunking Protocol (DTP) and understand its security implications
- Implement static EtherChannel configuration
- Configure EtherChannel using Port Aggregation Protocol (PAgP)
- Configure EtherChannel using Link Aggregation Control Protocol (LACP)
- Troubleshoot and verify EtherChannel configurations

## Topology

The lab uses three switches:
- **D1**: Distribution switch 1 (Cisco 3650)
- **D2**: Distribution switch 2 (Cisco 3650) 
- **A1**: Access switch (Cisco 2960+)

Multiple links connect each pair of switches to demonstrate link aggregation.

## Required Equipment

- 2 × Cisco 3650 switches (or comparable)
- 1 × Cisco 2960+ switch (or comparable)
- 1 × PC with terminal emulation software
- Console cables
- Ethernet cables as per topology

## Part 1: Dynamic Trunking Protocol (DTP) Exploration

### Background
DTP is a Cisco proprietary protocol that automatically negotiates trunk formation between switches. While convenient, it poses security risks as attackers could potentially force unauthorized trunk formation.

### Step 1: Initial DTP Investigation

1. **Cable the network** according to the topology diagram

2. **Examine default port behavior** on D1 and A1:

```bash
# On D1
Switch# configure terminal
Switch(config)# hostname D1
D1(config)# end
D1# show interfaces g1/0/5 switchport
```

```bash
# On A1
Switch# configure terminal
Switch(config)# hostname A1
A1(config)# end
A1# show interfaces f0/1 switchport
```

**Key Observations:**
- Administrative Mode: `dynamic auto` (default)
- Operational Mode: `static access`
- Negotiation of Trunking: `On`

### Step 2: DTP Mode Testing

1. **Configure Dynamic Desirable on A1:**

```bash
A1# configure terminal
A1(config)# interface f0/1
A1(config-if)# switchport mode dynamic desirable
A1(config-if)# end
```

2. **Verify trunk formation:**

```bash
A1# show interfaces trunk
D1# show interfaces trunk
```

**Result:** Trunk automatically forms because:
- A1 port = `desirable` (actively requests trunk)
- D1 port = `auto` (accepts trunk if requested)

### Step 3: Static Trunk Configuration

1. **Configure static trunk on D1:**

```bash
D1# configure terminal
D1(config)# interface g1/0/6
D1(config-if)# switchport mode trunk
D1(config-if)# end
```

2. **Observe automatic trunk formation** on A1 f0/2

### Step 4: Securing DTP (Best Practice)

⚠️ **Security Warning:** DTP can be exploited by attackers to gain access to all VLANs on a trunk.

1. **Disable DTP negotiation:**

```bash
D1# configure terminal
D1(config)# interface range g1/0/5-6
D1(config-if-range)# switchport mode trunk
D1(config-if-range)# switchport nonegotiate
D1(config-if-range)# end
```

2. **Test the security:** Shutdown A1 ports, then re-enable them. Notice trunks don't form automatically.

3. **Reset interfaces to defaults:**

```bash
A1# configure terminal
A1(config)# default interface range f0/1-2
```

## Part 2: Basic Device Configuration

Apply the following configurations to each switch:

### D1 Configuration
```bash
hostname D1
banner motd # D1, Implement EtherChannel #
line con 0
exec-timeout 0 0
logging synchronous
exit
interface range g1/0/1-24, g1/1/1-4, g0/0
shutdown
exit
interface range g1/0/1-6
switchport mode trunk
no shutdown
exit
```

### D2 Configuration
```bash
hostname D2
banner motd # D2, Implement EtherChannel #
line con 0
exec-timeout 0 0
logging synchronous
exit
interface range g1/0/1-24, g1/1/1-4, g0/0
shutdown
exit
interface range g1/0/1-6
switchport mode trunk
no shutdown
exit
```

### A1 Configuration
```bash
hostname A1
banner motd # A1, Implement EtherChannel #
line con 0
exec-timeout 0 0
logging synchronous
exit
interface range f0/1-24, g0/1-2
shutdown
exit
interface range f0/1-4
switchport mode trunk
no shutdown
exit
```

**Don't forget to:**
- Set UTC time on each switch
- Save configurations with `copy running-config startup-config`

## Part 3: Static EtherChannel Configuration

### Background
Static EtherChannel uses no negotiation protocol. While functional, it's not recommended for production as there's no health checking mechanism.

### Step 1: Configure Secure Trunking (D2 ↔ A1)

```bash
# On D2
D2# configure terminal
D2(config)# interface range g1/0/5-6
D2(config-if-range)# switchport nonegotiate
D2(config-if-range)# end
```

### Step 2: Create Static EtherChannel

1. **Configure EtherChannel on both switches:**

```bash
# On D2
D2# configure terminal
D2(config)# interface range g1/0/5-6
D2(config-if-range)# channel-group 1 mode on
D2(config-if-range)# end
```

```bash
# On A1
A1# configure terminal
A1(config)# interface range f0/3-4
A1(config-if-range)# channel-group 1 mode on
A1(config-if-range)# end
```

2. **Verify EtherChannel formation:**

```bash
A1# show etherchannel summary
A1# show spanning-tree
```

**Key Observations:**
- Port-channel 1 (Po1) is created
- Individual ports show as `(P)` - bundled in port-channel
- Spanning Tree sees Po1 as single logical link with lower cost

### Step 3: Modify EtherChannel Configuration

⚠️ **Important Rule:** Always make EtherChannel changes at the port-channel interface level, NOT on individual member ports.

1. **Create VLAN 999:**

```bash
# On both D2 and A1
(config)# vlan 999
(config-vlan)# name NATIVE_VLAN
(config-vlan)# exit
```

2. **Configure native VLAN on port-channel:**

```bash
# On both D2 and A1
(config)# interface port-channel 1
(config-if)# switchport trunk native vlan 999
(config-if)# end
```

3. **Verify the change:**

```bash
A1# show interfaces trunk
```

## Part 4: PAgP EtherChannel Implementation

### Background
Port Aggregation Protocol (PAgP) is Cisco's proprietary EtherChannel protocol with two modes:
- **Desirable:** Actively negotiates EtherChannel formation
- **Auto:** Passively accepts EtherChannel if requested

### Step 1: Configure PAgP EtherChannel (D1 ↔ A1)

1. **Create PAgP EtherChannel:**

```bash
# On D1
D1# configure terminal
D1(config)# interface range g1/0/5-6
D1(config-if-range)# channel-group 2 mode desirable non-silent
D1(config-if-range)# end
```

```bash
# On A1
A1# configure terminal
A1(config)# interface range f0/1-2
A1(config-if-range)# channel-group 2 mode desirable non-silent
A1(config-if-range)# end
```

**Note:** `non-silent` option is recommended for switch-to-switch connections.

2. **Verify PAgP EtherChannel:**

```bash
A1# show etherchannel summary
```

**Expected Output:**
```
Group Port-channel Protocol Ports
------+-------------+-----------+-----------------------------------------------
2     Po2(SU)      PAgP       Fa0/1(P) Fa0/2(P)
```

### Step 2: Configure Native VLAN

```bash
# On D1 (create VLAN first)
D1# configure terminal
D1(config)# vlan 999
D1(config-vlan)# name NATIVE_VLAN
D1(config-vlan)# exit

# On both D1 and A1
D1(config)# interface port-channel 2
D1(config-if)# switchport trunk native vlan 999
D1(config-if)# end
```

3. **Verify configuration:**

```bash
D1# show interfaces trunk | include Port\|Po2
```

## Part 5: LACP EtherChannel Implementation

### Background
Link Aggregation Control Protocol (LACP) is an IEEE 802.3ad standard protocol with two modes:
- **Active:** Actively negotiates EtherChannel formation
- **Passive:** Passively accepts EtherChannel if requested

### Step 1: Configure LACP EtherChannel (D1 ↔ D2)

1. **Verify trunk connectivity:**

```bash
D2# show interfaces trunk
```

2. **Create LACP EtherChannel:**

```bash
# On D2
D2# configure terminal
D2(config)# interface range g1/0/1-4
D2(config-if-range)# channel-group 3 mode active
D2(config-if-range)# end
```

```bash
# On D1
D1# configure terminal
D1(config)# interface range g1/0/1-4
D1(config-if-range)# channel-group 3 mode active
D1(config-if-range)# end
```

3. **Verify LACP EtherChannel:**

```bash
D2# show etherchannel summary
D2# show spanning-tree
```

**Expected Output:**
```
Group Port-channel Protocol Ports
------+-------------+-----------+-----------------------------------------------
3     Po3(SU)      LACP       Gi1/0/1(P) Gi1/0/2(P) Gi1/0/3(P) Gi1/0/4(P)
```

### Step 2: Configure Native VLAN

```bash
# On both D1 and D2
(config)# interface port-channel 3
(config-if)# switchport trunk native vlan 999
(config-if)# exit
```

**Verify:**
```bash
D1# show interfaces trunk | include Port\|Po3
```

## Verification Commands Summary

### Essential EtherChannel Commands

| Command | Purpose |
|---------|---------|
| `show etherchannel summary` | Display all EtherChannels and their status |
| `show etherchannel port-channel` | Show port-channel information |
| `show interfaces trunk` | Display trunk interfaces and VLANs |
| `show spanning-tree` | Verify STP sees logical links |
| `show etherchannel load-balance` | Display load balancing method |

### EtherChannel Status Flags

| Flag | Meaning |
|------|---------|
| `D` | Down |
| `P` | Bundled in port-channel |
| `I` | Stand-alone |
| `S` | Suspended |
| `U` | In use |
| `f` | Failed to allocate aggregator |

## Protocol Comparison

| Feature | Static | PAgP | LACP |
|---------|--------|------|------|
| Standard | Cisco | Cisco Proprietary | IEEE 802.3ad |
| Negotiation | None | Yes | Yes |
| Health Checking | No | Yes | Yes |
| Active Modes | on | desirable | active |
| Passive Modes | on | auto | passive |
| Max Links | 8 | 8 | 8 |
| Recommendation | Not recommended | Cisco-only networks | Industry standard |

## Best Practices

1. **Always use negotiation protocols** (PAgP or LACP) instead of static configuration
2. **Use `switchport nonegotiate`** on trunk ports to disable DTP
3. **Make configuration changes at port-channel level**, not individual interfaces
4. **Use LACP when possible** for standards compliance
5. **Use `non-silent` mode for PAgP** switch-to-switch connections
6. **Verify configurations** with multiple show commands
7. **Document your EtherChannel assignments** to avoid conflicts

## Troubleshooting Tips

### Common Issues

1. **EtherChannel won't form:**
   - Check protocol compatibility (both sides must use same protocol)
   - Verify mode compatibility (at least one side must be active/desirable)
   - Ensure consistent configuration on all member ports

2. **Individual ports show as suspended:**
   - Configuration mismatch between member ports
   - Speed/duplex mismatch
   - VLAN mismatch

3. **Spanning Tree issues:**
   - EtherChannel reduces multiple physical links to one logical link
   - Check for consistent configuration across all member ports

### Debug Commands

```bash
# Enable debugging (use carefully in production)
debug etherchannel
debug pagp
debug lacp

# Disable debugging
undebug all
```

## Conclusion

EtherChannel technology provides both increased bandwidth and redundancy in switched networks. The three implementation methods each have their place:

- **Static:** Simple but lacks health checking
- **PAgP:** Cisco proprietary with good negotiation
- **LACP:** Industry standard, recommended for most deployments

Remember to always follow security best practices by disabling unnecessary protocols like DTP and making configuration changes at the appropriate interface level.

## Lab Cleanup

To reset interfaces to defaults:
```bash
# Reset specific interfaces
(config)# default interface range g1/0/1-6

# Remove port-channels
(config)# no interface port-channel 1
(config)# no interface port-channel 2  
(config)# no interface port-channel 3
```
