# Dynamic ARP Inspection (DAI) - CCNA Guide

## What is Dynamic ARP Inspection (DAI)?

**Dynamic ARP Inspection (DAI)** is a Layer 2 security feature on Cisco switches that is used to filter **ARP messages** received on **untrusted ports**.

- DAI only filters **ARP messages** (non-ARP traffic is unaffected)
- By default, **all ports are untrusted**
- Typically:
  - **Trusted Ports**: Uplink ports (to routers, switches, DHCP servers, gateways)
  - **Untrusted Ports**: Access ports (to end devices)

---

## ARP Review

**ARP (Address Resolution Protocol)** is used to map an IP address to a MAC address.  
Example: A PC sends an **ARP Request** to learn the MAC address of its **default gateway**.

ARP involves two messages:
- **ARP Request**: "Who has IP X.X.X.X? Tell me."
- **ARP Reply**: "IP X.X.X.X is at MAC YY:YY:YY:YY:YY:YY."

---

## Gratuitous ARP (GARP)

A **Gratuitous ARP** is an unsolicited ARP Reply (sent without a request).

- It is sent to the **broadcast MAC address**
- Purpose:
  - Allows devices to **update their ARP tables** quickly
  - Used when an interface comes up, IP changes, or MAC changes

---

## ARP Poisoning (Man-in-the-Middle Attack)

**ARP Poisoning** occurs when an attacker sends **fake ARP messages** to manipulate targets' ARP tables.

- Devices believe the attacker's MAC address is associated with another device's IP (e.g., the gateway)
- As a result, victims send traffic to the attacker instead of the real destination

➡️ Attacker can **intercept, modify, or drop traffic** (Man-in-the-Middle)

---

## How DAI Works

### 1. On Untrusted Ports:
- DAI inspects **ARP packets**
- It checks the **Sender IP/MAC** fields against the **DHCP Snooping Binding Table**
  - ✅ **Match** → Forward the ARP message
  - ❌ **No Match** → Drop the ARP message

### 2. On Trusted Ports:
- ARP messages are forwarded without inspection

### 3. Static Hosts (not using DHCP):
- DAI can use **ARP ACLs** (manually map IP ↔ MAC) to validate traffic

---

## DAI Features

### Integration with DHCP Snooping
Relies on DHCP Snooping Binding Table to validate ARP packets.

### ARP ACLs
Manually configure trusted IP-MAC mappings.

### Rate Limiting
Prevents attackers from overwhelming the switch with ARP traffic.  
If the rate exceeds the configured threshold → interface goes into `err-disabled` state.

### Optional Checks
- Validate ARP **header integrity**
- Ensure IP address consistency

---

## Configuration Example

### Enable DHCP Snooping
```bash
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 10
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# ip dhcp snooping trust
```

### Enable DAI
```bash
Switch(config)# ip arp inspection vlan 10
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# ip arp inspection trust
```

### Show Commands
```bash
Switch# show ip arp inspection interfaces
Switch# show ip arp inspection
```

---

## DAI Rate Limiting

Prevents ARP flooding attacks.

### Example:
```bash
Switch(config-if)# ip arp inspection limit rate 30
```

- If limit is exceeded → port enters err-disabled
- Recovery:
```bash
Switch(config)# errdisable recovery cause arp-inspection
```

---

## ARP ACLs (For Static Hosts)

### Example: Allow only server 192.168.1.100 with MAC AAAA.BBBB.CCCC

```bash
Switch(config)# arp access-list SRV1-ACL
Switch(config-arp-nacl)# permit ip host 192.168.1.100 mac host AAAA.BBBB.CCCC
Switch(config)# ip arp inspection filter SRV1-ACL vlan 10
```

---

## Verification

### Check DAI status:
```bash
Switch# show ip arp inspection
```

### Check statistics and drops:
```bash
Switch# show ip arp inspection statistics
```

---

## Command Review

| Command | Description |
|---------|-------------|
| `ip arp inspection vlan <vlan>` | Enable DAI on VLAN |
| `ip arp inspection trust` | Mark port as trusted |
| `ip arp inspection limit rate <value>` | Set ARP rate limit |
| `arp access-list <name>` | Define ARP ACL for static hosts |
| `show ip arp inspection` | Verify configuration |

---

## Summary

- **ARP Poisoning** is a major Layer 2 attack (man-in-the-middle)
- **DAI** prevents ARP spoofing by validating ARP packets against the DHCP Snooping binding table
- **Ports** must be properly configured as trusted/untrusted
- **DAI** supports rate limiting, optional checks, and ARP ACLs