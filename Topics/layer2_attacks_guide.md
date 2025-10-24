# CCNA Layer 2 Attacks Guide

## Overview of Layer 2 Attacks

Layer 2 attacks target weaknesses in **switching and VLAN protocols** to gain unauthorized access or disrupt network operations.

---

## 1. VLAN Attacks

### VLAN Hopping Attack

**What it is**: Attacker gains access to traffic on other VLANs

**Two Methods**:

#### Method 1: Switch Spoofing
- Attacker sends DTP frames
- Tricks switch into forming trunk
- Attacker can now access all VLANs

```
[Attacker PC] --DTP frames--> [Switch]
                              Port becomes trunk
                              Attacker sees all VLANs
```

**Mitigation**:
```cisco
interface fastethernet 0/1
 switchport mode access
 switchport nonegotiate
```

#### Method 2: Double-Tagging
- Attacker sends frame with **two VLAN tags**
- Outer tag = Native VLAN
- Inner tag = Target VLAN
- First switch removes outer tag
- Frame forwarded to target VLAN

```
[Attacker] --[VLAN1][VLAN20][Data]--> [SW1] removes VLAN1
                                       [VLAN20][Data]--> [SW2]
                                                         Delivers to VLAN20
```

**Mitigation**:
```cisco
! Change native VLAN from default (1)
interface gigabitethernet 0/1
 switchport trunk native vlan 999
 
! Disable unused VLANs
vlan 999
 name UNUSED
```

### VLAN Double-Tagging Attack

**Requirements**:
- Attacker must be on **native VLAN**
- Network must have **trunk links**
- Works only in **one direction** (attacker can send but not receive)

**Prevention**:
- Don't use VLAN 1 as native VLAN
- Don't put user ports on native VLAN
- Disable DTP on access ports

---

## 2. DHCP Attacks

### DHCP Starvation Attack

**What it is**: Attacker floods network with fake DHCP requests

**How it works**:
```
1. Attacker sends thousands of DHCP DISCOVER messages
2. Uses different fake MAC addresses
3. DHCP server assigns all available IPs
4. Legitimate users cannot get IP addresses
5. Network unusable (DoS)
```

**Impact**: DHCP server runs out of addresses

**Mitigation**: Port security + DHCP snooping
```cisco
interface fastethernet 0/1
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation shutdown
```

---

### DHCP Spoofing (Rogue DHCP Server)

**What it is**: Attacker runs fake DHCP server

**How it works**:
```
1. Attacker sets up rogue DHCP server
2. Responds to DHCP requests faster than legitimate server
3. Gives clients wrong configuration:
   - Wrong default gateway (attacker's IP)
   - Wrong DNS server (attacker's DNS)
4. Attacker can intercept/modify all traffic (MITM)
```

**Impact**: 
- Traffic redirected through attacker
- Man-in-the-middle attack
- Data theft possible

**Mitigation**: DHCP Snooping
```cisco
! Enable DHCP snooping globally
ip dhcp snooping
ip dhcp snooping vlan 10,20,30

! Trust port connected to legitimate DHCP server
interface gigabitethernet 0/1
 ip dhcp snooping trust

! All other ports untrusted (default)
! Rate limit DHCP packets
interface range fastethernet 0/1-24
 ip dhcp snooping limit rate 10
```

---

## 3. ARP Attacks

### ARP Spoofing/Poisoning

**What it is**: Attacker sends fake ARP messages

**How it works**:
```
Normal ARP:
PC asks: "Who has 192.168.1.1?"
Router replies: "I do! My MAC is 00:11:22:33:44:55"

ARP Spoofing:
Attacker sends: "192.168.1.1 is at AA:BB:CC:DD:EE:FF" (attacker's MAC)
PC updates ARP table with attacker's MAC
PC now sends traffic to attacker instead of router
```

**Impact**:
- Man-in-the-middle attack
- Attacker intercepts all traffic
- Can read/modify data

**Mitigation**: Dynamic ARP Inspection (DAI)
```cisco
! Enable DHCP snooping first (DAI depends on it)
ip dhcp snooping
ip dhcp snooping vlan 10

! Enable DAI
ip arp inspection vlan 10

! Trust uplink ports
interface gigabitethernet 0/1
 ip arp inspection trust
 ip dhcp snooping trust
```

---

## 4. Address Spoofing Attack

**What it is**: Attacker uses fake MAC or IP addresses

**Types**:
- **MAC Spoofing**: Change MAC to bypass port security
- **IP Spoofing**: Use someone else's IP address
- **DHCP Spoofing**: Covered above

**Mitigation**: 
- Port security (MAC)
- IP Source Guard (IP)
- DHCP snooping + DAI

**IP Source Guard**:
```cisco
! Enable DHCP snooping first
ip dhcp snooping
ip dhcp snooping vlan 10

! Enable IP Source Guard on access ports
interface fastethernet 0/1
 ip verify source
```

---

## 5. STP Attacks

### STP Manipulation Attack

**What it is**: Attacker tries to become root bridge

**How it works**:
```
1. Attacker sends fake BPDUs
2. Claims to have better Bridge ID (lower priority)
3. Network recalculates spanning tree
4. Attacker becomes root bridge
5. All traffic flows through attacker (MITM)
```

**Impact**:
- Attacker can intercept traffic
- Network topology disrupted
- Potential loops if attacker fails

**Mitigation**: BPDU Guard + Root Guard
```cisco
! Enable BPDU Guard on access ports (portfast)
interface fastethernet 0/1
 spanning-tree portfast
 spanning-tree bpduguard enable

! Enable Root Guard on uplinks
interface gigabitethernet 0/1
 spanning-tree guard root
```

