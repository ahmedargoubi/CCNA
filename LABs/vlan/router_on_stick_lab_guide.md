# Router-on-a-Stick Inter-VLAN Routing Lab Guide

## Lab Overview

### Topology
```
PC1 (VLAN 10) --- F0/11 [S1] G0/1 --- G0/0 [R1]
                         |
PC3 (VLAN 30) --- F0/6  |
```

### Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|-----------------|
| R1 | G0/0.10 | 172.17.10.1 | 255.255.255.0 | N/A |
| R1 | G0/0.30 | 172.17.30.1 | 255.255.255.0 | N/A |
| PC1 | NIC | 172.17.10.10 | 255.255.255.0 | 172.17.10.1 |
| PC3 | NIC | 172.17.30.10 | 255.255.255.0 | 172.17.30.1 |

### Objectives
- Configure VLANs on a switch
- Configure router subinterfaces (Router-on-a-Stick)
- Configure trunk port
- Test Inter-VLAN routing

---

## Concept: Router-on-a-Stick

### What is Router-on-a-Stick?

**Definition:** A method of Inter-VLAN routing where a single router interface connects to a switch trunk port and uses subinterfaces to route between VLANs.

### Why Use It?

**Problem:** Devices in different VLANs cannot communicate without routing
```
PC1 (VLAN 10) -X- PC3 (VLAN 30)
VLANs are separate broadcast domains
```

**Solution:** Router-on-a-Stick provides routing between VLANs
```
PC1 (VLAN 10) → Router → PC3 (VLAN 30)
               Subinterfaces route traffic
```

### How It Works

**Single Physical Interface:**
- One router interface (G0/0)
- Connected to switch trunk port
- Carries traffic for multiple VLANs

**Multiple Subinterfaces:**
- Virtual interfaces (G0/0.10, G0/0.30)
- Each subinterface = one VLAN
- Each has its own IP address (default gateway for VLAN)

**802.1Q Trunking:**
- VLAN tags added to frames
- Router knows which VLAN each frame belongs to
- Routes between VLANs

**Traffic Flow:**
```
1. PC1 sends packet to PC3 (different VLAN)
2. Packet goes to default gateway (R1's G0/0.10)
3. R1 receives on subinterface G0/0.10 (VLAN 10)
4. R1 routes to destination network
5. R1 sends out subinterface G0/0.30 (VLAN 30)
6. Packet tagged with VLAN 30
7. Switch receives and forwards to PC3
```

---

## Part 1: Add VLANs to Switch

### Step 1: Create VLANs on S1

**Commands:**
```cisco
S1> enable
S1# configure terminal
S1(config)# vlan 10
S1(config-vlan)# name VLAN10
S1(config-vlan)# exit
S1(config)# vlan 30
S1(config-vlan)# name VLAN30
S1(config-vlan)# exit
```

**What This Does:**
- Creates VLAN 10 for PC1
- Creates VLAN 30 for PC3
- Names are optional but recommended

---

### Step 2: Assign VLANs to Ports

**a. Configure Access Ports**

**For PC1 (F0/11 → VLAN 10):**
```cisco
S1(config)# interface fastEthernet 0/11
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 10
S1(config-if)# exit
```

**For PC3 (F0/6 → VLAN 30):**
```cisco
S1(config)# interface fastEthernet 0/6
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 30
S1(config-if)# exit
```

**What This Does:**
- Sets ports as access ports (connect to end devices)
- Assigns each port to specific VLAN
- PC1 traffic untagged in VLAN 10
- PC3 traffic untagged in VLAN 30

---

**b. Verify VLAN Configuration**

```cisco
S1# show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/7, Fa0/8, Fa0/9
                                                Fa0/10, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/18
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
10   VLAN10                           active    Fa0/11
30   VLAN30                           active    Fa0/6
1002 fddi-default                     active
1003 token-ring-default               active
1004 fddinet-default                  active
1005 trnet-default                    active
```

**Verify:**
- ✓ VLAN 10 exists with Fa0/11 assigned
- ✓ VLAN 30 exists with Fa0/6 assigned
- ✗ G0/1 is still in VLAN 1 (will fix in Part 3)

---

### Step 3: Test Connectivity Between PC1 and PC3

**Test:**
```
PC1> ping 172.17.30.10
```

**Question:** Were the pings successful?

**Answer:** NO ❌

**Explanation:**
- PC1 is in VLAN 10
- PC3 is in VLAN 30
- Different VLANs = different broadcast domains
- No routing configured yet
- Switch operates at Layer 2 (cannot route)
- Traffic cannot cross VLAN boundaries

**Why It Fails:**
```
PC1 (VLAN 10) → Switch → VLAN boundary ✗
                         Cannot forward to VLAN 30
```

