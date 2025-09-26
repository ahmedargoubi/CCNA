# CCNA WAN Architectures Quiz #2 - 12 Additional Questions

*These are additional CCNA-style questions covering different aspects of WAN architectures that frequently appear on the exam.*

---

## Question 1
**A company is implementing a WAN solution that requires guaranteed bandwidth, predictable latency, and the highest level of security. Cost is not a primary concern. Which WAN technology best meets these requirements?**

A. MPLS VPN  
B. Internet VPN using IPSec  
C. Leased lines  
D. Cable Internet with VPN  

---

## Question 2
**What is the correct encapsulation protocol typically used on serial interfaces for leased line connections?**

A. Ethernet  
B. Frame Relay  
C. PPP or HDLC  
D. ATM  

---

## Question 3
**In a GRE tunnel configuration, what command is used to specify the physical interface that the tunnel will use as its source?**

A. tunnel source interface  
B. tunnel destination  
C. ip tunnel source  
D. tunnel mode gre ip  

---

## Question 4
**A network administrator wants to connect 4 branch offices in a configuration where each office can communicate directly with every other office without traffic passing through a central hub. How many connections will be required?**

A. 3  
B. 4  
C. 6  
D. 12  

---

## Question 5
**Which technology allows multiple customers to share the same physical infrastructure while maintaining traffic separation through the use of labels?**

A. Frame Relay  
B. ATM  
C. MPLS  
D. ISDN  

---

## Question 6
**What is the speed of an E1 circuit?**

A. 1.544 Mbps  
B. 2.048 Mbps  
C. 64 Kbps  
D. 34.368 Mbps  

---

## Question 7
**A remote user needs to securely access company resources from a coffee shop using a laptop. The solution should work through web browsers without requiring special client software. Which VPN type is most appropriate?**

A. IPSec site-to-site VPN  
B. Clientless SSL VPN  
C. GRE tunnel  
D. DMVPN  

---

## Question 8
**In an 802.1X authentication framework, what component does the wireless access point represent?**

A. Supplicant  
B. Authenticator  
C. Authentication Server  
D. RADIUS Client  

---

## Question 9
**Which routing protocol characteristic makes it incompatible with standard IPSec tunnels?**

A. Use of hop count as a metric  
B. Requirement for multicast Hello packets  
C. Support for VLSM  
D. Administrative distance values  

---

## Question 10
**A company has implemented DMVPN with a hub router at headquarters and spoke routers at branch offices. What happens when Branch A needs to send traffic to Branch B?**

A. Traffic always goes through the hub router  
B. A direct tunnel is automatically created between Branch A and Branch B  
C. Traffic is load-balanced between hub and direct paths  
D. Branch routers cannot communicate directly  

---

## Question 11
**Which Internet access technology typically provides the highest upload and download speeds in metropolitan areas?**

A. DSL  
B. Cable Internet (DOCSIS)  
C. Fiber optic Internet  
D. Satellite Internet  

---

## Question 12
**What is the primary difference between Transport mode and Tunnel mode in IPSec?**

A. Transport mode is faster than Tunnel mode  
B. Transport mode encrypts the entire packet, while Tunnel mode encrypts only the payload  
C. Transport mode encrypts only the payload, while Tunnel mode encrypts the entire packet  
D. Transport mode supports multicast, while Tunnel mode does not  

---

## ANSWER KEY

**Question 1: C** - Leased lines  
*Leased lines provide dedicated bandwidth with guaranteed performance and the highest security since they're private circuits. MPLS and Internet VPNs share infrastructure with other customers.*

**Question 2: C** - PPP or HDLC  
*Serial interfaces on leased lines typically use PPP (Point-to-Point Protocol) or HDLC (High-Level Data Link Control) encapsulation for Layer 2 framing.*

**Question 3: A** - tunnel source interface  
*The "tunnel source" command specifies which physical interface the tunnel will use. You can specify either an interface name or IP address.*

**Question 4: C** - 6  
*Using the full mesh formula: n(n-1)/2 where n=4 sites. 4(4-1)/2 = 4×3/2 = 6 connections needed for full mesh connectivity.*

**Question 5: C** - MPLS  
*MPLS (Multi-Protocol Label Switching) uses labels to create separate paths through shared provider infrastructure, enabling VPN services over shared networks.*

**Question 6: B** - 2.048 Mbps  
*E1 (European standard) provides 2.048 Mbps bandwidth with 32 channels of 64 Kbps each. T1 (North American) is 1.544 Mbps.*

**Question 7: B** - Clientless SSL VPN  
*Clientless SSL VPNs (WebVPN) work through standard web browsers without requiring special client software installation, perfect for public computers.*

**Question 8: B** - Authenticator  
*In 802.1X, the access point acts as the authenticator, controlling network access based on authentication decisions from the authentication server (RADIUS).*

**Question 9: B** - Requirement for multicast Hello packets  
*Routing protocols like OSPF and EIGRP use multicast packets for neighbor discovery and updates. IPSec only supports unicast traffic.*

**Question 10: B** - A direct tunnel is automatically created between Branch A and Branch B  
*DMVPN's key benefit is automatic spoke-to-spoke tunnel creation for direct communication, eliminating unnecessary hub transit.*

**Question 11: C** - Fiber optic Internet  
*Fiber optic connections typically provide the highest speeds (often gigabit+) with symmetric upload/download capabilities in metropolitan areas.*

**Question 12: C** - Transport mode encrypts only the payload, while Tunnel mode encrypts the entire packet  
*Transport mode preserves original IP headers (host-to-host), while Tunnel mode encrypts everything and adds new headers (site-to-site).*

---

## Advanced Study Points for WAN Architectures

### Technical Implementation Details:
1. **GRE Configuration**: Remember tunnel source/destination commands
2. **IPSec Modes**: Transport (host-to-host) vs Tunnel (site-to-site)
3. **Speed Standards**: T1/E1 differences between regions
4. **VPN Client Types**: Clientless, thin client, full client options
5. **DMVPN Benefits**: Hub-and-spoke config with full-mesh performance

### Business Decision Factors:
- **Guaranteed Performance** → Leased Lines
- **Cost Optimization** → MPLS or Internet VPN
- **Scalability** → MPLS or DMVPN
- **Remote Users** → SSL/TLS VPN
- **Site-to-Site** → IPSec VPN

### Common Troubleshooting Scenarios:
- **Routing protocol not working over IPSec** → Need GRE over IPSec
- **High tunnel configuration overhead** → Consider DMVPN
- **Users can't access from public computers** → Use clientless SSL VPN
- **Need direct branch-to-branch communication** → DMVPN or full mesh

### Configuration Command Recall:
```cisco
! GRE Tunnel Basics
interface tunnel X
 tunnel source [interface]
 tunnel destination [IP]
 ip address [tunnel-IP] [mask]

! PPP with Authentication  
interface serial X
 encapsulation ppp
 ppp authentication chap
```

### Exam Strategy Tips:
- **Read questions carefully** - distinguish between site-to-site and remote access
- **Consider business requirements** - cost vs performance vs security
- **Know the formulas** - full mesh connections = n(n-1)/2
- **Understand limitations** - IPSec multicast issues, speed differences
- **Practice scenarios** - when to recommend each tec