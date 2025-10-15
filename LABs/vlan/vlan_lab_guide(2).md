# VLAN Configuration Lab - Complete Guide

## Lab Overview

**Objective:** Configure VLANs on three switches (S1, S2, S3) and assign ports to specific VLANs, including Voice VLAN configuration.


---

## Part 1: Verify Default VLAN Configuration

### Step 1: Display Current VLANs

**On S1 (and repeat for S2, S3):**

```cisco
S1> enable
S1# show vlan brief
```

** Output:**
```

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1, Gig0/2
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

**Key Command:**
```cisco
show vlan brief
```
Shows VLAN ID, name, status, and assigned ports.

---

### Step 2: Verify Connectivity

**Test connectivity between PCs on same subnet:**

**From PC1 (172.17.10.21):**
```
PC1> ping 172.17.10.24
Success! (Both in 172.17.10.0/24 subnet)
```

**From PC2 (172.17.20.22):**
```
PC2> ping 172.17.20.25
Success! (Both in 172.17.20.0/24 subnet)
```

**From PC3 (172.17.30.23):**
```
PC3> ping 172.17.30.26
Success! (Both in 172.17.30.0/24 subnet)
```

**Cross-subnet ping (should fail):**
```
PC1> ping 172.17.20.22
Failed! (Different subnets, no routing)
```

---

### Question: What benefits can VLANs provide?

**Answer:**
1. **Segmentation** - Separate traffic into logical groups
2. **Security** - Isolate sensitive data (e.g., Faculty from Students)
3. **Performance** - Reduce broadcast domains, less congestion
4. **Flexibility** - Easy to move users between groups without rewiring
5. **Management** - Centralized control and policies per VLAN
6. **Cost Savings** - No need for separate physical switches per department

---

## Part 2: Configure VLANs

### Step 1: Create and Name VLANs on S1

**Enter Global Configuration Mode:**
```cisco
S1> enable
S1# configure terminal
S1(config)#
```

**Create VLAN 10:**
```cisco
S1(config)# vlan 10
S1(config-vlan)# name Faculty/Staff
S1(config-vlan)# exit
```

**Create VLAN 20:**
```cisco
S1(config)# vlan 20
S1(config-vlan)# name Students
S1(config-vlan)# exit
```

**Create VLAN 30:**
```cisco
S1(config)# vlan 30
S1(config-vlan)# name Guest(Default)
S1(config-vlan)# exit
```

**Create VLAN 99:**
```cisco
S1(config)# vlan 99
S1(config-vlan)# name Management&Native
S1(config-vlan)# exit
```

**Create VLAN 150:**
```cisco
S1(config)# vlan 150
S1(config-vlan)# name VOICE
S1(config-vlan)# exit
```

**Quick Method (All at once):**
```cisco
S1(config)# vlan 10
S1(config-vlan)# name Faculty/Staff
S1(config-vlan)# vlan 20
S1(config-vlan)# name Students
S1(config-vlan)# vlan 30
S1(config-vlan)# name Guest(Default)
S1(config-vlan)# vlan 99
S1(config-vlan)# name Management&Native
S1(config-vlan)# vlan 150
S1(config-vlan)# name VOICE
S1(config-vlan)# end
```

---

### Step 2: Verify VLAN Configuration

**Command to display only VLAN name, status, and ports:**
```cisco
S1# show vlan brief
```

**Output:**
```

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1, Gig0/2
10   Faculty/Staff                    active    
20   Students                         active    
30   Guest                            active    
99   Management&Native                active    
150  VOICE                            active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    

**Alternative Commands:**
```cisco
show vlan          # Detailed view
show vlan id 10    # Specific VLAN
show vlan name Students  # By name
```

---

### Step 3: Create VLANs on S2 and S3

**Repeat the same commands on S2:**
```cisco
S2> enable
S2# configure terminal
S2(config)# vlan 10
S2(config-vlan)# name Faculty/Staff
S2(config-vlan)# vlan 20
S2(config-vlan)# name Students
S2(config-vlan)# vlan 30
S2(config-vlan)# name Guest(Default)
S2(config-vlan)# vlan 99
S2(config-vlan)# name Management&Native
S2(config-vlan)# vlan 150
S2(config-vlan)# name VOICE
S2(config-vlan)# end
```

**Repeat on S3 (same commands):**
```cisco
S3> enable
S3# configure terminal
S3(config)# vlan 10
S3(config-vlan)# name Faculty/Staff
S3(config-vlan)# vlan 20
S3(config-vlan)# name Students
S3(config-vlan)# vlan 30
S3(config-vlan)# name Guest(Default)
S3(config-vlan)# vlan 99
S3(config-vlan)# name Management&Native
S3(config-vlan)# vlan 150
S3(config-vlan)# name VOICE
S3(config-vlan)# end
```

---

### Step 4: Verify on All Switches

```cisco
S2# show vlan brief
S3# show vlan brief
```

