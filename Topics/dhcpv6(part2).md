# DHCPv6 Configuration Guide - CCNA Essentials

## Table of Contents
1. [DHCPv6 Router Roles](#1-dhcpv6-router-roles)
2. [Stateless DHCPv6 Server](#2-stateless-dhcpv6-server)
3. [Stateless DHCPv6 Client](#3-stateless-dhcpv6-client)
4. [Stateful DHCPv6 Server](#4-stateful-dhcpv6-server)
5. [Stateful DHCPv6 Client](#5-stateful-dhcpv6-client)
6. [Verification Commands](#6-verification-commands)
7. [DHCPv6 Relay Agent](#7-dhcpv6-relay-agent)
8. [Verify Relay Agent](#8-verify-relay-agent)

---

## 1. DHCPv6 Router Roles

### Key Facts to Memorize
- **Ports:** UDP 546 (client), UDP 547 (server)
- **Multicast:** FF02::1:2 (all DHCPv6 servers/relays)
- **Protocol:** Similar to DHCPv4 but for IPv6

### Three Roles
1. **DHCPv6 Server** - Provides addresses and/or configuration
2. **DHCPv6 Client** - Requests configuration from server
3. **DHCPv6 Relay Agent** - Forwards messages between clients and servers

### Two Operation Modes

**Stateless DHCPv6:**
- Address from **SLAAC** (router advertisement)
- DNS/domain from **DHCPv6**
- Uses **O-flag = 1**

**Stateful DHCPv6:**
- Address from **DHCPv6**
- DNS/domain from **DHCPv6**
- Uses **M-flag = 1**

### RA Flags

| A | O | M | Result |
|---|---|---|--------|
| 1 | 0 | 0 | SLAAC only |
| 1 | 1 | 0 | **Stateless DHCPv6** |
| 0 | 0 | 1 | **Stateful DHCPv6** |

---

## 2. Stateless DHCPv6 Server

**Use:** Clients use SLAAC for address, DHCPv6 for DNS/domain

### Configuration

```cisco
! Enable IPv6 routing
R1(config)# ipv6 unicast-routing

! Create pool (NO address prefix)
R1(config)# ipv6 dhcp pool STATELESS-POOL
R1(config-dhcpv6)# dns-server 2001:4860:4860::8888
R1(config-dhcpv6)# domain-name example.com
R1(config-dhcpv6)# exit

! Configure interface
R1(config)# interface g0/0/1
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 dhcp server STATELESS-POOL
R1(config-if)# ipv6 nd other-config-flag
R1(config-if)# no shutdown
```

**Key:** `ipv6 nd other-config-flag` sets O-flag

---

## 3. Stateless DHCPv6 Client

### Router as Client

```cisco
R2(config)# interface g0/0/1
R2(config-if)# ipv6 address autoconfig
R2(config-if)# no shutdown
```

**Result:** Gets address from SLAAC + DNS from DHCPv6

---

## 4. Stateful DHCPv6 Server

**Use:** DHCPv6 provides everything (address + configuration)

### Configuration

```cisco
! Enable IPv6 routing
R1(config)# ipv6 unicast-routing

! Create pool (WITH address prefix)
R1(config)# ipv6 dhcp pool STATEFUL-POOL
R1(config-dhcpv6)# address prefix 2001:db8:acad:1::/64
R1(config-dhcpv6)# dns-server 2001:4860:4860::8888
R1(config-dhcpv6)# domain-name example.com
R1(config-dhcpv6)# exit

! Configure interface
R1(config)# interface g0/0/1
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 dhcp server STATEFUL-POOL
R1(config-if)# ipv6 nd managed-config-flag
R1(config-if)# ipv6 nd prefix default no-autoconfig
R1(config-if)# no shutdown
```

**Key differences:**
- `address prefix` - Provides address pool
- `ipv6 nd managed-config-flag` - Sets M-flag
- `ipv6 nd prefix default no-autoconfig` - Disables SLAAC

---

## 5. Stateful DHCPv6 Client

### Router as Client

```cisco
R2(config)# interface g0/0/1
R2(config-if)# ipv6 address dhcp
R2(config-if)# no shutdown
```

**Result:** Gets everything from DHCPv6

---

## 6. Verification Commands

### Essential Commands

```cisco
! Show pool
R1# show ipv6 dhcp pool

! Show bindings (leases)
R1# show ipv6 dhcp binding

! Show interface DHCPv6 config
R1# show ipv6 dhcp interface

! Show statistics
R1# show ipv6 dhcp statistics

! Debug (use carefully)
R1# debug ipv6 dhcp detail
R1# undebug all
```

### Sample Output

**show ipv6 dhcp pool:**
```
DHCPv6 pool: STATEFUL-POOL
  Address allocation prefix: 2001:db8:acad:1::/64
  DNS server: 2001:4860:4860::8888
  Active clients: 3
```

**show ipv6 dhcp binding:**
```
Client: FE80::C801:16FF:FEE8:8
  Address: 2001:DB8:ACAD:1:5555::1
  preferred lifetime 86400, valid lifetime 172800
```

---

## 7. DHCPv6 Relay Agent

**Use:** Server on different network than clients

### Network Example
```
[Clients] --- [R2 Relay] --- [R1 Server]
2001:db8:1::/64              2001:db8:2::/64
```

### Server Configuration (R1)

```cisco
R1(config)# ipv6 unicast-routing
R1(config)# interface g0/0/0
R1(config-if)# ipv6 address 2001:db8:2::1/64
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# ipv6 dhcp pool REMOTE-POOL
R1(config-dhcpv6)# address prefix 2001:db8:1::/64
R1(config-dhcpv6)# dns-server 2001:4860:4860::8888
R1(config-dhcpv6)# domain-name example.com
```

### Relay Configuration (R2)

```cisco
! Client-facing interface
R2(config)# interface g0/0/1
R2(config-if)# ipv6 address 2001:db8:1::1/64
R2(config-if)# ipv6 dhcp relay destination 2001:db8:2::1
R2(config-if)# ipv6 nd managed-config-flag
R2(config-if)# no shutdown

! Server-facing interface
R2(config)# interface g0/0/0
R2(config-if)# ipv6 address 2001:db8:2::2/64
R2(config-if)# no shutdown
```

**Key command:** `ipv6 dhcp relay destination [server-ipv6]`

---

## 8. Verify Relay Agent

### Relay Verification

```cisco
! Show relay configuration
R2# show ipv6 dhcp interface g0/0/1
```

**Expected:**
```
GigabitEthernet0/0/1 is in relay mode
  Relay destinations:
    2001:DB8:2::1 via GigabitEthernet0/0/0
```

### Check Statistics

```cisco
R2# show ipv6 dhcp statistics
```

### Server Verification

```cisco
! Check bindings on server (not relay)
R1# show ipv6 dhcp binding
R1# show ipv6 dhcp pool
```

### Troubleshooting Steps

1. Verify relay destination configured
2. Check routing to server: `ping 2001:db8:2::1`
3. Verify pool on server has correct prefix
4. Use debug: `debug ipv6 dhcp detail`

---

## Quick Reference

### Stateless vs Stateful

| | Stateless | Stateful |
|---|-----------|----------|
| **Address** | SLAAC | DHCPv6 |
| **DNS** | DHCPv6 | DHCPv6 |
| **Pool has address prefix** | NO | YES |
| **RA flag** | O-flag | M-flag |
| **Disable SLAAC** | NO | YES |

### Essential Commands

```cisco
# Enable IPv6 routing (ALWAYS FIRST)
ipv6 unicast-routing

# Create pool
ipv6 dhcp pool NAME
 dns-server 2001:4860:4860::8888
 domain-name example.com
 address prefix 2001:db8::/64    (stateful only)

# Bind to interface
interface g0/0/1
 ipv6 dhcp server POOL-NAME
 ipv6 nd other-config-flag       (stateless)
 ipv6 nd managed-config-flag     (stateful)
 ipv6 nd prefix default no-autoconfig  (stateful)

# Relay
ipv6 dhcp relay destination [server-ipv6]

# Client
ipv6 address autoconfig  (stateless)
ipv6 address dhcp        (stateful)

# Verify
show ipv6 dhcp pool
show ipv6 dhcp binding
show ipv6 dhcp interface
```

---

## CCNA Exam Tips

### Must Memorize
- **Ports:** 546 (client), 547 (server)
- **Multicast:** FF02::1:2
- **Stateless = SLAAC + DHCPv6 (O-flag)**
- **Stateful = DHCPv6 only (M-flag)**
- **ALWAYS enable:** `ipv6 unicast-routing`

### Configuration Differences

**Stateless:**
- NO `address prefix` in pool
- Use `ipv6 nd other-config-flag`
- Don't disable SLAAC

**Stateful:**
- HAS `address prefix` in pool
- Use `ipv6 nd managed-config-flag`
- MUST disable SLAAC: `ipv6 nd prefix default no-autoconfig`

### Common Mistakes
- ❌ Forgetting `ipv6 unicast-routing`
- ❌ Using O-flag for stateful (should be M-flag)
- ❌ Not disabling SLAAC in stateful mode
- ❌ Forgetting `address prefix` in stateful pool
- ❌ Wrong relay destination address

### Quick Decision Tree

**Need centralized address management?**
- YES → **Stateful DHCPv6**
- NO → **Stateless DHCPv6**

**Server on different network?**
- YES → **Use Relay Agent**
- NO → **Direct DHCPv6**
