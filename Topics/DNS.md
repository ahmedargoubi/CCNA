# 🌐 DNS (Domain Name System) - CCNA Notes

## 1️⃣ Purpose of DNS
- Translates **domain names ↔ IP addresses**.
- Allows users to access network resources using names instead of numeric IPs.
- Works at the **Application Layer (OSI Layer 7)**.

---

## 2️⃣ Protocols and Ports Used
- **Transport Protocol:** Primarily **UDP** for queries (fast, lightweight).  
- **TCP** is used for:
  - Zone transfers (server-to-server updates).  
  - Large DNS responses exceeding UDP limit (512 bytes).  
- **Port Number:** **53** for both UDP and TCP.

---

## 3️⃣ Key DNS Components
- **DNS Client (Resolver):** Sends queries to a DNS server.
- **DNS Server:** Provides IP resolution answers.
- **Authoritative Server:** Holds original records for a domain.
- **Recursive Resolver:** Queries other DNS servers on behalf of client.
- **DNS Cache:** Stores recently resolved names for quick reuse.

---

## 4️⃣ DNS Record Types (CCNA Exam Focus)

| Record | Purpose                                                |
|--------|--------------------------------------------------------|
| A      | Hostname → IPv4 address                               |
| AAAA   | Hostname → IPv6 address                               |
| CNAME  | Alias for another hostname                            |
| MX     | Defines mail server for the domain                    |
| PTR    | Reverse lookup (IP → Hostname)                        |
| NS     | Identifies authoritative DNS servers for the domain   |
| SOA    | Start of Authority, defines zone info and admin email |

---

## 5️⃣ DNS Resolution Process
1. **Client Request:** User enters domain (e.g., `ping www.cisco.com`).  
2. **Local Cache Check:** Resolver checks local cache first.  
3. **Query Sent:** To configured DNS server (UDP port 53).  
4. **Server Behavior:**  
   - If cached: immediate reply.  
   - If not cached: recursive queries to other DNS servers (root → TLD → authoritative).  
5. **Response:** IP address is sent back to client.

---

## 6️⃣ Basic DNS Commands on Cisco Devices

```bash
Router(config)# ip domain-lookup           # Enable DNS resolution
Router(config)# ip name-server 8.8.8.8     # Specify DNS server IP
Router(config)# ip domain-name lab.local   # Define default domain name
Router# ping www.cisco.com                 # Test DNS resolution
```

**Verification:**
```bash
Router# show running-config
Router# ping <hostname>
```

---

## 7️⃣ Troubleshooting DNS
- Ensure **DNS server IP is configured correctly**.
- Verify **connectivity** to DNS server using `ping <DNS-IP>`.
- Use **IP address directly** to confirm whether DNS or connectivity is failing.
- Check **firewall or ACLs** that may block UDP/TCP port 53.

---

## 📝 Related Quiz
- [DNS Quiz](./quiz/DNS_Quiz.md)

---

## ✅ CCNA Key Takeaways
- DNS runs on **UDP/TCP port 53** (UDP for queries, TCP for zone transfers).
- Application layer protocol converting names ↔ IPs.
- Memorize common **record types** (A, AAAA, CNAME, MX, PTR, NS, SOA).
- Know **Cisco CLI commands** for DNS configuration and troubleshooting.

---
