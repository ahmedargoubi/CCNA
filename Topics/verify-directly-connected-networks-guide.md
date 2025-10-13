# 🧠 Cisco Networking Academy – Verify Directly Connected Networks (1.5)

## 🎯 Learning Objectives
By the end of this module, you should be able to:
- Use **show** commands to verify interface configuration and status.
- Check **IPv4** and **IPv6** addresses.
- Verify and interpret **routing tables**.
- Filter and navigate command outputs efficiently.
- Use **command history** for faster troubleshooting.
- Practice verification in **Packet Tracer**.

---

## 1️⃣ 1.5.1 Interface Verification Commands

Use `show` commands to check connected interfaces and network status.

```bash
Router# show ip interface brief
Router# show interfaces
Router# show ipv6 interface brief
```

**Key Outputs:**
- **Status**: Indicates if the interface is administratively up/down.
- **Protocol**: Shows whether Layer 2 (data link) is operational.
- **IP Address**: Displays configured IPv4 or IPv6 addresses.

---

## 2️⃣ 1.5.2 Verify Interface Status

Check physical and data link status:
```bash
Router# show interfaces GigabitEthernet0/0
```

Look for:
- `line protocol is up` → Interface is active.
- `administratively down` → Interface was manually disabled.
- `input/output errors` → Possible cabling or hardware issues.

---

## 3️⃣ 1.5.3 Verify IPv6 Link Local and Multicast Addresses

Each IPv6-enabled interface has:
- A **Link-Local Address (FE80::/10)**
- **Multicast Addresses** for neighbor discovery.

Commands:
```bash
Router# show ipv6 interface GigabitEthernet0/0
Router# show ipv6 neighbors
Router# show ipv6 route
```

---

## 4️⃣ 1.5.4 Verify Interface Configuration

To view detailed configuration:
```bash
Router# show running-config interface GigabitEthernet0/0
Router# show ip interface GigabitEthernet0/0
```

Check for:
- Assigned IP address
- Description field
- Duplex and speed settings
- Access control lists (ACLs), if any

---

## 5️⃣ 1.5.5 Verify Routes

Display current routing information:
```bash
Router# show ip route
Router# show ipv6 route
```

Legend:
- **C** = Connected directly to the router
- **L** = Local address (interface IP)
- **S** = Static route
- **D** = Learned via EIGRP
- **O** = Learned via OSPF

---

## 6️⃣ 1.5.6 Filter Show Command Output

Use filters to find specific information:
```bash
Router# show running-config | section interface
Router# show ip interface brief | include up
Router# show interfaces | begin GigabitEthernet0/0
Router# show ip route | exclude static
```

Filtering options:
- `| include` – show matching lines
- `| exclude` – hide matching lines
- `| begin` – start output from a matching line

---

## 7️⃣ 1.5.7 Syntax Checker – Filter Show Command Output

Practice commands that use filters. Example:
```bash
Router# show ip route | include 10.10.10
```
✅ Confirms ability to isolate routes quickly in long outputs.

---

## 8️⃣ 1.5.8 Command History Feature

Use command recall to save time:
- Press **↑** / **↓** arrows to scroll previous commands.
- Configure history buffer size:
  ```bash
  Router(config)# terminal history size 20
  ```
- View command history:
  ```bash
  Router# show history
  ```

---

## 9️⃣ 1.5.9 Syntax Checker – Command History Features

Try recalling and reusing past commands using arrow keys or `Ctrl+P / Ctrl+N`.

---

## 🔟 1.5.10 Packet Tracer – Verify Directly Connected Networks

### Task:
- Open the provided Packet Tracer file.
- Configure IP addresses on router interfaces.
- Use the following verification commands:
  ```bash
  show ip interface brief
  show ip route
  ping <neighbor IP>
  ```
- Confirm both routers recognize their directly connected networks.

---

## 1️⃣1️⃣ 1.5.11 Check Your Understanding

✅ Review Questions:
1. What is the purpose of the `show ip interface brief` command?  
2. Which command displays directly connected routes?  
3. How do you display only interfaces that are up?  
4. What is the difference between `| include` and `| exclude` filters?

---

## 🧾 Summary

| Concept | Command | Purpose |
|----------|----------|----------|
| Interface summary | `show ip interface brief` | View IPs and status |
| Interface details | `show interfaces` | Check errors and traffic |
| IPv6 info | `show ipv6 interface brief` | View IPv6 addresses |
| Routing table | `show ip route` | See connected and learned routes |
| Filtering | `| include / exclude / begin` | Manage large outputs |
| History | `show history` | Recall previous commands |

---

**End of Guide**
