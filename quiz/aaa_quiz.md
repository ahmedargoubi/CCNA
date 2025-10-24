# CCNA AAA Quiz - 10 Exam-Style Questions

---

### Question 1
What are the three components of the AAA security framework in the correct order?

**A)** Accounting, Authorization, Authentication  
**B)** Authentication, Authorization, Accounting  
**C)** Authorization, Authentication, Accounting  
**D)** Authentication, Accounting, Authorization

---

### Question 2
A network administrator wants to configure centralized authentication for network device management with full packet encryption. Which AAA protocol should be used?

**A)** RADIUS  
**B)** TACACS+  
**C)** LDAP  
**D)** Kerberos

---

### Question 3
What is the primary difference between TACACS+ and RADIUS in terms of security?

**A)** TACACS+ uses UDP while RADIUS uses TCP  
**B)** TACACS+ encrypts the entire packet while RADIUS only encrypts the password  
**C)** TACACS+ is an open standard while RADIUS is Cisco proprietary  
**D)** TACACS+ does not support accounting while RADIUS does

---

### Question 4
Which command is required to enable AAA services on a Cisco router?

**A)** `aaa enable`  
**B)** `aaa authentication enable`  
**C)** `aaa new-model`  
**D)** `enable aaa`

---

### Question 5
An administrator configures the following command on a router:
```
aaa authentication login default group tacacs+ local
```
What will happen if the TACACS+ server is unreachable?

**A)** Access will be denied  
**B)** The router will use the local username database  
**C)** The router will allow access without authentication  
**D)** The router will prompt for the enable password

---

### Question 6
In the context of AAA, what does Authorization control?

**A)** Verifying user identity  
**B)** Logging user activities  
**C)** Determining what actions an authenticated user can perform  
**D)** Encrypting user credentials

---

### Question 7
Which privilege level provides full access to all router commands in privileged EXEC mode?

**A)** Level 0  
**B)** Level 1  
**C)** Level 10  
**D)** Level 15

---

### Question 8
In 802.1X authentication, what is the role of the switch?

**A)** Supplicant  
**B)** Authenticator  
**C)** Authentication server  
**D)** Authorization server

---

### Question 9
Which transport protocol and port does TACACS+ use for communication?

**A)** UDP port 1812  
**B)** TCP port 49  
**C)** UDP port 1645  
**D)** TCP port 389

---

### Question 10
A company wants to track which commands administrators execute on network devices. Which AAA service should be configured?

**A)** Authentication  
**B)** Authorization  
**C)** Accounting  
**D)** Auditing

---

## Answer Key

### Question 1: **B** - Authentication, Authorization, Accounting
**Explanation**: AAA always follows this specific order:
1. **Authentication** - First, verify who the user is (identity verification)
2. **Authorization** - Second, determine what they're allowed to do (permissions)
3. **Accounting** - Third, track what they actually did (logging/auditing)

You cannot authorize someone before knowing who they are, and you cannot account for actions before they're authorized to perform them.

---

### Question 2: **B** - TACACS+
**Explanation**: TACACS+ is the preferred protocol for device administration because:
- It encrypts the **entire packet** (most secure)
- It's specifically designed for device management
- It separates authentication, authorization, and accounting functions
- Uses TCP for reliable communication

RADIUS only encrypts the password and is better suited for network access (VPN, Wi-Fi). TACACS+ is Cisco proprietary but provides superior security for router/switch management.

---

### Question 3: **B** - TACACS+ encrypts the entire packet while RADIUS only encrypts the password
**Explanation**: This is the most significant security difference:
- **TACACS+**: Encrypts the entire packet body, providing complete confidentiality
- **RADIUS**: Only encrypts the password field, leaving other information in plaintext

Other facts to remember:
- TACACS+ uses TCP (not UDP)
- TACACS+ is Cisco proprietary (not RADIUS)
- Both support accounting

---

### Question 4: **C** - `aaa new-model`
**Explanation**: The `aaa new-model` command is the first and essential command to enable AAA services on a Cisco device. Without this command, no AAA configurations will take effect. This command:
- Enables the AAA access control system
- Must be configured before any other AAA commands
- Changes how authentication works on the device

After enabling, you can configure authentication, authorization, and accounting services.

---

### Question 5: **B** - The router will use the local username database
**Explanation**: The command `aaa authentication login default group tacacs+ local` defines a method list:
- **First method**: Try TACACS+ server group
- **Second method** (fallback): Use local database

This provides redundancy. If the TACACS+ server is unreachable (network down, server offline), the router automatically falls back to the local username database stored on the device itself. This prevents complete lockout if the AAA server fails.