---

## 6. CDP Reconnaissance Attack

### CDP Information Gathering

**What it is**: Attacker uses CDP to gather network info

**What CDP reveals**:
- Device hostname
- IOS version
- IP addresses
- Device model
- VLAN information
- Platform details

**How attacker uses it**:
```
1. Connect to network
2. Listen to CDP advertisements
3. Gather device information
4. Identify vulnerabilities
5. Plan targeted attacks
```

**Mitigation**: Disable CDP on untrusted ports
```cisco
! Disable CDP globally
no cdp run

! Or disable per interface (access ports)
interface fastethernet 0/1
 no cdp enable

! Keep CDP on trunk ports for management
interface gigabitethernet 0/1
 cdp enable
```

---

## Layer 2 Security Best Practices

### Complete Security Configuration

**On Access Ports** (user-facing):
```cisco
interface fastethernet 0/1
 ! Force access mode
 switchport mode access
 switchport access vlan 10
 
 ! Disable DTP
 switchport nonegotiate
 
 ! Port security
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation shutdown
 switchport port-security mac-address sticky
 
 ! Spanning tree
 spanning-tree portfast
 spanning-tree bpduguard enable
 
 ! Disable CDP
 no cdp enable
```

**On Trunk Ports** (switch-to-switch):
```cisco
interface gigabitethernet 0/1
 ! Force trunk mode
 switchport mode trunk
 switchport nonegotiate
 
 ! Change native VLAN
 switchport trunk native vlan 999
 
 ! Allow only needed VLANs
 switchport trunk allowed vlan 10,20,30
 
 ! Root guard (optional)
 spanning-tree guard root
```

**Global Security Settings**:
```cisco
! DHCP Snooping
ip dhcp snooping
ip dhcp snooping vlan 10,20,30

! Dynamic ARP Inspection
ip arp inspection vlan 10,20,30

! Trust uplink ports
interface gigabitethernet 0/1
 ip dhcp snooping trust
 ip arp inspection trust
```

---

## Attack Summary Table

| Attack | Layer | Target | Impact | Mitigation |
|--------|-------|--------|--------|------------|
| **VLAN Hopping** | 2 | VLANs | Access other VLANs | Disable DTP, change native VLAN |
| **DHCP Starvation** | 2 | DHCP | DoS, no IPs available | Port security, DHCP snooping |
| **DHCP Spoofing** | 2 | DHCP | MITM, traffic redirection | DHCP snooping |
| **ARP Spoofing** | 2 | ARP | MITM, intercept traffic | DAI |
| **STP Attack** | 2 | STP | Become root, MITM | BPDU Guard, Root Guard |
| **CDP Recon** | 2 | CDP | Info gathering | Disable CDP on access ports |

---

## Mitigation Technology Summary

### DHCP Snooping
- **Purpose**: Prevent rogue DHCP servers
- **How**: Trust legitimate DHCP server ports only
- **Builds**: DHCP binding table (IP-MAC-Port mapping)
- **Required for**: DAI and IP Source Guard

### Dynamic ARP Inspection (DAI)
- **Purpose**: Prevent ARP spoofing
- **How**: Validates ARP packets against DHCP snooping table
- **Requires**: DHCP snooping enabled first

### IP Source Guard
- **Purpose**: Prevent IP spoofing
- **How**: Filters traffic based on DHCP snooping table
- **Validates**: Source IP matches MAC and port

### Port Security
- **Purpose**: Limit MAC addresses per port
- **How**: Learns and restricts MAC addresses
- **Prevents**: MAC flooding, unauthorized devices

### BPDU Guard
- **Purpose**: Prevent STP attacks
- **How**: Shuts down port if BPDU received
- **Used on**: Portfast-enabled access ports

### Root Guard
- **Purpose**: Prevent unauthorized root bridge
- **How**: Blocks ports from becoming root port
- **Used on**: Trunk ports to distribution/access

---

## CCNA Exam Key Points

**Know**:
- VLAN hopping uses DTP or double-tagging
- DHCP spoofing = rogue DHCP server (MITM)
- ARP spoofing = fake ARP replies (MITM)
- STP attack = attacker becomes root bridge
- CDP reveals sensitive device information

**Mitigations**:
- DTP → `switchport nonegotiate`
- DHCP attacks → DHCP snooping
- ARP spoofing → DAI
- STP attacks → BPDU Guard + Root Guard
- CDP recon → `no cdp enable`

**Common Questions**:
- "How to prevent VLAN hopping?" → Disable DTP, change native VLAN
- "How to stop rogue DHCP server?" → DHCP snooping
- "How to prevent ARP spoofing?" → Dynamic ARP Inspection
- "What does BPDU Guard do?" → Shuts port if BPDU received
- "Why disable CDP on access ports?" → Prevents info gathering

---

## Quick Command Reference

```cisco
! Disable DTP (VLAN hopping prevention)
switchport mode access
switchport nonegotiate

! DHCP Snooping (DHCP attack prevention)
ip dhcp snooping
ip dhcp snooping vlan [vlan-id]
interface [interface]
 ip dhcp snooping trust

! DAI (ARP spoofing prevention)
ip arp inspection vlan [vlan-id]
interface [interface]
 ip arp inspection trust

! BPDU Guard (STP attack prevention)
interface [interface]
 spanning-tree portfast
 spanning-tree bpduguard enable

! Disable CDP (reconnaissance prevention)
no cdp enable
```

---

*Layer 2 security requires multiple defenses working together - defense in depth!*