# CCNA Study Guide – Network Devices

## 1. What is a Network?
A **network** is a digital telecommunications system that allows **nodes** (devices) to share resources and exchange data.

### Key Terms
- **Client**: A device that accesses a service provided by another device.
- **Server**: A device that provides functions or services for clients.
- **Peer-to-Peer (P2P)**: A network where devices can act as both clients and servers depending on the situation.

---

## 2. Common Networking Devices

### 2.1 Switches (Layer 2)
**Purpose**: Connect end hosts within the same **LAN** (Local Area Network).

**Key Points for CCNA**:
- Operate at **Layer 2** of the OSI model (Data Link Layer).
- Forward frames based on **MAC addresses**.
- Have multiple ports for host connectivity.
- **Do not** connect different networks (LAN to LAN).
- Support **VLANs** for segmenting broadcast domains.

**Example**: Connecting PCs, printers, and access points within the same office network.

---

### 2.2 Routers (Layer 3)
**Purpose**: Connect **different networks** and direct traffic between them.

**Key Points for CCNA**:
- Operate at **Layer 3** of the OSI model (Network Layer).
- Forward packets based on **IP addresses**.
- Often have fewer interfaces than switches.
- Provide connectivity between LANs and to the Internet.
- Perform **routing** using static routes or dynamic routing protocols (OSPF, EIGRP, RIP).

**Example**: Connecting an office LAN to the ISP for Internet access.

---

### 2.3 Firewalls (Layer 3, 4, and 7)
**Purpose**: Control and filter network traffic for security purposes.

**Key Points for CCNA**:
- Can be **hardware** devices or **software** applications.
- Can operate at multiple OSI layers:
  - **Layer 3**: Filter by IP address.
  - **Layer 4**: Filter by TCP/UDP port numbers.
  - **Layer 7**: Inspect application traffic (HTTP, DNS, etc.).
- Types:
  - **Network-based Firewall**: Protects entire networks.
  - **Host-based Firewall**: Installed on individual devices.
- **Next-Generation Firewalls (NGFW)** offer advanced features:
  - Application awareness.
  - Intrusion prevention.
  - Deep packet inspection.

**Placement**:
- **Inside firewall**: Protects internal LAN from outside threats.
- **Outside firewall**: Often sits at the network perimeter.

---

## 3. Additional Devices to Know for CCNA

### 3.1 Wireless Access Points (WAPs)
- Provide **wireless connectivity** to devices.
- Connect to a wired LAN via a switch.
- Operate at Layer 2 (data link) but bridge wireless and wired networks.

### 3.2 Modems
- Convert digital signals from a router into analog signals for transmission over mediums such as DSL, cable, or fiber.
- Often integrated into home routers.

### 3.3 Network Interface Cards (NICs)
- Hardware in each host allowing network communication.
- Can be wired (Ethernet) or wireless.

---

## 4. Summary Table

| Device         | OSI Layer | Purpose                                    | Key Function                    |
|----------------|-----------|--------------------------------------------|-----------------------------------|
| Switch         | Layer 2   | Connects devices in the same LAN           | Forwards frames by MAC address   |
| Router         | Layer 3   | Connects different networks                | Routes packets by IP address     |
| Firewall       | L3, L4, L7| Security filtering of traffic              | Applies rules to network traffic |
| WAP            | Layer 2   | Wireless connectivity                     | Bridges wireless and wired LAN   |
| Modem          | Physical  | Converts digital ↔ analog signals          | Internet access over ISP link    |

---

## 5. Exam Tips
- Remember **Layer 2 = Switch, Layer 3 = Router**.
- Firewalls can span **multiple layers** and have evolved into NGFWs.
- In CCNA questions, **switches do not route between LANs** unless they are **Layer 3 switches** (which can perform routing).
- Modems and WAPs are often mentioned in home/SMB network design scenarios.

