# Advanced NAT/PAT CCNA Quiz - 15 Questions
## Real CCNA Exam Style - Frequently Repeated Questions

---

### Question 1 ⭐ (ALWAYS on CCNA)
A network administrator configures the following NAT commands on RouterA:
```
access-list 10 permit 192.168.1.0 0.0.0.255
ip nat pool NATPOOL 203.0.113.10 203.0.113.15 netmask 255.255.255.0
ip nat inside source list 10 pool NATPOOL overload
```
What is the maximum number of simultaneous connections that can be supported?

A) 6 connections (one per IP in pool)
B) 65,536 connections per IP in pool
C) Approximately 393,216 total connections
D) Unlimited connections

---

### Question 2 ⭐ (Command Analysis - Very Common)
Given this NAT configuration:
```
interface FastEthernet0/0
 ip nat inside
interface Serial0/0
 ip nat outside
access-list 1 deny 192.168.1.100
access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 interface Serial0/0 overload
```
What happens when host 192.168.1.100 tries to access the internet?

A) The packet is dropped by the router
B) The packet is forwarded without NAT translation
C) The packet is translated using a different pool
D) An error message is generated

---

### Question 3 ⭐ (Translation Table - Always Tested)
Examine this NAT translation table output:
```
Pro Inside global    Inside local     Outside local    Outside global
tcp 200.1.1.1:1024  192.168.1.10:80  10.0.0.5:2000   10.0.0.5:2000
tcp 200.1.1.1:1025  192.168.1.20:443 172.16.0.1:3000 172.16.0.1:3000
```
Which statement is correct about this NAT implementation?

A) This is Static NAT with two servers
B) This is Dynamic NAT with pool exhaustion
C) This is PAT (NAT Overload) with port translation
D) This configuration will cause conflicts

---

### Question 4 ⭐ (Troubleshooting - Very Frequent)
A router configured with PAT shows these statistics:
```
Total active translations: 50 (0 static, 50 dynamic; 50 extended)
Peak translations: 120
Hits: 2500  Misses: 45
```
What does the "Misses: 45" indicate?

A) 45 packets were dropped due to no available translations
B) 45 packets required new translation entries to be created
C) 45 packets failed ACL permission checks
D) 45 packets had incorrect destination addresses

---

### Question 5 ⭐ (Configuration Error - Common)
A network engineer configured the following, but users cannot access the internet:
```
interface GigabitEthernet0/0
 ip address 192.168.1.1 255.255.255.0
interface GigabitEthernet0/1
 ip address 203.0.113.1 255.255.255.0
access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 interface GigabitEthernet0/1 overload
```
What is missing from this configuration?

A) NAT pool definition
B) Interface NAT inside/outside designation
C) Route to the internet
D) Extended ACL instead of standard ACL

---

### Question 6 ⭐ (Pool Exhaustion - Frequently Asked)
A company has 200 internal hosts and configures Dynamic NAT with this pool:
```
ip nat pool COMPANY 203.0.113.10 203.0.113.20 netmask 255.255.255.0
```
How many hosts can simultaneously access external networks?

A) 200 hosts
B) 11 hosts
C) 10 hosts
D) Unlimited hosts

---

### Question 7 ⭐ (Advanced PAT Scenario)
Host 192.168.1.50 with source port 3000 connects to server 8.8.8.8:53. The PAT router translates this to 200.1.1.1:5000. What will be the source and destination in the return packet from 8.8.8.8?

A) Source: 8.8.8.8:53, Destination: 192.168.1.50:3000
B) Source: 8.8.8.8:53, Destination: 200.1.1.1:5000
C) Source: 200.1.1.1:5000, Destination: 8.8.8.8:53
D) Source: 8.8.8.8:53, Destination: 200.1.1.1:3000

---

### Question 8 ⭐ (Static NAT vs PAT - Always Tested)
When would you choose Static NAT over PAT for a web server?

A) When you want to save public IP addresses
B) When the server needs to be accessible from the internet
C) When you have multiple web servers
D) When you want load balancing

---

### Question 9 ⭐ (ACL Logic - Very Common)
Given this configuration:
```
access-list 100 deny tcp 192.168.1.0 0.0.0.255 any eq 80
access-list 100 permit ip 192.168.1.0 0.0.0.255 any
ip nat inside source list 100 interface Serial0/0 overload
```
What happens to HTTP traffic from the 192.168.1.0/24 network?

A) HTTP traffic is blocked and not forwarded
B) HTTP traffic is forwarded without NAT translation
C) HTTP traffic is translated with a different rule
D) HTTP traffic generates an error

---

### Question 10 ⭐ (Command Output Analysis)
Which command would show you the current utilization of a NAT pool?

A) show ip nat statistics
B) show ip nat pool
C) show ip nat translations
D) show running-config | include nat

---

### Question 11 ⭐ (Advanced Configuration)
A company wants inside host 192.168.1.100 to always appear as 203.0.113.100 on the internet, but other hosts should use PAT with the router's outside interface IP. Which commands accomplish this?

A) 
```
ip nat inside source static 192.168.1.100 203.0.113.100
access-list 1 deny 192.168.1.100
access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 interface Serial0/0 overload
```

