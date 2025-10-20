# DHCPv4 Configuration Lab - Complete Guide

## Lab Overview

**Objective:** Configure a Cisco router (R2) as a DHCP server to provide IP addresses to clients on remote LANs, configure DHCP relay, and configure R2 as a DHCP client.

**Topology:**
```
PC1 (DHCP) ─── R1 ─── R2 (DHCP Server) ─── R3 ─── PC2 (DHCP)
  192.168.10.0/24    10.1.1.0/30    10.2.2.0/30    192.168.30.0/24
                          │
                          └── ISP (DHCP Server)
                         (R2 G0/1 gets IP via DHCP)
```

**Key Concepts:**
- **R2** acts as DHCP server for R1 and R3 LANs
- **R1 and R3** act as DHCP relay agents (forward DHCP requests)
- **R2 G0/1** acts as DHCP client (receives IP from ISP)

---

## Part 1: Configure a Router as a DHCP Server

### Understanding DHCP Server Configuration

**DHCP Server Components:**
1. **Excluded addresses** - IPs NOT given to clients (routers, servers)
2. **DHCP pool** - Range of IPs available for clients
3. **Network statement** - Defines the subnet
4. **Default-router** - Gateway for clients
5. **DNS-server** - DNS server IP for clients

---

### Step 1: Configure Excluded IPv4 Addresses

**Why exclude addresses?**
- Router interfaces already have static IPs
- Servers need static IPs
- Management devices need static IPs
- Avoids duplicate IP address conflicts

**Addresses to exclude:**
- R1 LAN: 192.168.10.1 (router) + 9 others = .1 to .10
- R3 LAN: 192.168.30.1 (router) + 9 others = .1 to .10

#### Configure on R2

**Exclude first 10 addresses from R1 LAN:**

```cisco
R2> enable
R2# configure terminal
R2(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10
```

**This excludes:**
```
192.168.10.1  - R1's G0/0 interface
192.168.10.2  - Reserved for server/printer
192.168.10.3  - Reserved
...
192.168.10.10 - Reserved
```

**Exclude first 10 addresses from R3 LAN:**

```cisco
R2(config)# ip dhcp excluded-address 192.168.30.1 192.168.30.10
```

**This excludes:**
```
192.168.30.1  - R3's G0/0 interface
192.168.30.2  - Reserved for server/printer
192.168.30.3  - Reserved
...
192.168.30.10 - Reserved
```

**Verify excluded addresses:**

```cisco
R2# show ip dhcp binding
R2# show ip dhcp excluded-address
```

---

### Step 2: Create DHCP Pool for R1 LAN

