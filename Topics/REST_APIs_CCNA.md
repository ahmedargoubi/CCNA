# REST APIs in Networking (CCNA Focus)

## 1. What is a REST API?
- **REST** = Representational State Transfer
- A **REST API** allows programs (clients) to interact with systems (servers) over HTTP.
- In networking, REST APIs let software tools configure, monitor, and control network devices.

---

## 2. REST in Network Automation
- Used by controllers, network management systems, and cloud services to **communicate with network devices**.
- Data is exchanged in formats like **JSON** or **XML**.
- REST APIs are **stateless**: each request contains all information needed, no persistent session is required.

---

## 3. Northbound and Southbound APIs

### Northbound API
- **Purpose**: Allows external applications to communicate *with the network controller*.
- **Used by**: Orchestration tools, monitoring dashboards, custom scripts.
- **Direction**: From *application layer* → *controller*.
- **Example**: A monitoring app uses a northbound REST API to get network stats from Cisco DNA Center.

### Southbound API
- **Purpose**: Allows the controller to communicate *with network devices*.
- **Used by**: Controllers to push configs or retrieve data from switches, routers, firewalls.
- **Direction**: From *controller* → *network devices*.
- **Examples**:
  - NETCONF
  - RESTCONF
  - OpenFlow

---

## 4. REST API Methods in Networking
- **GET** → Retrieve device or network information.
- **POST** → Create a new configuration or resource.
- **PUT** → Update existing configuration.
- **DELETE** → Remove configuration or resource.

---

## 5. Why REST APIs Matter in CCNA
- REST APIs are part of **network programmability** topics.
- They enable:
  - Centralized automation
  - Faster provisioning
  - Integration with DevOps tools
- **Key example**: Cisco DNA Center uses REST-based northbound APIs to interface with automation tools, and southbound APIs (like NETCONF/RESTCONF) to talk to devices.

---

## 6. Quick Comparison Table

| Feature                  | Northbound API                               | Southbound API                          |
|--------------------------|----------------------------------------------|------------------------------------------|
| Direction                | App → Controller                             | Controller → Device                      |
| Audience                 | Developers, automation scripts               | Controllers, orchestration systems       |
| Examples                 | REST API to DNA Center, OpenDaylight         | NETCONF, RESTCONF, OpenFlow              |
| Data Formats             | JSON, XML                                    | XML, JSON, Protobuf                      |

---

**Exam Tip**:  
If the question mentions **"apps talking to controllers"**, think **northbound API**.  
If it mentions **"controllers talking to devices"**, think **southbound API**.
