# IPv4 Header CCNA Quiz - 15 Questions

## Question 1
What is the minimum size of an IPv4 header?

A) 16 bytes  
B) 20 bytes  
C) 24 bytes  
D) 32 bytes  

---

## Question 2
What is the maximum size of an IPv4 header?

A) 40 bytes  
B) 50 bytes  
C) 60 bytes  
D) 65 bytes  

---

## Question 3
What protocol number is used for TCP in the IPv4 header Protocol field?

A) 1  
B) 6  
C) 17  
D) 89  

---

## Question 4
What protocol number is used for UDP in the IPv4 header Protocol field?

A) 1  
B) 6  
C) 17  
D) 89  

---

## Question 5
What protocol number is used for ICMP in the IPv4 header Protocol field?

A) 1  
B) 6  
C) 17  
D) 89  

---

## Question 6
An IPv4 header has an IHL field value of 6. What is the total header length?

A) 20 bytes  
B) 24 bytes  
C) 28 bytes  
D) 32 bytes  

---

## Question 7
What is the purpose of the TTL (Time To Live) field in the IPv4 header?

A) To indicate packet priority  
B) To prevent routing loops  
C) To identify the source address  
D) To specify the total packet length  

---

## Question 8
What is the recommended default value for the TTL field?

A) 32  
B) 64  
C) 128  
D) 255  

---

## Question 9
Which field in the IPv4 header is used for Quality of Service (QoS) marking?

A) Version  
B) TTL  
C) DSCP  
D) Protocol  

---

## Question 10
How many bits is the DSCP field in the IPv4 header?

A) 4 bits  
B) 6 bits  
C) 8 bits  
D) 16 bits  

---

## Question 11
What happens when a router receives a packet with a TTL value of 0?

A) The packet is forwarded normally  
B) The packet is dropped and an ICMP message is sent  
C) The TTL is reset to default value  
D) The packet is queued for later processing  

---

## Question 12
Which three fields in the IPv4 header are primarily used for packet fragmentation?

A) Version, IHL, Total Length  
B) Identification, Flags, Fragment Offset  
C) TTL, Protocol, Header Checksum  
D) DSCP, ECN, Total Length  

---

## Question 13
What is the maximum value that can be stored in the Total Length field of an IPv4 header?

A) 1500  
B) 9000  
C) 65535  
D) 4294967295  

---

## Question 14
How many bits are used for the source and destination address fields combined in an IPv4 header?

A) 32 bits  
B) 48 bits  
C) 64 bits  
D) 128 bits  

---

## Question 15
If the IHL field contains a value of 5, what can you conclude about the IPv4 header?

A) The header contains Options field  
B) The header is the minimum size with no Options  
C) The header is corrupted  
D) The header is using IPv6  

---

## Answer Key
1. B) 20 bytes
2. C) 60 bytes
3. B) 6
4. C) 17
5. A) 1
6. B) 24 bytes (6 × 4 = 24)
7. B) To prevent routing loops
8. B) 64
9. C) DSCP
10. B) 6 bits
11. B) The packet is dropped and an ICMP message is sent
12. B) Identification, Flags, Fragment Offset
13. C) 65535
14. C) 64 bits (32 + 32)
15. B) The header is the minimum size with no Options

## Score Guide
- 13-15 correct: Excellent! IPv4 header expert
- 10-12 correct: Good knowledge, review a few concepts
- 7-9 correct: Fair understanding, need more study
- Below 7: Focus on memorizing key values and field purposes