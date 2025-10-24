# CCNA Endpoint Security Guide

## Network Attacks Today

### Common Attack Types

**Malware**
- Viruses, worms, trojans, ransomware
- Infects systems and spreads
- Can encrypt data (ransomware) or steal information

**DoS/DDoS (Denial of Service)**
- Overwhelms target with traffic
- Makes services unavailable
- DDoS uses multiple sources (botnet)

**Phishing**
- Fake emails/websites to steal credentials
- Social engineering attack
- Targets human behavior

**Man-in-the-Middle (MITM)**
- Attacker intercepts communication
- Can read or modify data
- Exploits unencrypted connections

**Password Attacks**
- Brute force, dictionary attacks
- Credential stuffing
- Exploits weak passwords

**Zero-Day Exploits**
- Attacks using unknown vulnerabilities
- No patch available yet
- High risk until vendor releases fix

---

## Network Security Devices

### 1. VPN-Enabled Router

**Purpose**: Secure remote access and site-to-site connectivity

**Functions**:
- Routes traffic between networks
- Creates encrypted VPN tunnels
- IPsec or SSL VPN support
- Acts as first line of defense

**VPN Types**:
- **Site-to-Site**: Connects entire networks (office-to-office)
- **Remote Access**: Individual users connect to corporate network

**Use Case**: Remote workers securely access company resources

---

### 2. NAC (Network Access Control)

**Purpose**: Controls which devices can access the network

**Functions**:
- Authenticates users and devices
- Checks device compliance (antivirus, patches, OS version)
- Grants or denies network access
- Can quarantine non-compliant devices

**How It Works**:
```
Device connects → NAC checks credentials
                → NAC checks device health
                → Grant access OR Quarantine
```

**Cisco Solution**: Cisco ISE (Identity Services Engine)

**Use Case**: Ensure only authorized, compliant devices connect to network

---

### 3. NGFW (Next-Generation Firewall)

**Purpose**: Advanced firewall with application-level inspection

**Traditional Firewall**:
- Filters based on IP, port, protocol (Layer 3-4)
- Stateful packet inspection

**NGFW Adds**:
- **Application awareness** (Layer 7)
- **Deep packet inspection** (DPI)
- **Intrusion Prevention System (IPS)**
- **User identity tracking**
- **SSL/TLS inspection**
- **Threat intelligence integration**

**Cisco Solution**: Cisco Firepower

**Comparison**:
| Feature | Traditional Firewall | NGFW |
|---------|---------------------|------|
| IP/Port filtering | ✅ | ✅ |
| Stateful inspection | ✅ | ✅ |
| Application control | ❌ | ✅ |
| IPS | ❌ | ✅ |
| User identity | ❌ | ✅ |
| Advanced threats | ❌ | ✅ |

**Use Case**: Block specific applications (Facebook), detect malware, prevent intrusions

---

## Endpoint Protection

### What Are Endpoints?

**Endpoints** = Devices that connect to network:
- Desktop computers
- Laptops
- Smartphones
- Tablets
- Servers
- IoT devices

### Endpoint Security Components

**Antivirus/Anti-malware**
- Detects and removes malicious software
- Signature-based and behavior-based detection

**Host-Based Firewall**
- Controls traffic to/from individual device
- Application-level control

**Host-Based IPS (HIPS)**
- Monitors system behavior
- Blocks suspicious activity

**Disk Encryption**
- Encrypts entire hard drive
- Protects data if device stolen

**Patch Management**
- Keeps OS and software updated
- Fixes security vulnerabilities

**Endpoint Detection and Response (EDR)**
- Advanced threat detection
- Monitors endpoint behavior
- Automated response to threats

**Cisco Solution**: Cisco AMP (Advanced Malware Protection) for Endpoints

---

## ESA (Email Security Appliance)

### Purpose
Protects against email-based threats

### Key Functions

**Spam Filtering**
- Blocks unwanted/junk email
- Reduces inbox clutter

**Phishing Protection**
- Detects fake emails
- Blocks credential theft attempts

