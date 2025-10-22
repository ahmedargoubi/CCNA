# DHCPv6 Guide - Stateless vs Stateful

## DHCPv6 Overview

**Purpose**: Automatically assign IPv6 addresses and configuration to devices  
**Key Difference from DHCPv4**: Two operation modes - Stateless and Stateful

---

## The Three Flags (RA Message)

Routers use **Router Advertisement (RA)** messages with **3 flags** to tell hosts how to get IPv6 configuration:

### Flag Combinations

| M Flag | O Flag | A Flag | Result | Method |
|--------|--------|--------|--------|--------|
| 0 | 0 | 1 | Use SLAAC only | SLAAC |
| 0 | 1 | 1 | Use SLAAC + DHCPv6 for DNS | **Stateless DHCPv6** |
| 1 | 0 | 0 | Use DHCPv6 for everything | **Stateful DHCPv6** |

### Flag Meanings

**M Flag (Managed Address Configuration)**
- **M=0**: Don't use DHCPv6 for address
- **M=1**: Use DHCPv6 for address (Stateful)

**O Flag (Other Configuration)**
- **O=0**: Don't use DHCPv6 for other info
- **O=1**: Use DHCPv6 for DNS, domain, etc. (Stateless)

**A Flag (Autonomous Address Configuration)**
- **A=1**: Use SLAAC to create address
- **A=0**: Don't use SLAAC

---

## Stateless DHCPv6

### How It Works
1. Host creates IPv6 address using **SLAAC** (from RA message)
2. Host contacts **DHCPv6 server** for DNS, domain name only
3. **No tracking** of which host got what address

### Configuration

**Router (RA with O=1)**:
```cisco
Router(config)# interface g0/0
Router(config-if)# ipv6 address 2001:db8:1::1/64
Router(config-if)# ipv6 nd other-config-flag
Router(config-if)# no shutdown
```

**DHCPv6 Server**:
```cisco
Router(config)# ipv6 dhcp pool STATELESS-POOL
Router(config-dhcpv6)# dns-server 2001:db8:1::100
Router(config-dhcpv6)# domain-name example.com
Router(config-dhcpv6)# exit

Router(config)# interface g0/0
Router(config-if)# ipv6 dhcp server STATELESS-POOL
```

### What Host Gets
- ✅ IPv6 Address: From SLAAC (self-generated)
- ✅ DNS Server: From DHCPv6
- ✅ Domain Name: From DHCPv6
- ✅ Default Gateway: From RA message

### Key Points
- No address assignment by server
- No database of client addresses
- Lightweight operation
- Host generates its own address

---

## Stateful DHCPv6

### How It Works
1. Router RA has **M=1** (managed)
2. Host contacts **DHCPv6 server** for everything
3. Server **assigns and tracks** IPv6 address
4. Similar to DHCPv4

### Configuration

**Router (RA with M=1)**:
```cisco
Router(config)# interface g0/0
Router(config-if)# ipv6 address 2001:db8:1::1/64
Router(config-if)# ipv6 nd managed-config-flag
Router(config-if)# no shutdown
```

**DHCPv6 Server**:
```cisco
Router(config)# ipv6 dhcp pool STATEFUL-POOL
Router(config-dhcpv6)# address prefix 2001:db8:1::/64
Router(config-dhcpv6)# dns-server 2001:db8:1::100
Router(config-dhcpv6)# domain-name example.com
Router(config-dhcpv6)# exit

Router(config)# interface g0/0
Router(config-if)# ipv6 dhcp server STATEFUL-POOL
```

### What Host Gets
- ✅ IPv6 Address: From DHCPv6 server (assigned)
- ✅ DNS Server: From DHCPv6 server
- ✅ Domain Name: From DHCPv6 server
- ✅ Default Gateway: From RA message

### Key Points
- Server assigns address
- Server maintains database (bindings)
- Tracks which client has which address
- More control for administrators

---

## Quick Comparison

| Feature | Stateless | Stateful |
|---------|-----------|----------|
| **Address Assignment** | Host (SLAAC) | Server |
| **DNS/Domain** | Server | Server |
| **Server Tracking** | No | Yes |
| **M Flag** | 0 | 1 |
| **O Flag** | 1 | 0 or 1 |
| **Like DHCPv4?** | No | Yes |
| **Complexity** | Simple | More complex |

---

## DHCPv6 Operation Steps

### Stateless Process
```
1. Host sends Router Solicitation (RS)
2. Router sends Router Advertisement (RA) - O=1, M=0
3. Host creates address using SLAAC
4. Host sends DHCPv6 INFORMATION-REQUEST
5. Server sends DHCPv6 REPLY (DNS, domain)
```

### Stateful Process
```
1. Host sends Router Solicitation (RS)
2. Router sends Router Advertisement (RA) - M=1
3. Host sends DHCPv6 SOLICIT
4. Server sends DHCPv6 ADVERTISE
5. Host sends DHCPv6 REQUEST
6. Server sends DHCPv6 REPLY (address, DNS, domain)
```

---

## Configuration Commands

### Enable Stateless DHCPv6
```cisco
! On router interface
ipv6 nd other-config-flag
ipv6 dhcp server [pool-name]
```

### Enable Stateful DHCPv6
```cisco
! On router interface
ipv6 nd managed-config-flag
ipv6 dhcp server [pool-name]
```

### Create DHCPv6 Pool (Stateless)
```cisco
ipv6 dhcp pool [name]
 dns-server [ipv6-address]
 domain-name [name]
```

### Create DHCPv6 Pool (Stateful)
```cisco
ipv6 dhcp pool [name]
 address prefix [ipv6-prefix/length]
 dns-server [ipv6-address]
 domain-name [name]
```

---

## Verification Commands

```cisco
! View DHCPv6 configuration
show ipv6 dhcp pool
show ipv6 dhcp binding

! View interface DHCPv6 settings
show ipv6 interface [interface]

! Verify ND flags
show ipv6 interface [interface] | include ND
```

---

## Key Differences Summary

### Stateless DHCPv6
- Host generates own address (SLAAC)
- Server provides only DNS/domain
- O Flag = 1, M Flag = 0
- No address tracking
- Use: Simple networks, less control needed

### Stateful DHCPv6
- Server assigns address
- Server provides DNS/domain
- M Flag = 1
- Full address tracking
- Use: Need control, address management, like DHCPv4

---

## CCNA Exam Tips

**Remember**:
- **M=1** = Stateful (Managed addresses)
- **O=1** = Stateless (Other config only)
- Default gateway always from RA (never from DHCPv6)
- Stateless = SLAAC + DHCPv6 for DNS
- Stateful = DHCPv6 for everything (like DHCPv4)

**Common Questions**:
- "M and O both 0?" → SLAAC only
- "O=1, M=0?" → Stateless DHCPv6
- "M=1?" → Stateful DHCPv6
- "Where does host get gateway?" → Always RA message

---

## Quick Command Reference

```cisco
! Stateless
interface g0/0
 ipv6 nd other-config-flag
 ipv6 dhcp server POOL-NAME

ipv6 dhcp pool POOL-NAME
 dns-server 2001:DB8::100
 domain-name example.com

! Stateful  
interface g0/0
 ipv6 nd managed-config-flag
 ipv6 dhcp server POOL-NAME

ipv6 dhcp pool POOL-NAME
 address prefix 2001:DB8:1::/64
 dns-server 2001:DB8::100
 domain-name example.com
```

---

*Understanding the M and O flags is key to DHCPv6 for CCNA!*