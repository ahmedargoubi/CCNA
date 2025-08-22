# CCNA Dynamic ARP Inspection (DAI) Quiz - 10 Questions

**Topic:** Dynamic ARP Inspection (DAI)  
**Number of Questions:** 10  
**Time:** 20 minutes  

## Instructions
- Choose the **ONE** best answer for each question
- Questions reflect real CCNA exam style and difficulty
- Focus on advanced concepts and frequently tested scenarios

---

## Question 1
A network administrator is implementing DAI on a switch to prevent ARP poisoning attacks. The network has a DHCP server on VLAN 10 connected to interface Gi0/1, and client devices connected to interfaces Gi0/2-24. After configuring DAI, clients cannot obtain IP addresses. Which configuration is most likely missing?

A) `ip dhcp snooping trust` on interface Gi0/1  
B) `ip arp inspection trust` on all client interfaces  
C) `ip arp inspection database` configuration  
D) ARP rate limiting configuration on client ports

---

## Question 2
Examine the following configuration output:
```
Switch# show ip arp inspection interfaces
Interface        Trust State     Rate (pps)    Burst Interval
Gi0/1           Trusted         None          N/A
Gi0/10          Untrusted       15            1
Gi0/11          Untrusted       30            1
```

A user connected to Gi0/11 is running a network discovery tool that generates 35 ARP requests per second. What will happen?

A) The first 30 packets will be processed, remaining packets will be queued  
B) All packets will be processed but logged as violations  
C) The interface will be placed in err-disabled state  
D) DAI will dynamically increase the rate limit to accommodate the traffic

---

## Question 3
A company has implemented DAI on VLAN 20 where both DHCP clients and static servers coexist. The static servers have IP addresses 192.168.20.100-110 with known MAC addresses. What is the most appropriate configuration approach?

A) Configure all server ports as trusted and leave client ports untrusted  
B) Create ARP ACLs for static servers and apply them to VLAN 20  
C) Disable DAI validation and rely only on port security  
D) Configure static DHCP reservations for all servers

---

## Question 4
A network engineer notices that DAI is dropping legitimate ARP packets. The show command reveals:
```
DHCP snooping is configured on following VLANs: 30
Dynamic ARP inspection is configured on following VLANs: 30
```

However, the DHCP snooping binding table is empty despite active DHCP clients. What is the root cause?

A) DAI and DHCP snooping cannot operate on the same VLAN  
B) The DHCP server interface is not configured as trusted for DHCP snooping  
C) ARP ACLs are overriding DHCP snooping bindings  
D) The VLAN interface is administratively down

---

## Question 5
Which command sequence correctly creates and applies an ARP ACL for a server with IP 10.1.1.100 and MAC address aabb.ccdd.eeff on VLAN 50?

A) `arp access-list SERVER1` → `permit ip 10.1.1.100 mac aabb.ccdd.eeff` → `ip arp inspection filter SERVER1 vlan 50`  
B) `arp access-list SERVER1` → `permit ip host 10.1.1.100 mac host aabb.ccdd.eeff` → `ip arp inspection filter SERVER1 vlan 50`  
C) `ip arp access-list SERVER1` → `allow ip 10.1.1.100 mac aabb.ccdd.eeff` → `arp inspection apply SERVER1 vlan 50`  
D) `access-list arp SERVER1` → `permit 10.1.1.100 aabb.ccdd.eeff` → `dai filter SERVER1 vlan 50`

---

## Question 6
A switch has DAI enabled with the following configuration:
```
arp access-list STATIC-HOSTS
 permit ip host 192.168.1.50 mac host 1111.2222.3333
 permit ip host 192.168.1.51 mac host 4444.5555.6666
ip arp inspection filter STATIC-HOSTS vlan 100
```

A DHCP client with IP 192.168.1.75 (legitimately assigned) sends an ARP request on VLAN 100. What will happen?

A) The ARP request will be dropped because it doesn't match the ACL  
B) The ARP request will be validated against the DHCP snooping binding table  
C) The ARP request will be forwarded without validation  
D) The client will be required to use static IP configuration

---

## Question 7
An administrator wants to automatically recover interfaces that are err-disabled due to ARP inspection rate limiting violations. The recovery should occur every 45 seconds. Which commands are required?

A) `errdisable recovery cause arp-inspection` and `errdisable recovery interval 45`  
B) `ip arp inspection recovery enable` and `ip arp inspection recovery time 45`  
C) `auto-recovery arp-inspection 45`  
D) `dai recovery interval 45`

---

