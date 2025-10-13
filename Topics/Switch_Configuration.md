# CCNA Switch Configuration 

## Table of Contents
1. [Prerequisites & Fundamentals](#prerequisites)
2. [Switch Port Configuration](#switch-port-configuration)
3. [Verifying Switch Configuration](#verification-commands)
4. [Auto-MDIX Configuration](#auto-mdix)
5. [Configuring SSH for Secure Remote Access](#ssh-configuration)
6. [Troubleshooting Network Access Layer](#troubleshooting)

---

## Prerequisites & Fundamentals {#prerequisites}

### Understanding Switch Basics

**What is a Switch?**
- Layer 2 device that forwards frames based on MAC addresses
- Creates separate collision domains for each port
- Uses CAM (Content Addressable Memory) table to learn MAC addresses

**Key Concepts to Master:**
1. **Duplex & Speed** - Communication modes and data rates
2. **Auto-MDIX** - Automatic cable type detection
3. **Interface Status** - Understanding up/down states
4. **MAC Address Table** - How switches learn and forward
5. **SSH vs Telnet** - Secure vs insecure remote access

### Switch Access Methods

1. **Console Access** - Direct physical connection (initial configuration)
2. **Telnet** - Remote access (insecure, sends passwords in plaintext)
3. **SSH** - Secure remote access (encrypted)

---

## Switch Port Configuration {#switch-port-configuration}

### Basic Port Configuration Steps

#### Step 1: Enter Interface Configuration Mode

**Single Interface:**
```
S1# configure terminal
S1(config)# interface fastethernet 0/1
```

**Multiple Interfaces (Range):**
```
S1(config)# interface range fastethernet 0/1-24
```

**GigabitEthernet Interface:**
```
S1(config)# interface gigabitethernet 1/0/1
```

#### Step 2: Add Interface Description

```
S1(config-if)# description Connection to PC-A
```

**Best Practice**: Always document what's connected to each port for easier troubleshooting!

#### Step 3: Configure Duplex Mode

```
S1(config-if)# duplex auto
```

**Duplex Options:**
- `duplex auto` - **Autonegotiation (RECOMMENDED)**
- `duplex full` - Full-duplex (send and receive simultaneously)
- `duplex half` - Half-duplex (one direction at a time - legacy only)

**Important:**
- Full-duplex doubles bandwidth (no collisions)
- Half-duplex is only for legacy devices
- Modern networks use full-duplex exclusively

#### Step 4: Configure Interface Speed

```
S1(config-if)# speed auto
```

**Speed Options:**
- `speed auto` - **Autonegotiation (RECOMMENDED)**
- `speed 10` - 10 Mbps
- `speed 100` - 100 Mbps (Fast Ethernet)
- `speed 1000` - 1000 Mbps = 1 Gbps (Gigabit Ethernet)

**Critical Speed/Duplex Rules:**
- **10/100 Mbps ports**: Can operate in half-duplex OR full-duplex
- **1000 Mbps (1 Gbps) ports**: Operate ONLY in full-duplex mode
- **Fiber-optic ports (1000BASE-SX)**: Fixed speed, always full-duplex

**Autonegotiation Best Practice:**
When connecting to known devices (servers, workstations, network devices), manually setting speed and duplex can prevent issues, but `auto` is generally recommended unless troubleshooting connectivity problems.

#### Step 5: Enable the Interface

```
S1(config-if)# no shutdown
```

**Note**: Switch ports are enabled by default (unlike router interfaces).

#### Step 6: Return and Save Configuration

```
S1(config-if)# end
S1# copy running-config startup-config
```

**Shorthand:**
```
S1# write memory
```
or simply:
```
S1# wr
```

---

## Complete Port Configuration Example

```cisco
! Enter global configuration mode
S1# configure terminal

! Configure a single port
S1(config)# interface fastethernet 0/6
S1(config-if)# description PC-A Workstation
S1(config-if)# duplex auto
S1(config-if)# speed auto
S1(config-if)# no shutdown
S1(config-if)# exit

! Configure multiple ports at once
S1(config)# interface range gigabitethernet 1/0/1-12
S1(config-if-range)# description End User Workstations
S1(config-if-range)# duplex auto
S1(config-if-range)# speed auto
S1(config-if-range)# no shutdown
S1(config-if-range)# end

! Save configuration to NVRAM
S1# copy running-config startup-config
Destination filename [startup-config]? [Enter]
Building configuration...
[OK]
```

---

## Auto-MDIX Configuration {#auto-mdix}

### What is Auto-MDIX?

**Problem it solves:**
- Traditionally, you needed **straight-through cables** to connect different device types (switch-to-PC)
- You needed **crossover cables** to connect similar device types (switch-to-switch, PC-to-PC)

**Auto-MDIX Solution:**
- Automatically detects the required cable connection type
- Configures the interface appropriately
- Works with either straight-through OR crossover cables
- Eliminates the need to worry about cable types

### Enabling Auto-MDIX

```
S1(config)# interface fastethernet 0/1
S1(config-if)# mdix auto
S1(config-if)# exit
```

**Enable on all ports:**
```
S1(config)# interface range fastethernet 0/1-24
S1(config-if-range)# mdix auto
S1(config-if-range)# exit
```

### Auto-MDIX Requirements

**CRITICAL**: For auto-MDIX to work correctly:
- Interface speed must be set to `auto`
- Interface duplex must be set to `auto`

```
S1(config-if)# speed auto
S1(config-if)# duplex auto
S1(config-if)# mdix auto
```

### Verify Auto-MDIX Configuration

```
S1# show controllers ethernet-controller fa0/1 phy | include MDIX
Auto-MDIX        : On   [AdminState=1   Flags=0x00052248]
```

**Alternative verification:**
```
S1# show interfaces fa0/1 status

Port      Name               Status       Duplex  Speed Type
Fa0/1     PC-A              connected    a-full  a-100 10/100BaseTX
```

### Auto-MDIX Availability

**Important Notes:**
- Auto-MDIX is **enabled by default** on Catalyst 2960 and 3560 switches
- Auto-MDIX is **NOT available** on older Catalyst 2950 and 3550 switches
- Always verify availability on your specific switch model

---

## Verification Commands {#verification-commands}

### Essential Switch Verification Commands

| Task | Command |
|------|---------|
| Display interface status and configuration | `show interfaces [interface-id]` |
| Display startup configuration | `show startup-config` |
| Display running configuration | `show running-config` |
| Display flash file system | `show flash` |
| Display IOS version and hardware | `show version` |
| Display command history | `show history` |
| Display IP information | `show ip interface [interface-id]` |
| Display MAC address table | `show mac-address-table` |

### Detailed Verification Examples

#### 1. Check Interface Status (Detailed)

```
S1# show interfaces fastethernet 0/18
```

**Output:**
```
FastEthernet0/18 is up, line protocol is up (connected)
  Hardware is Fast Ethernet, address is 0025.83e6.9092 (bia 0025.83e6.9092)
  MTU 1500 bytes, BW 100000 Kbit/sec, DLY 100 usec,
  reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Full-duplex, 100Mb/s, media type is 10/100BaseTX
```

**Key Information:**
- **Interface status**: `up` = physically connected
- **Line protocol**: `up` = Layer 2 operational
- **MAC address**: `0025.83e6.9092`
- **Speed**: `BW 100000 Kbit/sec` = 100 Mbps
- **Duplex**: `Full-duplex`

#### 2. Check Running Configuration

```
S1# show running-config
```

**Sample Output:**
```
Building configuration...

Current configuration : 1466 bytes
!
hostname S1
!
interface FastEthernet0/18
 switchport mode access
 duplex full
 speed 100
!
interface GigabitEthernet1/0/1
 description Link to Router
 duplex auto
 speed auto
!
ip default-gateway 172.17.99.1
```

#### 3. Check Interface Summary

```
S1# show ip interface brief
```

**Output:**
```
Interface              IP-Address      OK? Method Status                Protocol
Vlan99                 172.17.99.11    YES manual up                    up
FastEthernet0/1        unassigned      YES unset  up                    up
FastEthernet0/6        unassigned      YES unset  up                    up
GigabitEthernet1/0/1   unassigned      YES unset  down                  down
```

**Status Meanings:**
- **up/up** - Interface is working properly
- **up/down** - Physical layer is up, but data link layer has issues
- **down/down** - Cable unplugged or interface disabled
- **administratively down** - Interface manually shut down with `shutdown` command

#### 4. Check Specific Interface Details

```
S1# show interfaces fastethernet 0/18 status
```

**Output:**
```
Port      Name               Status       Duplex  Speed Type
Fa0/18    PC-A              connected    a-full  a-100 10/100BaseTX
```

**Column meanings:**
- **a-full** = Auto-negotiated full-duplex
- **a-100** = Auto-negotiated 100 Mbps
- **connected** = Interface is up and working

#### 5. View MAC Address Table

```
S1# show mac-address-table
```

**Output:**
```
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0025.83e6.9092   DYNAMIC     Fa0/18
   1    00d0.58ad.05fa   DYNAMIC     Fa0/6
Total Mac Addresses for this criterion: 2
```

---

## Configuring SSH for Secure Remote Access {#ssh-configuration}

### Why SSH Instead of Telnet?

| Feature | Telnet | SSH |
|---------|--------|-----|
| **Encryption** | ❌ None (plaintext) | ✅ Yes (encrypted) |
| **Password Security** | ❌ Sent in plaintext | ✅ Encrypted |
| **Commands Security** | ❌ Visible on network | ✅ Encrypted |
| **Authentication** | Basic | Strong |
| **Security Level** | INSECURE | SECURE |

**Critical**: Never use Telnet in production networks. It's only acceptable for lab environments.

### Verify SSH Support

Before configuring SSH, verify your switch IOS supports cryptographic features:

```
S1# show version
```

**Look for:**
```
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE7
```

**Key identifier**: **"k9"** in the filename indicates cryptographic (SSH) support.

**IOS Image Types:**
- **c2960-lanbasek9-mz** - Includes crypto features (SSH capable) ✅
- **c2960-lanbase-mz** - NO crypto features (SSH NOT available) ❌

---

### SSH Configuration Steps

#### Step 1: Configure Hostname

```
S1(config)# hostname S1
```

**Why needed**: SSH uses the hostname in the encryption key generation.

#### Step 2: Configure Domain Name

```
S1(config)# ip domain-name cisco.com
```

**Why needed**: Required for generating the RSA encryption keys. Combined with hostname (S1.cisco.com).

#### Step 3: Generate RSA Encryption Keys

```
S1(config)# crypto key generate rsa
```

**Interactive prompts:**
```
The name for the keys will be: S1.cisco.com
Choose the size of the key modulus in the range of 360 to 4096 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 2048
```

**Key Size Recommendations:**
- **1024 bits** - Minimum for security
- **2048 bits** - **RECOMMENDED** for good security
- **4096 bits** - Maximum security (slower performance)

**Note**: Larger keys = more secure but slower performance and longer generation time.

#### Step 4: Create Local Username and Password

```
S1(config)# username admin privilege 15 secret Cisco123!
```

**Command breakdown:**
- `username admin` - Creates user "admin"
- `privilege 15` - Highest privilege level (full access)
- `secret Cisco123!` - Strong encrypted password

**Password Best Practices:**
- Minimum 8 characters
- Mix of uppercase, lowercase, numbers, symbols
- Never use common words or patterns

#### Step 5: Enable SSH Version 2

```
S1(config)# ip ssh version 2
```

**Why SSH version 2?**
- More secure than version 1
- Better encryption algorithms
- Industry standard
- SSH version 1 has known vulnerabilities

#### Step 6: Configure VTY Lines for SSH

```
S1(config)# line vty 0 15
S1(config-line)# transport input ssh
S1(config-line)# login local
S1(config-line)# exit
```

**Command explanations:**
- `line vty 0 15` - Configure all 16 virtual terminal lines (0-15)
- `transport input ssh` - **ONLY allow SSH** (blocks Telnet)
- `login local` - Use local username/password database

**Alternative** (Allow both SSH and Telnet - NOT recommended):
```
S1(config-line)# transport input ssh telnet
```

#### Step 7: Configure Console Line (Optional but Recommended)

```
S1(config)# line console 0
S1(config-line)# password ConsolePass123
S1(config-line)# login
S1(config-line)# logging synchronous
S1(config-line)# exit
```

#### Step 8: Configure Enable Secret

```
S1(config)# enable secret class
```

This password protects privileged EXEC mode access.

#### Step 9: Save Configuration

```
S1(config)# end
S1# copy running-config startup-config
```

---

### Complete SSH Configuration Example

```cisco
! Step 1: Set hostname
S1(config)# hostname S1

! Step 2: Set domain name
S1(config)# ip domain-name cisco.com

! Step 3: Generate RSA keys
S1(config)# crypto key generate rsa
[Choose 2048 bits when prompted]

! Step 4: Create local user account
S1(config)# username admin privilege 15 secret Cisco123!

! Step 5: Enable SSH version 2
S1(config)# ip ssh version 2

! Step 6: Optional - Set SSH timeout and retries
S1(config)# ip ssh time-out 60
S1(config)# ip ssh authentication-retries 3

! Step 7: Configure VTY lines for SSH only
S1(config)# line vty 0 15
S1(config-line)# transport input ssh
S1(config-line)# login local
S1(config-line)# exec-timeout 5 0
S1(config-line)# exit

! Step 8: Configure enable password
S1(config)# enable secret class

! Step 9: Optional - Encrypt all passwords
S1(config)# service password-encryption

! Step 10: Save configuration
S1(config)# end
S1# copy running-config startup-config
```

---

### Verify SSH Configuration

#### 1. Check SSH Status

```
S1# show ip ssh
```

**Output:**
```
SSH Enabled - version 2.0
Authentication timeout: 60 secs; Authentication retries: 3
Minimum expected Diffie Hellman key size : 1024 bits
IOS Keys in SECSH format(ssh-rsa, base64 encoded):
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAgQDO...
```

#### 2. View SSH Sessions

```
S1# show ssh
```

**Output:**
```
Connection Version Mode Encryption  Hmac         State           Username
0          2.0     IN   aes128-cbc  hmac-sha1    Session started admin
0          2.0     OUT  aes128-cbc  hmac-sha1    Session started admin

%No SSHv1 server connections running.
```

#### 3. Check Active Users

```
S1# show users
```

**Output:**
```
    Line       User       Host(s)              Idle       Location
*  0 con 0                idle                 00:00:00   
   2 vty 0     admin      idle                 00:00:03 192.168.1.10

  Interface    User               Mode         Idle     Peer Address
```

#### 4. Verify Crypto Keys

```
S1# show crypto key mypubkey rsa
```

**Output:**
```
% Key pair was generated at: 14:25:31 UTC Oct 13 2025
Key name: S1.cisco.com
 Storage Device: private-config
 Usage: General Purpose Key
 Key is not exportable.
 Key Data:
  305C300D 06092A86 4870D...
```

---

### Testing SSH Connection

#### From Windows PC
```
C:\> ssh admin@192.168.1.2
```

#### From Cisco Device or Linux
```
PC> ssh -l admin 192.168.1.2
```

**Login process:**
```
Password: [Enter password - Cisco123!]

S1> enable
Password: [Enter enable secret - class]
S1#
```

---

### SSH Configuration Tips & Best Practices

**Security Best Practices:**
1. ✅ **Always use SSH version 2** (never version 1)
2. ✅ **Use strong passwords** (minimum 8 characters, mixed case, numbers, symbols)
3. ✅ **Disable Telnet completely** - `transport input ssh` only
4. ✅ **Set timeouts** - `exec-timeout 5 0` (5 minutes)
5. ✅ **Limit authentication retries** - `ip ssh authentication-retries 3`
6. ✅ **Use privilege 15** for admin accounts
7. ✅ **Enable password encryption** - `service password-encryption`

**Common Mistakes to Avoid:**
1. ❌ Forgetting to set domain name before generating RSA keys
2. ❌ Using key size less than 1024 bits
3. ❌ Not configuring `login local` on VTY lines
4. ❌ Allowing both SSH and Telnet (security risk)
5. ❌ Not saving configuration after setup
6. ❌ Using weak passwords

---

### Deleting and Regenerating SSH Keys

If you need to delete existing keys (change hostname/domain or troubleshoot):

```
S1(config)# crypto key zeroize rsa
```

**Warning**: This will delete all RSA keys and disable SSH!

Then regenerate:
```
S1(config)# crypto key generate rsa
[Enter 2048 when prompted]
```

---

## Troubleshooting Network Access Layer {#troubleshooting}

### Troubleshooting Flowchart Process

```
START: Network connectivity issue
    ↓
1. Perform "show interfaces"
    ↓
Is the interface UP? ──────┐
    ↓ YES                  ↓ NO
    ↓                      ↓
2a. Check for EMI/noise    2b. Physical troubleshooting:
    - Remove sources           • Verify proper cables
    - Check duplex settings    • Check cables/connectors for damage
    on both ends              • Verify speed settings on both ends
    ↓                         ↓
    └────────┬────────────────┘
             ↓
    Is the problem solved?
       ↓ YES        ↓ NO
       ↓            ↓
    DONE     Document and escalate
```

### Step-by-Step Troubleshooting Guide

#### Step 1: Check Interface Status

```
S1# show interfaces
```

Look for interface state indicators:
- **up/up** - Working correctly ✅
- **up/down** - Physical OK, protocol issue
- **down/down** - Cable or hardware problem
- **administratively down** - Interface shut down

#### Step 2: If Interface is DOWN

**Physical Layer Issues:**

1. **Verify Cables:**
   ```
   • Check cable is properly seated
   • Inspect for damage or kinks
   • Test with known-good cable
   • Verify correct cable type (unless auto-MDIX enabled)
   ```

2. **Check Connectors:**
   ```
   • Inspect RJ-45 connectors for damage
   • Check for bent pins
   • Verify locking tab is engaged
   ```

3. **Verify Speed Settings:**
   ```
   S1(config)# interface fa0/1
   S1(config-if)# speed auto
   S1(config-if)# duplex auto
   ```

4. **Check Interface is Not Shutdown:**
   ```
   S1(config-if)# no shutdown
   ```

#### Step 3: If Interface is UP

**Data Link Layer Issues:**

1. **Check for EMI (Electromagnetic Interference):**
   ```
   • Move cables away from:
     - Fluorescent lights
     - Electric motors
     - Power cables
     - Microwave ovens
   ```

2. **Verify Duplex Settings:**
   
   **Duplex mismatch** causes:
   - Slow performance
   - Intermittent connectivity
   - Collisions and errors

   ```
   S1# show interfaces fa0/1
   
   ! Check for:
   Full-duplex, 100Mb/s  ← Should match on both ends
   ```

   **Fix duplex mismatch:**
   ```
   S1(config)# interface fa0/1
   S1(config-if)# duplex full
   ```
   
   **Or use auto-negotiation:**
   ```
   S1(config-if)# duplex auto
   S1(config-if)# speed auto
   ```

3. **Check for Errors:**
   ```
   S1# show interfaces fa0/1
   
   Look for:
   • Input errors
   • CRC errors
   • Output errors
   • Collisions (should be 0 in full-duplex)
   ```

4. **Verify Speed Match:**
   ```
   Both ends must have same speed:
   • 10 Mbps ←→ 10 Mbps
   • 100 Mbps ←→ 100 Mbps
   • 1000 Mbps ←→ 1000 Mbps
   ```

#### Step 4: Document and Escalate

If problem persists after these steps:

1. **Document:**
   ```
   • What symptoms are occurring
   • What tests were performed
   • Output of show commands
   • Changes that were made
   ```

2. **Escalate** to senior network engineer or vendor support

---

### Common Interface Issues and Solutions

| Problem | Symptoms | Solution |
|---------|----------|----------|
| **Cable Unplugged** | Interface down/down | Check physical connection |
| **Wrong Cable Type** | Interface down or errors | Use correct cable (or enable auto-MDIX) |
| **Duplex Mismatch** | Slow speed, errors | Set both ends to auto or same duplex |
| **Speed Mismatch** | Interface down or errors | Set both ends to auto or same speed |
| **EMI/Noise** | Intermittent errors | Move cables away from interference sources |
| **Bad Cable** | Intermittent or no connection | Replace cable |
| **Administratively Down** | Interface shows admin down | Issue `no shutdown` command |

---

### Useful Troubleshooting Commands

```cisco
! Check interface status summary
S1# show ip interface brief

! Check specific interface details
S1# show interfaces fa0/1

! Check interface statistics and errors
S1# show interfaces fa0/1 statistics

! Check duplex and speed
S1# show interfaces fa0/1 status

! Clear interface counters (for testing)
S1# clear counters fa0/1

! Check auto-MDIX status
S1# show controllers ethernet-controller fa0/1 phy | include MDIX

! Verify cable connectivity
S1# show cable-diagnostics tdr interface fa0/1
```

---

### Critical Troubleshooting Notes

**Duplex/Speed Autonegotiation Failure:**

When one end is set to auto and the other is manually configured, autonegotiation can fail:

**Mismatched Settings Create:**
- One side operates in full-duplex
- Other side defaults to half-duplex
- Result: Poor performance and errors

**Solution:**
- **Best Practice**: Set BOTH ends to auto/auto
- **Alternative**: Set BOTH ends to same manual settings (e.g., 100/full)
- **Never**: Mix auto on one end with manual on the other

---

## Quick Reference Summary

### Basic Port Configuration Commands
```cisco
interface fastethernet 0/1
 description [description]
 duplex {auto | full | half}
 speed {auto | 10 | 100 | 1000}
 mdix auto
 no shutdown
```

### SSH Configuration Commands
```cisco
hostname [name]
ip domain-name [domain]
crypto key generate rsa
[Choose 2048 bits]
username [name] privilege 15 secret [password]
ip ssh version 2
line vty 0 15
 transport input ssh
 login local
```

### Essential Verification Commands
```cisco
show interfaces [interface-id]
show ip interface brief
show running-config
show version
show ip ssh
show ssh
show mac-address-table
```

### Troubleshooting Commands
```cisco
show interfaces [interface-id]
show interfaces [interface-id] status
show controllers ethernet-controller [int] phy
clear counters [interface-id]
```

---

**End of Guide** - You now have a complete understanding of switch port configuration and SSH security for CCNA!
