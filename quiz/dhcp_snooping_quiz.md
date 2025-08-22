# CCNA DHCP Snooping Quiz - 10 Questions

**Topic:** DHCP Snooping  
**Number of Questions:** 10  
**Time:** 20 minutes  

## Instructions
- Choose the **ONE** best answer for each question
- Questions reflect real CCNA exam style and difficulty
- Answers and explanations provided at the end

---

## Question 1
A network administrator wants to prevent rogue DHCP servers on the network. The legitimate DHCP server is connected to interface Gi0/1 on the access switch. Which configuration commands are required on the switch?

A) `ip dhcp snooping` and `ip dhcp snooping vlan 10`  
B) `ip dhcp snooping`, `ip dhcp snooping vlan 10`, and `ip dhcp snooping trust` on Gi0/1  
C) `ip dhcp snooping enable` and `ip dhcp snooping trusted-server 192.168.1.1`  
D) `ip dhcp snooping` and `ip dhcp snooping database dhcp-server.txt`

---

## Question 2
After enabling DHCP snooping globally and on VLAN 20, users report they cannot obtain IP addresses. The show command output reveals:

```
DHCP snooping is configured on following VLANs: 20
DHCP snooping is operational on following VLANs: 20
Interface                  Trusted    Allow option
---------                  -------    ------------
GigabitEthernet0/5         no         yes
GigabitEthernet0/10        no         yes
```

The DHCP server is connected to Gi0/5. What is the problem?

A) DHCP snooping database is not configured  
B) Interface Gi0/5 needs to be configured as trusted  
C) VLAN 20 is not active on the switch  
D) Option 82 insertion is enabled by default

---

## Question 3
Which statement about the DHCP snooping binding table is correct?

A) It stores bindings for all VLANs regardless of DHCP snooping configuration  
B) It only contains entries for DHCP clients on trusted ports  
C) It maintains IP-to-MAC bindings for clients on untrusted ports  
D) It is automatically synchronized between switches in the same VTP domain

---

## Question 4
An administrator configured the following on a switch:
```
ip dhcp snooping
ip dhcp snooping vlan 10
interface GigabitEthernet0/1
 ip dhcp snooping trust
interface GigabitEthernet0/24
 ip dhcp snooping limit rate 5
```

What will happen if a device on port Gi0/24 generates 8 DHCP packets per second?

A) The port will be placed in err-disabled state  
B) Only 5 packets per second will be processed, others will be dropped  
C) All packets will be processed but a warning will be logged  
D) The rate limiting will be ignored on access ports

---

## Question 5
A company has DHCP servers on multiple VLANs. The network administrator wants to prevent DHCP starvation attacks while allowing legitimate DHCP traffic. Examine this configuration:

```
ip dhcp snooping
ip dhcp snooping vlan 10,20,30
ip dhcp snooping information option
no ip dhcp snooping information option allow-untrusted
```

What is the result of the `no ip dhcp snooping information option allow-untrusted` command?

A) DHCP packets with Option 82 from untrusted ports will be dropped  
B) DHCP packets without Option 82 from untrusted ports will be dropped  
C) Option 82 insertion will be disabled globally  
D) Untrusted ports will not be able to send any DHCP packets

---

## Question 6
Which show command will display the DHCP snooping binding table with MAC addresses, IP addresses, and associated interfaces?

A) `show ip dhcp snooping`  
B) `show ip dhcp snooping binding`  
C) `show ip dhcp snooping database`  
D) `show ip dhcp binding`

---

## Question 7
A switch has DHCP snooping enabled on VLAN 100. A legitimate DHCP client receives an IP address of 192.168.100.50 from the DHCP server. Later, a malicious user manually configures their device with the same IP address 192.168.100.50. What will happen when the malicious device tries to send traffic?

A) The traffic will be forwarded normally since DHCP snooping only monitors DHCP packets  
B) The traffic will be dropped if Dynamic ARP Inspection is also enabled  
C) The switch will generate an alert but forward the traffic  
D) The malicious device will be automatically assigned a different IP address

---

