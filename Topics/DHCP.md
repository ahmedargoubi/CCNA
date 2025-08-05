# 🖧 DHCP (Dynamic Host Configuration Protocol)

## 1️⃣ What is DHCP?

- DHCP automatically assigns **IP addresses**, **subnet masks**, **default gateways**, and **DNS server info** to hosts on a network.
- It eliminates the need for manual IP configuration.

---

## 2️⃣ DHCP Operation (DORA Process)

1. **D – Discover** → Client broadcasts to find a DHCP server.
2. **O – Offer** → Server responds with available IP configuration.
3. **R – Request** → Client requests offered IP.
4. **A – Acknowledge** → Server confirms the lease.

---

## 3️⃣ DHCP Terminology

- **Scope/Pool:** Range of IP addresses available for clients.
- **Lease:** The time a client can use an assigned IP before renewal.
- **Exclusion:** IPs that will not be handed out (e.g., for static devices).
- **Reservation:** A specific IP is always given to a specific MAC address.

---

## 4️⃣ Configuring DHCP on a Cisco Router

### Step 1: Exclude addresses
```bash
Router(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10
```

### Step 2: Create DHCP pool
```bash
Router(config)# ip dhcp pool OFFICE
Router(dhcp-config)# network 192.168.1.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.1.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# domain-name company.local
Router(dhcp-config)# lease 7
```

---

## 5️⃣ Verify DHCP

```bash
Router# show ip dhcp pool
Router# show ip dhcp binding
Router# show running-config
```

---

## 6️⃣ DHCP Relay (IP Helper Address)

Used when clients and DHCP server are in **different subnets**.

```bash
Router(config)# interface g0/1
Router(config-if)# ip helper-address 10.1.1.10
```
> This forwards DHCP broadcasts to a DHCP server in another network.

---

## 7️⃣ Troubleshooting DHCP

- **Check interfaces:** Must be `up` and have `ip address` configured.
- **Check pool availability:** `show ip dhcp pool`.
- **Verify helper address** on relay devices.
- **Use `debug ip dhcp server events`** for real-time DHCP messages.

---

## 8️⃣ Key Commands (Quick Reference)

| Command                                | Purpose                                   |
|---------------------------------------|-------------------------------------------|
| `ip dhcp excluded-address A B`        | Reserve addresses manually                |
| `ip dhcp pool NAME`                   | Create DHCP pool                         |
| `network A.B.C.D MASK`                | Define the range of IPs                   |
| `default-router A.B.C.D`              | Define default gateway for clients        |
| `dns-server A.B.C.D`                  | Set DNS server for clients                |
| `lease DAYS`                          | Define lease duration                     |
| `ip helper-address A.B.C.D`           | Forward DHCP broadcasts to another server |
| `show ip dhcp binding`                | Display IPs leased to clients             |
| `debug ip dhcp server events`         | Troubleshoot DHCP process                 |

---

✅ **Tip for CCNA Exam:**  
- Understand the **DORA process** very well.  
- Know all essential commands and `ip helper-address` usage.  
- Be ready for troubleshooting scenarios in simulations.


## 📝 Related Quiz

- [DHCP Quiz](CCNA/quiz/DHCP_Quiz.md)
