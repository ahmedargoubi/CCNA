# EtherChannel CCNA Quiz - 10 Questions

## Instructions
Choose the best answer for each question. Answers are provided at the end.

---

### Question 1
What is the maximum number of physical interfaces that can be bundled into a single EtherChannel?

A) 4  
B) 6  
C) 8  
D) 12  

---

### Question 2
Which EtherChannel protocol is Cisco proprietary?

A) LACP  
B) PAgP  
C) STP  
D) VTP  

---

### Question 3
An administrator wants to configure an EtherChannel using LACP where the switch actively negotiates the formation of the channel. Which mode should be used?

A) Auto  
B) Desirable  
C) Active  
D) Passive  

---

### Question 4
What happens when you configure EtherChannel mode as "on"?

A) The interface uses PAgP for negotiation  
B) The interface uses LACP for negotiation  
C) The interface forces EtherChannel formation without negotiation  
D) The interface waits for the other side to initiate negotiation  

---

### Question 5
Which of the following requirements must be met for interfaces to successfully form an EtherChannel? (Choose the best answer)

A) Same speed and duplex settings only  
B) Same VLAN configuration only  
C) Same speed, duplex, VLAN configuration, and trunking mode  
D) Only the same IP address configuration  

---

### Question 6
A network administrator configures one side of an EtherChannel with PAgP mode "desirable" and the other side with LACP mode "active". What will happen?

A) The EtherChannel will form successfully  
B) The EtherChannel will not form due to protocol mismatch  
C) Only the PAgP side will be active  
D) The protocols will automatically negotiate compatibility  

---

### Question 7
What is the IEEE standard for Link Aggregation Control Protocol (LACP)?

A) 802.1Q  
B) 802.3ad (now 802.1AX)  
C) 802.11  
D) 802.1D  

---

### Question 8
Which command would you use to verify the status and member ports of all EtherChannels on a switch?

A) `show interfaces trunk`  
B) `show etherchannel summary`  
C) `show vlan brief`  
D) `show spanning-tree`  

---

### Question 9
How does EtherChannel typically distribute traffic across its member links?

A) Round-robin basis  
B) Based on link utilization  
C) Using hashing algorithms based on MAC or IP addresses  
D) Randomly  

---

### Question 10
What logical interface is created when EtherChannel is configured?

A) Virtual-channel interface  
B) Port-channel interface  
C) Trunk-channel interface  
D) Link-channel interface  

---

## Answer Key

**Question 1:** C) 8  
*EtherChannel supports a maximum of 8 physical links per channel.*

**Question 2:** B) PAgP  
*Port Aggregation Protocol (PAgP) is Cisco's proprietary EtherChannel protocol.*

**Question 3:** C) Active  
*LACP "active" mode actively sends LACP packets to negotiate EtherChannel formation.*

**Question 4:** C) The interface forces EtherChannel formation without negotiation  
*"On" mode bypasses all negotiation protocols and forces the EtherChannel.*

**Question 5:** C) Same speed, duplex, VLAN configuration, and trunking mode  
*All these parameters must match for successful EtherChannel formation.*

**Question 6:** B) The EtherChannel will not form due to protocol mismatch  
*PAgP and LACP are incompatible protocols and cannot form an EtherChannel together.*

**Question 7:** B) 802.3ad (now 802.1AX)  
*LACP is defined in IEEE 802.3ad standard, which is now part of 802.1AX.*

**Question 8:** B) `show etherchannel summary`  
*This command displays the status and member ports of all EtherChannels.*

**Question 9:** C) Using hashing algorithms based on MAC or IP addresses  
*EtherChannel uses hashing algorithms to distribute traffic across member links.*

**Question 10:** B) Port-channel interface  
*When EtherChannel is configured, a logical Port-channel interface is created.*

---

## Scoring Guide
- **9-10 correct:** Excellent! You have a strong understanding of EtherChannel concepts.
- **7-8 correct:** Good job! Review the missed topics for better understanding.
- **5-6 correct:** Fair. More study needed on EtherChannel fundamentals.
- **Below 5:** Consider reviewing the EtherChannel material thoroughly before retaking.