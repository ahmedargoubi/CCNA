# EtherChannel – Complete CCNA Guide

## 1. Introduction to EtherChannel
EtherChannel is a technology that allows multiple physical Ethernet links to be combined into one logical link. This increases bandwidth and provides redundancy between switches, routers, or servers.

- It bundles several physical links to act as a single logical link.
- Helps prevent loops without relying solely on STP.
- Provides load balancing and redundancy.
- Supports Fast Ethernet, Gigabit Ethernet, and 10 Gigabit Ethernet interfaces.

---

## 2. Benefits of EtherChannel
- **Increased bandwidth:** Combines the bandwidth of multiple physical links.
- **Redundancy:** If one physical link fails, traffic continues over other links.
- **Load balancing:** Traffic is distributed across all active links.
- **Simplified management:** Treat multiple links as a single interface.
- **Prevents loops:** STP treats the EtherChannel as a single link, avoiding topology loops.

---

## 3. EtherChannel Modes and Protocols

EtherChannel can be configured in two main modes using two protocols:

### 3.1 PAgP (Port Aggregation Protocol)
- Cisco proprietary protocol.
- Used to automatically negotiate and maintain EtherChannel.
- Modes:
  - **Auto:** Passively waits for PAgP packets to form EtherChannel.
  - **Desirable:** Actively tries to form EtherChannel by sending PAgP packets.

### 3.2 LACP (Link Aggregation Control Protocol)
- IEEE 802.3ad standard (now 802.1AX).
- Used to automatically negotiate EtherChannel.
- Modes:
  - **Passive:** Passively waits for LACP packets.
  - **Active:** Actively sends LACP packets to form EtherChannel.

### 3.3 On mode (No negotiation)
- Forces interfaces to form EtherChannel without negotiation.
- Should be used carefully to avoid misconfigurations.

---

## 4. EtherChannel Configuration Steps

### 4.1 Basic Configuration Example (PAgP)
```bash
interface range GigabitEthernet0/1 - 2
  channel-group 1 mode desirable
!
interface Port-channel1
  switchport mode trunk
```

### 4.2 Basic Configuration Example (LACP)
```bash
interface range GigabitEthernet0/1 - 2
  channel-group 1 mode active
!
interface Port-channel1
  switchport mode trunk
```

### 4.3 On mode (No protocol)
```bash
interface range GigabitEthernet0/1 - 2
  channel-group 1 mode on
!
interface Port-channel1
  switchport mode trunk
```

---

## 5. Important EtherChannel Concepts

### 5.1 Port-channel Interface
- When EtherChannel is configured, a logical interface called Port-channel is created.
- This interface is managed as a single unit.

### 5.2 Load Balancing
- EtherChannel distributes traffic based on source/destination MAC or IP addresses.
- Hashing algorithms decide which physical link carries the traffic.
- Load balancing methods include:
  - Source MAC address
  - Destination MAC address
  - Source and destination IP addresses

### 5.3 Limitations and Rules
- All physical interfaces in an EtherChannel must have:
  - Same speed and duplex settings
  - Same VLAN configuration and trunking mode
  - Same allowed VLANs on trunks
  - Same native VLAN on trunks
- Mixing different EtherChannel protocols on the same channel will cause it to fail.
- Maximum of 8 physical links per EtherChannel.

---

## 6. Troubleshooting EtherChannel

### 6.1 Common Issues
- Misconfigured modes causing negotiation failure.
- Mismatched VLANs or trunk settings.
- Different speed/duplex settings.
- Incompatible EtherChannel protocols on either side.

### 6.2 Useful Commands
- `show etherchannel summary` — Displays EtherChannel status and ports.
- `show interfaces port-channel` — Shows details about the logical interface.
- `show running-config interface port-channelX` — Verify Port-channel config.
- `show interfaces GigabitEthernet0/1 etherchannel` — Shows EtherChannel info per physical interface.

---

## 7. Summary

| Topic | Details |
|-------|---------|
| Purpose | Bundles multiple physical links into one logical link |
| Protocols | PAgP (Cisco), LACP (IEEE), On (manual) |
| Modes | PAgP: auto/desirable, LACP: passive/active, On |
| Maximum Links | 8 |
| Load Balancing Methods | MAC address, IP address-based hashing |
| Use Cases | Increase bandwidth, redundancy, simplified management |

---

## 8. References
- Cisco CCNA Official Cert Guide
- Cisco Documentation on EtherChannel
- IEEE 802.3ad Standard