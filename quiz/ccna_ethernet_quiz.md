# CCNA Ethernet LAN Switching Quiz - Advanced Level

## Question 1
A network administrator notices that a switch's MAC address table shows some entries as "STATIC" while others show "DYNAMIC". The administrator runs `clear mac address-table dynamic` but notices that some MAC addresses remain in the table. What is the most likely explanation for this behavior?

**A)** The switch is malfunctioning and requires a reboot  
**B)** Static entries are manually configured and are not affected by the clear dynamic command  
**C)** The connected devices immediately sent new frames, repopulating the table  
**D)** The clear command only works on specific VLAN entries  

---

## Question 2
A host needs to send a 40-byte payload in an Ethernet frame. What will be the total frame size after the switch processes it?

**A)** 58 bytes  
**B)** 64 bytes  
**C)** 40 bytes  
**D)** 46 bytes  

---

## Question 3
During network troubleshooting, you observe the following ping output:
```
Router# ping 192.168.1.100
.!!!!
Success rate is 80 percent (4/5)
```
What is the most likely cause of this pattern?

**A)** The destination host is powered off  
**B)** ARP resolution occurred before the first ping, causing initial delay  
**C)** There's a routing loop in the network  
**D)** The network cable is damaged  

---

## Question 4
An engineer captures an ARP request on the network and notices the destination MAC address field contains FF:FF:FF:FF:FF:FF. However, the ARP request also contains a specific destination IP address. Why does the ARP request use a broadcast MAC when it knows the specific IP address it's looking for?

**A)** It's an error in the ARP implementation  
**B)** The purpose is to find the unknown MAC address corresponding to that known IP address  
**C)** ARP requests always use broadcast regardless of the destination  
**D)** The broadcast ensures the frame reaches all VLANs  

---

## Question 5
A switch receives an Ethernet frame with a source MAC address it has never seen before on port Fa0/5. What will the switch do with this information?

**A)** Drop the frame because the MAC is unknown  
**B)** Forward the frame to all ports except Fa0/5  
**C)** Add the source MAC address to its MAC address table associated with port Fa0/5  
**D)** Send an ARP request to verify the MAC address  

---

## Question 6
You need to remove only the MAC address entries learned on interface FastEthernet0/10 without affecting other interfaces. Which command would you use?

**A)** `clear mac address-table dynamic`  
**B)** `clear mac address-table dynamic interface FastEthernet0/10`  
**C)** `clear mac address-table dynamic address FastEthernet0/10`  
**D)** `delete mac address-table interface FastEthernet0/10`  

---

## Question 7
A network has hosts with IP addresses 192.168.1.10 and 192.168.1.20. Host A (192.168.1.10) wants to communicate with Host B (192.168.1.20) for the first time. Both hosts are on the same subnet. What sequence of messages will occur?

**A)** ICMP Echo Request → ICMP Echo Reply  
**B)** ARP Request → ARP Reply → Data Frame  
**C)** ARP Request → ARP Reply → ICMP Echo Request → ICMP Echo Reply  
**D)** Data Frame → ARP Request → ARP Reply  

---

## Question 8
An Ethernet frame arrives at a switch with a total size of 72 bytes. The data portion of this frame contains how many bytes?

**A)** 72 bytes  
**B)** 54 bytes  
**C)** 46 bytes  
**D)** Cannot be determined without more information  

---

## Question 9
A switch's MAC address table shows the following entry:
```
Vlan    MAC Address       Type        Ports
1       00:1A:2B:3C:4D:5E DYNAMIC     Fa0/1
```
If the switch receives a frame destined for MAC address 00:1A:2B:3C:4D:5E, what action will it take?

**A)** Broadcast the frame to all ports in VLAN 1  
**B)** Forward the frame only to port Fa0/1  
**C)** Drop the frame because it's a dynamic entry  
**D)** Send an ARP request to verify the destination  

---

## Question 10
During network analysis, you discover that some Ethernet frames in your network contain padding. Under what specific condition does this padding get added to frames?

**A)** When the frame size exceeds 1518 bytes  
**B)** When the data payload is less than 46 bytes  
**C)** When the frame travels between different VLAN segments  
**D)** When the frame contains broadcast traffic  

---

## Answer Key

1. **B** - Static entries are manually configured and remain after clearing dynamic entries
2. **B** - 40 bytes payload + 18 bytes header/trailer + 6 bytes padding = 64 bytes minimum
3. **B** - First ping fails during ARP resolution time, subsequent pings succeed
4. **B** - ARP finds unknown MAC addresses for known IP addresses via broadcast
5. **C** - Switches learn source MAC addresses and associate them with incoming ports
6. **B** - Specific interface clearing syntax
7. **B** - ARP must occur first to resolve MAC addresses before data communication
8. **B** - 72 total bytes - 18 bytes header/trailer = 54 bytes data
9. **B** - Known unicast frames are forwarded only to the specific learned port
10. **B** - Padding ensures minimum 46-byte payload requirement is met

## Scoring Guide
- **9-10 correct**: Excellent understanding of Ethernet switching concepts
- **7-8 correct**: Good grasp with minor gaps to review
- **5-6 correct**: Fair understanding, recommend additional study
- **Below 5**: Review fundamental concepts before advancing