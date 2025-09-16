# CCNA CDP and LLDP Quiz
## 12 Most Frequently Asked Exam Questions

---

### Question 1
**Which statement about Cisco Discovery Protocol (CDP) configuration on a Cisco switch is TRUE?**

A) CDP is disabled by default and can be enabled globally with the command `cdp run`
B) CDP is enabled by default and can be disabled globally with the command `no cdp run`
C) CDP is enabled by default and can be disabled per interface with the command `no cdp run`
D) CDP cannot be disabled on Cisco switches for security reasons

---

### Question 2
**What is the default CDP timer and holdtime values?**

A) Timer: 30 seconds, Holdtime: 90 seconds
B) Timer: 60 seconds, Holdtime: 180 seconds
C) Timer: 120 seconds, Holdtime: 360 seconds
D) Timer: 90 seconds, Holdtime: 270 seconds

---

### Question 3
**A network administrator wants to implement a discovery protocol in a multi-vendor environment. Which protocol should be used and what commands are needed?**

A) CDP only - `cdp run`
B) LLDP only - `lldp run` globally and `lldp transmit` and `lldp receive` on interfaces
C) Both CDP and LLDP - `cdp run` and `lldp enable`
D) LLDP only - `lldp enable` globally

---

### Question 4
**Which multicast MAC address does CDP use to send its advertisements?**

A) 01-00-5e-00-00-01
B) 01-80-c2-00-00-0e
C) 01-00-0c-cc-cc-cc
D) 33-33-00-00-00-01

---

### Question 5
**What IEEE standard defines LLDP?**

A) IEEE 802.1D
B) IEEE 802.1AB
C) IEEE 802.1Q
D) IEEE 802.3ad

---

### Question 6
**Which command would you use to view detailed information about CDP neighbors including IP addresses and IOS versions?**

A) `show cdp neighbors`
B) `show cdp neighbors detail`
C) `show cdp entry *`
D) Both B and C are correct

---

### Question 7
**In a security-conscious environment, what is the BEST practice regarding discovery protocols?**

A) Enable both CDP and LLDP for redundancy
B) Disable CDP globally and enable LLDP only where needed
C) Disable both protocols and use manual documentation
D) Keep CDP enabled but disable it on internet-facing interfaces

---

### Question 8
**Which of the following information is NOT typically shared by CDP?**

A) Device hostname and domain name
B) IOS version and platform type
C) Interface IP addresses
D) Routing table entries

---

### Question 9
**A Cisco switch shows a neighbor device in CDP but not in LLDP output. What is the most likely reason?**

A) The neighbor device only supports CDP (Cisco proprietary)
B) LLDP holdtime has expired
C) There is a configuration error
D) CDP has higher priority than LLDP

---

### Question 10
**What happens when you disable CDP on a specific interface using `no cdp enable`?**

A) CDP is disabled globally on the device
B) CDP is disabled only on that specific interface
C) LLDP is automatically enabled on that interface
D) The interface stops functioning

---

### Question 11
**Which command enables LLDP to both transmit and receive on all interfaces?**

A) `lldp run`
B) `lldp enable`
C) `lldp transmit` and `lldp receive`
D) `lldp global enable`

---

### Question 12
**When viewing CDP neighbors, you see "Capability: R S I". What does each letter represent?**

A) R=Router, S=Switch, I=IGMP
B) R=Router, S=Switch, I=IP Phone
C) R=Repeater, S=Source Bridge, I=IGMP
D) R=Router, S=Source Bridge, I=IGMP

---

## Answer Key

**1. B)** CDP is enabled by default and can be disabled globally with the command `no cdp run`

**2. B)** Timer: 60 seconds, Holdtime: 180 seconds

**3. B)** LLDP only - `lldp run` globally and `lldp transmit` and `lldp receive` on interfaces

**4. C)** 01-00-0c-cc-cc-cc

**5. B)** IEEE 802.1AB

**6. D)** Both `show cdp neighbors detail` and `show cdp entry *` provide detailed neighbor information

**7. B)** Disable CDP globally and enable LLDP only where needed - CDP should be disabled in security-conscious environments as it's proprietary and reveals network topology

**8. D)** Routing table entries - CDP shares device information, not routing information

**9. A)** The neighbor device only supports CDP (Cisco proprietary)

**10. B)** CDP is disabled only on that specific interface - `no cdp enable` works per interface

**11. A)** `lldp run` - this is the global command to enable LLDP

**12. D)** R=Router, S=Source Bridge, I=IGMP - These are the standard CDP capability codes

## Key Points for CCNA Exam:

- **CDP is Cisco proprietary and enabled by default**
- **LLDP is IEEE 802.1AB standard and disabled by default on Cisco devices**
- **Both protocols can coexist**
- **CDP uses multicast MAC 01-00-0c-cc-cc-cc**
- **Default CDP timer: 60s, holdtime: 180s**
- **Security best practice: disable CDP, use LLDP selectively**
- **Commands: `cdp run/no cdp run` (global), `cdp enable/no cdp enable` (interface)**
- **Commands: `lldp run`, `lldp transmit/receive` (interface level)**