---

## Part 2: Configure Subinterfaces

### Step 1: Configure Subinterfaces on R1

**Understanding Subinterfaces:**
- Subinterface = Virtual interface
- Format: `interface-type.vlan-number` (e.g., G0/0.10)
- Each subinterface represents one VLAN
- Requires 802.1Q encapsulation

---

**a. Create Subinterface G0/0.10 (VLAN 10)**

```cisco
R1> enable
R1# configure terminal
R1(config)# interface gigabitEthernet 0/0.10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip address 172.17.10.1 255.255.255.0
R1(config-subif)# exit
```

**Command Breakdown:**

**`interface g0/0.10`**
- Creates subinterface
- `.10` is arbitrary (best practice: match VLAN number)

**`encapsulation dot1Q 10`**
- Enables 802.1Q VLAN tagging
- Associates this subinterface with VLAN 10
- Router will process frames tagged with VLAN 10

**`ip address 172.17.10.1 255.255.255.0`**
- IP address for this subinterface
- Will be default gateway for VLAN 10 devices
- PC1's gateway: 172.17.10.1

---

**b. Create Subinterface G0/0.30 (VLAN 30)**

```cisco
R1(config)# interface gigabitEthernet 0/0.30
R1(config-subif)# encapsulation dot1Q 30
R1(config-subif)# ip address 172.17.30.1 255.255.255.0
R1(config-subif)# exit
```

**What This Does:**
- Creates subinterface for VLAN 30
- Encapsulates with VLAN 30 tag
- IP address is default gateway for VLAN 30
- PC3's gateway: 172.17.30.1

---

### Step 2: Verify Configuration

**a. Check Subinterface Status**

```cisco
R1# show ip interface brief

Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     unassigned      YES unset  administratively down down
GigabitEthernet0/0.10  172.17.10.1     YES manual down                  down
GigabitEthernet0/0.30  172.17.30.1     YES manual down                  down
```

**Observation:**
- Both subinterfaces show **down**
- Physical interface G0/0 is **administratively down**

**Why Down?**
- Subinterfaces are virtual
- Depend on physical interface
- If physical interface is down, subinterfaces are down

---

**b. Enable Physical Interface G0/0**

```cisco
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# no shutdown
R1(config-if)# exit
```

**Important:** 
- Physical interface does NOT need IP address
- Subinterfaces have the IP addresses
- Physical interface just needs to be enabled

---

**c. Verify Subinterfaces Are Now Active**

```cisco
R1# show ip interface brief

Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     unassigned      YES unset  up                    up
GigabitEthernet0/0.10  172.17.10.1     YES manual up                    up
GigabitEthernet0/0.30  172.17.30.1     YES manual up                    up
```

**Verify:**
- ✓ G0/0 is **up/up**
- ✓ G0/0.10 is **up/up**
- ✓ G0/0.30 is **up/up**

---

**Additional Verification Commands:**

```cisco
R1# show interfaces g0/0.10
GigabitEthernet0/0.10 is up, line protocol is up
  Hardware is Gigabit Ethernet, address is 0001.9770.4501
  Internet address is 172.17.10.1/24
  MTU 1500 bytes, BW 1000000 Kbit/sec
  Encapsulation 802.1Q Virtual LAN, VLAN ID 10
                ↑
          802.1Q encapsulation with VLAN 10

R1# show vlans
Virtual LAN ID:  10 (IEEE 802.1Q Encapsulation)
   vLAN Trunk Interface:   GigabitEthernet0/0.10
   Protocols Configured:   Address:                Received:        Transmitted:
           IP              172.17.10.1                    0                   0

Virtual LAN ID:  30 (IEEE 802.1Q Encapsulation)
   vLAN Trunk Interface:   GigabitEthernet0/0.30
   Protocols Configured:   Address:                Received:        Transmitted:
           IP              172.17.30.1                    0                   0
```

---

## Part 3: Test Connectivity with Inter-VLAN Routing

### Step 1: Ping Between PC1 and PC3

**Test:**
```
PC1> ping 172.17.30.10
```

**Question:** Do the pings succeed?

**Answer:** NO ❌ (still failing)

**Explanation:**
- Router is configured correctly ✓
- VLANs are configured correctly ✓
- BUT: Switch port G0/1 is still an access port in VLAN 1
- Trunk port NOT configured yet
- VLAN-tagged traffic from router cannot pass through

**Why It Fails:**
```
PC1 → VLAN 10 → [S1 G0/1 (VLAN 1 access)] ✗ Router
                 Traffic dropped at trunk
```

---

### Step 2: Enable Trunking

**a. Check Current VLAN Assignment**

```cisco
S1# show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/7, Fa0/8, Fa0/9
                                                Fa0/10, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/18
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
                                                             ↑
10   VLAN10                           active    Fa0/11
30   VLAN30                           active    Fa0/6
```

