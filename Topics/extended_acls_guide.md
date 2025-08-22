# Extended Access Control Lists (ACLs) - CCNA Guide

## Introduction

**Extended ACLs** are a type of Access Control List that allow network administrators to filter traffic with much greater precision than Standard ACLs.  
They can filter traffic based on:

- **Protocol** (IP, TCP, UDP, ICMP, etc.)
- **Source IP address**
- **Destination IP address**
- **Source / Destination port numbers** (for TCP/UDP)

This makes Extended ACLs more powerful than Standard ACLs, which only filter based on **source IP address**.

---

## Numbered vs Named ACLs

Like Standard ACLs, Extended ACLs can be **numbered** or **named**.

- **Numbered Extended ACLs**:
  - Ranges: **100–199** and **2000–2699**
  - Example: `access-list 101 deny tcp any any eq 80`

- **Named Extended ACLs**:
  - Configured using a name instead of a number.
  - Easier to edit and manage.

---

## Syntax

### Extended Numbered ACL
```bash
R1(config)# access-list <number> {permit | deny} <protocol> <src-ip> <dest-ip> [options]
```

### Extended Named ACL
```bash
R1(config)# ip access-list extended <name>
R1(config-ext-nacl)# <seq-num> {permit | deny} <protocol> <src-ip> <dest-ip> [options]
```

---

## Matching Protocols

Extended ACLs can filter traffic based on Layer 4 protocols.

Examples of common protocols:
- **ICMP** = 1
- **TCP** = 6  
- **UDP** = 17
- **EIGRP** = 88
- **OSPF** = 89

### Example:
```bash
R1(config-ext-nacl)# deny tcp any 10.0.0.0 0.0.0.255
```
➡️ Deny all TCP traffic from any source to 10.0.0.0/24.

---

## Matching Source and Destination IP

- `any` = match any address
- `host <ip>` = match a single host
- `<network> <wildcard>` = match a range of IPs

### Examples:
```bash
permit ip any any          # Allow all traffic
deny udp 10.0.0.0 0.0.255.255 host 192.168.1.1
deny icmp host 172.16.1.1 192.168.0.0 0.0.0.255
```

---

## Matching TCP/UDP Port Numbers

You can specify source and/or destination ports when filtering TCP/UDP traffic.

### Operators:
- `eq` → equal
- `gt` → greater than
- `lt` → less than
- `neq` → not equal
- `range` → range of ports

### Examples:
```bash
permit tcp 10.0.0.0 0.0.255.255 host 2.2.2.2 eq 443
deny udp any range 20000 30000 host 3.3.3.3
permit tcp 172.16.1.0 0.0.0.255 gt 9999 host 4.4.4.4 neq 23
```

---

## Placement of Extended ACLs

**Extended ACLs should be placed as close to the source as possible.**

This avoids unnecessary traffic traveling across the network before being filtered.

---

## Example Network Requirements

- Hosts in 192.168.1.0/24 can't use HTTPS to access SRV1 (10.0.1.100).
- Hosts in 192.168.2.0/24 can't access 10.0.2.0/24.
- None of the hosts in 192.168.1.0/24 or 192.168.2.0/24 can ping 10.0.1.0/24 or 10.0.2.0/24.

---

## Configuration Examples

### Extended ACL #1 – Block HTTPS to SRV1
Applied on R1 G0/1 INBOUND

```bash
R1(config)# ip access-list extended HTTP_SRV1
R1(config-ext-nacl)# deny tcp 192.168.1.0 0.0.0.255 host 10.0.1.100 eq 443
R1(config-ext-nacl)# permit ip any any
R1(config)# int g0/1
R1(config-if)# ip access-group HTTP_SRV1 in
```

### Extended ACL #2 – Block 192.168.2.0/24 to 10.0.2.0/24
Applied on R1 G0/2 INBOUND

```bash
R1(config)# ip access-list extended BLOCK_10.0.2.0
R1(config-ext-nacl)# deny ip 192.168.2.0 0.0.0.255 10.0.2.0 0.0.0.255
R1(config-ext-nacl)# permit ip any any
R1(config)# int g0/2
R1(config-if)# ip access-group BLOCK_10.0.2.0 in
```

### Extended ACL #3 – Block ICMP (Ping)
Applied on R1 G0/0 OUTBOUND

```bash
R1(config)# ip access-list extended BLOCK_ICMP
R1(config-ext-nacl)# deny icmp 192.168.1.0 0.0.0.255 10.0.1.0 0.0.0.255
R1(config-ext-nacl)# deny icmp 192.168.1.0 0.0.0.255 10.0.2.0 0.0.0.255
R1(config-ext-nacl)# deny icmp 192.168.2.0 0.0.0.255 10.0.1.0 0.0.0.255
R1(config-ext-nacl)# permit ip any any
R1(config)# int g0/0
R1(config-if)# ip access-group BLOCK_ICMP out
```

---

## Editing and Resequencing ACLs

- Named ACLs allow inserting or deleting individual entries.
- Numbered ACLs require removal and reconfiguration.

### Resequence command:
```bash
R1(config)# ip access-list resequence <acl-id> <start-seq> <increment>
```

---

## Verification Commands

**Show all ACLs:**
```bash
R1# show access-lists
```

**Show ACLs applied on interfaces:**
```bash
R1# show ip interface
```

**Show running config (to confirm ACL placement):**
```bash
R1# show running-config
```

---

## Key Takeaways

- Extended ACLs filter based on protocol, IP addresses, and ports.
- They can be numbered (100–199, 2000–2699) or named.
- Always processed top to bottom.
- Include an implicit deny all at the end.
- Apply Extended ACLs close to the source.
- Use named ACLs for easier management.