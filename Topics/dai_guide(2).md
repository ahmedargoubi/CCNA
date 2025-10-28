# DAI (Dynamic ARP Inspection) Guide

## What is DAI?

**Dynamic ARP Inspection (DAI)** validates ARP packets to prevent ARP spoofing/poisoning attacks.

**Purpose**: Block fake ARP messages that redirect traffic to attackers

---

## ARP Attack Reminder

### Normal ARP:
```
PC: "Who has 192.168.1.1?"
Router: "I do! MAC = 00:11:22:33:44:55"
```

### ARP Spoofing:
```
Attacker: "192.168.1.1 is at AA:BB:CC:DD:EE:FF" (fake)
PC believes attacker
Traffic goes to attacker (MITM)
```

**DAI blocks the fake ARP message**

---

## How DAI Works

### Validation Process

```
ARP packet arrives → DAI checks DHCP snooping table
                   → IP-MAC-Port match?
                   → Yes: Forward
                   → No: Drop
```

### Trusted vs Untrusted Ports

**Untrusted Ports** (default):
- DAI validates all ARP packets
- Drops invalid ARP packets
- Typically: access ports (user devices)

**Trusted Ports**:
- DAI does NOT validate ARP packets
- All ARP packets forwarded
- Typically: uplink ports, server ports, trunk ports

---

## DAI Configuration

### Prerequisites

**IMPORTANT**: DHCP snooping MUST be enabled first!

```cisco
! Step 1: Enable DHCP snooping
ip dhcp snooping
ip dhcp snooping vlan 10

! Trust legitimate DHCP server port
interface gigabitethernet 0/24
 ip dhcp snooping trust
```

### Basic DAI Configuration

```cisco
! Step 2: Enable DAI on VLANs
ip arp inspection vlan 10

! Step 3: Trust uplink/trunk ports
interface gigabitethernet 0/1
 ip arp inspection trust
```

### Complete Example

```cisco
! Enable DHCP snooping (required)
ip dhcp snooping
ip dhcp snooping vlan 10,20,30

! Enable DAI
ip arp inspection vlan 10,20,30

! Trust uplinks/trunks
interface gigabitethernet 0/1
 ip dhcp snooping trust
 ip arp inspection trust

! Untrusted access ports (no commands needed - default)
interface fastethernet 0/5
 ! DAI automatically validates ARP here
```

---

## DAI Validation Checks

### Three Validation Options

**1. Source MAC Validation**
```cisco
ip arp inspection validate src-mac
```
- Checks sender MAC in Ethernet header matches sender MAC in ARP body

**2. Destination MAC Validation**
```cisco
ip arp inspection validate dst-mac
```
- Checks target MAC in Ethernet header matches target MAC in ARP body
- Only for ARP replies

**3. IP Address Validation**
```cisco
ip arp inspection validate ip
```
- Checks for invalid/unexpected IP addresses (0.0.0.0, 255.255.255.255)

### Enabling Multiple Validations

```cisco
! Enable all three (recommended)
ip arp inspection validate src-mac dst-mac ip
```

**Note**: Each command overwrites previous - use all in one command

---

## Rate Limiting (Optional but Recommended)

**Purpose**: Protect against ARP packet floods

```cisco
! Limit ARP packets per second on untrusted ports
interface fastethernet 0/5
 ip arp inspection limit rate 15

! Unlimited on trusted ports (default)
interface gigabitethernet 0/1
 ip arp inspection limit rate none
```

**Default**: 15 packets per second  
**If exceeded**: Port moves to err-disabled state

---

## Static ARP Entries (For Non-DHCP Devices)

**Problem**: DAI relies on DHCP snooping table. What about static IP devices?

**Solution**: Add static ARP ACL

```cisco
! Create ARP ACL
arp access-list STATIC-HOSTS
 permit ip host 192.168.10.100 mac host 0011.2233.4455
 permit ip host 192.168.10.101 mac host 0011.2233.6677

! Apply to VLAN
ip arp inspection filter STATIC-HOSTS vlan 10
```

---

## Verification Commands

### Check DAI Status
```cisco
Switch# show ip arp inspection
```

**Output shows**:
- Which VLANs have DAI enabled
- Number of packets forwarded/dropped
- Validation configuration

---

### Check DAI per Interface
```cisco
Switch# show ip arp inspection interfaces
```

**Shows**:
- Trust state (Trusted/Untrusted)
- Rate limit
- Packets forwarded/dropped per interface

---

