# CDP and LLDP Network Discovery Lab Guide

## Lab Overview

This lab teaches you how to use Cisco Discovery Protocol (CDP) and Link Layer Discovery Protocol (LLDP) to discover network topology and connected devices. You'll learn to enable, disable, and configure these protocols for network discovery and documentation.

## Network Topology

```
    ISP Router ---- Gateway Router ---- Switch S3
                                          |
                                    Switch S2 ---- Switch S1
```

## Addressing Table

| Device | Interface | IP Address | Subnet Mask |
|--------|-----------|------------|-------------|
| Gateway | G0/1 | 192.168.1.254 | 255.255.255.0 |
| Gateway | S0/0/1 | 209.165.200.226 | 255.255.255.252 |
| ISP | S0/0/1 (DCE) | 209.165.200.225 | 255.255.255.252 |

## Learning Objectives

By the end of this lab, you will be able to:

1. Understand the difference between CDP and LLDP
2. Configure and verify CDP operations
3. Configure and verify LLDP operations
4. Use discovery protocols to document network topology
5. Disable discovery protocols on external interfaces for security
6. Interpret CDP and LLDP neighbor information

## Protocol Overview

### Cisco Discovery Protocol (CDP)
- **Proprietary:** Cisco-only protocol
- **Layer:** Data Link Layer (Layer 2)
- **Default:** Enabled by default on Cisco devices
- **Purpose:** Discovers directly connected Cisco devices
- **Frequency:** Sends advertisements every 60 seconds
- **Hold Time:** 180 seconds

### Link Layer Discovery Protocol (LLDP)
- **Standard:** IEEE 802.1AB (vendor-neutral)
- **Layer:** Data Link Layer (Layer 2)
- **Default:** May or may not be enabled by default
- **Purpose:** Discovers directly connected devices (any vendor)
- **Frequency:** Sends advertisements every 30 seconds
- **Hold Time:** 120 seconds

## Required Equipment

- 2 Cisco Routers (1941 ISR or comparable)
- 3 Cisco Switches (2960 or comparable)
- Console cables
- Ethernet cables

---

## Part 1: Build the Network and Configure Basic Device Settings

### Step 1: Physical Network Setup

1. **Cable the network** according to the topology diagram
2. **Initialize and reload** all network devices to ensure clean configurations

### Step 2: Configure Basic Switch Settings

For **all switches (S1, S2, S3):**

```cisco
Switch> enable
Switch# configure terminal
Switch(config)# no ip domain-lookup
Switch(config)# hostname [S1/S2/S3]
```

#### Verify switchport status:
```cisco
S1# show interfaces status
```

#### Save configuration:
```cisco
S1# copy running-config startup-config
```

### Step 3: Configure Router ISP

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname ISP
Router(config)# no ip domain-lookup
Router(config)# interface Serial0/0/1
Router(config-if)# ip address 209.165.200.225 255.255.255.252
Router(config-if)# clock rate 64000
Router(config-if)# no shutdown
Router(config-if)# exit
Router(config)# exit
Router# copy running-config startup-config
```

### Step 4: Configure Gateway Router

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname Gateway
Router(config)# no ip domain-lookup

Router(config)# interface GigabitEthernet0/1
Router(config-if)# ip address 192.168.1.254 255.255.255.0
Router(config-if)# ip nat inside
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface Serial0/0/1
Router(config-if)# ip address 209.165.200.226 255.255.255.252
Router(config-if)# ip nat outside
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# ip nat inside source list 1 interface Serial0/0/1 overload
Router(config)# access-list 1 permit 192.168.1.0 0.0.0.255
Router(config)# exit
Gateway# copy running-config startup-config
```

---

## Part 2: Network Discovery with CDP

### Step 1: Verify CDP Global Status

```cisco
Gateway# show cdp
```

**Expected Output:**
```
Global CDP information:
    Sending CDP packets every 60 seconds
    Sending a holdtime value of 180 seconds
    Sending CDPv2 advertisements is enabled
```

**Key Information:**
- CDP packets sent every **60 seconds**
- Hold time is **180 seconds**

#### If CDP is disabled, enable it:
```cisco
Gateway(config)# cdp run
```

### Step 2: View CDP-Enabled Interfaces

```cisco
Gateway# show cdp interface
```

**Expected Output:**
```
GigabitEthernet0/1 is up, line protocol is up
    Encapsulation ARPA
    Sending CDP packets every 60 seconds
    Holdtime is 180 seconds

Serial0/0/1 is up, line protocol is up
    Encapsulation HDLC
    Sending CDP packets every 60 seconds
    Holdtime is 180 seconds

cdp enabled interfaces : 5
interfaces up : 2
interfaces down : 3
```

**Analysis Questions:**
- How many interfaces are participating in CDP advertisement?
- Which interfaces are up?

### Step 3: Discover CDP Neighbors

```cisco
Gateway# show cdp neighbors
```