## Question 8
Review the following DHCP snooping configuration on a switch:
```
ip dhcp snooping
ip dhcp snooping vlan 50
ip dhcp snooping database flash:dhcp-snooping.db
ip dhcp snooping database write-delay 300
```

What is the purpose of the database configuration?

A) To store DHCP server information for redundancy  
B) To log all DHCP transactions for security auditing  
C) To persist DHCP snooping bindings across switch reboots  
D) To backup DHCP reservations from the DHCP server

---

## Question 9
A network has multiple switches with DHCP snooping enabled. Switch A learns a binding (IP: 10.1.1.100, MAC: 1111.2222.3333) for a client on VLAN 10. If this client moves to Switch B on the same VLAN, what happens?

A) Switch B will automatically learn the binding from Switch A  
B) The client must request a new IP address through DHCP  
C) Switch B will reject the client until the binding ages out on Switch A  
D) Both switches will maintain separate bindings for the same MAC address

---

## Question 10
An enterprise network experiences frequent DHCP-related attacks. The security team wants to implement the most comprehensive protection. Which combination of features should be deployed?

A) DHCP Snooping only  
B) DHCP Snooping + Dynamic ARP Inspection  
C) DHCP Snooping + IP Source Guard  
D) DHCP Snooping + Dynamic ARP Inspection + IP Source Guard

---

# Answer Key and Explanations

## Answer 1: B) `ip dhcp snooping`, `ip dhcp snooping vlan 10`, and `ip dhcp snooping trust` on Gi0/1
**Explanation:** DHCP snooping requires global enablement, VLAN specification, and the interface connected to the legitimate DHCP server must be configured as trusted. Without the trust configuration, DHCP responses from the server will be blocked.

## Answer 2: B) Interface Gi0/5 needs to be configured as trusted
**Explanation:** The output shows Gi0/5 is "not trusted." Since the DHCP server is connected to this interface, it must be configured as trusted with `ip dhcp snooping trust` or DHCP responses will be blocked.

## Answer 3: C) It maintains IP-to-MAC bindings for clients on untrusted ports
**Explanation:** The DHCP snooping binding table tracks legitimate DHCP assignments (IP, MAC, VLAN, interface) for clients connected to untrusted ports. This information is used by Dynamic ARP Inspection and IP Source Guard.

## Answer 4: A) The port will be placed in err-disabled state
**Explanation:** When DHCP rate limiting is configured with `ip dhcp snooping limit rate 5` and the threshold is exceeded (8 > 5 packets/second), the interface is placed in err-disabled state as a security measure.

## Answer 5: A) DHCP packets with Option 82 from untrusted ports will be dropped
**Explanation:** By default, DHCP snooping inserts Option 82. The command `no ip dhcp snooping information option allow-untrusted` configures the switch to drop DHCP packets that already contain Option 82 from untrusted ports, preventing Option 82 spoofing.

## Answer 6: B) `show ip dhcp snooping binding`
**Explanation:** This command displays the DHCP snooping binding database showing MAC addresses, IP addresses, lease times, VLANs, and associated interfaces for all dynamically learned bindings.

## Answer 7: B) The traffic will be dropped if Dynamic ARP Inspection is also enabled
**Explanation:** DHCP snooping alone only monitors DHCP packets. However, if Dynamic ARP Inspection (DAI) is enabled, it uses the DHCP snooping binding table to validate ARP packets and would drop traffic from the unauthorized IP assignment.

## Answer 8: C) To persist DHCP snooping bindings across switch reboots
**Explanation:** The database configuration stores DHCP snooping bindings in non-volatile memory (flash) so they persist across switch reboots. The write-delay specifies how often (in seconds) the database is updated.

## Answer 9: B) The client must request a new IP address through DHCP
**Explanation:** DHCP snooping bindings are locally significant to each switch. When a client moves to a different switch, it must go through the DHCP process again to establish a new binding on the new switch.

## Answer 10: D) DHCP Snooping + Dynamic ARP Inspection + IP Source Guard
**Explanation:** For comprehensive Layer 2 security: DHCP Snooping prevents rogue DHCP servers, Dynamic ARP Inspection prevents ARP spoofing using DHCP bindings, and IP Source Guard prevents IP spoofing by validating source IP addresses against DHCP bindings.