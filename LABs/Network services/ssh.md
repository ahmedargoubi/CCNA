# Complete Guide: Configure Network Devices with SSH

## Table of Contents
1. [Lab Overview](#lab-overview)
2. [Topology and Addressing](#topology-and-addressing)
3. [Required Equipment](#required-equipment)
4. [Part 1: Basic Device Settings](#part-1-basic-device-settings)
5. [Part 2: Configure Router for SSH Access](#part-2-configure-router-for-ssh-access)
6. [Part 3: Configure Switch for SSH Access](#part-3-configure-switch-for-ssh-access)
7. [Part 4: SSH from CLI on Switch](#part-4-ssh-from-cli-on-switch)
8. [SSH Security Best Practices](#ssh-security-best-practices)
9. [Troubleshooting SSH Issues](#troubleshooting-ssh-issues)
10. [Key Commands Reference](#key-commands-reference)
11. [Reflection Questions](#reflection-questions)

## Lab Overview

### Objectives
- Configure basic device settings on router and switch
- Configure the router for SSH access
- Configure the switch for SSH access
- Establish SSH connections from PC and CLI
- Understanding SSH security advantages over Telnet

### Background
**Why SSH over Telnet?**

**Telnet Issues:**
- Transmits data in clear text format
- Passwords and configuration information can be intercepted
- No encryption or authentication
- Security vulnerability for network management

**SSH Advantages:**
- Encrypts all information over network link
- Provides authentication of remote computer
- Secure terminal emulation connection
- Supports file transfer via SFTP and SCP protocols
- Industry standard for secure remote access

## Topology and Addressing

### Network Topology
```
PC-A ---- S1 ---- R1
```

### Addressing Table
| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|-----------------|
| R1 | G0/0/1 | 192.168.1.1 | 255.255.255.0 | N/A |
| S1 | VLAN 1 | 192.168.1.11 | 255.255.255.0 | 192.168.1.1 |
| PC-A | NIC | 192.168.1.3 | 255.255.255.0 | 192.168.1.1 |

## Required Equipment
- 1 Router (Cisco 4221 with Cisco IOS XE Release 16.9.4 or comparable)
- 1 Switch (Cisco 2960 with Cisco IOS Release 15.2(2) or comparable)
- 1 PC (Windows with terminal emulation program like Tera Term)
- Console cables for device configuration
- Ethernet cables as shown in topology

## Part 1: Basic Device Settings

### Step 1: Cable the Network
Connect devices according to the topology:
- PC-A to S1 via Ethernet
- S1 to R1 via Ethernet
- Console cables for initial configuration

### Step 2: Initialize Devices
```bash
# Erase startup configurations and reload
Router> enable
Router# erase startup-config
Router# reload

Switch> enable
Switch# erase startup-config
Switch# reload
```

### Step 3: Configure the Router (R1)

#### Basic Router Configuration
```bash
Router> enable
Router# configure terminal
Router(config)# hostname R1

# Disable DNS lookup
R1(config)# no ip domain-lookup

# Set privileged EXEC password
R1(config)# enable secret class

# Configure console access
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit

# Configure VTY access
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit

# Encrypt plaintext passwords
R1(config)# service password-encryption

# Create warning banner
R1(config)# banner motd #Unauthorized access is prohibited!#

# Configure interface G0/0/1
R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

# Save configuration
R1(config)# exit
R1# copy running-config startup-config
```

### Step 4: Configure PC-A
Configure PC-A with the following network settings:
- **IP Address:** 192.168.1.3
- **Subnet Mask:** 255.255.255.0
- **Default Gateway:** 192.168.1.1

### Step 5: Verify Network Connectivity
```bash
# From PC-A, ping R1
ping 192.168.1.1
```
If ping fails, troubleshoot physical connections and IP configurations.

## Part 2: Configure Router for SSH Access

### Understanding SSH Configuration Requirements
SSH requires the following components:
1. Device hostname (for crypto key generation)
2. Domain name (for crypto key generation)  
3. Encryption key generation
4. Local user database
5. VTY line configuration for SSH

### Step 1: Configure Device Authentication

#### Set Device Name and Domain
```bash
R1(config)# hostname R1
R1(config)# ip domain-name ccna-lab.com
```

**Important:** Device name and domain must be configured before generating crypto keys as they form part of the key.

### Step 2: Configure Encryption Key Method
```bash
R1(config)# crypto key generate rsa
```

**Sample Output:**
```
The name for the keys will be: R1.ccna-lab.com
Choose the size of the key modulus in the range of 360 to 4096 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...
[OK] (elapsed time was 1 seconds)
```

**Key Size Recommendations:**
- **512 bits:** Minimum, faster generation, less secure
- **1024 bits:** Good balance of security and performance
- **2048 bits:** Higher security, slower generation

### Step 3: Configure Local Database Username
```bash
R1(config)# username admin privilege 15 secret Adm1nP@55
```

**Command Breakdown:**
- `username admin` - Creates user account named "admin"
- `privilege 15` - Grants full administrative privileges
- `secret Adm1nP@55` - Sets encrypted password

### Step 4: Enable SSH on VTY Lines
```bash
R1(config)# line vty 0 4
R1(config-line)# transport input telnet ssh
R1(config-line)# login local
R1(config-line)# exit
```

**Command Explanation:**
- `transport input telnet ssh` - Allows both Telnet and SSH (SSH preferred)
- `login local` - Uses local database for authentication

### Step 5: Save Configuration
```bash
R1# copy running-config startup-config
```

### Step 6: Establish SSH Connection from PC-A

#### Using Tera Term or Similar SSH Client
1. Start Tera Term on PC-A
2. Select "SSH" connection type
3. Enter R1's IP address: 192.168.1.1
4. Use credentials:
   - **Username:** admin
   - **Password:** Adm1nP@55

**Expected Result:**
```
login as: admin
admin@192.168.1.1's password:
Unauthorized access is prohibited!
R1>
```

## Part 3: Configure Switch for SSH Access

### Step 1: Configure Basic Switch Settings

```bash
Switch> enable
Switch# configure terminal
Switch(config)# hostname S1

# Disable DNS lookup
S1(config)# no ip domain-lookup

# Set privileged EXEC password
S1(config)# enable secret class

# Configure console access
S1(config)# line console 0
S1(config-line)# password cisco
S1(config-line)# login
S1(config-line)# exit

# Configure VTY access
S1(config)# line vty 0 15
S1(config-line)# password cisco
S1(config-line)# login
S1(config-line)# exit

# Encrypt plaintext passwords
S1(config)# service password-encryption

# Create warning banner
S1(config)# banner motd #Unauthorized access is prohibited!#

# Configure VLAN 1 interface
S1(config)# interface vlan 1
S1(config-if)# ip address 192.168.1.11 255.255.255.0
S1(config-if)# no shutdown
S1(config-if)# exit

# Set default gateway
S1(config)# ip default-gateway 192.168.1.1

# Save configuration
S1(config)# exit
S1# copy running-config startup-config
```

### Step 2: Configure Switch for SSH Connectivity

#### Apply Same SSH Configuration as Router
```bash
# Set domain name
S1(config)# ip domain-name ccna-lab.com

# Generate RSA keys
S1(config)# crypto key generate rsa
# Choose 1024 bits when prompted

# Create local user account
S1(config)# username admin privilege 15 secret Adm1nP@55

# Configure VTY lines for SSH
S1(config)# line vty 0 15
S1(config-line)# transport input telnet ssh
S1(config-line)# login local
S1(config-line)# exit
```

### Step 3: Establish SSH Connection to Switch

#### Test SSH Access from PC-A
1. Start Tera Term on PC-A
2. SSH to S1's VLAN 1 interface: 192.168.1.11
3. Use credentials:
   - **Username:** admin
   - **Password:** Adm1nP@55

**Verification Question:** Are you able to establish an SSH session with the switch?
**Answer:** Yes, if all configurations are correct.

## Part 4: SSH from CLI on Switch

### Understanding Built-in SSH Client
Cisco IOS includes a built-in SSH client accessible from the CLI, allowing device-to-device SSH connections without external software.

### Step 1: View SSH Client Parameters
```bash
S1# ssh ?
  -c        Select encryption algorithm
  -l        Log in using this user name
  -m        Select HMAC algorithm  
  -o        Specify options
  -p        Connect to this port
  -v        Specify SSH Protocol Version
  -vrf      Specify vrf name
  WORD      IP address or hostname of a remote system
```

### Step 2: SSH from S1 to R1

#### Establish SSH Connection
```bash
S1# ssh -l admin 192.168.1.1
Password: Adm1nP@55

Unauthorized access is prohibited!
R1>
```

#### Managing SSH Sessions

**Suspend SSH Session (Return to S1):**
- Press `Ctrl+Shift+6`, then release and press `x`
```bash
R1>
S1#
```

**Resume SSH Session:**
- Press `Enter` on blank line (may need to press twice)
```bash
S1#
[Resuming connection 1 to 192.168.1.1 ... ]
R1>
```

**End SSH Session:**
```bash
R1> exit
[Connection to 192.168.1.1 closed by foreign host]
S1#
```

### Step 3: View Active Sessions
```bash
S1# show sessions
Conn Host                Address             Byte  Idle Conn Name
   1 192.168.1.1         192.168.1.1            0     0 192.168.1.1
```

**SSH Version Support Question:** What versions of SSH are supported from the CLI?
**Answer:** Both SSH version 1 and version 2 are typically supported, with version 2 being the default and recommended for better security.

## SSH Security Best Practices

### Password Security
1. **Use Strong Passwords:**
   - Minimum 8 characters
   - Mix of uppercase, lowercase, numbers, symbols
   - Example: `Adm1nP@55`, `S3cure#2024`

2. **Regular Password Changes:**
   - Change passwords periodically
   - Don't reuse old passwords
   - Document password change schedule

### Key Management
1. **Use Appropriate Key Sizes:**
   - Minimum 1024 bits
   - Prefer 2048 bits for high-security environments
   - Regenerate keys periodically

2. **Key Generation:**
```bash
# Generate new keys (replaces existing)
Router(config)# crypto key generate rsa modulus 2048
```

### Access Control
1. **Limit SSH Access:**
```bash
# Restrict SSH to specific VTY lines
Router(config)# line vty 0 1
Router(config-line)# transport input ssh
```

2. **Use Access Control Lists:**
```bash
# Create ACL to limit SSH sources
Router(config)# access-list 1 permit 192.168.1.0 0.0.0.255
Router(config)# line vty 0 4
Router(config-line)# access-class 1 in
```

### Multiple User Management
```bash
# Create multiple users with different privilege levels
Router(config)# username admin privilege 15 secret AdminP@ss123
Router(config)# username operator privilege 5 secret OperP@ss123
Router(config)# username readonly privilege 1 secret ReadP@ss123
```

## Troubleshooting SSH Issues

### Common Problems and Solutions

#### 1. SSH Connection Refused
**Symptoms:**
- "Connection refused" error
- Cannot establish SSH session

**Possible Causes & Solutions:**
```bash
# Check if SSH is enabled
Router# show ip ssh

# Verify RSA keys exist
Router# show crypto key mypubkey rsa

# Check VTY line configuration
Router# show running-config | section line vty

# Ensure SSH is allowed on VTY lines
Router(config)# line vty 0 4
Router(config-line)# transport input ssh
```

#### 2. Authentication Failed
**Symptoms:**
- "Authentication failed" message
- Correct username/password rejected

**Solutions:**
```bash
# Verify local user database
Router# show running-config | section username

# Check authentication method
Router# show running-config | section line vty

# Ensure login local is configured
Router(config)# line vty 0 4
Router(config-line)# login local
```

#### 3. SSH Service Not Available
**Symptoms:**
- SSH commands not recognized
- Missing SSH functionality

**Solutions:**
```bash
# Check IOS image supports SSH
Router# show version | include image

# Verify domain name is configured
Router# show running-config | include ip domain-name

# Regenerate RSA keys
Router(config)# crypto key generate rsa modulus 1024
```

#### 4. No Response from SSH Connection
**Symptoms:**
- SSH client hangs
- No prompt appears

**Solutions:**
```bash
# Check network connectivity
Router# ping 192.168.1.1

# Verify interface status
Router# show ip interface brief

# Check for ACL blocking SSH
Router# show access-lists
```

### Diagnostic Commands
```bash
# View SSH configuration
show ip ssh

# Check active SSH sessions
show ssh

# View RSA key information
show crypto key mypubkey rsa

# Check VTY line status
show line vty 0 4

# View user accounts
show running-config | section username
```

## Key Commands Reference

### Basic SSH Configuration
```bash
# Configure hostname and domain
hostname [name]
ip domain-name [domain]

# Generate RSA keys
crypto key generate rsa [modulus size]

# Create local user
username [name] privilege [level] secret [password]

# Configure VTY lines
line vty [start] [end]
transport input [telnet] [ssh]
login local
```

### SSH Client Commands
```bash
# SSH to remote device
ssh -l [username] [ip-address]

# SSH with specific options
ssh -v [version] -l [username] [ip-address]

# View active sessions
show sessions

# Resume suspended session
[Enter key]

# End SSH session
exit
```

### Verification Commands
```bash
# Show SSH configuration
show ip ssh

# Show active SSH connections
show ssh

# Show RSA key details
show crypto key mypubkey rsa

# Show line configuration
show line

# Show running configuration sections
show running-config | section [keyword]
```

### Security Commands
```bash
# Set password encryption
service password-encryption

# Configure access class on VTY lines
line vty 0 4
access-class [acl-number] in

# Set SSH version
ip ssh version [1|2]

# Set SSH timeout
ip ssh time-out [seconds]

# Set SSH authentication retries
ip ssh authentication-retries [number]
```

## Router Interface Summary Reference

| Router Model | Ethernet Interface #1 | Ethernet Interface #2 | Serial Interface #1 | Serial Interface #2 |
|--------------|----------------------|---------------------|-------------------|-------------------|
| 1800 | Fast Ethernet 0/0 (F0/0) | Fast Ethernet 0/1 (F0/1) | Serial 0/0/0 (S0/0/0) | Serial 0/0/1 (S0/0/1) |
| 1900 | Gigabit Ethernet 0/0 (G0/0) | Gigabit Ethernet 0/1 (G0/1) | Serial 0/0/0 (S0/0/0) | Serial 0/0/1 (S0/0/1) |
| 2801 | Fast Ethernet 0/0 (F0/0) | Fast Ethernet 0/1 (F0/1) | Serial 0/1/0 (S0/1/0) | Serial 0/1/1 (S0/1/1) |
| 2811 | Fast Ethernet 0/0 (F0/0) | Fast Ethernet 0/1 (F0/1) | Serial 0/0/0 (S0/0/0) | Serial 0/0/1 (S0/0/1) |
| 2900 | Gigabit Ethernet 0/0 (G0/0) | Gigabit Ethernet 0/1 (G0/1) | Serial 0/0/0 (S0/0/0) | Serial 0/0/1 (S0/0/1) |
| 4221 | Gigabit Ethernet 0/0/0 (G0/0/0) | Gigabit Ethernet 0/0/1 (G0/0/1) | Serial 0/1/0 (S0/1/0) | Serial 0/1/1 (S0/1/1) |
| 4300 | Gigabit Ethernet 0/0/0 (G0/0/0) | Gigabit Ethernet 0/0/1 (G0/0/1) | Serial 0/1/0 (S0/1/0) | Serial 0/1/1 (S0/1/1) |

## Reflection Questions

### 1. Multiple User Access Management
**Question:** How would you provide multiple users, each with their own username, access to a network device?

**Answer:** Configure multiple local user accounts with appropriate privilege levels:

```bash
# Administrative users (full access)
username admin1 privilege 15 secret AdminPass1!
username admin2 privilege 15 secret AdminPass2!

# Operator users (limited configuration access)
username operator1 privilege 5 secret OperPass1!
username operator2 privilege 5 secret OperPass2!

# Read-only users (monitoring only)
username readonly1 privilege 1 secret ReadPass1!
username readonly2 privilege 1 secret ReadPass2!

# Configure VTY lines to use local authentication
line vty 0 15
login local
transport input ssh
```

**Additional Considerations:**
- Document user accounts and their purposes
- Implement password policies
- Regular account audits and cleanup
- Consider using external authentication (RADIUS/TACACS+) for larger environments

### 2. SSH Version Compatibility
**Question:** What versions of SSH are supported from the CLI?

**Answer:** 
- **SSH Version 1:** Older, less secure, deprecated
- **SSH Version 2:** Current standard, more secure, recommended

**Configuration:**
```bash
# Force SSH version 2 only (recommended)
Router(config)# ip ssh version 2

# Check current SSH version support
Router# show ip ssh
```

### 3. Security Implementation Strategies
**Question:** What additional security measures can enhance SSH access?

**Answer:**
1. **Access Control Lists (ACLs)**
2. **Key-based Authentication** 
3. **SSH Version Restrictions**
4. **Connection Limits**
5. **Logging and Monitoring**

```bash
# Example enhanced security configuration
ip ssh version 2
ip ssh time-out 60
ip ssh authentication-retries 3

# ACL to restrict SSH sources
access-list 10 permit 192.168.1.0 0.0.0.255
line vty 0 4
access-class 10 in
transport input ssh
login local
```

## Lab Summary

This lab demonstrates essential skills for secure network device management:

### Key Learning Outcomes
1. **SSH Configuration:** Understanding complete SSH setup process
2. **Security Awareness:** Recognizing SSH advantages over Telnet
3. **User Management:** Creating and managing local user accounts
4. **Remote Access:** Establishing secure connections from multiple sources
5. **Troubleshooting:** Identifying and resolving SSH connectivity issues

### Practical Applications
- **Network Management:** Secure remote administration
- **Device Maintenance:** Safe configuration updates
- **Multi-user Environments:** Controlled access with user accountability
- **Security Compliance:** Meeting industry standards for remote access

This comprehensive guide provides the foundation for implementing secure remote access to network devices using SSH protocol.
