# CCNA Containers Quiz

## Instructions
- 10 questions in CCNA exam style
- Select the best answer for each question
- Time limit: 15 minutes
- Focus on containers vs VMs

---

## Questions

### Question 1
Which type of hypervisor is commonly used in data center environments?

A) Type 1 hypervisor  
B) Type 2 hypervisor  
C) Hosted hypervisor  
D) Container engine

---

### Question 2
What is the typical boot time for a container compared to a virtual machine?

A) Containers: minutes, VMs: seconds  
B) Containers: seconds, VMs: minutes  
C) Containers: milliseconds, VMs: minutes  
D) Both boot in the same time

---

### Question 3
Which component manages and allocates hardware resources to virtual machines?

A) Container engine  
B) Host operating system  
C) Hypervisor  
D) Guest operating system

---

### Question 4 - SCENARIO
A development team needs to deploy an application that:
- Requires minimal resource overhead
- Must start quickly (under 1 second)
- Needs to run the same across different environments
- Will be part of a microservices architecture

Which technology should they choose?

A) Type 1 hypervisor with VMs  
B) Type 2 hypervisor with VMs  
C) Containers with orchestration  
D) Physical servers only

---

### Question 5
**MULTIPLE CHOICE (Select TWO)** - Which two are examples of Type 2 hypervisors?

A) VMware ESXi  
B) VMware Workstation  
C) Microsoft Hyper-V  
D) Oracle VirtualBox  
E) Docker Engine

---

### Question 6
What is the PRIMARY difference between containers and virtual machines regarding operating systems?

A) Containers require Windows, VMs require Linux  
B) Containers share the host OS, VMs each run their own OS  
C) VMs share the host OS, containers each run their own OS  
D) Both require separate operating systems

---

### Question 7
Which container orchestration platform was originally designed by Google?

A) Docker Swarm  
B) VMware vSphere  
C) Kubernetes  
D) OpenStack

---

### Question 8 - SCENARIO
An enterprise has legacy applications that require:
- Strong isolation between applications
- Different operating systems (Windows Server and Linux)
- Dedicated resource allocation
- High security requirements

Which technology is MOST appropriate?

A) Containers with Docker  
B) Virtual machines with Type 1 hypervisor  
C) Container orchestration with Kubernetes  
D) Type 2 hypervisor on desktop

---

### Question 9
In terms of disk space usage, containers typically require:

A) Gigabytes, similar to VMs  
B) Terabytes due to dependencies  
C) Megabytes, much less than VMs  
D) The same amount as physical servers

---

### Question 10
What happens to containers when the host operating system crashes?

A) Containers continue running independently  
B) Only some containers are affected  
C) All containers on that host are affected  
D) Containers automatically migrate to other hosts

---

## Answer Key

| Question | Answer | Explanation |
|----------|--------|-------------|
| 1 | **A** | Type 1 (bare-metal) hypervisors are used in data centers |
| 2 | **C** | Containers boot in milliseconds, VMs take minutes |
| 3 | **C** | Hypervisor manages and allocates resources to VMs |
| 4 | **C** | Containers meet all requirements: fast, lightweight, portable |
| 5 | **B, D** | VMware Workstation and VirtualBox are Type 2 hypervisors |
| 6 | **B** | Containers share host OS, VMs run individual operating systems |
| 7 | **C** | Kubernetes was originally designed by Google |
| 8 | **B** | VMs with Type 1 hypervisor provide strong isolation and multi-OS |
| 9 | **C** | Containers use megabytes vs gigabytes for VMs |
| 10 | **C** | All containers affected since they share the host OS |

## Scoring
- **9-10 correct**: Excellent! Ready for CCNA 🎉
- **7-8 correct**: Good understanding 👍  
- **5-6 correct**: Review container concepts 📚
- **Below 5**: Study VM vs container differences 🔄