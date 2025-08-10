
# CCNA Spanning Tree Protocols – Detailed Guide

## 1. Introduction

Spanning Tree Protocol (STP) and its variations are **Layer 2 protocols** used to prevent **loops** in Ethernet networks that have **redundant paths**.

Loops can cause:
- **Broadcast storms**
- **MAC table instability**
- **Multiple frame copies**
  
STP ensures a **loop-free topology** by **blocking** certain redundant paths while keeping them ready for backup if the active path fails.

---

## 2. Spanning Tree Protocol (STP)

**Standard:** IEEE 802.1D  
**Invented by:** Dr. Radia Perlman  

### 2.1 STP Purpose
- Detects loops
- Blocks redundant paths
- Reactivates a blocked path if the active link fails

---

### 2.2 How STP Works – Core Concepts

STP uses **Bridge Protocol Data Units (BPDUs)** to share information between switches and decide which ports to block or forward.

#### Key Roles:
1. **Root Bridge**
   - The central reference point for STP
   - Elected based on **Bridge ID** (priority + MAC address)
   - Lowest Bridge ID wins

2. **Root Port (RP)**
   - On each non-root switch
   - The port with the **lowest path cost** to the Root Bridge

3. **Designated Port (DP)**
   - Forwards frames **toward the Root Bridge** for each network segment
   - One DP per segment

4. **Blocked Port**
   - Not forwarding frames
   - Prevents loops

---

### 2.3 STP Election Process

1. **Root Bridge Election**
   - Each switch sends its **Bridge ID**
   - The lowest Bridge ID becomes the Root Bridge

   **Bridge ID Format:**
   ```
   Bridge Priority (2 bytes) + MAC Address (6 bytes)
   ```
   Default priority = **32768**

   Example:
   - SW1: 32768 + MAC AABB.CCDD.EEFF
   - SW2: 32768 + MAC AABB.CCDD.EEEE  
   → SW2 wins because MAC is lower

---

2. **Root Port Selection**
   - On non-root switches, choose the port with **lowest path cost** to Root Bridge
   - Path cost depends on link speed:

| Speed      | STP Cost (802.1D Default) |
|------------|----------------------------|
| 10 Mbps    | 100                        |
| 100 Mbps   | 19                         |
| 1 Gbps     | 4                          |
| 10 Gbps    | 2                          |

---

3. **Designated Port Selection**
   - On each segment, the port **closest to the Root Bridge** becomes Designated Port
   - Other ports in that segment are set to **blocking** if not chosen

---

### 2.4 STP Port States

**802.1D STP** has 5 port states:

1. **Disabled** – Port is administratively down
2. **Blocking** – No user data sent/received; only listens to BPDUs
3. **Listening** – Participating in STP, no user data sent yet
4. **Learning** – Learns MAC addresses but no user data sent
5. **Forwarding** – Forwards frames and learns MAC addresses

---

### 2.5 STP Timers (Default)

| Timer Name   | Default Value | Function                                    |
|--------------|--------------|---------------------------------------------|
| Hello Time   | 2 sec        | Interval between BPDU transmissions         |
| Forward Delay| 15 sec       | Listening/Learning state duration each      |
| Max Age      | 20 sec       | BPDU aging time before assuming link failure|

**Convergence Time** for 802.1D STP: ~30–50 seconds

---

## 3. Rapid Spanning Tree Protocol (RSTP)

**Standard:** IEEE 802.1w  
**Purpose:** Faster convergence than STP (1–10 seconds)

### Improvements over STP:
- Merges Listening & Learning into one **discarding** state
- Faster root/designated port election
- Immediate transition to forwarding for **edge ports** (like PortFast)
- Supports link types:
  - **Point-to-point** – full-duplex, fast transitions
  - **Shared** – slower transitions
  - **Edge** – immediate forwarding

---

## 4. PVST+ (Per VLAN Spanning Tree Plus)

**Vendor-specific:** Cisco  
**Operation:**
- Runs **one STP instance per VLAN**
- Allows load balancing by having different VLANs use different root bridges
- Uses Cisco proprietary BPDUs (Ethernet multicast 0100.0CCC.CCCD)

---

## 5. Rapid PVST+ (RPVST+)

**Cisco proprietary**, based on **RSTP**
- One **RSTP instance per VLAN**
- Faster convergence + VLAN-specific STP topologies
- Common in Cisco environments with VLAN-based design

---

## 6. Multiple Spanning Tree Protocol (MSTP)

**Standard:** IEEE 802.1s  
**Purpose:**
- Map **multiple VLANs** into **fewer STP instances**
- Reduces CPU and memory load compared to PVST+
- Allows load balancing across instances

**Key Terms:**
- **MST Region** – Set of switches with same MST configuration
- **MST Instance (MSTI)** – Runs a version of RSTP
- VLANs are mapped to MSTIs

---

## 7. Comparison Table

| Feature      | STP (802.1D) | RSTP (802.1w) | PVST+ | RPVST+ | MSTP (802.1s) |
|--------------|-------------|--------------|-------|--------|---------------|
| Convergence  | 30–50 sec   | 1–10 sec     | 30–50 sec | 1–10 sec | 1–10 sec |
| VLAN Support | Single inst.| Single inst. | Per VLAN | Per VLAN | Multiple VLANs per instance |
| Standard     | IEEE        | IEEE         | Cisco  | Cisco   | IEEE         |
| BPDU Type    | Config BPDU | RST BPDU     | Cisco  | Cisco   | RST BPDU    |
| Load Balance | No          | No           | Yes    | Yes     | Yes         |

---

## 8. Exam Tips for CCNA

- **STP Root Bridge** → lowest Bridge ID (priority first, then MAC)
- **Default priority:** 32768, increments of 4096
- **Port cost** → lower is better (use link speed table)
- RSTP converges faster due to **proposal/agreement** mechanism
- PVST+/RPVST+ → Cisco only, runs per VLAN
- MSTP → groups VLANs into instances for efficiency
- Remember default STP timers and states
- PortFast → immediate forwarding for access ports (bypasses STP delay)
- BPDU Guard → disables port if BPDU received on PortFast-enabled port

---
