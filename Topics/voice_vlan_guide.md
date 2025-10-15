# Voice VLAN - CCNA Guide

## Table of Contents
1. [What is Voice VLAN?](#what-is-voice-vlan)
2. [Why Voice VLANs Are Needed](#why-voice-vlans-are-needed)
3. [How Voice VLAN Works](#how-voice-vlan-works)
4. [Configuration](#configuration)
5. [Verification](#verification)
6. [Best Practices](#best-practices)
7. [Quick Reference](#quick-reference)

---

## What is Voice VLAN?

### Simple Definition

**Voice VLAN** is a special VLAN designed specifically for **VoIP (Voice over IP) traffic** to separate voice from data traffic on the same switch port.

### The Concept

**Traditional Setup (Problem):**
```
One device per port:
Port 1 → PC (Data VLAN 10)
Port 2 → IP Phone (Data VLAN 10)

Problem: Wastes ports!
```

**Voice VLAN Solution:**
```
One port, two VLANs:
Port 1 → PC (Data VLAN 10) + IP Phone (Voice VLAN 20)
       ↓
    IP Phone ←→ PC
    
Both devices, one port!
```

### Real-World Scenario

**Office Setup:**
```
┌─────────────┐
│  IP Phone   │ ← Voice VLAN 20 (QoS priority)
└──────┬──────┘
       │
       │ (Single cable)
       │
┌──────┴──────┐
│   Switch    │
│   Port Fa0/1│
└─────────────┘
       │
       │ (PC connection through phone)
       │
┌──────┴──────┐
│     PC      │ ← Data VLAN 10 (normal traffic)
└─────────────┘

One port = Two VLANs!
```

---

## Why Voice VLANs Are Needed

### 1. Port Efficiency

**Without Voice VLAN:**
```
Desk 1: Port 1 (Phone) + Port 2 (PC) = 2 ports used
Desk 2: Port 3 (Phone) + Port 4 (PC) = 2 ports used
Desk 3: Port 5 (Phone) + Port 6 (PC) = 2 ports used

10 desks = 20 ports needed!
```

**With Voice VLAN:**
```
Desk 1: Port 1 (Phone + PC) = 1 port used
Desk 2: Port 2 (Phone + PC) = 1 port used
Desk 3: Port 3 (Phone + PC) = 1 port used

10 desks = 10 ports needed!
Saves 50% of ports!
```

### 2. Quality of Service (QoS)

**Voice traffic requirements:**
- **Low latency** (< 150ms)
- **Low jitter** (< 30ms)
- **Low packet loss** (< 1%)
- **Priority over data**

**With separate VLAN:**
```
Voice VLAN 20: High priority (voice calls)
Data VLAN 10: Normal priority (emails, web)

Switch prioritizes voice traffic automatically!
```

**Example:**
```
Network congestion occurs:
- Voice packets: Sent first (clear call)
- Data packets: Wait slightly (web page loads a bit slower)

Result: Phone calls stay crystal clear!
```

### 3. Network Segmentation

**Security and management benefits:**

```
Voice VLAN 20:
- Separate subnet (10.20.0.0/24)
- Dedicated DHCP scope
- Different gateway
- Isolated from data traffic
- Easy to monitor/troubleshoot

Data VLAN 10:
- Separate subnet (10.10.0.0/24)
- Different security policies
- Normal user traffic
```

### 4. Simplified Management

**Centralized voice management:**
```
All IP phones in one VLAN:
- Easy to apply policies
- Consistent QoS settings
- Simple monitoring
- Centralized updates
- Easy troubleshooting
```

---

## How Voice VLAN Works

### Access Port with Voice VLAN

**Special port configuration:**

```
Switch Port Configuration:
├─ Access Mode (not trunk!)
├─ Data VLAN: 10 (untagged)
└─ Voice VLAN: 20 (tagged with 802.1Q)
```

### Tagging Explained

**Frame tagging:**

**PC Traffic (Untagged):**
```
┌──────────────────────────────────┐
│ Ethernet Header │ Data │   FCS   │
└──────────────────────────────────┘
No VLAN tag (native VLAN)
Goes to Data VLAN 10
```

**IP Phone Traffic (Tagged):**
```
┌─────────────────────────────────────────────┐
│ Ethernet │ 802.1Q Tag │ Data │     FCS     │
│ Header   │ (VLAN 20)  │      │             │
└─────────────────────────────────────────────┘
VLAN tag present
Goes to Voice VLAN 20
```

### How It Works Step-by-Step

**Step 1: Phone Powers Up**
```
Switch port provides PoE (Power over Ethernet)
IP Phone boots up
```

**Step 2: Phone Discovers Voice VLAN**
```
Switch tells phone via:
- CDP (Cisco Discovery Protocol), OR
- LLDP-MED (Link Layer Discovery Protocol)

Message: "Use VLAN 20 for voice"
```

**Step 3: Phone Gets IP Address**
```
Phone sends DHCP request on VLAN 20
DHCP server responds with:
- IP address (10.20.0.50)
- Subnet mask
- Gateway
- TFTP server (for config)
```

**Step 4: PC Connects Through Phone**
```
PC connects to phone's built-in switch
PC sends untagged traffic
Goes to Data VLAN 10
```

**Step 5: Dual Traffic Flow**
```
Voice traffic: Tagged (VLAN 20) → High priority
Data traffic: Untagged (VLAN 10) → Normal priority

Both on same physical port!
```

### Visual Traffic Flow

```
┌────────────────────────────────────────────┐
│           IP Phone (VLAN 20)               │
│  ┌──────────────────────────────────┐      │
│  │ Internal 3-port Switch:          │      │
│  │ - Uplink to switch               │      │
│  │ - Phone connection (VLAN 20)     │      │
│  │ - PC connection (VLAN 10)        │      │
│  └──────────────────────────────────┘      │
└────────────────┬───────────────────────────┘
                 │
                 │ Single Cable
                 │
        ┌────────┴────────┐
        │  Switch Port    │
        │  Fa0/1          │
        │  Data: VLAN 10  │
        │  Voice: VLAN 20 │
        └─────────────────┘
                 │
        ┌────────┴────────┐
        │   PC (VLAN 10)  │
        └─────────────────┘
```

### CoS (Class of Service) Tagging

**Automatic QoS marking:**

**802.1p CoS values (0-7):**
```
Voice VLAN traffic automatically tagged:
CoS 5: Voice bearer (actual voice)
CoS 3: Call signaling (setup/teardown)

Data VLAN traffic:
CoS 0: Best effort (default)
```

**Switch behavior:**
```
Queue 1 (Highest): CoS 5 (Voice)
Queue 2: CoS 3 (Signaling)
Queue 3: CoS 0 (Data)

Voice gets priority!
```

---

## Configuration

### Basic Voice VLAN Configuration

#### Step 1: Create VLANs

```cisco
! Create Data VLAN
Switch(config)# vlan 10
Switch(config-vlan)# name DATA
Switch(config-vlan)# exit

! Create Voice VLAN
Switch(config)# vlan 20
Switch(config-vlan)# name VOICE
Switch(config-vlan)# exit
```

#### Step 2: Configure Interface

```cisco
! Enter interface configuration
Switch(config)# interface FastEthernet0/1

! Set to access mode
Switch(config-if)# switchport mode access

! Assign data VLAN (for PC)
Switch(config-if)# switchport access vlan 10

! Assign voice VLAN (for phone)
Switch(config-if)# switchport voice vlan 20

! Enable PoE (if needed)
Switch(config-if)# power inline auto

! Optional: Enable CDP (usually on by default)
Switch(config-if)# cdp enable

! Exit
Switch(config-if)# exit
```

### Configuration with QoS Trust

**Trust phone's CoS markings:**

```cisco
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# switchport voice vlan 20

! Trust CoS values from IP phone
Switch(config-if)# mls qos trust cos

! Or trust DSCP values
Switch(config-if)# mls qos trust dscp
```

### Range Configuration

**Configure multiple ports at once:**

```cisco
! Configure ports 1-24
Switch(config)# interface range FastEthernet0/1-24
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
Switch(config-if-range)# switchport voice vlan 20
Switch(config-if-range)# power inline auto
Switch(config-if-range)# exit
```

### Advanced: Voice VLAN with Trunk to Another Switch

**Uplink configuration:**

```cisco
! Trunk port to another switch
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20
Switch(config-if)# exit
```

---

## Verification

### Essential Show Commands

#### 1. Verify VLAN Configuration

```cisco
Switch# show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------
1    default                          active    
10   DATA                             active    Fa0/1, Fa0/2
20   VOICE                            active    
```

#### 2. Verify Interface Configuration

```cisco
Switch# show interfaces FastEthernet0/1 switchport

Name: Fa0/1
Switchport: Enabled
Administrative Mode: static access
Operational Mode: static access
Administrative Trunking Encapsulation: dot1q
Negotiation of Trunking: Off
Access Mode VLAN: 10 (DATA)
Trunking Native Mode VLAN: 1 (default)
Voice VLAN: 20 (VOICE)  ← Voice VLAN configured!
```

#### 3. Verify CDP/LLDP

**Check if phone discovered:**

```cisco
Switch# show cdp neighbors

Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
SEP001122334455  Fas 0/1           165        H P         IP Phone  Port 1

H = Host (IP Phone)
P = Phone capability
```

#### 4. Verify PoE Status

```cisco
Switch# show power inline FastEthernet0/1

Interface Admin  Oper       Power   Device              Class Max
--------- ------ ---------- ------- ------------------- ----- ----
Fa0/1     auto   on         15.4    IP Phone 7960       3     15.4

Power is being supplied to phone!
```

#### 5. Check MAC Addresses

```cisco
Switch# show mac address-table

VLAN    Mac Address       Type        Ports
----    -----------       --------    -----
10      0011.2233.4455    DYNAMIC     Fa0/1  ← PC MAC
20      0099.8877.6655    DYNAMIC     Fa0/1  ← Phone MAC

Both on same port, different VLANs!
```

---

## Best Practices

### 1. Use Separate Subnets

```
Data VLAN 10: 10.10.0.0/24
Voice VLAN 20: 10.20.0.0/24

Different subnets = Better management
```

### 2. Enable QoS

```cisco
! Globally enable QoS
Switch(config)# mls qos

! Trust phone markings
Switch(config-if)# mls qos trust cos
```

### 3. Use PoE for IP Phones

```cisco
! Enable PoE on ports
Switch(config-if)# power inline auto

! Check PoE budget to ensure enough power
Switch# show power inline
```

### 4. Implement Security

**Limit devices on voice VLAN:**

```cisco
! Port security to allow only one phone
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security maximum 2
Switch(config-if)# switchport port-security violation restrict
```

### 5. Proper VLAN Design

```
Voice VLAN: 
- Use consistent VLAN across all sites
- Typically VLAN 100-199 range
- Document clearly

Example:
Site A: VLAN 100 (Voice)
Site B: VLAN 100 (Voice)
Site C: VLAN 100 (Voice)
```

### 6. Configure DHCP Options

**Voice VLAN DHCP scope:**

```
DHCP Server configuration:
- Subnet: 10.20.0.0/24
- Gateway: 10.20.0.1
- DNS: 10.20.0.10
- Option 150: TFTP server (for phone configs)
- Option 66: TFTP server name
```

---

## Quick Reference

### Voice VLAN Configuration Template

```cisco
! Create VLANs
vlan 10
 name DATA
vlan 20
 name VOICE

! Configure access port with voice VLAN
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
 switchport voice vlan 20
 power inline auto
 spanning-tree portfast
 mls qos trust cos
```

### Key Concepts

| Concept | Description |
|---------|-------------|
| **Voice VLAN** | Separate VLAN for IP phone traffic |
| **Data VLAN** | VLAN for PC/workstation traffic |
| **Tagged** | Voice traffic (802.1Q tag) |
| **Untagged** | Data traffic (native VLAN) |
| **CoS** | Class of Service (Layer 2 QoS) |
| **CDP/LLDP** | Discovery protocol for VLAN info |
| **PoE** | Power over Ethernet for phones |

### Port Types and Voice VLAN

| Port Type | Supports Voice VLAN? | Notes |
|-----------|---------------------|--------|
| **Access** | ✅ Yes | Most common setup |
| **Trunk** | ❌ No | Voice VLAN not needed on trunk |
| **Dynamic** | ❌ No | Should use access mode |

### Common Commands

```cisco
! Show voice VLAN config
show interfaces [interface] switchport

! Show CDP neighbors (phones)
show cdp neighbors

! Show PoE status
show power inline [interface]

! Show MAC addresses per VLAN
show mac address-table vlan [vlan-id]

! Show QoS configuration
show mls qos interface [interface]
```

### Troubleshooting Checklist

**Phone not getting IP address:**
```
1. Check VLAN exists: show vlan brief
2. Check voice VLAN configured: show interfaces switchport
3. Check CDP enabled: show cdp neighbors
4. Check DHCP server accessible on voice VLAN
5. Check trunk allows voice VLAN (if going to another switch)
```

**Phone powered but no voice VLAN:**
```
1. Verify CDP: show cdp neighbors
2. Check voice VLAN command: switchport voice vlan 20
3. Verify phone supports CDP or LLDP
4. Check phone configuration
```

**No PoE to phone:**
```
1. Check PoE enabled: power inline auto
2. Verify PoE budget: show power inline
3. Check cable (Cat5e minimum for PoE)
4. Verify switch supports PoE
```

---

## Remember This!

✅ **Voice VLAN = Separate VLAN for IP phones**  
✅ **One port supports TWO VLANs** (data + voice)  
✅ **Voice traffic is TAGGED** (802.1Q)  
✅ **Data traffic is UNTAGGED** (native VLAN)  
✅ **Use `switchport voice vlan [number]`** to configure  
✅ **CDP/LLDP tells phone which VLAN to use**  
✅ **PoE powers IP phones** through network cable  
✅ **QoS prioritizes voice traffic** (CoS 5)  
✅ **Port stays in ACCESS mode** (not trunk!)  
✅ **Saves 50% of switch ports**  
✅ **Separates voice and data** for security and management  
✅ **Essential for modern VoIP deployments**