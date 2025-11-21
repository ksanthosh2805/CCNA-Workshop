# CCNA Workshop: Multi‑Layer Enterprise Network Topology

Design, configure, and verify a complete enterprise‑grade LAN using Cisco Packet Tracer with OSPF routing, multi‑scope DHCP, VLAN segmentation, and web services across three interconnected branch networks.

---

## Project Overview

This workshop focuses on building a hierarchical multi‑router, multi‑switch enterprise topology that mimics a real‑world corporate network.  
The network is divided into three sites (Headquarters, Branch Office, Data Center) and uses dynamic routing (OSPF), centralized IP management (DHCP on routers), VLANs on switches, and a web server reachable end‑to‑end.

---

## Objectives

- Design a hierarchical network with **3 Routers, 6 Switches, and 30+ end devices (including IoT)**.  
- Implement **OSPF** for full IP connectivity between all LANs.  
- Configure **DHCP pools on routers** to dynamically assign IP addresses to clients.  
- Deploy a **Web Server** and verify HTTP access from any PC in the network.  
- Implement **VLANs** on switches to logically segment the network.

---

## Topology Overview

The enterprise network is built around three main routers, each managing two LAN segments:

- **Router 1 (R1) – Headquarters**
  - Manages LAN 1 (Left) and LAN 2 (Right).
  - Connects to Router 2 (R2) via a serial WAN link.

- **Router 2 (R2) – Branch Office (Middle)**
  - Manages LAN 3 (Left) and LAN 4 (Right).
  - Acts as a transit router between R1 and R3.

- **Router 3 (R3) – Data Center**
  - Manages LAN 5 (Left) and LAN 6 (Right).
  - Hosts the Web Server in LAN 6.

---

## IP Addressing Plan

### Router Interfaces and Server

| Device | Interface | Description              | IP Address       | Subnet Mask        |
|--------|-----------|--------------------------|------------------|--------------------|
| R1     | G0/0/0    | LAN 1 (Left) Gateway    | 192.168.10.1     | 255.255.255.0      |
| R1     | G0/0/1    | LAN 2 (Right) Gateway   | 192.168.11.1     | 255.255.255.0      |
| R1     | S0/1/0    | WAN link to R2          | 10.1.1.1         | 255.255.255.252    |
| R2     | G0/0/0    | LAN 3 (Left) Gateway    | 192.168.20.1     | 255.255.255.0      |
| R2     | G0/0/1    | LAN 4 (Right) Gateway   | 192.168.21.1     | 255.255.255.0      |
| R2     | S0/1/0    | WAN link to R1          | 10.1.1.2         | 255.255.255.252    |
| R2     | S0/1/1    | WAN link to R3          | 10.2.2.1         | 255.255.255.252    |
| R3     | G0/0/0    | LAN 5 (Left) Gateway    | 192.168.30.1     | 255.255.255.0      |
| R3     | G0/0/1    | LAN 6 (Right) Gateway   | 192.168.31.1     | 255.255.255.0      |
| R3     | S0/1/0    | WAN link to R2          | 10.2.2.2         | 255.255.255.252    |
| Server | NIC       | Web Server (on R3 LAN6) | 192.168.31.100   | 255.255.255.0      |

- All end devices in each LAN receive IPs via DHCP from their respective router.  
- WAN links use /30 subnets for point‑to‑point efficiency.

---

## Requirements

### Software

- **Cisco Packet Tracer** (any recent version supporting routers, multilayer switches, and IoT devices).

### Hardware (within Packet Tracer)

- 3 x Routers (R1, R2, R3).  
- 6 x Switches (access/distribution as per your design).  
- 30+ end devices:
  - PCs, Laptops, Printers, etc.
  - IoT devices (e.g., 3 Fans, 3 Lights).
- 1 x Server (for HTTP web service).

---

## Step‑by‑Step Configuration

### 1. Router 1 – Headquarters (R1)

Handles LAN 1 and LAN 2, and connects to R2 over Serial.


```

enable
conf t
hostname R1

! LAN Interfaces
interface g0/0/0
ip address 192.168.10.1 255.255.255.0
no shutdown

interface g0/0/1
ip address 192.168.11.1 255.255.255.0
no shutdown

! WAN Interface to R2
interface s0/1/0
ip address 10.1.1.1 255.255.255.252
clock rate 128000
no shutdown

! OSPF Routing
router ospf 1
network 192.168.10.0 0.0.0.255 area 0
network 192.168.11.0 0.0.0.255 area 0
network 10.1.1.0 0.0.0.3 area 0

! DHCP Exclusions
ip dhcp excluded-address 192.168.10.1
ip dhcp excluded-address 192.168.11.1

! DHCP Pools
ip dhcp pool LAN1
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8

ip dhcp pool LAN2
network 192.168.11.0 255.255.255.0
default-router 192.168.11.1
dns-server 8.8.8.8

```



---

### 2. Router 2 – Branch Office (R2)

Middle router bridging R1 and R3, and serving LAN 3 and LAN 4.

