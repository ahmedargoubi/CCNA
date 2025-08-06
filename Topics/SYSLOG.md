# 📝 SYSLOG - CCNA Notes

## 1️⃣ Introduction

- **Syslog** is a **standard protocol used to collect and store log messages** from network devices (routers, switches, firewalls, servers).
- Helps in **monitoring, troubleshooting, and maintaining** a network by reporting events like:
  - Device reboots
  - Configuration changes
  - Interface up/down
  - Security alerts
  - Debugging information

---

## 2️⃣ How Syslog Works

- Devices (known as **syslog clients**) generate **log messages**.
- Messages are sent to:
  - **Local buffer:** Stored temporarily in device memory (lost after reboot).
  - **Console:** Displayed directly on the device console.
  - **Terminal lines (vty):** Displayed on remote sessions.
  - **Syslog server:** Sent over the network to a central **Syslog collector** for long-term storage and analysis.

- **Transport Protocol:**  
  - Uses **UDP port 514** (default).  
  - TCP can also be used but is less common in Cisco IOS.

---

## 3️⃣ Syslog Message Format

A syslog message has:
- **Timestamp:** When the event occurred.
- **Facility:** The process or service that generated the message (e.g., kernel, interface, security).
- **Severity level:** Indicates the importance of the message.
- **Message text:** Description of the event.

Example:
*Mar 1 00:00:22.637: %LINK-3-UPDOWN: Interface FastEthernet0/1, changed state to up

- **%LINK** → Facility
- **3** → Severity level (Error)
- **UPDOWN** → Event type

---

## 4️⃣ Syslog Severity Levels

Syslog defines **8 levels of message severity (0–7)**:

| Level | Keyword   | Meaning                                           |
|-------|-----------|---------------------------------------------------|
| 0     | **Emergency** | System is unusable (highest priority)          |
| 1     | **Alert**     | Immediate action needed                        |
| 2     | **Critical**  | Critical conditions                            |
| 3     | **Error**     | Error conditions                               |
| 4     | **Warning**   | Warning conditions                             |
| 5     | **Notification** | Normal but significant conditions           |
| 6     | **Informational** | Informational messages                     |
| 7     | **Debugging**    | Detailed debugging information (lowest)      |

- Lower numbers mean **higher priority messages**.

---

## 5️⃣ Syslog Facilities

Facilities categorize messages by source:
- **local0–local7:** Custom facility codes for local use.
- Other predefined ones like **auth, daemon, kern** are for OS processes.

---

## 6️⃣ Syslog in Cisco IOS

### Key Commands

| Command                                         | Description                                         |
|------------------------------------------------|-----------------------------------------------------|
| `logging host <ip>`                            | Defines the syslog server IP address               |
| `logging trap <level>`                         | Sets severity level to send to server              |
| `logging console <level>`                      | Controls messages sent to console                  |
| `logging buffered <size>`                      | Stores messages in router’s RAM buffer             |
| `show logging`                                 | Displays logging configuration and buffer content  |
| `no logging console`                           | Disables console logging                           |

Example configuration:

Router(config)# logging 192.168.1.100
Router(config)# logging trap warnings
Router(config)# logging buffered 16384

- Sends severity level **4 (warnings) and above** to server and buffer.

---

## 7️⃣ Syslog vs SNMP

| Feature        | Syslog                                         | SNMP                                      |
|----------------|-----------------------------------------------|-------------------------------------------|
| Purpose        | Event logging and monitoring                  | Device management and monitoring          |
| Transport      | UDP 514                                       | UDP 161/162                               |
| Data type      | Text-based logs                               | Structured data (MIBs, OIDs)              |
| Notification   | Asynchronous (events)                        | Polling or traps/informs                  |

---

## ✅ CCNA Key Takeaways

- Syslog is used for **event logging** on network devices.
- **UDP 514** is the default transport protocol/port.
- **Severity levels 0–7**, where 0 is the most severe.
- Syslog messages can be sent to:
  - Console
  - VTY
  - Buffer
  - Syslog server (recommended for permanent storage).
- Use **`show logging`** to display stored log messages.
- Filter severity with `logging trap <level>`.

---

## 📝 Related Quiz
- [SYSLOG Quiz](./quiz/SYSLOG_Quiz.md)

---
