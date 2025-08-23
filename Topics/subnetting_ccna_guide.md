# Subnetting - Complete CCNA Guide

## Table of Contents
1. [Introduction to IP Address Classes](#introduction-to-ip-address-classes)
2. [The Problem with Classful Addressing](#the-problem-with-classful-addressing)
3. [CIDR - Classless Inter-Domain Routing](#cidr---classless-inter-domain-routing)
4. [Subnet Masks and CIDR Notation](#subnet-masks-and-cidr-notation)
5. [Calculating Usable Addresses](#calculating-usable-addresses)
6. [Subnetting Cheat Sheet](#subnetting-cheat-sheet)
7. [Step-by-Step Subnetting Method](#step-by-step-subnetting-method)
8. [Alternative Magic Octet Method](#alternative-magic-octet-method)
9. [Practical Examples](#practical-examples)
10. [Class C Network Subnetting](#class-c-network-subnetting)
11. [Class B Network Subnetting](#class-b-network-subnetting)
12. [Key Formulas and Quick Reference](#key-formulas-and-quick-reference)

---

## Introduction to IP Address Classes

### Traditional Classful Addressing
Only Class A, B, and C addresses can be assigned to devices as IP addresses.

| Class | Prefix Length | Default Subnet Mask | Address Range |
|-------|---------------|-------------------|---------------|
| A     | /8            | 255.0.0.0         | 1.0.0.0 - 126.255.255.255 |
| B     | /16           | 255.255.0.0       | 128.0.0.0 - 191.255.255.255 |
| C     | /24           | 255.255.255.0     | 192.0.0.0 - 223.255.255.255 |

### Address Assignment Authority
The **IANA (Internet Assigned Numbers Authority)** assigns IPv4 addresses/networks to companies based on their size.

---

## The Problem with Classful Addressing

**Classful assignment led to massive IP address waste:**
- A company needing 5,000 addresses was assigned a Class B network
- Class B provides 65,534 usable addresses
- This left over 60,000 addresses unused!

---

## CIDR - Classless Inter-Domain Routing

In **1993**, the **IETF (Internet Engineering Task Force)** introduced **CIDR** to solve the wastefulness problem.

### CIDR Benefits:
- Removed the rigid requirements of Class A, B, and C sizes
- Allowed larger networks to be split into smaller networks
- Enabled much greater efficiency in address allocation
- These smaller networks are called **"SUB-NETWORKS"** or **"SUBNETS"**

---

## Subnet Masks and CIDR Notation

### Converting CIDR to Subnet Mask

**Example: 203.0.113.0/25**

1. `/25` means the first 25 bits are the network portion
2. Convert to binary: `203.0.113.0` = `11001011.00000000.01110001.0|0000000`
3. First 25 bits are network, remaining 7 bits are host
4. Subnet mask: `11111111.11111111.11111111.1|0000000`
5. Convert to decimal: `255.255.255.128`

**Example: /28**

1. `/28` means first 28 bits are network
2. Remaining 4 bits are host
3. Subnet mask: `11111111.11111111.11111111.1111|0000`
4. Last octet: `11110000` = 128+64+32+16 = **240**
5. Subnet mask: `255.255.255.240`

---

## Calculating Usable Addresses

### Formula: 2^n - 2 = Usable Addresses
- **n** = number of host bits
- **-2** accounts for Network ID and Broadcast address

### Examples:
- `/25` has 7 host bits: 2^7 - 2 = 128 - 2 = **126 usable addresses**
- `/28` has 4 host bits: 2^4 - 2 = 16 - 2 = **14 usable addresses**

---

## Subnetting Cheat Sheet

| Group Size | 128 | 64  | 32  | 16  | 8   | 4   | 2   | 1   |
|------------|-----|-----|-----|-----|-----|-----|-----|-----|
| **Subnet Mask (4th Octet)** | 128 | 192 | 224 | 240 | 248 | 252 | 254 | 255 |
| **CIDR (4th Octet)** | /25 | /26 | /27 | /28 | /29 | /30 | /31 | /32 |
| **CIDR (3rd Octet)** | /17 | /18 | /19 | /20 | /21 | /22 | /23 | /24 |
| **CIDR (2nd Octet)** | /9  | /10 | /11 | /12 | /13 | /14 | /15 | /16 |
| **CIDR (1st Octet)** | /1  | /2  | /3  | /4  | /5  | /6  | /7  | /8  |

### How to Use the Cheat Sheet:
1. **CIDR/Subnet Mask** map to each other
2. **Locate Group Size**
3. **Increase by Group Size** until you PASS the Target IP
4. **If reaching 256**, increase the previous octet by 1, current octet becomes 0

---

## Step-by-Step Subnetting Method

### The Process:
1. Use the CIDR/Mask to find the column on the cheat sheet
2. Identify the Group Size
3. Count by Group Size: 0, Group Size, 2×Group Size, etc.
4. Find which range contains your target IP
5. Determine network boundaries

### Key Definitions:
- **Network ID**: Number BEFORE target IP
- **Next Network**: Number AFTER target IP  
- **Broadcast IP**: Address BEFORE next network
- **First Host**: Address AFTER network ID
- **Last Host**: Address BEFORE broadcast IP

### Example: 192.168.1.100/26
1. `/26` → Group Size = 64
2. Count: 0, 64, 128 (target 100 is between 64 and 128)
3. **Network**: 192.168.1.64
4. **Broadcast**: 192.168.1.127
5. **First Host**: 192.168.1.65
6. **Last Host**: 192.168.1.126
7. **Usable Addresses**: 62

---

## Alternative Magic Octet Method

### Steps:
1. **Find the "magic octet"** where the CIDR boundary falls
2. **Count network bits** in that octet
3. **Calculate Group Size**: 2^(network bits in magic octet)
4. **Subnet Mask**: 256 - Group Size (in magic octet)
5. **Find Network**: Divide target IP by Group Size
6. **Calculate ranges**

### Octet Boundaries:
- **4th Octet**: /25 - /32
- **3rd Octet**: /17 - /24  
- **2nd Octet**: /9 - /16
- **1st Octet**: /1 - /8

---

## Practical Examples

### Example 1: 154.219.154.180/20

**Step 1**: Identify magic octet
- `/20` falls in 3rd octet range

**Step 2**: Calculate Group Size  
- Network bits in 3rd octet: 4 bits
- Group Size = 2^4 = 16

**Step 3**: Subnet Mask
- 256 - 16 = 240
- Subnet Mask: **255.255.240.0**

**Step 4**: Find Network
- 154 ÷ 16 = 9 remainder 10
- Network base: 9 × 16 = 144
- **Network**: 154.219.144.0

**Step 5**: Calculate Broadcast
- Broadcast base: 144 + 16 - 1 = 159  
- **Broadcast**: 154.219.159.255

**Step 6**: Results
- **Subnets**: 2^4 = 16
- **Total Hosts**: 2^(32-20) - 2 = 4,094

### Example 2: 84.75.21.6/10

**Step 1**: Magic octet = 2nd octet

**Step 2**: Group Size = 2^2 = 64

**Step 3**: Subnet Mask = 255.192.0.0

**Step 4**: Network calculation
- 75 ÷ 64 = 1 remainder 11
- Network base: 1 × 64 = 64
- **Network**: 84.64.0.0

**Step 5**: Broadcast
- **Broadcast**: 84.127.255.255

**Step 6**: Results  
- **Subnets**: 2^2 = 4
- **Total Hosts**: 2^22 - 2 = 4,194,302

---

## Class C Network Subnetting

### Default: /24 (255.255.255.0)

| CIDR | Subnet Mask     | Group Size | Subnets | Hosts per Subnet |
|------|----------------|------------|---------|------------------|
| /25  | 255.255.255.128| 128        | 2       | 126              |
| /26  | 255.255.255.192| 64         | 4       | 62               |
| /27  | 255.255.255.224| 32         | 8       | 30               |
| /28  | 255.255.255.240| 16         | 16      | 14               |
| /29  | 255.255.255.248| 8          | 32      | 6                |
| /30  | 255.255.255.252| 4          | 64      | 2                |

### Example: 192.168.1.0/26
- **4 subnets** of **62 hosts each**
- Subnet 0: 192.168.1.0 - 192.168.1.63
- Subnet 1: 192.168.1.64 - 192.168.1.127  
- Subnet 2: 192.168.1.128 - 192.168.1.191
- Subnet 3: 192.168.1.192 - 192.168.1.255

---

## Class B Network Subnetting  

### Default: /16 (255.255.0.0)

| CIDR | Subnet Mask     | Subnets | Hosts per Subnet |
|------|----------------|---------|------------------|
| /17  | 255.255.128.0  | 2       | 32,766           |
| /18  | 255.255.192.0  | 4       | 16,382           |
| /19  | 255.255.224.0  | 8       | 8,190            |
| /20  | 255.255.240.0  | 16      | 4,094            |
| /21  | 255.255.248.0  | 32      | 2,046            |
| /22  | 255.255.252.0  | 64      | 1,022            |
| /23  | 255.255.254.0  | 128     | 510              |
| /24  | 255.255.255.0  | 256     | 254              |

---

## Key Formulas and Quick Reference

### Essential Formulas:
- **Usable Hosts**: 2^(host bits) - 2
- **Total Addresses**: 2^(host bits)  
- **Number of Subnets**: 2^(borrowed bits)
- **Group Size**: 256 - subnet mask octet

### CIDR to Subnet Mask Quick Reference:
- **/8** = 255.0.0.0
- **/16** = 255.255.0.0  
- **/24** = 255.255.255.0
- **/25** = 255.255.255.128
- **/26** = 255.255.255.192
- **/27** = 255.255.255.224
- **/28** = 255.255.255.240
- **/29** = 255.255.255.248
- **/30** = 255.255.255.252

### Powers of 2 Reference:
- 2^1 = 2
- 2^2 = 4  
- 2^3 = 8
- 2^4 = 16
- 2^5 = 32
- 2^6 = 64
- 2^7 = 128
- 2^8 = 256

### Important Notes:
- **Always subtract 2** from total addresses for usable hosts
- **Network ID** is always the first address
- **Broadcast** is always the last address  
- **/31** subnets have **0 usable hosts** (point-to-point links)
- **/32** is a **host route** (single IP)

---

## CCNA Exam Tips

1. **Memorize the cheat sheet** - it's your fastest tool
2. **Practice the magic octet method** for complex subnets
3. **Know powers of 2** up to 2^16
4. **Remember the -2 rule** for usable addresses
5. **Understand VLSM** (Variable Length Subnet Masking)
6. **Practice with different classes** - don't just focus on Class C