# 📝 FTP & TFTP - CCNA Notes

## 1️⃣ Introduction

FTP (**File Transfer Protocol**) and TFTP (**Trivial File Transfer Protocol**) are two widely used protocols for transferring files over a network.  
In Cisco environments, they are often used to:
- **Backup and restore** device configuration files.
- **Upgrade IOS images** on routers and switches.

Both protocols operate at the **Application Layer (Layer 7)** of the OSI model.

---

## 2️⃣ FTP (File Transfer Protocol)

### a) Overview
- **Reliable file transfer protocol** used to upload and download files between a client and server.
- Uses **TCP (Transmission Control Protocol)** for reliable communication.
- Provides **authentication (username/password)** for secure access.
- Can operate in **active** or **passive** modes.

---

### b) Transport Protocol and Ports
- **TCP port 21:** Control connection (commands, login, session management).
- **TCP port 20:** Data transfer (in active mode).
- In **passive mode**, the data channel is opened dynamically on random TCP ports.

---

### c) FTP Modes
- **Active Mode:** Server actively opens data connection from port 20 to client.
- **Passive Mode:** Client initiates both control and data connections (used when firewall/NAT is in place).

---

### d) FTP Features
- Supports **binary** (image, IOS files) and **ASCII** file transfers.
- Can list directories, delete, and rename files.
- Allows both **upload (put)** and **download (get)** operations.

---

### e) FTP Use in Cisco Devices
Cisco devices can act as:
- **FTP client:** Connects to an FTP server to transfer files.
- Command example:

Router# copy running-config ftp:

Router# copy flash: ftp:


You will be prompted for:
- FTP server IP
- Destination filename
- Username/password

---

## 3️⃣ TFTP (Trivial File Transfer Protocol)

### a) Overview
- A **simpler, lightweight protocol** for transferring files.
- **No authentication** (open access).
- Uses **UDP (User Datagram Protocol)**, making it **faster but less reliable** (no built-in error recovery like TCP).
- Often used for **network booting** and **Cisco IOS upgrades** in LAN environments.

---

### b) Transport Protocol and Port
- **UDP port 69** for initial connection.
- Data transfer uses **ephemeral (random)** UDP ports above 1023.

---

### c) Features of TFTP
- Transfers only files, no directory browsing.
- No login or encryption.
- Typically used in **trusted internal networks**, not over the Internet.
- Supports **read and write** operations but minimal error messages.

---

### d) TFTP Use in Cisco Devices
- Example commands:

Router# copy running-config tftp:

Router# copy tftp: running-config

Router# copy flash: tftp:

Router# copy tftp: flash:

You will be prompted for:
- TFTP server IP
- Source/Destination filename

---

## 4️⃣ FTP vs TFTP – Key Differences

| Feature        | FTP                          | TFTP                        |
|----------------|------------------------------|-----------------------------|
| Protocol       | TCP (20, 21)                 | UDP (69)                    |
| Reliability    | Reliable (acknowledgments)   | Unreliable (no handshake)    |
| Authentication | Yes (username/password)      | No (open access)            |
| Security       | Higher (credentials)         | Lower                       |
| Speed          | Slightly slower              | Faster but less safe        |
| Features       | File management (list, del)  | Basic transfer only         |
| Usage          | External or internal network | Internal, local LAN use      |

---

## 5️⃣ CCNA Relevant Commands

### Viewing available file systems:

Router# show file systems


### Copying configurations:

Router# copy running-config tftp:
Router# copy startup-config ftp:
Router# copy ftp: running-config
Router# copy tftp: flash:


### Example TFTP copy:

Router# copy running-config tftp:
Address or name of remote host []? 192.168.1.100
Destination filename [running-config]? backup1
!!
[OK]


---

## 6️⃣ Troubleshooting FTP/TFTP Transfers

- **Check reachability:** `ping <server-ip>`
- **Check file permissions** on server.
- **Check correct port is open (69 for TFTP, 20/21 for FTP)**.
- **Verify that a TFTP/FTP server software** is running on the remote host.
- Use `debug tftp events` or `debug ip packet` for diagnostics.

---

## ✅ CCNA Key Takeaways

- **FTP:** TCP 20/21, requires authentication, reliable.
- **TFTP:** UDP 69, no authentication, lightweight and fast.
- Used to **transfer IOS images, backup/restore configuration files**.
- Cisco command: `copy source destination` (e.g., `copy running-config tftp:`).
- `show file systems` helps verify available storage locations.

---

## 📝 Related Quiz
- [FTP/TFTP Quiz](./quiz/FTP_TFTP_Quiz.md)

---
