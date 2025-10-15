# DTP (Dynamic Trunking Protocol) Guide

## What is DTP?

**Dynamic Trunking Protocol (DTP)** is a Cisco proprietary protocol that automatically negotiates trunking between two switches.

**Purpose**: 
- Automatically establish trunk links
- Negotiate trunk encapsulation (ISL or 802.1Q)
- Reduce manual configuration

**Note**: Cisco proprietary - only works between Cisco switches

---

## Switchport Modes

### Access Mode
```cisco
switchport mode access
```
- **Purpose**: Connects to end devices (PCs, printers)
- **Behavior**: Never becomes a trunk
- **DTP**: Sends DTP frames stating it's access
- **Use**: End-user ports

### Trunk Mode
```cisco
switchport mode trunk
```
- **Purpose**: Connects to other switches/routers
- **Behavior**: Always a trunk
- **DTP**: Sends DTP frames stating it's trunk
- **Use**: Switch-to-switch connections

### Dynamic Auto (Default on most switches)
```cisco
switchport mode dynamic auto
```
- **Purpose**: Passively waits for trunk negotiation
- **Behavior**: Becomes trunk ONLY if other side is trunk or desirable
- **DTP**: Responds to DTP frames
- **Use**: Default mode (not recommended for production)

### Dynamic Desirable
```cisco
switchport mode dynamic desirable
```
- **Purpose**: Actively tries to form trunk
- **Behavior**: Becomes trunk if other side is trunk, desirable, or auto
- **DTP**: Actively sends DTP frames
- **Use**: Aggressive trunk negotiation (not recommended)

---

## DTP Negotiation Results

### Trunk Formation Table

| Side A | Side B | Result |
|--------|--------|--------|
| **Trunk** | Trunk | ✅ Trunk |
| **Trunk** | Dynamic Auto | ✅ Trunk |
| **Trunk** | Dynamic Desirable | ✅ Trunk |
| **Trunk** | Access | ❌ Error (misconfig) |
| **Dynamic Desirable** | Dynamic Desirable | ✅ Trunk |
| **Dynamic Desirable** | Dynamic Auto | ✅ Trunk |
| **Dynamic Desirable** | Access | ❌ Access |
| **Dynamic Auto** | Dynamic Auto | ❌ Access |
| **Dynamic Auto** | Access | ❌ Access |
| **Access** | Access | ❌ Access |

**Key Rule**: Both sides must "agree" to trunk. Auto + Auto = No trunk (both waiting)

---

## Disabling DTP (Best Practice)

### Disable on Access Ports
```cisco
interface fastethernet 0/1
switchport mode access
switchport nonegotiate
```
**Why**: Security - prevents trunk negotiation attacks

### Disable on Trunk Ports
```cisco
interface gigabitethernet 0/1
switchport mode trunk
switchport nonegotiate
```
**Why**: Predictable behavior, faster convergence

---

## Verifying DTP

### Check Port Mode
```cisco
Switch# show interfaces gigabitethernet 0/1 switchport
```
**Look for**:
- Administrative Mode: trunk/access/dynamic auto/dynamic desirable
- Operational Mode: trunk/static access
- Administrative Trunking Encapsulation
- Negotiation of Trunking: On/Off

**Example Output**:
```
Name: Gi0/1
Switchport: Enabled
Administrative Mode: dynamic auto
Operational Mode: static access
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: native
Negotiation of Trunking: On
```

### Check DTP Packets
```cisco
Switch# show dtp interface gigabitethernet 0/1
```
**Shows**:
- DTP status
- Packets sent/received
- Trunk status

---

## Configuration Examples

### Scenario 1: Manual Trunk (Recommended)
**Switch A**:
```cisco
interface gigabitethernet 0/1
switchport mode trunk
switchport trunk native vlan 99
switchport trunk allowed vlan 10,20,30
switchport nonegotiate
```

**Switch B**:
```cisco
interface gigabitethernet 0/1
switchport mode trunk
switchport trunk native vlan 99
switchport trunk allowed vlan 10,20,30
switchport nonegotiate
```

**Result**: ✅ Trunk, DTP disabled, secure

---

### Scenario 2: Access Port (Secure)
```cisco
interface fastethernet 0/10
switchport mode access
switchport access vlan 10
switchport nonegotiate
spanning-tree portfast
```

**Result**: ✅ Access port, DTP disabled, portfast enabled

---

### Scenario 3: Dynamic (Not Recommended)
**Switch A**:
```cisco
interface gigabitethernet 0/1
switchport mode dynamic desirable
```

**Switch B**:
```cisco
interface gigabitethernet 0/1
switchport mode dynamic auto
```

**Result**: ✅ Trunk forms, but unpredictable and insecure

---

## Packet Tracer Configuration

### Step 1: Configure Trunk
```cisco
Switch(config)# interface gigabitethernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport nonegotiate
Switch(config-if)# end
```

### Step 2: Verify
```cisco
Switch# show interfaces gigabitethernet 0/1 switchport
Switch# show interfaces trunk
```

### Step 3: Check Both Sides
- Verify both switches show "trunk" as operational mode
- Check allowed VLANs match
- Verify native VLAN matches

---

## Security Concerns

### DTP Attacks

**VLAN Hopping**: Attacker sends DTP frames to form trunk, accessing all VLANs

**Prevention**:
```cisco
! On all access ports
switchport mode access
switchport nonegotiate

! On unused ports
shutdown
```

---

## Best Practices

✅ **Always disable DTP** on access ports  
✅ **Manually configure trunks** (switchport mode trunk)  
✅ **Use nonegotiate** on all configured ports  
✅ **Shutdown unused ports**  
✅ **Document trunk configurations**  
✅ **Match native VLAN** on both sides  

❌ **Never use** dynamic auto in production  
❌ **Never use** dynamic desirable in production  
❌ **Don't rely** on DTP auto-negotiation  

---

## CCNA Exam Tips

**Know**:
- DTP is Cisco proprietary
- 4 switchport modes (access, trunk, dynamic auto, dynamic desirable)
- Auto + Auto = No trunk
- Trunk + Auto = Trunk
- `switchport nonegotiate` disables DTP
- Default mode is usually dynamic auto

**Commands**:
- `show interfaces switchport` - Check mode
- `show interfaces trunk` - Verify trunks
- `switchport nonegotiate` - Disable DTP

---

## Quick Reference

### Port Modes Comparison

| Mode | DTP Sent? | Becomes Trunk? | Use |
|------|-----------|----------------|-----|
| **Access** | Yes (I'm access) | Never | End devices |
| **Trunk** | Yes (I'm trunk) | Always | Switch links |
| **Dynamic Auto** | Responds only | If other initiates | Default (bad) |
| **Dynamic Desirable** | Yes (wants trunk) | Actively tries | Legacy (bad) |

### Essential Commands

```cisco
! Configure trunk
switchport mode trunk
switchport nonegotiate

! Configure access
switchport mode access
switchport nonegotiate

! Verify
show interfaces switchport
show interfaces trunk
show dtp interface [name]
```

---

*DTP should be disabled in modern networks for security and predictability.*