**DHCP Pool contains:**
- Network address and subnet mask
- Default gateway (R1's interface)
- DNS server address

#### Create Pool on R2

**Step 1: Create pool named R1-LAN:**

```cisco
R2(config)# ip dhcp pool R1-LAN
R2(dhcp-config)#
```

**Note:** Pool name is **case-sensitive** - must be exactly "R1-LAN"

**Step 2: Configure network statement:**

```cisco
R2(dhcp-config)# network 192.168.10.0 255.255.255.0
```

**This defines:**
- Network: 192.168.10.0/24
- Available IPs: 192.168.10.11 to 192.168.10.254 (after exclusions)

**Step 3: Configure default gateway:**

```cisco
R2(dhcp-config)# default-router 192.168.10.1
```

**This is R1's G0/0 interface - the gateway for clients**

**Step 4: Configure DNS server:**

```cisco
R2(dhcp-config)# dns-server 192.168.20.254
```

**Complete configuration:**

```cisco
R2(dhcp-config)# exit
R2(config)#
```

**Verify R1-LAN pool:**

```cisco
R2# show ip dhcp pool R1-LAN
```

**Expected output:**
```
Pool R1-LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 254
 Leased addresses               : 0
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.10.11        192.168.10.1     - 192.168.10.254   0
```

---

### Step 3: Create DHCP Pool for R3 LAN

**Same process as R1 LAN, different network:**

#### Create Pool on R2

```cisco
R2(config)# ip dhcp pool R3-LAN
R2(dhcp-config)# network 192.168.30.0 255.255.255.0
R2(dhcp-config)# default-router 192.168.30.1
R2(dhcp-config)# dns-server 192.168.20.254
R2(dhcp-config)# exit
```

**Breakdown:**
- **Pool name:** R3-LAN (case-sensitive!)
- **Network:** 192.168.30.0/24
- **Available IPs:** 192.168.30.11 to 192.168.30.254
- **Gateway:** 192.168.30.1 (R3's G0/0)
- **DNS:** 192.168.20.254 (same DNS server)

**Verify R3-LAN pool:**

```cisco
R2# show ip dhcp pool R3-LAN
```

---

### Verify All DHCP Configuration on R2

```cisco
R2# show running-config | section dhcp
```

**Expected output:**
```
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.30.1 192.168.30.10
!
ip dhcp pool R1-LAN
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 192.168.20.254
!
ip dhcp pool R3-LAN
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 192.168.20.254
```

---

## Part 2: Configure DHCP Relay

### Understanding DHCP Relay

**The Problem:**
- DHCP uses **broadcast messages**
- Broadcasts **don't cross routers**
- PC1 is on R1 LAN, but DHCP server is on R2
- PC1's DHCP broadcast won't reach R2

**The Solution: DHCP Relay (ip helper-address)**

```
PC1 broadcasts → R1 receives → R1 forwards to R2 (unicast) → R2 responds
```

**How it works:**
1. PC1 sends DHCP Discover (broadcast)
2. R1 receives it on G0/0
3. R1 converts broadcast to unicast
4. R1 forwards to R2 at 10.1.1.2 (helper address)
5. R2 processes DHCP request
6. R2 sends DHCP Offer back to R1
7. R1 forwards to PC1

---

### Step 1: Configure R1 as DHCP Relay Agent

**R1 must forward DHCP requests to R2**

```cisco
R1> enable
R1# configure terminal
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip helper-address 10.1.1.2
R1(config-if)# exit
```

**What this does:**
- Applied to **LAN interface** (G0/0) where clients are
- **Helper address** is R2's serial interface (10.1.1.2)
- R1 forwards DHCP broadcasts to R2

**Why 10.1.1.2?**
- This is R2's S0/0/0 interface
- The WAN connection between R1 and R2
- R2 will receive and process DHCP requests

---

### Step 2: Configure R3 as DHCP Relay Agent

**R3 must also forward DHCP requests to R2**

```cisco
R3> enable
R3# configure terminal
R3(config)# interface gigabitEthernet 0/0
R3(config-if)# ip helper-address 10.2.2.2
R3(config-if)# exit
```

**What this does:**
- Applied to **R3's LAN interface** (G0/0) where PC2 is
- **Helper address** is R2's S0/0/1 interface (10.2.2.2)
- R3 forwards DHCP broadcasts to R2

**Verify configuration:**

```cisco
R1# show ip interface gigabitEthernet 0/0
```

**Look for:**
```
Helper address is 10.1.1.2
```

```cisco
R3# show ip interface gigabitEthernet 0/0
```

**Look for:**
```
Helper address is 10.2.2.2
```

---

### Step 3: Configure Hosts to Use DHCP

**Configure PC1 (on R1 LAN):**

1. Click on PC1
2. Desktop tab
3. IP Configuration
4. Select **DHCP** (instead of Static)
5. Click **DHCP** button or wait for automatic assignment

**Expected result:**
```
IP Address: 192.168.10.11 (or .12, .13, etc.)
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.10.1
DNS Server: 192.168.20.254
```

**Configure PC2 (on R3 LAN):**

1. Click on PC2
2. Desktop tab
3. IP Configuration
4. Select **DHCP**
5. Click **DHCP** button

**Expected result:**
```
IP Address: 192.168.30.11 (or .12, .13, etc.)
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.30.1
DNS Server: 192.168.20.254
```

**Troubleshooting if PCs don't get IPs:**
- Use **Fast Forward Time** in Packet Tracer
- Release and renew: `ipconfig /release` then `ipconfig /renew`
- Check DHCP relay configuration on R1/R3
- Verify DHCP pools on R2

---

## Part 3: Configure a Router as a DHCP Client

### Understanding Router as DHCP Client

**Scenario:** R2 connects to ISP, and ISP uses DHCP to assign IPs to customers

**Instead of static IP:** R2's G0/1 interface gets IP automatically from ISP

---

### Configure R2 G0/1 as DHCP Client

**Step 1: Enter interface configuration:**

```cisco
R2> enable
R2# configure terminal
R2(config)# interface gigabitEthernet 0/1
R2(config-if)#
```

**Step 2: Configure interface to use DHCP:**

```cisco
R2(config-if)# ip address dhcp
```

**This tells the interface:**
- Don't use static IP
- Request IP from DHCP server
- Act as DHCP client

**Step 3: Activate the interface:**

```cisco
R2(config-if)# no shutdown
R2(config-if)# exit
```

**Step 4: Wait for DHCP assignment:**

In Packet Tracer:
- Use **Fast Forward Time** feature (Alt + D)
- Or wait a few seconds

**Verify R2 received IP:**

```cisco
R2# show ip interface brief
```

**Expected output:**
```
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     192.168.20.1    YES manual up                    up 
GigabitEthernet0/1     64.100.X.X      YES DHCP   up                    up  ← DHCP assigned!
Serial0/0/0            10.1.1.2        YES manual up                    up 
Serial0/0/1            10.2.2.2        YES manual up                    up
```

**Key indicators:**
- **Method: DHCP** (not manual)
- **Status: up**
- **IP address assigned** by ISP

**Detailed DHCP client information:**

```cisco
R2# show dhcp lease
```

**Shows:**
- IP address assigned
- Lease duration
- DHCP server address
- Renewal times

---

## Part 4: Verify DHCP and Connectivity

### Step 1: Verify DHCP Bindings on R2

**DHCP bindings show which IPs are assigned to which clients**

```cisco
R2# show ip dhcp binding
```

**Expected output:**
```
IP address       Client-ID/              Lease expiration        Type
                 Hardware address
192.168.10.11    0002.4AA5.1470          --                      Automatic
192.168.30.11    0004.9A97.2535          --                      Automatic
```

**What this shows:**
- **192.168.10.11** - Assigned to PC1 (MAC: 0002.4AA5.1470)
- **192.168.30.11** - Assigned to PC2 (MAC: 0004.9A97.2535)
- **Type: Automatic** - Assigned by DHCP server

**Detailed binding information:**

```cisco
R2# show ip dhcp binding 192.168.10.11
```

---

### Step 2: Verify DHCP Pool Statistics

```cisco
R2# show ip dhcp pool
```

**Expected output:**
```
Pool R1-LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 254
 Leased addresses               : 1  ← PC1 got an IP
 Pending event                  : none

Pool R3-LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 254
 Leased addresses               : 1  ← PC2 got an IP
 Pending event                  : none
```

---

### Step 3: Verify DHCP Server Statistics

```cisco
R2# show ip dhcp server statistics
```

**Shows:**
- Messages received (Discover, Request)
- Messages sent (Offer, Acknowledge)
- Database operations

---

### Step 4: Verify Configurations on Clients

**On PC1:**

```
C:\> ipconfig

IPv4 Address: 192.168.10.11
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.10.1
DNS Server: 192.168.20.254
```

**On PC2:**

```
C:\> ipconfig

IPv4 Address: 192.168.30.11
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.30.1
DNS Server: 192.168.20.254
```

---

### Step 5: Test Connectivity

**From PC1:**

```
C:\> ping 192.168.10.1
Reply from 192.168.10.1 (R1 G0/0) ✅

C:\> ping 192.168.30.11
Reply from 192.168.30.11 (PC2) ✅

C:\> ping 192.168.20.254
Reply from 192.168.20.254 (DNS Server) ✅
```

**From PC2:**

```
C:\> ping 192.168.30.1
Reply from 192.168.30.1 (R3 G0/0) ✅

C:\> ping 192.168.10.11
Reply from 192.168.10.11 (PC1) ✅

C:\> ping 192.168.20.254
Reply from 192.168.20.254 (DNS Server) ✅
```

**Test DNS resolution (if configured):**

```
C:\> ping www.cisco.com
```

---

## Complete Configuration Summary

### R1 Configuration

```cisco
hostname R1

interface GigabitEthernet0/0
 ip address 192.168.10.1 255.255.255.0
 ip helper-address 10.1.1.2
 no shutdown

interface Serial0/0/0
 ip address 10.1.1.1 255.255.255.252
 no shutdown
```

---

### R2 Configuration (DHCP Server)

```cisco
hostname R2

! Excluded addresses
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.30.1 192.168.30.10

! DHCP Pool for R1 LAN
ip dhcp pool R1-LAN
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 192.168.20.254

! DHCP Pool for R3 LAN
ip dhcp pool R3-LAN
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 192.168.20.254

interface GigabitEthernet0/0
 ip address 192.168.20.1 255.255.255.0
 no shutdown

! R2 as DHCP client
interface GigabitEthernet0/1
 ip address dhcp
 no shutdown

interface Serial0/0/0
 ip address 10.1.1.2 255.255.255.252
 no shutdown

interface Serial0/0/1
 ip address 10.2.2.2 255.255.255.252
 no shutdown
```

---

### R3 Configuration

```cisco
hostname R3

interface GigabitEthernet0/0
 ip address 192.168.30.1 255.255.255.0
 ip helper-address 10.2.2.2
 no shutdown

interface Serial0/0/1
 ip address 10.2.2.1 255.255.255.252
 no shutdown
```

---

## Essential Verification Commands

```cisco
! Show DHCP configuration
show running-config | section dhcp

! Show DHCP pools
show ip dhcp pool
show ip dhcp pool R1-LAN

! Show DHCP bindings (assigned IPs)
show ip dhcp binding

! Show excluded addresses
show ip dhcp excluded-address

! Show DHCP statistics
show ip dhcp server statistics

! Show DHCP conflict
show ip dhcp conflict

! Show interface DHCP client status
show dhcp lease

! Show helper address configuration
show ip interface gigabitEthernet 0/0
```

---

## Troubleshooting Guide

### Problem: PC not getting IP address

**Checklist:**
```
1. Verify DHCP pool configured on R2
   show ip dhcp pool

2. Check excluded addresses don't block all IPs
   show ip dhcp excluded-address

3. Verify helper-address on router interface
   show ip interface g0/0 | include Helper

4. Check PC set to DHCP (not static)
   ipconfig (on PC)

5. Release and renew on PC
   ipconfig /release
   ipconfig /renew

6. Use Fast Forward Time in Packet Tracer
```

---

### Problem: Wrong IP address assigned

**Check:**
- DHCP pool network statement
- Excluded address range
- Verify correct pool being used

---

### Problem: No default gateway on PC

**Check:**
- default-router statement in DHCP pool
- Verify it matches router's LAN interface IP

---

### Problem: DHCP relay not working

**Check:**
```
1. Helper-address configured on correct interface
   (LAN interface, not WAN)

2. Helper-address points to correct IP
   (R2's reachable interface)

3. Routing between routers working
   ping 10.1.1.2 (from R1)
```

---

## Lab Completion Checklist

### Part 1: Configure Router as DHCP Server ✅
- [ ] Excluded addresses configured (192.168.10.1-10, 192.168.30.1-10)
- [ ] Created R1-LAN pool
- [ ] Configured network, default-router, dns-server for R1-LAN
- [ ] Created R3-LAN pool
- [ ] Configured network, default-router, dns-server for R3-LAN
- [ ] Verified pools with show commands

### Part 2: Configure DHCP Relay ✅
- [ ] Configured helper-address on R1 G0/0 (10.1.1.2)
- [ ] Configured helper-address on R3 G0/0 (10.2.2.2)
- [ ] Configured PC1 to use DHCP
- [ ] Configured PC2 to use DHCP
- [ ] Verified PCs received IPs from correct pools

### Part 3: Configure Router as DHCP Client ✅
- [ ] Configured R2 G0/1 with "ip address dhcp"
- [ ] Activated interface with "no shutdown"
- [ ] Verified R2 received IP from ISP
- [ ] Confirmed Method shows "DHCP" in show ip interface brief

### Part 4: Verify DHCP and Connectivity ✅
- [ ] Verified DHCP bindings on R2
- [ ] Checked IP assignments (PC1: 192.168.10.x, PC2: 192.168.30.x)
- [ ] Tested PC1 to PC2 connectivity
- [ ] Tested PC1 to DNS server
- [ ] Tested PC2 to DNS server
- [ ] All pings successful

---

## Key Takeaways

🔑 **DHCP server provides**: IP, subnet mask, gateway, DNS  
🔑 **Excluded addresses**: Prevent router/server IP conflicts  
🔑 **DHCP pools**: Define network and parameters for clients  
🔑 **ip helper-address**: Forwards DHCP broadcasts across routers  
🔑 **Applied to LAN interface**: Where clients are located  
🔑 **Points to DHCP server**: Reachable interface on server router  
🔑 **Router as DHCP client**: Uses "ip address dhcp"  
🔑 **Pool names**: Case-sensitive!  
🔑 **Verify bindings**: show ip dhcp binding  
🔑 **DHCP uses broadcasts**: Need relay for remote networks