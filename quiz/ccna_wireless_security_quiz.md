# CCNA Wireless Security Quiz - 12 Essential Questions

*These questions follow the exact CCNA exam format and cover the most frequently tested wireless security concepts.*

---

## Question 1
**A network administrator is configuring wireless security for a small office network. All employees will share the same password to connect to the wireless network. Which authentication mode should be configured?**

A. Enterprise mode with RADIUS  
B. Personal mode with PSK  
C. Open authentication with MAC filtering  
D. WEP shared key authentication  

---

## Question 2
**Which EAP authentication method provides the highest level of security for wireless networks?**

A. LEAP  
B. EAP-FAST  
C. PEAP  
D. EAP-TLS  

---

## Question 3
**In an 802.1X wireless implementation, what role does the wireless access point serve?**

A. Supplicant  
B. Authenticator  
C. Authentication Server  
D. RADIUS Client  

---

## Question 4
**Which encryption protocol is used by WPA2 to provide data confidentiality and integrity?**

A. TKIP  
B. CCMP  
C. GCMP  
D. WEP  

---

## Question 5
**A company wants to implement wireless security that requires each user to have individual login credentials and uses a central authentication server. Which solution should be implemented?**

A. WPA2-Personal with PSK  
B. WPA2-Enterprise with 802.1X  
C. Open authentication with web portal  
D. WEP with shared key  

---

## Question 6
**Which wireless security protocol was designed as a temporary solution to address WEP vulnerabilities while maintaining compatibility with existing hardware?**

A. CCMP  
B. GCMP  
C. TKIP  
D. AES  

---

## Question 7
**What is the primary security improvement that WPA3 provides over WPA2 in Personal mode?**

A. Stronger encryption algorithms  
B. SAE (Simultaneous Authentication of Equals)  
C. Support for 802.1X authentication  
D. Larger encryption key sizes  

---

## Question 8
**Which component in an 802.1X implementation is responsible for making the final authentication decision?**

A. Supplicant  
B. Authenticator  
C. Authentication Server (AS)  
D. Access Point  

---

## Question 9
**A network administrator discovers that WEP is being used on the corporate wireless network. What should be the immediate recommendation?**

A. Increase the WEP key length to 128 bits  
B. Enable MAC address filtering as additional security  
C. Upgrade to WPA2 or WPA3 immediately  
D. Configure static IP addresses for all clients  

---

## Question 10
**Which EAP method requires a digital certificate only on the authentication server, while the client is authenticated using credentials within a secure tunnel?**

A. EAP-TLS  
B. PEAP  
C. LEAP  
D. EAP-FAST  

---

## Question 11
**What security feature introduced in WPA3 helps protect 802.11 management frames from tampering and eavesdropping?**

A. GCMP encryption  
B. SAE authentication  
C. PMF (Protected Management Frames)  
D. Forward secrecy  

---

## Question 12
**In a wireless network using WPA2-Enterprise, where are user credentials typically stored and validated?**

A. On each wireless client device  
B. On the wireless access point  
C. On a RADIUS authentication server  
D. In the wireless controller's local database  

---

## ANSWER KEY

**Question 1: B** - Personal mode with PSK  
*Personal mode is designed for small networks where all users share the same password.*

**Question 2: D** - EAP-TLS  
*EAP-TLS requires certificates on both server and clients, providing the highest security.*

**Question 3: B** - Authenticator  
*The access point acts as the authenticator, controlling network access based on authentication results.*

**Question 4: B** - CCMP  
*CCMP (Counter/CBC-MAC Protocol) is the encryption standard used in WPA2.*

**Question 5: B** - WPA2-Enterprise with 802.1X  
*Enterprise mode provides individual user authentication with centralized credential management.*

**Question 6: C** - TKIP  
*TKIP was created to improve WEP security while remaining compatible with existing WEP hardware.*

**Question 7: B** - SAE (Simultaneous Authentication of Equals)  
*SAE replaces PSK in WPA3-Personal and protects against offline dictionary attacks.*

**Question 8: C** - Authentication Server (AS)  
*The Authentication Server (typically RADIUS) validates credentials and makes access decisions.*

**Question 9: C** - Upgrade to WPA2 or WPA3 immediately  
*WEP is completely broken and can be cracked in minutes. Immediate replacement is critical.*

**Question 10: B** - PEAP  
*PEAP uses server certificates for tunnel establishment, then authenticates clients within the secure tunnel.*

**Question 11: C** - PMF (Protected Management Frames)  
*PMF protects management frames from tampering and prevents deauthentication attacks.*

**Question 12: C** - On a RADIUS authentication server  
*WPA2-Enterprise uses 802.1X with RADIUS servers for centralized credential storage and validation.*

---

## Study Tips for CCNA Wireless Security

### High-Frequency Topics:
1. **WPA Evolution**: Know TKIP→CCMP→GCMP progression
2. **Authentication Modes**: Personal vs Enterprise scenarios  
3. **802.1X Components**: Supplicant, Authenticator, Authentication Server
4. **EAP Methods**: Focus on EAP-TLS and PEAP differences
5. **Security Upgrades**: Why WEP must be replaced immediately

### Memory Tricks:
- **Personal = PSK** (Pre-Shared Key)
- **Enterprise = Individual credentials**  
- **EAP-TLS = Certificates everywhere**
- **PEAP = Server certificate only**
- **WPA2 = CCMP, WPA3 = GCMP**