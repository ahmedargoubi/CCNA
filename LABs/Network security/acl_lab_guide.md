# Packet Tracer - Configure and Modify Standard IPv4 ACLs Lab Guide

## Overview
This lab teaches you how to configure, verify, and modify standard IPv4 Access Control Lists (ACLs) on Cisco routers. You'll implement network security policies by filtering traffic between different network segments.

## Network Topology Summary
- **R1**: Connects 192.168.10.0/24 and 192.168.20.0/24 networks
- **Edge**: ISP router (no ACL configuration)
- **R3**: Connects 192.168.30.0/24 and 192.168.40.0/24 networks
- **Server**: 209.165.200.254

---

## Part 1: Verify Connectivity

### Purpose
Test network connectivity BEFORE applying ACLs to establish a baseline.

### Tests to Perform

**From PC-A (192.168.10.3):**
```
ping 192.168.30.3  (PC-C)
ping 192.168.40.3  (PC-D)
```
**Expected Result:** ✅ Success

**From R1:**
```
ping 192.168.30.3
ping 192.168.40.3
```
**Expected Result:** ✅ Success

**From PC-C (192.168.30.3):**
```
ping 192.168.10.3  (PC-A)
ping 192.168.20.3  (PC-B)
```
**Expected Result:** ✅ Success

**From R3:**
```
ping 192.168.10.3
ping 192.168.20.3
```
**Expected Result:** ✅ Success

**From All PCs:**
```
ping 209.165.200.254  (Server)
```
**Expected Result:** ✅ Success

---

## Part 2: Configure and Verify Standard ACLs

### Step 1: Configure Numbered Standard ACL

#### Security Policy Requirements
- Allow 192.168.10.0/24 network to access 192.168.30.0/24
- Allow 192.168.20.0/24 network to access 192.168.30.0/24
- Deny all other traffic (explicit deny any)

#### Planning Questions & Answers

**Q: What wildcard mask for 192.168.10.0/24?**
- **A:** `0.0.0.255`

**Q: Which router for this ACL?**
- **A:** R3 (apply ACLs close to the destination for standard ACLs)

**Q: Which interface and direction?**
- **A:** G0/0/0, outbound direction

#### Configuration Steps

**On R3:**
```cisco
R3(config)# access-list 1 remark Allow R1 LANs Access
R3(config)# access-list 1 permit 192.168.10.0 0.0.0.255
R3(config)# access-list 1 permit 192.168.20.0 0.0.0.255
R3(config)# access-list 1 deny any
R3(config)# interface g0/0/0
R3(config-if)# ip access-group 1 out
R3(config-if)# end
```

#### Verification Commands

**View ACL configuration:**
```cisco
R3# show access-lists 1
```

**Expected Output:**
```
Standard IP access list 1
    permit 192.168.10.0, wildcard bits 0.0.0.255
    permit 192.168.20.0, wildcard bits 0.0.0.255
    deny any
```

**Check ACL placement:**
```cisco
R3# show ip interface g0/0/0
```

**Look for:**
```
Outgoing access list is 1
Inbound access list is not set
```

#### Testing the ACL

**Test 1: PC-A to PC-C**
```
ping 192.168.30.3
```
**Expected:** ✅ Success (192.168.10.0/24 is permitted)

**Test 2: PC-B to PC-C**
```
ping 192.168.30.3
```
**Expected:** ✅ Success (192.168.20.0/24 is permitted)

**Test 3: PC-D to PC-C**
```
ping 192.168.30.3
```
**Expected:** ❌ Failure (192.168.40.0/24 is denied)

