# CCNA Study Guide – Interfaces and Cables

## 1. Switch Interfaces
- Switches typically have **multiple Ethernet ports** (often 24 or 48).
- Most ports use **RJ-45 connectors**.
- Ports can support different speeds depending on the Ethernet standard.

---

## 2. Ethernet Overview
**Ethernet**: A collection of networking protocols and standards that define:
- How data is transmitted.
- Common **hardware** and **communication standards**.

**Standards Body**: IEEE (Institute of Electrical and Electronics Engineers)  
- Ethernet defined in **IEEE 802.3** (1983).

---

## 3. Ethernet Speeds and Standards (Copper)
| Speed     | Common Name       | IEEE Standard | Cable Type | Max Distance |
|-----------|-------------------|--------------|------------|--------------|
| 10 Mbps   | Ethernet          | 802.3i       | 10BASE-T   | 100 m        |
| 100 Mbps  | Fast Ethernet     | 802.3u       | 100BASE-T  | 100 m        |
| 1 Gbps    | Gigabit Ethernet  | 802.3ab      | 1000BASE-T | 100 m        |
| 10 Gbps   | 10 Gigabit Ethernet| 802.3an     | 10GBASE-T  | 100 m        |

**Notes**:
- **BASE** = Baseband signaling.
- **T** = Twisted Pair (usually UTP).

---

## 4. UTP Cabling
**UTP (Unshielded Twisted Pair)**:
- No metallic shielding.
- Twists reduce **EMI** (Electromagnetic Interference).
- Most Ethernet cables have **8 wires (4 pairs)**.

**Usage**:
- 10/100BASE-T → 2 pairs (4 wires).
- 1000BASE-T / 10GBASE-T → 4 pairs (8 wires), bidirectional.

---

## 5. Pin Assignments and Duplex
**RJ-45 Pins**:
- **PC**: TX on pins 1–2, RX on pins 3–6.
- **Switch**: RX on pins 1–2, TX on pins 3–6.
- This enables **full-duplex** communication.

---

## 6. Cable Types

### 6.1 Straight-Through Cable
- Connects **different device types**:
  - PC ↔ Switch
  - Router ↔ Switch
- Pins match on both ends.

### 6.2 Crossover Cable
- Connects **similar devices**:
  - Switch ↔ Switch
  - PC ↔ PC
  - Router ↔ Router
- Swaps TX and RX pins (1↔3, 2↔6).

### 6.3 Auto MDI-X
- Modern devices detect and adjust pin use automatically.
- Eliminates need for crossover cables in most cases.

---

## 7. Fiber-Optic Connections
**Standard**: IEEE 802.3ae.  
- Uses **SFP transceivers** to connect fiber cables to switches/routers.
- TX and RX are on **separate fibers**.

**Cable Structure**:
1. Core (light transmission).
2. Cladding.
3. Buffer coating.
4. Outer jacket.

---

### 7.1 Single-Mode Fiber (SMF)
- Narrow core, **laser-based** light.
- Very long distances.
- More expensive.

### 7.2 Multi-Mode Fiber (MMF)
- Wider core, **LED-based** light.
- Medium distances.
- Cheaper than single-mode.

---

## 8. Fiber Standards
| Speed     | Standard     | Mode Support       | Max Distance            |
|-----------|-------------|--------------------|-------------------------|
| 1 Gbps    | 1000BASE-LX | Multi / Single     | 550 m (Multi) / 5 km (Single) |
| 10 Gbps   | 10GBASE-SR  | Multi              | 400 m                   |
| 10 Gbps   | 10GBASE-LR  | Single             | 10 km                   |
| 10 Gbps   | 10GBASE-ER  | Single             | 30 km                   |

---

## 9. UTP vs Fiber

| Feature        | UTP                         | Fiber-Optic               |
|----------------|-----------------------------|---------------------------|
| Cost           | Lower                       | Higher                    |
| Max Distance   | ~100 m                      | Up to 30 km               |
| EMI            | Vulnerable                  | Immune                    |
| Security       | Can leak signal              | No leakage                |
| Port Type      | RJ-45 (cheap)               | SFP (more expensive)      |

---

## 10. Exam Tips
- Know the difference between **straight-through** and **crossover** cables.
- Understand **pinouts** and which devices use which pins.
- Remember:  
  - **UTP** = shorter, cheaper, EMI risk.  
  - **Fiber** = longer, more secure, more expensive.
- **Auto MDI-X** is common on modern switches.
- For fiber, **single-mode = long distance**, **multimode = shorter**.

