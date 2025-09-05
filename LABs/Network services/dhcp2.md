# Packet Tracer - Configure DHCPv4

## Overview

This lab teaches you how to configure a Cisco router as a DHCP server, configure DHCP relay, and configure a router as a DHCP client. You will learn how routers can provide DHCP services without requiring a dedicated server.

## Learning Objectives

Upon completion of this lab, you will be able to:
- Configure a router as a DHCP server
- Configure DHCP relay
- Configure a router as a DHCP client
- Verify DHCP operation and connectivity

## Addressing Table

| Device | Interface | IPv4 Address | Subnet Mask | Default Gateway |
|---------|-----------|--------------|-------------|-----------------|
| R1 | G0/0 | 192.168.10.1 | 255.255.255.0 | N/A |
| R1 | S0/0/0 | 10.1.1.1 | 255.255.255.252 | N/A |
| R2 | G0/0 | 192.168.20.1 | 255.255.255.0 | N/A |
| R2 | G0/1 | DHCP Assigned | DHCP Assigned | N/A |
| R2 | S0/0/0 | 10.1.1.2 | 255.255.255.252 | N/A |
| R2 | S0/0/1 | 10.2.2.2 | 255.255.255.252 | N/A |
| R3 | G0/0 | 192.168.30.1 | 255.255.255.0 | N/A |
| R3 | S0/0/1 | 10.2.2.1 | 255.255.255.252 | N/A |
| PC1 | NIC | DHCP Assigned | DHCP Assigned | DHCP Assigned |
| PC2 | NIC | DHCP Assigned | DHCP Assigned | DHCP Assigned |
| DNS Server | NIC | 192.168.20.254 | 255.255.255.0 | 192.168.20.1 |

---

## Scenario

A dedicated DHCP server is scalable and relatively easy to manage but it can be costly to have one at every location in a network. However, a Cisco router can be configured to provide DHCP services without the need for a dedicated server. As the network technician for your company, you have been assigned the task of configuring a Cisco router as a DHCP server. You are also required to configure the edge router as a DHCP client so that it receives an IP address from the ISP network.

---

## Part 1: Configure a Router as a DHCP Server

### Step 1: Configure the Excluded IPv4 Addresses

Addresses that have been statically assigned to devices in the networks that will use DHCP must be excluded from the DHCP pools. This avoids errors associated with duplicate IP addresses.

#### a. Configure R2 to exclude the first 10 addresses from the R1 LAN:

```cisco
R2(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10
```

#### b. Configure R2 to exclude the first 10 addresses from R3 LAN:

```cisco
R2(config)# ip dhcp excluded-address 192.168.30.1 192.168.30.10
```

**Explanation**: These commands reserve addresses .1 to .10 for static assignment to routers, servers, and management interfaces.

### Step 2: Create a DHCP Pool on R2 for the R1 LAN

#### a. Create a DHCP pool named R1-LAN (case-sensitive):

```cisco
R2(config)# ip dhcp pool R1-LAN
```

#### b. Configure the DHCP pool to include the network address, the default gateway, and the IP address of the DNS server:

```cisco
R2(dhcp-config)# network 192.168.10.0 255.255.255.0
R2(dhcp-config)# default-router 192.168.10.1
R2(dhcp-config)# dns-server 192.168.20.254
R2(dhcp-config)# exit
```

### Step 3: Create a DHCP Pool on R2 for the R3 LAN

#### a. Create a DHCP pool named R3-LAN (case-sensitive):

```cisco
R2(config)# ip dhcp pool R3-LAN
```

#### b. Configure the DHCP pool to include the network address, the default gateway, and the IP address of the DNS server:

```cisco
R2(dhcp-config)# network 192.168.30.0 255.255.255.0
R2(dhcp-config)# default-router 192.168.30.1
R2(dhcp-config)# dns-server 192.168.20.254
R2(dhcp-config)# exit
```

---

## Part 2: Configure DHCP Relay

### Step 1: Configure R1 and R3 as a DHCP Relay Agent

For DHCP clients to obtain an address from a server on a different LAN segment, the interface that the clients are attached to must include a helper address pointing to the DHCP server.

#### a. Configure the helper address for the LAN interface on R1:

```cisco
R1(config)# interface g0/0
R1(config-if)# ip helper-address 10.1.1.2
R1(config-if)# exit
```

#### b. Configure the helper address for the LAN interface on R3:

```cisco
R3(config)# interface g0/0
R3(config-if)# ip helper-address 10.2.2.2
R3(config-if)# exit
```

**Important Note**: 
- On R1, the helper address points to R2 via address 10.1.1.2
- On R3, the helper address points to R2 via address 10.2.2.2
- DHCP traffic from hosts on R1 and R3 LANs will be forwarded to these addresses and processed by the DHCP server configured on R2

### Step 2: Configure Hosts to Receive IP Addressing Information from DHCP

#### a. Configure hosts PC1 and PC2 to receive their IP addresses from a DHCP server:

1. **On PC1:**
   - Click on PC1 → Desktop → IP Configuration
   - Select "DHCP"
   - Wait for address assignment

2. **On PC2:**
   - Click on PC2 → Desktop → IP Configuration
   - Select "DHCP"
   - Wait for address assignment

#### b. Verify that the hosts have received their addresses from the correct DHCP pools:

