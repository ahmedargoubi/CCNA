# CCNA Subnetting - 15 Question Practice Quiz

## Instructions
These questions are based on the most commonly repeated subnetting concepts in CCNA exams. Choose the best answer for each question.

---

## Question 1
What is the subnet mask for a /27 network?

A) 255.255.255.192  
B) 255.255.255.224  
C) 255.255.255.240  
D) 255.255.255.248  

---

## Question 2
How many usable host addresses are available in a /26 network?

A) 30  
B) 62  
C) 126  
D) 254  

---

## Question 3
Given the IP address 192.168.10.45/28, what is the network address?

A) 192.168.10.32  
B) 192.168.10.40  
C) 192.168.10.48  
D) 192.168.10.0  

---

## Question 4
Which subnet does the IP address 172.16.100.25/20 belong to?

A) 172.16.96.0  
B) 172.16.100.0  
C) 172.16.112.0  
D) 172.16.64.0  

---

## Question 5
A company needs to subnet the 10.0.0.0/8 network to create exactly 30 subnets. What is the minimum subnet mask that should be used?

A) /13  
B) /14  
C) /15  
D) /16  

---

## Question 6
What is the broadcast address for the network 192.168.5.96/27?

A) 192.168.5.127  
B) 192.168.5.119  
C) 192.168.5.111  
D) 192.168.5.95  

---

## Question 7
How many /24 subnets can be created from a /22 network?

A) 2  
B) 4  
C) 8  
D) 16  

---

## Question 8
Given the VLSM requirements below for network 192.168.1.0/24, which subnet should be assigned to the Sales department?
- Sales: 50 hosts  
- Engineering: 25 hosts  
- Management: 10 hosts  
- Point-to-Point Links: 2 hosts each  

A) 192.168.1.0/26  
B) 192.168.1.64/26  
C) 192.168.1.128/26  
D) 192.168.1.192/26  

---

## Question 9
What is the last usable host address in the subnet 10.10.16.0/20?

A) 10.10.31.254  
B) 10.10.31.255  
C) 10.10.16.254  
D) 10.10.32.254  

---

## Question 10
A network administrator receives the 172.16.0.0/16 network and needs to create subnets that support exactly 1000 hosts each. What subnet mask should be used?

A) /20  
B) /21  
C) /22  
D) /23  

---

## Question 11
Which of the following IP addresses are in the same subnet as 192.168.50.97/26? (Choose all that apply)

A) 192.168.50.65  
B) 192.168.50.95  
C) 192.168.50.126  
D) 192.168.50.127  

---

## Question 12
What is the network address and subnet mask in dotted decimal notation for 10.1.1.130/25?

A) Network: 10.1.1.0, Mask: 255.255.255.128  
B) Network: 10.1.1.128, Mask: 255.255.255.128  
C) Network: 10.1.1.128, Mask: 255.255.255.0  
D) Network: 10.1.1.0, Mask: 255.255.255.0  

---

## Question 13
A company has been assigned 203.100.10.0/24. They need to create 8 equal-sized subnets. How many hosts can each subnet support?

A) 14  
B) 30  
C) 32  
D) 62  

---

## Question 14
What is the summary address that covers all of these networks?
- 172.16.4.0/24  
- 172.16.5.0/24  
- 172.16.6.0/24  
- 172.16.7.0/24  

A) 172.16.0.0/20  
B) 172.16.4.0/22  
C) 172.16.4.0/23  
D) 172.16.0.0/22  

---

## Question 15
A host has the IP address 192.168.20.177/28. What is the next subnet's network address?

A) 192.168.20.192  
B) 192.168.20.180  
C) 192.168.20.184  
D) 192.168.20.176  

---

## Answer Key & Explanations

### Question 1: **B** - 255.255.255.224
**/27 = 27 network bits, 5 host bits**
- Last octet: 11100000 = 128 + 64 + 32 = 224
- Subnet mask: 255.255.255.224

### Question 2: **B** - 62
**/26 = 6 host bits**
- Total addresses: 2^6 = 64
- Usable addresses: 64 - 2 = 62 (subtract network and broadcast)