### Check DAI Statistics
```cisco
Switch# show ip arp inspection statistics
```

**Shows**:
- Total packets processed
- Packets dropped and why
- Validation failures

---

### Check DHCP Snooping Binding Table
```cisco
Switch# show ip dhcp snooping binding
```

**This is what DAI uses for validation!**

---

## Common DAI Scenarios

### Scenario 1: Standard Access Layer

```cisco
! Enable foundation
ip dhcp snooping
ip dhcp snooping vlan 10
ip arp inspection vlan 10

! Trust uplink to distribution
interface gigabitethernet 0/1
 ip dhcp snooping trust
 ip arp inspection trust

! Access ports (untrusted - default)
interface range fastethernet 0/1-24
 ! No commands needed - untrusted by default
 ip arp inspection limit rate 15
```

---

### Scenario 2: With Static IP Server

```cisco
! Basic DAI configuration
ip dhcp snooping
ip dhcp snooping vlan 10
ip arp inspection vlan 10

! Add static ARP entry for server
arp access-list SERVER
 permit ip host 192.168.10.5 mac host 1111.2222.3333

ip arp inspection filter SERVER vlan 10

! Trust server port (alternative to ARP ACL)
interface fastethernet 0/10
 ip arp inspection trust
```

---

## DAI Configuration Guidelines

### Always Trust These Ports:
- ✅ Uplink ports (to distribution/core)
- ✅ Trunk ports (switch-to-switch)
- ✅ Ports to DHCP servers
- ✅ Ports to routers/gateways
- ✅ Ports to trusted servers (if static IP)

### Never Trust These Ports:
- ❌ Access ports to end users
- ❌ Ports to unknown devices
- ❌ Guest network ports
- ❌ Untrusted devices

---

## Troubleshooting DAI

### Issue: Legitimate Traffic Blocked

**Symptom**: Valid devices can't communicate

**Check**:
```cisco
show ip arp inspection statistics
show ip dhcp snooping binding
```

**Solutions**:
- Trust the port if legitimate device
- Add static ARP entry if non-DHCP device
- Verify DHCP snooping is working

---

### Issue: Port in err-disabled

**Symptom**: Port shuts down due to rate limit

**Check**:
```cisco
show interfaces status err-disabled
```

**Fix**:
```cisco
! Increase rate limit
interface fastethernet 0/5
 ip arp inspection limit rate 30

! Re-enable port
shutdown
no shutdown
```

---

### Issue: DAI Not Working

**Checklist**:
1. ✅ DHCP snooping enabled?
2. ✅ DHCP snooping on correct VLAN?
3. ✅ DAI enabled on VLAN?
4. ✅ DHCP server port trusted?
5. ✅ Uplink ports trusted?
6. ✅ Binding table populated?

---

## Quick Command Reference

### Essential Configuration
```cisco
! Enable DHCP snooping (required first)
ip dhcp snooping
ip dhcp snooping vlan [vlan-id]

! Enable DAI
ip arp inspection vlan [vlan-id]

! Trust ports
interface [interface]
 ip dhcp snooping trust
 ip arp inspection trust

! Validation (optional)
ip arp inspection validate src-mac dst-mac ip

! Rate limit (optional)
interface [interface]
 ip arp inspection limit rate [pps]
```

### Verification
```cisco
show ip arp inspection
show ip arp inspection interfaces
show ip arp inspection statistics
show ip dhcp snooping binding
```

---

## CCNA Exam Key Points

**Remember**:
- DAI prevents ARP spoofing/poisoning
- DHCP snooping MUST be enabled first
- Trust uplinks, trunks, DHCP server ports
- Untrusted ports validate ARP packets
- Uses DHCP snooping binding table
- Rate limiting prevents ARP floods
- Static ARP ACL for non-DHCP devices

**Common Questions**:
- "What does DAI prevent?" → ARP spoofing
- "What must be enabled before DAI?" → DHCP snooping
- "Which ports to trust?" → Uplinks, trunks, DHCP servers
- "Where does DAI check ARP?" → DHCP snooping binding table
- "What happens if rate exceeded?" → Port err-disabled

---

## DAI Dependencies

```
DHCP Snooping
      ↓
Builds binding table (IP-MAC-Port)
      ↓
DAI uses binding table
      ↓
Validates ARP packets
      ↓
Blocks ARP spoofing
```

**Both must be configured on same VLANs!**

---

*DAI is your defense against ARP-based man-in-the-middle attacks!*