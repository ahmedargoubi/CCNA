# Software Defined Networking (SDN) - CCNA Study Notes

## 1. What is SDN?
- **Software Defined Networking** centralizes the **control plane** into an application called a **controller**.
- In **traditional networking**, the control plane is **distributed** — each device runs its own routing and control logic.
- In **SDN**, the controller calculates routes and policies, then programs the network devices.

---

## 2. Key Components
- **Controller**: Centralized brain of the network.
- **Southbound Interface (SBI)**:  
  - Communication from **controller → network devices**.  
  - Examples: NETCONF, RESTCONF, OpenFlow, traditional SSH/SNMP.
- **Northbound Interface (NBI)**:  
  - Communication from **apps/scripts → controller**.  
  - Example: REST API to Cisco DNA Center.

---

## 3. Cisco SDN Solutions
- **SD-Access** → Automates **campus LANs**.
- **ACI (Application Centric Infrastructure)** → Automates **data centers**.
- **SD-WAN** → Automates **WANs**.

### Cisco DNA Center
- Controller for **SD-Access**.
- Can also manage **traditional networks**.
- Supports **Intent-Based Networking (IBN)**:  
  - You describe **what** you want, DNA Center figures out **how** to configure devices.

---

## 4. Underlay, Overlay, and Fabric

| Term      | Description                                                                 |
|-----------|-----------------------------------------------------------------------------|
| Underlay  | Physical network (switches, routers, wireless APs) providing IP connectivity.|
| Overlay   | Virtual network built over the underlay (VXLAN tunnels, logical segmentation).|
| Fabric    | Combination of **underlay + overlay** (full SDN topology).                  |

---

## 5. SD-Access Roles
- **Edge Node** → Connects end hosts.
- **Border Node** → Connects to networks outside SD-Access (WAN, internet).
- **Control Node** → Runs **LISP** for endpoint-to-location mapping.

---

## 6. SD-Access Control & Data Plane
- **Control Plane**: LISP (maps EIDs to RLOCs).  
  - EID = Endpoint Identifier (end host).  
  - RLOC = Routing Locator (switch location).
- **Data Plane**: VXLAN tunnels for forwarding.
- **Policy Plane**: Cisco TrustSec (CTS) for QoS/security.

---

## 7. Deployment Types
- **Brownfield** → Add SD-Access to an existing network (hardware/software must be compatible).
- **Greenfield** → Build new network with optimal SD-Access underlay (Layer 3 everywhere, routed links, IS-IS protocol).

---

## 8. DNA Center vs Traditional Management

| Feature                   | Traditional Network Mgmt                     | DNA Center SDN Mgmt                        |
|---------------------------|-----------------------------------------------|---------------------------------------------|
| Config method              | Device-by-device via CLI                     | Centralized GUI/API                         |
| Policy mgmt                | Per-device ACLs                              | Intent-based, automatic config              |
| Deployment speed           | Slow (manual)                                | Fast (auto-provisioning)                    |
| Error risk                 | Higher (manual entry)                        | Lower (automation)                          |
| Software updates           | Manual per device                            | Centralized and automated                   |

---

## 9. Exam Tips
- **Northbound API** = Apps/scripts → Controller.  
- **Southbound API** = Controller → Devices.
- DNA Center = Controller in SD-Access.
- Underlay supports VXLAN overlay.
- VXLAN = Data plane, LISP = Control plane, CTS = Policy plane.
- Greenfield = New design with Layer 3 everywhere, IS-IS routing.