## Question 8
In a network with DAI enabled, an attacker sends gratuitous ARP messages claiming to be the default gateway (192.168.1.1) from MAC address aaaa.bbbb.cccc. The legitimate gateway has MAC address 1234.5678.9abc and there's a valid DHCP binding for 192.168.1.1 with the correct MAC. What will DAI do?

A) Allow the gratuitous ARP because it's from a gateway IP  
B) Drop the gratuitous ARP because the MAC doesn't match the binding  
C) Forward the gratuitous ARP and log it as suspicious  
D) Allow the gratuitous ARP if the port is configured as trusted

---

## Question 9
A network administrator runs `show ip arp inspection statistics` and sees:
```
Packets forwarded:                    15420
Packets dropped:                      847
DHCP permits:                         14673
ACL permits:                          747
DHCP drops:                           823
ACL drops:                            24
```

Based on this output, what can be concluded?

A) The majority of dropped packets are due to ACL misconfigurations  
B) DHCP snooping bindings are working correctly for most traffic  
C) ARP ACLs are dropping legitimate traffic  
D) The switch is under an ARP flooding attack

---

## Question 10
A company implements comprehensive Layer 2 security including DAI, DHCP snooping, and IP Source Guard. A legitimate client obtains IP 10.1.1.100 via DHCP and begins normal network communication. Later, an attacker manually configures their device with IP 10.1.1.100 and attempts to communicate. Which security feature will prevent this attack?

A) DAI will drop the attacker's ARP packets  
B) DHCP snooping will block the attacker's DHCP requests  
C) IP Source Guard will drop the attacker's data packets  
D) All three features will collaborate to block different aspects of the attack

---

# Answer Key and Explanations

## Answer 1: A) `ip dhcp snooping trust` on interface Gi0/1
**Explanation:** DAI relies on DHCP snooping bindings for validation. If clients can't get IP addresses, the DHCP server interface likely isn't trusted for DHCP snooping, causing DHCP responses to be dropped before bindings can be created.

## Answer 2: C) The interface will be placed in err-disabled state
**Explanation:** Interface Gi0/11 has a rate limit of 30 pps. When the discovery tool generates 35 ARP requests per second, exceeding the configured limit, the interface will be err-disabled as a security measure.

## Answer 3: B) Create ARP ACLs for static servers and apply them to VLAN 20
**Explanation:** The most secure approach is to use ARP ACLs for static servers (defining trusted IP-to-MAC mappings) while allowing DHCP clients to be validated through DHCP snooping bindings.

## Answer 4: B) The DHCP server interface is not configured as trusted for DHCP snooping
**Explanation:** An empty DHCP snooping binding table despite active clients indicates DHCP responses are being blocked, most likely because the DHCP server interface isn't configured as trusted for DHCP snooping.

## Answer 5: B) `arp access-list SERVER1` → `permit ip host 10.1.1.100 mac host aabb.ccdd.eeff` → `ip arp inspection filter SERVER1 vlan 50`
**Explanation:** Correct syntax requires `arp access-list <n>` to create the ACL, `permit ip host <ip> mac host <mac>` for specific mappings, and `ip arp inspection filter <acl> vlan <vlan>` to apply it.

## Answer 6: B) The ARP request will be validated against the DHCP snooping binding table
**Explanation:** When ARP packets don't match an applied ARP ACL, DAI falls back to validating them against the DHCP snooping binding table rather than dropping them automatically.

## Answer 7: A) `errdisable recovery cause arp-inspection` and `errdisable recovery interval 45`
**Explanation:** These two commands enable automatic recovery from ARP inspection violations with a custom 45-second interval. Both commands are required for automatic recovery.

## Answer 8: B) Drop the gratuitous ARP because the MAC doesn't match the binding
**Explanation:** DAI will validate the gratuitous ARP against existing bindings. Since the attacker's MAC (aaaa.bbbb.cccc) doesn't match the legitimate gateway's MAC in the binding table, the packet will be dropped.

## Answer 9: B) DHCP snooping bindings are working correctly for most traffic
**Explanation:** The statistics show 14,673 DHCP permits vs 823 DHCP drops, indicating DHCP snooping validation is working well. The 747 ACL permits suggest ARP ACLs are also functioning properly.

## Answer 10: D) All three features will collaborate to block different aspects of the attack
**Explanation:** DAI will drop the attacker's ARP packets (wrong MAC for the IP), DHCP snooping prevents rogue DHCP responses, and IP Source Guard will drop data packets from unauthorized IP addresses, providing comprehensive protection.