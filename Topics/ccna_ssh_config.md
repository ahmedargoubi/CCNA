# CCNA SSH Configuration Guide
## 6 Main Steps to Configure SSH

---

## Overview of SSH

**SSH (Secure Shell)** is a cryptographic network protocol used for secure remote access to network devices.

**Why use SSH instead of Telnet?**
- **Encryption**: All data is encrypted (Telnet sends in plain text)
- **Authentication**: More secure authentication methods
- **Data Integrity**: Prevents tampering with data in transit

---

## The 6 Main Steps to Configure SSH

### Step 1: Configure Hostname
**Why?** SSH requires a unique hostname for the device.

```cisco
Router(config)# hostname R1
R1(config)#
```

**Explanation**: 
- The hostname is used as part of the encryption key generation
- Default hostname "Router" or "Switch" won't work properly
- Must be changed before generating encryption keys

---

### Step 2: Configure Domain Name
**Why?** SSH requires a domain name to generate the RSA encryption keys.

```cisco
R1(config)# ip domain-name example.com
```

**Explanation**:
- The domain name combines with hostname to create FQDN (Fully Qualified Domain Name)
- FQDN = hostname.domain-name (e.g., R1.example.com)
- This FQDN is used in the RSA key generation process
- Can be any domain name (doesn't have to be real)

---

### Step 3: Generate RSA Encryption Keys
**Why?** SSH uses RSA keys for encryption and authentication.

```cisco
R1(config)# crypto key generate rsa
The name for the keys will be: R1.example.com
Choose the size of the key modulus in the range of 360 to 4096 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 2048
% Generating 2048 bit RSA keys, keys will be non-exportable...
[OK] (elapsed time was 1 seconds)
```

**Key Size Recommendations**:
- **512 bits**: Minimum (not recommended - weak security)
- **1024 bits**: SSH version 2 minimum
- **2048 bits**: **Recommended for CCNA** (good security)
- **4096 bits**: Maximum (very secure but slower)

**Important Notes**:
- Keys are stored in NVRAM
- Generating keys automatically enables SSH
- Key size of 768 bits or higher enables SSHv2

**Alternative one-line command** (Cisco IOS 15.0+):
```cisco
R1(config)# crypto key generate rsa modulus 2048
```

---

### Step 4: Configure SSH Version (Optional but Recommended)
**Why?** SSHv2 is more secure than SSHv1.

```cisco
R1(config)# ip ssh version 2
```

**Explanation**:
- **SSHv1**: Older, has security vulnerabilities
- **SSHv2**: More secure, recommended for production
- Default may be version 1.99 (supports both v1 and v2)
- Forcing version 2 improves security

**Other optional SSH configurations**:
```cisco
R1(config)# ip ssh time-out 60          # Session timeout (default 120 seconds)
R1(config)# ip ssh authentication-retries 3   # Login attempts (default 3)
```

---

### Step 5: Create Local User Account
**Why?** SSH requires username/password authentication.

```cisco
R1(config)# username admin privilege 15 secret Cisco123!
```

**Explanation**:
- **username**: The login username (e.g., "admin")
- **privilege 15**: Gives full administrative access (exec level 15)
- **secret**: Creates an encrypted password (better than "password")
- Can create multiple users with different privilege levels

**Privilege Levels**:
- **0-1**: Limited access (user EXEC mode)
- **2-14**: Custom privilege levels
- **15**: Full administrative access (privileged EXEC mode)

---

### Step 6: Enable SSH on VTY Lines
**Why?** VTY lines are the virtual terminal lines used for remote access.

```cisco
R1(config)# line vty 0 4
R1(config-line)# transport input ssh
R1(config-line)# login local
R1(config-line)# exit
```

**Explanation**:
- **line vty 0 4**: Configure VTY lines 0-4 (allows 5 simultaneous SSH sessions)
- **transport input ssh**: Allow ONLY SSH (blocks Telnet)
- **login local**: Use local username database for authentication
- Some devices support vty 0 15 (16 simultaneous sessions)

**Alternative Options**:
```cisco
# Allow both SSH and Telnet
R1(config-line)# transport input ssh telnet

# Allow only Telnet (not recommended)
R1(config-line)# transport input telnet

# Allow all protocols
R1(config-line)# transport input all
```

---

## Complete Configuration Example

```cisco
! Step 1: Configure hostname
Router(config)# hostname R1

! Step 2: Configure domain name
R1(config)# ip domain-name cisco.com

! Step 3: Generate RSA keys
R1(config)# crypto key generate rsa modulus 2048

! Step 4: Configure SSH version 2
R1(config)# ip ssh version 2

! Step 5: Create local user
R1(config)# username admin privilege 15 secret Cisco123!

! Step 6: Configure VTY lines for SSH
R1(config)# line vty 0 4
R1(config-line)# transport input ssh
R1(config-line)# login local
R1(config-line)# exit

! Save configuration
R1(config)# exit
R1# write memory
```

---

## Verification Commands

### Verify SSH Configuration
```cisco
R1# show ip ssh
SSH Enabled - version 2.0
Authentication timeout: 120 secs; Authentication retries: 3
```

### View RSA Keys
```cisco
R1# show crypto key mypubkey rsa
% Key pair was generated at: 00:15:20 UTC Mar 1 2024
Key name: R1.cisco.com
 Storage Device: not specified
 Usage: General Purpose Key
 Key is not exportable.
 Key Data:
  30820122 300D0609 2A864886 F70D0101 01050003 82010F00 3082010A 02820101
  [... key data ...]
```

### Check Active SSH Sessions
```cisco
R1# show ssh
Connection Version Mode Encryption  Hmac         State           Username
0          2.0     IN   aes256-cbc  hmac-sha1    Session started admin
0          2.0     OUT  aes256-cbc  hmac-sha1    Session started admin
```

### View VTY Line Configuration
```cisco
R1# show line vty 0 4
Tty Line Typ     Tx/Rx    A Modem  Roty AccO AccI   Uses   Noise  Overruns   Int
*  2 VTY          -       -    -      -    -    -      0       0     0/0       -
   3 VTY          -       -    -      -    -    -      0       0     0/0       -
```

### Check User Accounts
```cisco
R1# show running-config | include username
username admin privilege 15 secret 5 $1$mERr$hx5rVt7rPNoS4wqbXKX7m0
```

---

## Connecting to Device via SSH

### From Another Cisco Device
```cisco
Router# ssh -l admin 192.168.1.1
Password: 

R1>
```

### From PC/Linux/Mac
```bash
ssh admin@192.168.1.1
```

### From Windows Command Prompt
```cmd
ssh admin@192.168.1.1
```

---

## Troubleshooting SSH

### Common Issues and Solutions

**Problem**: "Connection refused"
```
Solution:
- Verify SSH is enabled: show ip ssh
- Check if RSA keys exist: show crypto key mypubkey rsa
- Verify VTY line configuration: show running-config | section line vty
```

**Problem**: "No hostkey alg"
```
Solution:
- Generate RSA keys with sufficient size (1024+ for SSHv2)
- Verify: crypto key generate rsa modulus 2048
```

**Problem**: "Authentication failed"
```
Solution:
- Verify username exists: show running-config | include username
- Check login method on VTY: show running-config | section line vty
- Ensure "login local" is configured
```

**Problem**: Can't generate keys
```
Solution:
- Configure hostname: hostname R1
- Configure domain name: ip domain-name cisco.com
- Then generate keys
```

---

## Deleting and Regenerating SSH Keys

### Delete RSA Keys
```cisco
R1(config)# crypto key zeroize rsa
% All RSA keys will be removed.
% All router certs issued using these keys will also be removed.
Do you really want to remove these keys? [yes/no]: yes
```

**Warning**: This disables SSH until new keys are generated!

### Regenerate Keys
```cisco
R1(config)# crypto key generate rsa modulus 2048
```

---

## Additional Security Best Practices

### Use Strong Passwords
```cisco
R1(config)# username admin privilege 15 secret MyStr0ng!Pass123
```

### Limit Login Attempts
```cisco
R1(config)# ip ssh authentication-retries 2
```

### Set Session Timeout
```cisco
R1(config)# ip ssh time-out 60
R1(config)# line vty 0 4
R1(config-line)# exec-timeout 5 0
```

### Use Access Control Lists (ACL)
```cisco
R1(config)# access-list 10 permit 192.168.1.0 0.0.0.255
R1(config)# line vty 0 4
R1(config-line)# access-class 10 in
```

---

## Quick Reference Summary

### The 6 Steps (Quick Version)
```cisco
1. hostname R1
2. ip domain-name cisco.com
3. crypto key generate rsa modulus 2048
4. ip ssh version 2
5. username admin privilege 15 secret Cisco123!
6. line vty 0 4
   transport input ssh
   login local
```

### Essential Verification Commands
```cisco
show ip ssh
show crypto key mypubkey rsa
show ssh
show line vty 0 4
```

---

## CCNA Exam Tips

**Remember**:
1. SSH requires BOTH hostname AND domain name before generating keys
2. Minimum 768-bit key size for SSHv2 (recommend 2048)
3. "login local" on VTY lines uses local username database
4. "transport input ssh" blocks Telnet for security
5. Privilege level 15 gives full administrative access
6. Use "secret" instead of "password" (more secure encryption)

**Common Exam Questions**:
- Why won't SSH keys generate? (Missing hostname or domain name)
- What command enables SSHv2? (ip ssh version 2)
- What's the purpose of "login local"? (Use local user database)
- Which privilege level gives full access? (15)
- How to allow only SSH on VTY lines? (transport input ssh)

---

*This guide covers everything you need to know about SSH configuration for the CCNA exam.*