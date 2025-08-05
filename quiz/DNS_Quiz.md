# 📝 DNS Practice Quiz (CCNA 200-301)

This file contains CCNA-style questions on **DNS**, with answers and explanations.

---

## **Question 1**
What is the primary purpose of the Domain Name System (DNS)?

- a) Convert IP addresses to MAC addresses
- b) Convert MAC addresses to IP addresses
- c) Convert domain names to IP addresses
- d) Convert hostnames to MAC addresses

✅ **Correct Answer:** c  
**Explanation:** DNS resolves human-readable domain names into IP addresses so devices can communicate.

---

## **Question 2**
Which of the following record types maps a hostname to an IPv4 address?

- a) A
- b) AAAA
- c) CNAME
- d) MX

✅ **Correct Answer:** a  
**Explanation:** An **A record** is used for IPv4 name-to-address mapping.

---

## **Question 3**
Which command on a Cisco router enables the device to perform DNS lookups?

- a) ip dns-enable
- b) ip domain-lookup
- c) dns lookup enable
- d) ip name-server

✅ **Correct Answer:** b  
**Explanation:** The command **ip domain-lookup** allows the router to resolve hostnames to IP addresses.

---

## **Question 4**
Which record type provides the mail server information for a domain?

- a) MX
- b) NS
- c) PTR
- d) CNAME

✅ **Correct Answer:** a  
**Explanation:** **MX (Mail Exchange)** records define mail servers for email routing.

---

## **Question 5**
Which of the following could prevent a client from resolving domain names? (Choose two answers.)

- a) Wrong DNS server IP configured
- b) Network interface shutdown
- c) Missing default gateway
- d) Duplicate MAC address on the network

✅ **Correct Answers:** a, b  
**Explanation:** If DNS server info is wrong or the network interface is down, DNS queries cannot reach the server.

---

## **Question 6**
Which record type is used for reverse DNS lookups (IP → Name)?

- a) A
- b) PTR
- c) CNAME
- d) MX

✅ **Correct Answer:** b  
**Explanation:** **PTR records** map an IP address back to a hostname.

---

## **Question 7**
You configured a Cisco router with `ip name-server 8.8.8.8` but `ping www.cisco.com` fails. What is the next step in troubleshooting?

- a) Disable ip domain-lookup
- b) Check connectivity to 8.8.8.8
- c) Add a PTR record
- d) Configure MX records

✅ **Correct Answer:** b  
**Explanation:** Ensure the router can reach the DNS server before assuming DNS configuration is wrong.

---

## **Question 8**
Which OSI layer is DNS categorized under?

- a) Network
- b) Transport
- c) Application
- d) Data Link

✅ **Correct Answer:** c  
**Explanation:** DNS is an **Application Layer protocol** (Layer 7).

---

## **Question 9**
Which command specifies the IP address of a DNS server on a Cisco router?

- a) ip name-server
- b) ip dns-server
- c) ip dns-config
- d) name-server set

✅ **Correct Answer:** a  
**Explanation:** Use **ip name-server <IP>** to tell the router which DNS server to query.

---

## **Question 10**
Which DNS record is an alias that points to another domain name?

- a) CNAME
- b) PTR
- c) MX
- d) NS

✅ **Correct Answer:** a  
**Explanation:** **CNAME (Canonical Name)** records map one name to another domain name (aliasing).

---
