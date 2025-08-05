# 📝 DHCP Practice Quiz (CCNA 200-301)

This file contains CCNA-style questions on **DHCP**, with answers and explanations.

---

## **Question 1**
A PC connects to a LAN and uses DHCP to lease an IP address for the first time. Of the usual four DHCP messages that flow between the PC and the DHCP server, which ones do the client send? (Choose two answers.)

- a) Acknowledgment
- b) Discover
- c) Offer
- d) Request

✅ **Correct Answers:** b, d  
**Explanation:** Clients initiate the process by sending **Discover** and later **Request** messages. Servers reply with **Offer** and **Acknowledgment** messages.

---

## **Question 2**
Which of the following kinds of information are part of a DHCP server configuration? (Choose two answers.)

- a) Ranges of IP addresses in subnets that the server should lease
- b) Ranges of IP addresses to not lease per subnet
- c) DNS server hostnames
- d) The default router IP and MAC address in each subnet

✅ **Correct Answers:** a, b  
**Explanation:** DHCP servers are configured with **IP pools (ranges)** and **excluded addresses**. They store DNS server **IP addresses** (not hostnames) and default gateways by IP only.

---

## **Question 3**
Which answers list a criterion for choosing which router interfaces need to be configured as a DHCP relay agent? (Choose two answers.)

- a) If the subnet off the interface does not include a DHCP server
- b) If the subnet off the interface does include a DHCP server
- c) If the subnet off the interface contains DHCP clients
- d) If the router interface already has an ip address dhcp command

✅ **Correct Answers:** a, c  
**Explanation:** A DHCP relay is needed where clients exist but **no local DHCP server** is available.

---

## **Question 4**
A router connects to an ISP using its G0/0/0 interface, with the `ip address dhcp` command configured. What does the router do with the DHCP-learned default gateway information?

- a) Ignores the value learned from DHCP
- b) Uses the default gateway like a host, ignoring its routing table
- c) Uses the setting only for self-generated traffic
- d) Adds a default route to its IP routing table

✅ **Correct Answer:** d  
**Explanation:** The router installs a **default route (0.0.0.0/0)** pointing to the learned default gateway.

---

## **Question 5**
In the following excerpt from a command on a Mac, which parts of the output represent information learned from a DHCP server? (Choose two answers.)

```
Macprompt$ ifconfig en0
En1: flags=8863<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 1500
ether 00:6d:e7:b1:9a:11
inet 172.16.4.2 netmask 0xffffff00 broadcast 172.16.4.255
```
- a) 00:6d:e7:b1:9a:11
- b) 172.16.4.2
- c) 0xffffff00
- d) 172.16.4.255

✅ **Correct Answers:** b, d  
**Explanation:** DHCP assigns **IP addresses** and **broadcast information**. The MAC address is fixed hardware information.

---

## **Question 6**
Which of the following commands on a Windows OS should list both the IP address and DNS servers as learned with DHCP?

- a) ifconfig
- b) ipconfig
- c) ifconfig /all
- d) ipconfig /all

✅ **Correct Answer:** d  
**Explanation:** `ipconfig /all` displays all DHCP-learned configuration including DNS servers.

---

## **Question 7**
Which DHCP message is broadcast by the client to request specific offered IP parameters from a DHCP server?

- a) DHCPDISCOVER
- b) DHCPOFFER
- c) DHCPREQUEST
- d) DHCPACK

✅ **Correct Answer:** c  
**Explanation:** The **DHCPREQUEST** message is sent by the client to accept the offer.

---

## **Question 8**
What command displays all currently assigned IP addresses to clients by the router acting as a DHCP server?

- a) show ip dhcp lease
- b) show ip dhcp pool
- c) show ip dhcp binding
- d) show dhcp clients

✅ **Correct Answer:** c  
**Explanation:** `show ip dhcp binding` lists all active DHCP leases.

---

## **Question 9**
What is the purpose of the command `ip dhcp excluded-address 192.168.1.1 192.168.1.20`?

- a) Blocks specific clients from accessing DHCP
- b) Reserves these addresses for static assignment
- c) Forces clients to avoid this subnet
- d) Deletes IP addresses from DHCP pool

✅ **Correct Answer:** b  
**Explanation:** Excluded addresses are **reserved for manual/static configuration** and not offered by DHCP.

---

## **Question 10**
Which DHCP feature ensures that a specific client always receives the same IP address?

- a) Lease time
- b) Excluded address
- c) Reservation
- d) Relay agent

✅ **Correct Answer:** c  
**Explanation:** DHCP **reservations** bind a client’s MAC address to a fixed IP.

---

*(More questions can be added in future updates)*
