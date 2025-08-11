# REST APIs — CCNA Detailed Guide

> **Scope:** This file explains REST APIs in the context of network automation and the CCNA exam. It covers core REST concepts, HTTP methods & status codes, data formats (JSON/XML), RESTCONF basics, examples (curl & Python), security, best practices, and a CCNA checklist.

---

## 1. What is a REST API?

**REST** (Representational State Transfer) is an architectural style for designing networked applications. A **REST API** exposes resources (data or services) as URLs and lets clients interact with those resources using standard HTTP methods.

In networking, REST APIs let automation tools and controllers (Ansible, Python scripts, Cisco DNA Center, etc.) programmatically **read**, **create**, **update**, and **delete** device configuration and operational state through HTTP(S).

Key REST principles:
- **Resources** are identified by URIs (URLs).
- **Stateless**: Each request contains all information needed to process it.
- **Uniform Interface**: Standardized use of HTTP methods and status codes.
- **Representation**: Resources are exchanged as representations (JSON, XML, YAML).

---

## 2. HTTP Methods (CRUD mapping)

| HTTP Method | CRUD Action | Idempotent? | Typical Use in Network Automation |
|-------------|-------------|-------------|-----------------------------------|
| `GET`       | Read        | Yes         | Retrieve interface/state/config   |
| `POST`      | Create      | No          | Create new object, start action   |
| `PUT`       | Replace     | Yes         | Replace configuration resource    |
| `PATCH`     | Modify      | Sometimes   | Modify part of a resource         |
| `DELETE`    | Delete      | Yes         | Remove configuration/resource     |

**Idempotent** means repeating the same request has the same effect as doing it once (important for safe automation).

---

## 3. HTTP Status Codes (Common ones you should know)

- `200 OK` — Request successful; response body typically returned.
- `201 Created` — Resource created (typically from `POST`).
- `204 No Content` — Success but no body (common after `DELETE` or successful `PUT` with no return).
- `400 Bad Request` — Syntax or validation error.
- `401 Unauthorized` — Authentication required or failed.
- `403 Forbidden` — Authenticated but not allowed.
- `404 Not Found` — Resource not found.
- `409 Conflict` — Resource conflict (version or duplicate).
- `500` / `502` / `503` — Server errors.

Memorize the meanings of `200`, `201`, `204`, `400`, `401`, `403`, `404`, `409`.

---

## 4. Data Formats: JSON, XML, YAML

- **JSON** (JavaScript Object Notation): Most common for REST; lightweight, easy for scripts to parse.
- **XML**: Used in older or NETCONF-related APIs; verbose but supports schemas.
- **YAML**: Human-friendly, common in orchestration tools (Ansible), not as common for REST payloads.

For network REST APIs (especially RESTCONF) you’ll often see `application/yang-data+json` or `application/yang-data+xml` content types when interacting with YANG-modeled data.

---

## 5. RESTCONF (Network-specific REST API)

**RESTCONF** is a standardized REST-like protocol (RFC 8040) for accessing YANG-modeled configuration and state data on a device. Key points:

- Uses **HTTP/HTTPS** as transport (usually HTTPS).
- Maps YANG data models to URI endpoints (for example: `/restconf/data/ietf-interfaces:interfaces`).
- Supports standard HTTP methods (`GET`, `PUT`, `PATCH`, `POST`, `DELETE`) to read and manipulate YANG data.
- Content types often: `application/yang-data+json` or `application/yang-data+xml`.
- Used by many Cisco platforms (IOS XE, NX-OS, IOS-XR) to expose device configuration via REST.

> **CCNA tip:** Know that RESTCONF is the REST API commonly used to manage Cisco devices and that it returns JSON or XML representations of YANG data.

---

## 6. Authentication & Security

Network devices expose REST APIs over HTTPS. Common authentication options:

- **Basic Auth** (username/password) — simple, but pair with HTTPS.
- **Token-based** (session tokens, bearer tokens) — more secure than plain basic auth after initial login.
- **Client certificates** (mutual TLS) — strong, certificate-based authentication.
- **AAA integration**: TACACS+/RADIUS for authorization and accounting.

**Security best practices**:
- Always use **HTTPS** (TLS) — never plain HTTP for management APIs.
- Use **least privilege** — limit API user permissions.
- Rotate credentials and use strong passwords or certificates.
- Restrict API access with **ACLs**, VPNs, or jump hosts.
- Enable **logging and auditing** of API activity.
- Rate-limit API access where possible to avoid DoS.

---

## 7. Practical Examples

