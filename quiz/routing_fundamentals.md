# CCNA Routing Fundamentals - 15 Question Practice Quiz

## Instructions
These questions are based on the most commonly repeated routing fundamentals concepts in CCNA exams. Choose the best answer for each question.

---

## Question 1
What is the administrative distance of a static route?

A) 0  
B) 1  
C) 90  
D) 110  

---

## Question 2
A network administrator configures interface Fa0/0 on router R1 with the command `ip address 172.16.1.254 255.255.255.0`. However, when issuing `show ip route`, the routing table does not show the directly connected network. What is the most likely cause?

A) The subnet mask is incorrect for the IPv4 address  
B) No packets with destination network 172.16.1.0 have been sent to R1  
C) The interface Fa0/0 has not been activated  
D) The configuration needs to be saved first  

---

## Question 3
Which route will be selected by the router when these routes exist for the same destination network 192.168.1.0/24?

A) OSPF route with AD 110, metric 65  
B) EIGRP route with AD 90, metric 2816  
C) Static route with AD 1, metric 0  
D) RIP route with AD 120, metric 2  

---

## Question 4
What does the following routing table entry represent?
```
C    192.168.10.0/24 is directly connected, FastEthernet0/0
```

A) A static route configured by the administrator  
B) A network directly connected to the router interface  
C) A route learned through OSPF protocol  
D) A default route entry  

---

## Question 5
What is the purpose of a default route?

A) To provide a backup path when the primary route fails  
B) To route packets when no specific route exists for the destination  
C) To summarize multiple network routes into one entry  
D) To provide load balancing across multiple paths  

---

## Question 6
A router receives a packet destined for 192.168.50.100. The routing table contains these entries:
```
192.168.0.0/16 via 10.0.0.1
192.168.50.0/24 via 10.0.0.2  
0.0.0.0/0 via 10.0.0.3
```
Which route will the router use?

A) 192.168.0.0/16 via 10.0.0.1  
B) 192.168.50.0/24 via 10.0.0.2  
C) 0.0.0.0/0 via 10.0.0.3  
D) The packet will be dropped  

---

## Question 7
What command is used to configure a static route in Cisco IOS?

A) `route add 192.168.1.0 255.255.255.0 10.0.0.1`  
B) `ip route 192.168.1.0 255.255.255.0 10.0.0.1`  
C) `static route 192.168.1.0/24 next-hop 10.0.0.1`  
D) `add route 192.168.1.0 mask 255.255.255.0 gateway 10.0.0.1`  

---

## Question 8
In the routing table entry below, what does the number 110 represent?
```
O    192.168.10.0/24 [110/65] via 10.0.0.2, 00:05:30, Serial0/0/0
```

A) The metric of the route  
B) The administrative distance  
C) The OSPF process ID  
D) The interface cost  

---

## Question 9
Which routing method requires NO protocol overhead and provides the most secure routing?

A) OSPF (Open Shortest Path First)  
B) EIGRP (Enhanced Interior Gateway Routing Protocol)  
C) Static routing  
D) RIP (Routing Information Protocol)  

---

## Question 10
A router has multiple routes to network 10.1.1.0/24 from different routing protocols. Which route will be installed in the routing table?

A) EIGRP route (AD=90, Metric=2816)  
B) OSPF route (AD=110, Metric=20)  
C) RIP route (AD=120, Metric=3)  
D) External EIGRP route (AD=170, Metric=2048)  

---

## Question 11
What is the network address and broadcast address for the IP address 172.16.10.50/28?

A) Network: 172.16.10.48, Broadcast: 172.16.10.63  
B) Network: 172.16.10.32, Broadcast: 172.16.10.47  
C) Network: 172.16.10.0, Broadcast: 172.16.10.255  
D) Network: 172.16.10.16, Broadcast: 172.16.10.31  

---

## Question 12
Which statement about floating static routes is correct?

A) They have an administrative distance of 0  
B) They have an administrative distance greater than dynamic routing protocols  
C) They are always preferred over dynamic routes  
D) They cannot be used as backup routes  

---

## Question 13
A router needs to forward a packet to network 192.168.20.0/24. The routing table shows:
```
S    192.168.20.0/24 [1/0] via 10.1.1.2
S    192.168.20.0/24 [1/0] via 10.1.1.3
```
What will happen to the traffic?

A) All traffic will use the first route (10.1.1.2)  
B) All traffic will use the second route (10.1.1.3)  
C) Traffic will be load-balanced between both routes  
D) The router will drop the packets due to route conflict  

---

## Question 14
What does the asterisk (*) symbol typically represent in a routing table?

