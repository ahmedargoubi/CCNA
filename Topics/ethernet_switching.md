# CCNA Study Guide – Analyzing Ethernet LAN Switching

## 1. Introduction
Ethernet LAN switches form the backbone of most local area networks. Their primary role is to forward frames between devices based on **MAC addresses**. Understanding how switches learn, store, and use MAC addresses is critical for both real-world troubleshooting and CCNA exam success.

---

## 2. L2 vs. L3 Switches

### 2.1 Layer 2 Switch
- Operates at the **Data Link layer (Layer 2)** of the OSI model.
- Uses **MAC addresses** to make forwarding decisions.
- Does **not** look at Layer 3 (IP) addresses for switching.
- Typical use: connecting devices within the same VLAN.

### 2.2 Layer 3 Switch
- Combines **switching (Layer 2)** and **routing (Layer 3)** capabilities.
- Can perform **inter-VLAN routing** without an external router.
- Uses **IP routing tables** in addition to MAC tables.
- Typical use: distribution layer in enterprise networks.

---

## 3. Switching Concepts

### 3.1 MAC Learning
**Process:**
1. When a frame arrives on a port, the switch checks the **source MAC address**.
2. If the MAC address is **not in the table**, it is added and associated with that incoming port.
3. If it **already exists** but on a different port, the switch updates the entry to the new port (host moved).
4. This process builds the **MAC Address Table** dynamically.

**Example:**
| MAC Address       | Port |
|-------------------|------|
| 00:1A:2B:3C:4D:5E | Fa0/1|
| 00:1B:3D:4E:5F:6A | Fa0/3|

---

### 3.2 MAC Aging
- MAC address entries are **temporary** (default: 300 seconds on Cisco switches).
- If no frames are seen from a MAC address within the aging timer:
  - The entry is **removed** from the MAC table.
- Prevents stale information from causing incorrect forwarding.

---

### 3.3 Frame Switching
Switches forward frames based on **destination MAC address**:

#### Steps:
1. Frame arrives at the switch.
2. Switch looks at **destination MAC**:
   - **If found** in MAC table → forward out the associated port.
   - **If not found** → frame is **flooded** (see next section).
3. Source MAC is learned (as explained above).

---

### 3.4 Frame Flooding
Occurs when:
- Destination MAC is **unknown** (not in MAC table).
- Frame is a **broadcast** (FF:FF:FF:FF:FF:FF).
- Frame is a **multicast** (unless multicast snooping is enabled).

**Behavior**: Switch sends the frame out **all ports in the same VLAN**, except the one it arrived on.

---

## 4. MAC Address Table

### 4.1 Structure
The MAC table maps MAC addresses to switch ports and VLANs:
```plaintext
Switch# show mac address-table
Vlan    MAC Address       Type        Ports
1       00:1A:2B:3C:4D:5E DYNAMIC     Fa0/1
1       00:1B:3D:4E:5F:6A DYNAMIC     Fa0/3
```

- **VLAN**: Identifies the VLAN in which the MAC address is valid.
- **Type**:
  - **Dynamic**: Learned automatically.
  - **Static**: Manually configured and never aged out.
- **Port**: The physical/logical port for the device.

---

## 5. Troubleshooting & Commands

### 5.1 Useful Commands (Cisco IOS)
```
show mac address-table       ! View learned MAC addresses
clear mac address-table      ! Clear all dynamic entries
```

### 5.2 Common Issues
- **MAC flapping**: Same MAC appears on multiple ports repeatedly.
  - Cause: Loops or misconfigured port channels.
- **Aging misconfiguration**: Too short → table constantly relearns; too long → stale entries.

---

## 6. Exam Tips
- MAC Learning happens from the **source MAC**, not the destination.
- Unknown unicast frames are **flooded**, not dropped.
- Broadcast frames are sent to **all ports in the VLAN** except incoming port.
- Layer 3 switches can route between VLANs without an external router.
- Be careful with aging timers in questions—Cisco might try to trick you.

---

## 7. Summary Table
| Concept | Description | Exam Tip |
|---------|-------------|-----------|
| MAC Learning | Adds source MAC to table when a frame is received | Learned from source, not destination |
| MAC Aging | Removes MAC after inactivity timer expires | Default 300s on Cisco |
| Frame Switching | Forwards frames based on destination MAC lookup | If found, forward; if not, flood |
| Frame Flooding | Sends unknown unicast, broadcast, or certain multicast to all ports | Exclude incoming port |
| L2 vs L3 Switch | L2 uses MAC, L3 uses IP + MAC | L3 switch = router + switch |

---

## 8. Example Exam Scenario

**Question:**
A switch receives a frame on Fa0/5 with source MAC 00:AA:BB:CC:DD:01 and destination MAC 00:11:22:33:44:55. The MAC table has:

```
Vlan 1    00:11:22:33:44:55   Fa0/3
```

What happens?

**Answer:**
1. The source MAC (00:AA:BB:CC:DD:01) is added to the table mapped to Fa0/5.
2. The destination MAC is found on Fa0/3 → frame is forwarded out Fa0/3 only.
