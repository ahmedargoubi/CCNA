
# DHCP Snooping (Layer 2 Security) - CCNA Notes

## What is DHCP Snooping?

- **DHCP Snooping** is a **Layer 2 security feature** of Cisco switches.  
- It is used to **filter DHCP messages** received on **untrusted ports**.  
- Only **DHCP traffic** is inspected; non-DHCP traffic is **not affected**.  
- **All ports are untrusted by default.**  
  - **Uplink ports** (toward DHCP server) → configured as **trusted**.  
  - **Downlink ports** (toward clients) → remain **untrusted**.  

---

## DHCP-Based Attacks

### 1. DHCP Starvation Attack  
- Attacker floods the DHCP server with **DHCP Discover messages** using **spoofed MAC addresses**.  
- The DHCP pool becomes **exhausted**, preventing legitimate clients from obtaining IP addresses.  
- Result: **Denial of Service (DoS)**.  

### 2. DHCP Poisoning (Man-in-the-Middle Attack)  
- Attacker runs a **rogue DHCP server**.  
- Clients usually accept the **first DHCP Offer** they receive.  
- Rogue server assigns:  
  - **IP Address**  
  - **Default Gateway = Attacker’s IP**  
- Traffic is sent to the attacker first, who can **inspect, modify, or drop packets** before forwarding them.  
- This compromises **confidentiality** and **integrity** of communications.  

---

## DHCP Messages Overview

### DHCP Server Messages
- **OFFER** → Proposes an IP address to the client.  
- **ACK** → Confirms IP lease to client.  
- **NAK** → Declines client request.  

### DHCP Client Messages
- **DISCOVER** → Broadcast to find DHCP server.  
- **REQUEST** → Requests IP address from DHCP server.  
- **RELEASE** → Client releases assigned IP.  
- **DECLINE** → Client rejects offered IP (e.g., duplicate detected).  

---

## How DHCP Snooping Works

1. **Trusted Port Behavior**  
   - DHCP messages are forwarded **without inspection**.  

2. **Untrusted Port Behavior**  
   - DHCP **server messages** (OFFER, ACK, NAK) → **discarded**.  
   - DHCP **client messages** are inspected:  
     - **DISCOVER / REQUEST** → Check if **Source MAC = CHADDR field**.  
       - Match = forward  
       - Mismatch = drop  
     - **RELEASE / DECLINE** → Check if **Source IP + Interface match binding table**.  
       - Match = forward  
       - Mismatch = drop  

3. **DHCP Snooping Binding Table**  
   - Created when a client successfully receives an IP lease.  
   - Stores: **MAC, IP, VLAN, Interface, Lease time**.  

---

## DHCP Snooping Configuration

### Basic Configuration (on a switch)
```bash
SW(config)# ip dhcp snooping
SW(config)# ip dhcp snooping vlan 10
SW(config)# interface g0/1
SW(config-if)# ip dhcp snooping trust
SW(config)# interface g0/2
SW(config-if)# ip dhcp snooping limit rate 10
```

- `ip dhcp snooping` → enable globally  
- `ip dhcp snooping vlan X` → enable for VLAN  
- `ip dhcp snooping trust` → marks interface as trusted  
- `ip dhcp snooping limit rate <number>` → sets rate-limit for DHCP messages  

---

## DHCP Snooping Rate-Limiting

- Prevents DHCP starvation attacks.  
- Limits **rate of DHCP messages** allowed per port.  
- Exceeding the limit → port goes **err-disabled**.  
- Can be recovered:  
```bash
SW(config)# errdisable recovery cause dhcp-rate-limit
SW(config)# errdisable recovery interval 30
```  

---

## DHCP Option 82 (Relay Agent Information Option)

- **Option 82** provides info about:  
  - Which switch/interface/VLAN received the DHCP request.  
- Switches with DHCP Snooping enabled:  
  - **Automatically insert Option 82** into client messages.  
- By default, DHCP messages with **Option 82 received on untrusted ports are dropped**.  
- Must configure consistently across switches to avoid drops.  

Example:  
```bash
SW(config)# ip dhcp snooping information option
SW(config)# no ip dhcp snooping information option   # disables Option 82
```  

---

## Verification & Commands

```bash
SW# show ip dhcp snooping
SW# show ip dhcp snooping binding
SW# debug ip dhcp snooping
```

- `show ip dhcp snooping` → Displays global config and VLANs protected.  
- `show ip dhcp snooping binding` → Shows current binding table entries.  
- `debug ip dhcp snooping` → Used for troubleshooting.  

---

## Command Summary

- `ip dhcp snooping` → Enables snooping globally.  
- `ip dhcp snooping vlan <X>` → Enables snooping for VLAN.  
- `ip dhcp snooping trust` → Configures trusted interface.  
- `ip dhcp snooping limit rate <rate>` → Limits rate of DHCP messages.  
- `errdisable recovery cause dhcp-rate-limit` → Auto-recovery from shutdown.  
- `ip dhcp snooping information option` → Enables Option 82 insertion.  

---

## Key Takeaways

- **Prevents DHCP starvation & poisoning attacks.**  
- **Trust only uplink ports** toward DHCP servers.  
- **Untrusted ports** (client-facing) are inspected.  
- **DHCP Binding Table** = critical for validation.  
- Supports **rate-limiting** & **Option 82** for better security.  

---

✅ **For CCNA**: Understand **concepts, attacks, configuration steps, and key commands.**
