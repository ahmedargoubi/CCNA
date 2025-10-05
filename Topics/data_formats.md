# JSON, XML, and YAML — CCNA Automation Data Formats

## 1. Data Serialization

**Data Serialization** is the process of converting data into a standardized format or structure that can be:
- **Stored** (in a file)
- **Transmitted** (over a network)  

and later **reconstructed** by a different application.

This allows data to be communicated between applications in a way **both applications understand**.

Data serialization languages represent variables in **text form**, making them human-readable and machine-readable.

---

## 2. JSON (JavaScript Object Notation)

### Definition
- JSON is an **open standard file format** and **data interchange format** that uses human-readable text to store and transmit data objects.
- Defined in **RFC 8259** → [RFC Link](https://datatracker.ietf.org/doc/html/rfc8259)
- Originally derived from JavaScript, but **language-independent**.
- Widely used in **REST APIs**.
- Whitespace is **insignificant**.

### Data Types in JSON

#### Primitive Types
- **String** → Text value inside double quotes  
  Example: `"Hello"`, `"5"`
- **Number** → Numeric value without quotes  
  Example: `5`, `100`
- **Boolean** → True/False without quotes  
  Example: `true`, `false`
- **Null** → Represents the intentional absence of a value  
  Example: `null`

#### Structured Types
- **Object** → Unordered list of **key-value pairs** (dictionary)  
  - Surrounded by `{ }`
  - Keys are strings in quotes
  - Values can be any valid JSON data type
  - Key and value separated by a colon `:`
  - Multiple pairs separated by commas

```json
{
  "hostname": "R1",
  "ip": "192.168.1.1",
  "status": true
}
```

- **Array** → Ordered list of values separated by commas
  - Surrounded by `[ ]`
  - Values can be mixed types

```json
["R1", "R2", 5, true]
```

---

## 3. XML (Extensible Markup Language)

### Definition
- Originally developed as a markup language, now used for data serialization.
- Similar to HTML in syntax but focuses on data storage and exchange.
- Less human-readable than JSON.
- Whitespace is **insignificant**.
- Often used by REST APIs and NETCONF.

### XML Structure
Data is enclosed in tags:

```xml
<device>
  <hostname>R1</hostname>
  <ip>192.168.1.1</ip>
  <status>true</status>
</device>
```

---

## 4. YAML (YAML Ain't Markup Language)

### Definition
- Initially stood for "Yet Another Markup Language", now "YAML Ain't Markup Language" to emphasize it's for data serialization.
- Used by automation tools like Ansible.
- Very human-readable compared to JSON and XML.
- Whitespace is **significant** — indentation is critical.
- Files start with `---` (three dashes).

### YAML Structure
Key-value pairs are written as:

```yaml
hostname: R1
ip: 192.168.1.1
status: true
```

Lists are indicated with a dash `-`:

```yaml
devices:
  - R1
  - R2
```

---

## 5. JSON vs YAML — Same Data Example

### JSON Format
```json
{
  "devices": [
    "R1",
    "R2"
  ]
}
```

### YAML Format
```yaml
devices:
  - R1
  - R2
```

---

## 6. CCNA Key Points to Remember

- **JSON**: Most common in REST APIs (RESTCONF), lightweight, uses `{}` and `[]`.
- **XML**: Common in NETCONF, uses `<tags>`.
- **YAML**: Used in configuration automation tools like Ansible, indentation matters.
- **JSON & XML**: Whitespace does not matter.
- **YAML**: Whitespace matters.

---

## Quick Reference Summary

| Format | Human Readable | Whitespace Significant | Common Use Cases |
|--------|----------------|----------------------|------------------|
| JSON   | ✅ Good        | ❌ No               | REST APIs, RESTCONF |
| XML    | ⚠️ Moderate    | ❌ No               | NETCONF, SOAP APIs |
| YAML   | ✅ Excellent   | ✅ Yes              | Ansible, Configuration Files |
