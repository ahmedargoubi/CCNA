# 📡 SNMP (Simple Network Management Protocol) - CCNA Notes

## 1️⃣ Introduction

- **SNMP** (Simple Network Management Protocol) is used to **monitor and manage network devices** (routers, switches, servers, firewalls, printers, etc.).
- Operates at the **Application Layer (Layer 7)** of the OSI model.
- Allows a **Network Management System (NMS)** to communicate with network devices (agents) to **collect, modify, and monitor** their information.

---

## 2️⃣ SNMP Components

1. **SNMP Manager (NMS):**
   - The central server that polls agents to gather data.
   - Runs management applications that monitor the network.

2. **SNMP Agent:**
   - Software running on network devices (routers, switches, printers, etc.).
   - Responds to queries from the SNMP Manager.

3. **MIB (Management Information Base):**
   - A **database of managed objects** in a device.
   - Organized hierarchically, identified by **Object Identifiers (OIDs)**.

4. **OID (Object Identifier):**
   - A unique identifier for a managed object within the MIB.

---

## 3️⃣ SNMP Versions

- **SNMPv1:** Original version, basic operations, no strong security (community strings in clear text).
- **SNMPv2c:** Adds more operations (bulk retrieval), still uses community strings, no encryption.
- **SNMPv3:** Adds **authentication and encryption** for secure communication (preferred for modern networks).

---

## 4️⃣ SNMP Operations (PDU Types)

| Operation   | Purpose                                                                     |
|-------------|-----------------------------------------------------------------------------|
| **GET**     | Manager requests information from the agent.                               |
| **SET**     | Manager modifies or sets a parameter on the agent.                         |
| **GET-NEXT**| Retrieves the next object in a list/table.                                 |
| **GET-BULK**| Retrieves large amounts of data efficiently (SNMPv2+).                     |
| **TRAP**    | Agent sends an **unsolicited alert** to manager (event notification).      |
| **INFORM**  | Like a trap, but requires acknowledgment (SNMPv2+).                       |
| **RESPONSE**| Agent replies to GET or SET requests.                                      |

---

## 5️⃣ SNMP Communication

- Uses **UDP transport protocol**.
- **Default ports:**
  - **161:** Used by the SNMP Manager to query agents.
  - **162:** Used by agents to send TRAP messages to the Manager.

---

## 6️⃣ SNMP Security

### Community Strings (v1/v2c)
- Function like **passwords** for SNMP access:
  - `public` → Read-only access (view information).
  - `private` → Read-write access (can modify configuration).

### SNMPv3 Security Levels
- **noAuthNoPriv:** No authentication or encryption.
- **authNoPriv:** Authentication, no encryption.
- **authPriv:** Authentication and encryption (most secure).

---

## 7️⃣ SNMP Benefits

- Centralized **network monitoring and troubleshooting**.
- Detects failures, high CPU usage, interface errors, link failures.
- Can automatically trigger **alerts and automated responses**.

---

## 8️⃣ SNMP Limitations

- SNMPv1/v2c are not secure (community strings can be intercepted).
- Polling can generate extra network traffic.
- Misconfigured SNMP access may lead to **information leaks**.

---

## 9️⃣ Basic SNMP Commands on Cisco IOS

| Command                                       | Description                          |
|-----------------------------------------------|--------------------------------------|
| `snmp-server community public RO`             | Enables SNMP read-only community     |
| `snmp-server community private RW`            | Enables SNMP read-write community    |
| `snmp-server location HQ`                     | Sets device location                 |
| `snmp-server contact admin@example.com`       | Sets admin contact                   |
| `show snmp`                                   | Displays SNMP configuration/status   |

---

## ✅ CCNA Key Takeaways

- SNMP is a **monitoring and management protocol** for network devices.
- **Ports:** 161 (queries), 162 (traps).
- **Versions:** v1, v2c (no encryption), v3 (secure).
- Uses **UDP** for transport.
- Community strings act as simple passwords (use SNMPv3 for secure networks).
- SNMP **TRAPS** are alerts sent by agents to managers without being polled.

---

## 📝 Related Quiz
- [SNMP Quiz](./quiz/SNMP_Quiz.md)

---