- PC1 should receive an address in the range 192.168.10.11-192.168.10.254
- PC2 should receive an address in the range 192.168.30.11-192.168.30.254

---

## Part 3: Configure a Router as a DHCP Client

Just as a PC is able to receive an IPv4 address from a server, a router interface has the ability to do the same. Router R2 needs to be configured to receive addressing from the ISP.

### Configure the Interface as a DHCP Client

#### a. Configure the Gigabit Ethernet 0/1 interface on R2 to receive IP addressing from DHCP and activate the interface:

```cisco
R2(config)# interface g0/1
R2(config-if)# ip address dhcp
R2(config-if)# no shutdown
R2(config-if)# exit
```

**Note**: Use Packet Tracer's Fast Forward Time feature to speed up the process.

#### b. Use the show ip interface brief command to verify that R2 received an IP address from DHCP:

```cisco
R2# show ip interface brief
```

The output should show that interface G0/1 has received an IP address from the ISP's DHCP server.

---

## Part 4: Verify DHCP and Connectivity

### Step 1: Verify DHCP Bindings

Use the following command on R2 to see assigned addresses:

```cisco
R2# show ip dhcp binding
```

**Expected Output:**
```
IP address       Client-ID/            Lease expiration    Type
                Hardware address
192.168.10.11    0002.4AA5.1470       --                 Automatic
192.168.30.11    0004.9A97.2535       --                 Automatic
```

### Step 2: Verify Configurations

#### Connectivity Tests to Perform:

1. **From PC1 to PC2:**
   ```
   PC1> ping 192.168.30.11
   ```

2. **From PC1 to DNS Server:**
   ```
   PC1> ping 192.168.20.254
   ```

3. **From PC2 to R1:**
   ```
   PC2> ping 192.168.10.1
   ```

4. **From PC1 to R3:**
   ```
   PC1> ping 192.168.30.1
   ```

All pings should be successful, indicating proper DHCP operation and network connectivity.

---

## Important Verification Commands

| Command | Purpose |
|---------|---------|
| `show ip dhcp binding` | Display active DHCP bindings |
| `show ip dhcp pool` | Display DHCP pool statistics |
| `show ip interface brief` | Verify interface status |
| `show ip dhcp conflict` | Display DHCP address conflicts |
| `debug ip dhcp server events` | Troubleshoot DHCP issues |
| `ipconfig /all` (on PC) | View complete IP configuration |
| `show ip dhcp server statistics` | Display DHCP server statistics |

---

## Troubleshooting

### Common Issues and Solutions:

#### 1. PCs Don't Receive DHCP Addresses:
- **Check** that helper addresses are configured on routers R1 and R3
- **Verify** that DHCP pools are properly configured on R2
- **Verify** connectivity between routers
- **Check** interface status with `show ip interface brief`

#### 2. Incorrect Address Assignment:
- **Verify** address exclusion ranges
- **Check** network configuration in DHCP pools
- **Ensure** pool names match exactly (case-sensitive)

#### 3. No Connectivity Between Networks:
- **Verify** routing configuration
- **Check** that interfaces are active (`no shutdown`)
- **Use** `ping` and `traceroute` to diagnose connectivity issues
- **Verify** default gateways are correctly assigned

#### 4. Router Not Receiving DHCP Address:
- **Check** interface configuration (`ip address dhcp`)
- **Verify** interface is not shutdown
- **Check** connection to ISP DHCP server
- **Use** Fast Forward Time in Packet Tracer

---

## Key Concepts Learned

### 1. **DHCP Server on Router**
- Cost-effective alternative to dedicated DHCP servers
- Pool configuration with networks, gateways, and DNS
- Static address exclusion for network devices

### 2. **DHCP Relay (Helper Address)**
- Enables DHCP clients to access servers on different network segments
- Converts DHCP broadcasts to unicast
- Essential in multi-segment networks

### 3. **DHCP Client on Router**
- Routers can obtain addresses via DHCP
- Useful for ISP connections
- Simplifies WAN address management

### 4. **Dynamic Address Assignment**
- Centralized IP address management
- Reduces manual configuration errors
- Automatic network parameter assignment

---

## Advanced Configuration Options

### Additional DHCP Pool Parameters:

```cisco
R2(dhcp-config)# lease 0 2 30        # Lease time: 2 hours 30 minutes
R2(dhcp-config)# domain-name company.com
R2(dhcp-config)# netbios-name-server 192.168.20.100
R2(dhcp-config)# option 150 ip 192.168.20.200  # TFTP server for IP phones
```

### DHCP Reservations (Static Assignments):

```cisco
R2(config)# ip dhcp pool PRINTER
R2(dhcp-config)# host 192.168.10.100 255.255.255.0
R2(dhcp-config)# client-identifier 01aa.bbcc.ddee.ff
R2(dhcp-config)# default-router 192.168.10.1
R2(dhcp-config)# dns-server 192.168.20.254
```

---

## Benefits of This Configuration

1. **Centralization**: Centralized IP address management
2. **Cost-Effectiveness**: No need for dedicated DHCP servers
3. **Flexibility**: Support for multiple networks from single server
4. **Automation**: Automatic network parameter assignment
5. **Scalability**: Easily extensible to new networks
6. **Redundancy**: Multiple routers can provide DHCP services

This lab demonstrates how Cisco routers can provide robust DHCP services for small to medium enterprise networks while maintaining flexibility and reducing infrastructure costs.
