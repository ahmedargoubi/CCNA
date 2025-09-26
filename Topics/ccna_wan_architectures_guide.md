# CCNA WAN Architectures Complete Guide

## Table of Contents
1. [Introduction to WANs](#introduction)
2. [WAN Connection Types](#connection-types)
3. [WAN Topologies](#topologies)
4. [Leased Lines](#leased-lines)
5. [MPLS VPNs](#mpls)
6. [Internet Connectivity](#internet)
7. [VPN Technologies](#vpn)
8. [WAN Configuration](#configuration)
9. [Troubleshooting](#troubleshooting)
10. [Exam Tips](#exam-tips)

---

## Introduction to WANs {#introduction}

### What is a WAN?
- **Wide Area Network**: Network extending over large geographic areas
- **Purpose**: Connect geographically separate LANs
- **Scope**: Connects offices, data centers, and remote sites
- **Private vs Public**: Enterprise WANs use private connections; Internet is a public WAN

### WAN vs LAN Characteristics

| Characteristic | LAN | WAN |
|----------------|-----|-----|
| **Geographic Scope** | Building/Campus | Cities/Countries |
| **Ownership** | Single organization | Multiple providers |
| **Speed** | High (1-100+ Gbps) | Lower (64 Kbps - 10 Gbps) |
| **Cost** | One-time + maintenance | Monthly recurring |
| **Media** | Ethernet (copper/fiber) | Various technologies |
| **Protocols** | Ethernet | PPP, HDLC, Frame Relay, MPLS |

### Key WAN Concepts
- **CPE (Customer Premises Equipment)**: Customer-owned equipment
- **Demarcation Point (Demarc)**: Boundary between customer and provider
- **Local Loop**: Connection from customer to provider's central office
- **CO (Central Office)**: Provider's switching facility
- **Toll Network**: Provider's backbone network

---

## WAN Connection Types {#connection-types}

### 1. Dedicated Connections (Leased Lines)
**Characteristics**:
- Private, dedicated bandwidth
- Point-to-point connectivity
- Guaranteed bandwidth and low latency
- Most expensive option

**Use Cases**:
- Mission-critical applications
- Real-time traffic (voice/video)
- Predictable bandwidth requirements

### 2. Shared Infrastructure
**Characteristics**:
- Multiple customers share the same infrastructure
- Lower cost than dedicated
- Variable performance depending on usage

**Examples**:
- Internet connections
- MPLS networks
- Metro Ethernet

### 3. On-Demand Connections
**Characteristics**:
- Established when needed
- Pay-per-use model
- Backup or overflow traffic

**Examples**:
- ISDN
- Dialup connections
- Cellular data

---

## WAN Topologies {#topologies}

### Hub-and-Spoke Topology
```
    [Branch A]
         |
    [HUB Site] ---- [Branch B]
         |
    [Branch C]
```

**Advantages**:
- Centralized management and security
- Lower cost (fewer connections)
- Simple routing

**Disadvantages**:
- Single point of failure (hub)
- All traffic passes through hub
- Potential bottleneck at hub

**Best For**: Small to medium networks with centralized resources

### Full Mesh Topology
```
[Site A] ---- [Site B]
   |    \    /    |
   |     \  /     |
   |      \/      |
   |      /\      |
   |     /  \     |
[Site D] ---- [Site C]
```

**Advantages**:
- Redundancy and fault tolerance
- Direct communication between sites
- Optimal path selection

**Disadvantages**:
- High cost (n(n-1)/2 connections)
- Complex configuration and management
- Scalability issues

**Formula**: For n sites, need n(n-1)/2 connections

### Partial Mesh Topology
```
[Site A] ---- [Site B]
   |             |
   |             |
[Site D] ---- [Site C]
```

**Advantages**:
- Balance of cost and redundancy
- Some direct paths available
- More scalable than full mesh

**Disadvantages**:
- More complex than hub-and-spoke
- Not all sites have direct connections
- Suboptimal paths for some traffic

---

## Leased Lines {#leased-lines}

### Overview
- **Definition**: Dedicated physical link between two sites
- **Encapsulation**: PPP or HDLC
- **Connection**: Serial interfaces (typically)
- **Availability**: 24/7 dedicated bandwidth

### Common Standards

#### T-Carrier System (North America)
| Service | Speed | Channels |
|---------|--------|----------|
| **T1** | 1.544 Mbps | 24 DS0 (64 Kbps each) |
| **T3** | 44.736 Mbps | 28 T1 lines |

#### E-Carrier System (Europe)
| Service | Speed | Channels |
|---------|--------|----------|
| **E1** | 2.048 Mbps | 32 DS0 (64 Kbps each) |
| **E3** | 34.368 Mbps | 16 E1 lines |

#### Other Standards
- **DS3**: 44.736 Mbps (same as T3)
- **OC-1**: 51.84 Mbps (SONET)
- **OC-3**: 155.52 Mbps
- **OC-12**: 622.08 Mbps

### Advantages and Disadvantages

**Advantages**:
- Guaranteed bandwidth
- Low latency and jitter
- High security (dedicated circuit)
- Consistent performance

**Disadvantages**:
- High cost
- Long installation lead times
- Limited scalability
- Geographic availability issues

### When to Use Leased Lines
- **Mission-critical applications**
- **Real-time traffic** (voice, video conferencing)
- **Predictable, high-bandwidth requirements**
- **Maximum security requirements**
- **SLA guarantees needed**

---

## MPLS VPNs {#mpls}

### MPLS Fundamentals
- **Multi-Protocol Label Switching**: Traffic forwarding based on labels
- **Shared Infrastructure**: Multiple customers use same network
- **VPN Creation**: Labels create virtual private networks
- **Scalable**: Service provider manages core network

### MPLS Components

#### Router Types
1. **CE Router (Customer Edge)**
   - Customer-owned equipment
   - Connects to provider network
   - Does not run MPLS

2. **PE Router (Provider Edge)**
   - Provider-owned equipment
   - Adds/removes MPLS labels
   - Interfaces with customer routers

3. **P Router (Provider Core)**
   - Provider backbone routers
   - Forwards based on MPLS labels only
   - No customer route knowledge

### MPLS VPN Types

#### Layer 3 MPLS VPN
```
[CE Router] ---- [PE Router] ~~~~ [P Routers] ~~~~ [PE Router] ---- [CE Router]
     |              |                                    |              |
   OSPF          MPLS BGP                              MPLS BGP      OSPF
 Peering         Labels                                 Labels     Peering
```

**Characteristics**:
- CE and PE routers form routing protocol adjacencies
- PE routers exchange routes via MP-BGP
- Each customer has separate routing table (VRF)
- PE routers perform routing lookups

**Configuration Example**:
```
! CE Router Configuration
interface s0/0/0
 ip address 192.168.1.1 255.255.255.252
!
router ospf 1
 network 192.168.1.0 0.0.0.3 area 0
 network 10.1.0.0 0.0.255.255 area 0
```

#### Layer 2 MPLS VPN
```
[CE Router] ---- [PE Router] ~~~~ [P Routers] ~~~~ [PE Router] ---- [CE Router]
     |              |                                    |              |
   Same           Layer 2                               Layer 2      Same
  Subnet         Service                                Service     Subnet
```

**Characteristics**:
- CE routers appear directly connected
- Same subnet on both CE router interfaces
- Provider network is transparent
- CE routers peer directly for routing protocols

**Configuration Example**:
```
! CE Router A Configuration
interface s0/0/0
 ip address 192.168.100.1 255.255.255.252
!
router ospf 1
 network 192.168.100.0 0.0.0.3 area 0
 network 10.1.0.0 0.0.255.255 area 0

! CE Router B Configuration
interface s0/0/0
 ip address 192.168.100.2 255.255.255.252
!
router ospf 1
 network 192.168.100.0 0.0.0.3 area 0
 network 10.2.0.0 0.0.255.255 area 0
```

### MPLS Benefits
- **Scalability**: Provider manages core routing
- **QoS Support**: Traffic engineering capabilities
- **VPN Services**: Secure customer separation
- **Any-to-Any Connectivity**: Full mesh without full mesh cost
- **Flexible Topologies**: Hub-and-spoke or partial mesh

### MPLS Access Methods
- **Ethernet**: Most common for new deployments
- **T1/E1**: Legacy but still widely used
- **Frame Relay**: Legacy, being phased out
- **ATM**: Legacy, rarely used for new deployments

---

## Internet Connectivity {#internet}

### Consumer Technologies Used by Enterprises

#### DSL (Digital Subscriber Line)
**Technology Overview**:
- Uses existing telephone lines
- Simultaneous voice and data
- Distance-sensitive performance
- Asymmetric speeds (download > upload)

**DSL Types**:
| Type | Download Speed | Upload Speed | Distance Limit |
|------|----------------|--------------|----------------|
| **ADSL** | 1-8 Mbps | 64-640 Kbps | 18,000 feet |
| **SDSL** | 1.5 Mbps | 1.5 Mbps | 10,000 feet |
| **VDSL** | 50+ Mbps | 10+ Mbps | 4,000 feet |

**Components**:
- **DSL Modem**: Converts digital data to analog for transmission
- **DSLAM**: Digital Subscriber Line Access Multiplexer at CO
- **Splitter**: Separates voice and data frequencies

**Advantages**:
- Uses existing infrastructure
- Always-on connection
- Cost-effective for small businesses

**Disadvantages**:
- Distance limitations
- Speed varies with line quality
- Asymmetric bandwidth

#### Cable Internet (CATV)
**Technology Overview**:
- Uses cable TV infrastructure (coaxial cable)
- Shared bandwidth in neighborhood
- Higher speeds than DSL typically

**Components**:
- **Cable Modem**: DOCSIS-compliant device
- **CMTS**: Cable Modem Termination System at headend
- **Coaxial Cable**: RG-6 or RG-59 typically

**Speed Standards**:
| DOCSIS Version | Download Speed | Upload Speed |
|----------------|----------------|--------------|
| **DOCSIS 3.0** | 1+ Gbps | 200+ Mbps |
| **DOCSIS 3.1** | 10+ Gbps | 1+ Gbps |

**Advantages**:
- High-speed capability
- Wide availability
- Good for high-bandwidth applications

**Disadvantages**:
- Shared bandwidth (performance varies)
- Upload speeds typically lower
- Security concerns (shared medium)

#### Fiber Internet
**Technology Overview**:
- Fiber optic cables to premises (FTTP/FTTH)
- Highest speeds and reliability
- Growing availability

**Types**:
- **FTTP**: Fiber to the Premises
- **FTTH**: Fiber to the Home
- **FTTC**: Fiber to the Curb (copper last mile)

**Advantages**:
- Highest speeds available
- Symmetric bandwidth options
- Low latency and jitter
- Future-proof technology

**Disadvantages**:
- Limited availability
- Higher installation costs
- Requires fiber infrastructure

### Redundant Internet Connections

#### Dual-Homed Single ISP
```
[Router] ---- [ISP Router A]
    |              |
    |         [ISP Network]
    |              |
    └--------- [ISP Router B]
```

**Advantages**:
- Protection against local ISP equipment failure
- Single relationship to manage
- Potentially lower cost

**Disadvantages**:
- Still dependent on single ISP
- Limited path diversity

#### Dual-Homed Multiple ISPs
```
[Router] ---- [ISP A Network]
    |
    |
    └--------- [ISP B Network]
```

**Advantages**:
- Complete ISP redundancy
- Path diversity
- Can optimize costs/performance per ISP

**Disadvantages**:
- More complex configuration
- Multiple relationships to manage
- Potential routing issues

#### BGP Requirements
When multihoming to different ISPs, BGP is typically required:
- **AS Number**: Need your own or provider-assigned
- **Public IP Space**: Usually need provider-independent addresses
- **BGP Peering**: Establish sessions with each ISP
- **Route Advertisements**: Control inbound traffic paths

---

## VPN Technologies {#vpn}

### Site-to-Site VPNs

#### IPSec Overview
**Purpose**: Secure site-to-site connectivity over Internet
**Security Services**:
- **Authentication**: Verify identity of peers
- **Encryption**: Protect data confidentiality
- **Integrity**: Ensure data hasn't been modified
- **Anti-Replay**: Prevent replay attacks

#### IPSec Components

**Security Protocols**:
1. **AH (Authentication Header)**
   - Provides authentication and integrity
   - No encryption
   - IP Protocol 51

2. **ESP (Encapsulating Security Payload)**
   - Provides authentication, integrity, and encryption
   - IP Protocol 50
   - More commonly used than AH

**Key Management**:
1. **IKE (Internet Key Exchange)**
   - **IKEv1**: Original version, two phases
   - **IKEv2**: Improved version, more efficient
   - Establishes Security Associations (SAs)

**Modes**:
1. **Transport Mode**
   - Encrypts only the payload
   - Original IP header preserved
   - Host-to-host communications

2. **Tunnel Mode**
   - Encrypts entire original packet
   - New IP header added
   - Site-to-site VPNs

#### IPSec Process Flow
```
1. [Original Packet] → IKE Negotiation → [Tunnel Established]
2. [Original Packet] → ESP Encryption → [Encrypted Packet]
3. [Encrypted Packet] → New IP Header → [Tunneled Packet]
4. [Internet Transmission]
5. [Tunneled Packet] → Remove Header → [Encrypted Packet]
6. [Encrypted Packet] → ESP Decryption → [Original Packet]
7. [Original Packet] → Forward to Destination
```

#### IPSec Limitations
1. **No Broadcast/Multicast Support**
   - Only unicast traffic supported
   - Routing protocols like OSPF cannot run directly
   - Solution: GRE over IPSec

2. **Complex Configuration**
   - Manual tunnel configuration required
   - Difficult to scale to full mesh
   - Solution: DMVPN

#### GRE over IPSec
```
[Original Packet] → [GRE Header] → [IPSec Encryption] → [New IP Header]
```

**GRE Benefits**:
- Supports multicast and broadcast
- Multiple Layer 3 protocol support
- Simpler than pure IPSec for routing

**Combined Solution**:
- GRE provides protocol flexibility
- IPSec provides security
- Best of both technologies

#### DMVPN (Dynamic Multipoint VPN)
**Problem Solved**: Automatic full-mesh tunnel creation

**Architecture**:
- **Hub Router**: Central site with all spoke tunnels
- **Spoke Routers**: Branch sites with tunnel to hub
- **NHS (Next Hop Server)**: Function on hub router
- **NHRP**: Protocol for dynamic tunnel creation

**Process**:
1. Spokes establish tunnels to hub
2. Hub maintains spoke IP address database
3. When spoke-to-spoke communication needed:
   - Spoke queries hub for peer information
   - Hub provides peer details
   - Spokes establish direct tunnel
   - Traffic flows directly between spokes

**Benefits**:
- Hub-and-spoke configuration simplicity
- Full-mesh communication efficiency
- Automatic tunnel creation
- Scalable solution

### Remote Access VPNs

#### TLS/SSL VPNs
**Technology**: Transport Layer Security (formerly SSL)
**Use Case**: End-user remote access to corporate resources

**Components**:
1. **TLS Server**: Corporate firewall/router
2. **TLS Client**: Software on end-user device
3. **Web Browser**: For clientless access

**Client Types**:

1. **Clientless (WebVPN)**
   - Access via web browser
   - No software installation required
   - Limited application support
   - Good for basic web applications

2. **Thin Client**
   - Java applet or ActiveX control
   - Downloaded automatically
   - More application support than clientless
   - Cross-platform compatibility

3. **Full Client**
   - Dedicated VPN client software
   - Full network-layer access
   - Best application compatibility
   - Example: Cisco AnyConnect

#### Remote Access Process
```
1. User connects to corporate VPN server
2. Authentication (username/password, certificates)
3. TLS tunnel establishment
4. IP address assignment to client
5. Access to internal resources through tunnel
6. All traffic encrypted between client and server
```

### Site-to-Site vs Remote Access Comparison

| Aspect | Site-to-Site | Remote Access |
|--------|--------------|---------------|
| **Protocol** | IPSec (typically) | TLS/SSL (typically) |
| **Use Case** | Connect networks | Individual user access |
| **Users Served** | Many per tunnel | One per connection |
| **Connection Type** | Permanent/always-on | On-demand |
| **Configuration** | Router/firewall | Client software |
| **Scalability** | Limited by tunnels | High (many concurrent users) |

---

## WAN Configuration {#configuration}

### GRE Tunnel Configuration

#### Basic GRE Tunnel Setup
```cisco
! Router 1 Configuration
interface tunnel 0
 ip address 172.16.1.1 255.255.255.252
 tunnel source serial 0/0/0
 tunnel destination 203.0.113.2
 tunnel mode gre ip
!
ip route 0.0.0.0 0.0.0.0 198.51.100.1

! Router 2 Configuration  
interface tunnel 0
 ip address 172.16.1.2 255.255.255.252
 tunnel source serial 0/0/0
 tunnel destination 198.51.100.2
 tunnel mode gre ip
!
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

#### GRE with OSPF
```cisco
! Router 1
interface tunnel 0
 ip address 172.16.1.1 255.255.255.252
 tunnel source serial 0/0/0
 tunnel destination 203.0.113.2
!
router ospf 1
 network 172.16.1.0 0.0.0.3 area 0
 network 10.1.0.0 0.0.255.255 area 0
 passive-interface serial 0/0/0

! Router 2
interface tunnel 0
 ip address 172.16.1.2 255.255.255.252  
 tunnel source serial 0/0/0
 tunnel destination 198.51.100.2
!
router ospf 1
 network 172.16.1.0 0.0.0.3 area 0
 network 10.2.0.0 0.0.255.255 area 0
 passive-interface serial 0/0/0
```

### PPP Configuration

#### Basic PPP Setup
```cisco
! Router 1
interface serial 0/0/0
 ip address 192.168.1.1 255.255.255.252
 encapsulation ppp
 no shutdown

! Router 2  
interface serial 0/0/0
 ip address 192.168.1.2 255.255.255.252
 encapsulation ppp
 no shutdown
```

#### PPP with Authentication
```cisco
! Router 1
username Router2 password cisco123
!
interface serial 0/0/0
 ip address 192.168.1.1 255.255.255.252
 encapsulation ppp
 ppp authentication chap
 no shutdown

! Router 2
username Router1 password cisco123
!
interface serial 0/0/0
 ip address 192.168.1.2 255.255.255.252
 encapsulation ppp
 ppp authentication chap
 no shutdown
```

### HDLC Configuration
```cisco
! Default encapsulation on Cisco serial interfaces
interface serial 0/0/0
 ip address 192.168.1.1 255.255.255.252
 encapsulation hdlc
 no shutdown
```

---

## Troubleshooting {#troubleshooting}

### Common WAN Issues

#### Physical Layer Problems
**Symptoms**:
- Interface shows down/down
- High error rates
- Intermittent connectivity

**Troubleshooting Steps**:
```cisco
! Check interface status
show interfaces serial 0/0/0

! Check for errors
show interfaces serial 0/0/0 | include error

! Check keepalives
show interfaces serial 0/0/0 | include keepalive
```

**Common Causes**:
- Cable problems
- Clock rate issues (DCE/DTE)
- Provider circuit issues

#### Data Link Layer Problems
**Symptoms**:
- Interface shows up/down
- Encapsulation mismatches
- Authentication failures

**Troubleshooting Steps**:
```cisco
! Verify encapsulation
show interfaces serial 0/0/0

! Check PPP status
show ppp multilink
show ppp interfaces

! Debug PPP (use carefully)
debug ppp negotiation
debug ppp authentication
```

#### Network Layer Problems
**Symptoms**:
- Interface up but no connectivity
- Routing issues
- IP address conflicts

**Troubleshooting Steps**:
```cisco
! Verify IP configuration
show ip interface brief
show ip route

! Test connectivity
ping [destination]
traceroute [destination]

! Check routing protocols
show ip ospf neighbor
show ip eigrp neighbors
```

### GRE Tunnel Troubleshooting

#### Common Issues
1. **Tunnel Source/Destination Mismatch**
2. **Routing Problems**
3. **MTU Issues**
4. **Recursive Routing**

#### Troubleshooting Commands
```cisco
! Check tunnel status
show interfaces tunnel 0

! Verify tunnel configuration
show running-config interface tunnel 0

! Check routing table
show ip route

! Test tunnel connectivity
ping [tunnel-peer-ip] source tunnel 0

! Check for recursive routing
show ip route [tunnel-destination]
```

### VPN Troubleshooting

#### IPSec Issues
**Common Problems**:
- IKE negotiation failures
- IPSec SA establishment issues
- Interesting traffic problems

**Troubleshooting Commands**:
```cisco
! Check IKE SAs
show crypto isakmp sa

! Check IPSec SAs  
show crypto ipsec sa

! Debug IKE (use carefully)
debug crypto isakmp
debug crypto ipsec
```

---

## Exam Tips {#exam-tips}

### High-Frequency Topics

#### 1. WAN Technologies Comparison
**Know the differences between**:
- Leased lines vs MPLS vs Internet
- Layer 2 vs Layer 3 MPLS VPNs
- Site-to-site vs Remote access VPNs

#### 2. Topology Selection
**Understand when to use**:
- Hub-and-spoke: Centralized resources, cost-sensitive
- Full mesh: High availability, any-to-any communication
- Partial mesh: Balance of cost and redundancy

#### 3. VPN Technologies
**Key Points**:
- IPSec for site-to-site (typically)
- TLS for remote access (typically)
- GRE over IPSec for multicast support
- DMVPN for scalable full mesh

#### 4. Configuration Concepts
**Be familiar with**:
- GRE tunnel configuration steps
- PPP authentication methods
- Basic troubleshooting commands

### Memory Aids

#### WAN Speed Comparison
- **T1**: 1.544 Mbps (24 channels × 64k)
- **E1**: 2.048 Mbps (32 channels × 64k) 
- **T3**: ~45 Mbps (28 T1s)
- **E3**: ~34 Mbps (16 E1s)

#### MPLS Router Types
- **CE**: Customer Edge (customer owns)
- **PE**: Provider Edge (adds/removes labels)
- **P**: Provider core (forwards on labels only)

#### VPN Protocol Association
- **Site-to-Site**: IPSec (typically)
- **Remote Access**: TLS/SSL (typically)
- **Multicast Support**: GRE over IPSec
- **Scalable Mesh**: DMVPN

### Common Exam Scenarios

#### Scenario 1: Technology Selection
*"A company needs to connect 5 branch offices with guaranteed bandwidth and low latency for real-time applications. Cost is not the primary concern. What technology should they use?"*

**Answer**: Leased lines (guaranteed bandwidth, low latency)

#### Scenario 2: VPN Requirements  
*"A company wants to allow employees to securely access internal resources from home using their personal devices. What VPN technology is most appropriate?"*

**Answer**: Remote access VPN using TLS/SSL

#### Scenario 3: Routing Protocol Issues
*"OSPF is not forming adjacencies across an IPSec tunnel between two sites. What is the most likely cause and solution?"*

**Answer**: IPSec doesn't support multicast; use GRE over IPSec

#### Scenario 4: Topology Design
*"A company has a headquarters and 10 branch offices. Branch offices primarily communicate with headquarters, with minimal inter-branch communication. What topology is most cost-effective?"*

**Answer**: Hub-and-spoke with headquarters as the hub

Remember: Focus on understanding the business drivers for each technology choice, not just the technical details. The CCNA exam often tests your ability to recommend the right solution for specific requirements.