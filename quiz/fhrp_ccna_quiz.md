# FHRP CCNA Quiz - Most Repeated Questions
## 10 Questions That Always Appear on CCNA Exams

---

### Question 1 ⭐ (ALWAYS appears)
What is the default virtual MAC address for HSRP group 5 using version 1?

A) 0000.0c07.ac05  
B) 0000.0c9f.f005  
C) 0000.5e00.0105  
D) 0007.b400.0505  

---

### Question 2 ⭐ (ALWAYS appears)
Which multicast address does VRRP use for communication?

A) 224.0.0.2  
B) 224.0.0.18  
C) 224.0.0.102  
D) 224.0.0.5  

---

### Question 3 ⭐ (ALWAYS appears)
Which FHRP protocol is an open standard?

A) HSRP  
B) VRRP  
C) GLBP  
D) None of them  

---

### Question 4 ⭐ (ALWAYS appears)
In HSRP, Router A has priority 120 and Router B has priority 80. Which router will be active?

A) Router A  
B) Router B  
C) Both will be active  
D) The one with lowest IP address  

---

### Question 5 ⭐ (ALWAYS appears)
What command is used to view HSRP status?

A) show ip hsrp  
B) show standby  
C) show hsrp brief  
D) show redundancy  

---

### Question 6 ⭐ (ALWAYS appears)
What happens when an HSRP active router fails and then comes back online (default configuration)?

A) It immediately becomes active again  
B) It becomes standby  
C) It causes network disruption  
D) It forces new election  

---

### Question 7 ⭐ (ALWAYS appears)
Which protocol provides load balancing among gateways?

A) HSRP v1  
B) HSRP v2  
C) VRRP  
D) GLBP  

---

### Question 8 ⭐ (ALWAYS appears)
What is the purpose of Gratuitous ARP in FHRP?

A) To find the virtual IP  
B) To update switch MAC tables during failover  
C) To authenticate routers  
D) To load balance traffic  

---

### Question 9 ⭐ (ALWAYS appears)
HSRP version 2 uses which multicast address?

A) 224.0.0.2  
B) 224.0.0.18  
C) 224.0.0.102  
D) 224.0.0.22  

---

### Question 10 ⭐ (ALWAYS appears)
In VRRP terminology, what is the active router called?

A) Active  
B) Primary  
C) Master  
D) Leader  

---

## ANSWER KEY

**Question 1: A** - 0000.0c07.ac05  
*HSRP v1 format: 0000.0c07.acXX (XX = group number in hex)*

**Question 2: B** - 224.0.0.18  
*VRRP always uses 224.0.0.18*

**Question 3: B** - VRRP  
*VRRP = Open Standard, HSRP/GLBP = Cisco Proprietary*

**Question 4: A** - Router A  
*Higher priority = Active router (120 > 80)*

**Question 5: B** - show standby  
*Standard command for HSRP verification*

**Question 6: B** - It becomes standby  
*Non-preemptive by default*

**Question 7: D** - GLBP  
*Only GLBP does load balancing*

**Question 8: B** - To update switch MAC tables during failover  
*Critical for proper failover operation*

**Question 9: C** - 224.0.0.102  
*HSRP v2 uses 224.0.0.102 (v1 uses 224.0.0.2)*

**Question 10: C** - Master  
*VRRP uses Master/Backup, HSRP uses Active/Standby*

---

## 🎯 MEMORIZE THESE FOR GUARANTEED POINTS:

### Multicast Addresses (Always Tested)
- HSRP v1: **224.0.0.2**
- HSRP v2: **224.0.0.102**  
- VRRP: **224.0.0.18**
- GLBP: **224.0.0.102**

### Virtual MAC Formats (Always Tested)
- HSRP v1: **0000.0c07.acXX**
- HSRP v2: **0000.0c9f.fXXX**
- VRRP: **0000.5e00.01XX**
- GLBP: **0007.b400.XXYY**

### Protocol Facts (Always Tested)
- **VRRP = Open Standard**
- **HSRP/GLBP = Cisco Proprietary**
- **Only GLBP does load balancing**
- **Higher Priority = Active/Master**
- **Non-preemptive by default**

### Commands (Always Tested)
- **show standby** (HSRP)
- **show vrrp** (VRRP)
- **show glbp** (GLBP)