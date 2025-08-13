# CCNA Study Guide – OSI Model & TCP/IP Suite

## 1. What is a Networking Model?
- A **networking model** organizes and standardizes networking protocols and functions.
- **Protocols** = rules for how network devices and software communicate.
- Two main models:
  - **OSI Model** (theoretical reference)
  - **TCP/IP Model** (practical, used today)

---

## 2. OSI Model Overview
**OSI = Open Systems Interconnection**  
- Developed by ISO (International Organization for Standardization).
- Conceptual framework with **7 layers**.
- Helps standardize networking terminology and troubleshooting.

### Mnemonics:
- **From top to bottom**: *All People Seem To Need Data Processing*
- **From bottom to top**: *Please Do Not Throw Sausage Pizza Away*

---

## 3. OSI Model Layers (Top → Bottom)

### **Layer 7 – Application**
- Closest to the user.
- Interfaces with software applications.
- Examples: **HTTP, HTTPS, FTP, SMTP**.
- Functions:
  - Identify communication partners.
  - Synchronize communication.

### **Layer 6 – Presentation**
- Translates data between application and network formats.
- Handles **encryption, compression, and formatting**.

### **Layer 5 – Session**
- Manages and controls **sessions** between applications.
- Establishes, maintains, and terminates connections.

*(Layers 5–7 are typically handled by application developers, not network engineers.)*

---

### **Layer 4 – Transport**
- Provides **host-to-host** (end-to-end) communication.
- Segments large data into smaller units.
- Adds **Layer 4 header**, creating a **Segment**.
- Common protocols: **TCP** (reliable) and **UDP** (faster, unreliable).

---

### **Layer 3 – Network**
- Provides **connectivity between different networks**.
- Assigns **logical addressing** (IP addresses).
- Chooses best path (routing).
- Adds **Layer 3 header**, creating a **Packet**.
- Device: **Router** operates here.

---

### **Layer 2 – Data Link**
- Provides **node-to-node** connectivity.
- Formats data for transmission.
- Uses **MAC addresses**.
- Adds **Layer 2 header + trailer**, creating a **Frame**.
- Device: **Switch** operates here.

---

### **Layer 1 – Physical**
- Defines **physical characteristics** (voltages, cables, connectors).
- Transmits bits as electrical, light, or radio signals.
- Deals with **0s and 1s** only.

---

## 4. OSI PDUs (Protocol Data Units)

| OSI Layer(s) | PDU Name | Data Added                        |
|--------------|----------|-----------------------------------|
| 7–5          | Data     | Application-level data            |
| 4            | Segment  | Layer 4 Header                    |
| 3            | Packet   | Layer 3 Header                    |
| 2            | Frame    | Layer 2 Header + Trailer          |
| 1            | Bit      | Electrical/Optical/Wireless signal|

**Encapsulation**: Moving downward through the layers (adding headers/trailers).  
**De-encapsulation**: Moving upward through the layers (removing headers/trailers).

---

## 5. TCP/IP Model Overview
- Developed by **DARPA** (US DoD) for ARPANET.
- **Practical model** in use today.
- Named after core protocols: **TCP** & **IP**.
- Has **4 layers**, mapping to OSI as follows:

| TCP/IP Layer        | Corresponding OSI Layers      | Example Protocols       |
|---------------------|------------------------------|-------------------------|
| Application         | 7–5 (Application, Presentation, Session) | HTTP, FTP, DNS |
| Transport           | 4 (Transport)                | TCP, UDP                |
| Internet            | 3 (Network)                  | IP, ICMP, ARP           |
| Network Access      | 2–1 (Data Link, Physical)    | Ethernet, Wi-Fi         |

---

## 6. Layer Interactions

### **Adjacent-Layer Interaction**
- Communication between layers **on the same device**.
- Example: Layer 5 sends data to Layer 4, which adds a header.

### **Same-Layer Interaction**
- Communication between **same layers on different devices**.
- Example: Application layer of a web browser ↔ Application layer of a web server.

---

## 7. Exam Tips
- Know **layer order** and **functions**.
- Match **devices** to layers:  
  - Router = Layer 3  
  - Switch = Layer 2  
  - Hub = Layer 1
- Understand **encapsulation & de-encapsulation**.
- TCP/IP model is **practical**, OSI model is **theoretical**.