If "local" wasn't specified, access would be denied when TACACS+ is unavailable.

---

### Question 6: **C** - Determining what actions an authenticated user can perform
**Explanation**: Authorization is the second "A" in AAA and controls permissions:
- **After authentication** verifies WHO you are
- **Authorization** determines WHAT you can do
- Examples: Which commands you can execute, which privilege level you have, which resources you can access

Authentication = Identity verification  
Authorization = Permission control  
Accounting = Activity logging

---

### Question 7: **D** - Level 15
**Explanation**: Cisco IOS uses privilege levels 0-15:
- **Level 0**: Very limited (only `disable`, `enable`, `exit`, `help`, `logout`)
- **Level 1**: User EXEC mode (default, limited commands like `ping`, `show`)
- **Levels 2-14**: Custom privilege levels (can be configured)
- **Level 15**: Privileged EXEC mode (full access to all commands)

When you configure `username admin privilege 15 secret password`, you're giving that user complete administrative access. Be careful who gets level 15!

---

### Question 8: **B** - Authenticator
**Explanation**: 802.1X uses three components:
1. **Supplicant**: The device requesting access (PC, laptop, IP phone)
2. **Authenticator**: The network device controlling access (typically a **switch**)
3. **Authentication Server**: The RADIUS server that verifies credentials

The switch acts as the authenticator - it:
- Receives credentials from the supplicant
- Forwards them to the authentication server
- Opens or blocks the port based on the server's response

Remember: Switch = Authenticator (middle-man in 802.1X)

---

### Question 9: **B** - TCP port 49
**Explanation**: TACACS+ uses:
- **Protocol**: TCP (reliable, connection-oriented)
- **Port**: 49

RADIUS uses:
- **Protocol**: UDP (connectionless)
- **Ports**: 1812 (authentication), 1813 (accounting) - or legacy 1645/1646

The use of TCP makes TACACS+ more reliable for device administration, as it ensures packet delivery and can detect if the server is unreachable.

---

### Question 10: **C** - Accounting
**Explanation**: Accounting is the third "A" in AAA and provides:
- **Logging** of user activities
- **Tracking** of executed commands
- **Audit trails** for security and compliance
- **Session information** (login time, duration, logout time)

To track commands, use:
```
aaa accounting commands 15 default start-stop group tacacs+
```

This logs all privilege level 15 commands to the TACACS+ server, creating an audit trail of administrative actions.

Authentication = Who logged in  
Authorization = What they can do  
Accounting = What they actually did ✓

---

## Score Interpretation

- **9-10 correct**: Excellent! Ready for AAA exam questions
- **7-8 correct**: Good knowledge, review missed concepts
- **5-6 correct**: Need more study on AAA fundamentals
- **Below 5**: Review the guide and retake quiz

---

## Key Concepts to Review

If you missed questions, focus on:

**Questions 1, 6, 10**: Understand each A's purpose (Authentication, Authorization, Accounting)  
**Questions 2, 3, 9**: TACACS+ vs RADIUS differences (encryption, protocol, ports)  
**Question 4**: Essential AAA commands (`aaa new-model`)  
**Question 5**: AAA method lists and fallback behavior  
**Question 7**: Privilege levels (1 vs 15)  
**Question 8**: 802.1X components (Supplicant, Authenticator, Server)

---

## Common Exam Traps

⚠️ **TACACS+ vs RADIUS**: Don't confuse which is Cisco proprietary (TACACS+) vs open standard (RADIUS)  
⚠️ **Encryption**: TACACS+ encrypts ENTIRE packet, RADIUS only password  
⚠️ **Protocol**: TACACS+ uses TCP, RADIUS uses UDP  
⚠️ **AAA Order**: Must be Authentication → Authorization → Accounting (not any other order)  
⚠️ **802.1X Switch Role**: Switch is Authenticator (not Supplicant or Server)  
⚠️ **Privilege Level**: Level 15 = full access (not 1, 10, or 0)

---

## Quick Memory Aids

**AAA Order**: "**A**lways **A**sk **A**fter" → Authentication, Authorization, Accounting

**TACACS+ vs RADIUS**:
- **T**ACACS+ = **T**CP, **T**otal encryption, **T**acacs is Cisco
- **R**ADIUS = **R**eally only password encrypted, **U**DP protocol

**802.1X Roles**:
- **S**upplicant = **S**eeking access (client device)
- **A**uthenticator = **A**ccess controller (switch)
- Server = Verifies credentials (RADIUS)

**Privilege Levels**:
- Level **1** = **1**st level (user EXEC)
- Level **15** = **15** letters in "privileged EXEC"

---

*Master these concepts and you'll ace AAA questions on the CCNA exam!*