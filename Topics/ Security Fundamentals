# CCNA Security Fundamentals

## 🔑 Key Security Concepts

### Why Security?
The goal of **network security** is to protect an enterprise’s data and systems.  
It is built on the **CIA Triad**:

- **Confidentiality**  
  - Only authorized users can access data.  
  - Example: Some information is public (website), others must remain private (internal records).

- **Integrity**  
  - Data should remain accurate and unmodified by unauthorized users.  
  - Example: Prevent tampering of financial records.

- **Availability**  
  - Authorized users should always have reliable access to systems and data.  
  - Example: Network must remain operational.

➡️ Attackers aim to compromise **Confidentiality, Integrity, or Availability**.

---

## ⚡ Key Terms

- **Vulnerability** → A weakness in a system (e.g., outdated software).  
- **Exploit** → A method or tool used to take advantage of a vulnerability.  
- **Threat** → The potential for a vulnerability to be exploited.  
- **Mitigation** → Steps taken to reduce or block threats (patches, firewalls, training).

⚠️ No system is perfectly secure — security is about **reducing risk**.

---

## 🚨 Common Attacks

### 1. DoS & DDoS Attacks
- **Denial of Service (DoS):** Overwhelms a system to make it unavailable.  
- **TCP SYN Flood:** Attacker spams SYN requests but never completes the handshake.  
- **Distributed DoS (DDoS):** Uses a **botnet** of infected devices to attack.

---

### 2. Spoofing Attacks
- Attacker uses a **fake source address** (IP/MAC).  
- Example: **DHCP exhaustion attack** floods DHCP requests with fake MACs → pool exhaustion.

---

### 3. Reflection & Amplification
- **Reflection:** Attacker spoofs victim’s IP so replies are sent to the victim.  
- **Amplification:** Small attacker requests trigger **large responses** (e.g., DNS amplification).

---

### 4. Man-in-the-Middle (MITM)
- Attacker intercepts communications between two parties.  
- **ARP Spoofing/Poisoning:** Tricks devices into sending traffic to attacker first.  
- Compromises **confidentiality & integrity**.

---

### 5. Reconnaissance
- Gathering information before launching attacks.  
- Examples:  
  - `nslookup` to find IPs.  
  - **WHOIS queries** for domain ownership info.  

---

### 6. Malware
- **Virus** → Requires host program, spreads via user sharing.  
- **Worm** → Standalone, spreads automatically, congests networks.  
- **Trojan Horse** → Disguised as legitimate software, spreads via downloads.  

---

### 7. Social Engineering
- Targets the **human element**.  
- Types:
  - **Phishing:** Fake emails trick users into revealing info.  
  - **Spear Phishing:** Targeted phishing (specific company/individual).  
  - **Whaling:** Targets executives or high-value individuals.  
  - **Vishing:** Phone-based phishing.  
  - **Smishing:** SMS-based phishing.  
  - **Watering Hole Attack:** Compromise a trusted site.  
  - **Tailgating:** Attacker physically follows someone into a secure area.

---

### 8. Password-Related Attacks
- **Dictionary Attack:** Tries common words.  
- **Brute Force Attack:** Tries all combinations.  
- **Guessing:** Based on personal info.  

**Strong Passwords Should:**  
✔️ Be at least 8+ characters  
✔️ Mix uppercase, lowercase, numbers, and symbols  
✔️ Change regularly  

---

## 🔐 Authentication Enhancements

### Multi-Factor Authentication (MFA)
Requires two or more factors:  
1. **Something you know** (password, PIN)  
2. **Something you have** (phone, token)  
3. **Something you are** (biometrics: fingerprint, retina, face scan)

➡️ Stronger than passwords alone.

---

### Digital Certificates
- Used to prove identity of websites/users.  
- Issued by **Certificate Authorities (CAs)** after a **CSR (Certificate Signing Request)**.  
- Example: HTTPS websites use certificates to verify authenticity.

---

## 🛡️ Controlling & Monitoring Users with AAA

**AAA = Authentication, Authorization, Accounting**

- **Authentication** → Verifies identity (logging in).  
- **Authorization** → Grants permissions (what resources can be used).  
- **Accounting** → Logs actions (who did what, when).  

### AAA Servers
- **Cisco ISE (Identity Services Engine)** provides AAA services.  
- **Protocols:**
  - **RADIUS** → Open standard, UDP 1812/1813.  
  - **TACACS+** → Cisco proprietary, TCP 49.

📌 **For CCNA: Know differences between Authentication, Authorization, Accounting.**

---

## 🏢 Security Program Elements

- **User Awareness Programs:** Teach employees about threats (phishing, tailgating).  
- **User Training Programs:** Formal education on security practices.  
- **Physical Access Control:** Restrict physical access (badges, locks, CCTV).  

---

## ✅ CCNA Exam Tips for Security
- Know the **CIA Triad** (Confidentiality, Integrity, Availability).  
- Be able to identify different **attack types** (DoS, Spoofing, MITM, Phishing, etc.).  
- Understand **AAA functions** and **protocol differences (RADIUS vs TACACS+)**.  
- Remember basics of **MFA, Digital Certificates, and strong passwords**.  
- Recognize the importance of **user awareness & training**.

---
