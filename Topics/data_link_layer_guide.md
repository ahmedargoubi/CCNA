# Data Link Layer (Layer 2) - Simple Guide

## Definition

**The Data Link Layer (Layer 2)** is responsible for **node-to-node communication** and ensuring reliable data transfer between directly connected devices on the same network.

Think of it as the "local delivery service" - it makes sure data gets from one device to the next device on the same network segment, using **MAC addresses** to identify devices.

---

## Purpose

The Data Link Layer has **three main purposes**:

### 1. **Framing**
- Packages data from Layer 3 (Network Layer) into **frames**
- Adds headers and trailers with control information
- Frames are the format used for local network transmission

### 2. **Physical Addressing (MAC Addressing)**
- Uses **MAC addresses** to identify source and destination devices
- Ensures frames reach the correct device on the local network
- MAC address = 48-bit hardware address (e.g., `AA:BB:CC:DD:EE:FF`)

### 3. **Error Detection and Control**
- Detects transmission errors using **FCS (Frame Check Sequence)**
- Provides flow control to prevent overwhelming receivers
- Ensures reliable delivery between directly connected devices

**Simple Analogy**: If the Physical Layer is the road, the Data Link Layer is the postal worker who:
- Puts letters in envelopes (framing)
- Writes addresses on envelopes (MAC addressing)
- Checks if letters arrived intact (error detection)

---

## Data Link Sublayers

The Data Link Layer is divided into **TWO sublayers**:

```
┌─────────────────────────────────────┐
│  Network Layer (Layer 3)            │
├─────────────────────────────────────┤
│  LLC (Logical Link Control)         │  ← Upper Sublayer
├─────────────────────────────────────┤
│  MAC (Media Access Control)         │  ← Lower Sublayer
├─────────────────────────────────────┤
│  Physical Layer (Layer 1)           │
└─────────────────────────────────────┘
```

### Why Two Sublayers?

**Separation of concerns:**
- **LLC**: Talks to upper layers (software side)
- **MAC**: Talks to lower layer (hardware side)

This allows different network types (Ethernet, Wi-Fi, etc.) to work with the same upper layer protocols!

---

## 1. LLC Sublayer (Logical Link Control)

### What is LLC?

**LLC** is the **upper portion** of the Data Link Layer that provides an interface between the **Network Layer** and the **MAC sublayer**.

**Defined by**: IEEE 802.2 standard

### Functions of LLC

#### Function 1: Communication with Upper Layers
- **Receives data** from the Network Layer (Layer 3)
- **Passes data** to the MAC sublayer for transmission
- **Shields upper layers** from hardware differences

**Simple explanation**: LLC is like a translator between software (Network Layer) and hardware (MAC sublayer).

#### Function 2: Multiplexing and Demultiplexing
- **Multiplexing**: Combines data from multiple upper layer protocols
- **Demultiplexing**: Identifies which upper layer protocol should receive data
- Uses **SAP (Service Access Point)** identifiers

**Example**: 
```
IPv4 packet → LLC adds identifier → MAC processes → Physical transmission
IPv6 packet → LLC adds identifier → MAC processes → Physical transmission
```

#### Function 3: Flow Control
- **Prevents sender** from overwhelming the receiver
- Manages the rate of data transmission
- Uses techniques like:
  - **Windowing**: Controls how much data can be sent before acknowledgment
  - **Buffering**: Temporary storage of incoming data

#### Function 4: Error Control (Optional)
- Can provide **error checking** and retransmission
- **Acknowledgments**: Confirms data received correctly
- **Retransmission**: Resends data if errors detected

**Note**: Not all LLC implementations provide error control (Ethernet typically doesn't).

### LLC Frame Format

```
┌──────┬──────┬─────────────┐
│ DSAP │ SSAP │   Control   │
└──────┴──────┴─────────────┘
```

- **DSAP (Destination SAP)**: Which protocol to deliver to
- **SSAP (Source SAP)**: Which protocol sent the data
- **Control**: Type of LLC frame and flow control

---

## 2. MAC Sublayer (Media Access Control)

### What is MAC?

**MAC** is the **lower portion** of the Data Link Layer that directly interacts with the **Physical Layer** and controls how devices access the network medium.

**Defined by**: Various IEEE 802 standards (802.3 for Ethernet, 802.11 for Wi-Fi)

### Functions of MAC

#### Function 1: Frame Encapsulation
**Adds MAC header and trailer to data from LLC**

**MAC Frame Structure:**
```
┌──────────┬─────────┬──────────┬──────┬─────┐
│ Preamble │ Dest MAC│ Src MAC  │ Data │ FCS │
└──────────┴─────────┴──────────┴──────┴─────┘
```

Components:
- **Preamble**: Synchronization pattern
- **Destination MAC**: Where the frame is going
- **Source MAC**: Where the frame came from
- **Data**: Payload from upper layers
- **FCS (Frame Check Sequence)**: Error detection code

#### Function 2: Media Access Control
**Controls HOW and WHEN devices can transmit on the network**

This is **THE KEY FUNCTION** that gives MAC its name!

**Two Main Methods:**

**A) Contention-Based (CSMA/CD for Ethernet)**
- **CSMA/CD** = Carrier Sense Multiple Access with Collision Detection
- Devices "listen" before transmitting
- If collision occurs, both devices stop and retry
- Used in traditional Ethernet (hubs)

