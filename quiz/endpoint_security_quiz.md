# CCNA Endpoint Security Quiz

## 10 Exam-Style Questions

---

### Question 1
Which security device would be most appropriate to prevent unauthorized users and non-compliant devices from accessing the corporate network?

**A)** VPN-enabled router  
**B)** Next-Generation Firewall (NGFW)  
**C)** Network Access Control (NAC)  
**D)** Web Security Appliance (WSA)

---

### Question 2
A network administrator needs to secure remote workers connecting to the corporate network over the internet. Which technology should be implemented on the edge router?

**A)** NAC  
**B)** VPN  
**C)** ESA  
**D)** NGFW

---

### Question 3
What is the primary difference between a traditional firewall and a Next-Generation Firewall (NGFW)?

**A)** NGFW can filter traffic based on IP addresses and port numbers  
**B)** NGFW includes application awareness and deep packet inspection  
**C)** NGFW uses stateful packet inspection  
**D)** NGFW operates at Layer 3 of the OSI model

---

### Question 4
Which type of attack involves an attacker intercepting communication between two parties without their knowledge?

**A)** Phishing  
**B)** DoS (Denial of Service)  
**C)** Man-in-the-Middle (MITM)  
**D)** Zero-day exploit

---

### Question 5
An organization wants to prevent employees from accessing social media websites during work hours and block downloads of malicious files from the internet. Which device should be deployed?

**A)** Email Security Appliance (ESA)  
**B)** Web Security Appliance (WSA)  
**C)** Network Access Control (NAC)  
**D)** VPN-enabled router

---

### Question 6
What feature does an Email Security Appliance (ESA) provide to protect against phishing attacks?

**A)** URL filtering and website categorization  
**B)** Detection of fake emails and credential theft attempts  
**C)** Encryption of VPN tunnels  
**D)** Device compliance checking

---

### Question 7
Which of the following is considered an endpoint device that requires security protection?

**A)** Layer 3 switch  
**B)** Firewall  
**C)** Laptop computer  
**D)** Router

---

### Question 8
A company implements a security strategy where multiple layers of security controls are used, so if one layer fails, other layers continue to provide protection. What is this approach called?

**A)** Single point of failure  
**B)** Defense-in-depth  
**C)** Zero trust architecture  
**D)** Perimeter security

---

### Question 9
What capability does HTTPS inspection on a Web Security Appliance (WSA) provide?

**A)** Blocks all encrypted traffic  
**B)** Speeds up web browsing performance  
**C)** Decrypts, inspects, and re-encrypts SSL/TLS traffic  
**D)** Prevents users from accessing HTTPS websites

---

### Question 10
Which Cisco security device checks whether an endpoint has the required antivirus software and latest patches before allowing it to connect to the network?

**A)** Cisco Firepower (NGFW)  
**B)** Cisco ISE (NAC)  
**C)** Cisco ESA  
**D)** Cisco AMP

---

## Answer Key

### Question 1: **C** - Network Access Control (NAC)
**Explanation**: NAC is specifically designed to authenticate users and devices, check device compliance (antivirus, patches, OS version), and grant or deny network access based on those checks. It can quarantine non-compliant devices.

---

### Question 2: **B** - VPN
**Explanation**: A VPN (Virtual Private Network) creates encrypted tunnels over the internet, allowing remote workers to securely access the corporate network. VPN-enabled routers provide this functionality at the network edge.

---

### Question 3: **B** - NGFW includes application awareness and deep packet inspection
**Explanation**: While traditional firewalls filter based on IP/port/protocol (Layer 3-4) and use stateful inspection, NGFWs add Layer 7 application awareness, deep packet inspection, IPS, user identity tracking, and advanced threat detection capabilities.

---

### Question 4: **C** - Man-in-the-Middle (MITM)
**Explanation**: A Man-in-the-Middle attack occurs when an attacker secretly intercepts and possibly alters communication between two parties who believe they are directly communicating with each other. VPNs and encryption help prevent MITM attacks.

---

### Question 5: **B** - Web Security Appliance (WSA)
**Explanation**: WSA provides URL filtering (blocking social media by category), time-based access controls, malware scanning of downloads, and application visibility and control. It's designed specifically to secure and control web traffic.

---

### Question 6: **B** - Detection of fake emails and credential theft attempts
**Explanation**: ESA includes phishing protection that detects fake emails attempting to steal credentials, blocks them, and can rewrite URLs to protect users. It also scans attachments and uses advanced threat analysis.

---

### Question 7: **C** - Laptop computer
**Explanation**: Endpoints are devices that connect to the network and are used by end users, including desktops, laptops, smartphones, tablets, and servers. Network infrastructure devices (switches, routers, firewalls) are not considered endpoints.

---

### Question 8: **B** - Defense-in-depth
**Explanation**: Defense-in-depth is a security strategy that uses multiple layers of security controls throughout an IT system. If one layer fails, other layers provide backup protection. This includes perimeter security, network security, endpoint security, and application security.

---

### Question 9: **C** - Decrypts, inspects, and re-encrypts SSL/TLS traffic
**Explanation**: HTTPS inspection (also called SSL/TLS inspection) allows the WSA to decrypt encrypted web traffic, inspect it for threats and policy violations, then re-encrypt it before forwarding. This prevents attackers from hiding malware in encrypted traffic.

---

### Question 10: **B** - Cisco ISE (NAC)
**Explanation**: Cisco ISE (Identity Services Engine) is Cisco's NAC solution that performs posture assessment, checking whether endpoints meet security requirements (antivirus installed, patches current, OS version approved) before granting network access. Non-compliant devices can be quarantined or given limited access.

---

## Score Interpretation

- **9-10 correct**: Excellent! You're ready for this section
- **7-8 correct**: Good understanding, review missed topics
- **5-6 correct**: Need more study on endpoint security concepts
- **Below 5**: Review the guide thoroughly and retake quiz

---

## Key Concepts to Review

If you missed questions, focus on:

**Questions 1, 2, 10**: Understand which device does what (NAC vs VPN vs NGFW)  
**Question 3**: Know NGFW capabilities beyond traditional firewalls  
**Questions 5, 6**: Difference between WSA (web) and ESA (email)  
**Question 7**: Definition of endpoint devices  
**Question 8**: Defense-in-depth strategy  
**Question 9**: HTTPS inspection process

---

*These question types frequently appear on CCNA exams - practice until you can answer them quickly!*