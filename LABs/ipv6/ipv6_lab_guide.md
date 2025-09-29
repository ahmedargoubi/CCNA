# Lab Guide: Identify IPv6 Addresses

## Overview

This lab helps you understand IPv6 addressing, including address types, compression/decompression techniques, and how to examine IPv6 settings on a Windows PC.

---

## Part 1: Practice with Different Types of IPv6 Addresses

### Step 1: Match IPv6 Addresses to Their Types

#### IPv6 Address Types Reference

- **Loopback Address**: `::1` - Used for testing on the local host
- **Global Unicast Address**: Starts with `2000::/3` (2000-3FFF) - Public, routable addresses
- **Link-Local Address**: Starts with `fe80::/10` - Used for local network communication only
- **Unique-Local Address**: Starts with `fc00::/7` or `fd00::/8` - Private addresses (similar to IPv4 private addresses)
- **Multicast Address**: Starts with `ff00::/8` - Used for one-to-many communication

#### Answer Key

| IPv6 Address | Answer | Explanation |
|--------------|--------|-------------|
| `2001:0db8:1:acad::fe55:6789:b210` | **b. Global unicast address** | Starts with 2001, within 2000::/3 range |
| `::1` | **a. Loopback address** | The only loopback address in IPv6 |
| `fc00:22:a:2::cd4:23e4:76fa` | **d. Unique-local address** | Starts with fc00, private address space |
| `2033:db8:1:1:22:a33d:259a:21fe` | **b. Global unicast address** | Starts with 2033, within 2000::/3 range |
| `fe80::3201:cc01:65b1` | **c. Link-local address** | Starts with fe80, link-local prefix |
| `ff00::` | **e. Multicast address** | Starts with ff00, multicast prefix |
| `ff00::db7:4322:a231:67c` | **e. Multicast address** | Starts with ff00, multicast prefix |
| `ff02::2` | **e. Multicast address** | Starts with ff02, multicast prefix (all-routers) |

---

### Step 2: Compress and Decompress IPv6 Addresses

#### IPv6 Compression Rules

1. **Remove leading zeros** in each hextet (group of 4 hex digits)
2. **Replace consecutive zeros** with `::` (can only be used once per address)
3. When decompressing, expand `::` to the appropriate number of zero hextets

#### Answers with Explanations

**a. Compress:** `2002:0ec0:0200:0001:0000:04eb:44ce:08a2`

**Answer:** `2002:ec0:200:1:0:4eb:44ce:8a2` or `2002:ec0:200:1::4eb:44ce:8a2`

*Explanation:* Remove leading zeros from each hextet. The single zero hextet can be replaced with `::` for maximum compression.

---

**b. Compress:** `fe80:0000:0000:0001:0000:60bb:008e:7402`

**Answer:** `fe80:0:0:1:0:60bb:8e:7402` or `fe80::1:0:60bb:8e:7402`

*Explanation:* Remove leading zeros. Multiple consecutive zero hextets can be replaced with `::`.

---

**c. Decompress:** `fe80::7042:b3d7:3dec:84b8`

**Answer:** `fe80:0000:0000:0000:7042:b3d7:3dec:84b8`

*Explanation:* The `::` represents 4 consecutive zero hextets. IPv6 addresses have 8 hextets total, so: 1 (fe80) + 4 (zeros) + 3 (7042:b3d7:3dec:84b8) = 8 hextets.

---

**d. Decompress:** `ff00::`

**Answer:** `ff00:0000:0000:0000:0000:0000:0000:0000`

*Explanation:* The `::` at the end represents 7 consecutive zero hextets (8 total minus 1).

---

**e. Compress:** `2001:0030:0001:acad:0000:330e:10c2:32bf`

**Answer:** `2001:30:1:acad:0:330e:10c2:32bf` or `2001:30:1:acad::330e:10c2:32bf`

*Explanation:* Remove leading zeros from each hextet. The single zero hextet can be compressed to `::`.

---

## Part 2: Examine a Host IPv6 Network Interface and Address

### Step 1: Check PC IPv6 Network Settings

#### For Windows 10/11:

1. **Open Control Panel**
   - Press `Windows + R`, type `control`, press Enter
   - OR search "Control Panel" in Start menu

2. **Navigate to Network Settings**
   - Click **Network and Sharing Center**
   - Click **Change adapter settings** (left sidebar)

3. **Check Adapter Properties**
   - Right-click your active network adapter (Ethernet or Wi-Fi)
   - Select **Properties**
   - Look for **Internet Protocol Version 6 (TCP/IPv6)**
   - Ensure it's checked (enabled)

4. **View IPv6 Properties**
   - Select **Internet Protocol Version 6 (TCP/IPv6)**
   - Click **Properties**
   - You'll likely see "Obtain an IPv6 address automatically"
   - Click **Cancel** to exit

#### Using Command Line (ipconfig):

1. **Open Command Prompt**
   - Press `Windows + R`, type `cmd`, press Enter
   - OR search "Command Prompt" in Start menu

2. **Run ipconfig Command**
   ```cmd
   ipconfig /all
   ```

3. **Analyze the Output**