**Process:**
```
1. Listen to the wire (Carrier Sense)
2. If clear, transmit
3. If busy, wait
4. Detect collisions while transmitting
5. If collision, stop and wait random time
6. Retry transmission
```

**B) Contention-Free (Token Passing, Scheduled)**
- Organized access to the medium
- No collisions because access is controlled
- Examples:
  - **Token Ring**: Device must have "token" to transmit
  - **TDMA**: Each device has assigned time slot

**Modern Ethernet (Switches):**
- Uses **full-duplex** communication
- **No collisions** because dedicated bandwidth per port
- CSMA/CD effectively disabled (not needed)

#### Function 3: Physical Addressing (MAC Addressing)

**MAC Address Characteristics:**
- **48 bits (6 bytes)** long
- Written in hexadecimal: `AA:BB:CC:DD:EE:FF`
- **Globally unique** (in theory)
- **Burned into NIC** (Network Interface Card) at manufacture

**MAC Address Structure:**
```
AA:BB:CC:DD:EE:FF
└──┬──┘ └───┬───┘
   │        │
   OUI      Device ID
(Vendor)   (Unique)
```

- **OUI (Organizationally Unique Identifier)**: First 24 bits (manufacturer)
- **Device Identifier**: Last 24 bits (unique device number)

**Special MAC Addresses:**
- **Broadcast**: `FF:FF:FF:FF:FF:FF` (sent to all devices)
- **Multicast**: First bit = 1 (sent to group of devices)
- **Unicast**: First bit = 0 (sent to single device)

#### Function 4: Error Detection

**Uses FCS (Frame Check Sequence) - also called CRC (Cyclic Redundancy Check)**

**How it works:**
1. **Sender**: Calculates mathematical value based on frame data
2. **Adds FCS** to end of frame (usually 4 bytes)
3. **Receiver**: Recalculates the value from received data
4. **Compares**: If values match = no errors; If different = errors detected

**Important**: 
- MAC sublayer **detects** errors but **does NOT correct** them
- Corrupted frames are **dropped**
- Upper layers (like TCP) handle retransmission if needed

#### Function 5: Frame Delimiting

**Identifies where frames START and END**

**Methods:**
- **Preamble/SFD**: Start of frame delimiter
- **Minimum frame size**: Ensures proper collision detection (64 bytes for Ethernet)
- **Maximum frame size**: Prevents one device monopolizing the medium (1518 bytes for Ethernet)

---

## Providing Access to Media

### How Data Link Layer Controls Network Access

The Data Link Layer determines **WHO can transmit** and **WHEN they can transmit** on a shared medium.

### Access Methods

#### 1. Contention-Based Access (CSMA/CD)

**Used in**: Ethernet with hubs (older networks)

**Characteristics:**
- **All devices share** the same medium
- **Devices compete** for access
- **Collisions can occur**
- **Best effort** - no guarantees

**Advantages:**
- Simple to implement
- Works well with light traffic
- No central coordination needed

**Disadvantages:**
- Performance degrades with heavy traffic
- Collisions waste bandwidth
- Unpredictable access times

**Example Scenario:**
```
Device A: Wants to transmit → Listens → Clear → Sends
Device B: Wants to transmit → Listens → Clear → Sends
COLLISION! → Both stop → Random wait → Retry
```

#### 2. Contention-Free Access (Controlled)

**Used in**: Token Ring, some wireless standards

**Characteristics:**
- **Organized access** to medium
- **No collisions** possible
- **Predictable** transmission times
- **Requires coordination**

**Token Passing Example:**
```
Device A has token → Transmits data → Passes token → Device B
Device B has token → Transmits data → Passes token → Device C
```

#### 3. Full-Duplex (Modern Ethernet)

**Used in**: Ethernet with switches

**Characteristics:**
- **Dedicated connection** per device
- **Simultaneous** send and receive
- **No collisions** (separate transmit/receive paths)
- **Maximum efficiency**

**Why it's better:**
```
Hub (Half-Duplex):     Switch (Full-Duplex):
All devices share      Each device has
same bandwidth         dedicated bandwidth
```

---

## Data Link Layer Standards

### IEEE 802 Standards Family

The **IEEE 802** committee defines standards for LANs (Local Area Networks).

### Common Data Link Layer Standards

#### 1. IEEE 802.3 (Ethernet)

**Most common LAN technology**

**Specifications:**
- **10BASE-T**: 10 Mbps over twisted pair
- **100BASE-TX**: 100 Mbps (Fast Ethernet)
- **1000BASE-T**: 1 Gbps (Gigabit Ethernet)
- **10GBASE-T**: 10 Gbps

