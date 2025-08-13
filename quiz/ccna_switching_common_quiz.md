# CCNA Ethernet LAN Switching - Most Repeated Quiz Questions

**Instructions**: Choose the best answer for each question. These are the most commonly tested concepts on CCNA exams.

---

## Question 1
When a switch receives a frame, from which MAC address does it learn and add to its MAC address table?

A) Destination MAC address  
B) Source MAC address  
C) Both source and destination MAC addresses  
D) The broadcast MAC address  

---

## Question 2
What action does a switch take when it receives a frame with a destination MAC address that is NOT in its MAC address table?

A) Drops the frame silently  
B) Forwards the frame to the default gateway  
C) Floods the frame out all ports in the same VLAN except the incoming port  
D) Sends an ICMP destination unreachable message  

---

## Question 3
What is the default MAC address aging timer on Cisco switches?

A) 150 seconds  
B) 300 seconds  
C) 600 seconds  
D) 900 seconds  

---

## Question 4
A switch receives a frame on port Fa0/5 with source MAC 00:AA:BB:CC:DD:01 and destination MAC 00:11:22:33:44:55. The MAC table shows:
```
Vlan 1    00:11:22:33:44:55   DYNAMIC   Fa0/3
```
What happens?

A) Frame is dropped  
B) Frame is flooded to all ports  
C) Source MAC is learned on Fa0/5, frame forwarded out Fa0/3  
D) Frame is sent to the default gateway  

---

## Question 5
Which command displays the MAC address table on a Cisco switch?

A) `show mac table`  
B) `show mac address-table`  
C) `show cam table`  
D) `show switching table`  

---

## Question 6
What happens when the same MAC address appears on multiple ports repeatedly?

A) Normal switching behavior  
B) MAC flapping - indicates a potential loop  
C) Automatic load balancing  
D) Port security violation  

---

## Question 7
A switch has learned a MAC address on port Fa0/2. If a frame arrives on port Fa0/7 with the same source MAC address, what happens to the MAC table entry?

A) No change occurs  
B) A duplicate entry is created  
C) The entry is updated to show port Fa0/7  
D) The entry is removed from the table  

---

## Question 8
Which types of frames are always flooded by a switch?

A) Unknown unicast only  
B) Broadcast only  
C) Unknown unicast, broadcast, and multicast (by default)  
D) Only frames with errors  

---

## Question 9
What is the difference between DYNAMIC and STATIC entries in a MAC address table?

A) Dynamic entries are faster to process  
B) Static entries are manually configured and never age out  
C) Dynamic entries are more secure  
D) Static entries use less memory  

---

## Question 10
In a MAC address table, what does the VLAN field indicate?

A) The physical VLAN cable connected  
B) The VLAN in which the MAC address is valid  
C) The priority of the VLAN  
D) The speed of the VLAN  

---

## Answer Key

**Question 1: B) Source MAC address**  
*Explanation: Switches learn MAC addresses from the SOURCE MAC address of incoming frames, not the destination. This is the fundamental principle of MAC learning.*

**Question 2: C) Floods the frame out all ports in the same VLAN except the incoming port**  
*Explanation: When a destination MAC is unknown (not in the MAC table), the switch floods the frame to all ports in the same VLAN except the port it arrived on.*

**Question 3: B) 300 seconds**  
*Explanation: The default MAC address aging timer on Cisco switches is 300 seconds (5 minutes). This is one of the most frequently tested facts.*

**Question 4: C) Source MAC is learned on Fa0/5, frame forwarded out Fa0/3**  
*Explanation: The switch learns the source MAC (00:AA:BB:CC:DD:01) on port Fa0/5, then looks up the destination MAC and finds it on Fa0/3, so forwards the frame only to Fa0/3.*

**Question 5: B) `show mac address-table`**  
*Explanation: The correct Cisco IOS command to display the MAC address table is `show mac address-table`. This command format is frequently tested.*

**Question 6: B) MAC flapping - indicates a potential loop**  
*Explanation: MAC flapping occurs when the same MAC address appears on multiple ports repeatedly, typically indicating a network loop or misconfigured port channel.*

**Question 7: C) The entry is updated to show port Fa0/7**  
*Explanation: When a switch sees a previously learned MAC address on a different port, it updates the entry to reflect the new port (indicating the device has moved).*

**Question 8: C) Unknown unicast, broadcast, and multicast (by default)**  
*Explanation: Switches flood unknown unicast frames, broadcast frames (FF:FF:FF:FF:FF:FF), and multicast frames (unless multicast snooping is configured).*

**Question 9: B) Static entries are manually configured and never age out**  
*Explanation: STATIC entries are manually configured by administrators and never age out, while DYNAMIC entries are learned automatically and age out after the aging timer expires.*

**Question 10: B) The VLAN in which the MAC address is valid**  
*Explanation: The VLAN field indicates which VLAN the MAC address belongs to, as the same MAC address can exist in different VLANs simultaneously.*

---

## Most Frequently Tested Concepts

### MAC Learning Process (Critical!)
1. **Source MAC learning** - switches learn from source, NOT destination
2. **Port association** - MAC addresses are mapped to specific ports
3. **Dynamic updates** - entries update when device moves to different port

### Frame Forwarding Decisions
- **Known unicast**: Forward to specific port
- **Unknown unicast**: Flood to all ports in VLAN (except incoming)
- **Broadcast**: Always flood to all ports in VLAN (except incoming)

### MAC Table Management
- **Aging timer**: Default 300 seconds
- **Dynamic vs Static**: Dynamic ages out, static doesn't
- **MAC flapping**: Same MAC on multiple ports = problem

### Commands You Must Know
```
show mac address-table           ! View MAC table
show mac address-table dynamic   ! Show only dynamic entries
clear mac address-table         ! Clear dynamic entries
```

## Exam Scenarios Always Tested
1. **Frame forwarding with known/unknown destinations**
2. **MAC learning from source addresses**
3. **What happens when devices move ports**
4. **Flooding behavior for different frame types**
5. **MAC table aging and cleanup**

## Scoring Guide
- **9-10 correct**: Excellent! You've mastered switching fundamentals.
- **7-8 correct**: Good! Review MAC learning and flooding concepts.
- **5-6 correct**: Fair. Focus on frame forwarding decisions and MAC table operations.
- **Below 5**: Study switching fundamentals thoroughly - these are core CCNA concepts.

## Study Tips
- **Always remember**: Learn from SOURCE, forward based on DESTINATION
- **Memorize the 300-second aging timer** - appears frequently
- **Understand flooding vs forwarding** - critical for troubleshooting scenarios
- **Practice with frame walk-through scenarios** - very common exam format