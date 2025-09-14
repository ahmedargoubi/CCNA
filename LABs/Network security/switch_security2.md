# Cisco Switch Security Configuration Lab Guide

## Lab Overview

This lab focuses on implementing comprehensive security measures on two access layer switches (SW-1 and SW-2) in a partially configured network. You will configure secure trunks, port security, DHCP snooping, and STP security features.

## Network Information

### VLAN Configuration Table

| Switch | VLAN | Name | Ports | Network |
|--------|------|------|-------|---------|
| SW-1 | 10 | Admin | F0/1, F0/2 | 192.168.10.0/24 |
| SW-1 | 20 | Sales | F0/10 | 192.168.20.0/24 |
| SW-1 | 99 | Management | F0/24 | 192.168.99.0/24 |
| SW-1 | 100 | Native | G0/1, G0/2 | None |
| SW-1 | 999 | BlackHole | All unused | None |
| SW-2 | 10 | Admin | F0/1, F0/22 | 192.168.10.0/24 |
| SW-2 | 20 | Sales | F0/10 | 192.168.20.0/24 |
| SW-2 | 99 | Management | F0/24 | 192.168.99.0/24 |
| SW-2 | 100 | Native | None | None |
| SW-2 | 999 | BlackHole | All unused | None |

## Lab Objectives

1. Create a Secure Trunk
2. Secure Unused Switchports
3. Implement Port Security
4. Enable DHCP Snooping
5. Configure Rapid PVST PortFast and BPDU Guard

---

## Step 1: Create a Secure Trunk

### Physical Connection
- Connect the G0/2 ports of SW-1 and SW-2 using a straight-through cable

### Configuration on Both Switches (SW-1 and SW-2)

```cisco
# Enter global configuration mode
enable
configure terminal

# Create VLAN 100 for native VLAN
vlan 100
name Native
exit

# Configure G0/1 as static trunk
interface gigabitethernet 0/1
switchport mode trunk
switchport nonegotiate
switchport trunk native vlan 100
exit

# Configure G0/2 as static trunk
interface gigabitethernet 0/2
switchport mode trunk
switchport nonegotiate
switchport trunk native vlan 100
exit
```

### Verification Commands
```cisco
show interfaces trunk
show vlan brief
show interfaces g0/1 switchport
show interfaces g0/2 switchport
```

---

## Step 2: Secure Unused Switchports (SW-1 Only)

### Identify Unused Ports
Based on the VLAN table, active ports on SW-1 are: F0/1, F0/2, F0/10, F0/24, G0/1, G0/2
All other FastEthernet ports (F0/3-F0/9, F0/11-F0/23) are unused.

### Configuration on SW-1

```cisco
# Create BlackHole VLAN
vlan 999
name BlackHole
exit

# Shutdown and secure unused ports
interface range fastethernet 0/3-9
shutdown
switchport mode access
switchport access vlan 999
exit

interface range fastethernet 0/11-23
shutdown
switchport mode access
switchport access vlan 999
exit
```

### Verification
```cisco
show interfaces status
show vlan brief
```

---

## Step 3: Implement Port Security (SW-1 Only)

### Configuration on Active Access Ports

```cisco
# Configure port security on F0/1 (Admin VLAN)
interface fastethernet 0/1
switchport port-security
switchport port-security maximum 4
switchport port-security violation restrict
switchport port-security mac-address sticky
# Add static MAC address for PC connected to F0/1
# Replace xxxx.xxxx.xxxx with actual PC MAC address
switchport port-security mac-address [PC-MAC-ADDRESS]
exit

# Configure port security on F0/2 (Admin VLAN)
interface fastethernet 0/2
switchport port-security
switchport port-security maximum 4
switchport port-security violation restrict
switchport port-security mac-address sticky
exit

# Configure port security on F0/10 (Sales VLAN)
interface fastethernet 0/10
switchport port-security
switchport port-security maximum 4
switchport port-security violation restrict
switchport port-security mac-address sticky
exit

# Configure port security on F0/24 (Management VLAN)
interface fastethernet 0/24
switchport port-security
switchport port-security maximum 4
switchport port-security violation restrict
switchport port-security mac-address sticky
exit
```

### How to Find PC MAC Address
1. Click on the PC connected to F0/1
2. Go to Desktop → Command Prompt
3. Type `ipconfig /all` and note the Physical Address

### Verification
```cisco
show port-security
show port-security interface fastethernet 0/1
show port-security address
```

---

## Step 4: Configure DHCP Snooping

### Configuration on SW-1

```cisco
# Configure trunk ports as trusted
interface gigabitethernet 0/1
ip dhcp snooping trust
exit

interface gigabitethernet 0/2
ip dhcp snooping trust
exit

# Limit untrusted ports to 5 DHCP packets per second
interface fastethernet 0/1
ip dhcp snooping limit rate 5
exit

interface fastethernet 0/2
ip dhcp snooping limit rate 5
exit

interface fastethernet 0/10
ip dhcp snooping limit rate 5
exit

interface fastethernet 0/24
ip dhcp snooping limit rate 5
exit
```

### Configuration on SW-2

```cisco
# Enable DHCP snooping globally and for specific VLANs
ip dhcp snooping
ip dhcp snooping vlan 10
ip dhcp snooping vlan 20
ip dhcp snooping vlan 99
```

### Verification
```cisco
show ip dhcp snooping
show ip dhcp snooping binding
```

---

## Step 5: Configure PortFast and BPDU Guard

### Configuration on SW-1 (Specific Ports)

```cisco
# Enable PortFast and BPDU Guard on access ports
interface fastethernet 0/1
spanning-tree portfast
spanning-tree bpduguard enable
exit

interface fastethernet 0/2
spanning-tree portfast
spanning-tree bpduguard enable
exit

interface fastethernet 0/10
spanning-tree portfast
spanning-tree bpduguard enable
exit

interface fastethernet 0/24
spanning-tree portfast
spanning-tree bpduguard enable
exit
```

### Configuration on SW-2 (Global Default)

```cisco
# Configure all access ports to use PortFast by default
spanning-tree portfast default
```

### Verification
```cisco
show spanning-tree interface fastethernet 0/1 detail
show spanning-tree summary
```

---

## Final Verification Commands

### Complete Configuration Check

```cisco
# Check VLAN configuration
show vlan brief

# Check trunk configuration
show interfaces trunk

# Check port security
show port-security
show port-security address

# Check DHCP snooping
show ip dhcp snooping

# Check spanning tree
show spanning-tree summary
show spanning-tree interface fastethernet 0/1 detail

# Check interface status
show interfaces status
```

---

## Troubleshooting Tips

1. **Trunk Issues**: Ensure both sides are configured identically and DTP is disabled
2. **Port Security**: Verify MAC addresses are learned correctly with `show port-security address`
3. **DHCP Snooping**: Check that trust relationships are properly configured
4. **PortFast**: Ensure it's only enabled on access ports, never on trunk ports
5. **BPDU Guard**: If a port goes into err-disabled state, use `shutdown` then `no shutdown` to recover

---

## Security Features Summary

- **Secure Trunks**: Static configuration with native VLAN and DTP disabled
- **Unused Port Security**: Shutdown and moved to BlackHole VLAN
- **Port Security**: MAC address learning limited with violation restrictions
- **DHCP Snooping**: Prevents rogue DHCP servers
- **PortFast**: Reduces convergence time for access ports
- **BPDU Guard**: Protects against switching loops from unauthorized devices

---

## Save Configuration

Don't forget to save your configuration on both switches:

```cisco
copy running-config startup-config
```

or

```cisco
write memory
```