**Frame Format:**
```
┌──────────┬──────┬─────┬────┬──────┬─────┐
│ Preamble │ Dest │ Src │Type│ Data │ FCS │
│  8 bytes │6 byte│6 byt│2 by│46-1500│4 by│
└──────────┴──────┴─────┴────┴──────┴─────┘
```

**Key Features:**
- Uses CSMA/CD (historically)
- MAC addressing
- CRC error detection
- Minimum frame: 64 bytes
- Maximum frame: 1518 bytes

#### 2. IEEE 802.11 (Wi-Fi / Wireless LAN)

**Wireless networking standard**

**Specifications:**
- **802.11a**: 54 Mbps (5 GHz)
- **802.11b**: 11 Mbps (2.4 GHz)
- **802.11g**: 54 Mbps (2.4 GHz)
- **802.11n**: 600 Mbps (2.4/5 GHz)
- **802.11ac**: 6.9 Gbps (5 GHz)
- **802.11ax (Wi-Fi 6)**: 9.6 Gbps

**Access Method**: **CSMA/CA** (Collision Avoidance)
- Cannot detect collisions in wireless
- Uses **RTS/CTS** (Request to Send/Clear to Send)
- Acknowledgment-based transmission

#### 3. IEEE 802.2 (LLC)

**Logical Link Control standard**

**Provides:**
- Interface to Network Layer
- Protocol identification
- Optional flow and error control

**Used by**: All 802 standards (802.3, 802.11, etc.)

#### 4. IEEE 802.1Q (VLAN Tagging)

**Virtual LAN standard**

**Adds 4-byte tag to Ethernet frame:**
```
┌──────┬─────┬────────┬─────┬──────┬─────┐
│ Dest │ Src │802.1Q  │Type │ Data │ FCS │
│  MAC │ MAC │Tag(4B) │     │      │     │
└──────┴─────┴────────┴─────┴──────┴─────┘
```

**VLAN Tag contains:**
- VLAN ID (12 bits) = 4096 possible VLANs
- Priority (3 bits) = QoS information

---

## Quick Comparison: LLC vs MAC

| Aspect | LLC Sublayer | MAC Sublayer |
|--------|--------------|--------------|
| **Position** | Upper Data Link | Lower Data Link |
| **Standard** | IEEE 802.2 | IEEE 802.3, 802.11, etc. |
| **Purpose** | Interface with Network Layer | Interface with Physical Layer |
| **Key Function** | Protocol multiplexing | Media access control |
| **Addressing** | SAP (Service Access Points) | MAC addresses |
| **Hardware/Software** | Software-oriented | Hardware-oriented |
| **Variability** | Same across technologies | Varies by media type |

---

## Real-World Example

**Sending data from your PC to another PC on the same network:**

### Step-by-Step Process

1. **Network Layer**: Creates IP packet with IP addresses
2. **LLC Sublayer**: 
   - Receives packet from Network Layer
   - Identifies protocol type (IPv4)
   - Passes to MAC sublayer
3. **MAC Sublayer**:
   - Adds source MAC address (your PC)
   - Adds destination MAC address (other PC)
   - Adds FCS for error detection
   - Creates complete Ethernet frame
4. **Media Access Control**:
   - Checks if medium is free (CSMA/CD or full-duplex)
   - Transmits frame
5. **Physical Layer**: Converts frame to signals
6. **Destination PC**:
   - Physical Layer receives signals
   - MAC checks destination MAC address (match!)
   - MAC verifies FCS (no errors!)
   - MAC removes header/trailer
   - LLC identifies protocol (IPv4)
   - Passes data to Network Layer

---

## Summary Table

| Component | Purpose | Key Points |
|-----------|---------|------------|
| **Data Link Layer** | Node-to-node delivery | Uses MAC addresses, creates frames |
| **LLC Sublayer** | Upper layer interface | Protocol ID, flow control, multiplexing |
| **MAC Sublayer** | Media access & addressing | Controls when to transmit, MAC addresses |
| **CSMA/CD** | Contention-based access | Listen before transmit, detect collisions |
| **MAC Address** | Physical addressing | 48-bit, globally unique, burned into NIC |
| **Frame** | Data Link PDU | Header + Data + Trailer with FCS |
| **IEEE 802.3** | Ethernet standard | Most common wired LAN |
| **IEEE 802.11** | Wi-Fi standard | Wireless LAN |

---

## Remember This!

✅ **Data Link Layer = Layer 2 = MAC Address Layer**  
✅ **Two sublayers: LLC (upper) and MAC (lower)**  
✅ **LLC talks to Network Layer, MAC talks to Physical Layer**  
✅ **MAC address = 48 bits = Physical address**  
✅ **MAC sublayer controls WHEN devices transmit**  
✅ **FCS detects errors but doesn't correct them**  
✅ **Ethernet = IEEE 802.3 standard**  
✅ **Wi-Fi = IEEE 802.11 standard**  
✅ **Frame = Data Link Layer PDU (Protocol Data Unit)**