**Malware Detection**
- Scans email attachments
- Blocks infected files

**Data Loss Prevention (DLP)**
- Prevents sensitive data from leaving via email
- Scans outbound emails

**Advanced Threat Protection**
- Sandboxing suspicious attachments
- URL analysis and rewriting
- Outbreak filters

**Encryption**
- Secures email content in transit
- Protects sensitive communications

### How ESA Works
```
Incoming Email → ESA Scans → Clean: Deliver
                            → Malicious: Block/Quarantine
                            → Suspicious: Sandbox analysis
```

**Deployment**: Typically at network perimeter before mail server

**Cisco Solution**: Cisco Email Security Appliance (ESA)

---

## WSA (Web Security Appliance)

### Purpose
Controls and secures web traffic

### Key Functions

**URL Filtering**
- Blocks access to malicious/inappropriate websites
- Category-based filtering (social media, gambling, etc.)
- Blacklist/whitelist management

**Malware Scanning**
- Scans downloaded files
- Blocks drive-by downloads

**Application Visibility and Control**
- Identifies web applications
- Enforces usage policies
- Bandwidth management

**HTTPS Inspection**
- Decrypts and inspects encrypted traffic
- Re-encrypts before forwarding

**Data Loss Prevention (DLP)**
- Prevents sensitive data upload
- Monitors file uploads to cloud

**Acceptable Use Policy (AUP) Enforcement**
- Enforces company web usage rules
- Time-based access controls

### How WSA Works
```
User requests website → WSA intercepts
                      → Check URL reputation
                      → Scan for malware
                      → Apply policies
                      → Allow/Block
```

**Deployment Options**:
- Inline (proxy mode)
- Transparent mode
- Cloud-based (Cisco Umbrella)

**Cisco Solutions**: 
- Cisco Web Security Appliance (WSA)
- Cisco Umbrella (cloud-based)

---

## Security Device Comparison

| Device | Primary Function | Layer | Protects Against |
|--------|------------------|-------|------------------|
| **VPN Router** | Encrypted connectivity | Network | Eavesdropping, MITM |
| **NAC** | Access control | Network/Endpoint | Unauthorized access |
| **NGFW** | Advanced firewall | Network/Application | Intrusions, malware, apps |
| **Endpoint Protection** | Device security | Endpoint | Malware, exploits |
| **ESA** | Email security | Application | Phishing, spam, malware |
| **WSA** | Web security | Application | Malicious sites, malware |

---

## Defense-in-Depth Strategy

Security should have **multiple layers**:

```
Internet
    ↓
[NGFW] ← Perimeter protection
    ↓
[WSA/ESA] ← Application-level filtering
    ↓
[NAC] ← Access control
    ↓
Internal Network
    ↓
[Endpoint Protection] ← Device protection
    ↓
End Users
```

**Principle**: If one layer fails, others provide backup protection

---

## CCNA Exam Key Points

**Know**:
- Common attack types (malware, DoS, phishing)
- Three main devices: VPN Router, NAC, NGFW
- Difference between traditional firewall and NGFW
- What endpoints are and how to protect them
- ESA protects email, WSA protects web traffic
- Defense-in-depth concept

**Common Questions**:
- "What does NGFW add?" → Application awareness, IPS, user identity
- "What checks device compliance?" → NAC
- "What secures remote access?" → VPN-enabled router
- "What protects against phishing?" → ESA
- "What blocks malicious websites?" → WSA

---

## Quick Reference

### Device Acronyms
- **VPN**: Virtual Private Network
- **NAC**: Network Access Control
- **NGFW**: Next-Generation Firewall
- **ESA**: Email Security Appliance
- **WSA**: Web Security Appliance
- **IPS**: Intrusion Prevention System
- **EDR**: Endpoint Detection and Response
- **DLP**: Data Loss Prevention

### Cisco Solutions
- **Firepower**: NGFW
- **ISE**: NAC
- **AMP**: Endpoint protection
- **ESA**: Email security
- **WSA/Umbrella**: Web security

---

*Endpoint security uses multiple layers to protect against modern threats.*