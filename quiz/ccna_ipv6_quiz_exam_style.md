# CCNA IPv6 Quiz (Exam Style)

---

### Q1. Address Compression
Which of the following is the correct compressed form of the IPv6 address?

```
2001:0db8:0000:0000:0000:0000:0000:0001
```

A. `2001:db8::1`  
B. `2001:0db8:0:0::1`  
C. `2001:db8:0000::0001`  
D. `2001::db8:1`  

---

### Q2. Address Expansion
Expand the IPv6 address into full form:

```
2001:db8::abcd
```

---

### Q3. Address Type
Which type of IPv6 address is **always automatically assigned** to an interface when IPv6 is enabled?  

A. Global Unicast  
B. Link-Local  
C. Unique Local  
D. Multicast  

---

### Q4. Identify Address Range
Which IPv6 address belongs to the **Unique Local** address space?  

A. `fd12:3456:789a::1`  
B. `fe80::1`  
C. `2001:db8::1`  
D. `ff02::1`  

---

### Q5. Static Route
You configure the following static route on R1:  

```
R1(config)# ipv6 route 2001:db8:0:3::/64 g0/0
```

Why will this not work on an Ethernet interface?  

---

### Q6. IPv6 Multicast
Which IPv6 multicast address is used by **all routers** on a link?  

A. `ff02::1`  
B. `ff02::2`  
C. `ff02::5`  
D. `ff02::9`  

---

### Q7. Anycast Concept
What is true about an IPv6 Anycast address?  

A. Assigned to one device only  
B. Packets delivered to all devices in group  
C. Packets delivered to the **nearest device** with that address  
D. Replaces broadcast in IPv6  

---

### Q8. Special Addresses
Match the following IPv6 addresses with their IPv4 equivalents:  

- `::`  
- `::1`  
- `::/0`  

Options:  
1. Loopback (IPv4 `127.0.0.1`)  
2. Default route (IPv4 `0.0.0.0/0`)  
3. Unspecified (IPv4 `0.0.0.0`)  

---

### Q9. IPv6 Header
Which field was **removed** from the IPv6 header (present in IPv4 but not in IPv6)?  

A. Flow Label  
B. Hop Limit  
C. Next Header  
D. Header Checksum  

---

### Q10. EUI-64 Conversion
Convert the MAC address `74-A2-BC-10-22-5F` into an EUI-64 IPv6 Interface ID.  
(Show the 3 steps: split, insert FFFE, flip 7th bit).  

---

### Q11. Neighbor Discovery
Which ICMPv6 message is used by a host to request the MAC address of another host on the same link?  

A. Router Solicitation (133)  
B. Router Advertisement (134)  
C. Neighbor Solicitation (135)  
D. Neighbor Advertisement (136)  

---

### Q12. SLAAC
What information must a router send in an **RA message** for a host to configure its IPv6 address via SLAAC?  

---

### Q13. Duplicate Address Detection (DAD)
If a host sends an NS to check its own IPv6 address and receives an NA reply, what happens?  

---

### Q14. Default Route Config
Write the command to configure a default IPv6 static route on router R1 with next-hop:  

```
2001:db8:0:12::2
```

---

### Q15. Troubleshooting
You configured an IPv6 address on R1’s G0/0 interface:  

```
R1(config)# int g0/0
R1(config-if)# ipv6 address 2001:db8:1::1/64
R1(config-if)# no shutdown
```

Ping to another IPv6 subnet fails. What **global command** is missing?  

---

# ✅ End of Quiz
