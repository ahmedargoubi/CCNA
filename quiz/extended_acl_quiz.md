# Extended ACL - CCNA Practice Quiz

## Instructions
Choose the best answer for each question. These questions reflect common CCNA exam patterns for Extended ACL topics.

---

## Question 1
A network administrator wants to block all HTTP traffic from the 10.0.1.0/24 network to any destination. Which ACL entry is correct?

A) `access-list 101 deny tcp 10.0.1.0 0.0.0.255 any eq 80`  
B) `access-list 15 deny tcp 10.0.1.0 0.0.0.255 any eq 80`  
C) `access-list 101 deny http 10.0.1.0 0.0.0.255 any`  
D) `access-list 101 deny ip 10.0.1.0 0.0.0.255 any eq 80`  

---

## Question 2
Where should an extended ACL be placed for optimal network performance?

A) As close to the source as possible  
B) As close to the destination as possible  
C) Only on core routers  
D) On both source and destination interfaces  

---

## Question 3
Which command creates a named extended ACL called "WEB_FILTER"?

A) `access-list extended WEB_FILTER`  
B) `ip access-list WEB_FILTER extended`  
C) `ip access-list extended WEB_FILTER`  
D) `access-list WEB_FILTER extended`  

---

## Question 4
An administrator configures:
```
access-list 110 deny tcp any host 192.168.1.5 eq 23
access-list 110 permit ip any any
```
What does this ACL accomplish?

A) Denies all TCP traffic to 192.168.1.5  
B) Denies Telnet traffic to 192.168.1.5, permits everything else  
C) Denies SSH traffic to 192.168.1.5  
D) Permits only Telnet traffic to 192.168.1.5  

---

## Question 5
Which port operator would match TCP ports 8000 through 8999?

A) `eq 8000-8999`  
B) `range 8000 8999`  
C) `gt 7999 lt 9000`  
D) Both B and C are correct  

---

## Question 6
What is the correct syntax to deny ICMP echo-reply packets from any source to the 172.16.0.0/16 network?

A) `deny icmp any 172.16.0.0 0.0.255.255 echo-reply`  
B) `deny icmp any 172.16.0.0 0.0.255.255 eq echo-reply`  
C) `deny ping any 172.16.0.0 0.0.255.255`  
D) `deny icmp any 172.16.0.0 0.0.255.255 type 0`  

---

## Question 7
An extended ACL is configured with the following entries in order:
```
10 deny tcp host 10.0.0.5 any eq 80
20 permit tcp 10.0.0.0 0.0.0.255 any eq 80
30 deny ip any any
```
What happens to HTTP traffic from host 10.0.0.5?

A) It is permitted by line 20  
B) It is denied by line 10  
C) It is denied by line 30  
D) The ACL has a configuration error  

---

## Question 8
Which ACL number ranges are valid for extended ACLs?

A) 1-99 and 1300-1999  
B) 100-199 and 2000-2699  
C) 100-199 only  
D) 200-299 and 2000-2999  

---

## Question 9
A company wants to allow only HTTPS and SSH traffic from the internal network (192.168.0.0/16) to any external destination. Which ACL configuration is correct?

A) 
```
access-list 120 permit tcp 192.168.0.0 0.0.255.255 any eq 443
access-list 120 permit tcp 192.168.0.0 0.0.255.255 any eq 22
```

B) 
```
access-list 120 permit tcp 192.168.0.0 0.0.255.255 any eq 443
access-list 120 permit tcp 192.168.0.0 0.0.255.255 any eq 22
access-list 120 deny ip any any
```

C) Both A and B will work the same way  
D) Neither A nor B will work correctly  

---

## Question 10
What does the following ACL entry accomplish?
```
permit udp any gt 1023 host 10.0.0.10 eq 53
```

A) Permits DNS queries from any host to 10.0.0.10  
B) Permits DNS responses from 10.0.0.10 to any host  
C) Permits DNS queries from hosts using high-numbered ports to 10.0.0.10  
D) Permits all UDP traffic to 10.0.0.10  

---

## Question 11
An administrator wants to block all traffic from subnet 172.16.5.0/24 to subnet 172.16.10.0/24, but allow all other traffic. Which configuration is most efficient?