**Expected Output:**
```
Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
                 S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone,
                 D - Remote, C - CVTA, M - Two-port Mac Relay

Device ID    Local Intrfce   Holdtme  Capability  Platform    Port ID
ISP          Ser 0/0/1       158      R B S I     CISCO1941   Ser 0/0/1
S3           Gig 0/1         170      S I         WS-C2960-   Fas 0/5
```

### Step 4: Detailed CDP Neighbor Information

```cisco
Gateway# show cdp neighbors detail
```

**Sample Output:**
```
-------------------------
Device ID: ISP
Entry address(es): 
  IP address: 209.165.200.225
Platform: Cisco CISCO1941/K9,  Capabilities: Router Source-Route-Bridge Switch IGMP 
Interface: Serial0/0/1,  Port ID (outgoing port): Serial0/0/1
Holdtime : 143 sec

Version :
Cisco IOS Software, C1900 Software (C1900-UNIVERSALK9-M), Version 15.4(3)M2,
RELEASE SOFTWARE (fc2)

-------------------------
Device ID: S3
Entry address(es): 
Platform: cisco WS-C2960-24TT-L,  Capabilities: Switch IGMP 
Interface: GigabitEthernet0/1,  Port ID (outgoing port): FastEthernet0/5
Holdtime : 158 sec

Version :
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE7,
RELEASE SOFTWARE (fc1)
```

**Information Available:**
- Device hostnames and models
- IP addresses
- IOS versions and capabilities
- Connected interfaces and ports
- Platform information

### Step 5: Configure Switch S3 Management IP

```cisco
S3(config)# interface vlan 1
S3(config-if)# ip address 192.168.1.3 255.255.255.0
S3(config-if)# no shutdown
S3(config-if)# exit
S3(config)# ip default-gateway 192.168.1.254
```

#### Verify additional information now available:
```cisco
Gateway# show cdp neighbors detail
```

**Note:** You should now see the IP address 192.168.1.3 for S3.

### Step 6: Disable CDP on External Interface (Security)

For security reasons, disable CDP on interfaces facing external networks:

```cisco
Gateway(config)# interface serial 0/0/1
Gateway(config-if)# no cdp enable
Gateway(config-if)# exit
```

#### Verify CDP is disabled:
```cisco
Gateway# show cdp neighbors
```

**Expected Result:** ISP router should no longer appear as a CDP neighbor.

```cisco
Gateway# show cdp interface
```

**Expected Result:** Serial 0/0/1 should not be listed, showing fewer CDP-enabled interfaces.

### Step 7: Global CDP Control

#### Disable CDP globally:
```cisco
Gateway(config)# no cdp run
```

#### Verify CDP is disabled globally:
```cisco
Gateway# show cdp neighbors
Gateway# show cdp interface
```

#### Re-enable CDP globally:
```cisco
Gateway(config)# cdp run
```

**Note:** When re-enabled globally, the interface-specific `no cdp enable` setting on S0/0/1 remains in effect.

### Step 8: Document Switch Interconnections

Use CDP commands on all switches to map the network topology:

#### On Switch S3:
```cisco
S3# show cdp neighbors
```

**Expected Output:**
```
Device ID    Local Intrfce   Holdtme  Capability  Platform    Port ID
Gateway      Fas 0/5         143      R B S I     CISCO1941   Gig 0/1
S2           Fas 0/2         173      S I         WS-C2960-   Fas 0/4
S1           Fas 0/4         171      S I         WS-C2960-   Fas 0/4
```

#### Repeat for S1 and S2:
```cisco
S1# show cdp neighbors
S2# show cdp neighbors
```

**Document your findings in the topology diagram.**

---

## Part 3: Network Discovery with LLDP

### Step 1: Verify LLDP Status

```cisco
Gateway# show lldp
```

**If LLDP is not enabled:**
```
% LLDP is not enabled
```

### Step 2: Enable LLDP

#### On Gateway router:
```cisco
Gateway(config)# lldp run
```

#### Verify LLDP is enabled:
```cisco
Gateway# show lldp
```

**Expected Output:**
```
Global LLDP Information:
    Status: ACTIVE
    LLDP advertisements are sent every 30 seconds
    LLDP hold time advertised is 120 seconds
    LLDP interface reinitialisation delay is 2 seconds
```

**Key Differences from CDP:**
- LLDP packets sent every **30 seconds** (vs 60 for CDP)
- Hold time is **120 seconds** (vs 180 for CDP)

### Step 3: Check LLDP Neighbors

```cisco
Gateway# show lldp neighbors
```

**If no neighbors appear, enable LLDP on switches:**

#### Enable LLDP on all switches:
```cisco
S1(config)# lldp run
S2(config)# lldp run
S3(config)# lldp run
```

### Step 4: View LLDP Neighbors

```cisco
Gateway# show lldp neighbors
```

**Expected Output:**
```
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other

Device ID         Local Intf    Hold-time    Capability    Port ID
S3                Gi0/1         120          B             Fa0/5

Total entries displayed: 1
```

