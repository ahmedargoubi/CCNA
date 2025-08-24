# IPv4 Header - Complete CCNA Study Guide

## Overview

The **IPv4 Header** is a Layer 3 (Network Layer) protocol data unit that enables **routing** - sending data between devices on separate networks across the Internet.

### Key Functions
- **Encapsulates** TCP or UDP segments from Layer 4
- Contains **addressing information** (source and destination IP addresses)
- Provides **routing information** for packet forwarding
- Includes **error detection** mechanisms
- Supports **fragmentation** for large packets

### OSI Model Context
```
Layer 4 (Transport): TCP/UDP Segment
Layer 3 (Network):   IPv4 Header + Data ← We are here
Layer 2 (Data Link): Ethernet Frame
Layer 1 (Physical):  Electrical signals
```

---

## IPv4 Header Structure

The IPv4 header is **20-60 bytes** in length and contains **11 mandatory fields** plus an optional Options field.

### Header Layout
```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version|  IHL  |    DSCP   |ECN|          Total Length         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Identification        |Flags|      Fragment Offset    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Time to Live |    Protocol   |         Header Checksum       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                       Source Address                          |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Destination Address                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options (0-320 bits)                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

---

## Detailed Field Analysis

### 1. Version Field
- **Length**: 4 bits
- **Purpose**: Identifies the IP version being used
- **Values**:
  - **IPv4**: 0100 (binary) = 4 (decimal)
  - **IPv6**: 0110 (binary) = 6 (decimal)

**CCNA Note**: This field allows routers to process IPv4 and IPv6 packets differently.

### 2. Internet Header Length (IHL)
- **Length**: 4 bits
- **Purpose**: Specifies the IPv4 header length in **4-byte increments**
- **Range**: 
  - **Minimum**: 5 (5 × 4 = 20 bytes) - no Options field
  - **Maximum**: 15 (15 × 4 = 60 bytes) - maximum Options field
- **Why needed**: The Options field is variable length

**Key Values to Remember**:
- **Minimum IPv4 header**: 20 bytes
- **Maximum IPv4 header**: 60 bytes

### 3. DSCP (Differentiated Services Code Point)
- **Length**: 6 bits
- **Purpose**: **Quality of Service (QoS)** marking
- **Function**: Prioritizes delay-sensitive traffic
- **Use cases**: VoIP, video streaming, critical applications
- **Range**: 0-63 (64 possible values)

**CCNA Exam Tip**: DSCP is the modern QoS marking method, replacing the old ToS field.

### 4. ECN (Explicit Congestion Notification)
- **Length**: 2 bits
- **Purpose**: Indicates network congestion without dropping packets
- **Requirements**: Must be supported by both endpoints and network infrastructure
- **Benefit**: Reduces packet loss during congestion

### 5. Total Length
- **Length**: 16 bits
- **Purpose**: Indicates total packet size (header + data)
- **Units**: Measured in **bytes** (not 4-byte increments like IHL)
- **Range**:
  - **Minimum**: 20 bytes (header only, no data)
  - **Maximum**: 65,535 bytes (2^16 - 1)

**Calculation**: Total Length = IPv4 Header + Layer 4 Segment

### 6. Identification
- **Length**: 16 bits
- **Purpose**: Identifies fragments belonging to the same original packet
- **Function**: All fragments of a packet share the same Identification value
- **Use case**: Required when packets exceed MTU (Maximum Transmission Unit)
- **Common MTU**: 1500 bytes (Ethernet frame limit)

### 7. Flags Field
- **Length**: 3 bits
- **Purpose**: Controls packet fragmentation

#### Flag Bits:
- **Bit 0**: Reserved (always 0)
- **Bit 1**: **Don't Fragment (DF)** 
  - 1 = Don't fragment this packet
  - 0 = Fragmentation allowed
- **Bit 2**: **More Fragments (MF)**
  - 1 = More fragments follow
  - 0 = Last fragment or no fragmentation

### 8. Fragment Offset
- **Length**: 13 bits
- **Purpose**: Indicates the position of this fragment in the original packet
- **Units**: Measured in **8-byte blocks**
- **Function**: Allows reassembly even if fragments arrive out of order
- **Range**: 0-8191 (2^13 - 1)

### 9. Time To Live (TTL)
- **Length**: 8 bits
- **Purpose**: Prevents infinite routing loops
- **Function**: Decremented by 1 at each router
- **Action**: Packet dropped when TTL reaches 0
- **Default**: Recommended value is **64**
- **Range**: 0-255

**Important**: Originally designed for seconds, now used as hop count.

### 10. Protocol Field
- **Length**: 8 bits
- **Purpose**: Identifies the Layer 4 protocol
- **Key Values to Memorize**:
  - **1**: ICMP
  - **6**: TCP
  - **17**: UDP
  - **89**: OSPF

**CCNA Exam Note**: These protocol numbers are frequently tested.

### 11. Header Checksum
- **Length**: 16 bits
- **Purpose**: Error detection for **IPv4 header only**
- **Process**:
  1. Sender calculates checksum
  2. Receiver recalculates and compares
  3. If mismatch, packet is dropped
- **Scope**: Only validates header, not data payload
- **Note**: TCP/UDP have their own checksums for data validation

### 12. Source Address
- **Length**: 32 bits
- **Purpose**: IPv4 address of the packet sender
- **Format**: Dotted decimal notation (e.g., 192.168.1.10)

### 13. Destination Address
- **Length**: 32 bits  
- **Purpose**: IPv4 address of the intended receiver
- **Format**: Dotted decimal notation (e.g., 10.0.0.1)

### 14. Options Field
- **Length**: 0-320 bits (variable)
- **Purpose**: Additional optional features
- **Usage**: Rarely used in modern networks
- **Detection**: If IHL > 5, Options field is present
- **Padding**: May require padding to align to 32-bit boundary

---

## Fragmentation Process

### When Fragmentation Occurs
- Packet size exceeds **MTU** of outgoing interface
- Common MTU sizes:
  - **Ethernet**: 1500 bytes
  - **PPPoE**: 1492 bytes

### Fragmentation Fields Working Together

#### Example: 3000-byte packet fragmented into 3 pieces
| Fragment | Identification | Flags (MF) | Fragment Offset | Size |
|----------|----------------|------------|-----------------|------|
| 1st      | 12345          | 1 (more)   | 0               | 1500 |
| 2nd      | 12345          | 1 (more)   | 185             | 1500 |
| 3rd      | 12345          | 0 (last)   | 370             | 20   |

**Note**: Fragment Offset is in 8-byte units (1500 ÷ 8 = 187.5 ≈ 185)

---

## Key CCNA Concepts

### Header Size Calculations
```
Minimum Header: IHL = 5 → 5 × 4 = 20 bytes
Maximum Header: IHL = 15 → 15 × 4 = 60 bytes
Options Length: (IHL × 4) - 20 bytes
```

### Common Protocol Numbers
- **ICMP**: 1 (ping, traceroute)
- **TCP**: 6 (HTTP, HTTPS, SSH, Telnet)  
- **UDP**: 17 (DNS, DHCP, TFTP)
- **OSPF**: 89 (routing protocol)

### TTL Behavior
```
Original TTL: 64
After Router 1: 63
After Router 2: 62
...
TTL = 0: Packet dropped, ICMP Time Exceeded sent
```

### QoS Marking
- **DSCP**: 6 bits = 64 possible values (0-63)
- **Common values**:
  - 0: Best effort
  - 46: Expedited Forwarding (voice)
  - 34: Assured Forwarding (video)

---

## Troubleshooting with IPv4 Header

### Common Issues
1. **Fragmentation problems**: Check MTU sizes
2. **TTL expiration**: Routing loops or very long paths  
3. **Checksum errors**: Corrupted headers
4. **Protocol mismatches**: Wrong Layer 4 protocol

### Useful Commands
```cisco
# Show MTU of interface
show interfaces [interface]

# Show IP protocol statistics
show ip traffic

# Debug IP packets (use carefully)
debug ip packet
```

---

## CCNA Exam Essentials

### Must Memorize
- **Header size range**: 20-60 bytes
- **IHL range**: 5-15 (×4 bytes each)
- **Protocol numbers**: ICMP=1, TCP=6, UDP=17, OSPF=89
- **TTL purpose**: Prevents loops, decremented per hop
- **Default TTL**: 64
- **Maximum Total Length**: 65,535 bytes

### Key Concepts
- **Fragmentation**: ID field groups fragments
- **QoS**: DSCP field for traffic prioritization  
- **Error detection**: Checksum validates header only
- **Addressing**: 32-bit source and destination addresses
- **Options**: Variable field increases header size

### Common Exam Questions
- Calculate header length from IHL value
- Identify Layer 4 protocol from Protocol field
- Understand fragmentation flag meanings
- Know when packets get fragmented (MTU)
- Recognize QoS markings in DSCP field

This comprehensive understanding of the IPv4 header will serve you well in both CCNA exam scenarios and real-world network troubleshooting.