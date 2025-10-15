# Lab - Implement VLANs and Trunking

## Lab Overview

**Topology**: 3 switches (SWA, SWB, SWC) interconnected with PCs on SWB and SWC  
**Objective**: Configure VLANs, assign ports, configure static and dynamic trunking  
**Duration**: 45-60 minutes

---

## Addressing Table

### End Devices
| Device | IP Address | Subnet Mask | Switch Port | VLAN |
|--------|------------|-------------|-------------|------|
| PC1 | 192.168.10.10 | 255.255.255.0 | SWB F0/1 | 10 |
| PC2 | 192.168.20.20 | 255.255.255.0 | SWB F0/2 | 20 |
| PC3 | 192.168.30.30 | 255.255.255.0 | SWB F0/3 | 30 |
| PC4 | 192.168.10.11 | 255.255.255.0 | SWC F0/1 | 10 |
| PC5 | 192.168.20.21 | 255.255.255.0 | SWC F0/2 | 20 |
| PC6 | 192.168.30.31 | 255.255.255.0 | SWC F0/3 | 30 |
| PC7 | 192.168.10.12 | 255.255.255.0 | SWC F0/4 | 10 + Voice 40 |

### Switch Management
| Switch | SVI IP | Subnet Mask | VLAN |
|--------|--------|-------------|------|
| SWA | 192.168.99.252 | 255.255.255.0 | 99 |
| SWB | 192.168.99.253 | 255.255.255.0 | 99 |
| SWC | 192.168.99.254 | 255.255.255.0 | 99 |

---

## VLAN Configuration

### VLANs Required
| VLAN ID | Name | Purpose |
|---------|------|---------|
| 10 | Admin | Administrative users |
| 20 | Accounts | Accounting department |
| 30 | HR | Human Resources |
| 40 | Voice | VoIP phones |
| 99 | Management | Switch management |
| 100 | Native | Trunk native VLAN |

---

## Part 1: Configure VLANs

### Create VLANs on All Switches (SWA, SWB, SWC)

**Commands** (repeat on each switch):
```cisco
Switch> enable
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)# name Admin
Switch(config-vlan)# vlan 20
Switch(config-vlan)# name Accounts
Switch(config-vlan)# vlan 30
Switch(config-vlan)# name HR
Switch(config-vlan)# vlan 40
Switch(config-vlan)# name Voice
Switch(config-vlan)# vlan 99
Switch(config-vlan)# name Management
Switch(config-vlan)# vlan 100
Switch(config-vlan)# name Native
Switch(config-vlan)# exit
```

### Verify VLANs
```cisco
Switch# show vlan brief
```

**Expected Output**:
```

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1, Gig0/2
10   Admin                            active    
20   Accounts                         active    
30   HR                               active    
40   Voice                            active    
99   Management                       active    
100  Native                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active     
```

---

## Part 2: Assign Ports to VLANs

### Step 1: Configure Access Ports on SWB

```cisco
SWB(config)# interface fastethernet 0/1
SWB(config-if)# switchport mode access
SWB(config-if)# switchport access vlan 10
SWB(config-if)# exit

SWB(config)# interface fastethernet 0/2
SWB(config-if)# switchport mode access
SWB(config-if)# switchport access vlan 20
SWB(config-if)# exit

SWB(config)# interface fastethernet 0/3
SWB(config-if)# switchport mode access
SWB(config-if)# switchport access vlan 30
SWB(config-if)# exit
```

### Step 2: Configure Access Ports on SWC

```cisco
SWC(config)# interface fastethernet 0/1
SWC(config-if)# switchport mode access
SWC(config-if)# switchport access vlan 10
SWC(config-if)# exit

SWC(config)# interface fastethernet 0/2
SWC(config-if)# switchport mode access
SWC(config-if)# switchport access vlan 20
SWC(config-if)# exit

SWC(config)# interface fastethernet 0/3
SWC(config-if)# switchport mode access
SWC(config-if)# switchport access vlan 30
SWC(config-if)# exit
```

### Step 3: Configure Voice VLAN Port (SWC F0/4)

**PC7 needs both data (VLAN 10) and voice (VLAN 40)**:

