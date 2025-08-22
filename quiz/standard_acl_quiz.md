# Standard ACL - CCNA Practice Quiz

## Instructions
Choose the best answer for each question. These questions reflect common CCNA exam patterns for Standard ACL topics.

---

## Question 1
A network administrator wants to deny access from the 192.168.10.0/24 network to a server at 10.0.0.5. Where should the standard ACL be applied for optimal performance?

A) Inbound on the interface closest to the 192.168.10.0 network  
B) Outbound on the interface closest to the 192.168.10.0 network  
C) Inbound on the interface closest to the 10.0.0.5 server  
D) Outbound on the interface closest to the 10.0.0.5 server  

---

## Question 2
Which command correctly creates a standard ACL entry to permit only the host 172.16.1.100?

A) `access-list 10 permit 172.16.1.100`  
B) `access-list 10 permit 172.16.1.100 0.0.0.0`  
C) `access-list 10 permit host 172.16.1.100`  
D) Both B and C are correct  

---

## Question 3
An administrator configures the following ACL:
```
access-list 15 deny 192.168.1.10 0.0.0.0
access-list 15 permit 192.168.1.0 0.0.0.255
```
What will happen to traffic from host 192.168.1.10?

A) It will be permitted because of the second line  
B) It will be denied by the implicit deny  
C) It will be denied by the first line  
D) It will be permitted by default  

---

## Question 4
Which wildcard mask should be used to match hosts 192.168.4.32 through 192.168.4.63?

A) 0.0.0.15  
B) 0.0.0.31  
C) 0.0.0.32  
D) 0.0.0.63  

---

## Question 5
A router has the following configuration:
```
interface FastEthernet0/0
 ip access-group 25 in
!
access-list 25 permit 10.0.0.0 0.255.255.255
```
What traffic will be allowed through FastEthernet0/0?

A) Only traffic from the 10.0.0.0/8 network entering the interface  
B) Only traffic from the 10.0.0.0/8 network leaving the interface  
C) All traffic from any network  
D) No traffic will be allowed due to implicit deny  

---

## Question 6
Which ACL number ranges are valid for standard ACLs?

A) 1-99 only  
B) 100-199 only  
C) 1-99 and 1300-1999  
D) 1-99 and 100-199  

---

## Question 7
An administrator wants to create a named standard ACL. Which command sequence is correct?

A) `ip access-list standard MYACL` then `permit any`  
B) `access-list standard MYACL` then `10 permit any`  
C) `ip access-list standard MYACL` then `10 permit any`  
D) Both A and C are correct  

---

## Question 8
Given this ACL configuration:
```
ip access-list standard SALES
 10 deny host 192.168.2.50
 30 permit 192.168.2.0 0.0.0.255
```
What happens to traffic from 192.168.2.100?

A) It is permitted by line 30  
B) It is denied by line 10  
C) It is denied by implicit deny  
D) The ACL has a configuration error  

---

## Question 9
A standard ACL is applied outbound on interface Serial0/0. Traffic from which direction will be filtered by this ACL?

A) Traffic entering Serial0/0 from the network  
B) Traffic leaving Serial0/0 toward the network  
C) Traffic in both directions  
D) No traffic will be filtered  

---

## Question 10
Which statement about the implicit deny in ACLs is correct?

A) It can be removed with the `no access-list implicit-deny` command  
B) It only applies to standard ACLs, not extended ACLs  
C) It appears at the end of every ACL and cannot be seen or removed  
D) It can be overridden by adding `permit any` at the beginning of the ACL  

---

## Answer Key

1. **C** - Standard ACLs should be placed close to the destination, inbound on the interface closest to the server.

2. **D** - Both `access-list 10 permit 172.16.1.100 0.0.0.0` and `access-list 10 permit host 172.16.1.100` are correct ways to specify a single host.

3. **C** - The first line specifically denies 192.168.1.10, so it will be denied before reaching the permit statement.

4. **B** - To match 32 hosts (192.168.4.32-192.168.4.63), the wildcard mask is 0.0.0.31 (32-1=31).

5. **A** - The ACL permits traffic from 10.0.0.0/8 network entering (inbound) the FastEthernet0/0 interface.

6. **C** - Standard ACLs use numbers 1-99 and the extended range 1300-1999.

7. **D** - Both commands are correct. Named ACLs can use sequence numbers (optional) or not.

8. **A** - Traffic from 192.168.2.100 matches the permit statement on line 30 for the 192.168.2.0/24 network.

9. **B** - An outbound ACL filters traffic leaving the interface toward the network.

10. **C** - The implicit deny is automatically added to every ACL, cannot be seen in the configuration, and cannot be removed.

## Scoring Guide
- **9-10 correct**: Excellent understanding of Standard ACLs
- **7-8 correct**: Good knowledge, review placement rules and wildcard masks
- **5-6 correct**: Basic understanding, study ACL processing and syntax
- **Below 5**: Review all Standard ACL concepts thoroughly