### Step 5: Detailed LLDP Information

```cisco
Gateway# show lldp neighbors detail
```

**Expected Output:**
```
------------------------------------------------
Local Intf: Gi0/1
Chassis id: 0cd9.96e8.7400
Port id: Fa0/5
Port Description: FastEthernet0/5
System Name: S3

System Description:
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE7,
RELEASE SOFTWARE (fc1)

Time remaining: 103 seconds
System Capabilities: B
Enabled Capabilities: B
Management Addresses:
    IP: 192.168.1.3

Auto Negotiation - supported, enabled
Physical media capabilities:
    100base-TX(FD)
    100base-TX(HD)
    10base-T(FD)
    10base-T(HD)

Media Attachment Unit type: 16
Vlan ID: 1

Total entries displayed: 1
```

**Additional LLDP Information Available:**
- Auto-negotiation capabilities
- Physical media capabilities (speed/duplex options)
- VLAN information
- Media Attachment Unit (MAU) type

### Step 6: Document Complete Network Topology

Use both CDP and LLDP information to complete your network topology diagram with:
- Device names
- Interface connections
- IP addresses
- Interface types and capabilities

---

## Command Reference

### CDP Commands

| Command | Purpose |
|---------|---------|
| `show cdp` | Display CDP global information |
| `show cdp interface` | Show CDP-enabled interfaces |
| `show cdp neighbors` | Display CDP neighbor table |
| `show cdp neighbors detail` | Show detailed CDP neighbor information |
| `cdp run` | Enable CDP globally |
| `no cdp run` | Disable CDP globally |
| `cdp enable` | Enable CDP on interface (default) |
| `no cdp enable` | Disable CDP on specific interface |

### LLDP Commands

| Command | Purpose |
|---------|---------|
| `show lldp` | Display LLDP global information |
| `show lldp interface` | Show LLDP-enabled interfaces |
| `show lldp neighbors` | Display LLDP neighbor table |
| `show lldp neighbors detail` | Show detailed LLDP neighbor information |
| `lldp run` | Enable LLDP globally |
| `no lldp run` | Disable LLDP globally |
| `lldp transmit` | Enable LLDP transmission on interface |
| `lldp receive` | Enable LLDP reception on interface |

---

## Security Considerations

### When to Disable Discovery Protocols:

1. **External-facing interfaces:** Interfaces connected to ISP or internet
2. **DMZ interfaces:** Interfaces in demilitarized zones
3. **Guest networks:** Interfaces serving guest users
4. **High-security environments:** Where information disclosure is a concern

### Best Practices:

1. **Disable on WAN interfaces:** Always disable CDP/LLDP on external connections
2. **Use interface-specific control:** Disable only where necessary, not globally
3. **Regular audits:** Periodically review which interfaces have discovery protocols enabled
4. **Documentation:** Maintain current topology documentation using discovery protocol data

### Example Security Configuration:

```cisco
! Disable CDP on external interface
interface Serial0/0/1
 no cdp enable
 no lldp transmit
 no lldp receive

! Keep CDP/LLDP enabled on internal interfaces
interface GigabitEthernet0/1
 cdp enable
 lldp transmit
 lldp receive
```

---

## Troubleshooting

### Common Issues:

1. **No neighbors discovered:**
   - Verify protocols are enabled globally
   - Check interface-specific settings
   - Ensure physical connectivity
   - Wait for hold time to expire after configuration changes

2. **Partial neighbor information:**
   - Check if devices support both CDP and LLDP
   - Verify IP addressing on management interfaces
   - Ensure proper VLAN configuration

3. **Security concerns:**
   - Audit all external interfaces
   - Review discovery protocol configuration regularly
   - Document approved protocol usage

### Verification Steps:

```cisco
! Check global status
show cdp
show lldp

! Check interface participation
show cdp interface
show lldp interface

! View neighbors
show cdp neighbors detail
show lldp neighbors detail

! Check interface status
show interfaces status
show ip interface brief
```

---

## Lab Questions and Reflection

### Analysis Questions:

1. **How often are CDP packets sent?** Every 60 seconds

2. **What is the CDP hold time?** 180 seconds

3. **How does LLDP frequency compare to CDP?** LLDP sends every 30 seconds (more frequent than CDP's 60 seconds)

4. **What additional information does LLDP provide that CDP doesn't?** 
   - Physical media capabilities
   - Auto-negotiation status
   - MAU type information

5. **On which interfaces should you NOT use discovery protocols?**
   - External-facing interfaces (WAN, Internet connections)
   - DMZ interfaces
   - Guest network interfaces
   - Any interface where information disclosure poses security risks

### Reflection:

Discovery protocols are valuable tools for network documentation and troubleshooting but must be used judiciously. The key is balancing operational convenience with security requirements. Internal network segments benefit from discovery protocols for documentation and troubleshooting, while external interfaces should have these protocols disabled to prevent information leakage to potential attackers.

This completes the CDP and LLDP Network Discovery lab configuration and analysis.