Example output:
```
Wireless LAN adapter Wireless Network Connection:
   Connection-specific DNS Suffix  . :
   Description . . . . . . . . . . . : Intel(R) Centrino(R) Advanced-N 6200 AGN
   Physical Address. . . . . . . . . : 02-37-10-41-FB-48
   DHCP Enabled. . . . . . . . . . . : Yes
   Autoconfiguration Enabled . . . . : Yes
   Link-local IPv6 Address . . . . . : fe80::8d4f:4f4d:3237:95e2%14(Preferred)
   IPv4 Address. . . . . . . . . . . : 192.168.2.106(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.2.1
   DHCP Server . . . . . . . . . . . : 192.168.2.1
   DNS Servers . . . . . . . . . . . : 192.168.1.1
```

---

### Questions and Answers

#### Question 1: What does it indicate about the network regarding IPv6 global unicast address, IPv6 unique-local address, or IPv6 gateway address?

**Answer:** 

The absence of a global unicast address, unique-local address, or IPv6 gateway indicates that:

- The network is **NOT fully configured for IPv6** routing
- The router is **NOT advertising IPv6 prefixes** via Router Advertisement (RA)
- The network is operating in **IPv4-only mode** or dual-stack with only link-local IPv6
- **No DHCPv6 server** is providing IPv6 addresses
- The PC can only communicate with other devices on the same local link using IPv6

The PC has auto-configured only a link-local address (`fe80::/10`), which is sufficient for local network communication but cannot route to the internet or other networks.

---

#### Question 2: What kind of IPv6 addresses did you find when using ipconfig /all?

**Answer:**

From the example output, only a **Link-local IPv6 Address** was found:
- `fe80::8d4f:4f4d:3237:95e2%14`

This address:
- Starts with `fe80::`, identifying it as link-local
- Has a randomly generated 64-bit interface ID
- The `%14` is the zone ID (interface index)
- Is automatically configured (SLAAC - Stateless Address Autoconfiguration)
- Is only valid for communication on the local network segment

**Note:** In a properly configured IPv6 network, you might also see:
- Global unicast addresses (2000::/3)
- Unique-local addresses (fc00::/7)
- Temporary IPv6 addresses (for privacy)

---

## Reflection Questions

### Question 1: How do you think you must support IPv6 in the future?

**Suggested Answer:**

To support IPv6 in the future, networking professionals should:

- **Gain expertise** in IPv6 addressing, subnetting, and configuration
- **Understand dual-stack networks** (running both IPv4 and IPv6 simultaneously)
- **Learn IPv6 routing protocols** (OSPFv3, EIGRPv6, BGP4+)
- **Implement IPv6 security** (IPsec, firewall rules, RA Guard)
- **Configure network devices** for IPv6 support (routers, switches, firewalls)
- **Test and validate** IPv6 connectivity in production environments
- **Plan migration strategies** from IPv4-only to dual-stack or IPv6-only networks
- **Stay updated** on best practices and new IPv6 technologies
- **Educate users and stakeholders** about IPv6 benefits and requirements

---

### Question 2: Do you think IPv4 networks continue on, or will everyone eventually switch over to IPv6? How long do you think it will take?

**Suggested Answer:**

IPv4 networks will likely continue for many years due to:

**Reasons IPv4 persists:**
- Massive existing infrastructure investment
- NAT and private addressing extend IPv4 lifespan
- Many legacy devices and applications only support IPv4
- Organizational inertia and migration costs
- Dual-stack support allows gradual transition

**IPv6 adoption is inevitable because:**
- IPv4 address exhaustion is real (all addresses allocated since 2011)
- Mobile carriers and ISPs increasingly deploy IPv6
- IoT requires billions of addresses
- Improved security and performance with IPv6
- Global routing table efficiency

**Timeline estimate:**
- **Complete transition:** 10-20+ years (2035-2045)
- **Majority adoption:** 5-10 years for major networks
- **Dual-stack period:** Will last for decades
- Some IPv4 networks may never fully migrate

The transition will be gradual, with dual-stack configurations remaining standard for the foreseeable future.

---

## Additional IPv6 Commands and Tools

### Windows Commands

```cmd
# Display IPv6 routing table
netsh interface ipv6 show route

# Display IPv6 neighbors (similar to ARP for IPv4)
netsh interface ipv6 show neighbors

# Test IPv6 connectivity
ping -6 ipv6.google.com

# Trace IPv6 route
tracert -6 ipv6.google.com

# Display detailed IPv6 configuration
netsh interface ipv6 show config
```

### Linux/Mac Commands

```bash
# Display IPv6 addresses
ip -6 addr show

# Display IPv6 routing table
ip -6 route show

# Display IPv6 neighbors
ip -6 neigh show

# Test IPv6 connectivity
ping6 ipv6.google.com

# Trace IPv6 route
traceroute6 ipv6.google.com
```

---

## Key Takeaways

1. **IPv6 has 128-bit addresses** (vs. 32-bit for IPv4)
2. **Eight hextets** (groups of 4 hexadecimal digits) separated by colons
3. **Compression rules** simplify address representation
4. **Multiple address types** serve different purposes
5. **Link-local addresses** (fe80::/10) are automatically configured
6. **Global unicast addresses** require router configuration
7. **IPv6 doesn't require DHCP** - uses SLAAC (Stateless Address Autoconfiguration)
8. **Dual-stack** (IPv4 + IPv6) is the current standard approach

---

## Conclusion

This lab provides hands-on experience with IPv6 addressing fundamentals. Understanding IPv6 is essential for modern networking professionals as the internet transitions from IPv4 to IPv6. Practice these concepts regularly to build proficiency in IPv6 network configuration and troubleshooting.

---

**End of Lab Guide**