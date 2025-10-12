# Lab Guide - Interpret Show Command Output

## Lab Overview

**Objective**: Learn to use and interpret Cisco IOS `show` commands for network troubleshooting and verification.

**Focus**: Understanding command output, not configuration

**Duration**: 30-45 minutes

**Difficulty**: Beginner to Intermediate

---

## Lab Setup Instructions

### Connecting to ISP Router

**Step 1: Access ISP PC**
1. Click on **ISP PC** in Packet Tracer
2. Click the **Desktop** tab
3. Click **Terminal**

**Step 2: Terminal Settings** (Usually auto-configured)
- Bits per second: 9600
- Data bits: 8
- Parity: None
- Stop bits: 1
- Flow Control: None
- Click **OK**

**Step 3: Enter Privileged EXEC Mode**
```
Router> enable
Router# 
```

Or if password required:
```
Router> enable
Password: [enter password]
ISPRouter#
```

---

## Essential Show Commands Reference

### Command Categories

**Interface Information**:
- `show ip interface brief` - Quick interface status
- `show interfaces` - Detailed interface information
- `show protocols` - Protocol status per interface

**Routing Information**:
- `show ip route` - Routing table
- `show ip protocols` - Routing protocol information

**System Information**:
- `show version` - IOS version and hardware info
- `show flash:` - Flash memory contents
- `show arp` - ARP cache

**Management Information**:
- `show users` - Active user sessions

---

## Part 1: Analyzing Show Command Output

### 1. Show ARP

**Command**:
```
ISPRouter# show arp
```

**Sample Output**:
```
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.1.1.1                -   0001.C731.4401  ARPA   GigabitEthernet0/0
Internet  10.1.1.2                5   0050.0F12.3456  ARPA   GigabitEthernet0/0
Internet  192.168.1.1             -   0001.C731.4402  ARPA   GigabitEthernet0/1
Internet  192.168.1.10            3   00D0.BA45.6789  ARPA   GigabitEthernet0/1
Internet  209.165.200.225         -   0001.C731.4403  ARPA   Serial0/0/0
```

**What This Shows**:
- **Protocol**: Network protocol (Internet = IPv4)
- **Address**: IP address
- **Age**: How long ago the entry was learned (- means local interface)
- **Hardware Addr**: MAC address (Layer 2)
- **Type**: ARP type (ARPA = standard Ethernet)
- **Interface**: Which interface knows this address

**Key Points**:
- Shows IP-to-MAC address mappings
- Age of "-" means it's the router's own interface
- Helps verify Layer 2 connectivity
- Useful for troubleshooting ARP issues

**Real-World Use**:
- Verify devices are responding to ARP
- Check if specific device is reachable at Layer 2
- Troubleshoot duplicate IP addresses

---

### 2. Show Flash

**Command**:
```
ISPRouter# show flash:
```

**Sample Output**:
```
System flash directory:
File  Length   Name/status
  3   486899376  c2900-universalk9-mz.SPA.151-4.M4.bin
  2   28282    sigdef-category.xml
  1   227537   sigdef-default.xml
[486903296 bytes used, 33685504 available, 520588800 total]
520192K bytes of processor board System flash (Read/Write)
```

**What This Shows**:
- **File**: File number
- **Length**: File size in bytes
- **Name/status**: Filename and status
- **Summary**: Used/available/total space

**Key Information**:
- IOS image file: `c2900-universalk9-mz.SPA.151-4.M4.bin`
- Total flash: 520,588,800 bytes (~520 MB)
- Available: 33,685,504 bytes (~33 MB)
- Used: 486,903,296 bytes (~487 MB)

**File Name Breakdown** (c2900-universalk9-mz.SPA.151-4.M4.bin):
- **c2900**: Router platform (2900 series)
- **universalk9**: Feature set (universal with crypto)
- **mz**: Runs from RAM, compressed
- **SPA**: Architecture type
- **151-4.M4**: IOS version 15.1(4)M4

**Real-World Use**:
- Check available space before IOS upgrade
- Verify IOS image is present
- Determine which IOS version is installed
- Manage flash memory

---

### 3. Show IP Route

**Command**:
```
ISPRouter# show ip route
```

**Sample Output**:
```
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is 209.165.200.226 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 209.165.200.226
      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        10.1.1.0/24 is directly connected, GigabitEthernet0/0
L        10.1.1.1/32 is directly connected, GigabitEthernet0/0
      192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.1.0/24 is directly connected, GigabitEthernet0/1
L        192.168.1.1/32 is directly connected, GigabitEthernet0/1
      209.165.200.0/24 is variably subnetted, 2 subnets, 2 masks
C        209.165.200.224/30 is directly connected, Serial0/0/0
L        209.165.200.225/32 is directly connected, Serial0/0/0
```

**Understanding the Output**:

**Route Codes**:
- **C** = Connected (directly attached network)
- **L** = Local (router's own IP address)
- **S** = Static route (manually configured)
- **S\*** = Default static route

**Route Entry Breakdown**:
```
S*    0.0.0.0/0 [1/0] via 209.165.200.226
```
- **S\***: Static default route
- **0.0.0.0/0**: Default network (matches all destinations)
- **[1/0]**: [Administrative Distance / Metric]
- **via 209.165.200.226**: Next-hop IP address

```
C     10.1.1.0/24 is directly connected, GigabitEthernet0/0
```
- **C**: Connected network
- **10.1.1.0/24**: Network address and prefix
- **GigabitEthernet0/0**: Exit interface

```
L     10.1.1.1/32 is directly connected, GigabitEthernet0/0
```
- **L**: Local route (router's own IP)
- **/32**: Host route (single IP address)

**Key Points**:
- Shows all known networks
- Indicates how networks were learned
- Shows next-hop or exit interface
- Gateway of last resort = default gateway

**Real-World Use**:
- Verify routing is working
- Troubleshoot connectivity issues
- Check if specific network is reachable
- Verify routing protocol operation

---

### 4. Show Interfaces

**Command**:
```
ISPRouter# show interfaces
```

**Sample Output** (for one interface):
```
GigabitEthernet0/0 is up, line protocol is up
  Hardware is CN Gigabit Ethernet, address is 0001.c731.4401 (bia 0001.c731.4401)
  Description: Connection to LAN
  Internet address is 10.1.1.1/24
  MTU 1500 bytes, BW 1000000 Kbit/sec, DLY 10 usec,
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Full Duplex, 1Gbps, media type is RJ45
  output flow-control is unsupported, input flow-control is unsupported
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:00:01, output 00:00:00, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/40 (size/max)
  5 minute input rate 1000 bits/sec, 2 packets/sec
  5 minute output rate 1000 bits/sec, 2 packets/sec
     1548 packets input, 146548 bytes, 0 no buffer
     Received 0 broadcasts (0 IP multicasts)
     0 runts, 0 giants, 0 throttles
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     0 watchdog, 1017 multicast, 0 pause input
     1549 packets output, 146659 bytes, 0 underruns
     0 output errors, 0 collisions, 1 interface resets
     0 unknown protocol drops
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier, 0 pause output
     0 output buffer failures, 0 output buffers swapped out
```

**Critical Status Lines**:

**Line 1**: `GigabitEthernet0/0 is up, line protocol is up`
- **First "up"**: Physical layer (Layer 1) - Cable connected, interface enabled
- **Second "up"**: Data link layer (Layer 2) - Protocol functioning

**Possible States**:
- **up, up**: ✅ Interface working perfectly
- **up, down**: ⚠️ Physical OK, protocol problem (wrong encapsulation, no keepalives)
- **down, down**: ❌ Physical problem (cable, shutdown, hardware failure)
- **administratively down, down**: Interface manually shutdown

**Important Fields**:

**MAC Address**:
```
address is 0001.c731.4401 (bia 0001.c731.4401)
```
- **address**: Current MAC address
- **bia**: Burned-in address (factory default)

**IP Address**:
```
Internet address is 10.1.1.1/24
```
Shows IP and subnet mask in CIDR notation

**Interface Speed**:
```
Full Duplex, 1Gbps, media type is RJ45
```
- **Duplex**: Full (transmit and receive simultaneously)
- **Speed**: 1 Gbps (1000 Mbps)
- **Media**: RJ45 connector type

**Traffic Statistics**:
```
5 minute input rate 1000 bits/sec, 2 packets/sec
5 minute output rate 1000 bits/sec, 2 packets/sec
1548 packets input, 146548 bytes
1549 packets output, 146659 bytes
```
Shows traffic load and counters

**Error Counters**:
```
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
0 output errors, 0 collisions, 1 interface resets
```
- **CRC errors**: Physical layer problems (bad cable, interference)
- **Collisions**: Normal in half-duplex, problem in full-duplex
- **Runts**: Frames smaller than 64 bytes
- **Giants**: Frames larger than 1518 bytes

**Real-World Use**:
- Verify interface status
- Check for errors indicating cable/hardware problems
- Monitor traffic statistics
- Troubleshoot duplex mismatches
- Verify IP configuration

---

### 5. Show IP Interface Brief

**Command**:
```
ISPRouter# show ip interface brief
```

**Sample Output**:
```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     10.1.1.1        YES manual up                    up
GigabitEthernet0/1     192.168.1.1     YES manual up                    up
GigabitEthernet0/2     unassigned      YES unset  administratively down down
Serial0/0/0            209.165.200.225 YES manual up                    up
Serial0/0/1            unassigned      YES unset  administratively down down
Vlan1                  unassigned      YES unset  administratively down down
```

**Column Descriptions**:

**Interface**: Interface name
- GigabitEthernet0/0 = Gig0/0 = G0/0 (same interface, different notation)

**IP-Address**: Assigned IP address
- "unassigned" = No IP configured

**OK?**: Configuration method working?
- **YES** = Configuration is valid
- **NO** = Problem with configuration

**Method**: How IP was assigned
- **manual** = Configured by administrator
- **DHCP** = Obtained from DHCP server
- **unset** = Not configured

**Status**: Physical layer (Layer 1)
- **up** = Interface enabled and cable connected
- **down** = Cable disconnected or hardware problem
- **administratively down** = Shutdown by admin

**Protocol**: Data link layer (Layer 2)
- **up** = Protocol functioning
- **down** = Protocol not functioning

**Interface Status Combinations**:

| Status | Protocol | Meaning | Action |
|--------|----------|---------|--------|
| up | up | ✅ Working | None needed |
| down | down | ❌ Physical problem | Check cable, hardware |
| admin down | down | 🔒 Shutdown | Use `no shutdown` |
| up | down | ⚠️ Protocol issue | Check encapsulation, clock |

**Real-World Use**:
- Quick overview of all interfaces
- Identify which interfaces are active
- Find IP addresses quickly
- Verify interface status at a glance
- MOST COMMONLY USED show command

---

### 6. Show Protocols

**Command**:
```
ISPRouter# show protocols
```

**Sample Output**:
```
Global values:
  Internet Protocol routing is enabled
GigabitEthernet0/0 is up, line protocol is up
  Internet address is 10.1.1.1/24
GigabitEthernet0/1 is up, line protocol is up
  Internet address is 192.168.1.1/24
GigabitEthernet0/2 is administratively down, line protocol is down
Serial0/0/0 is up, line protocol is up
  Internet address is 209.165.200.225/30
Serial0/0/1 is administratively down, line protocol is down
Vlan1 is administratively down, line protocol is down
```

**What This Shows**:
- Similar to `show ip interface brief`
- Shows interface status
- Shows IP addresses with subnet mask in CIDR format
- Confirms IP routing is enabled

**Differences from show ip interface brief**:
- Shows subnet mask as /24, /30 (CIDR notation)
- Focuses on Layer 3 protocol status
- Shows "Global values" section
- Less columnar, more descriptive

**Real-World Use**:
- Verify IP routing is enabled
- Quick check of interface protocols
- See IP addresses with prefix length
- Alternative to show ip interface brief

---

### 7. Show Users

**Command**:
```
ISPRouter# show users
```

**Sample Output**:
```
    Line       User       Host(s)              Idle       Location
   0 con 0                idle                 00:00:00   
*  2 vty 0     admin      idle                 00:00:00 10.1.1.10

  Interface    User               Mode         Idle     Peer Address
```

**Column Descriptions**:

**Line**: Connection type and number
- **con 0**: Console port
- **vty 0-15**: Virtual terminal lines (Telnet/SSH)
- **aux 0**: Auxiliary port (modem/dialup)

**User**: Username (if configured)
- Blank = no username authentication

**Host(s)**: Remote hostname (if applicable)

**Idle**: Time since last activity
- **00:00:00** = Currently active
- **00:05:23** = Idle for 5 minutes, 23 seconds

**Location**: IP address of remote connection
- Shows where user is connecting from

**Asterisk (\*)**: Current session (your connection)

**Sample Interpretations**:

```
* 2 vty 0    admin    idle    00:00:00 10.1.1.10
```
- This is YOUR current session (*)
- Connected via SSH/Telnet (vty 0)
- Username: admin
- Connected from 10.1.1.10
- Currently active (00:00:00 idle)

```
  0 con 0              idle    00:15:32
```
- Console connection exists
- No username shown
- Idle for 15 minutes, 32 seconds
- Someone connected but inactive

**Real-World Use**:
- Check who else is connected to the router
- Verify your own connection type
- Identify idle sessions
- Security audit of active connections
- Coordinate with other administrators

**Security Note**: If multiple users are connected, coordinate changes to avoid conflicts

---

### 8. Show Version

**Command**:
```
ISPRouter# show version
```

**Sample Output** (key sections):
```
Cisco IOS Software, C2900 Software (C2900-UNIVERSALK9-M), Version 15.1(4)M4, RELEASE SOFTWARE (fc2)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2012 by Cisco Systems, Inc.
Compiled Thurs 5-Jan-12 15:41 by pt_team

ROM: System Bootstrap, Version 15.0(1r)M15, RELEASE SOFTWARE (fc1)

ISPRouter uptime is 1 hour, 23 minutes
System returned to ROM by power-on
System image file is "flash0:c2900-universalk9-mz.SPA.151-4.M4.bin"
Last reload type: Normal Reload

This product contains cryptographic features and is subject to United
States and local country laws governing import, export, transfer and
use. Delivery of Cisco cryptographic products does not imply
third-party authority to import, export, distribute or use encryption.
Importers, exporters, distributors and users are responsible for
compliance with U.S. and local country laws. By using this product you
agree to comply with applicable laws and regulations. If you are unable
to comply with U.S. and local laws, return this product immediately.

A summary of U.S. laws governing Cisco cryptographic products may be found at:
http://www.cisco.com/wwl/export/crypto/tool/stqrg.html

If you require further assistance please contact us by sending email to
export@cisco.com.

Cisco CISCO2911/K9 (revision 1.0) with 491520K/32768K bytes of memory.
Processor board ID FTX152400KS
3 Gigabit Ethernet interfaces
2 Serial interfaces
1 terminal line
1 Virtual Private Network (VPN) Module
DRAM configuration is 64 bits wide with parity disabled.
255K bytes of non-volatile configuration memory.
500880K bytes of ATA System CompactFlash 0 (Read/Write)

Configuration register is 0x2102
```

**Critical Information to Extract**:

**IOS Version**:
```
Version 15.1(4)M4, RELEASE SOFTWARE (fc2)
```
- **15.1**: Major version
- **(4)**: Minor version
- **M4**: Maintenance release
- **fc2**: Feature consistency release

**Hardware Platform**:
```
Cisco CISCO2911/K9 (revision 1.0)
```
- **2911**: Router model
- **K9**: Contains cryptography features
- **revision 1.0**: Hardware revision

**Memory Information**:
```
491520K/32768K bytes of memory
```
- **491520K** = 491 MB RAM
- **32768K** = 32 MB NVRAM (approximately)

**Interfaces Available**:
```
3 Gigabit Ethernet interfaces
2 Serial interfaces
```
Lists physical interface types and quantities

**Uptime**:
```
ISPRouter uptime is 1 hour, 23 minutes
```
Time since last reload/boot

**IOS Image File**:
```
System image file is "flash0:c2900-universalk9-mz.SPA.151-4.M4.bin"
```
Exact filename and location of IOS

**Flash Memory**:
```
500880K bytes of ATA System CompactFlash 0
```
~501 MB flash storage available

**Configuration Register**:
```
Configuration register is 0x2102
```
- **0x2102**: Normal boot (default)
- **0x2142**: Skip startup-config (password recovery)

**Real-World Use**:
- Verify IOS version before upgrade
- Check hardware capabilities
- Confirm sufficient memory for features
- Troubleshoot boot problems
- Verify licensing requirements
- Document network inventory

---

## Part 2: Reflection Questions and Answers

### Question 1
**Which commands can you use to determine the IP address and network prefix of interfaces?**

**Answer**:
```
show interfaces
show protocols
show ip route
show running-config
```

**Detailed Explanation**:

**show interfaces** - Shows IP address with subnet mask in dotted decimal:
```
Internet address is 10.1.1.1/24
```

**show protocols** - Shows IP address with CIDR notation:
```
Internet address is 10.1.1.1/24
```

**show ip route** - Shows both connected networks and local host routes:
```
C    10.1.1.0/24 is directly connected, GigabitEthernet0/0
L    10.1.1.1/32 is directly connected, GigabitEthernet0/0
```

**show running-config** - Shows exact configuration:
```
interface GigabitEthernet0/0
 ip address 10.1.1.1 255.255.255.0
```

**Best Command**: `show interfaces` or `show protocols` for complete IP address and prefix information

---

### Question 2
**Which command provides the IP address and interface assignment, but not the network prefix?**

**Answer**:
```
show ip interface brief
```

**Explanation**:
This command shows IP addresses assigned to interfaces but does NOT show the subnet mask or prefix length.

**Sample Output**:
```
Interface              IP-Address      OK? Method Status Protocol
GigabitEthernet0/0     10.1.1.1        YES manual up     up
```
Shows "10.1.1.1" but NOT "/24" or "255.255.255.0"

**When to use it**: Quick overview of which interfaces have IPs assigned, without needing subnet details

---

### Question 3
**Which commands would you use to determine if an interface is up?**

**Answer**:
```
show ip interface brief
show interfaces
show protocols
```

**Detailed Explanation**:

**show ip interface brief** - ⭐ BEST for quick check:
```
Interface              IP-Address      OK? Method Status    Protocol
GigabitEthernet0/0     10.1.1.1        YES manual up        up
                                                  ↑         ↑
                                              Layer 1   Layer 2
```

**show interfaces** - Most detailed:
```
GigabitEthernet0/0 is up, line protocol is up
                      ↑                      ↑
                  Layer 1              Layer 2
```

**show protocols** - Alternative:
```
GigabitEthernet0/0 is up, line protocol is up
```

**Interface Status Meanings**:
- **up, up** = ✅ Interface fully operational
- **down, down** = ❌ Physical problem (cable/hardware)
- **administratively down, down** = 🔒 Interface shutdown
- **up, down** = ⚠️ Physical OK but protocol problem

**Recommended**: Use `show ip interface brief` for fastest results

---

### Question 4
**You need to determine the IOS version that is running on a router. Which command will give you this information?**

**Answer**:
```
show version
```

**Explanation**:
The `show version` command displays comprehensive system information including:
- IOS version number
- IOS feature set
- Uptime
- Hardware platform
- Memory amounts
- IOS image filename

**Sample Output** (relevant line):
```
Cisco IOS Software, C2900 Software (C2900-UNIVERSALK9-M), 
Version 15.1(4)M4, RELEASE SOFTWARE (fc2)
        ↑
    IOS Version
```

**Also shows**:
```
System image file is "flash0:c2900-universalk9-mz.SPA.151-4.M4.bin"
```

**Use Case**: Before upgrading IOS, always check current version and compatibility

---

### Question 5
**Which commands provide information about the addresses of the router interfaces?**

**Answer**:
```
show ip interface brief
show interfaces
show protocols
show ip route (for local routes)
show arp (for Layer 2 addresses)
show running-config
```

**Explanation**:

**For IP Addresses**:
- `show ip interface brief` - All IPs, no subnet mask
- `show interfaces` - IPs with subnet mask
- `show protocols` - IPs with CIDR notation
- `show ip route` - Connected and local routes

**For MAC Addresses**:
- `show interfaces` - Shows interface MAC address
- `show arp` - Shows MAC addresses of neighbors

**Example**:
```
show interfaces GigabitEthernet0/0
...
Hardware is CN Gigabit Ethernet, address is 0001.c731.4401
Internet address is 10.1.1.1/24
```
Shows both Layer 2 (MAC) and Layer 3 (IP) addresses

---

### Question 6
**You are considering an IOS upgrade and need to determine if router flash can hold the new IOS. Which commands provide information about the amount of Flash memory available?**

**Answer**:
```
show flash:
show version
```

**Detailed Explanation**:

**show flash:** - ⭐ BEST for flash details:
```
[486903296 bytes used, 33685504 available, 520588800 total]
520192K bytes of processor board System flash (Read/Write)
```
Shows:
- Used space: ~487 MB
- Available space: ~33 MB
- Total space: ~521 MB

**show version** - Also shows flash size:
```
500880K bytes of ATA System CompactFlash 0 (Read/Write)
```
Shows total flash memory (~501 MB)

**How to use this information**:
1. Check new IOS file size (example: 60 MB)
2. Compare to available space (33 MB in example)
3. Decision: Need to delete old files or upgrade flash memory

**Calculation**:
- Current IOS: 487 MB
- Available: 33 MB
- New IOS needs: 60 MB
- Result: NOT enough space (need to free up 27 MB)

---

### Question 7
**You need to adjust a router configuration, but you suspect that a colleague may also be working on the router from another location. Which command provides information about the lines being used for configuration or device monitoring?**

**Answer**:
```
show users
```

**Explanation**:
This command displays all active connections to the router, including:
- Who is connected
- Where they're connected from
- Which line they're using (console, vty, aux)
- How long they've been idle
- Which session is yours (*)

**Sample Output**:
```
    Line       User       Host(s)    Idle       Location
 *  2 vty 0    admin      idle       00:00:00   10.1.1.10
    3 vty 1    john       idle       00:05:23   10.1.1.15
```

**Interpretation**:
- YOU are connected via vty 0 (*)
- Another admin "john" is also connected via vty 1
- John has been idle for 5 minutes, 23 seconds
- John is connecting from 10.1.1.15

**Best Practice**: Before making major changes, check if others are connected and coordinate with them

**Additional useful command**:
```
show line
```
Shows configuration of all lines (console, vty, aux)

---

### Question 8
**You have been asked to check the performance of a device interface. Which command provides traffic statistics for router interfaces?**

**Answer**:
```
show interfaces
```

**Explanation**:
This command provides comprehensive traffic statistics including:

**Traffic Counters**:
```
5 minute input rate 1000 bits/sec, 2 packets/sec
5 minute output rate 1000 bits/sec, 2 packets/sec
1548 packets input, 146548 bytes
1549 packets output, 146659 bytes
```

**Error Statistics**:
```
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
0 output errors, 0 collisions, 1 interface resets
0 runts, 0 giants, 0 throttles
```

**Performance Metrics**:
- **Packets/sec**: Traffic rate
- **Bits/sec**: Bandwidth usage
- **Input/Output errors**: Quality indicators
- **CRC errors**: Physical layer problems
- **Collisions**: Duplex mismatch indicators
- **Runts/Giants**: Frame size problems

**What to look for**:
- ✅ **Low errors** = Good performance
- ⚠️ **Increasing errors** = Investigate cable/hardware
- ⚠️ **High collisions** = Possible duplex mismatch
- ⚠️ **High utilization** = May need bandwidth upgrade

**Additional command** for specific interface:
```
show interfaces GigabitEthernet0/0
```

---

### Question 9
**Customers are complaining that they cannot reach a server that they use for file storage. You suspect that the network may have become unreachable due to a recent upgrade. Which command provides information about the paths that are available for network traffic?**

**Answer**:
```
show ip route
```

**Explanation**:
The routing table shows all networks the router knows about and how to reach them.

**What to check when troubleshooting unreachable networks**:

1. **Does a route exist to the destination?**
```
ISPRouter# show ip route | include 192.168.10.0
C    192.168.10.0/24 is directly connected, GigabitEthernet0/2
```
If no output, route doesn't exist

2. **Is there a default route?**
```
S*   0.0.0.0/0 [1/0] via 209.165.200.226
```
Default route catches all unknown destinations

3. **Is the next-hop reachable?**
```
ISPRouter# ping 209.165.200.226
```

4. **Check routing protocol status** (if dynamic routing used):
```
show ip protocols
show ip ospf neighbor
show ip eigrp neighbors
```

**Troubleshooting scenario**:
```
Problem: Server at 192.168.50.10 unreachable

ISPRouter# show ip route | include 192.168.50
% No matching routes found

ISPRouter# show ip route
... (no route to 192.168.50.0/24 exists)

Diagnosis: Route was removed during upgrade
Solution: Add static route or configure routing protocol
```

**Additional useful commands**:
```
show ip route summary          - Overview of routing table
show ip route static           - Only static routes
show ip route connected        - Only connected routes
show ip route [network]        - Specific network details
```

---

### Question 10
**Which interfaces are currently active on the ISP Router?**

**Answer** (Will vary based on your Packet Tracer topology):
```
GigabitEthernet0/0 - UP
GigabitEthernet0/1 - UP  
Serial0/0/0 - UP
```

**How to determine**:
```
ISPRouter# show ip interface brief
```

Look for interfaces with Status = "up" and Protocol = "up"

**Sample Output**:
```
Interface              IP-Address      OK? Method Status    Protocol
GigabitEthernet0/0     10.1.1.1        YES manual up        up      ← ACTIVE
GigabitEthernet0/1     192.168.1.1     YES manual up        up      ← ACTIVE
GigabitEthernet0/2     unassigned      YES unset  admin down down  ← INACTIVE
Serial0/0/0            209.165.200.225 YES manual up        up      ← ACTIVE
Serial0/0/1            unassigned      YES unset  admin down down  ← INACTIVE
```

**Answer for typical topology**:
- **GigabitEthernet0/0**: Active (connects to LAN 1)
- **GigabitEthernet0/1**: Active (connects to LAN 2)
- **Serial0/0/0**: Active (WAN connection)
- **GigabitEthernet0/2**: Inactive (shutdown)
- **Serial0/0/1**: Inactive (shutdown)

**Note**: Your specific answer depends on the Packet Tracer file configuration

---

## Command Comparison Chart

### Quick Reference Table

| Command | Purpose | Shows IP? | Shows Subnet? | Shows Status? | Detail Level |
|---------|---------|-----------|---------------|---------------|--------------|
| show ip interface brief | Quick interface overview | ✅ | ❌ | ✅ | Low |
| show interfaces | Detailed interface info | ✅ | ✅ | ✅ | Very High |
| show protocols | Protocol status | ✅ | ✅ | ✅ | Medium |
| show ip route | Routing table | ✅ | ✅ | ❌ | Medium |
| show version | System info | ❌ | ❌ | ❌ | High |
| show flash | Flash contents | ❌ | ❌ | ❌ | Low |
| show arp | ARP cache | ✅ | ❌ | ❌ | Low |
| show users | Active sessions | ❌ | ❌ | ❌ | Low |

---

## Most Commonly Used Commands (Priority List)

### Essential Commands - Master These First

1. **show ip interface brief** ⭐⭐⭐⭐⭐
   - Most frequently used
   - Quick interface status check
   - First command in most troubleshooting

2. **show interfaces** ⭐⭐⭐⭐
   - Detailed troubleshooting
   - Error checking
   - Performance monitoring

3. **show ip route** ⭐⭐⭐⭐
   - Routing problems
   - Verify connectivity paths
   - Check routing protocols

4. **show running-config** ⭐⭐⭐⭐
   - View current configuration
   - Verify settings
   - Troubleshoot misconfigurations

5. **show version** ⭐⭐⭐
   - System information
   - IOS version verification
   - Hardware inventory

---

## Advanced Show Commands

### Commands for Deeper Troubleshooting

**Interface-Specific Commands**:
```
show interfaces GigabitEthernet0/0
show ip interface GigabitEthernet0/0
show controllers GigabitEthernet0/0
show interfaces GigabitEthernet0/0 stats
show interfaces GigabitEthernet0/0 switchport (on switches)
```

**Routing-Specific Commands**:
```
show ip route summary
show ip route static
show ip route connected
show ip route 192.168.1.0
show ip protocols
```

**Memory and Process Commands**:
```
show processes
show processes cpu
show memory
show memory statistics
```

**Configuration Commands**:
```
show running-config
show startup-config
show running-config interface GigabitEthernet0/0
show running-config | include ip address
show running-config | section interface
show running-config | begin line vty
```

**System Status Commands**:
```
show clock
show reload
show boot
show logging
show tech-support (comprehensive output for TAC)
```

---

## Using Command Filters

### Pipe Commands (|)

**Filter output with pipe commands**:

**| include** - Show only lines containing keyword:
```
ISPRouter# show running-config | include ip address
 ip address 10.1.1.1 255.255.255.0
 ip address 192.168.1.1 255.255.255.0
 ip address 209.165.200.225 255.255.255.252
```

**| exclude** - Hide lines containing keyword:
```
ISPRouter# show ip interface brief | exclude unassigned
Interface              IP-Address      OK? Method Status    Protocol
GigabitEthernet0/0     10.1.1.1        YES manual up        up
GigabitEthernet0/1     192.168.1.1     YES manual up        up
Serial0/0/0            209.165.200.225 YES manual up        up
```

**| section** - Show section starting with keyword:
```
ISPRouter# show running-config | section interface GigabitEthernet0/0
interface GigabitEthernet0/0
 description Connection to LAN
 ip address 10.1.1.1 255.255.255.0
 duplex auto
 speed auto
```

**| begin** - Show from keyword onward:
```
ISPRouter# show running-config | begin line vty
line vty 0 4
 password cisco
 login
line vty 5 15
 login
```

---

## Troubleshooting Workflow Using Show Commands

### Step-by-Step Troubleshooting Methodology

**Step 1: Quick Interface Check**
```
show ip interface brief
```
✅ Are interfaces up?  
✅ Do they have IP addresses?

**Step 2: Detailed Interface Analysis** (if issues found)
```
show interfaces [interface-name]
```
✅ Check for errors  
✅ Verify duplex/speed  
✅ Check input/output rates

**Step 3: Verify IP Configuration**
```
show protocols
show interfaces [interface-name]
```
✅ Correct IP and subnet?  
✅ Matching with network plan?

**Step 4: Check Routing**
```
show ip route
```
✅ Does route to destination exist?  
✅ Is next-hop reachable?  
✅ Default route present?

**Step 5: Test Connectivity**
```
ping [destination]
traceroute [destination]
```
✅ Can reach destination?  
✅ Where does traffic stop?

**Step 6: Check ARP** (Layer 2 issues)
```
show arp
```
✅ MAC address resolved?  
✅ Devices responding?

**Step 7: Verify System Health**
```
show version
show processes cpu
show memory
```
✅ Sufficient resources?  
✅ High CPU usage?  
✅ Memory issues?

---

## Common Troubleshooting Scenarios

### Scenario 1: Interface Down

**Symptom**: No connectivity through interface

**Commands to use**:
```
ISPRouter# show ip interface brief
Interface              IP-Address      OK? Method Status    Protocol
GigabitEthernet0/0     10.1.1.1        YES manual down      down
                                                  ↑
                                              Problem!
```

**Troubleshooting steps**:
```
ISPRouter# show interfaces GigabitEthernet0/0
GigabitEthernet0/0 is administratively down, line protocol is down
                      ↑
                  Shutdown command applied
```

**Solution**:
```
ISPRouter(config)# interface GigabitEthernet0/0
ISPRouter(config-if)# no shutdown
ISPRouter(config-if)# end
ISPRouter# show ip interface brief
Interface              IP-Address      OK? Method Status    Protocol
GigabitEthernet0/0     10.1.1.1        YES manual up        up
                                                  ↑         ↑
                                              Fixed!    Fixed!
```

---

### Scenario 2: High Interface Errors

**Symptom**: Slow or intermittent connectivity

**Commands to use**:
```
ISPRouter# show interfaces GigabitEthernet0/0
...
0 input errors, 125 CRC, 0 frame, 0 overrun, 0 ignored
                 ↑
              Problem: CRC errors
```

**What CRC errors indicate**:
- Bad cable
- Electromagnetic interference
- Bad network card
- Duplex mismatch

**Troubleshooting**:
```
ISPRouter# show interfaces GigabitEthernet0/0
...
Full Duplex, 1Gbps
```

Check if other end also shows Full Duplex - if not, there's a mismatch!

**Solution**:
- Replace cable
- Check for interference sources
- Manually set duplex on both ends
- Replace hardware if needed

---

### Scenario 3: Can't Reach Remote Network

**Symptom**: Ping to remote network fails

**Commands to use**:
```
ISPRouter# ping 192.168.50.10

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.50.10, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
```

**Check routing table**:
```
ISPRouter# show ip route | include 192.168.50
% No matching routes found
                ↑
            Problem: No route exists
```

**Check all routes**:
```
ISPRouter# show ip route

Gateway of last resort is not set
      ↑
  No default route either!

C    10.1.1.0/24 is directly connected, GigabitEthernet0/0
C    192.168.1.0/24 is directly connected, GigabitEthernet0/1
```

**Solution**: Add route
```
ISPRouter(config)# ip route 192.168.50.0 255.255.255.0 209.165.200.226
ISPRouter(config)# end
ISPRouter# show ip route
...
S    192.168.50.0/24 [1/0] via 209.165.200.226
```

---

### Scenario 4: Multiple Administrators Connected

**Symptom**: Need to make changes but want to coordinate

**Commands to use**:
```
ISPRouter# show users
    Line       User       Host(s)    Idle       Location
 *  2 vty 0    admin      idle       00:00:00   10.1.1.10
    3 vty 1    bob        idle       00:00:15   10.1.1.25
    0 con 0               idle       01:15:32
```

**Interpretation**:
- YOU are on vty 0 (*)
- User "bob" is on vty 1 from 10.1.1.25
- Bob is idle for 15 seconds (likely active)
- Someone left console connection idle for over an hour

**Action**:
- Contact bob before making changes
- Or wait until bob disconnects
- Disconnect idle console session if needed:
```
ISPRouter# clear line 0
```

---

## Command Output Interpretation Guide

### Reading "show ip interface brief"

**Perfect Interface**:
```
GigabitEthernet0/0     10.1.1.1        YES manual up        up
```
✅ Has IP address  
✅ Configuration is valid (YES)  
✅ Manually configured  
✅ Physical layer working (up)  
✅ Protocol layer working (up)

**Shutdown Interface**:
```
GigabitEthernet0/2     unassigned      YES unset  admin down down
```
❌ No IP configured  
🔒 Administratively shutdown  
⚠️ Cannot use until "no shutdown"

**Physical Problem**:
```
GigabitEthernet0/1     192.168.1.1     YES manual down      down
```
❌ Physical layer down (cable problem)  
❌ Protocol layer down  
🔧 Check cable connection

**Protocol Problem**:
```
Serial0/0/0            209.165.200.225 YES manual up        down
```
✅ Physical layer OK  
❌ Protocol layer down  
⚠️ Possible encapsulation mismatch or keepalive issue

---

### Reading "show interfaces" Error Counters

**Normal (Healthy) Interface**:
```
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
0 output errors, 0 collisions, 0 interface resets
0 runts, 0 giants
```
✅ All zeros = Perfect health

**Cable Problem**:
```
150 input errors, 145 CRC, 0 frame, 5 overrun, 0 ignored
```
⚠️ CRC errors = Physical layer problem  
🔧 Replace cable or check connections

**Duplex Mismatch**:
```
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
1250 output errors, 1230 collisions, 2 interface resets
```
⚠️ High collisions in full duplex = Duplex mismatch  
🔧 Verify duplex settings on both ends

**Frame Size Problems**:
```
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
125 runts, 75 giants
```
⚠️ Runts (<64 bytes) or Giants (>1518 bytes)  
🔧 Check MTU settings or hardware problems

---

## Tips for Exam Success

### CCNA Exam Focus Areas

**Most Tested Show Commands**:
1. ⭐ show ip interface brief
2. ⭐ show ip route  
3. ⭐ show interfaces
4. ⭐ show version
5. ⭐ show running-config

**Common Exam Questions**:
- "Which command shows interface status?" → show ip interface brief
- "How to verify IOS version?" → show version
- "How to check available flash?" → show flash
- "How to see who's connected?" → show users
- "How to check routing table?" → show ip route

**Exam Tips**:
1. **Memorize common outputs** - Recognize normal vs problem states
2. **Understand Layer 1/2 status** - "up up" vs "up down" vs "down down"
3. **Know command filters** - | include, | exclude, | section
4. **Practice interpretation** - Don't just run commands, understand output
5. **Troubleshooting methodology** - Systematic approach is key

---

## Practice Exercises

### Exercise 1: Interface Status Interpretation

Given this output, identify the problem:
```
Interface              IP-Address      OK? Method Status    Protocol
GigabitEthernet0/0     10.1.1.1        YES manual up        down
```

**Answer**: Layer 2 protocol problem. Physical layer is working (up) but data link layer is down. Check encapsulation type, keepalives, or clock rate (if serial).

---

### Exercise 2: Routing Table Analysis

Given this routing table output:
```
Gateway of last resort is not set

C    10.1.1.0/24 is directly connected, GigabitEthernet0/0
L    10.1.1.1/32 is directly connected, GigabitEthernet0/0
C    192.168.1.0/24 is directly connected, GigabitEthernet0/1
L    192.168.1.1/32 is directly connected, GigabitEthernet0/1
```

Question: Can this router reach 8.8.8.8?

**Answer**: NO. There's no default route ("Gateway of last resort is not set") and no specific route to 8.8.8.0/24. The router only knows about directly connected networks (10.1.1.0/24 and 192.168.1.0/24).

---

### Exercise 3: Flash Memory Calculation

Given this output:
```
[486903296 bytes used, 33685504 available, 520588800 total]
```

Question: New IOS is 55 MB. Can you install it?

**Answer**: NO. Available space is 33,685,504 bytes = ~32 MB. New IOS needs 55 MB. Must delete old IOS or other files first to free up ~23 MB more space.

---

### Exercise 4: Error Analysis

Given these interface statistics:
```
5 minute input rate 950000 bits/sec, 1200 packets/sec
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
0 output errors, 2500 collisions, 0 interface resets
Full Duplex, 1Gbps
```

Question: What's wrong?

**Answer**: Duplex mismatch suspected. Interface is configured for Full Duplex but has 2500 collisions, which should never happen in full duplex mode. Check the other end - likely configured for half duplex.

---

## Summary of Key Concepts

### Must-Know Information

✅ **show ip interface brief** - First command for any interface check  
✅ **show interfaces** - Detailed troubleshooting with error counters  
✅ **show ip route** - Verify routing and network reachability  
✅ **show version** - IOS version and hardware information  
✅ **show flash** - Available storage for IOS upgrades  
✅ **show users** - Check who else is connected  
✅ **show arp** - Layer 2 address resolution  
✅ **show protocols** - Interface protocol status with subnet info

### Interface Status Combinations

| Status | Protocol | Meaning | Action |
|--------|----------|---------|--------|
| up | up | ✅ Working perfectly | None |
| up | down | ⚠️ L1 OK, L2 problem | Check encapsulation, keepalives |
| down | down | ❌ Physical problem | Check cable, hardware |
| admin down | down | 🔒 Shutdown | Use "no shutdown" |

### Troubleshooting Order

1. **Physical** - Cables, LEDs, hardware
2. **Interface Status** - show ip interface brief
3. **Configuration** - show running-config
4. **Routing** - show ip route
5. **Connectivity** - ping, traceroute
6. **Performance** - show interfaces (errors)

---

## Additional Resources

### Related Commands for Further Study

**Configuration Commands**:
- show running-config
- show startup-config
- show configuration

**VLAN Commands** (for switches):
- show vlan brief
- show vlan
- show interfaces trunk

**Routing Protocol Commands**:
- show ip ospf
- show ip ospf neighbor
- show ip eigrp neighbors
- show ip bgp summary

**Security Commands**:
- show access-lists
- show ip access-lists
- show port-security

---

## Congratulations!

You've completed a comprehensive study of Cisco IOS show commands!

**What You've Learned**:
✅ How to analyze show command output  
✅ Interpret interface status and errors  
✅ Verify routing tables and network reachability  
✅ Check system resources and IOS versions  
✅ Monitor active users and sessions  
✅ Systematic troubleshooting approach  
✅ Common problems and their solutions

**These skills are essential for**:
- CCNA certification exam
- Network administration
- Troubleshooting connectivity issues
- Performance monitoring
- System maintenance

**Keep practicing these commands in Packet Tracer and on real equipment to build muscle memory!** 🎓

---

*End of Lab Guide - Interpret Show Command Output*