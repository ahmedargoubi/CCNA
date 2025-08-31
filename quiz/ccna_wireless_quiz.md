# CCNA Wireless Quiz - Exam-Style Questions

## Question 1
A network administrator needs to deploy wireless coverage in a large office building with multiple floors. The design requires seamless roaming between access points. Which service set type should be implemented?

A) IBSS  
B) BSS  
C) ESS  
D) MBSS

## Question 2
What is the primary reason why 802.11 wireless networks use CSMA/CA instead of CSMA/CD?

A) Wireless networks are half-duplex  
B) Collision detection is impossible while transmitting in RF  
C) CSMA/CA provides better security  
D) The hidden node problem prevents collision detection

## Question 3
A company has deployed autonomous access points throughout their campus. Users complain about poor performance when moving between floors. What is the most likely cause of this issue?

A) Insufficient power to the access points  
B) Different SSIDs configured on each access point  
C) Improper channel planning causing interference  
D) Faulty wireless network interface cards

## Question 4
In a lightweight access point deployment, which protocol is used for communication between the AP and WLC, and what are the default port numbers?

A) LWAPP - Control: UDP 12222, Data: UDP 12223  
B) CAPWAP - Control: UDP 5246, Data: UDP 5247  
C) SNMP - Control: UDP 161, Data: UDP 162  
D) HTTP - Control: TCP 80, Data: TCP 443

## Question 5
Which three channels should be used for 2.4 GHz deployment to avoid interference in a high-density environment?

A) 1, 5, 9  
B) 1, 6, 11  
C) 2, 7, 12  
D) 3, 8, 13

## Question 6
A wireless client is in the "Authenticated but not Associated" state. What can this client do?

A) Send and receive data frames  
B) Participate in the BSS but cannot send data  
C) Cannot send data frames or participate in the BSS  
D) Can only send broadcast frames

## Question 7
In an ESS deployment, what identifier must remain the same across all access points to enable proper roaming?

A) BSSID  
B) SSID  
C) Channel number  
D) Encryption key

## Question 8
Which access point architecture requires trunk links to switches and handles VLANs locally?

A) Lightweight access points  
B) Cloud-based access points  
C) Autonomous access points  
D) FlexConnect access points

## Question 9
What is the maximum number of access points typically supported by a Mobility Express deployment?

A) 50 APs  
B) 100 APs  
C) 200 APs  
D) 500 APs

## Question 10
Which RF propagation issue occurs when wireless signals bounce off metal surfaces like elevators?

A) Absorption  
B) Reflection  
C) Refraction  
D) Scattering

## Question 11
In a lightweight AP deployment, what type of switch port connection is required?

A) Trunk port to carry multiple VLANs  
B) Access port because traffic is tunneled to WLC  
C) Routed port for direct Layer 3 connectivity  
D) EtherChannel for redundancy

## Question 12
Which 802.11 standard was the first to support both 2.4 GHz and 5 GHz frequencies simultaneously?

A) 802.11g  
B) 802.11a  
C) 802.11n  
D) 802.11ac

## Question 13
In a CAPWAP deployment, what happens to the data tunnel traffic by default?

A) It is always encrypted with DTLS  
B) It is not encrypted by default  
C) It uses the same encryption as the control tunnel  
D) It is compressed but not encrypted

## Question 14
What is the recommended BSA (Basic Service Area) overlap percentage between adjacent access points in an ESS?

A) 5-10%  
B) 10-15%  
C) 20-25%  
D) 30-35%

## Question 15
Which WLC deployment model would be most appropriate for a branch office with 150 access points?

A) Unified WLC  
B) Embedded WLC  
C) Cloud-based WLC  
D) Mobility Express

## Question 16
An access point is configured in Monitor mode. What is its primary function?

A) Provide wireless connectivity to clients  
B) Act as a wireless bridge between sites  
C) Monitor for rogue devices and security threats  
D) Capture packets for spectrum analysis

## Question 17
What uniquely identifies each access point in a wireless network?

A) SSID  
B) BSSID  
C) ESS-ID  
D) WLAN-ID

## Question 18
Which access point mode allows local switching when the WLC is unreachable?

A) Local mode  
B) FlexConnect mode  
C) Bridge mode  
D) Monitor mode

## Question 19
In an IBSS (Independent Basic Service Set), what is true about the network topology?

A) Requires a wireless LAN controller  
B) Uses an access point as a central coordinator  
C) Operates in ad-hoc peer-to-peer mode  
D) Supports unlimited number of clients

## Question 20
Which frequency band offers the most non-overlapping channels but has shorter range and less wall penetration?

A) 2.4 GHz  
B) 5 GHz  
C) 6 GHz  
D) Both 5 GHz and 6 GHz

---

## Answer Key

1. **C) ESS** - Extended Service Set allows multiple APs with the same SSID for seamless roaming
2. **B) Collision detection is impossible while transmitting in RF** - Cannot listen while transmitting
3. **B) Different SSIDs configured on each access point** - Prevents seamless roaming
4. **B) CAPWAP - Control: UDP 5246, Data: UDP 5247** - Standard CAPWAP port numbers
5. **B) 1, 6, 11** - Only non-overlapping channels in 2.4 GHz band
6. **C) Cannot send data frames or participate in the BSS** - Must be both authenticated AND associated
7. **B) SSID** - Same SSID required for roaming; BSSID is unique per AP
8. **C) Autonomous access points** - Handle VLANs locally, require trunk links
9. **B) 100 APs** - Mobility Express capacity limit
10. **B) Reflection** - Signals bounce off metal surfaces
11. **B) Access port because traffic is tunneled to WLC** - No need for trunk, CAPWAP tunneling used
12. **C) 802.11n** - First standard to support dual-band operation
13. **B) It is not encrypted by default** - Control tunnel encrypted, data tunnel not by default
14. **B) 10-15%** - Recommended overlap for proper roaming
15. **B) Embedded WLC** - Appropriate for ~200 APs (150 fits this model)
16. **C) Monitor for rogue devices and security threats** - Dedicated security monitoring
17. **B) BSSID** - Unique identifier (AP's radio MAC address)
18. **B) FlexConnect mode** - Local switching capability when WLC unreachable
19. **C) Operates in ad-hoc peer-to-peer mode** - No AP required, direct device communication
20. **B) 5 GHz** - More channels but shorter range than 2.4 GHz