**Question:** What VLAN is G0/1 assigned to?

**Answer:** VLAN 1 (default VLAN)

**Problem:** G0/1 is an access port, but needs to be a trunk to carry multiple VLANs

---

**b. Configure G0/1 as Trunk Port**

```cisco
S1(config)# interface gigabitEthernet 0/1
S1(config-if)# switchport mode trunk
S1(config-if)# exit
```

**What This Does:**
- Changes port from access mode to trunk mode
- Trunk port can carry traffic for multiple VLANs
- Uses 802.1Q tagging
- Router subinterfaces can now communicate with VLANs

---

**c. Verify Trunk Configuration**

**Method 1: show vlan (trunk ports NOT listed)**
```cisco
S1# show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/7, Fa0/8, Fa0/9
                                                Fa0/10, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/18
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/2
                                                (G0/1 NOT listed)
10   VLAN10                           active    Fa0/11
30   VLAN30                           active    Fa0/6
```

**Question:** How can you determine interface is trunk using show vlan?

**Answer:** Trunk ports are NOT listed in the output of `show vlan brief`
- If port missing from VLAN list = trunk port
- G0/1 disappeared from VLAN 1 = now a trunk

---

**Method 2: show interface trunk (preferred)**
```cisco
S1# show interface trunk

Port        Mode         Encapsulation  Status        Native vlan
Gi0/1       on           802.1q         trunking      1
            ↑            ↑              ↑             ↑
         Trunk mode   Using 802.1Q   Operational   Native VLAN

Port        Vlans allowed on trunk
Gi0/1       1-4094

Port        Vlans allowed and active in management domain
Gi0/1       1,10,30

Port        Vlans in spanning tree forwarding state and not pruned
Gi0/1       1,10,30
```

**Verification:**
- ✓ Mode: on (or trunk)
- ✓ Encapsulation: 802.1q
- ✓ Status: trunking
- ✓ VLANs allowed: includes 10 and 30
- ✓ Active VLANs: 1, 10, 30

---

**Method 3: show interfaces switchport**
```cisco
S1# show interfaces g0/1 switchport

Name: Gi0/1
Switchport: Enabled
Administrative Mode: trunk
Operational Mode: trunk
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: dot1q
Negotiation of Trunking: On
Access Mode VLAN: 1 (default)
Trunking Native Mode VLAN: 1 (default)
Trunking VLANs Enabled: ALL
```

---

### Step 3: Test Connectivity

**Now test connectivity with complete configuration:**

**From PC1:**
```
PC1> ping 172.17.10.1
Reply from 172.17.10.1 ← Gateway reachable ✓

PC1> ping 172.17.30.1
Reply from 172.17.30.1 ← Other gateway reachable ✓

PC1> ping 172.17.30.10
Reply from 172.17.30.10 ← PC3 reachable ✓
```

**From PC3:**
```
PC3> ping 172.17.30.1
Reply from 172.17.30.1 ← Gateway reachable ✓

PC3> ping 172.17.10.1
Reply from 172.17.10.1 ← Other gateway reachable ✓

PC3> ping 172.17.10.10
Reply from 172.17.10.10 ← PC1 reachable ✓
```

**Question:** What addresses do PC1 and PC3 use as default gateway?

