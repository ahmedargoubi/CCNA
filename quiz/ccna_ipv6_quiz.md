# CCNA IPv6 Quiz - 15 Questions

## Instructions
- Choose the best answer for each question
- Each question has only one correct answer
- Questions reflect real CCNA exam style and difficulty

---

### Question 1
A network engineer needs to configure an IPv6 address on interface GigabitEthernet0/0. The address should be 2001:db8:1234:5678::1 with a /64 prefix length. Which command correctly configures this address?

A) `ipv6 address 2001:db8:1234:5678::1/64`  
B) `ipv6 address 2001:db8:1234:5678::1 255.255.255.0`  
C) `ip address ipv6 2001:db8:1234:5678::1/64`  
D) `ipv6 unicast-address 2001:db8:1234:5678::1/64`

---

### Question 2
What is the correct expanded form of the IPv6 address 2001:db8::1234:5678?

A) 2001:0db8:0000:0000:0000:0000:1234:5678  
B) 2001:db80:0000:0000:0000:0000:1234:5678  
C) 2001:0db8:0000:0001:0000:0000:1234:5678  
D) 2001:db8:0000:0000:0000:1234:5678:0000

---

### Question 3
A router has the MAC address 00:1A:2B:3C:4D:5E. When using EUI-64 to generate the interface identifier, what will be the complete interface identifier portion?

A) 001A:2BFF:FE3C:4D5E  
B) 021A:2BFF:FE3C:4D5E  
C) 001A:2B3C:4D5E:FFFE  
D) 021A:2B3C:4D5E:FFFE

---

### Question 4
Which IPv6 address type is automatically generated on all IPv6-enabled interfaces and is used for communication within the same subnet?

A) Global unicast address  
B) Unique local address  
C) Link-local address  
D) Multicast address

---

### Question 5
An administrator wants to configure a static route to reach the network 2001:db8:2::/64 through the next-hop address fe80::1 via interface GigabitEthernet0/1. Which command is correct?

A) `ipv6 route 2001:db8:2::/64 fe80::1`  
B) `ipv6 route 2001:db8:2::/64 gigabitethernet0/1 fe80::1`  
C) `ipv6 route 2001:db8:2::/64 gigabitethernet0/1`  
D) `ipv6 static-route 2001:db8:2::/64 fe80::1 gigabitethernet0/1`

---

### Question 6
What is the solicited-node multicast address for the IPv6 unicast address 2001:db8:1:1::abcd:ef12?

A) FF02::1:FFcd:ef12  
B) FF02::1:FFab:cdef  
C) FF02::1:FF12:abcd  
D) FF02::2:FFcd:ef12

---

### Question 7
A network uses the prefix 2001:db8:1200::/48. If the organization wants to create subnets using /64 prefix length, how many subnet bits are available for subnetting?

A) 12 bits  
B) 16 bits  
C) 20 bits  
D) 64 bits

---

### Question 8
Which NDP message type does a host send when it wants to learn the link-layer address of another IPv6 node on the same subnet?

A) Router Solicitation (RS)  
B) Router Advertisement (RA)  
C) Neighbor Solicitation (NS)  
D) Neighbor Advertisement (NA)

---

### Question 9
A router's IPv6 routing table shows the following entry:
```
S   2001:db8:2::/64 [1/0]
    via fe80::2, GigabitEthernet0/0
```
What does the "S" indicate?

A) Statically configured route  
B) Summary route  
C) Solicited route  
D) SLAAC-learned route

---

### Question 10
Which IPv6 multicast address is used to send packets to all routers on the local link?

A) FF02::1  
B) FF02::2  
C) FF02::5  
D) FF02::A

---

### Question 11
A company wants to use private IPv6 addressing that cannot be routed over the Internet. Which address range should they use?

A) 2001:db8::/32  
B) FE80::/10  
C) FD00::/8  
D) FF00::/8

---

### Question 12
When troubleshooting IPv6 connectivity, an administrator notices that a router is not forwarding IPv6 packets between interfaces. The IPv6 addresses are configured correctly on all interfaces. What is the most likely cause?

A) IPv6 addresses are not in the same subnet  
B) The `ipv6 unicast-routing` command is not configured  
C) NDP is not functioning properly  
D) The interfaces are in shutdown state

---

### Question 13
A host receives a Router Advertisement (RA) message containing the prefix 2001:db8:1::/64. If SLAAC is enabled, what will the host do with this information?

A) Use the entire prefix as its IPv6 address  
B) Combine the prefix with its EUI-64 interface identifier to create a global unicast address  
C) Send a Neighbor Solicitation to verify the prefix  
D) Ignore the message since it's not addressed specifically to the host

---

### Question 14
Which command would display the IPv6 neighbor table on a Cisco router, showing the mappings between IPv6 addresses and MAC addresses?

A) `show ipv6 interface brief`  
B) `show ipv6 route`  
C) `show ipv6 neighbors`  
D) `show ipv6 arp`

---

### Question 15
A network administrator is configuring an anycast address on multiple routers for load balancing. The address to be configured is 2001:db8:1:1::99. Which command should be used?

A) `ipv6 address 2001:db8:1:1::99/128 anycast`  
B) `ipv6 address 2001:db8:1:1::99/64 anycast`  
C) `ipv6 anycast-address 2001:db8:1:1::99/128`  
D) `ipv6 address 2001:db8:1:1::99/128 multicast`

---

## Answer Key

1. **A** - `ipv6 address 2001:db8:1234:5678::1/64`
2. **A** - 2001:0db8:0000:0000:0000:0000:1234:5678
3. **B** - 021A:2BFF:FE3C:4D5E (7th bit flipped: 00 becomes 02)
4. **C** - Link-local address
5. **B** - `ipv6 route 2001:db8:2::/64 gigabitethernet0/1 fe80::1`
6. **A** - FF02::1:FFcd:ef12 (last 24 bits: cd:ef12)
7. **B** - 16 bits (from /48 to /64)
8. **C** - Neighbor Solicitation (NS)
9. **A** - Statically configured route
10. **B** - FF02::2 (all routers)
11. **C** - FD00::/8 (Unique Local Addresses)
12. **B** - The `ipv6 unicast-routing` command is not configured
13. **B** - Combine the prefix with its EUI-64 interface identifier
14. **C** - `show ipv6 neighbors`
15. **A** - `ipv6 address 2001:db8:1:1::99/128 anycast`

---

## Explanations

**Question 3**: EUI-64 process: Split MAC (00:1A:2B | 3C:4D:5E), insert FFFE (00:1A:2B:FF:FE:3C:4D:5E), flip 7th bit (00 becomes 02).

**Question 5**: When using link-local addresses as next-hop, you must specify the exit interface.

**Question 6**: Solicited-node multicast = FF02::1:FF + last 24 bits of unicast address.

**Question 12**: IPv6 routing is disabled by default, unlike IPv4 which is enabled by default.

**Question 15**: Anycast addresses typically use /128 (host route) since they represent a specific service.