```cisco
SWC(config)# interface fastethernet 0/4
SWC(config-if)# switchport mode access
SWC(config-if)# switchport access vlan 10
SWC(config-if)# switchport voice vlan 40
SWC(config-if)# exit
```

**What this does**:
- Data traffic → VLAN 10 (untagged)
- Voice traffic → VLAN 40 (tagged)
- IP phone passes PC traffic through

### Step 4: Configure Management SVIs

**On SWA**:
```cisco
SWA(config)# interface vlan 99
SWA(config-if)# ip address 192.168.99.252 255.255.255.0
SWA(config-if)# no shutdown
SWA(config-if)# exit
```

**On SWB**:
```cisco
SWB(config)# interface vlan 99
SWB(config-if)# ip address 192.168.99.253 255.255.255.0
SWB(config-if)# no shutdown
SWB(config-if)# exit
```

**On SWC**:
```cisco
SWC(config)# interface vlan 99
SWC(config-if)# ip address 192.168.99.254 255.255.255.0
SWC(config-if)# no shutdown
SWC(config-if)# exit
```

**Note**: Lab states switches should NOT ping each other yet (trunk not configured)

### Verify Port Assignments
```cisco
Switch# show vlan brief
```

**Check that**:
- F0/1 is in VLAN 10
- F0/2 is in VLAN 20
- F0/3 is in VLAN 30
- F0/4 is in VLAN 10 (voice VLAN doesn't show here)

---

## Part 3: Configure Static Trunking

### Link: SWA ↔ SWB (Assume G0/1 on both)

**On SWA**:
```cisco
SWA(config)# interface gigabitethernet 0/1
SWA(config-if)# switchport mode trunk
SWA(config-if)# switchport trunk native vlan 100
SWA(config-if)# switchport nonegotiate
SWA(config-if)# exit
```

**On SWB**:
```cisco
SWB(config)# interface gigabitethernet 0/1
SWB(config-if)# switchport mode trunk
SWB(config-if)# switchport trunk native vlan 100
SWB(config-if)# switchport nonegotiate
SWB(config-if)# exit
```

**What each command does**:
- `switchport mode trunk` - Force port to trunk mode
- `switchport trunk native vlan 100` - Set native VLAN to 100 (not default 1)
- `switchport nonegotiate` - Disable DTP (static trunk)

### Verify Static Trunk
```cisco
SWA# show interfaces trunk
SWA# show interfaces gigabitethernet 0/1 switchport
```

**Check for**:
- Mode: trunk
- Native VLAN: 100
- DTP: Off (nonegotiate)

### Test Connectivity
```cisco
SWA# ping 192.168.99.253
```
**Should work** now that trunk carries VLAN 99

---

## Part 4: Configure Dynamic Trunking

### Link: SWA G0/2 ↔ SWC G0/2

**Scenario**: SWC G0/2 is default mode (dynamic auto on 2960)

**On SWC** (verify default):
```cisco
SWC# show interfaces gigabitethernet 0/2 switchport
```
Should show: `Administrative Mode: dynamic auto`

**On SWA** (configure to negotiate):
```cisco
SWA(config)# interface gigabitethernet 0/2
SWA(config-if)# switchport mode dynamic desirable
SWA(config-if)# switchport trunk native vlan 100
SWA(config-if)# exit
```

**Why dynamic desirable?**
- SWC is in dynamic auto (passive)
- SWA needs to actively negotiate (desirable)
- Desirable + Auto = Trunk forms

**Alternative** (if SWC isn't default):
```cisco
SWC(config)# interface gigabitethernet 0/2
SWC(config-if)# switchport mode dynamic auto
SWC(config-if)# switchport trunk native vlan 100
SWC(config-if)# exit
```

### Verify Dynamic Trunk
```cisco
SWA# show interfaces trunk
```

**Should show**:
- G0/2 in trunking mode
- Native VLAN: 100

```cisco
SWA# show interfaces gigabitethernet 0/2 switchport
```

**Look for**:
- Administrative Mode: dynamic desirable
- Operational Mode: trunk
- Negotiation: On

### Test Full Connectivity
```cisco
SWA# ping 192.168.99.254
PC1> ping 192.168.10.11
PC2> ping 192.168.20.21
```

**All should work** with trunks configured

---

## Verification Commands Summary

### Check VLANs
```cisco
show vlan brief
show vlan id [number]
```

### Check Trunks
```cisco
show interfaces trunk
show interfaces [interface] switchport
show interfaces [interface] trunk
```

### Check DTP
```cisco
show dtp interface [interface]
```

### Check Connectivity
```cisco
ping [ip-address]
show ip interface brief
```

---

## Troubleshooting Tips

### Issue: Switches can't ping each other
**Check**:
- Trunk configured on both ends?
- Native VLAN matches?
- VLAN 99 exists on all switches?
- SVI interfaces not shutdown?

### Issue: PCs in same VLAN can't communicate
**Check**:
- Both PCs in correct VLAN?
- Trunk carrying the VLAN?
- Ports in access mode?
- IP addresses in same subnet?

### Issue: Trunk won't form (Part 4)
**Check**:
- SWC in dynamic auto?
- SWA in dynamic desirable?
- Both sides have matching native VLAN?
- Cables connected?

### Issue: Native VLAN mismatch warning
**Solution**:
```cisco
! Make sure both sides match
switchport trunk native vlan 100
```

---

## Complete Configuration Examples

### SWA Full Config
```cisco
! VLANs
vlan 10
 name Admin
vlan 20
 name Accounts
vlan 30
 name HR
vlan 40
 name Voice
vlan 99
 name Management
vlan 100
 name Native

! Management SVI
interface vlan 99
 ip address 192.168.99.252 255.255.255.0
 no shutdown

! Static Trunk to SWB
interface gigabitethernet 0/1
 switchport mode trunk
 switchport trunk native vlan 100
 switchport nonegotiate

! Dynamic Trunk to SWC
interface gigabitethernet 0/2
 switchport mode dynamic desirable
 switchport trunk native vlan 100
```

### SWB Full Config
```cisco
! VLANs (same as SWA)

! Access Ports
interface fastethernet 0/1
 switchport mode access
 switchport access vlan 10

interface fastethernet 0/2
 switchport mode access
 switchport access vlan 20

interface fastethernet 0/3
 switchport mode access
 switchport access vlan 30

! Management SVI
interface vlan 99
 ip address 192.168.99.253 255.255.255.0
 no shutdown

! Static Trunk to SWA
interface gigabitethernet 0/1
 switchport mode trunk
 switchport trunk native vlan 100
 switchport nonegotiate
```

### SWC Full Config
```cisco
! VLANs (same as others)

! Access Ports
interface fastethernet 0/1
 switchport mode access
 switchport access vlan 10

interface fastethernet 0/2
 switchport mode access
 switchport access vlan 20

interface fastethernet 0/3
 switchport mode access
 switchport access vlan 30

! Voice VLAN Port
interface fastethernet 0/4
 switchport mode access
 switchport access vlan 10
 switchport voice vlan 40

! Management SVI
interface vlan 99
 ip address 192.168.99.254 255.255.255.0
 no shutdown

! Dynamic Trunk to SWA (default mode)
interface gigabitethernet 0/2
 switchport mode dynamic auto
 switchport trunk native vlan 100
```

---

## Key Concepts Demonstrated

✅ **VLAN Creation** - Multiple VLANs with meaningful names  
✅ **Access Port Assignment** - Assigning ports to specific VLANs  
✅ **Voice VLAN** - Dual VLAN on single port  
✅ **Management VLAN** - Separate VLAN for switch management  
✅ **Native VLAN** - Non-default native VLAN (100 instead of 1)  
✅ **Static Trunking** - Manual trunk with DTP disabled  
✅ **Dynamic Trunking** - DTP negotiation between switches  

---

## CCNA Exam Relevance

**Skills Tested**:
- VLAN configuration and naming
- Port assignment to VLANs
- Voice VLAN configuration
- Static trunk configuration
- DTP understanding and configuration
- Native VLAN configuration
- Switch management interface (SVI)

---

*This lab covers fundamental VLAN and trunking concepts essential for CCNA certification.*