A) The route is directly connected  
B) The route is a static route  
C) The route is a candidate default route  
D) The route is learned via OSPF  

---

## Question 15
A network administrator wants to configure a default route that will only be used when all dynamic routes fail. What should be configured?

A) `ip route 0.0.0.0 0.0.0.0 10.0.0.1`  
B) `ip route 0.0.0.0 0.0.0.0 10.0.0.1 200`  
C) `ip route 0.0.0.0 0.0.0.0 10.0.0.1 1`  
D) `ip route 0.0.0.0 0.0.0.0 10.0.0.1 90`  

---

## Answer Key & Explanations

### Question 1: **B** - 1
Static routes have an administrative distance of 1, making them highly trusted but not as trusted as directly connected routes (AD=0).

### Question 2: **C** - The interface Fa0/0 has not been activated
A directly connected network will only appear in the routing table when the interface is both configured with an IP address AND activated with the "no shutdown" command.

### Question 3: **C** - Static route with AD 1, metric 0
**Administrative Distance hierarchy**: Connected (0) > Static (1) > EIGRP (90) > OSPF (110) > RIP (120). Lower AD wins regardless of metric.

### Question 4: **B** - A network directly connected to the router interface  
The "C" code indicates a directly connected network. This route is automatically added when an interface is configured and active.

### Question 5: **B** - To route packets when no specific route exists for the destination
A default route (0.0.0.0/0) is the "route of last resort" used when no more specific route matches the destination.

### Question 6: **B** - 192.168.50.0/24 via 10.0.0.2
**Longest prefix match rule**: /24 is more specific than /16 or /0, so the 192.168.50.0/24 route is selected.

### Question 7: **B** - `ip route 192.168.1.0 255.255.255.0 10.0.0.1`
This is the correct Cisco IOS syntax for configuring a static route with destination network, subnet mask, and next-hop.

### Question 8: **B** - The administrative distance
In the format [AD/Metric], 110 is the administrative distance (OSPF default), and 65 is the metric.

### Question 9: **C** - Static routing
Static routing requires no routing protocol traffic, making it the most secure option with zero protocol overhead.

### Question 10: **A** - EIGRP route (AD=90, Metric=2816)
EIGRP has the lowest administrative distance (90) among the dynamic routing protocols listed, so it will be preferred.

### Question 11: **A** - Network: 172.16.10.48, Broadcast: 172.16.10.63
**/28 = 4 host bits, group size = 16**
- 50 ÷ 16 = 3 remainder 2
- Network: 3 × 16 = 48
- Broadcast: 48 + 16 - 1 = 63

### Question 12: **B** - They have an administrative distance greater than dynamic routing protocols
A floating static route has an administrative distance greater than the administrative distance of another static route or dynamic route, making it a backup route.

### Question 13: **C** - Traffic will be load-balanced between both routes
When multiple routes have the same administrative distance and metric, Cisco routers perform equal-cost load balancing.

### Question 14: **C** - The route is a candidate default route
The asterisk (*) typically marks routes that are candidates for the default route or gateway of last resort.

### Question 15: **B** - `ip route 0.0.0.0 0.0.0.0 10.0.0.1 200`
A floating static route with high AD (200) ensures it's only used when dynamic routes (AD 90-120) are unavailable.

---

## Scoring Guide
- **14-15 correct**: Excellent understanding of routing fundamentals
- **12-13 correct**: Good knowledge, review administrative distance concepts
- **10-11 correct**: Solid foundation, focus on routing table interpretation
- **8-9 correct**: Basic understanding, study route selection process
- **Below 8**: Review all routing fundamental concepts thoroughly

## Key Concepts Tested
1. **Administrative Distance** - Route trustworthiness and selection
2. **Routing Table Interpretation** - Understanding entries and codes
3. **Static vs Dynamic Routing** - Methods and characteristics
4. **Route Selection Process** - Longest prefix match and AD priority  
5. **Default Routes** - Configuration and purpose
6. **Interface Configuration** - Connected routes and activation
7. **Floating Static Routes** - Backup route configuration
8. **Load Balancing** - Equal-cost multipath
9. **Route Types** - Connected, static, and dynamic
10. **Troubleshooting** - Common routing issues

## Most Frequently Tested Areas
- **Administrative Distance values** and hierarchy
- **Routing table entry interpretation**
- **Static route configuration syntax**
- **Longest prefix match rule**
- **Interface activation for connected routes**
- **Default route configuration and purpose**
- **Floating static routes as backup paths**

These question types appear repeatedly on CCNA exams because they test fundamental routing concepts that every network engineer must understand.
