# CCNA Security Fundamentals Quiz

## Instructions
- Choose the best answer for each question
- Answers and explanations are provided at the end
- This quiz covers frequently tested CCNA Security topics

---

## Question 1
Which three elements make up the CIA Triad in network security?

A) Confidentiality, Integrity, Authentication  
B) Confidentiality, Integrity, Availability  
C) Classification, Integrity, Availability  
D) Confidentiality, Identity, Authorization

---

## Question 2
What is the primary difference between a virus and a worm?

A) Viruses spread automatically, worms require user action  
B) Worms spread automatically, viruses require a host program  
C) Viruses only affect Windows systems, worms affect all systems  
D) There is no difference between viruses and worms

---

## Question 3
Which attack type involves an attacker intercepting communications between two legitimate parties?

A) DoS attack  
B) Spoofing attack  
C) Man-in-the-Middle attack  
D) Social engineering attack

---

## Question 4
What does AAA stand for in network security?

A) Authentication, Authorization, Accounting  
B) Access, Authorization, Auditing  
C) Authentication, Access, Accounting  
D) Availability, Authorization, Auditing

---

## Question 5
Which protocol is used by RADIUS for authentication?

A) TCP port 49  
B) UDP port 1812  
C) TCP port 1812  
D) UDP port 49

---

## Question 6
What type of attack floods a DHCP server with requests using fake MAC addresses?

A) ARP spoofing  
B) DHCP exhaustion  
C) DNS amplification  
D) TCP SYN flood

---

## Question 7
Which type of phishing attack specifically targets high-level executives?

A) Spear phishing  
B) Vishing  
C) Whaling  
D) Smishing

---

## Question 8
What is the minimum recommended length for a strong password?

A) 6 characters  
B) 8 characters  
C) 10 characters  
D) 12 characters

---

## Question 9
In a TCP SYN flood attack, what does the attacker do?

A) Sends SYN packets but never sends ACK packets  
B) Sends only ACK packets without SYN packets  
C) Completes the three-way handshake multiple times  
D) Sends RST packets to terminate connections

---

## Question 10
Which of the following is NOT a factor in multi-factor authentication?

A) Something you know  
B) Something you have  
C) Something you are  
D) Something you want

---

## Question 11
What is the primary purpose of a digital certificate?

A) To encrypt all network traffic  
B) To prove the identity of websites or users  
C) To block malicious websites  
D) To create secure passwords

---

## Question 12
Which attack involves compromising a trusted website that the target frequently visits?

A) Phishing  
B) Spear phishing  
C) Watering hole attack  
D) Tailgating

---

## Question 13
What is the main difference between RADIUS and TACACS+?

A) RADIUS uses TCP, TACACS+ uses UDP  
B) RADIUS uses UDP, TACACS+ uses TCP  
C) RADIUS is proprietary, TACACS+ is open standard  
D) There is no difference

---

## Question 14
In a DNS amplification attack, what makes it an "amplification" attack?

A) It uses multiple DNS servers  
B) Small requests generate large responses  
C) It amplifies the attacker's IP address  
D) It increases the DNS query speed

---

## Question 15
What does CSR stand for in the context of digital certificates?

A) Certificate Security Request  
B) Certificate Signing Request  
C) Certificate Service Request  
D) Certificate System Request

---

## Question 16
Which social engineering attack involves following someone into a secure area without authorization?

A) Phishing  
B) Vishing  
C) Tailgating  
D) Baiting

---

## Question 17
What type of attack uses a botnet to overwhelm a target system?

A) DoS  
B) DDoS  
C) Spoofing  
D) MITM

---

## Question 18
Which reconnaissance tool is commonly used to find IP addresses associated with domain names?

A) ping  
B) traceroute  
C) nslookup  
D) netstat

---

## Question 19
What is ARP spoofing also known as?

A) ARP flooding  
B) ARP poisoning  
C) ARP reflection  
D) ARP amplification

---

## Question 20
Which Cisco solution provides comprehensive AAA services?

A) Cisco ASA  
B) Cisco ISE  
C) Cisco WLC  
D) Cisco Prime

---

# Answer Key and Explanations

## Answer 1: B) Confidentiality, Integrity, Availability
**Explanation:** The CIA Triad is the foundation of information security, consisting of Confidentiality (only authorized access), Integrity (data remains unmodified), and Availability (reliable access for authorized users).

## Answer 2: B) Worms spread automatically, viruses require a host program
**Explanation:** Viruses need a host program to spread and require user action (like sharing files), while worms are standalone programs that spread automatically across networks.

## Answer 3: C) Man-in-the-Middle attack
**Explanation:** MITM attacks involve an attacker positioning themselves between two communicating parties to intercept and potentially modify communications.

## Answer 4: A) Authentication, Authorization, Accounting
**Explanation:** AAA framework consists of Authentication (verifying identity), Authorization (granting permissions), and Accounting (logging actions).

## Answer 5: B) UDP port 1812
**Explanation:** RADIUS uses UDP port 1812 for authentication and UDP port 1813 for accounting.

## Answer 6: B) DHCP exhaustion
**Explanation:** DHCP exhaustion attacks flood DHCP servers with requests using fake MAC addresses to exhaust the available IP address pool.

## Answer 7: C) Whaling
**Explanation:** Whaling specifically targets high-value individuals like executives or celebrities, while spear phishing targets specific companies or groups.

## Answer 8: B) 8 characters
**Explanation:** The minimum recommended password length is 8 characters, though longer passwords are increasingly recommended.

## Answer 9: A) Sends SYN packets but never sends ACK packets
**Explanation:** In SYN flood attacks, attackers send many SYN packets to initiate connections but never complete the handshake, exhausting server resources.

## Answer 10: D) Something you want
**Explanation:** MFA factors are: something you know (password), something you have (token), and something you are (biometrics). "Something you want" is not a valid factor.

## Answer 11: B) To prove the identity of websites or users
**Explanation:** Digital certificates are used to verify and authenticate the identity of entities in digital communications.

## Answer 12: C) Watering hole attack
**Explanation:** Watering hole attacks compromise websites that targets frequently visit, waiting for victims to visit the infected site.

## Answer 13: B) RADIUS uses UDP, TACACS+ uses TCP
**Explanation:** RADIUS uses UDP ports 1812/1813, while TACACS+ uses TCP port 49. RADIUS is open standard, TACACS+ is Cisco proprietary.

## Answer 14: B) Small requests generate large responses
**Explanation:** Amplification attacks exploit protocols where small requests trigger disproportionately large responses, amplifying the attack traffic.

## Answer 15: B) Certificate Signing Request
**Explanation:** CSR is a Certificate Signing Request sent to a Certificate Authority to request a digital certificate.

## Answer 16: C) Tailgating
**Explanation:** Tailgating involves physically following authorized personnel into secure areas without proper authorization.

## Answer 17: B) DDoS
**Explanation:** Distributed Denial of Service (DDoS) attacks use multiple compromised systems (botnets) to overwhelm targets, unlike DoS which uses a single source.

## Answer 18: C) nslookup
**Explanation:** nslookup is a network administration command-line tool used to query DNS servers and find IP addresses associated with domain names.

## Answer 19: B) ARP poisoning
**Explanation:** ARP spoofing is also known as ARP poisoning, where attackers send false ARP messages to associate their MAC address with another device's IP.

## Answer 20: B) Cisco ISE
**Explanation:** Cisco Identity Services Engine (ISE) is Cisco's comprehensive AAA solution that provides authentication, authorization, and accounting services.