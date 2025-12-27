# Single-Area OSPFv2 Configuration Lab Guide

## Overview
This lab implements OSPF routing across a network with point-to-point and broadcast multiaccess topologies. You'll configure OSPF process 10 across all routers to enable full connectivity.

## Network Topology
- **Headquarters Network**: P2P-1, P2P-2, P2P-3 (Point-to-Point routers)
- **Data Service Network**: BC-1, BC-2, BC-3 (Broadcast multiaccess network)
- **Connection**: BC-1 connects both networks and provides internet access

---

## Configuration Steps

### 1. Configure OSPF on Headquarters Network (P2P Routers)

#### Router P2P-1
```
enable
configure terminal
router ospf 10
network 10.0.0.0 0.0.0.3 area 0
network 10.0.0.8 0.0.0.3 area 0
network 10.0.0.12 0.0.0.3 area 0
exit
```

#### Router P2P-2
```
enable
configure terminal
router ospf 10
network 10.0.0.0 0.0.0.3 area 0
network 10.0.0.4 0.0.0.3 area 0
network 192.168.1.0 0.0.0.255 area 0
network 192.168.2.0 0.0.0.255 area 0
exit
```

#### Router P2P-3
```
enable
configure terminal
router ospf 10
network 10.0.0.4 0.0.0.3 area 0
network 10.0.0.8 0.0.0.3 area 0
network 192.168.3.0 0.0.0.15 area 0
exit
```

---

### 2. Configure OSPF on Data Service Network (BC Routers)

#### Router BC-1
```
enable
configure terminal
router ospf 10
router-id 6.6.6.6
exit

interface Serial0/1/0
ip ospf 10 area 0
exit

interface GigabitEthernet0/0/0
ip ospf 10 area 0
exit
```

#### Router BC-2
```
enable
configure terminal
router ospf 10
router-id 5.5.5.5
exit

interface GigabitEthernet0/0/0
ip ospf 10 area 0
exit

interface GigabitEthernet0/0/1
ip ospf 10 area 0
exit
```

#### Router BC-3
```
enable
configure terminal
router ospf 10
router-id 4.4.4.4
exit

interface GigabitEthernet0/0/0
ip ospf 10 area 0
exit

interface GigabitEthernet0/0/1
ip ospf 10 area 0
exit
```

---

### 3. Prevent Routing Updates on User-Facing Interfaces

Configure passive interfaces on all LAN interfaces to prevent OSPF updates where not needed.

#### P2P-2
```
configure terminal
router ospf 10
passive-interface GigabitEthernet0/0/0
passive-interface GigabitEthernet0/0/1
exit
```

#### P2P-3
```
configure terminal
router ospf 10
passive-interface GigabitEthernet0/0/0
exit
```

#### BC-1
```
configure terminal
router ospf 10
passive-interface GigabitEthernet0/0/0
exit
```

#### BC-2
```
configure terminal
router ospf 10
passive-interface GigabitEthernet0/0/0
exit
```

#### BC-3
```
configure terminal
router ospf 10
passive-interface GigabitEthernet0/0/0
exit
```

---

### 4. Configure BC-1 as Designated Router

Set the OSPF priority on BC-1's multiaccess interface to ensure it becomes the DR.

#### Router BC-1
```
configure terminal
interface GigabitEthernet0/0/0
ip ospf priority 255
exit
```

---

### 5. Configure Default Route and Redistribution

#### Router BC-1
```
configure terminal
ip route 0.0.0.0 0.0.0.0 Serial0/1/1
router ospf 10
default-information originate
exit
```

---

### 6. Configure OSPF Interface Costs

Set reference bandwidth to make GigabitEthernet cost = 10 and FastEthernet cost = 100.

#### All Routers (P2P-1, P2P-2, P2P-3, BC-1, BC-2, BC-3)
```
configure terminal
router ospf 10
auto-cost reference-bandwidth 10000
exit
```

#### Configure P2P-1 Serial0/1/1 Cost
```
configure terminal
interface Serial0/1/1
ip ospf cost 50
exit
```

---

### 7. Configure Hello and Dead Timers

Modify timers on interfaces connecting P2P-1 and BC-1 (double the defaults: hello 20s, dead 80s).

#### Router P2P-1
```
configure terminal
interface Serial0/2/0
ip ospf hello-interval 20
ip ospf dead-interval 80
exit
```

#### Router BC-1
```
configure terminal
interface Serial0/1/0
ip ospf hello-interval 20
ip ospf dead-interval 80
exit
```

---

## Verification Commands

After configuration, verify your setup:

```
show ip ospf neighbor
show ip ospf interface brief
show ip route ospf
show ip protocols
ping 203.0.113.100 (from any PC)
```

---

## Key Points to Remember

- **Process ID 10** is used throughout the network
- **Network statements** use inverse masks (wildcard masks)
- **Interface configuration** method is used in the Data Service network
- **Router IDs** are manually set on BC routers
- **Passive interfaces** prevent unnecessary OSPF traffic
- **Default route** is redistributed automatically via `default-information originate`
- **Reference bandwidth** affects cost calculations
- **Timer values** must match on both ends of a link

---

## Troubleshooting Tips

1. If neighbors don't form, check that hello/dead timers match on both sides
2. Verify OSPF is enabled on the correct interfaces
3. Check that network statements use correct inverse masks
4. Ensure area numbers match (all should be area 0)
5. Verify passive interfaces are only on LAN interfaces, not WAN links