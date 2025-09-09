# CCNA LAN Architectures Quiz - 15 Frequently Asked Exam Questions

---

### Question 1
Which layer of the three-tier hierarchical network model is responsible for providing network access to end devices such as PCs, printers, and IP phones?

**A)** Core layer  
**B)** Distribution layer  
**C)** Access layer  
**D)** Transport layer

---

### Question 2
In which layer of the hierarchical network design would you typically find PoE (Power over Ethernet) support for VoIP phones and wireless access points?

**A)** Core layer  
**B)** Distribution layer  
**C)** Access layer  
**D)** Application layer

---

### Question 3
What is the primary characteristic of a collapsed core design?

**A)** It uses only wireless connections  
**B)** It combines the core and distribution layers into a single layer  
**C)** It eliminates the access layer  
**D)** It requires more physical connections than three-tier

---

### Question 4
According to Cisco's recommendations, when should you implement a three-tier architecture instead of a two-tier architecture?

**A)** When you have more than 10 users  
**B)** When you have more than 3 distribution layer switches  
**C)** When you need wireless connectivity  
**D)** When you have more than 100 devices

---

### Question 5
In a full mesh topology with 5 network devices, how many connections would be required?

**A)** 5 connections  
**B)** 10 connections  
**C)** 15 connections  
**D)** 25 connections

---

### Question 6
What is the main purpose of the core layer in a three-tier hierarchical network design?

**A)** To provide end-user connectivity  
**B)** To provide high-speed transport between distribution layers  
**C)** To implement security policies  
**D)** To handle QoS classification

---

### Question 7
Which topology describes a network where several devices all connect to one central device?

**A)** Mesh topology  
**B)** Ring topology  
**C)** Star topology  
**D)** Bus topology

---

### Question 8
In spine-leaf architecture, which devices are end hosts (servers) allowed to connect to?

**A)** Only spine switches  
**B)** Only leaf switches  
**C)** Both spine and leaf switches  
**D)** Core switches only

---

### Question 9
What type of traffic pattern increase led to the development of spine-leaf architecture in data centers?

**A)** North-South traffic  
**B)** East-West traffic  
**C)** Internet traffic  
**D)** Management traffic

---

### Question 10
Which network architecture is most appropriate for a small home office with basic connectivity needs?

**A)** Three-tier hierarchical  
**B)** Spine-leaf architecture  
**C)** SOHO (all-in-one device)  
**D)** Full mesh topology

---

### Question 11
In spine-leaf architecture, how many hops are required for communication between servers connected to different leaf switches?

**A)** 1 hop  
**B)** 2 hops  
**C)** 3 hops  
**D)** Variable hops

---

### Question 12
What is a key characteristic that distinguishes the distribution layer from other layers in a three-tier architecture?

**A)** It only handles wireless traffic  
**B)** It typically serves as the boundary between Layer 2 and Layer 3  
**C)** It connects directly to the internet  
**D)** It only uses fiber optic connections

---

### Question 13
Which of the following is NOT a valid connection in spine-leaf architecture?

**A)** Leaf switch to spine switch  
**B)** Spine switch to leaf switch  
**C)** Server to leaf switch  
**D)** Leaf switch to leaf switch

---

### Question 14
In a two-tier network architecture, which layer typically connects to services such as the Internet and WAN?

**A)** Access layer  
**B)** Distribution layer  
**C)** Core layer  
**D)** Physical layer

---

### Question 15
What is the primary advantage of using a hierarchical network design model?

**A)** Reduced equipment costs  
**B)** Improved scalability, manageability, and fault isolation  
**C)** Elimination of all network protocols  
**D)** Automatic configuration of all devices

---

## Answer Key

**Question 1: C** - Access layer  
The access layer is where end devices like PCs, printers, and IP phones connect to the network.

**Question 2: C** - Access layer  
PoE for VoIP phones and access points is typically provided at the access layer, where these devices directly connect.

**Question 3: B** - It combines the core and distribution layers into a single layer  
A collapsed core design is essentially a two-tier architecture that omits the separate core layer.

**Question 4: B** - When you have more than 3 distribution layer switches  
Cisco recommends adding a core layer when there are more than 3 distribution layers in a single location.

**Question 5: B** - 10 connections  
Formula for full mesh: n(n-1)/2 = 5(4)/2 = 10 connections.

**Question 6: B** - To provide high-speed transport between distribution layers  
The core layer focuses on "fast transport" and high-speed connectivity between distribution layers.

**Question 7: C** - Star topology  
Star topology is characterized by multiple devices connecting to one central device.

**Question 8: B** - Only leaf switches  
In spine-leaf architecture, end hosts only connect to leaf switches, never to spine switches.

**Question 9: B** - East-West traffic  
The increase in server-to-server communication (East-West traffic) due to virtualization drove the need for spine-leaf architecture.

**Question 10: C** - SOHO (all-in-one device)  
Small Office/Home Office networks typically use all-in-one devices that combine multiple functions.

**Question 11: B** - 2 hops  
Communication between servers on different leaf switches requires 2 hops: Server → Leaf → Spine → Leaf → Server.

**Question 12: B** - It typically serves as the boundary between Layer 2 and Layer 3  
The distribution layer commonly serves as the border between Layer 2 switching and Layer 3 routing.

**Question 13: D** - Leaf switch to leaf switch  
In spine-leaf architecture, leaf switches never connect to each other.

**Question 14: B** - Distribution layer  
In two-tier architecture, the distribution layer connects to external services like Internet and WAN.

**Question 15: B** - Improved scalability, manageability, and fault isolation  
Hierarchical design provides better organization, easier troubleshooting, and improved scalability compared to flat network designs.