---

## Part 3: Assign VLANs to Ports

### Step 1: Assign VLANs on S2

**Assign FastEthernet 0/11 to VLAN 10:**
```cisco
S2> enable
S2# configure terminal
S2(config)# interface fastEthernet 0/11
S2(config-if)# switchport mode access
S2(config-if)# switchport access vlan 10
S2(config-if)# exit
```

**Assign FastEthernet 0/18 to VLAN 20:**
```cisco
S2(config)# interface fastEthernet 0/18
S2(config-if)# switchport mode access
S2(config-if)# switchport access vlan 20
S2(config-if)# exit
```

**Assign FastEthernet 0/6 to VLAN 30:**
```cisco
S2(config)# interface fastEthernet 0/6
S2(config-if)# switchport mode access
S2(config-if)# switchport access vlan 30
S2(config-if)# end
```

**Quick Method (if configuring multiple ports):**
```cisco
S2(config)# interface fa0/11
S2(config-if)# switchport mode access
S2(config-if)# switchport access vlan 10
S2(config-if)# interface fa0/18
S2(config-if)# switchport mode access
S2(config-if)# switchport access vlan 20
S2(config-if)# interface fa0/6
S2(config-if)# switchport mode access
S2(config-if)# switchport access vlan 30
S2(config-if)# end
```

**Verify:**
```cisco
S2# show vlan brief
```

**Expected Output:**
```
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/7, Fa0/8, Fa0/9
                                                Fa0/10, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
10   Faculty/Staff                    active    Fa0/11
20   Students                         active    Fa0/18
30   Guest                            active    Fa0/6
99   Management&Native                active    
150  VOICE                            active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

---

### Step 2: Assign VLANs on S3

**Same port assignments as S2:**

```cisco
S3> enable
S3# configure terminal

S3(config)# interface fastEthernet 0/11
S3(config-if)# switchport mode access
S3(config-if)# switchport access vlan 10

S3(config-if)# interface fastEthernet 0/18
S3(config-if)# switchport mode access
S3(config-if)# switchport access vlan 20

S3(config-if)# interface fastEthernet 0/6
S3(config-if)# switchport mode access
S3(config-if)# switchport access vlan 30
S3(config-if)# end
```

---

### Step 3: Configure Voice VLAN on S3 Fa0/11

**Special configuration for IP Phone + PC:**

```cisco
S3> enable
S3# configure terminal
S3(config)# interface fastEthernet 0/11
S3(config-if)# mls qos trust cos
S3(config-if)# switchport voice vlan 150
S3(config-if)# end
```

**What this does:**
- **mls qos trust cos**: Trust QoS markings from IP phone
- **switchport voice vlan 150**: Voice traffic uses VLAN 150
- Port Fa0/11 now carries TWO VLANs:
  - VLAN 10 (data from PC4) - untagged
  - VLAN 150 (voice from IP Phone) - tagged

**Verify:**
```cisco
S3# show interfaces fa0/11 switchport
```

**Output:**
```
Name: Fa0/11
Switchport: Enabled
Administrative Mode: static access
Operational Mode: static access
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: native
Negotiation of Trunking: Off
Access Mode VLAN: 10 (Faculty/Staff)
Trunking Native Mode VLAN: 1 (default)
Voice VLAN: 150
```

---

### Step 4: Verify Loss of Connectivity

**Run on S2:**
```cisco
S2# show vlan brief
```

**Analysis:**
```
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/7, Fa0/8, Fa0/9
                                                Fa0/10, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
10   Faculty/Staff                    active    Fa0/11
20   Students                         active    Fa0/18
30   Guest                            active    Fa0/6
99   Management&Native                active    
150  VOICE                            active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

**Test Connectivity:**
```
PC1> ping 172.17.10.24
Failed! ❌
```

---

### Question 1: Although access ports are assigned to appropriate VLANs, were the pings successful? Explain.

**Answer:**

**No, pings were NOT successful.**

**Reason:**
- **Gig0/1 port is in VLAN 1** (default VLAN)
- Gig0/1 is the **inter-switch link** between S1 ↔ S2 and S2 ↔ S3
- VLAN 10 traffic from S2 Fa0/11 cannot reach S1 because Gig0/1 only carries VLAN 1
- **VLANs don't cross switches unless the link is configured as a trunk**

**Detailed Explanation:**
```
PC1 (VLAN 10) → S1 → Gig0/1 (VLAN 1 only!) ✗ Cannot pass VLAN 10 → S2 → PC4

VLANs are isolated!
Inter-switch links in VLAN 1 cannot carry VLAN 10, 20, or 30 traffic
```

---

### Question 2: What could be done to resolve this issue?

**Answer:**

**Configure Gig0/1 as a TRUNK port on all switches (S1, S2, S3).**

**Solution Configuration:**

**On S1:**
```cisco
S1(config)# interface gigabitEthernet 0/1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk native vlan 99
S1(config-if)# switchport trunk allowed vlan 10,20,30,99
S1(config-if)# end
```

