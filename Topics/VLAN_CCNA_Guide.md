# VLAN (Virtual Local Area Network) - CCNA Guide

## What is a VLAN?
A **VLAN** (Virtual Local Area Network) is a logical group of devices within a LAN that are segmented into separate broadcast domains regardless of their physical location. VLANs help reduce broadcast traffic and improve network performance and security.

## Benefits of VLANs
- **Improved Security**: Devices in different VLANs cannot communicate without a Layer 3 device (e.g., router).
- **Reduced Broadcasts**: Each VLAN is a separate broadcast domain.
- **Better Performance**: Traffic is confined to specific groups.
- **Simplified Administration**: Logical grouping of users by department or function.
- **Flexibility and Scalability**: Devices can be moved or reconfigured easily.

## Types of VLANs
1. **Default VLAN**: VLAN 1, configured by default on all switch ports.
2. **Data VLAN**: Carries user-generated traffic.
3. **Voice VLAN**: Carries voice traffic from IP phones.
4. **Management VLAN**: Used for management traffic like SSH, SNMP.
5. **Native VLAN**: The VLAN that carries untagged traffic on a trunk port.

## VLAN Tagging
- **IEEE 802.1Q**: Industry standard for VLAN tagging on trunk links.
- Adds a 4-byte tag to the Ethernet frame with VLAN ID.

## VLAN Ranges
- Normal range: VLAN 1 to 1005 (stored in VLAN.dat)
- Extended range: VLAN 1006 to 4094 (stored in running-config)

## VLAN Configuration Commands
```bash
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)# name Sales
Switch(config-vlan)# exit
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
```

## VLAN Trunking
Trunks allow VLAN traffic to pass between switches.
```bash
Switch(config)# interface FastEthernet0/24
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk encapsulation dot1q
```

## VLAN Trunking Protocol (VTP)
Used to propagate VLAN information across switches.
- VTP modes:
  - **Server**: Can create, modify, delete VLANs.
  - **Client**: Receives updates from server.
  - **Transparent**: Forwards updates but does not apply them.

## Verifying VLAN Configuration
```bash
Switch# show vlan brief
Switch# show interfaces trunk
Switch# show vtp status
```

## Common Issues
- Native VLAN mismatch
- Incorrect port mode (access vs trunk)
- VLAN not created or assigned to interface
- VTP configuration errors

## Best Practices
- Avoid using VLAN 1
- Use unique native VLANs on trunk links
- Document VLAN IDs and usage
- Use VTP in transparent mode to avoid unintended VLAN changes

---

**VLANs are a crucial concept in modern networking and essential for segmentation, performance, and security in enterprise networks.**