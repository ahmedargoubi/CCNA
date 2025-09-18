# CCNA Exam Questions: Life of a Packet

## Question 1
**A host with IP address 192.168.1.10 sends a packet to 192.168.2.20 through two routers. What happens to the source IP address as the packet travels through the network?**

A) It changes at each router to reflect the router's interface IP address  
B) It remains 192.168.1.10 throughout the entire journey  
C) It changes only at the destination router  
D) It alternates between the source and destination IP addresses  

---

## Question 2
**Which field in a packet header changes as it passes through each router in the path from source to destination?**

A) Source IP address  
B) Destination IP address  
C) Source MAC address  
D) Protocol field  

---

## Question 3
**Host A (192.168.1.5) wants to send data to Host B (10.1.1.10). Both hosts are connected to the same switch, but Host A's default gateway is 192.168.1.1. What will Host A do?**

A) Send the frame directly to Host B's MAC address  
B) Send the frame to the default gateway's MAC address  
C) Broadcast an ARP request for Host B  
D) Drop the packet because the destination is unreachable  

---

## Question 4
**In an Ethernet frame header, what is the correct order of the first two address fields?**

A) Source MAC address, then Destination MAC address  
B) Destination MAC address, then Source MAC address  
C) Source IP address, then Destination IP address  
D) Destination IP address, then Source IP address  

---

## Question 5
**A packet travels from Host 1 through Router A, Router B, and Router C to reach Host 2. How many times will the Time-To-Live (TTL) field be decremented?**

A) 1 time  
B) 2 times  
C) 3 times  
D) 4 times  

---

## Question 6
**Host A needs to send a packet to Host B on a different subnet. Host A has never communicated with its default gateway before. What must happen before Host A can send the packet?**

A) Host A must learn Host B's MAC address  
B) Host A must send an ARP request to learn the default gateway's MAC address  
C) Host A must update its routing table  
D) Host A must establish a TCP connection with the default gateway  

---

## Additional Practice Scenarios

### Scenario-Based Question 7 (Bonus)
**Network Topology:**
```
PC1 (192.168.1.10) ---- Router1 ---- Router2 ---- PC2 (192.168.2.10)
MAC: AA:AA:AA:AA:AA:01   G0/0: BB:BB:BB:BB:BB:01   G0/1: CC:CC:CC:CC:CC:01   MAC: DD:DD:DD:DD:DD:02
                         G0/1: BB:BB:BB:BB:BB:02   G0/0: CC:CC:CC:CC:CC:02
```

**When PC1 sends a packet to PC2, what will be the source MAC address in the frame when it reaches PC2?**

A) AA:AA:AA:AA:AA:01 (PC1's MAC)  
B) BB:BB:BB:BB:BB:02 (Router1's G0/1 interface)  
C) CC:CC:CC:CC:CC:01 (Router2's G0/1 interface)  
D) CC:CC:CC:CC:CC:02 (Router2's G0/0 interface)

## Key Exam Tips

### Most Common Wrong Answers:
1. **Thinking IP addresses change** - They NEVER do
2. **Confusing header order** - Remember: Ethernet (Dest first), IP (Source first)
3. **Forgetting ARP resolution** - Always needed for first communication
4. **Misunderstanding same vs different subnet** - Physical connectivity ≠ logical connectivity

### Quick Memory Aids:
- **"IPs are eternal"** - IP addresses never change
- **"MACs are local"** - MAC addresses change at each hop
- **"Ethernet Destination First"** - Unlike IP headers
- **"ARP before you hop"** - Need MAC addresses to forward