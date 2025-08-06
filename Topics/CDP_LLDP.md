# 🔎 CDP (Cisco Discovery Protocol) & LLDP (Link Layer Discovery Protocol) - CCNA Notes

## 1️⃣ Purpose of Discovery Protocols
- Used to **discover directly connected devices** (neighbors) on a network.
- Help in **network troubleshooting, documentation, and topology mapping**.
- Share device information such as:
  - Device ID (hostname)
  - IP address
  - Port ID
  - Capabilities (router, switch, phone, etc.)
  - Platform and OS version

---

## 2️⃣ CDP (Cisco Discovery Protocol)

- **Type:** Cisco proprietary protocol.
- **OSI Layer:** Data Link (Layer 2).
- **Default Status:** Enabled globally and on all supported interfaces.
- **Transport Protocol:** Does not use IP or higher layers; communicates directly over **Layer 2**.
- **Multicast Address:** 01:00:0c:cc:cc:cc.
- **Hello Interval:** By default, CDP advertisements are sent every **60 seconds**.
- **Holdtime:** Information held for **180 seconds** before being discarded.

### Key CDP Commands

| Command                          | Description                                   |
|---------------------------------|-----------------------------------------------|
| `cdp run`                       | Enables CDP globally                          |
| `no cdp run`                    | Disables CDP globally                         |
| `cdp enable`                    | Enables CDP on a specific interface           |
| `no cdp enable`                 | Disables CDP on a specific interface          |
| `show cdp neighbors`            | Displays directly connected Cisco devices      |
| `show cdp neighbors detail`     | Displays detailed info, including IP address   |
| `show cdp interface`            | Shows CDP status per interface                 |
| `show cdp traffic`              | Displays CDP traffic statistics                |

---

## 3️⃣ LLDP (Link Layer Discovery Protocol)

- **Type:** **IEEE 802.1AB standard** (vendor-neutral).
- **OSI Layer:** Data Link (Layer 2).
- **Purpose:** Similar to CDP, but works across **multi-vendor environments**.
- **Transport Protocol:** Layer 2 multicast (does not rely on IP).
- **Default Status:** **Disabled** on Cisco devices (must be enabled manually).
- **Hello Interval:** Default **30 seconds** for advertisements.
- **Holdtime:** Default **120 seconds**.

### Key LLDP Commands

| Command                             | Description                                  |
|------------------------------------|----------------------------------------------|
| `lldp run`                         | Enables LLDP globally                        |
| `no lldp run`                      | Disables LLDP globally                       |
| `lldp transmit`                    | Enables sending LLDP packets on interface    |
| `lldp receive`                     | Enables receiving LLDP packets on interface  |
| `no lldp transmit` / `no lldp receive` | Disables LLDP on an interface             |
| `show lldp neighbors`              | Displays LLDP neighbors                     |
| `show lldp neighbors detail`       | Shows detailed LLDP info                     |

---

## 4️⃣ CDP vs LLDP

| Feature              | CDP                       | LLDP                   |
|----------------------|---------------------------|------------------------|
| Vendor               | Cisco proprietary         | Open standard (IEEE)   |
| OSI Layer            | Layer 2                   | Layer 2                |
| Default on Cisco     | Enabled                   | Disabled               |
| Hello interval       | 60 seconds                | 30 seconds             |
| Holdtime             | 180 seconds               | 120 seconds            |
| Supported Devices    | Cisco only                | Multi-vendor           |

---

## ✅ CCNA Key Takeaways
- CDP and LLDP operate at **Layer 2**, independent of IP.
- **CDP:** Enabled by default, Cisco-only.
- **LLDP:** Multi-vendor, must be enabled manually on Cisco.
- Use **`show cdp neighbors detail`** or **`show lldp neighbors detail`** to discover IPs of connected devices.
- **Disable CDP** on untrusted interfaces for **security reasons** (prevents information disclosure).

---

## 📝 Related Quiz
- [CDP & LLDP Quiz](./quiz/CDP_LLDP_Quiz.md)

---
