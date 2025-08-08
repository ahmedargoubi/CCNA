# CCNA Automation & Data Formats – 20 Question Exam-Style Quiz

## Question 1
Which of the following statements about **JSON** is correct?  
A) JSON uses key-value pairs and supports data types such as strings, numbers, arrays, and objects.  
B) JSON is a binary format that cannot be read by humans.  
C) JSON supports comments inside the data.  
D) JSON requires each key to start with a number.  

---

## Question 2
In **XML**, what is the correct structure of an element?  
A) `<tag>content`  
B) `<tag>content</close>`  
C) `<tag>content</tag>`  
D) `tag{content}`  

---

## Question 3
Which of the following statements about **YAML** is true?  
A) YAML requires curly braces `{}` to define objects.  
B) YAML uses indentation to represent hierarchy.  
C) YAML is not human-readable.  
D) YAML cannot represent lists.  

---

## Question 4
Which data format is **most compact and human-readable**?  
A) JSON  
B) XML  
C) YAML  
D) CSV  

---

## Question 5
What is the main advantage of **JSON** over **XML**?  
A) JSON supports namespaces.  
B) JSON is less verbose and easier to parse programmatically.  
C) JSON allows multiple root elements.  
D) JSON is the only format that supports nested data.  

---

## Question 6
Which of the following is **not** a valid YAML list?  
A)  
```yaml
items:
  - apple
  - banana
  - orange
```

B)
```yaml
items: [apple, banana, orange]
```

C)
```yaml
items:
    apple
    banana
    orange
```

D) All are valid.

---

## Question 7
Which data format is **self-describing and tag-based**?  
A) JSON  
B) XML  
C) YAML  
D) INI  

---

## Question 8
Which CCNA automation tool often uses **JSON or YAML** for playbooks and configuration?  
A) SNMP  
B) Ansible  
C) Telnet  
D) FTP  

---

## Question 9
In **JSON**, which of the following is **valid syntax**?  
A)
```json
{ name: "Router1", "ip": "192.168.1.1" }
```

B)
```json
{ "name": "Router1", "ip": "192.168.1.1" }
```

C)
```json
{ "name": "Router1", ip: "192.168.1.1" }
```

D) All are valid.

---

## Question 10
Which of the following is **true** about YAML syntax?  
A) Indentation must use tabs.  
B) Strings must be enclosed in double quotes.  
C) Indentation must use spaces.  
D) Lists cannot be nested.  

---

## Question 11
Which JSON data type is used to represent the absence of a value?  
A) null  
B) none  
C) void  
D) empty  

---

## Question 12
In XML, which of the following is used to add extra information to an element?  
A) Attributes  
B) Comments  
C) Namespaces  
D) Stylesheets  

---

## Question 13
Which of the following is a valid YAML comment?  
A) `# This is a comment`  
B) `<!-- This is a comment -->`  
C) `// This is a comment`  
D) `/* This is a comment */`  

---

## Question 14
Which network automation protocol commonly uses XML data format?  
A) RESTCONF  
B) NETCONF  
C) SNMP  
D) Telnet  

---

## Question 15
What is the file extension commonly used for YAML files?  
A) .xml  
B) .json  
C) .yaml or .yml  
D) .txt  

---

## Question 16
Which of these is NOT a primitive data type in JSON?  
A) Boolean  
B) String  
C) Array  
D) Number  

---

## Question 17
How are lists represented in JSON?  
A) Curly braces `{}`  
B) Square brackets `[]`  
C) Parentheses `()`  
D) Angle brackets `<>`  

---

## Question 18
Which YAML feature makes it highly human-readable?  
A) Use of tags like `<tag>`  
B) Use of curly braces `{}`  
C) Use of indentation and minimal syntax  
D) Use of binary encoding  

---

## Question 19
What is the recommended way to handle indentation in YAML files?  
A) Use tabs only  
B) Use spaces only  
C) Use a mix of tabs and spaces  
D) Indentation doesn't matter  

---

## Question 20
Which data format allows defining schemas to validate structure and content?  
A) JSON Schema (for JSON) and XSD (for XML)  
B) YAML Schema only  
C) Only XML allows schemas  
D) None of these  

---

## Answer Key

1. **A)** JSON uses key-value pairs and supports data types such as strings, numbers, arrays, and objects.
2. **C)** `<tag>content</tag>` - XML elements must have opening and closing tags.
3. **B)** YAML uses indentation to represent hierarchy.
4. **C)** YAML - Most human-readable due to minimal syntax and indentation.
5. **B)** JSON is less verbose and easier to parse programmatically.
6. **C)** Invalid YAML list - missing dashes (-) for list items.
7. **B)** XML - Self-describing and tag-based structure.
8. **B)** Ansible - Uses YAML for playbooks and JSON for data exchange.
9. **B)** `{ "name": "Router1", "ip": "192.168.1.1" }` - All keys must be in quotes.
10. **C)** Indentation must use spaces (tabs are not recommended).
11. **A)** null - JSON uses lowercase 'null' to represent absence of value.
12. **A)** Attributes - XML attributes provide additional element information.
13. **A)** `# This is a comment` - YAML uses hash symbol for comments.
14. **B)** NETCONF - Network management protocol that uses XML encoding.
15. **C)** .yaml or .yml - Both extensions are commonly used for YAML files.
16. **C)** Array - Array is a structured data type, not primitive.
17. **B)** Square brackets `[]` - JSON arrays use square bracket notation.
18. **C)** Use of indentation and minimal syntax - Makes YAML highly readable.
19. **B)** Use spaces only - YAML standard recommends spaces over tabs.
20. **A)** JSON Schema (for JSON) and XSD (for XML) - Both formats support schema validation.

---

## Scoring Guide
- **18-20 correct:** Excellent! Ready for CCNA automation section
- **15-17 correct:** Good knowledge, review missed topics
- **12-14 correct:** Fair understanding, more practice needed
- **Below 12:** Study data formats fundamentals again

## Key CCNA Exam Tips
- **Protocol associations:** JSON with RESTCONF, XML with NETCONF, YAML with Ansible
- **Syntax rules:** JSON requires quotes on keys, YAML uses indentation, XML uses tags
- **Human readability:** YAML > JSON > XML
- **Parsing efficiency:** JSON > XML for REST APIs
- **Schema validation:** Both JSON and XML support schema validation