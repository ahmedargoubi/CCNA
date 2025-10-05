# CCNA Network Components Study Guide

## Table of Contents
1. [Host Roles](#host-roles)
2. [Peer-to-Peer (P2P) Networks](#peer-to-peer-p2p-networks)
3. [End Devices](#end-devices)
4. [Intermediary Devices](#intermediary-devices)
5. [Network Media](#network-media)
6. [Four Main Criteria for Choosing Network Media](#four-main-criteria-for-choosing-network-media)

---

## Host Roles

Hosts are devices that send or receive messages on a network. Every computer connected to a network is a host.

### Client
- **Definition:** A host that requests and uses services from servers
- **Characteristics:**
  - Initiates communication
  - Runs client software applications
  - Examples: Web browsers, email clients, file transfer applications
- **Functions:**
  - Sends requests to servers
  - Receives and processes responses
  - Provides user interface for services

### Server
- **Definition:** A host that provides services to clients
- **Characteristics:**
  - Responds to client requests
  - Runs server software applications
  - Typically more powerful hardware than clients
  - Often run continuously
- **Common Server Types:**
  - **Web Server:** Hosts websites (HTTP/HTTPS)
  - **Email Server:** Manages email (SMTP, POP3, IMAP)
  - **File Server:** Stores and shares files (FTP, SMB)
  - **DNS Server:** Translates domain names to IP addresses
  - **DHCP Server:** Assigns IP addresses dynamically
  - **Database Server:** Manages databases
  - **Application Server:** Hosts business applications

### Client-Server Model
```
Client → Request → Server
Client ← Response ← Server
```

**Advantages:**
- Centralized administration
- Better security control
- Resource sharing
- Scalability
- Backup and recovery management

**Disadvantages:**
- Single point of failure (server)
- Higher cost
- Requires administration
- Network dependency

---

## Peer-to-Peer (P2P) Networks

### Definition
A network architecture where each host can function as both a client and a server.

### Characteristics
- **Decentralized:** No dedicated server
- **Role flexibility:** Each device can be client and server simultaneously
- **Simple setup:** Easy to configure and deploy
- **Small scale:** Best for 10 or fewer computers

### Advantages
✓ Easy to set up
✓ Less complex
✓ Lower cost (no dedicated server needed)
✓ No specialized administrator required
✓ Individual control over resources

### Disadvantages
✗ No centralized administration
✗ Less secure
✗ Not scalable
✗ Slower performance under load
✗ Difficult to back up
✗ No centralized data storage

### Common P2P Applications
- File sharing (BitTorrent)
- Home networks
- Small office networks
- Ad-hoc wireless networks
- Cryptocurrency networks (Bitcoin)

### When to Use P2P
- Home networks (fewer than 10 devices)
- Small offices
- Temporary networks
- When budget is limited
- When centralized control is not required

### When NOT to Use P2P
- Networks with more than 10 computers
- When security is critical
- When centralized administration is needed
- For business-critical applications
- When consistent performance is required

---

## End Devices

### Definition
End devices are sources or destinations of messages transmitted over a network. They originate or receive data.

### Categories of End Devices

#### 1. Computers
- Desktop computers
- Laptop computers
- Workstations
- Servers (when acting as endpoints)

#### 2. Mobile Devices
- Smartphones
- Tablets
- Wearable devices (smartwatches)
- E-readers

#### 3. Network-Attached Devices
- Network printers
- Network scanners
- IP phones (VoIP phones)
- Video cameras (IP cameras)

#### 4. IoT (Internet of Things) Devices
- Smart home devices (thermostats, lights)
- Security systems
- Industrial sensors
- Smart appliances
- Medical devices

#### 5. Entertainment Devices
- Smart TVs
- Gaming consoles
- Streaming devices
- Set-top boxes

### Characteristics of End Devices
- **Endpoint:** Source or destination of data
- **IP Address:** Each has a unique network address
- **User Interface:** Most provide interface for users
- **Applications:** Run software applications
- **Network Interface:** Have NIC (Network Interface Card)

### End Device Functions
1. Generate data (source)
2. Receive data (destination)
3. Run applications
4. Provide user services
5. Connect to network infrastructure

---

## Intermediary Devices

### Definition
Intermediary devices connect individual end devices to the network and interconnect multiple networks. They provide connectivity and manage data flow.

### Types of Intermediary Devices

#### 1. Switch
- **Function:** Connects devices within a Local Area Network (LAN)
- **Layer:** Operates at Layer 2 (Data Link Layer)
- **Intelligence:** Makes forwarding decisions based on MAC addresses
- **Characteristics:**
  - Multiple ports (typically 24 or 48)
  - Fast switching speeds
  - Full-duplex communication
  - VLAN support
- **Use Cases:**
  - Connecting computers in an office
  - Creating network segments
  - Building campus networks

#### 2. Router
- **Function:** Interconnects networks and routes data between them
- **Layer:** Operates at Layer 3 (Network Layer)
- **Intelligence:** Makes forwarding decisions based on IP addresses
- **Characteristics:**
  - Connects different networks
  - Provides default gateway
  - Implements access control
  - Supports multiple protocols
- **Use Cases:**
  - Connecting LAN to WAN
  - Connecting to Internet
  - Interconnecting branch offices
  - Network segmentation

#### 3. Wireless Access Point (WAP)
- **Function:** Provides wireless connectivity to devices
- **Layer:** Operates at Layer 2
- **Characteristics:**
  - Converts wired signals to wireless
  - Supports WiFi standards (802.11a/b/g/n/ac/ax)
  - Multiple SSIDs support
  - Security features (WPA2/WPA3)
- **Use Cases:**
  - Wireless LANs
  - Guest networks
  - Mobile device connectivity

#### 4. Firewall
- **Function:** Filters traffic based on security policies
- **Layer:** Can operate at multiple layers (3-7)
- **Characteristics:**
  - Monitors incoming/outgoing traffic
  - Blocks unauthorized access
  - Creates security zones
  - Implements NAT
- **Types:**
  - Packet-filtering firewall
  - Stateful firewall
  - Next-generation firewall (NGFW)
  - Application firewall

#### 5. Wireless LAN Controller (WLC)
- **Function:** Manages multiple access points centrally
- **Characteristics:**
  - Centralized configuration
  - Seamless roaming
  - Load balancing
  - Security policy enforcement

#### 6. Multilayer Switch
- **Function:** Combines switching and routing functions
- **Layer:** Operates at Layer 2 and Layer 3
- **Characteristics:**
  - High-speed routing
  - VLAN routing
  - Wire-speed performance
  - QoS support

### Intermediary Device Functions

1. **Regenerate and retransmit signals**
   - Amplify weak signals
   - Extend network distance
   - Maintain signal quality

2. **Maintain information about network paths**
   - Store routing tables
   - Learn network topology
   - Update path information

3. **Notify devices of errors and communication failures**
   - Generate error messages
   - Report link failures
   - Send status updates

4. **Direct data along alternate paths**
   - Find best path
   - Implement redundancy
   - Balance traffic load

5. **Filter traffic based on security settings**
   - Implement access control
   - Block unauthorized traffic
   - Enforce policies

6. **Permit or deny data flows**
   - Quality of Service (QoS)
   - Bandwidth management
   - Priority handling

---

## Network Media

### Definition
Network media is the physical or wireless pathway that carries data signals between devices. It provides the channel for network communications.

### Types of Network Media

#### 1. Copper Cable

##### A. Unshielded Twisted Pair (UTP)
- **Description:** Most common cable type, pairs of insulated copper wires twisted together
- **Standards:** Cat5e, Cat6, Cat6a, Cat7, Cat8
- **Connector:** RJ-45
- **Maximum Distance:** 100 meters (328 feet)
- **Speed:**
  - Cat5e: Up to 1 Gbps
  - Cat6: Up to 10 Gbps (55m)
  - Cat6a: Up to 10 Gbps (100m)
  - Cat7/8: Up to 40/100 Gbps
- **Advantages:**
  - Inexpensive
  - Easy to install
  - Flexible
  - Most widely used
- **Disadvantages:**
  - Limited distance
  - Susceptible to EMI/RFI
  - Security 