# Static Routing CCNA Quiz - 15 Questions
## Real CCNA Exam Style

---

### Question 1 ⭐ (Command Analysis - Very Common)

A network administrator enters the following command on a router:
```
Router(config)# ip route 192.168.1.0 255.255.255.0 10.0.0.2
```

What type of route is configured?

A) Default static route  
B) Floating static route  
C) Standard static route with next-hop address  
D) Directly connected static route  

---

### Question 2 ⭐ (Administrative Distance - Always Tested)

Which command configures a floating static route to network 172.16.0.0/16 with next-hop 10.1.1.2 that will only be used when the primary route fails?

A) `ip route 172.16.0.0 255.255.0.0 10.1.1.2`  
B) `ip route 172.16.0.0 255.255.0.0 10.1.1.2 1`  
C) `ip route 172.16.0.0 255.255.0.0 10.1.1.2 5`  
D) `ip route 172.16.0.0 255.255.0.0 10.1.1.2 110`  

---

### Question 3 ⭐ (Default Route - Frequently Asked)

Which command configures a default static route on a router?

A) `ip route 0.0.0.0 0.0.0.0 203.0.113.1`  
B) `ip route 255.255.255.255 255.255.255.255 203.0.113.1`  
C) `ip route any any 203.0.113.1`  
D) `ip default-route 203.0.113.1`  

---

### Question 4 ⭐ (Routing Table Analysis - Common)

Examine the routing table entry:
```
S    172.16.1.0/24 [5/0] via 10.0.0.3
```

What does the value "5" represent?

A) Metric  
B) Administrative distance  
C) Hop count  
D) Cost  

---

### Question 5 ⭐ (IPv6 Static Route - Always Tested)

What is the correct command to configure an IPv6 default route with next-hop address 2001:db8:feed::1?

A) `ipv6 route 0:0:0:0:0:0:0:0/0 2001:db8:feed::1`  
B) `ipv6 route ::/0 2001:db8:feed::1`  
C) `ipv6 route default 2001:db8:feed::1`  
D) `ipv6 default-route 2001:db8:feed::1`  

---

### Question 6 ⭐ (Next-Hop Types - Very Frequent)

A network administrator needs to configure a static route on a point-to-point serial link. Which configuration is most appropriate?

A) Specify only the next-hop IP address  
B) Specify only the exit interface  
C) Specify both exit interface and next-hop IP address  
D) Use a dynamic routing protocol instead  

---

### Question 7 ⭐ (Scenario-Based - Exam Favorite)

A company has two connections to the Internet. The primary connection uses OSPF (AD 110) to learn routes. The administrator wants to configure a backup default route that will only be used if OSPF fails. Which command should be used?

A) `ip route 0.0.0.0 0.0.0.0 203.0.113.1 1`  
B) `ip route 0.0.0.0 0.0.0.0 203.0.113.1 110`  
C) `ip route 0.0.0.0 0.0.0.0 203.0.113.1 120`  
D) `ip route 0.0.0.0 0.0.0.0 203.0.113.1 90`  

---

### Question 8 ⭐ (Host Route - Common)

What type of route is created with the following command?
```
Router(config)# ip route 192.168.1.100 255.255.255.255 10.0.0.2
```

A) Default route  
B) Summary route  
C) Host route  
D) Network route  

---

### Question 9 ⭐ (Multiple Routes - Frequently Tested)

A router has the following static routes configured:
```
ip route 172.16.1.0 255.255.255.0 10.0.0.2
ip route 172.16.1.0 255.255.255.0 10.0.0.3 5
```

What will happen to traffic destined for 172.16.1.0/24?

A) Load balanced between both next-hops  
B) Sent via 10.0.0.2 only  
C) Sent via 10.0.0.3 only  
D) Dropped due to configuration conflict  

---

### Question 10 ⭐ (IPv6 Link-Local - Important)

An administrator configures the following IPv6 static route:
```
ipv6 route 2001:db8:1::/64 fe80::1
```

Why will this configuration fail?

A) The prefix length is incorrect  
B) Link-local addresses require the exit interface to be specified  
C) IPv6 static routes don't support link-local addresses  
D) The syntax is incorrect  

---

### Question 11 ⭐ (Summary Route - Often Tested)

A network has the following subnets:
- 192.168.16.0/24
- 192.168.17.0/24
- 192.168.18.0/24
- 192.168.19.0/24

