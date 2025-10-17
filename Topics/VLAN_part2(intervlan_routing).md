# CCNA Inter-VLAN Routing Guide

## Table of Contents
1. [Inter-VLAN Routing Overview](#inter-vlan-routing-overview)
2. [Method 1: Legacy Inter-VLAN Routing](#method-1-legacy-inter-vlan-routing)
3. [Method 2: Router-on-a-Stick](#method-2-router-on-a-stick)
4. [Method 3: Layer 3 Switch (SVI)](#method-3-layer-3-switch-svi)
5. [Troubleshooting Inter-VLAN Routing](#troubleshooting-inter-vlan-routing)

---

## Inter-VLAN Routing Overview

### The Problem

**VLANs are separate broadcast domains** - devices in different VLANs cannot communicate without routing.

```
PC1 (VLAN 10) -X- PC2 (VLAN 20)
Cannot communicate!
```

### The Solution

**Inter-VLAN Routing** enables communication between VLANs by routing packets at Layer 3.

```
PC1 (VLAN 10) → Router → PC2 (VLAN 20)
Communication works!
```

### Three Methods

| Method | Description | Use Case |
|--------|-------------|----------|
| **Legacy** | One router interface per VLAN | Obsolete (wasteful) |
| **Router-on-a-Stick** | One router interface, multiple subinterfaces | Small networks |
| **Layer 3 Switch (SVI)** | Switch routes internally | Modern networks (best) |

---

## Method 1: Legacy Inter-VLAN Routing

### Concept

**One physical router interface per VLAN**

```
[Router]
  |        |
 G0/0     G0/1
VLAN 10  VLAN 20
  |        |
[Switch]--[Switch]
  |        |
 PC1      PC2
```

### Configuration

**Router:**
```cisco
Router(config)# interface g0/0
Router(config-if)# ip address 192.168.10.1 255.255.255.0
Router(config-if)# no shutdown

Router(config)# interface g0/1
Router(config-if)# ip address 192.168.20.1 255.255.255.0
Router(config-if)# no shutdown
```

**Switch:**
```cisco
Switch(config)# vlan 10
Switch(config)# vlan 20

Switch(config)# interface f0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10

Switch(config)# interface f0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
```

### Disadvantages

✗ Wastes router interfaces (one per VLAN)
✗ Scalability issues (limited ports)
✗ Expensive (requires many interfaces)
✗ **Obsolete - don't use in production**

---

## Method 2: Router-on-a-Stick

### Concept

**One physical interface with multiple subinterfaces (one per VLAN)**

```
      [Router]
         |
        G0/0 (Trunk)
    .10  |  .20
    _____|_____
         |
    [Switch] G0/1 (Trunk)
    |        |
  VLAN 10  VLAN 20
    |        |
   PC1      PC2
```

### How It Works

- **Physical interface:** Connects to trunk port
- **Subinterfaces:** Virtual interfaces (G0/0.10, G0/0.20)
- **802.1Q tagging:** Identifies VLAN for each frame
- **Trunk port:** Carries multiple VLANs

---

### Configuration

#### Router Configuration

**Step 1: Enable physical interface (no IP address)**
```cisco
Router(config)# interface g0/0
Router(config-if)# no shutdown
Router(config-if)# exit
```

**Step 2: Create subinterfaces**
```cisco
! Subinterface for VLAN 10
Router(config)# interface g0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# exit

! Subinterface for VLAN 20
Router(config)# interface g0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config-subif)# exit
```

**Command Breakdown:**
- `interface g0/0.10` - Creates subinterface (`.10` = best practice to match VLAN)
- `encapsulation dot1Q 10` - Associates with VLAN 10 using 802.1Q
- `ip address` - Default gateway for VLAN 10 devices

---

#### Switch Configuration

**Step 1: Create VLANs**
```cisco
Switch(config)# vlan 10
Switch(config-vlan)# name Sales
Switch(config-vlan)# exit

Switch(config)# vlan 20
Switch(config-vlan)# name Engineering
Switch(config-vlan)# exit
```

**Step 2: Configure access ports**
```cisco
! PC1 in VLAN 10
Switch(config)# interface f0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

! PC2 in VLAN 20
Switch(config)# interface f0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# exit
```

**Step 3: Configure trunk to router**
```cisco
Switch(config)# interface g0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit
```

---

### Verification Commands

**Router:**
```cisco
Router# show ip interface brief
Interface         IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0 unassigned     YES unset  up                    up
GigabitEthernet0/0.10 192.168.10.1 YES manual up                  up
GigabitEthernet0/0.20 192.168.20.1 YES manual up                  up

Router# show interfaces g0/0.10
GigabitEthernet0/0.10 is up, line protocol is up
  Encapsulation 802.1Q Virtual LAN, VLAN ID 10
  Internet address is 192.168.10.1/24

Router# show vlans
Virtual LAN ID:  10 (IEEE 802.1Q Encapsulation)
   vLAN Trunk Interface:   GigabitEthernet0/0.10
```

**Switch:**
```cisco
Switch# show vlan brief
VLAN Name      Status    Ports
10   Sales     active    Fa0/1
20   Engineering active  Fa0/2

Switch# show interface trunk
Port      Mode    Encapsulation  Status      Native vlan
Gi0/1     on      802.1q         trunking    1

Switch# show interface g0/1 switchport
Name: Gi0/1
Administrative Mode: trunk
Operational Mode: trunk
```

---

### Advantages and Disadvantages

**Advantages:**
✓ Uses only one router interface
✓ Cost-effective
✓ Simple configuration
✓ Good for small networks

**Disadvantages:**
✗ Single point of failure
✗ Bandwidth bottleneck (all traffic on one link)
✗ All inter-VLAN traffic goes to router and back
✗ Not scalable for large networks

**When to Use:**
- Small networks (< 10 VLANs)
- Branch offices
- Budget constraints
- Lab/learning environments

---

## Method 3: Layer 3 Switch (SVI)

### Concept

**Switch performs routing internally using Switched Virtual Interfaces (SVIs)**

```
    [Layer 3 Switch]
    Routing + Switching
    |        |        |
  VLAN 10  VLAN 20  VLAN 30
    |        |        |
   PC1      PC2      PC3
```

### How It Works

- **SVI (Switched Virtual Interface):** Virtual interface representing a VLAN
- **Wire speed:** Routing happens in hardware (ASIC)
- **No external router needed**
- **Best performance**

---

### Configuration

#### Step 1: Enable IP Routing

```cisco
Switch(config)# ip routing
```
**Critical!** Layer 3 switching requires this command.

---

#### Step 2: Create VLANs

```cisco
Switch(config)# vlan 10
Switch(config-vlan)# name Sales
Switch(config-vlan)# exit

Switch(config)# vlan 20
Switch(config-vlan)# name Engineering
Switch(config-vlan)# exit

Switch(config)# vlan 30
Switch(config-vlan)# name HR
Switch(config-vlan)# exit
```

---

#### Step 3: Create SVIs (One per VLAN)

```cisco
! SVI for VLAN 10
Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.1 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit

! SVI for VLAN 20
Switch(config)# interface vlan 20
Switch(config-if)# ip address 192.168.20.1 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit

! SVI for VLAN 30
Switch(config)# interface vlan 30
Switch(config-if)# ip address 192.168.30.1 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit
```

**Note:** SVIs are default gateway addresses for each VLAN.

---

#### Step 4: Configure Access Ports

```cisco
! PC1 in VLAN 10
Switch(config)# interface f0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

! PC2 in VLAN 20
Switch(config)# interface f0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# exit

! PC3 in VLAN 30
Switch(config)# interface f0/3
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 30
Switch(config-if)# exit
```

---

### Verification Commands

```cisco
! Verify IP routing enabled
Switch# show ip interface brief
Interface         IP-Address      OK? Method Status                Protocol
Vlan10            192.168.10.1    YES manual up                    up
Vlan20            192.168.20.1    YES manual up                    up
Vlan30            192.168.30.1    YES manual up                    up

! Verify routing table
Switch# show ip route
     10.0.0.0/24 is subnetted, 1 subnets
C       192.168.10.0 is directly connected, Vlan10
     20.0.0.0/24 is subnetted, 1 subnets
C       192.168.20.0 is directly connected, Vlan20
     30.0.0.0/24 is subnetted, 1 subnets
C       192.168.30.0 is directly connected, Vlan30

! Check if IP routing enabled
Switch# show running-config | include ip routing
ip routing

! Verify VLAN interfaces
Switch# show interfaces vlan 10
Vlan10 is up, line protocol is up
  Internet address is 192.168.10.1/24
  MTU 1500 bytes, BW 1000000 Kbit/sec
```

---

### SVI Requirements

**For SVI to be UP:**
1. ✓ VLAN must exist
2. ✓ At least one access port in VLAN must be up
3. ✓ SVI must have `no shutdown`

**Example Issue:**
```cisco
Switch# show ip interface brief
Interface         IP-Address      OK? Method Status                Protocol
Vlan10            192.168.10.1    YES manual up                    up
Vlan20            192.168.20.1    YES manual down                  down
                                              ↑
                                    Problem: No active ports in VLAN 20
```

---

### Advantages and Disadvantages

**Advantages:**
✓ Best performance (wire speed)
✓ No external router needed
✓ Highly scalable
✓ No single point of failure
✓ Modern standard
✓ Routing happens in hardware (ASIC)

**Disadvantages:**
✗ Requires Layer 3 switch (more expensive)
✗ More complex configuration than Layer 2

**When to Use:**
- Enterprise networks
- Data centers
- Campus networks
- High-traffic environments
- **Preferred method for production**

---

## Method Comparison

| Feature | Legacy | Router-on-a-Stick | Layer 3 Switch |
|---------|--------|-------------------|----------------|
| **Interfaces Needed** | One per VLAN | One (subinterfaces) | None (internal) |
| **Performance** | Good | Bottleneck | Excellent |
| **Scalability** | Poor | Moderate | Excellent |
| **Cost** | High | Low | Moderate |
| **Complexity** | Simple | Moderate | Moderate |
| **CCNA Relevance** | Historical only | Important | Most important |
| **Production Use** | Never | Small networks | Always |

---

## Troubleshooting Inter-VLAN Routing

### Systematic Approach

**Step 1: Verify Physical Layer**
**Step 2: Verify VLANs**
**Step 3: Verify Trunk (Router-on-a-Stick)**
**Step 4: Verify Routing**
**Step 5: Verify End Device Configuration**

---

### Common Issues and Solutions

#### Issue 1: Devices in Same VLAN Cannot Communicate

**Symptoms:**
- PC1 (VLAN 10) cannot ping PC2 (VLAN 10)

**Troubleshooting:**
```cisco
! Check VLAN configuration
Switch# show vlan brief

! Check if ports in same VLAN
Switch# show interface f0/1 switchport | include VLAN
Access Mode VLAN: 10 (VLAN0010)

! Check interface status
Switch# show interface f0/1 status
Port    Name    Status       Vlan
Fa0/1           connected    10
```

**Common Causes:**
- Ports in wrong VLAN
- Interface shutdown
- Cable issue

---

#### Issue 2: Devices Cannot Reach Default Gateway

**Symptoms:**
- PC can't ping default gateway
- No inter-VLAN communication

**Router-on-a-Stick Troubleshooting:**
```cisco
! Check physical interface
Router# show interface g0/0
GigabitEthernet0/0 is up, line protocol is up
                   ↑
              Must be UP

! Check subinterfaces
Router# show ip interface brief
GigabitEthernet0/0.10  192.168.10.1  YES manual up  up
                                                 ↑   ↑
                                           Must be UP UP

! Verify encapsulation
Router# show interfaces g0/0.10 | include Encapsulation
  Encapsulation 802.1Q Virtual LAN, VLAN ID 10
                                         ↑
                              Must match VLAN number
```

**Common Causes:**
- Physical interface shutdown (`shutdown` on G0/0)
- Wrong encapsulation VLAN
- Wrong IP address on subinterface

---

**Layer 3 Switch Troubleshooting:**
```cisco
! Check if IP routing enabled
Switch# show running-config | include ip routing
ip routing    ← Must be present

! Check SVI status
Switch# show ip interface brief
Vlan10  192.168.10.1  YES manual up  up
                                  ↑   ↑
                            Must be UP UP

! If SVI is down, check:
Switch# show vlan id 10
VLAN Name      Status    Ports
10   VLAN0010  active    Fa0/1
                         ↑
                   Must have at least one active port

! Check if SVI has shutdown
Switch# show running-config interface vlan 10
interface Vlan10
 ip address 192.168.10.1 255.255.255.0
 no shutdown    ← Must be present
```

**Common Causes:**
- `ip routing` not enabled
- SVI shutdown
- No active ports in VLAN
- VLAN doesn't exist

---

#### Issue 3: Inter-VLAN Routing Not Working

**Symptoms:**
- Devices can ping gateway
- Cannot ping devices in other VLANs

**Troubleshooting:**

**Router-on-a-Stick:**
```cisco
! Check trunk on switch
Switch# show interface trunk
Port      Mode    Encapsulation  Status      Native vlan
Gi0/1     on      802.1q         trunking    1
                                 ↑
                           Must be "trunking"

! If not trunking:
Switch# show interface g0/1 switchport
Administrative Mode: access  ← Problem: should be trunk

! Fix:
Switch(config)# interface g0/1
Switch(config-if)# switchport mode trunk

! Verify allowed VLANs
Switch# show interface trunk | include allowed
Gi0/1     1-4094    ← Should include your VLANs
```

**Layer 3 Switch:**
```cisco
! Check routing table
Switch# show ip route
C    192.168.10.0/24 is directly connected, Vlan10
C    192.168.20.0/24 is directly connected, Vlan20
↑
"C" = directly connected (good!)

! If routes missing, check SVIs:
Switch# show ip interface brief
Vlan10  192.168.10.1  YES manual down  down
                                  ↑
                            Problem: SVI down

! Check why SVI is down:
Switch# show vlan id 10
VLAN Name      Status    Ports
10   VLAN0010  active    none
                         ↑
                   No ports = SVI down

! Or check for shutdown:
Switch# show running-config interface vlan 10
interface Vlan10
 ip address 192.168.10.1 255.255.255.0
 shutdown    ← Problem!

! Fix:
Switch(config)# interface vlan 10
Switch(config-if)# no shutdown
```

---

#### Issue 4: Native VLAN Mismatch (Router-on-a-Stick)

**Symptoms:**
- Some VLANs work, others don't
- CDP/DTP errors in logs

**Check:**
```cisco
Switch# show interface g0/1 switchport | include Native
Trunking Native Mode VLAN: 1 (default)
```

**On router, check if native VLAN configured:**
```cisco
Router# show running-config interface g0/0
interface GigabitEthernet0/0
 no ip address
 no shutdown
!
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
!
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
!
! No native VLAN configured = uses VLAN 1 by default
```

**If needed, configure native VLAN:**
```cisco
Router(config)# interface g0/0.1
Router(config-subif)# encapsulation dot1Q 1 native
```

---

#### Issue 5: Wrong VLAN Assignment

**Symptoms:**
- Device in wrong VLAN
- Can't communicate with expected devices

**Check:**
```cisco
Switch# show vlan brief
VLAN Name      Status    Ports
10   Sales     active    Fa0/1, Fa0/2
20   Engineer  active    Fa0/3
                         ↑
                   Verify PC is on correct port

! Check specific port:
Switch# show interface f0/1 switchport | include VLAN
Access Mode VLAN: 10 (VLAN0010)
```

**Fix:**
```cisco
Switch(config)# interface f0/1
Switch(config-if)# switchport access vlan 20
```

---

### Troubleshooting Checklist

**Router-on-a-Stick:**
- [ ] Physical interface enabled (`no shutdown` on G0/0)
- [ ] Subinterfaces configured with correct encapsulation
- [ ] Correct IP addresses on subinterfaces
- [ ] Trunk configured on switch port
- [ ] VLANs created on switch
- [ ] Access ports assigned to correct VLANs
- [ ] End devices have correct gateway

**Layer 3 Switch:**
- [ ] `ip routing` enabled globally
- [ ] VLANs created
- [ ] SVIs created with IP addresses
- [ ] SVIs not shutdown
- [ ] At least one active port per VLAN
- [ ] Access ports assigned to correct VLANs
- [ ] End devices have correct gateway

---

### Quick Verification Commands

```cisco
! Router-on-a-Stick
show ip interface brief
show interfaces g0/0.10
show vlans

! Switch (Layer 2 or 3)
show vlan brief
show interface trunk
show interface status

! Layer 3 Switch only
show ip route
show ip interface brief | include Vlan
show running-config | include ip routing

! End Device
ipconfig
ping <gateway>
ping <remote-device>
tracert <remote-device>
```

---

## Configuration Examples Summary

### Router-on-a-Stick Complete Config

```cisco
! Router
interface g0/0
 no shutdown
!
interface g0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
!
interface g0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

! Switch
vlan 10
vlan 20
!
interface f0/1
 switchport mode access
 switchport access vlan 10
!
interface f0/2
 switchport mode access
 switchport access vlan 20
!
interface g0/1
 switchport mode trunk
```

---

### Layer 3 Switch Complete Config

```cisco
ip routing
!
vlan 10
vlan 20
vlan 30
!
interface vlan 10
 ip address 192.168.10.1 255.255.255.0
 no shutdown
!
interface vlan 20
 ip address 192.168.20.1 255.255.255.0
 no shutdown
!
interface vlan 30
 ip address 192.168.30.1 255.255.255.0
 no shutdown
!
interface f0/1
 switchport mode access
 switchport access vlan 10
!
interface f0/2
 switchport mode access
 switchport access vlan 20
!
interface f0/3
 switchport mode access
 switchport access vlan 30
```

---

## CCNA Exam Tips

**Key Points:**
- Know all three methods (Legacy, Router-on-a-Stick, Layer 3 Switch)
- Router-on-a-Stick: `encapsulation dot1Q <vlan>` command
- Layer 3 Switch: `ip routing` command is critical
- Trunk required for Router-on-a-Stick
- SVI requires active port in VLAN

**Common Exam Questions:**
- Identify which method is being used
- Troubleshoot missing trunk configuration
- Identify missing `ip routing` command
- Configure subinterfaces with correct encapsulation
- Determine why SVI is down

**Commands to Memorize:**
```cisco
ip routing
encapsulation dot1Q <vlan>
switchport mode trunk
interface vlan <number>
show interface trunk
show ip route
```

---

**End of CCNA Inter-VLAN Routing Guide**
