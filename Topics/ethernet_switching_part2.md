# CCNA Study Guide – Ethernet LAN Switching Part 2

## 1. Ethernet Frame Structure

An **Ethernet Frame** consists of three main parts:
```
[Ethernet Header] --- [DATA (Packet)] --- [Ethernet Trailer]
```

---

## 2. Ethernet Header Fields

The Ethernet Header contains **5 fields**:

| Field | Size | Description |
|-------|------|-------------|
| **Preamble** | 7 bytes | Synchronization pattern |
| **SFD** (Start Frame Delimiter) | 1 byte | Indicates start of frame |
| **Destination MAC** | 6 bytes | Destination MAC address |
| **Source MAC** | 6 bytes | Source MAC address |
| **Type/Length** | 2 bytes | Indicates protocol type or frame length |

**Note**: The Preamble + SFD is not usually considered part of the Ethernet Header.

---

## 3. Ethernet Trailer

The Ethernet Trailer contains **1 field**:
- **FCS** (Frame Check Sequence) = **4 bytes**
- Used for error detection

---

## 4. Frame Size Calculations

### 4.1 Header + Trailer Size
**Ethernet Header + Trailer** = **18 bytes**
- Destination MAC (6) + Source MAC (6) + Type/Length (2) + FCS (4) = 18 bytes

### 4.2 Minimum Frame Size
- **Minimum Ethernet Frame size** = **64 bytes**
- **Minimum DATA payload** = 64 - 18 = **46 bytes**

### 4.3 Padding
- If payload is **less than 46 bytes**, **padding bytes** are added
- Padding consists of a series of **0's**
- Ensures frame meets minimum 64-byte requirement

---

## 5. Address Resolution Protocol (ARP)

### 5.1 ARP Overview
- **ARP** = Address Resolution Protocol
- Used to discover **Layer 2 address (MAC)** of a known **Layer 3 address (IP)**
- Essential for local network communication

### 5.2 ARP Messages
ARP consists of **two message types**:
1. **ARP REQUEST** (Source message)
2. **ARP REPLY** (Destination message)

---

### 5.3 ARP Request
**Characteristics**:
- **Broadcast frame** = sent to all hosts on network (except sender)
- Used when PC needs MAC address for unknown IP

**ARP Request Frame contains**:
- Source IP Address
- Destination IP Address
- Source MAC Address
- **Broadcast MAC Address** = **FF:FF:FF:FF:FF:FF**

---

### 5.4 ARP Reply
**Characteristics**:
- **Unicast frame** = sent only to the host that sent the ARP Request
- Contains the requested MAC address information

**ARP Reply Frame contains**:
- Source IP Address
- Destination IP Address
- Source MAC Address
- **Destination MAC Address** (now known)

---

## 6. PING Utility

### 6.1 PING Overview
- Network utility used to **test reachability**
- **Measures round-trip time**
- Uses **ICMP** (Internet Control Message Protocol)

### 6.2 PING Messages
Uses **two message types**:
1. **ICMP Echo REQUEST**
2. **ICMP Echo REPLY**

### 6.3 PING Characteristics
- **Unicast** communication
- **Command syntax**: `ping <IP_address>`

---

### 6.4 Cisco IOS PING Behavior
**Default settings**:
- Sends **5 ICMP requests/replies**
- Default packet size = **100 bytes**

**Result indicators**:
- **Period (.)** = Failed ping
- **Exclamation mark (!)** = Successful ping

**Example output**:
```
Router# ping 192.168.1.10
!!!!!
Success rate is 100 percent (5/5)
```

---

## 7. Useful Cisco IOS Commands

### 7.1 ARP Table Commands
```bash
PC1# show arp
# Shows host's ARP table
```

### 7.2 MAC Address Table Commands
```bash
SW1# show mac address-table
# Shows the switch's MAC address table
```

**Output format**:
```
Vlan    MAC Address       Type        Ports
1       00:1A:2B:3C:4D:5E DYNAMIC     Fa0/1
1       00:1B:3D:4E:5F:6A DYNAMIC     Fa0/3
```

Where:
- **Vlan** = Virtual Local Area Network ID
- **MAC Address** = Device MAC address
- **Type** = DYNAMIC (learned) or STATIC (configured)
- **Ports** = Physical interface/port

---

### 7.3 Clear MAC Address Table Commands

#### Clear Entire MAC Table
```bash
SW1# clear mac address-table dynamic
# Clears the entire switch's dynamic MAC table
```

#### Clear Specific MAC Address
```bash
SW1# clear mac address-table dynamic address <MAC_address>
# Clears a specific MAC address entry
```

#### Clear by Interface
```bash
SW1# clear mac address-table dynamic interface <interface_name>
# Clears MAC table entries for a specific interface
```

**Example**:
```bash
SW1# clear mac address-table dynamic interface FastEthernet0/1
```

---

## 8. Summary Table

| Concept | Key Points | Commands |
|---------|------------|----------|
| **Ethernet Frame** | Header (18 bytes) + Data (min 46 bytes) + Trailer | N/A |
| **Frame Padding** | Added when payload < 46 bytes | N/A |
| **ARP Request** | Broadcast to find MAC for known IP | `show arp` |
| **ARP Reply** | Unicast response with MAC address | `show arp` |
| **PING** | Tests reachability using ICMP Echo | `ping <IP>` |
| **MAC Table** | Switch's learned MAC-to-port mappings | `show mac address-table` |
| **Clear MAC** | Remove dynamic MAC entries | `clear mac address-table dynamic` |

---

## 9. Exam Tips

- **Remember frame sizes**: 64 bytes minimum, 18 bytes overhead, 46 bytes minimum payload
- **ARP Request** = Broadcast, **ARP Reply** = Unicast
- **Broadcast MAC** = FF:FF:FF:FF:FF:FF (always remember this!)
- **PING symbols**: "." = fail, "!" = success
- **Default PING count** = 5 packets in Cisco IOS
- **Clear commands** are frequently tested - know the syntax variations