**Answer:**
- **PC1:** 172.17.10.1 (R1's G0/0.10 subinterface)
- **PC3:** 172.17.30.1 (R1's G0/0.30 subinterface)

---

## Traffic Flow Explanation

### PC1 Pinging PC3

**Step-by-Step Process:**

**1. PC1 Sends Packet**
```
Source: 172.17.10.10 (PC1)
Destination: 172.17.30.10 (PC3)
```

**2. PC1 Checks Routing**
- Destination 172.17.30.10 not in local subnet (172.17.10.0/24)
- Must send to default gateway: 172.17.10.1

**3. PC1 Sends to Gateway**
```
Layer 3: IP packet (172.17.10.10 → 172.17.30.10)
Layer 2: Ethernet frame
  - Source MAC: PC1's MAC
  - Destination MAC: R1's G0/0 MAC (from ARP)
  - VLAN: 10 (untagged on access port)
```

**4. Switch S1 Receives Frame**
- Receives on F0/11 (access port VLAN 10)
- Destination MAC: Router's MAC
- Forwards out G0/1 (trunk port)
- Adds VLAN 10 tag (802.1Q)

**5. Router R1 Receives Frame**
```
Physical interface: G0/0
Frame has VLAN 10 tag → Goes to subinterface G0/0.10
```

**6. R1 Routes Packet**
- Receives on G0/0.10 (172.17.10.0/24 network)
- Destination: 172.17.30.10
- Routing table shows 172.17.30.0/24 is directly connected via G0/0.30
- Routes to G0/0.30 subinterface

**7. R1 Sends Out G0/0.30**
```
Layer 3: IP packet (172.17.10.10 → 172.17.30.10)
Layer 2: New Ethernet frame
  - Source MAC: R1's G0/0 MAC
  - Destination MAC: PC3's MAC (from ARP)
  - VLAN: 30 (802.1Q tag added)
Physical: G0/0 (same physical interface!)
```

**8. Switch S1 Receives Tagged Frame**
- Receives on G0/1 (trunk port)
- VLAN tag: 30
- Forwards to ports in VLAN 30
- Removes tag before sending out access port

**9. PC3 Receives Frame**
- Frame arrives on F0/6 (untagged)
- Destination MAC matches: accepts packet
- Destination IP matches: processes packet
- Ping successful! ✓

---

## Complete Configuration Summary

### Switch S1 Configuration

```cisco
! Create VLANs
vlan 10
 name VLAN10
vlan 30
 name VLAN30

! Configure access ports
interface FastEthernet0/11
 switchport mode access
 switchport access vlan 10

interface FastEthernet0/6
 switchport mode access
 switchport access vlan 30

! Configure trunk port
interface GigabitEthernet0/1
 switchport mode trunk
```

---

### Router R1 Configuration

```cisco
! Configure subinterfaces
interface GigabitEthernet0/0
 no shutdown
 no ip address

interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 172.17.10.1 255.255.255.0

interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 172.17.30.1 255.255.255.0
```

---

## Key Concepts for CCNA

### Router-on-a-Stick Requirements

**1. Switch Configuration:**
- ✓ VLANs created
- ✓ Access ports assigned to VLANs
- ✓ Trunk port configured (802.1Q)

**2. Router Configuration:**
- ✓ Subinterfaces created
- ✓ 802.1Q encapsulation configured
- ✓ IP addresses assigned
- ✓ Physical interface enabled (no IP needed)

**3. End Device Configuration:**
- ✓ IP addresses in correct subnet
- ✓ Default gateway = router subinterface IP

---

### Common Mistakes and Troubleshooting

**Problem 1: Pings fail after VLAN configuration**
- **Cause:** No routing configured
- **Solution:** Configure router subinterfaces

**Problem 2: Subinterfaces show down**
- **Cause:** Physical interface is shutdown
- **Solution:** `no shutdown` on physical interface

**Problem 3: Pings still fail after router config**
- **Cause:** Trunk not configured on switch
- **Solution:** `switchport mode trunk` on switch port

**Problem 4: Wrong VLAN traffic**
- **Cause:** Encapsulation VLAN mismatch
- **Solution:** Match encapsulation VLAN number to actual VLAN

---

### Verification Commands Summary

**Switch:**
```cisco
show vlan brief
show interface trunk
show interface g0/1 switchport
show mac address-table
```

**Router:**
```cisco
show ip interface brief
show interfaces g0/0.10
show vlans
show running-config interface g0/0
```

**End Devices:**
```
ipconfig
ping <gateway>
ping <remote-device>
```

---

## Advantages and Disadvantages

### Advantages of Router-on-a-Stick

✓ Cost-effective (one router interface)
✓ Simple for small networks
✓ Easy to understand and configure
✓ Good for learning Inter-VLAN routing

### Disadvantages of Router-on-a-Stick

✗ Single point of failure
✗ Bandwidth bottleneck (one physical link)
✗ All inter-VLAN traffic uses same link
✗ Not scalable for large networks

### When to Use

**Good For:**
- Small networks (< 10 VLANs)
- Lab environments
- Branch offices
- Budget-constrained deployments

**NOT Good For:**
- Large enterprises
- High-traffic environments
- Production networks requiring high availability

**Better Alternative:** Layer 3 switch with SVI (Switched Virtual Interfaces)

---

## Practice Questions

**Q1:** Why do subinterfaces need 802.1Q encapsulation?
**A:** To identify which VLAN the traffic belongs to using VLAN tags

**Q2:** Can the physical interface have an IP address?
**A:** Technically yes, but not needed for router-on-a-stick. Subinterfaces have the IPs.

**Q3:** What happens if trunk is not configured on switch?
**A:** VLAN-tagged frames from router are dropped; inter-VLAN routing fails

**Q4:** Why does subinterface naming convention use VLAN number (e.g., G0/0.10)?
**A:** Best practice for documentation; makes it clear which VLAN the subinterface handles

**Q5:** How many physical interfaces needed for 5 VLANs?
**A:** ONE physical interface with 5 subinterfaces

---

**End of Router-on-a-Stick Inter-VLAN Routing Lab Guide**