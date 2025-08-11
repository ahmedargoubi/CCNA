# CCNA Automation Quiz - Ansible, Puppet, and Chef

## Instructions
- Choose the **best** answer for each question
- Each question has only **one correct answer**
- Mark your answers and check against the answer key at the bottom
- Time limit: 15 minutes (similar to CCNA pace)

---

## Question 1
Which automation tool is agentless and uses SSH to connect to network devices?

**A)** Puppet  
**B)** Chef  
**C)** Ansible  
**D)** SaltStack  

---

## Question 2
What is the primary cause of configuration drift in network environments?

**A)** Hardware failures  
**B)** Software bugs in network operating systems  
**C)** Manual configuration changes made over time  
**D)** Network topology changes  

---

## Question 3
Which TCP port does Puppet use for agent-to-master communication?

**A)** 8080  
**B)** 8140  
**C)** 10002  
**D)** 22  

---

## Question 4
In Ansible, what file format is used to write playbooks?

**A)** JSON  
**B)** XML  
**C)** YAML  
**D)** Ruby DSL  

---

## Question 5
What is the main difference between push and pull automation models?

**A)** Push models are faster than pull models  
**B)** Pull models require agents, push models do not  
**C)** Push models send configs immediately, pull models request configs periodically  
**D)** There is no significant difference between the two models  

---

## Question 6
Which automation tool uses Ruby DSL and organizes configurations into cookbooks and recipes?

**A)** Ansible  
**B)** Puppet  
**C)** Chef  
**D)** Terraform  

---

## Question 7
An Ansible inventory file is used to:

**A)** Store backup configurations  
**B)** Define automation tasks and workflows  
**C)** List managed devices and their connection details  
**D)** Create configuration templates  

---

## Question 8
What is the default check-in interval for Puppet agents to contact the Puppet Master?

**A)** 15 minutes  
**B)** 30 minutes  
**C)** 60 minutes  
**D)** 24 hours  

---

## Question 9
Which Ansible module would you use to configure Cisco IOS devices?

**A)** ios_command  
**B)** ios_config  
**C)** ios_facts  
**D)** All of the above can be used for configuration  

---

## Question 10
A network engineer wants to prevent configuration drift across 200 routers while ensuring minimal impact on device performance. Which approach would be MOST suitable?

**A)** Manual configuration checks every week  
**B)** Using Ansible with daily playbook execution  
**C)** Using Puppet with standard 30-minute agent check-ins  
**D)** Using Chef with custom Ruby scripts  

---

## CCNA Exam Tips
- Read each question carefully
- Eliminate obviously wrong answers first  
- Look for keywords like "agentless," "push/pull," port numbers
- Consider the context of the scenario questions

---

## Answer Key

### Question 1: **C) Ansible**
**Explanation:** Ansible is the only agentless automation tool among the major options. It connects via SSH and doesn't require any software installation on managed devices.

### Question 2: **C) Manual configuration changes made over time**
**Explanation:** Configuration drift occurs when network administrators make individual manual changes during troubleshooting or maintenance, causing devices to slowly deviate from standard configurations.

### Question 3: **B) 8140**
**Explanation:** Puppet agents communicate with the Puppet Master using TCP port 8140. This is a key fact for CCNA exams.

### Question 4: **C) YAML**
**Explanation:** Ansible playbooks are written in YAML format, making them human-readable and easy to understand compared to other scripting languages.

### Question 5: **C) Push models send configs immediately, pull models request configs periodically**
**Explanation:** Push models (like Ansible) actively send configurations to devices immediately. Pull models (like Puppet and Chef) have agents that periodically check for updates.

### Question 6: **C) Chef**
**Explanation:** Chef uses Ruby DSL (Domain Specific Language) and organizes configurations into cookbooks (collections) and recipes (individual configuration instructions).

### Question 7: **C) List managed devices and their connection details**
**Explanation:** The Ansible inventory file contains the list of devices to be managed, their IP addresses, credentials, and grouping information.

### Question 8: **B) 30 minutes**
**Explanation:** By default, Puppet agents check in with the Puppet Master every 30 minutes to see if there are configuration updates to apply.

### Question 9: **B) ios_config**
**Explanation:** While ios_command executes commands and ios_facts gathers information, ios_config is specifically designed for making configuration changes to Cisco IOS devices.

### Question 10: **C) Using Puppet with standard 30-minute agent check-ins**
**Explanation:** For preventing configuration drift across many devices with minimal performance impact, Puppet's pull model with 30-minute intervals provides regular compliance checking without overwhelming the devices or network.

---

## Scoring Guide
- **9-10 correct:** Excellent! Ready for CCNA automation topics
- **7-8 correct:** Good understanding, review missed concepts  
- **5-6 correct:** Need more study on automation fundamentals
- **Below 5:** Review the automation guide thoroughly before retaking

---

## Key Concepts to Remember
✅ **Ansible:** Agentless, SSH, YAML, Push model  
✅ **Puppet:** Agent-based, TCP 8140, Pull model, 30-min intervals  
✅ **Chef:** Agent-based, TCP 10002, Ruby DSL, Pull model  
✅ **Configuration Drift:** Caused by manual changes over time  
✅ **Push vs Pull:** Immediate vs. periodic configuration updates  

---

*Practice these concepts regularly and focus on the comparison aspects between tools, as these are frequently tested in CCNA exams.*