```

enable
conf t
hostname R2

! LAN Interfaces
interface g0/0/0
ip address 192.168.20.1 255.255.255.0
no shutdown

interface g0/0/1
ip address 192.168.21.1 255.255.255.0
no shutdown

! WAN to R1
interface s0/1/0
ip address 10.1.1.2 255.255.255.252
no shutdown

! WAN to R3
interface s0/1/1
ip address 10.2.2.1 255.255.255.252
clock rate 128000
no shutdown

! OSPF Routing
router ospf 1
network 192.168.20.0 0.0.0.255 area 0
network 192.168.21.0 0.0.0.255 area 0
network 10.1.1.0 0.0.0.3 area 0
network 10.2.2.0 0.0.0.3 area 0

! DHCP Pools (example pattern – replicate as needed)
! ip dhcp excluded-address 192.168.20.1
! ip dhcp excluded-address 192.168.21.1
! ip dhcp pool LAN3
! network 192.168.20.0 255.255.255.0
! default-router 192.168.20.1
! dns-server 8.8.8.8
! ip dhcp pool LAN4
! network 192.168.21.0 255.255.255.0
! default-router 192.168.21.1
! dns-server 8.8.8.8

```


*(In the actual workshop, create DHCP pools for LAN 3 and LAN 4 using the same structure as R1.)*

---

### 3. Router 3 – Data Center (R3)

Hosts the server networks and connects back to R2.


```

enable
conf t
hostname R3

! LAN Interfaces
interface g0/0/0
ip address 192.168.30.1 255.255.255.0
no shutdown

interface g0/0/1
ip address 192.168.31.1 255.255.255.0
no shutdown

! WAN Interface to R2
interface s0/1/0
ip address 10.2.2.2 255.255.255.252
no shutdown

! OSPF Routing
router ospf 1
network 192.168.30.0 0.0.0.255 area 0
network 192.168.31.0 0.0.0.255 area 0
network 10.2.2.0 0.0.0.3 area 0

! (Optional) DHCP pools for LAN 5 and LAN 6 using same pattern as R1

```



---

## Switch & VLAN Configuration

Example shown for **Switch 0**, connected to R1 and serving VLAN‑based segmentation.

### Switch 0 – VLANs and Access Ports



```

enable
conf t

vlan 10
name STAFF

vlan 20
name GUEST

! Assign access ports to VLAN 10
interface range fa0/1-2
switchport mode access
switchport access vlan 10

! Uplink to Router – ensure correct VLAN
interface gig0/1
switchport mode access
switchport access vlan 10

```


- Repeat or adapt VLAN creation and port assignments on other switches as per your topology.  
- Use `show vlan brief` to confirm VLAN membership and port assignments.

---

## Server Configuration

### Web Server (on R3 LAN 6)

- **Location:** Connected to the switch associated with interface G0/0/1 of R3 (LAN 6).  
- **IP Address (Static):**
  - IP: `192.168.31.100`  
  - Subnet Mask: `255.255.255.0`  
  - Default Gateway: `192.168.31.1`  
- **Services:**
  - Enable **HTTP** (and HTTPS if desired) in the server’s Services tab.  
  - Optionally customize the default web page to indicate successful connectivity.

---

## IoT Device Setup

To reach the 30+ node requirement, include IoT devices alongside PCs:

- **Devices:**
  - 3 x Smart Fans.  
  - 3 x Smart Lights.  
- **Placement:**
  - Connect them to access switch ports across different LAN segments.  
- **Configuration:**
  - Set their **network adapter mode to DHCP** so they receive IP addresses from the corresponding router.  
  - Confirm that each IoT device gets a valid IP in the correct subnet and can ping its default gateway.

---

## Verification & Testing

Use these tests to validate that your configuration is correct.

### 1. End‑to‑End Ping

- From **PC0 in LAN 1 (192.168.10.0/24)**:  
  - Ping `192.168.31.100` (Web Server in LAN 6).  
  - Expected: Successful ICMP replies.

### 2. Trace Route

- From **PC0 in LAN 1** (Command Prompt):



```

tracert 192.168.31.100

```


- Expected hops (example order):
  - `192.168.10.1` (R1 G0/0/0)  
  - `10.1.1.2` (R2 S0/1/0)  
  - `10.2.2.2` (R3 S0/1/0)  
  - `192.168.31.100` (Web Server)

### 3. Web Access Test

- On **PC0 (or any PC in any LAN)**:
  - Open the web browser.  
  - Navigate to `http://192.168.31.100`.  
  - Expected: The server’s web page loads successfully.

### 4. VLAN Verification

- On **Switch 0** (or any switch with VLANs):



```

show vlan brief

```


- Confirm:
  - VLAN 10 labeled `STAFF` with correct ports assigned.  
  - VLAN 20 labeled `GUEST` (if used) with its ports listed.

---

## Expected Learning Outcomes

By completing this workshop, participants will:

- Gain hands‑on experience building a **multi‑router OSPF network**.  
- Understand **DHCP configuration on routers** for multiple LANs.  
- Practice **VLAN creation and port assignment** on switches.  
- Configure and verify **inter‑LAN connectivity** including web access.  
- Integrate **IoT devices** into an enterprise network and verify IP/DHCP behavior.

---