### Question 3: **A** - 192.168.10.32
**/28 = 4 host bits, group size = 16**
- Subnets: 0, 16, 32, 48...
- 45 falls in the 32-47 range
- Network address: 192.168.10.32

### Question 4: **A** - 172.16.96.0
**/20 = 12 network bits in first 2 octets + 4 in 3rd octet**
- Group size in 3rd octet: 16
- 100 ÷ 16 = 6 remainder 4
- Network: 6 × 16 = 96
- Network address: 172.16.96.0

### Question 5: **A** - /13
**Need 30 subnets from /8**
- Borrowed bits needed: 2^5 = 32 subnets (minimum that gives ≥30)
- New mask: /8 + 5 = /13

### Question 6: **A** - 192.168.5.127
**192.168.5.96/27, group size = 32**
- Network: 192.168.5.96
- Next network: 192.168.5.128
- Broadcast: 192.168.5.127 (one less than next network)

### Question 7: **B** - 4
**/22 to /24 = borrowing 2 more bits**
- 2^2 = 4 subnets

### Question 8: **A** - 192.168.1.0/26
**Sales needs 50 hosts, requires /26 (62 hosts)**
- VLSM: Start with largest requirement first
- /26 provides 62 usable hosts
- First available subnet: 192.168.1.0/26

### Question 9: **A** - 10.10.31.254
**10.10.16.0/20, group size = 16 (in 3rd octet)**
- Network: 10.10.16.0
- Next network: 10.10.32.0  
- Broadcast: 10.10.31.255
- Last usable: 10.10.31.254

### Question 10: **C** - /22
**Need 1000+ hosts per subnet**
- 2^10 = 1024 addresses
- Need 10 host bits, so 32 - 10 = 22
- Subnet mask: /22

### Question 11: **A, B, C** 
**192.168.50.97/26, group size = 64**
- Subnet range: 192.168.50.64 - 192.168.50.127
- A) 65 ✓ (in range)
- B) 95 ✓ (in range)  
- C) 126 ✓ (in range)
- D) 127 ✗ (broadcast address)

### Question 12: **B**
**10.1.1.130/25, group size = 128**
- Subnets: 0-127, 128-255
- 130 falls in 128-255 range
- Network: 10.1.1.128, Mask: 255.255.255.128

### Question 13: **B** - 30
**Need 8 subnets from /24**
- 2^3 = 8 subnets, need 3 bits
- New mask: /24 + 3 = /27
- Host bits: 32 - 27 = 5
- Hosts per subnet: 2^5 - 2 = 30

### Question 14: **B** - 172.16.4.0/22
**Summarizing 172.16.4.0/24 through 172.16.7.0/24**
- Need to cover 4 networks (4, 5, 6, 7)
- Binary: 4 = 100, 7 = 111
- Common bits: 10 (first 2 bits)
- Summary: 172.16.4.0/22

### Question 15: **A** - 192.168.20.192
**192.168.20.177/28, group size = 16**
- Current subnet: 176-191 (177 is in this range)
- Next subnet starts at: 192
- Next network: 192.168.20.192

---

## Scoring Guide
- **14-15 correct**: Excellent! Ready for CCNA subnetting questions
- **12-13 correct**: Very good, review VLSM and summarization
- **10-11 correct**: Good foundation, practice more calculations  
- **8-9 correct**: Basic understanding, focus on fundamentals
- **Below 8**: Review subnetting concepts thoroughly

## Key Concepts Tested
1. **CIDR to Subnet Mask conversion**
2. **Calculating usable hosts** (2^n - 2)
3. **Finding network addresses** using group size method
4. **VLSM planning** for different host requirements  
5. **Broadcast address calculation**
6. **Subnet range identification**  
7. **Route summarization** (supernetting)
8. **Binary to decimal conversion** skills
9. **Next network calculation**
10. **Same subnet identification**

## Study Tips for CCNA
- **Master the cheat sheet** - memorize group sizes and subnet masks
- **Practice mental math** - powers of 2 up to 2^16  
- **Use the "magic number" method** for quick calculations
- **Understand VLSM** - always start with largest requirements
- **Know your formulas**: 2^n - 2, 2^borrowed bits = subnets
- **Practice daily** - 15-20 questions minimum