# CCNA Port Security Quiz - 15 Questions

**Topic:** Port Security  
**Number of Questions:** 15  
**Time:** 30 minutes  

## Instructions
- Choose the **ONE** best answer for each question
- Questions reflect real CCNA exam style and difficulty
- Answers and explanations provided at the end

---

## Question 1
A network administrator configured port security on interface Fa0/5 with the default settings. After connecting a PC, the administrator notices the interface shows "secure MAC addresses: 1". Later, a user connects a small hub to the same port with two additional PCs. What will happen?

A) All three PCs will work normally  
B) The interface will go into err-disabled state  
C) Only the first PC to send traffic will work  
D) The interface will allow all devices but log violations

---

## Question 2
Examine the following configuration:
```
interface FastEthernet0/10
 switchport mode access
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation restrict
```

What will occur when a third MAC address attempts to send traffic through this interface?

A) The interface will shut down and generate a syslog message  
B) Traffic from the third MAC will be dropped and a violation counter will increment  
C) The third MAC will be learned and added to the secure MAC table  
D) All traffic on the interface will be blocked

---

## Question 3
A switch interface is in err-disabled state due to a port security violation. Which command sequence will restore the interface to operational status?

A) `clear port-security violations interface fa0/1`  
B) `shutdown` then `no shutdown` on the interface  
C) `no switchport port-security` then `switchport port-security`  
D) `errdisable recovery cause psecure-violation`

---

## Question 4
An administrator wants MAC addresses to be learned automatically on a port-security enabled interface and saved to the running configuration. Which command accomplishes this?

A) `switchport port-security mac-address dynamic`  
B) `switchport port-security mac-address sticky`  
C) `switchport port-security learn dynamic`  
D) `switchport port-security aging type sticky`

---

## Question 5
Review the following show command output:
```
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 1
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 1
Last Source Address:Vlan   : 0050.7966.6800:1
Security Violation Count   : 0
```

Based on this output, which statement is correct?

A) The interface has a manually configured MAC address  
B) The interface learned one MAC address and saved it to the configuration  
C) The interface will age out the MAC address immediately  
D) The interface is currently in violation mode

---

## Question 6
Which two prerequisites must be met before port security can be enabled on a switch interface? (Choose two)

A) The interface must be configured as an access port  
B) The interface must have an IP address assigned  
C) The interface must be administratively up  
D) The interface cannot be a member of an EtherChannel  
E) The interface must be connected to an end device

---

## Question 7
A network engineer configured the following on interface Fa0/8:
```
switchport port-security aging time 10
switchport port-security aging type inactivity
```

What is the result of this configuration?

A) MAC addresses will be removed after 10 minutes regardless of activity  
B) MAC addresses will be removed after 10 minutes of inactivity  
C) The interface will shut down after 10 minutes of inactivity  
D) Port security will be disabled after 10 minutes

---

## Question 8
An interface configured with port security is showing the following:
- Violation Mode: Protect
- Security Violation Count: 5

What can be concluded about this interface?

A) The interface has been shut down 5 times  
B) Five different MAC addresses have been blocked, but no notifications were sent  
C) Five syslog messages have been generated  
D) The interface is currently in err-disabled state

---

## Question 9
Which show command will display all secure MAC addresses learned on all interfaces of a switch?

A) `show port-security`  
B) `show mac address-table secure`  
C) `show port-security addresses`  
D) `show interfaces switchport`

---

## Question 10
A switch interface has been configured with:
```
switchport port-security maximum 3
switchport port-security mac-address 1111.2222.3333
switchport port-security mac-address sticky
```

Currently, two devices with MAC addresses 4444.5555.6666 and 7777.8888.9999 are connected and working. What will happen if a device with MAC address aaaa.bbbb.cccc connects?

A) The new device will work normally  
B) The interface will shut down  
C) The device with MAC 7777.8888.9999 will be disconnected  
D) Traffic from the new device will be dropped

---

## Question 11
To automatically recover interfaces from err-disabled state caused by port security violations every 5 minutes, which commands are required?

A) `errdisable recovery cause psecure-violation` and `errdisable recovery interval 300`  
B) `port-security recovery enable` and `port-security recovery time 300`  
C) `switchport recovery psecure-violation 300`  
D) `auto-recovery port-security 5`

---

## Question 12
A technician is troubleshooting a port security issue. The output of `show port-security interface fa0/12` shows:
```
Port Status                : Secure-shutdown
Violation Mode             : Shutdown
Security Violation Count   : 1
```

