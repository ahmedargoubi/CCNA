# AAA (Authentication, Authorization, Accounting) Guide

## What is AAA?

**AAA** is a security framework for controlling access to network devices and tracking user activities.

### The Three A's

**Authentication** - Who are you?  
**Authorization** - What are you allowed to do?  
**Accounting** - What did you do?

---

## 1. Authentication (Who are you?)

**Purpose**: Verify user identity before granting access

### Methods

**Local Password (No AAA)**
```cisco
line console 0
 password cisco
 login
```
- Simple username/password on device
- No centralized management
- Used for small networks

**Local Username Database**
```cisco
username admin privilege 15 secret Cisco123!
line console 0
 login local
```
- Usernames stored on device
- Better than single password
- Still not centralized

**AAA with External Server**
```cisco
aaa new-model
aaa authentication login default group tacacs+ local
```
- Uses external server (TACACS+ or RADIUS)
- Centralized management
- Fallback to local if server unavailable

---

## 2. AAA Components

### Three Main Components

#### TACACS+ (Terminal Access Controller Access-Control System Plus)
- **Cisco proprietary**
- **Encrypts entire packet** (more secure)
- **TCP port 49**
- Separates AAA functions
- Best for device administration

#### RADIUS (Remote Authentication Dial-In User Service)
- **Industry standard**
- **Encrypts only password** (less secure than TACACS+)
- **UDP ports 1812/1813** (authentication/accounting)
- Combines authentication and authorization
- Best for network access (Wi-Fi, VPN)

#### Local Database
- Stored on device itself
- Fallback option
- No external server needed

### Comparison

| Feature | TACACS+ | RADIUS |
|---------|---------|--------|
| **Vendor** | Cisco proprietary | Industry standard |
| **Encryption** | Entire packet | Password only |
| **Protocol** | TCP (port 49) | UDP (1812, 1813) |
| **AAA Separation** | Yes (separate functions) | No (combined) |
| **Use Case** | Device admin | Network access |

---

## 3. Authentication Process

### How Authentication Works

```
User attempts login
    ↓
Device checks authentication method
    ↓
Option 1: Local password → Check local database
Option 2: AAA server → Send request to TACACS+/RADIUS
    ↓
Server verifies credentials
    ↓
Grant or Deny access
```

### Authentication Methods

**Login Authentication** (Console, VTY, AUX)
```cisco
aaa authentication login default group tacacs+ local
```

**Enable Authentication** (Privileged EXEC)
```cisco
aaa authentication enable default group tacacs+ enable
```

**Order of Methods**:
- Try TACACS+ first
- If unavailable, use local database
- If local fails, deny access

---

## 4. Authorization (What can you do?)

**Purpose**: Control what authenticated users can do

### Authorization Types

**EXEC Authorization** - Control command access
```cisco
aaa authorization exec default group tacacs+ local
```

**Commands Authorization** - Control specific command levels
```cisco
aaa authorization commands 15 default group tacacs+ local
```

**Network Authorization** - Control network service access

### Privilege Levels

| Level | Access |
|-------|--------|
| **0** | User EXEC (limited) |
| **1** | User EXEC (default) |
| **2-14** | Custom levels |
| **15** | Privileged EXEC (full access) |

**Example**:
```cisco
username admin privilege 15 secret Admin123!
username helpdesk privilege 5 secret Help123!
```

---

## 5. Accounting (What did you do?)

**Purpose**: Track and log user activities

### Accounting Records

**What is Logged**:
- Who logged in
- When they logged in
- What commands were executed
- When they logged out
- Connection duration

### Accounting Types

**EXEC Accounting** - Track login sessions
```cisco
aaa accounting exec default start-stop group tacacs+
```

**Commands Accounting** - Track commands executed
```cisco
aaa accounting commands 15 default start-stop group tacacs+
```

**Network Accounting** - Track network connections

### Start-Stop vs Stop-Only

**start-stop**: Send accounting record at beginning and end  
**stop-only**: Send accounting record only at end

---

## 6. 802.1X (Port-Based Authentication)

**Purpose**: Authenticate devices before granting network access

### Components

**Supplicant** - Device requesting access (PC, phone)  
**Authenticator** - Switch controlling access  
**Authentication Server** - RADIUS server verifying credentials

### How 802.1X Works

```
1. Device connects to switch port
2. Switch blocks all traffic (except 802.1X)
3. Device sends credentials to switch
4. Switch forwards to RADIUS server
5. RADIUS verifies credentials
6. If valid: Switch opens port
   If invalid: Port remains blocked
```

### Configuration Example

**On Switch**:
```cisco
aaa new-model
aaa authentication dot1x default group radius
dot1x system-auth-control

interface fastethernet 0/1
 switchport mode access
 authentication port-control auto
 dot1x pae authenticator
```