Which summary route encompasses all four subnets?

A) `ip route 192.168.0.0 255.255.240.0 10.0.0.2`  
B) `ip route 192.168.16.0 255.255.252.0 10.0.0.2`  
C) `ip route 192.168.16.0 255.255.248.0 10.0.0.2`  
D) `ip route 192.168.0.0 255.255.0.0 10.0.0.2`  

---

### Question 12 ⭐ (Command Syntax - Common Error)

Which statement about static route configuration is correct?

A) IPv4 static routes always require a subnet mask  
B) Exit interface must always be specified  
C) Administrative distance cannot be manually configured  
D) Next-hop address must always be on a different subnet  

---

### Question 13 ⭐ (Verification - Exam Favorite)

Which command displays ONLY static routes in the routing table?

A) `show ip route`  
B) `show ip route static`  
C) `show static route`  
D) `show ip static`  

---

### Question 14 ⭐ (Fully Specified Route - Important Concept)

What is the advantage of configuring a fully specified static route (both exit interface and next-hop address)?

A) Reduces administrative distance  
B) Eliminates recursive route lookups  
C) Enables load balancing automatically  
D) Allows the route to work with any routing protocol  

---

### Question 15 ⭐ (Complex Scenario - Exam Style)

Network topology:
- Router R1 is connected to network 10.1.1.0/24 via interface Gi0/0
- Router R1 needs to reach network 172.16.0.0/16
- Next-hop router is at 10.1.1.254

Which command correctly configures a fully specified static route?

A) `ip route 172.16.0.0 255.255.0.0 10.1.1.254`  
B) `ip route 172.16.0.0 255.255.0.0 GigabitEthernet0/0`  
C) `ip route 172.16.0.0 255.255.0.0 GigabitEthernet0/0 10.1.1.254`  
D) `ip route 10.1.1.0 255.255.255.0 172.16.0.0`  

---

## ANSWER KEY & EXPLANATIONS

**Question 1: C** - Standard static route with next-hop address  
*Explanation: This is a recursive static route to network 192.168.1.0/24 via next-hop 10.0.0.2. It's not default (would be 0.0.0.0/0), not floating (no AD specified), and not directly connected (uses IP, not interface).*

**Question 2: C** - `ip route 172.16.0.0 255.255.0.0 10.1.1.2 5`  
*Explanation: Floating static route requires higher AD than primary. Default static AD = 1, so 5 is higher and acts as backup. AD 1 would be primary, 110 would be higher than OSPF.*

**Question 3: A** - `ip route 0.0.0.0 0.0.0.0 203.0.113.1`  
*Explanation: Default route uses 0.0.0.0/0 (quad-zero) to match all destinations. This is the gateway of last resort.*

**Question 4: B** - Administrative distance  
*Explanation: Format is [AD/Metric]. The 5 is the administrative distance (higher than default 1, indicating floating static route). The 0 is the metric.*

**Question 5: B** - `ipv6 route ::/0 2001:db8:feed::1`  
*Explanation: IPv6 default route uses ::/0 (all zeros). The compressed format :: represents all zeros. Command syntax is "ipv6 route" not "ipv6 default-route".*

**Question 6: C** - Specify both exit interface and next-hop IP address  
*Explanation: Fully specified static routes (both exit interface and next-hop) are best practice. They work with all network types and eliminate recursive lookups. Point-to-point links could use exit-interface only, but fully specified is always safer.*

**Question 7: C** - `ip route 0.0.0.0 0.0.0.0 203.0.113.1 120`  
*Explanation: Floating static must have AD higher than primary. OSPF AD = 110, so backup must be > 110. AD 120 ensures it only activates when OSPF fails. AD 1 would be primary, 110 would tie, 90 would override OSPF.*

**Question 8: C** - Host route  
*Explanation: 255.255.255.255 (/32) mask indicates a host route - a route to a single specific IP address, not a network.*

**Question 9: B** - Sent via 10.0.0.2 only  
*Explanation: First route has default AD=1, second has AD=5. Lower AD wins, so all traffic uses 10.0.0.2. The second route (via 10.0.0.3) is floating/backup and only used if first fails.*

