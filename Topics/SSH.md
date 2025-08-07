# SSH (Secure Shell) - CCNA Guide

## 1. What is SSH?

SSH (Secure Shell) is a cryptographic network protocol used to securely access and manage devices over an unsecured network. It replaces older, insecure protocols like Telnet.

## 2. Key Features

- **Encryption**: All traffic is encrypted, ensuring confidentiality and integrity.
- **Authentication**: Supports various methods (password, public key).
- **Command-line Access**: Allows administrators to manage routers, switches, and servers remotely.

## 3. SSH vs Telnet

| Feature | SSH | Telnet |
|--------|-----|--------|
| Encryption | Yes | No |
| Port | 22 | 23 |
| Security | Secure | Insecure |
| Protocol | TCP | TCP |

## 4. SSH Versions

- **SSH v1**: Deprecated due to security vulnerabilities.
- **SSH v2**: Secure and widely used.

## 5. SSH Operation

SSH uses a **client-server model**:
- The SSH **client** initiates the connection.
- The SSH **server** responds and authenticates.

## 6. SSH Configuration on Cisco Devices

```bash
# Set the hostname and domain name
Router(config)# hostname R1
R1(config)# ip domain-name example.com

# Generate RSA key pairs
R1(config)# crypto key generate rsa
How many bits in the modulus [512]: 1024

# Set local user credentials
R1(config)# username admin privilege 15 secret cisco123

# Enable SSH and set vty lines to use local login
R1(config)# line vty 0 4
R1(config-line)# transport input ssh
R1(config-line)# login local

# Enable SSH version 2
R1(config)# ip ssh version 2
```

## 7. SSH Security Best Practices

- Use SSH version 2.
- Use strong passwords or key-based authentication.
- Limit SSH access with ACLs.
- Disable unused vty lines.
- Use logging and monitor access attempts.

## 8. Verifying SSH Configuration

```bash
# Verify SSH version
R1# show ip ssh

# Check vty configuration
R1# show run | section vty

# Test SSH connectivity
PC> ssh -l admin 192.168.1.1
```

## 9. Common SSH Troubleshooting Commands

```bash
R1# debug ip ssh
R1# show ssh
R1# show users
```

## 10. Exam Tips

- Remember SSH uses **TCP port 22**.
- Know how to configure SSH using local authentication.
- Understand differences between SSH and Telnet.
- Be able to troubleshoot SSH access issues.
