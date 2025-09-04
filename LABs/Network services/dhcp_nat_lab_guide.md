# CCNA Lab 7.4.1: Basic DHCP and NAT Configuration

## Overview

This lab covers the configuration of DHCP (Dynamic Host Configuration Protocol) and NAT (Network Address Translation) services on Cisco routers. You'll learn to set up a DHCP server, configure various NAT types including static NAT, dynamic NAT with pools, and NAT overload (PAT).

## Learning Objectives

Upon completion of this lab, you will be able to:
- Prepare the network infrastructure
- Perform basic router configurations
- Configure a Cisco IOS DHCP server
- Configure static and default routing
- Configure static NAT
- Configure dynamic NAT with a pool of addresses
- Configure NAT overload (PAT)



## Addressing Table

| Device | Interface | IP Address       | Subnet Mask       |
|--------|-----------|------------------|-------------------|
| R1     | S0/0/0    | 10.1.1.1        | 255.255.255.252   |
|        | Fa0/0     | 192.168.10.1    | 255.255.255.0     |
|        | Fa0/1     | 192.168.11.1    | 255.255.255.0     |
| R2     | S0/0/0    | 10.1.1.2        | 255.255.255.252   |
|        | S0/0/1    | 209.165.200.225 | 255.255.255.252   |
|        | Fa0/0     | 192.168.20.1    | 255.255.255.0     |
| ISP    | S0/0/1    | 209.165.200.226 | 255.255.255.252   |

---

## Task 1: Basic Router Configuration

Configure all routers (R1, R2, and ISP) with the following basic settings:

### Basic Configuration Commands

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname [R1/R2/ISP]
Router(config)# no ip domain-lookup
Router(config)# enable secret class
Router(config)# banner motd #Unauthorized access is prohibited#
Router(config)# line console 0
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# exit
Router(config)# line vty 0 4
Router(config-line)# password cisco
Router(config-line)# login
Router(config-line)# exit
```

### Interface Configuration

#### R1 Configuration:
```cisco
R1(config)# interface s0/0/0
R1(config-if)# ip address 10.1.1.1 255.255.255.252
R1(config-if)# clock rate 64000
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# interface fa0/0
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# interface fa0/1
R1(config-if)# ip address 192.168.11.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
```

#### R2 Configuration:
```cisco
R2(config)# interface s0/0/0
R2(config-if)# ip address 10.1.1.2 255.255.255.252
R2(config-if)# no shutdown
R2(config-if)# exit
R2(config)# interface s0/0/1
R2(config-if)# ip address 209.165.200.225 255.255.255.252
R2(config-if)# clock rate 64000
R2(config-if)# no shutdown
R2(config-if)# exit
R2(config)# interface fa0/0
R2(config-if)# ip address 192.168.20.1 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit
```

#### ISP Configuration:
```cisco
ISP(config)# interface s0/0/1
ISP(config-if)# ip address 209.165.200.226 255.255.255.252
ISP(config-if)# no shutdown
ISP(config-if)# exit
```

### OSPF Configuration

Configure OSPF on R1 and R2 (exclude the ISP network):

```cisco
# On R1:
R1(config)# router ospf 1
R1(config-router)# network 10.1.1.0 0.0.0.3 area 0
R1(config-router)# network 192.168.10.0 0.0.0.255 area 0
R1(config-router)# network 192.168.11.0 0.0.0.255 area 0
R1(config-router)# exit