A) Apply the ACL inbound on the interface connected to 172.16.5.0/24  
B) Apply the ACL outbound on the interface connected to 172.16.10.0/24  
C) Apply the ACL on multiple interfaces  
D) Use a standard ACL instead  

---

## Question 12
Which statement about the implicit deny in extended ACLs is correct?

A) It can be overridden by placing `permit any any` at the beginning  
B) It only applies to the protocol specified in the first ACL entry  
C) It denies all traffic not explicitly permitted by previous entries  
D) It can be disabled with the `no implicit-deny` command  

---

## Question 13
What is the difference between these two ACL entries?
```
Entry 1: deny tcp any any eq 80
Entry 2: deny ip any any eq 80
```

A) Entry 1 is correct, Entry 2 will cause an error  
B) Both entries accomplish the same thing  
C) Entry 1 blocks only TCP port 80, Entry 2 blocks all protocols on port 80  
D) Entry 2 is more specific than Entry 1  

---

## Question 14
A network administrator configures:
```
ip access-list extended MANAGEMENT
 10 permit tcp host 10.0.0.100 any eq 22
 20 permit tcp host 10.0.0.100 any eq 23
```
Later, they want to insert a new entry between lines 10 and 20. What should they do?

A) Delete the entire ACL and recreate it  
B) Use sequence number 15: `15 permit tcp host 10.0.0.100 any eq 80`  
C) Add the entry with sequence number 25  
D) Remove line 20 and re-add both entries  

---

## Question 15
An ACL is applied inbound on interface FastEthernet0/0 with the following configuration:
```
access-list 105 deny tcp any host 192.168.1.100 eq 80
access-list 105 permit ip any any
```
What traffic will be affected by this ACL?

A) All traffic entering and leaving FastEthernet0/0  
B) Only HTTP traffic to 192.168.1.100 entering FastEthernet0/0  
C) All traffic entering FastEthernet0/0  
D) Only traffic from 192.168.1.100 leaving FastEthernet0/0  

---

## Answer Key

1. **A** - Extended ACLs use numbers 100-199 or 2000-2699. TCP protocol with port 80 (HTTP) is correct.

2. **A** - Extended ACLs should be placed as close to the source as possible to prevent unnecessary traffic flow.

3. **C** - The correct syntax is `ip access-list extended <name>`.

4. **B** - Port 23 is Telnet. The ACL denies Telnet to 192.168.1.5 but permits all other traffic.

5. **B** - The `range` operator specifies a range of ports. Option C would work but is less efficient.

6. **A** - ICMP echo-reply messages use the `echo-reply` keyword in ACLs.

7. **B** - ACL processing is top-down. Host 10.0.0.5 matches the first line (deny) and processing stops.

8. **B** - Extended ACLs use 100-199 and 2000-2699.

9. **B** - Option A is incomplete because there's no explicit deny, so the implicit deny will block everything else. Option B explicitly denies all other traffic.

10. **C** - This permits DNS queries (port 53) from clients using high-numbered source ports (gt 1023) to the DNS server.

11. **A** - Extended ACLs should be placed close to the source (inbound on 172.16.5.0/24's interface).

12. **C** - The implicit deny at the end of every ACL denies all traffic not explicitly permitted.

13. **A** - IP protocol doesn't use port numbers. Only TCP and UDP use ports, so Entry 2 would cause a syntax error.

14. **B** - Named ACLs allow insertion of entries using sequence numbers between existing entries.

15. **B** - An inbound ACL affects only traffic entering the interface. This specific ACL only denies HTTP traffic to 192.168.1.100.

## Scoring Guide
- **14-15 correct**: Excellent mastery of Extended ACLs
- **12-13 correct**: Very good understanding, review placement and syntax
- **10-11 correct**: Good knowledge, focus on port operations and protocol specifics
- **8-9 correct**: Basic understanding, study ACL processing and configuration
- **Below 8**: Review all Extended ACL concepts thoroughly

## Key Areas to Review Based on Common Mistakes
- **ACL placement rules** (Extended = close to source, Standard = close to destination)
- **Protocol vs port confusion** (IP protocol doesn't use ports)
- **ACL processing order** (top-down, first match wins)
- **Named ACL editing capabilities** (sequence numbers for insertion)
- **Inbound vs outbound application** effects