**States**:
- **auto**: Port requires 802.1X authentication
- **force-authorized**: Port always open (no authentication)
- **force-unauthorized**: Port always blocked

---

## AAA Configuration Examples

### Basic Local Authentication
```cisco
! Create local users
username admin privilege 15 secret Admin123!
username user1 privilege 1 secret User123!

! Apply to console
line console 0
 login local

! Apply to VTY (SSH/Telnet)
line vty 0 4
 login local
 transport input ssh
```

---

### AAA with TACACS+ Server
```cisco
! Enable AAA
aaa new-model

! Define TACACS+ server
tacacs-server host 192.168.1.100 key SecretKey123

! Configure authentication (try TACACS+, fallback to local)
aaa authentication login default group tacacs+ local

! Configure authorization
aaa authorization exec default group tacacs+ local

! Configure accounting
aaa accounting exec default start-stop group tacacs+
aaa accounting commands 15 default start-stop group tacacs+

! Apply to lines
line console 0
 login authentication default

line vty 0 4
 login authentication default
 authorization exec default
```

---

### AAA with RADIUS Server
```cisco
! Enable AAA
aaa new-model

! Define RADIUS server
radius-server host 192.168.1.100 key SecretKey123

! Configure authentication
aaa authentication login default group radius local

! Configure authorization
aaa authorization network default group radius

! Configure accounting
aaa accounting network default start-stop group radius
```

---

## Verification Commands

```cisco
! View AAA configuration
show aaa servers
show aaa sessions

! View authentication methods
show aaa authentication

! View TACACS+ configuration
show tacacs

! View RADIUS configuration  
show radius statistics

! Debug AAA (use carefully)
debug aaa authentication
debug aaa authorization
debug aaa accounting
```

---

## Local vs AAA Server

### When to Use Local

**Use Local When**:
- Small network (few devices)
- Limited budget
- No external servers available
- Backup/fallback method

**Advantages**:
- Simple configuration
- No external dependencies
- Works if network fails

**Disadvantages**:
- Not centralized
- Difficult to manage at scale
- No detailed logging
- Must configure each device

---

### When to Use AAA Server

**Use AAA Server When**:
- Many network devices
- Centralized management needed
- Detailed auditing required
- Enterprise environment

**Advantages**:
- Centralized user management
- Detailed accounting logs
- Easier to add/remove users
- Single point of configuration

**Disadvantages**:
- Requires external server
- More complex setup
- Single point of failure (use fallback)

---

## Best Practices

✅ **Always configure local fallback** (in case AAA server fails)  
✅ **Use strong encryption keys** for TACACS+/RADIUS  
✅ **Enable accounting** for audit trails  
✅ **Use TACACS+ for device admin** (full encryption)  
✅ **Use RADIUS for network access** (802.1X, VPN)  
✅ **Implement privilege levels** (don't give everyone level 15)  
✅ **Use 802.1X** for port security on access switches  
✅ **Monitor AAA logs** regularly  

❌ **Don't use simple passwords** on line console  
❌ **Don't skip accounting** (you need audit trails)  
❌ **Don't give all users privilege 15**  
❌ **Don't forget backup methods**  

---

## CCNA Exam Key Points

**Know**:
- AAA = Authentication, Authorization, Accounting
- TACACS+ = Cisco, TCP, full encryption, device admin
- RADIUS = Standard, UDP, password encryption, network access
- 802.1X = Port-based authentication with supplicant/authenticator/server
- Privilege levels: 1 (user), 15 (privileged)
- Local vs AAA server use cases

**Commands**:
- `aaa new-model` - Enable AAA
- `aaa authentication login` - Configure login authentication
- `aaa authorization exec` - Configure authorization
- `aaa accounting exec` - Configure accounting
- `login local` - Use local username database

**Common Questions**:
- "What's difference between TACACS+ and RADIUS?" → Encryption level, protocol
- "What does 802.1X authenticate?" → Devices connecting to switch ports
- "What are the three parts of AAA?" → Authentication, Authorization, Accounting
- "Which for device admin?" → TACACS+
- "Which for Wi-Fi/VPN?" → RADIUS

---

## Quick Reference

### AAA Order
```
1. Authentication - Verify identity
2. Authorization - Check permissions
3. Accounting - Log activities
```

### Essential Commands
```cisco
! Enable AAA
aaa new-model

! TACACS+ server
tacacs-server host [IP] key [password]

! RADIUS server
radius-server host [IP] key [password]

! Authentication
aaa authentication login default group tacacs+ local

! Authorization
aaa authorization exec default group tacacs+ local

! Accounting
aaa accounting exec default start-stop group tacacs+
```

---

*AAA provides centralized security management for network device access.*