# CCNA WAN Architectures Quiz - 12 Essential Questions

*These questions follow the exact CCNA exam format and cover the most frequently tested WAN architecture concepts.*

---

## Question 1
**A company has a headquarters and 8 branch offices. The branches primarily communicate with headquarters, with minimal inter-branch communication. Budget is a primary concern. Which WAN topology should be implemented?**

A. Full mesh topology  
B. Hub-and-spoke topology  
C. Partial mesh topology  
D. Point-to-point topology  

---

## Question 2
**What is the primary advantage of MPLS Layer 3 VPN over MPLS Layer 2 VPN?**

A. Lower cost implementation  
B. The service provider handles routing between sites  
C. Better security features  
D. Higher bandwidth availability  

---

## Question 3
**Which protocol is typically used for site-to-site VPN connections over the Internet?**

A. TLS  
B. SSL  
C. IPSec  
D. HTTPS  

---

## Question 4
**In an MPLS network, which router type is responsible for adding and removing MPLS labels?**

A. CE (Customer Edge) router  
B. PE (Provider Edge) router  
C. P (Provider Core) router  
D. CPE (Customer Premises Equipment) router  

---

## Question 5
**A network administrator needs to allow OSPF to run across an IPSec tunnel between two sites. What solution should be implemented?**

A. Configure OSPF to use unicast instead of multicast  
B. Implement GRE over IPSec  
C. Use static routing instead of OSPF  
D. Configure IPSec in transport mode  

---

## Question 6
**What is the speed of a T1 leased line?**

A. 2.048 Mbps  
B. 1.544 Mbps  
C. 64 Kbps  
D. 44.736 Mbps  

---

## Question 7
**Which VPN technology is most commonly used for remote access by individual users connecting from home?**

A. IPSec site-to-site VPN  
B. GRE tunnel  
C. TLS/SSL VPN  
D. DMVPN  

---

## Question 8
**In a full mesh WAN topology with 6 sites, how many point-to-point connections are required?**

A. 6  
B. 12  
C. 15  
D. 30  

---

## Question 9
**What is the primary limitation of standard IPSec that prevents routing protocols like OSPF from working properly?**

A. High latency and jitter  
B. Lack of support for broadcast and multicast traffic  
C. Insufficient encryption strength  
D. Limited bandwidth capacity  

---

## Question 10
**Which Internet access technology uses existing telephone lines and allows simultaneous voice and data transmission?**

A. Cable Internet (CATV)  
B. Fiber optic Internet  
C. DSL (Digital Subscriber Line)  
D. Satellite Internet  

---

## Question 11
**What is the main benefit of DMVPN (Dynamic Multipoint VPN) over traditional IPSec site-to-site VPNs?**

A. Higher encryption strength  
B. Lower bandwidth requirements  
C. Automatic creation of spoke-to-spoke tunnels  
D. Support for multicast traffic  

---

## Question 12
**In an MPLS Layer 2 VPN, how do the CE routers appear to be connected?**

A. Through a Layer 3 routed network  
B. Directly connected on the same subnet  
C. Connected through multiple hops  
D. Connected via separate VPN tunnels  

---

## ANSWER KEY

**Question 1: B** - Hub-and-spoke topology  
*Hub-and-spoke minimizes the number of connections needed (only n-1 connections for n sites), making it the most cost-effective solution when branches primarily communicate with headquarters.*

**Question 2: B** - The service provider handles routing between sites  
*In Layer 3 MPLS VPN, PE routers handle routing decisions and maintain customer routing tables, while in Layer 2 MPLS VPN, the customer is responsible for routing.*

**Question 3: C** - IPSec  
*IPSec is the standard protocol for site-to-site VPNs, providing authentication, encryption, and integrity for network-to-network connections.*

**Question 4: B** - PE (Provider Edge) router  
*PE routers are at the edge of the provider network and are responsible for adding MPLS labels to incoming customer traffic and removing labels from outgoing traffic.*

**Question 5: B** - Implement GRE over IPSec  
*GRE supports multicast traffic that OSPF requires, while IPSec provides the security. GRE over IPSec combines both benefits.*

**Question 6: B** - 1.544 Mbps  
*T1 provides 1.544 Mbps of bandwidth, consisting of 24 channels of 64 Kbps each. (E1 is 2.048 Mbps, T3 is 44.736 Mbps)*

**Question 7: C** - TLS/SSL VPN  
*TLS/SSL VPNs (like Cisco AnyConnect) are designed for individual user remote access, often through web browsers or lightweight clients.*

**Question 8: C** - 15  
*Full mesh formula: n(n-1)/2 where n=number of sites. For 6 sites: 6(6-1)/2 = 6×5/2 = 15 connections.*

**Question 9: B** - Lack of support for broadcast and multicast traffic  
*IPSec only supports unicast traffic, preventing routing protocols that rely on multicast (like OSPF Hello packets) from functioning properly.*

**Question 10: C** - DSL (Digital Subscriber Line)  
*DSL uses existing telephone lines and frequency division to allow simultaneous voice and data transmission on the same line.*

**Question 11: C** - Automatic creation of spoke-to-spoke tunnels  
*DMVPN allows spoke sites to dynamically create direct tunnels to each other for optimal routing, without manual configuration of each tunnel.*

**Question 12: B** - Directly connected on the same subnet  
*In Layer 2 MPLS VPN, the provider network is transparent to CE routers, making them appear as if they're directly connected on the same Layer 2 segment.*

---

## Study Tips for CCNA WAN Architectures

### High-Frequency Concepts:
1. **Topology Calculations**: Remember the full mesh formula n(n-1)/2
2. **MPLS Router Roles**: CE (customer), PE (adds/removes labels), P (core forwarding)
3. **VPN Protocol Usage**: IPSec for site-to-site, TLS/SSL for remote access
4. **Speed Standards**: T1=1.544 Mbps, E1=2.048 Mbps, T3=44.736 Mbps
5. **IPSec Limitations**: No multicast support, solved by GRE over IPSec

### Memory Tricks:
- **Hub-and-spoke = Cost effective** (fewer connections)
- **Full mesh = Maximum redundancy** (but expensive)
- **MPLS Layer 2 = Same subnet** (transparent provider network)
- **MPLS Layer 3 = Provider routing** (PE routers handle routing)
- **DMVPN = Dynamic spoke-to-spoke** (automatic tunnel creation)

### Common Exam Scenarios:
- **Cost-sensitive with centralized resources** → Hub-and-spoke
- **Maximum redundancy required** → Full mesh  
- **OSPF across IPSec not working** → Need GRE over IPSec
- **Individual remote users** → TLS/SSL VPN
- **Network-to-network connection** → IPSec site-to-site VPN