What is the most likely cause of this status?

A) The interface is administratively shut down  
B) A port security violation occurred and the interface was disabled  
C) The maximum number of MAC addresses was exceeded  
D) Both B and C are correct

---

## Question 13
Which violation mode should be configured if the administrator wants to drop unauthorized traffic while keeping the interface operational and receiving notifications about violations?

A) Shutdown  
B) Restrict  
C) Protect  
D) Monitor

---

## Question 14
An administrator notices that sticky MAC addresses configured on an interface are not appearing in the startup configuration after saving. What is the most likely cause?

A) Sticky MAC addresses cannot be saved to startup configuration  
B) The `copy running-config startup-config` command was not executed  
C) Port security is not enabled on the interface  
D) The interface is in trunk mode

---

## Question 15
In a network where users frequently move between workstations, which port security configuration would be most appropriate for user access ports?

A) Maximum 1, violation shutdown, no aging  
B) Maximum 2, violation restrict, aging time 30 minutes  
C) Maximum 5, violation protect, sticky MAC addresses  
D) Maximum 1, violation protect, no MAC address learning

---

# Answer Key and Explanations

## Answer 1: B) The interface will go into err-disabled state
**Explanation:** With default port security settings (maximum 1 MAC, violation shutdown), when the second PC attempts to send traffic through the hub, it will trigger a violation and the interface will be placed in err-disabled state.

## Answer 2: B) Traffic from the third MAC will be dropped and a violation counter will increment
**Explanation:** In restrict mode, unauthorized traffic is dropped, the port remains up, a syslog message is generated, and the violation counter increments. The interface does not shut down.

## Answer 3: B) `shutdown` then `no shutdown` on the interface
**Explanation:** To manually recover from err-disabled state, the interface must be administratively shut down and then brought back up. This is the immediate recovery method.

## Answer 4: B) `switchport port-security mac-address sticky`
**Explanation:** Sticky MAC addresses are learned dynamically when devices connect but are automatically saved to the running configuration, making them persistent.

## Answer 5: B) The interface learned one MAC address and saved it to the configuration
**Explanation:** The output shows "Sticky MAC Addresses: 1" and "Configured MAC Addresses: 0", indicating one MAC was learned via sticky learning and saved to config.

## Answer 6: A and D) The interface must be configured as an access port AND cannot be a member of an EtherChannel
**Explanation:** Port security only works on access ports and cannot be configured on EtherChannel member interfaces.

## Answer 7: B) MAC addresses will be removed after 10 minutes of inactivity
**Explanation:** With "inactivity" aging type, the timer resets each time traffic is seen from the MAC address. The address is only removed after the specified time of no activity.

## Answer 8: B) Five different MAC addresses have been blocked, but no notifications were sent
**Explanation:** In protect mode, unauthorized traffic is silently dropped without generating syslog messages or SNMP notifications, but the violation counter still increments.

## Answer 9: B) `show mac address-table secure`
**Explanation:** This command displays all secure MAC addresses (static, dynamic, and sticky) learned through port security across all interfaces.

## Answer 10: B) The interface will shut down
**Explanation:** The interface allows maximum 3 MACs: one static (manually configured), and two sticky addresses are already learned. A fourth MAC address would cause a violation with the default shutdown mode.

## Answer 11: A) `errdisable recovery cause psecure-violation` and `errdisable recovery interval 300`
**Explanation:** These commands enable automatic recovery from port security violations with a 300-second (5-minute) interval.

## Answer 12: D) Both B and C are correct
**Explanation:** "Secure-shutdown" status indicates the interface was disabled due to a port security violation, and "Security Violation Count: 1" confirms a violation occurred, likely from exceeding the maximum MAC addresses.

## Answer 13: B) Restrict
**Explanation:** Restrict mode drops unauthorized traffic, keeps the interface up, and generates notifications (syslog/SNMP messages). This provides security while maintaining connectivity and visibility.

## Answer 14: B) The `copy running-config startup-config` command was not executed
**Explanation:** Sticky MAC addresses appear in the running configuration but must be manually saved to startup configuration to persist across reboots.

## Answer 15: B) Maximum 2, violation restrict, aging time 30 minutes
**Explanation:** This allows for a phone and PC connection, uses restrict mode to maintain connectivity while providing security, and ages out MACs to accommodate user movement between workstations.