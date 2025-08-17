# Port Security – CCNA Guide

## 1. Introduction to Port Security

**Port Security** is a security feature of Cisco switches that controls **which source MAC addresses are allowed** to enter a switchport. If an **unauthorized MAC address** enters the port, the switch takes an **action**.

By default → the interface goes into an **err-disabled** state (shut down).

### Key Points:

- Default: **1 MAC address per port allowed**
- MAC addresses can be:
  - **Configured manually**
  - **Learned dynamically** (first MAC that connects)
  - **Sticky** (learned dynamically but saved in config)
- Maximum number of MACs can be changed
- Combination of **manual + dynamic** learning is possible

---

## 2. Why Use Port Security?

- Limits which devices can connect to the switch
- Helps mitigate **MAC flooding** or **DHCP starvation attacks**
- Protects the **MAC address table** from being overwhelmed by spoofed addresses

💡 **Note:** MAC spoofing is still possible, but limiting the number of addresses per port adds strong protection.

---

## 3. Enabling Port Security

### Basic Configuration

```bash
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security maximum 2
Switch(config-if)# switchport port-security mac-address sticky
Switch(config-if)# switchport port-security violation shutdown
```

**Command Breakdown:**
- `switchport port-security` → Enables port security
- `maximum 2` → Allows 2 MAC addresses
- `mac-address sticky` → Saves dynamically-learned MACs in config
- `violation shutdown` → Sets action if unauthorized MAC detected

### Verification

```bash
Switch# show port-security interface f0/1
Switch# show mac address-table secure
```

---

## 4. Port Security Violation Modes

When an **unauthorized MAC** is detected, the switch reacts based on the **configured violation mode**:

### 1. Shutdown (default)

- Port is **disabled (err-disabled)**
- Syslog/SNMP message generated
- Violation counter set to `1`

### 2. Restrict

- Unauthorized traffic is **discarded**
- Port **stays up**
- Syslog/SNMP message generated
- Violation counter **increments**

### 3. Protect

- Unauthorized traffic is **discarded silently**
- No syslog/SNMP message
- No violation counter update
- Port **remains active**

---

## 5. Secure MAC Address Aging

By default: **secure MAC addresses never age out**.

Can configure with:

```bash
Switch(config-if)# switchport port-security aging time 5
Switch(config-if)# switchport port-security aging type {absolute | inactivity}
```

### Aging Types:

- **Absolute** → Timer starts when MAC is learned; removed after time expires (even if still active)
- **Inactivity** → Timer resets if traffic is seen from MAC; removed only if inactive for the set time

---

## 6. Sticky Secure MAC Addresses

Learned MACs are **saved to the running config**:

```bash
Switch(config-if)# switchport port-security mac-address sticky
```

- If saved to startup-config, they **persist after reload**
- Sticky MACs = converted into **static entries**

---

## 7. Port Security and MAC Address Table

- Secure MACs appear in the **MAC address table** like normal entries
- Types:
  - **Static** = manually configured or sticky
  - **Dynamic** = learned normally

View secure MACs:

```bash
Switch# show mac address-table secure
```

---

## 8. Recovery from Port Security Violation

If shutdown occurs, interface goes into **err-disabled**.

### Manual Recovery

```bash
Switch(config)# interface f0/1
Switch(config-if)# shutdown
Switch(config-if)# no shutdown
```

### Automatic Recovery (Errdisable Recovery)

```bash
Switch(config)# errdisable recovery cause psecure-violation
Switch(config)# errdisable recovery interval 30
```

Automatically recovers after 30 seconds.

---

## 9. Command Review

| Command | Description |
|---------|-------------|
| `switchport port-security` | Enable feature |
| `switchport port-security maximum N` | Max MACs allowed |
| `switchport port-security violation {shutdown\|restrict\|protect}` | Set violation action |
| `switchport port-security mac-address {MAC\|sticky}` | Configure or learn secure MACs |
| `switchport port-security aging ...` | Configure aging |
| `show port-security` | Verify status |
| `show mac address-table secure` | Check learned secure MACs |

---

## ✅ Summary for CCNA

- **Port Security** = control over devices connecting to a port
- **Default** = 1 MAC, shutdown on violation
- **Violation modes** = Shutdown / Restrict / Protect
- **Sticky MACs** = make dynamic addresses permanent
- **Aging** allows dynamic cleanup of secure MACs
- **Errdisable recovery** helps automate re-enabling ports