> **Note:** Replace IPs, usernames, and resource paths with the ones from your lab. Some devices use self-signed certs — you may need `--insecure` with curl or `verify=False` in Python requests (not recommended for production).

### Example 1 — GET interfaces (curl, JSON)
```bash
curl -k -u admin:AdminPass \
  -H "Accept: application/yang-data+json" \
  https://192.0.2.1/restconf/data/ietf-interfaces:interfaces
```

### Example 2 — Configure an interface (PUT) with RESTCONF (JSON payload)
```bash
curl -k -u admin:AdminPass \
  -H "Content-Type: application/yang-data+json" \
  -X PUT https://192.0.2.1/restconf/data/ietf-interfaces:interfaces/interface=GigabitEthernet0/0 \
  -d '{
    "ietf-interfaces:interface": {
      "name": "GigabitEthernet0/0",
      "description": "Configured by RESTCONF",
      "enabled": true,
      "type": "iana-if-type:ethernetCsmacd",
      "ietf-ip:ipv4": {
        "address": [
          { "ip": "192.168.100.1", "netmask": "255.255.255.0" }
        ]
      }
    }
  }'
```

### Example 3 — Python `requests` GET
```python
import requests
from requests.auth import HTTPBasicAuth
import json

url = "https://192.0.2.1/restconf/data/ietf-interfaces:interfaces"
auth = HTTPBasicAuth("admin", "AdminPass")
headers = {"Accept": "application/yang-data+json"}

resp = requests.get(url, headers=headers, auth=auth, verify=False)
print(resp.status_code)
print(json.dumps(resp.json(), indent=2))
```

---

## 8. Design Considerations & Best Practices

- **Idempotency:** Prefer idempotent methods (PUT/DELETE) in automation to avoid side effects when retries happen.
- **Retries & Timeouts:** Set sensible timeouts and retry logic in scripts (avoid infinite retry loops).
- **Versioning:** API endpoints should be versioned (`/api/v1/…`) to support future changes.
- **Rate limiting & pagination:** Be prepared to handle paginated responses and server-imposed rate limits.
- **Schema validation:** Use JSON Schema or YANG models to validate payloads before sending.
- **Atomicity & Consistency:** Some network APIs do not support transactional commits like NETCONF's `candidate` datastore — verify device behavior.
- **Error handling:** Parse and log error responses (status codes + body) for troubleshooting.

---

## 9. REST vs NETCONF (Quick Comparison)

| Feature | REST (RESTCONF) | NETCONF |
|---------|------------------|---------|
| Transport | HTTP/HTTPS | SSH |
| Data Format | JSON or XML | XML |
| Protocol Style | RESTful | RPC-based |
| Model | YANG (RESTCONF maps to YANG) | YANG |
| Transactions | Limited (depends on implementation) | Candidate/commit & confirmed-commit (stronger transaction support) |
| Ease for Scripting | Very easy (HTTP tools, curl, requests) | Requires NETCONF libraries (ncclient) |

---

## 10. Troubleshooting Tips

- Check device **reachability** with `ping` and `telnet`/`nc` to port 443 (HTTPS).
- Verify HTTP/REST services are **enabled** on the device (`ip http secure-server`, `restconf` on Cisco).
- Inspect TLS certificate problems — when testing, you may need to accept self-signed certs.
- Use verbose curl (`-v`) to view HTTP headers and server response.
- Check server-side logs on controllers or devices for API errors.

---

## 11. CCNA Checklist — What to Memorize

- REST = Stateless, resources via URIs, uses HTTP methods.
- HTTP methods: `GET`, `POST`, `PUT`, `PATCH`, `DELETE` (know CRUD mapping).
- Common HTTP status codes (`200`, `201`, `204`, `400`, `401`, `403`, `404`, `409`, `500`).
- Content types: `application/json`, `application/yang-data+json`, `application/yang-data+xml`.
- RESTCONF basics: uses HTTPS, maps YANG models to URIs (e.g., `/restconf/data/...`).
- Security: always use HTTPS, least privilege, AAA, ACLs.
- Automation best practices: idempotency, retries/timeouts, input validation.

---

## 12. Further Reading (recommended)
- RFC 7231 (HTTP/1.1 Semantics and Content) — for deep HTTP concepts.  
- RFC 8040 (RESTCONF) — RESTCONF protocol description.  
- YANG RFCs (RFC 7950) — data modeling language used by NETCONF/RESTCONF.  
- Vendor docs (Cisco RESTCONF guides) — for device-specific endpoints and enabling commands.

---

**End of file — REST APIs (CCNA guide)**  