**On S2:**
```cisco
S2(config)# interface gigabitEthernet 0/1
S2(config-if)# switchport mode trunk
S2(config-if)# switchport trunk native vlan 99
S2(config-if)# switchport trunk allowed vlan 10,20,30,99
S2(config-if)# end
```

**On S3 :**
```cisco
S3(config)# interface gigabitEthernet 0/2
S3(config-if)# switchport mode trunk
S3(config-if)# switchport trunk native vlan 99
S3(config-if)# switchport trunk allowed vlan 10,20,30,99,150
S3(config-if)# end
```

**Why this works:**
- **Trunk ports carry multiple VLANs** using 802.1Q tagging
- VLAN 10, 20, 30 traffic can now pass between switches
- Native VLAN 99 for untagged management traffic
- Voice VLAN 150 also allowed on trunk

**Verify trunk:**
```cisco
S1# show interfaces trunk
S1# show interfaces gigabitEthernet 0/1 switchport
```

---

## Complete Configuration Summary

### S1 Configuration
```cisco
! VLANs
vlan 10
 name Faculty/Staff
vlan 20
 name Students
vlan 30
 name Guest(Default)
vlan 99
 name Management&Native
vlan 150
 name VOICE

! Trunk port (to S2)
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
```

### S2 Configuration
```cisco
! VLANs
vlan 10
 name Faculty/Staff
vlan 20
 name Students
vlan 30
 name Guest(Default)
vlan 99
 name Management&Native
vlan 150
 name VOICE

! Access ports
interface fastEthernet 0/11
 switchport mode access
 switchport access vlan 10
interface fastEthernet 0/18
 switchport mode access
 switchport access vlan 20
interface fastEthernet 0/6
 switchport mode access
 switchport access vlan 30

! Trunk port (to S1)
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
```

### S3 Configuration
```cisco
! VLANs
vlan 10
 name Faculty/Staff
vlan 20
 name Students
vlan 30
 name Guest(Default)
vlan 99
 name Management&Native
vlan 150
 name VOICE

! Access ports
interface fastEthernet 0/11
 switchport mode access
 switchport access vlan 10
 mls qos trust cos
 switchport voice vlan 150
interface fastEthernet 0/18
 switchport mode access
 switchport access vlan 20
interface fastEthernet 0/6
 switchport mode access
 switchport access vlan 30

! Trunk port (to S1/S2)
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99,150
```

---

## Key Verification Commands

```cisco
! Show all VLANs (brief)
show vlan brief

! Show specific VLAN
show vlan id 10

! Show interface VLAN assignment
show interfaces fa0/11 switchport

! Show trunk ports
show interfaces trunk

! Show trunk status on specific interface
show interfaces gi0/1 trunk

! Show running configuration
show running-config

! Show VLAN configuration file
show vlan

! Show Voice VLAN assignment
show interfaces fa0/11 switchport | include Voice
```

---

## Common Mistakes to Avoid

❌ **Forgetting to set switchport mode access**
```cisco
! Wrong
switchport access vlan 10  

! Correct
switchport mode access
switchport access vlan 10
```

❌ **VLAN name case sensitivity**
```cisco
! Wrong
name Faculty/staff  

! Correct
name Faculty/Staff  (exact match required!)
```

❌ **Not configuring trunk ports between switches**
```cisco
! VLANs won't cross switches without trunks!
```

❌ **Forgetting Voice VLAN on IP Phone ports**
```cisco
! Must include both commands
mls qos trust cos
switchport voice vlan 150
```

---

## Lab Completion Checklist

✅ **Part 1:**
- [ ] Viewed default VLAN configuration
- [ ] Tested connectivity between same-subnet PCs
- [ ] Answered benefits of VLANs question

✅ **Part 2:**
- [ ] Created VLANs 10, 20, 30, 99, 150 on S1
- [ ] Verified with `show vlan brief`
- [ ] Created same VLANs on S2 and S3
- [ ] Verified on all switches

✅ **Part 3:**
- [ ] Assigned VLANs to ports on S2
- [ ] Assigned VLANs to ports on S3
- [ ] Configured Voice VLAN on S3 Fa0/11
- [ ] Verified loss of connectivity
- [ ] Answered why pings failed
- [ ] Identified solution (trunk configuration)

✅ **Bonus (if required):**
- [ ] Configured trunk ports on all switches
- [ ] Verified connectivity between VLANs across switches
- [ ] Tested PC1 can ping PC4 successfully

---

## Remember!

🔑 **VLANs segment networks logically**  
🔑 **Access ports = One VLAN per port**  
🔑 **Trunk ports = Multiple VLANs (inter-switch links)**  
🔑 **Voice VLAN = Special case (two VLANs on one port)**  
🔑 **Native VLAN = Untagged traffic on trunk**  
🔑 **Always verify configuration with show commands**
