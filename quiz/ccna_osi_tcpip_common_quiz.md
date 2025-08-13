# CCNA OSI Model & TCP/IP Suite - Most Repeated Quiz Questions

**Instructions**: Choose the best answer for each question. These are the most commonly tested concepts on CCNA exams.

---

## Question 1
At which OSI layer is a destination IP address added to a PDU during the encapsulation process?

A) Layer 2 (Data Link)  
B) Layer 3 (Network)  
C) Layer 4 (Transport)  
D) Layer 7 (Application)  

---

## Question 2
Which correctly describes the encapsulation process as data moves down the OSI model?

A) Data → Packet → Segment → Frame → Bits  
B) Data → Segment → Packet → Frame → Bits  
C) Data → Frame → Packet → Segment → Bits  
D) Data → Bits → Frame → Packet → Segment  

---

## Question 3
What is the Protocol Data Unit (PDU) name at the Transport Layer (Layer 4)?

A) Frame  
B) Packet  
C) Segment  
D) Bits  

---

## Question 4
Which layers of the OSI model are included in the TCP/IP Application Layer?

A) Application only  
B) Application and Presentation  
C) Application, Presentation, and Session  
D) Session, Presentation, and Application  

---

## Question 5
A user is troubleshooting network connectivity issues. Using the layered troubleshooting approach starting from the lowest layer, what should be checked first?

A) IP address configuration  
B) Physical cable connections  
C) DNS resolution  
D) Application settings  

---

## Question 6
Which OSI layer is responsible for logical addressing and routing between different networks?

A) Layer 2 (Data Link)  
B) Layer 3 (Network)  
C) Layer 4 (Transport)  
D) Layer 5 (Session)  

---

## Question 7
What happens during the de-encapsulation process when data is received at the destination?

A) Headers are added at each layer  
B) Headers are removed at each layer  
C) Data is segmented into smaller pieces  
D) Error checking is disabled  

---

## Question 8
Which Transport Layer protocol provides reliable, connection-oriented communication?

A) UDP (User Datagram Protocol)  
B) TCP (Transmission Control Protocol)  
C) IP (Internet Protocol)  
D) ICMP (Internet Control Message Protocol)  

---

## Question 9
At which OSI layer do MAC addresses operate?

A) Layer 1 (Physical)  
B) Layer 2 (Data Link)  
C) Layer 3 (Network)  
D) Layer 4 (Transport)  

---

## Question 10
Which statement correctly describes the difference between the OSI model and TCP/IP model?

A) OSI has 4 layers, TCP/IP has 7 layers  
B) OSI has 7 layers, TCP/IP has 4 layers  
C) Both models have exactly 7 layers  
D) OSI is practical, TCP/IP is theoretical  

---

## Answer Key

**Question 1: B) Layer 3 (Network)**  
*Explanation: The destination IP address is added at Layer 3 (Network Layer) during encapsulation. This layer is responsible for logical addressing and routing.*

**Question 2: B) Data → Segment → Packet → Frame → Bits**  
*Explanation: As data moves down the OSI stack: Application data becomes a Segment (Layer 4), then a Packet (Layer 3), then a Frame (Layer 2), then Bits (Layer 1).*

**Question 3: C) Segment**  
*Explanation: At the Transport Layer (Layer 4), the PDU is called a Segment. Layer 3 = Packet, Layer 2 = Frame, Layer 1 = Bits.*

**Question 4: C) Application, Presentation, and Session**  
*Explanation: The TCP/IP Application Layer combines the functionality of OSI Layers 5, 6, and 7 (Session, Presentation, and Application layers).*

**Question 5: B) Physical cable connections**  
*Explanation: Using the bottom-up troubleshooting approach, you start with Layer 1 (Physical) by checking cables, connections, and physical connectivity first.*

**Question 6: B) Layer 3 (Network)**  
*Explanation: Layer 3 (Network Layer) handles logical addressing (IP addresses) and routing between different networks. Layer 2 handles physical addressing within the same network.*

**Question 7: B) Headers are removed at each layer**  
*Explanation: During de-encapsulation, each layer removes its corresponding header as data moves up the stack to reconstruct the original data.*

**Question 8: B) TCP (Transmission Control Protocol)**  
*Explanation: TCP provides reliable, connection-oriented communication with features like acknowledgments, flow control, and error recovery. UDP is connectionless and unreliable.*

**Question 9: B) Layer 2 (Data Link)**  
*Explanation: MAC addresses operate at Layer 2 (Data Link Layer) and provide physical addressing for devices on the same network segment.*

**Question 10: B) OSI has 7 layers, TCP/IP has 4 layers**  
*Explanation: The OSI model has 7 layers (theoretical framework), while the original TCP/IP model has 4 layers (practical implementation): Application, Transport, Internet, and Network Access.*

---

## Most Frequently Tested Concepts

### OSI Layer Functions (Know by Heart!)
1. **Physical (1)**: Bits, cables, electrical signals
2. **Data Link (2)**: Frames, MAC addresses, switches
3. **Network (3)**: Packets, IP addresses, routers
4. **Transport (4)**: Segments, TCP/UDP, port numbers
5. **Session (5)**: Sessions, dialogs
6. **Presentation (6)**: Encryption, compression, formatting
7. **Application (7)**: User interface, applications

### PDU Names by Layer
- **Layer 1**: Bits
- **Layer 2**: Frames
- **Layer 3**: Packets
- **Layer 4**: Segments
- **Layer 5-7**: Data

### TCP vs UDP (Always Tested!)
- **TCP**: Reliable, connection-oriented, flow control, error recovery
- **UDP**: Unreliable, connectionless, faster, no guarantees

### Encapsulation/De-encapsulation Process
- **Down the stack**: Add headers (encapsulation)
- **Up the stack**: Remove headers (de-encapsulation)

## Scoring Guide
- **9-10 correct**: Excellent! You've mastered the most critical OSI/TCP-IP concepts.
- **7-8 correct**: Good! Review the specific layer functions you missed.
- **5-6 correct**: Fair. Focus on memorizing layer functions and PDU names.
- **Below 5**: Study the fundamentals thoroughly - these are essential CCNA concepts.

## Study Tips
- **Memorize layer functions** - this is tested extensively
- **Know PDU names** - frequently asked in different ways
- **Understand encapsulation/de-encapsulation** - appears in troubleshooting scenarios
- **TCP vs UDP differences** - always appears on CCNA exams
- **Practice layered troubleshooting** - bottom-up approach is key