B)
```
ip nat inside source static 192.168.1.100 203.0.113.100
access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 interface Serial0/0 overload
```

C) Both A and B will work
D) Neither configuration will work

---

### Question 12 ⭐ (Troubleshooting Translation Issues)
A host with IP 192.168.1.10 cannot access external websites. The NAT configuration appears correct. Which debug command would help identify the issue?

A) debug ip packet
B) debug ip nat
C) debug ip route
D) debug interface

---

### Question 13 ⭐ (Port Ranges in PAT)
In PAT implementation, what is the typical source port range used for outbound connections?

A) 1-1023 (well-known ports)
B) 1024-5000 (registered ports)
C) 1024-65535 (dynamic ports)
D) 49152-65535 (private ports)

---

### Question 14 ⭐ (NAT Inside vs Outside)
A packet travels from 192.168.1.10 to 8.8.8.8 through a NAT router. At which point in the router does the source IP get translated?

A) When entering the inside interface
B) When exiting the outside interface
C) During routing table lookup
D) During ARP resolution

---

### Question 15 ⭐ (Complex Scenario - Exam Favorite)
Network topology: Host A (192.168.1.10) → RouterR1 (NAT) → RouterR2 → ServerB (203.0.113.50)

RouterR1 configuration:
```
ip nat inside source static 192.168.1.10 200.1.1.10
```

When Host A sends a packet to ServerB, what will be the source IP seen by ServerB?

A) 192.168.1.10
B) 200.1.1.10  
C) RouterR1's outside interface IP
D) RouterR2's interface IP closest to ServerB

---

## ANSWER KEY & EXPLANATIONS

**Question 1: C** - Approximately 393,216 total connections  
*Explanation: With PAT overload, each of the 6 IPs in the pool can support ~65,536 ports, giving roughly 393,216 total connections (6 × 65,536).*

**Question 2: B** - The packet is forwarded without NAT translation  
*Explanation: ACL deny means no translation occurs, but the packet is still forwarded if routing exists. Only permit entries get translated.*

**Question 3: C** - This is PAT (NAT Overload) with port translation  
*Explanation: Multiple inside locals (192.168.1.10:80, 192.168.1.20:443) map to same inside global IP (200.1.1.1) but different ports, indicating PAT.*

**Question 4: B** - 45 packets required new translation entries to be created  
*Explanation: "Misses" occur when a packet needs translation but no existing entry exists, so a new translation must be created.*

**Question 5: B** - Interface NAT inside/outside designation  
*Explanation: The interfaces lack "ip nat inside" and "ip nat outside" commands, which are mandatory for NAT operation.*

**Question 6: B** - 11 hosts  
*Explanation: Pool 203.0.113.10 to 203.0.113.20 contains 11 IPs. Dynamic NAT is one-to-one, so only 11 simultaneous connections.*

**Question 7: B** - Source: 8.8.8.8:53, Destination: 200.1.1.1:5000  
*Explanation: Return traffic uses the translated address/port as seen by the external server.*

**Question 8: B** - When the server needs to be accessible from the internet  
*Explanation: Static NAT allows inbound connections, making it suitable for servers that external users need to reach.*

**Question 9: B** - HTTP traffic is forwarded without NAT translation  
*Explanation: Extended ACL deny means no translation, but packets are still forwarded. Only permitted traffic gets translated.*

**Question 10: A** - show ip nat statistics  
*Explanation: This command shows pool utilization, active translations, hits, misses, and other statistics.*

**Question 11: A** - First configuration  
*Explanation: Static NAT for .100, then ACL denies .100 from PAT rule, other hosts use PAT. Option B would cause .100 to match both rules.*

**Question 12: B** - debug ip nat  
*Explanation: This debug specifically shows NAT translation processes, successes, and failures.*

**Question 13: C** - 1024-65535 (dynamic ports)  
*Explanation: PAT uses ephemeral/dynamic port range for outbound translations, avoiding well-known ports 1-1023.*

**Question 14: B** - When exiting the outside interface  
*Explanation: NAT translation occurs as packets exit the outside interface, after routing decisions are made.*

**Question 15: B** - 200.1.1.10  
*Explanation: Static NAT translates source IP from 192.168.1.10 to 200.1.1.10, which is what ServerB will see.*

---

## SCORING GUIDE

- **13-15 correct**: Expert level! You're ready for CCNA NAT questions
- **10-12 correct**: Advanced! Review the missed concepts  
- **7-9 correct**: Intermediate. Focus on PAT and troubleshooting
- **4-6 correct**: Basic understanding. Study NAT fundamentals more
- **0-3 correct**: Needs significant study of NAT/PAT concepts

## KEY TAKEAWAYS FOR EXAM SUCCESS

1. **PAT can handle ~65k connections per public IP** using port numbers
2. **ACL deny = no translation but packet still forwarded** (if routing exists)
3. **Static NAT allows inbound connections**, Dynamic/PAT typically don't
4. **Interface designation (inside/outside) is mandatory**
5. **Translation occurs when exiting outside interface**
6. **"Misses" in statistics = new translation entries created**
7. **Extended ACLs can selectively translate different traffic types**
8. **Static + PAT combination** is common for servers + general internet access