**Question 10: B** - Link-local addresses require the exit interface to be specified  
*Explanation: IPv6 link-local addresses (fe80::/10) are only unique on a specific link. Must specify exit interface: `ipv6 route 2001:db8:1::/64 GigabitEthernet0/0 fe80::1`*

**Question 11: B** - `ip route 192.168.16.0 255.255.252.0 10.0.0.2`  
*Explanation: Networks 192.168.16-19.0 can be summarized as 192.168.16.0/22 (255.255.252.0). This covers .16, .17, .18, .19 exactly. /20 would be too broad, /24 too specific.*

**Question 12: A** - IPv4 static routes always require a subnet mask  
*Explanation: IPv4 static routes must specify network-address AND subnet-mask. Exit interface is optional (can use next-hop IP). AD can be manually configured. Next-hop can be on same or different subnet.*

**Question 13: B** - `show ip route static`  
*Explanation: Command "show ip route static" filters routing table to display only static routes (marked with S). "show ip route" shows all routes.*

**Question 14: B** - Eliminates recursive route lookups  
*Explanation: Fully specified routes (exit-intf + next-hop) don't require recursive lookup to find exit interface. Router knows exactly where to forward. This improves performance and works with all network types.*

**Question 15: C** - `ip route 172.16.0.0 255.255.0.0 GigabitEthernet0/0 10.1.1.254`  
*Explanation: Fully specified static route includes: destination network (172.16.0.0), subnet mask (255.255.0.0), exit interface (Gi0/0), and next-hop IP (10.1.1.254). This is best practice configuration.*

---

## SCORING GUIDE

- **13-15 correct**: Expert! Ready for CCNA static routing questions  
- **10-12 correct**: Very good! Review missed concepts  
- **7-9 correct**: Good foundation. Study AD and route types  
- **4-6 correct**: Basic understanding. Review configuration syntax  
- **0-3 correct**: Need significant study of static routing  

---

## KEY TAKEAWAYS FOR EXAM SUCCESS

1. **Default static route = 0.0.0.0/0** (IPv4) or **::/0** (IPv6)
2. **Floating static = Higher AD** than primary route
3. **Default static AD = 1** (lower than dynamic protocols)
4. **Host route = /32 mask** (IPv4) or **/128** (IPv6)
5. **Fully specified = exit-intf + next-hop** (best practice)
6. **IPv6 link-local next-hops MUST include exit interface**
7. **Summary routes reduce routing table size**
8. **Lower AD = preferred route** (0=connected, 1=static)
9. **Routing table format: [AD/Metric]**
10. **Verification command: show ip route static**

---

## MOST COMMON EXAM SCENARIOS

### Scenario Type 1: Identify Route Type
Given a command, determine if it's standard, default, floating, summary, or host route.

### Scenario Type 2: Administrative Distance
Calculate or select correct AD for floating static routes to work as backup.

### Scenario Type 3: Command Syntax
Choose correct syntax for fully specified, directly connected, or recursive routes.

### Scenario Type 4: IPv6 Link-Local
Recognize when exit interface is required (link-local next-hops).

### Scenario Type 5: Route Selection
Multiple routes to same destination - which one is used? (Lower AD wins)

### Scenario Type 6: Summary Routes
Calculate summary route that encompasses multiple networks.

---

## CRITICAL COMMANDS TO MEMORIZE

```cisco
! IPv4 static route
ip route network mask {next-hop | exit-intf | exit-intf next-hop} [AD]

! IPv4 default route
ip route 0.0.0.0 0.0.0.0 {next-hop | exit-intf}

! IPv6 static route
ipv6 route prefix/length {next-hop | exit-intf | exit-intf next-hop} [AD]

! IPv6 default route
ipv6 route ::/0 {next-hop | exit-intf}

! Verification
show ip route static
show ipv6 route static
show ip route [network]
```

---

## EXAM TIPS

✅ **Read carefully**: Distinguish between next-hop, exit-intf, and fully specified  
✅ **Check AD values**: Lower AD = active, higher AD = backup  
✅ **Watch for /32 or /128**: These indicate host routes  
✅ **IPv6 link-local**: Always requires exit interface  
✅ **Default route keywords**: 0.0.0.0/0 or ::/0  
✅ **Summary routes**: Calculate prefix that covers all networks  
✅ **Command syntax**: "ip route" not "ip static-route"  
✅ **Verification**: Know how to read routing table output [AD/Metric]