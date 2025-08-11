# CCNA REST APIs Quiz

## Instructions
- Choose the **best** answer for each question
- Each question has only **one correct answer**
- Mark your answers and check against the answer key at the bottom
- Time limit: 12 minutes (similar to CCNA pace)

---

## Question 1
Which HTTP method should be used to retrieve device information from a REST API without modifying any data?

**A)** POST  
**B)** PUT  
**C)** GET  
**D)** DELETE  

---

## Question 2
In SDN architecture, what is the primary difference between northbound and southbound APIs?

**A)** Northbound APIs are faster than southbound APIs  
**B)** Northbound APIs connect applications to the controller, southbound APIs connect the controller to network devices  
**C)** Northbound APIs use JSON, southbound APIs use XML  
**D)** There is no functional difference between them  

---

## Question 3
What HTTP status code indicates that a new resource has been successfully created through a REST API?

**A)** 200 OK  
**B)** 201 Created  
**C)** 204 No Content  
**D)** 404 Not Found  

---

## Question 4
Which data format is most commonly used in modern REST APIs?

**A)** XML  
**B)** HTML  
**C)** JSON  
**D)** CSV  

---

## Question 5
A network engineer receives an HTTP 401 status code when making a REST API call. What does this indicate?

**A)** The request was successful  
**B)** The resource was not found  
**C)** Authentication is required  
**D)** The server has an internal error  

---

## Question 6
Which Cisco technology provides a centralized REST API for network management and automation?

**A)** IOS-XE RESTCONF  
**B)** DNA Center  
**C)** Meraki Dashboard  
**D)** ASA REST API  

---

## Question 7
In the following URL structure, what does `/devices/R1/interfaces` represent in REST API terms?

**A)** An HTTP method  
**B)** A hierarchical resource path  
**C)** An authentication token  
**D)** A status code  

---

## Question 8
Which HTTP method is considered idempotent and is used to completely update or replace a resource?

**A)** GET  
**B)** POST  
**C)** PUT  
**D)** PATCH  

---

## Question 9
What is the primary characteristic that makes REST APIs "stateless"?

**A)** They don't use HTTP protocols  
**B)** Each request contains all information needed to process it  
**C)** They only work with static data  
**D)** They cannot handle multiple requests  

---

## Question 10
In an SDN environment, which component would typically use a southbound API to configure network switches?

**A)** End-user applications  
**B)** Network monitoring tools  
**C)** SDN controller  
**D)** Web browsers  

---

## CCNA Exam Tips
- Focus on the **direction** of API communication (northbound vs southbound)
- Remember **HTTP methods** and their purposes
- Know common **status codes** and their meanings
- Understand **REST characteristics** (stateless, resource-based)

---

## Answer Key

### Question 1: **C) GET**
**Explanation:** The GET method is specifically designed to retrieve data without making any modifications to the server state. It's safe and idempotent, making it perfect for reading device information.

### Question 2: **B) Northbound APIs connect applications to the controller, southbound APIs connect the controller to network devices**
**Explanation:** This is a fundamental SDN concept. Northbound APIs allow applications to request network services from the controller, while southbound APIs allow the controller to configure and manage the underlying network infrastructure.

### Question 3: **B) 201 Created**
**Explanation:** HTTP status code 201 specifically indicates that a request has been fulfilled and has resulted in one or more new resources being created. Status 200 indicates general success but not necessarily creation.

### Question 4: **C) JSON**
**Explanation:** JSON (JavaScript Object Notation) has become the de facto standard for REST APIs due to its lightweight nature, human readability, and excellent support across programming languages.

### Question 5: **C) Authentication is required**
**Explanation:** HTTP 401 Unauthorized indicates that the request lacks valid authentication credentials. The client must authenticate itself to get the requested response.

### Question 6: **B) DNA Center**
**Explanation:** Cisco DNA Center provides a centralized platform with REST APIs for managing and automating network operations across the entire network infrastructure.

### Question 7: **B) A hierarchical resource path**
**Explanation:** This URL structure represents a hierarchical path to a specific resource - the interfaces of device R1. REST APIs use this hierarchical approach to organize and access resources.

### Question 8: **C) PUT**
**Explanation:** PUT is idempotent, meaning multiple identical requests will have the same effect. It's used to create or completely replace a resource at a specific URL.

### Question 9: **B) Each request contains all information needed to process it**
**Explanation:** Statelessness in REST means that each request must contain all the information necessary to understand and process it. The server doesn't store any client context between requests.

### Question 10: **C) SDN controller**
**Explanation:** The SDN controller sits between applications (northbound) and network devices (southbound). It uses southbound APIs to configure and control network switches and routers.

---

## Scoring Guide
- **9-10 correct:** Excellent! Ready for CCNA REST API topics
- **7-8 correct:** Good understanding, review missed concepts  
- **5-6 correct:** Need more study on API fundamentals
- **Below 5:** Review the REST API guide thoroughly before retaking

---

## Key Concepts to Remember
✅ **HTTP Methods:** GET (read), POST (create), PUT (update), DELETE (remove)  
✅ **API Direction:** Northbound (apps→controller), Southbound (controller→devices)  
✅ **Status Codes:** 200 (success), 201 (created), 401 (unauthorized), 404 (not found)  
✅ **REST Characteristics:** Stateless, resource-based, uses HTTP methods  
✅ **Data Format:** JSON is most common  
✅ **Security:** Always use HTTPS, authenticate with API keys/tokens  

---

## Common Exam Patterns
- **Scenario questions** about choosing the right HTTP method
- **SDN architecture** questions about northbound vs southbound
- **Status code recognition** in troubleshooting scenarios
- **Cisco technology identification** for specific use cases
- **REST principles** and characteristics

---

*Focus on understanding the concepts rather than memorizing - CCNA questions often test your ability to apply knowledge in real scenarios!*