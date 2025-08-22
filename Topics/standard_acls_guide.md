# Standard Access Control Lists (ACLs) - CCNA Guide

## What are ACLs?

**ACLs (Access Control Lists)** are rules applied on network devices (usually routers or switches) that act as **packet filters**.  
They allow or deny traffic based on specific conditions.

- ACLs can filter traffic based on:
  - **Source / Destination IP addresses**
  - **Source / Destination Layer 4 ports**
  - Other attributes depending on the ACL type.

In this section, we focus on **Standard ACLs** from the CCNA exam perspective.

---

## How ACLs Work

- ACLs are configured **globally** on the router in global configuration mode.
- They are an **ordered sequence of ACEs (Access Control Entries)**.
- ACLs must be **applied to an interface** to take effect:
  - **Inbound** → traffic entering the interface.
  - **Outbound** → traffic leaving the interface.

### Packet Processing

- The router processes ACL entries **top to bottom**.
- When a packet matches an ACE:
  - The action (**permit** or **deny**) is applied.
  - Processing stops (no further entries are checked).
- If no ACE matches:
  - The **implicit deny** at the end of the ACL denies all traffic.

⚠️ **Important**: Always plan your ACE order carefully.

---

## Implicit Deny

- Every ACL ends with an **implicit deny all** (invisible command).
- This means that any traffic not explicitly permitted is automatically denied.

---

## ACL Types

- **Standard ACLs**: Filter based only on **source IP address**.
- **Extended ACLs**: Filter based on source/destination IPs, protocols, ports, etc.

---

## Standard Numbered ACLs

- Match traffic based **only on source IP address**.
- Identified by a **number**:
  - Range for Standard ACLs: **1–99** and **1300–1999**.

### Syntax

```bash
R1(config)# access-list <number> {deny | permit} <ip> <wildcard-mask>
```

### Examples

**Deny a specific host:**
```bash
R1(config)# access-list 1 deny 1.1.1.1 0.0.0.0
R1(config)# access-list 1 deny host 1.1.1.1
```

**Permit any traffic:**
```bash
R1(config)# access-list 1 permit any
R1(config)# access-list 1 permit 0.0.0.0 255.255.255.255
```

**Add a description/remark:**
```bash
R1(config)# access-list 1 remark ## BLOCK BOB FROM ACCOUNTING ##
```

### Applying ACL to an Interface

```bash
R1(config-if)# ip access-group <number> {in | out}
```

### Placement Rule

**Standard ACLs should be applied as close to the destination as possible.**

---

## Standard Named ACLs

- Use a name instead of a number.
- Provide better readability and easier management.

### Syntax

```bash
R1(config)# ip access-list standard <acl-name>
R1(config-std-nacl)# [sequence-number] {deny | permit} <ip> <wildcard-mask>
```

### Example

```bash
R1(config)# ip access-list standard BLOCK_BOB
R1(config-std-nacl)# 10 deny host 192.168.1.10
R1(config-std-nacl)# 20 permit any
```

### Applying Named ACL

```bash
R1(config-if)# ip access-group BLOCK_BOB {in | out}
```

---

## Wildcard Masks Recap

- Wildcard masks are the inverse of subnet masks.
  - `0.0.0.0` → match exactly one host.
  - `0.0.0.255` → match a /24 network.

### Examples

- `192.168.1.0 0.0.0.255` → match all hosts in 192.168.1.0/24.
- `192.168.1.1 0.0.0.0` → match only host 192.168.1.1.

---

## Verification Commands

**Show ACLs configured:**
```bash
R1# show access-lists
```

**Show ACLs applied to interfaces:**
```bash
R1# show running-config
```

---

## Key Takeaways

- Standard ACLs match source IP only.
- Use numbers 1–99 / 1300–1999 or a name.
- Always remember the implicit deny all at the end.
- Apply Standard ACLs closest to the destination.
- Use named ACLs for readability in production networks.