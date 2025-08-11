# CCNA Software Defined Networking (SDN) Quiz

## Instructions
- Choose the **best** answer for each question
- Each question has only **one correct answer**
- Mark your answers and check against the answer key at the bottom
- Time limit: 15 minutes (similar to CCNA pace)

---

## Question 1
What is the primary difference between traditional networking and Software Defined Networking (SDN)?

**A)** SDN uses faster hardware than traditional networking  
**B)** SDN centralizes the control plane into a controller, while traditional networking distributes it across devices  
**C)** SDN only works with Cisco equipment  
**D)** SDN eliminates the need for physical network devices  

---

## Question 2
In SDN architecture, what is the purpose of the Southbound Interface (SBI)?

**A)** Communication from applications to the controller  
**B)** Communication from the controller to network devices  
**C)** Communication between end users and applications  
**D)** Communication between different controllers  

---

## Question 3
Which Cisco SDN solution is specifically designed for automating campus LANs?

**A)** SD-WAN  
**B)** ACI (Application Centric Infrastructure)  
**C)** SD-Access  
**D)** DNA Center  

---

## Question 4
What does Intent-Based Networking (IBN) allow network administrators to do?

**A)** Configure devices manually with CLI commands  
**B)** Describe what they want and let the system figure out how to configure devices  
**C)** Monitor network traffic in real-time  
**D)** Replace all physical network equipment  

---

## Question 5
In SD-Access terminology, what is the "fabric"?

**A)** Only the physical network infrastructure  
**B)** Only the virtual network overlays  
**C)** The combination of underlay and overlay networks  
**D)** The controller software  

---

## Question 6
Which protocol is used for the control plane in SD-Access to map endpoint identifiers to routing locators?

**A)** OSPF  
**B)** EIGRP  
**C)** LISP  
**D)** BGP  

---

## Question 7
What is the data plane protocol used in SD-Access for forwarding traffic through tunnels?

**A)** GRE  
**B)** VXLAN  
**C)** IPSec  
**D)** MPLS  

---

## Question 8
In SD-Access, what is the role of a Border Node?

**A)** Connects end hosts to the network  
**B)** Runs LISP for endpoint-to-location mapping  
**C)** Connects to networks outside SD-Access  
**D)** Provides wireless connectivity  

---

## Question 9
What is the main characteristic of a Greenfield SD-Access deployment?

**A)** Adding SD-Access to existing network infrastructure  
**B)** Building a new network with optimal SD-Access design using Layer 3 everywhere  
**C)** Using only wireless access points  
**D)** Implementing traditional routing protocols  

---

## Question 10
Which statement best describes the Northbound Interface (NBI) in SDN?

**A)** It connects the controller to network switches  
**B)** It provides communication from applications and scripts to the controller  
**C)** It handles data forwarding between devices  
**D)** It manages physical cable connections  

---

## Bonus Question 11
What are the three planes in SD-Access architecture?

**A)** Physical, Virtual, and Logical planes  
**B)** Control (LISP), Data (VXLAN), and Policy (CTS) planes  
**C)** Management, Configuration, and Monitoring planes  
**D)** Access, Distribution, and Core planes  

---

## CCNA Exam Tips
- Remember the **direction** of communication: Northbound (apps→controller), Southbound (controller→devices)
- Focus on **Cisco SDN solutions**: SD-Access (campus), ACI (data center), SD-WAN (WAN)
- Understand **deployment types**: Greenfield (new) vs Brownfield (existing)
- Know the **three planes**: Control (LISP), Data (VXLAN), Policy (CTS)

---

## Answer Key

### Question 1: **B) SDN centralizes the control plane into a controller, while traditional networking distributes it across devices**
**Explanation:** The fundamental difference in SDN is centralization of the control plane. Traditional networks have each device making independent routing decisions, while SDN moves this intelligence to a centralized controller.

### Question 2: **B) Communication from the controller to network devices**
**Explanation:** The Southbound Interface (SBI) enables the SDN controller to communicate with and configure the network infrastructure devices. Examples include NETCONF, RESTCONF, and OpenFlow.

### Question 3: **C) SD-Access**
**Explanation:** SD-Access is Cisco's SDN solution specifically designed for campus LANs. ACI is for data centers, and SD-WAN is for wide area networks.

### Question 4: **B) Describe what they want and let the system figure out how to configure devices**
**Explanation:** Intent-Based Networking (IBN) allows administrators to specify business intent ("what" they want) rather than specific device configurations ("how" to achieve it).

### Question 5: **C) The combination of underlay and overlay networks**
**Explanation:** The fabric is the complete SDN topology that includes both the physical underlay network and the virtual overlay network working together.

### Question 6: **C) LISP**
**Explanation:** LISP (Locator/ID Separation Protocol) is used in the control plane to map Endpoint Identifiers (EIDs) to Routing Locators (RLOCs) in SD-Access.

### Question 7: **B) VXLAN**
**Explanation:** VXLAN (Virtual Extensible LAN) is the data plane protocol used in SD-Access to create tunnels for forwarding traffic across the fabric.

### Question 8: **C) Connects to networks outside SD-Access**
**Explanation:** Border Nodes provide connectivity to external networks such as WANs, internet, or other network domains outside the SD-Access fabric.

### Question 9: **B) Building a new network with optimal SD-Access design using Layer 3 everywhere**
**Explanation:** Greenfield deployment involves building a new network from scratch with optimal design principles like Layer 3 everywhere and IS-IS routing protocol.

### Question 10: **B) It provides communication from applications and scripts to the controller**
**Explanation:** The Northbound Interface (NBI) allows applications, scripts, and management tools to communicate with the SDN controller, typically through REST APIs.

### Bonus Question 11: **B) Control (LISP), Data (VXLAN), and Policy (CTS) planes**
**Explanation:** SD-Access uses three planes: Control plane (LISP for endpoint mapping), Data plane (VXLAN for forwarding), and Policy plane (Cisco TrustSec for security/QoS).

---

## Scoring Guide
- **10-11 correct:** Excellent! Ready for CCNA SDN topics
- **8-9 correct:** Good understanding, review missed concepts  
- **6-7 correct:** Need more study on SDN fundamentals
- **Below 6:** Review the SDN study notes thoroughly before retaking

---

## Key Concepts Summary
✅ **SDN centralizes** control plane in controller  
✅ **Southbound API:** Controller → Devices (NETCONF, RESTCONF, OpenFlow)  
✅ **Northbound API:** Apps → Controller (REST API)  
✅ **Cisco SDN Solutions:** SD-Access (campus), ACI (data center), SD-WAN (WAN)  
✅ **DNA Center:** Controller for SD-Access  
✅ **Fabric = Underlay + Overlay**  
✅ **Three Planes:** Control (LISP), Data (VXLAN), Policy (CTS)  
✅ **Deployment:** Greenfield (new) vs Brownfield (existing)  
✅ **SD-Access Roles:** Edge, Border, Control nodes  

---

## Common Exam Scenarios
- **Scenario 1:** "A company wants to automate their campus network..." → **SD-Access**
- **Scenario 2:** "An application needs to request network services..." → **Northbound API**  
- **Scenario 3:** "The controller needs to configure switches..." → **Southbound API**
- **Scenario 4:** "Building a new network with optimal SDN design..." → **Greenfield**
- **Scenario 5:** "Mapping endpoints to switch locations..." → **LISP (Control plane)**

---

*Focus on understanding the relationships between components and the direction of communication flows - this is key to CCNA SDN success!*