**Test 4: R1 to PC-C**
```
R1# ping 192.168.30.3
```
**Expected:** ❌ Failure (R1's source IP 10.1.1.1 is denied)

---

### Step 2: Configure Named Standard ACL

#### Security Policy Requirements
- Allow all hosts from 192.168.40.0/24 to access 192.168.10.0/24
- Allow ONLY PC-C (192.168.30.3) to access 192.168.10.0/24
- ACL name: `BRANCH-OFFICE-POLICY`

#### Planning Questions & Answers

**Q: Which router for this ACL?**
- **A:** R1 (close to destination)

**Q: Which interface and direction?**
- **A:** G0/0/0, outbound direction

#### Configuration Steps

**On R1:**
```cisco
R1(config)# ip access-list standard BRANCH-OFFICE-POLICY
R1(config-std-nacl)# permit host 192.168.30.3
R1(config-std-nacl)# permit 192.168.40.0 0.0.0.255
R1(config-std-nacl)# end
```

**Alternative way to write first line:**
```cisco
permit 192.168.30.3 0.0.0.0
```

**Apply to interface:**
```cisco
R1(config)# interface g0/0/0
R1(config-if)# ip access-group BRANCH-OFFICE-POLICY out
```

#### Verification

**View ACL:**
```cisco
R1# show access-lists
```

**Expected Output:**
```
Standard IP access list BRANCH-OFFICE-POLICY
    10 permit host 192.168.30.3
    20 permit 192.168.40.0 0.0.0.255
```

**Key Difference from R3's ACL:**
- Named ACL automatically assigns sequence numbers (10, 20)
- No explicit `deny any` statement (implicit deny still exists)
- Uses name instead of number

**Check interface:**
```cisco
R1# show ip interface g0/0/0
```

**Look for:**
```
Outgoing access list is BRANCH-OFFICE-POLICY
```

#### Testing the ACL

**Test 1: PC-C to PC-A**
```
ping 192.168.10.3
```
**Expected:** ✅ Success (PC-C host is permitted)

**Test 2: Extended ping from R3 (simulating traffic from 192.168.30.1)**
```cisco
R3# ping
Protocol [ip]: 
Target IP address: 192.168.10.3
Repeat count [5]: 
Datagram size [100]: 
Timeout in seconds [2]: 
Extended commands [n]: y
Source address or interface: 192.168.30.1
[Accept remaining defaults]
```
**Expected:** ❌ Failure (only 192.168.30.3 is permitted, not 192.168.30.1)

**Test 3: PC-D to PC-A**
```
ping 192.168.10.3
```
**Expected:** ✅ Success (192.168.40.0/24 is permitted)

---

## Part 3: Modify a Standard ACL

### Problem Scenario
PC-A cannot ping the server (209.165.200.254) because return traffic from 209.165.200.224/27 network is blocked by the ACL on R1.

### New Security Policy
- Allow traffic from 209.165.200.224/27 network to access 192.168.10.0/24
- Add explicit `deny any` statement for consistency

### Modification Options

**Option 1:** Delete and recreate entire ACL
```cisco
R1(config)# no ip access-list standard BRANCH-OFFICE-POLICY
[Then recreate from scratch]
```
**Drawback:** Risk of errors, temporarily removes filtering

**Option 2:** Modify ACL in place (RECOMMENDED)
```cisco
R1(config)# ip access-list standard BRANCH-OFFICE-POLICY
R1(config-std-nacl)# [sequence] [statement]
```
**Advantage:** Safer, more efficient, no service interruption

### Configuration Steps

**Check current ACL:**
```cisco
R1# show access-lists
```

**Add new entries:**
```cisco
R1(config)# ip access-list standard BRANCH-OFFICE-POLICY
R1(config-std-nacl)# 30 permit 209.165.200.224 0.0.0.31
R1(config-std-nacl)# 40 deny any
R1(config-std-nacl)# end
```

**Wildcard calculation for /27:**
- Subnet mask: 255.255.255.224
- Wildcard: 0.0.0.31 (255-224=31)

### Verification

**View modified ACL:**
```cisco
R1# show access-lists
```

**Expected Output:**
```
Standard IP access list BRANCH-OFFICE-POLICY
    10 permit 192.168.30.3 (8 matches)
    20 permit 192.168.40.0, wildcard bits 0.0.0.255 (5 matches)
    30 permit 209.165.200.224, wildcard bits 0.0.0.31
    40 deny any
```

**Q: Do you need to reapply the ACL to the interface?**
- **A:** No! The ACL is already applied. Modifications take effect immediately.

### Testing

**From PC-A:**
```
ping 209.165.200.254
```
**Expected:** ✅ Success (return traffic from 209.165.200.224/27 is now permitted)

---

## Key Concepts Summary

### Standard ACL Best Practices
1. **Apply close to destination** - Minimizes unnecessary filtering
2. **Use meaningful names** - Easier to understand and manage
3. **Document with remarks** - Use `remark` command for numbered ACLs
4. **Explicit deny any** - Makes policy clear, helps with troubleshooting

### ACL Processing Rules
- Processed top to bottom
- First match wins (stops processing)
- Implicit deny any at the end
- Applied per interface, per direction, per protocol

### Wildcard Mask Quick Reference
| Subnet | Wildcard Mask |
|--------|---------------|
| /24 (255.255.255.0) | 0.0.0.255 |
| /25 (255.255.255.128) | 0.0.0.127 |
| /26 (255.255.255.192) | 0.0.0.63 |
| /27 (255.255.255.224) | 0.0.0.31 |
| /32 (host) | 0.0.0.0 |

### Useful Commands Reference

| Command | Purpose |
|---------|---------|
| `show access-lists` | View all ACLs |
| `show access-lists [number/name]` | View specific ACL |
| `show ip interface [interface]` | Check ACL application |
| `show running-config` | View complete configuration |
| `ip access-list standard [name]` | Enter named ACL config mode |

---

## Reflection Questions & Answers

**1. Why would you ever need extended ACLs?**

Standard ACLs only filter based on source IP address. Extended ACLs are needed when you want to filter based on:
- Destination IP address
- Protocol type (TCP, UDP, ICMP, etc.)
- Port numbers (HTTP port 80, HTTPS port 443, etc.)
- More granular control over traffic

**Example:** Allow web traffic but block FTP from a specific network.

**2. Why choose named ACLs over numbered?**

Advantages of named ACLs:
- **Descriptive names** - "BRANCH-OFFICE-POLICY" vs "1"
- **Easier to modify** - Can insert/delete specific lines
- **Better documentation** - Self-documenting configuration
- **Sequence numbers** - Automatically assigned, can be customized
- **Scalability** - Easier to manage in large networks

The extra typing is worth it for maintainability and clarity!

---

## Troubleshooting Tips

### Ping Failures
1. Check ACL with `show access-lists`
2. Verify application with `show ip interface`
3. Check match counters in ACL output
4. Verify routing with `show ip route`
5. Remember: standard ACLs filter on SOURCE IP only

### Common Mistakes
- Applying ACL in wrong direction
- Wrong wildcard mask calculation
- Forgetting implicit deny any
- Applying standard ACL too close to source
- Not testing before applying ACLs

### ACL Not Working?
```cisco
! Remove ACL from interface
R1(config-if)# no ip access-group [name/number] [in/out]

! Delete ACL
R1(config)# no access-list [number]
R1(config)# no ip access-list standard [name]
```

---

## Lab Completion Checklist

- ✅ Verified initial connectivity (Part 1)
- ✅ Configured numbered ACL on R3 (ACL 1)
- ✅ Applied ACL 1 to R3 G0/0/0 outbound
- ✅ Verified ACL 1 functionality
- ✅ Configured named ACL on R1 (BRANCH-OFFICE-POLICY)
- ✅ Applied named ACL to R1 G0/0/0 outbound
- ✅ Verified named ACL functionality
- ✅ Modified BRANCH-OFFICE-POLICY to allow server traffic
- ✅ Tested all connectivity scenarios
- ✅ Answered reflection questions

---

**Good luck with your lab! Remember to save your configurations before closing Packet Tracer!**

```cisco
R1# copy running-config startup-config
R3# copy running-config startup-config
```