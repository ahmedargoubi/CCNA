# 🧠 Cisco Packet Tracer Lab Guide – Configure SSH

## 🗂️ Addressing Table

| Device | Interface | IP Address   | Subnet Mask     |
|---------|------------|--------------|-----------------|
| S1      | VLAN 1     | 10.10.10.2   | 255.255.255.0   |
| PC1     | NIC        | 10.10.10.10  | 255.255.255.0   |

---

## 🎯 Objectives

1. **Secure Passwords**
2. **Encrypt Communications**
3. **Verify SSH Implementation**

---

## 🔎 Background

> Telnet sends data — including passwords — in plain text.  
> SSH (Secure Shell) replaces Telnet to provide **encrypted, secure** remote management of Cisco devices.

In this lab, you’ll:
- Encrypt local passwords
- Configure SSH on the switch
- Verify secure access

---

## 🧩 Part 1: Secure Passwords

### Step 1: Connect and Access S1
1. On **PC1**, open the **Command Prompt**.
2. Telnet into the switch:
   ```bash
   telnet 10.10.10.2
   ```
3. When prompted:
   - **User EXEC password:** `cisco`
   - **Privileged EXEC password:** `cisco`

### Step 2: Save Current Configuration
```bash
S1# copy running-config startup-config
```

### Step 3: Encrypt Plaintext Passwords
```bash
S1(config)# service password-encryption
```

### Step 4: Verify Encryption
```bash
S1# show running-config
```
- Ensure all passwords are now encrypted (they appear as scrambled text).

---

## 🔐 Part 2: Encrypt Communications

### Step 1: Configure Domain Name and Generate RSA Keys
```bash
S1(config)# ip domain-name netacad.pka
S1(config)# crypto key generate rsa
How many bits in the modulus [512]: 1024
```

### Step 2: Create SSH User
```bash
S1(config)# username admin secret cisco
```

### Step 3: Configure VTY Lines for SSH
```bash
S1(config)# line vty 0 15
S1(config-line)# transport input ssh
S1(config-line)# login local
S1(config-line)# no password
S1(config-line)# exit
```

---

## ✅ Part 3: Verify SSH Implementation

### Step 1: Test Telnet (Should Fail)
From PC1:
```bash
telnet 10.10.10.2
```
➡️ Connection should be **refused**.

### Step 2: Test SSH (Should Succeed)
From PC1:
```bash
ssh -l admin 10.10.10.2
```
> Note: `-l` is the letter **L**, not the number **1**.

- When prompted for a password, enter: `cisco`

### Step 3: Save Configuration
```bash
S1# copy running-config startup-config
```

---

## 🧾 Summary

| Task | Command / Action |
|------|------------------|
| Encrypt passwords | `service password-encryption` |
| Set domain name | `ip domain-name netacad.pka` |
| Generate RSA keys | `crypto key generate rsa` |
| Create SSH user | `username admin secret cisco` |
| Restrict to SSH only | `transport input ssh` |
| Verify SSH | `ssh -l admin 10.10.10.2` |

---

## 🧰 Troubleshooting Tips

- If SSH fails, ensure:
  - The switch has a hostname and domain name configured.
  - RSA keys were generated successfully.
  - PC1 and S1 are in the same subnet.
  - The VTY lines are set to `login local` and `transport input ssh`.

- To restart:
  ```bash
  S1# reload
  ```

---

**End of Guide**
