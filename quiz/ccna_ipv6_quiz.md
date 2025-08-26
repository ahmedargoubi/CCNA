
# CCNA IPv6 Quiz (15 Questions)

This quiz is designed in **CCNA exam style** — scenario-based, not just definitions.  

---

### Q1. Address Compression
Which of the following is the correct compressed form of the IPv6 address below?  

```
2001:0db8:0000:0000:0000:0000:0000:0001
```

A. `2001:db8::1`  
B. `2001:0db8:0:0::1`  
C. `2001:db8:0000::0001`  
D. `2001::db8:1`  

---

### Q2. IPv6 Address Expansion
Expand the following IPv6 address into full form:  

```
2001:db8::abcd
```

---

### Q3. Address Type
Which type of IPv6 address is automatically assigned to an interface when IPv6 is enabled?  

A. Global Unicast  
B. Unique Local  
C. Link-Local  
D. Multicast  

---

### Q4. IPv6 Address Ranges
Which IPv6 address belongs to the **Unique Local** address space?  

A. `fd12:3456:789a::1`  
B. `fe80::1`  
C. `2001:db8::1`  
D. `ff02::1`  

---

### Q5. Routing – Next-Hop
You configured the following static route on R1:  

```
R1(config)# ipv6 route 2001:db8:0:3::/64 g0/0
```

Why will this not work on an Ethernet interface?  

---

### Q6. Multicast Knowledge
Which IPv6 multicast address is used by **all routers** on a link?  

A. `ff02::1`  
B. `ff02::2`  
C. `ff02::5`  
D. `ff02::9`  

---

### Q7. Anycast
What is true about an IPv6 **Anycast address**?  

A. It is assigned to exactly one device.  
B. It delivers packets to all devices in the group.  
C. It delivers packets to the nearest device configured with the address.  
D. It is the IPv6 replacement for broadcast.  

---

### Q8. IPv6 Special Addresses
Match the following addresses with their IPv4 equivalents:  

- `::`  
- `::1`  
- `::/0`  

Options:  
1. Default route (IPv4 `0.0.0.0/0`)  
2. Loopback (IPv4 `127.0.0.1`)  
3. Unspecified (IPv4 `0.0.0.0`)  

---

### Q9. IPv6 Header
Which of the following is **NOT** a field in the IPv6 header?  

A. Flow Label  
B. Hop Limit  
C. Header Checksum  
D. Next Header  

---

### Q10. EUI-64 Conversion
Given the MAC address `74-A2-BC-10-22-5F`, what will the **EUI-64 interface ID** be?  
(Show your steps: split, insert FFFE, flip 7th bit).  

---

### Q11. NDP Messages
Which ICMPv6 message is used by a host to **find the MAC address of another host** on the same link?  

A. Router Solicitation (133)  
B. Router Advertisement (134)  
C. Neighbor Solicitation (135)  
D. Neighbor Advertisement (136)  

---

### Q12. SLAAC
What does a host need to receive from a router in order to configure its IPv6 address via **SLAAC**?  

A. An NS message  
B. An RA message with prefix information  
C. A default route  
D. A multicast address  

---

### Q13. Duplicate Address Detection (DAD)
What happens if a host sends a Neighbor Solicitation (NS) to check its own IPv6 address and receives a Neighbor Advertisement (NA) in reply?  

---

### Q14. Static Routing
Write the command to configure a **default IPv6 static route** on router R1 using next-hop `2001:db8:0:12::2`.  

---

### Q15. Troubleshooting
You configured the following on an interface:  

```
R1(config)# int g0/0
R1(config-if)# ipv6 address 2001:db8:1::1/64
R1(config-if)# no shutdown
```

But IPv6 routing between subnets is not working. What single global command did you forget to enable?  

---

# ✅ End of IPv6 Quiz