# On R2:
R2(config)# router ospf 1
R2(config-router)# network 10.1.1.0 0.0.0.3 area 0
R2(config-router)# network 192.168.20.0 0.0.0.255 area 0
R2(config-router)# exit
```

---

## Task 2: Configure DHCP Server on R2

### Step 1: Exclude Static IP Addresses

Exclude the first 10 IP addresses from each pool:

```cisco
R2(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10
R2(config)# ip dhcp excluded-address 192.168.11.1 192.168.11.10
```

### Step 2: Create DHCP Pools

#### Pool for 192.168.10.0/24 network:
```cisco
R2(config)# ip dhcp pool R1Fa0
R2(dhcp-config)# network 192.168.10.0 255.255.255.0
R2(dhcp-config)# dns-server 192.168.11.5
R2(dhcp-config)# default-router 192.168.10.1
R2(dhcp-config)# exit
```

#### Pool for 192.168.11.0/24 network:
```cisco
R2(config)# ip dhcp pool R1Fa1
R2(dhcp-config)# network 192.168.11.0 255.255.255.0
R2(dhcp-config)# dns-server 192.168.11.5
R2(dhcp-config)# default-router 192.168.11.1
R2(dhcp-config)# exit
```



### Step 3: Verify DHCP Configuration

Use these commands to verify DHCP operation:

```cisco
R2# show ip dhcp binding
R2# show ip dhcp pool
R2# debug ip dhcp server events
```

---

## Task 3: Configure Static and Default Routing

### Configure Static Route on ISP

```cisco
ISP(config)# ip route 209.165.200.240 255.255.255.240 serial 0/0/1
```

### Configure Default Route on R2

```cisco
R2(config)# ip route 0.0.0.0 0.0.0.0 209.165.200.226
R2(config)# router ospf 1
R2(config-router)# default-information originate
R2(config-router)# exit
```

### Verify Routing

```cisco
R1# show ip route
R1# ping 209.165.200.225
```

---

## Task 4: Configure Static NAT

### Step 1: Create Static NAT Mapping

Map the internal server to a public IP address:

```cisco
R2(config)# ip nat inside source static 192.168.20.254 209.165.200.254
```

### Step 2: Specify NAT Interfaces

```cisco
R2(config)# interface serial 0/0/1
R2(config-if)# ip nat outside
R2(config-if)# exit
R2(config)# interface fa0/0
R2(config-if)# ip nat inside
R2(config-if)# exit
```

### Step 3: Verify Static NAT

```cisco
ISP# ping 209.165.200.254
R2# show ip nat translations
```

---

## Task 5: Configure Dynamic NAT with Pool

### Step 1: Define NAT Pool

```cisco
R2(config)# ip nat pool MY-NAT-POOL 209.165.200.241 209.165.200.246 netmask 255.255.255.248
```

### Step 2: Create Access Control List

```cisco
R2(config)# ip access-list extended NAT
R2(config-ext-nacl)# permit ip 192.168.10.0 0.0.0.255 any
R2(config-ext-nacl)# permit ip 192.168.11.0 0.0.0.255 any
R2(config-ext-nacl)# exit
```

### Step 3: Bind Pool to ACL

```cisco
R2(config)# ip nat inside source list NAT pool MY-NAT-POOL
```

### Step 4: Configure Inside Interface

```cisco
R2(config)# interface serial 0/0/0
R2(config-if)# ip nat inside
R2(config-if)# exit
```

### Step 5: Verify Dynamic NAT

```cisco
# Generate traffic from PC1 and PC2
R2# show ip nat translations
R2# show ip nat statistics
R2# debug ip nat
```

---

## Task 6: Configure NAT Overload (PAT)

### Step 1: Remove Previous NAT Configuration

```cisco
R2(config)# no ip nat pool MY-NAT-POOL 209.165.200.241 209.165.200.246 netmask 255.255.255.248
R2(config)# no ip nat inside source list NAT pool MY-NAT-POOL
R2# clear ip nat translation *
```

### Step 2: Configure PAT

```cisco
R2(config)# ip nat inside source list NAT interface S0/0/1 overload
```

### Step 3: Verify PAT Configuration

```cisco
R2# show ip nat translations
R2# show ip nat statistics
```

---

## Verification Commands Summary

| Command | Purpose |
|---------|---------|
| `show ip dhcp binding` | Display DHCP lease information |
| `show ip dhcp pool` | Show DHCP pool statistics |
| `show ip nat translations` | Display NAT translation table |
| `show ip nat statistics` | Show NAT statistics |
| `debug ip dhcp server events` | Troubleshoot DHCP issues |
| `debug ip nat` | Troubleshoot NAT issues |
| `show ip route` | Verify routing table |
| `show running-config` | Display current configuration |

---

## Troubleshooting Tips

### DHCP Issues:
- Check DHCP pool configuration
- Ensure excluded-address ranges are correct
- Use debug commands to trace DHCP messages

### NAT Issues:
- Verify inside/outside interface designations
- Check ACL configuration for dynamic NAT
- Ensure routing is working properly
- Use debug ip nat to trace translations
- Clear NAT translations if making configuration changes

### Routing Issues:
- Verify OSPF neighbor relationships
- Check default route propagation
- Ensure static routes are configured correctly
- Test connectivity with ping and traceroute

---

## Key Concepts Learned

1. **DHCP Server Configuration**: How to configure Cisco IOS as a DHCP server with pools, exclusions, and helper addresses
2. **Static NAT**: One-to-one mapping between private and public IP addresses
3. **Dynamic NAT**: Many-to-many mapping using a pool of public IP addresses
4. **NAT Overload (PAT)**: Many-to-one mapping using port numbers to distinguish connections
5. **Routing Integration**: How DHCP and NAT work with routing protocols

This lab provides hands-on experience with essential network services that are commonly used in enterprise networks to manage IP addressing and provide Internet connectivity for private networks.
