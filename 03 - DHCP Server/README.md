# Lab 03 — DHCP Server Configuration

## Overview

In this lab, I configured a Cisco router as a DHCP server to automatically provide network configuration to client computers.

DHCP (Dynamic Host Configuration Protocol) allows devices to automatically receive important network settings instead of requiring an administrator to manually configure each device.

The network consists of:

- 1 Cisco 2911 Router
- 1 Cisco 2960 Switch
- 3 Client PCs

The DHCP server automatically provides each client with:

- IPv4 (Internet Protocol version 4) address
- Subnet mask
- Default gateway
- DNS (Domain Name System) server

I also verified network connectivity and examined how DHCP, ARP (Address Resolution Protocol), MAC (Media Access Control) addressing, switching, and ICMP (Internet Control Message Protocol) work together on a local network.

## Network Topology

The lab uses a simple LAN (Local Area Network) consisting of one router, one switch, and three client computers.

### Devices

| Device | Purpose | IP Address |
|---|---|---|
| R1 (Router 1) | Router and DHCP Server | 192.168.10.1 |
| SW1 (Switch 1) | Connects devices on the LAN | No IP configured |
| PC-A | DHCP Client | 192.168.10.21 |
| PC-B | DHCP Client | 192.168.10.22 |
| PC-C | DHCP Client | 192.168.10.23 |

### Network Addressing

- Network Address: `192.168.10.0/24`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `192.168.10.1`
- DNS (Domain Name System) Server: `8.8.8.8`
- DHCP (Dynamic Host Configuration Protocol) Excluded Range: `192.168.10.1 - 192.168.10.20`
- First Available DHCP Client Address: `192.168.10.21`

### Topology

![DHCP Lab Network Topology](screenshots/01-topology.png)


## Router Configuration

### Configure the Router Interface

The router interface connected to SW1 (Switch 1) was configured with the IP (Internet Protocol) address `192.168.10.1`.

```text
enable — Enters Privileged EXEC (Executive) mode, which provides access to administrative and troubleshooting commands.
configure terminal — Enters Global Configuration mode, where changes can be made to the router.
hostname R1 — Changes the router's hostname to R1 (Router 1).
interface GigabitEthernet0/0 — Selects the Gigabit Ethernet interface connected to SW1.
ip address 192.168.10.1 255.255.255.0 — Assigns the router interface the IP address 192.168.10.1 with a /24 subnet mask.
no shutdown — Enables the router interface.
exit — Leaves Interface Configuration mode.

DHCP Configuration

DHCP (Dynamic Host Configuration Protocol) was configured on R1 to automatically assign network settings to the client computers.

Exclude Reserved Addresses
ip dhcp excluded-address 192.168.10.1 192.168.10.20

This prevents DHCP from assigning addresses 192.168.10.1 through 192.168.10.20 to clients.

These addresses are reserved so they can be used for infrastructure devices such as routers, switches, servers, and printers.

Create the DHCP Pool
ip dhcp pool OFFICE-LAN
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
DHCP Command Explanation
ip dhcp pool OFFICE-LAN — Creates a DHCP address pool named OFFICE-LAN.
network 192.168.10.0 255.255.255.0 — Defines the 192.168.10.0/24 network that DHCP will serve.
default-router 192.168.10.1 — Tells DHCP clients to use R1 at 192.168.10.1 as their default gateway.
dns-server 8.8.8.8 — Tells DHCP clients to use 8.8.8.8 as their DNS (Domain Name System) server.

### One important concept to remember

You're documenting two different roles performed by **R1 (Router 1)**:

```text
                   R1
             192.168.10.1
              /          \
             /            \
       DHCP Server     Default Gateway
            ↓                ↓
     Gives PCs their     Provides path
     network settings   to other networks

So when PC-A started, DHCP (Dynamic Host Configuration Protocol) gave it:

IP Address:       192.168.10.21
Subnet Mask:      255.255.255.0
Default Gateway:  192.168.10.1
DNS Server:       8.8.8.8

## DHCP Verification

After configuring DHCP (Dynamic Host Configuration Protocol), each client computer was configured to obtain its network settings automatically.

The clients received the following IPv4 (Internet Protocol version 4) addresses:

| Device | IPv4 Address | Subnet Mask | Default Gateway | DNS Server |
|---|---|---|---|---|
| PC-A | 192.168.10.21 | 255.255.255.0 | 192.168.10.1 | 8.8.8.8 |
| PC-B | 192.168.10.22 | 255.255.255.0 | 192.168.10.1 | 8.8.8.8 |
| PC-C | 192.168.10.23 | 255.255.255.0 | 192.168.10.1 | 8.8.8.8 |

### DHCP Client Configuration

PC-A successfully received its network configuration automatically from R1 (Router 1).

![PC-A DHCP Configuration](screenshots/02-pc-a-dhcp.png)

### Verify DHCP Bindings

The following command was used on R1:

```text
show ip dhcp binding
```

A DHCP binding shows which IPv4 address has been assigned to a particular DHCP client.

The router showed three active DHCP assignments:

- PC-A → `192.168.10.21`
- PC-B → `192.168.10.22`
- PC-C → `192.168.10.23`

![DHCP Binding Table](screenshots/03-dhcp-bindings.png)

### Verify the DHCP Pool

The following command was used:

```text
show ip dhcp pool
```

This displays information about the DHCP address pool, including the network being served and the number of addresses currently leased to clients.

The OFFICE-LAN pool showed three leased addresses, confirming that all three PCs successfully received their network configuration from R1.

DHCP Server
     R1
      │
      ├── leases 192.168.10.21 → PC-A
      ├── leases 192.168.10.22 → PC-B
      └── leases 192.168.10.23 → PC-C

      DORA:

D — Discover: The PC looks for a DHCP server.
O — Offer: R1 offers an IP address.
R — Request: The PC requests that address.
A — Acknowledge: R1 approves the lease.

## Network Connectivity Testing

After the DHCP (Dynamic Host Configuration Protocol) clients received their network settings, I tested connectivity between the devices using the `ping` command.

### Test PC-A to R1

From PC-A, I tested connectivity to R1 (Router 1), which has the IPv4 (Internet Protocol version 4) address `192.168.10.1`.

```text
ping 192.168.10.1
```

The test returned:

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

This confirmed that PC-A could successfully communicate with its default gateway.

![PC-A Ping to R1](screenshots/04-router-ping.png)

### Test PC-A to PC-B

I then tested communication between two DHCP clients on the same LAN (Local Area Network).

```text
ping 192.168.10.22
```

PC-B responded successfully with 0% packet loss.

![PC-A Ping to PC-B](screenshots/05-pc-ping.png)

### ICMP

ICMP stands for Internet Control Message Protocol.

The `ping` command uses ICMP messages to test whether another device is reachable across an IP network.

When PC-A pinged R1, the basic process was:

PC-A → ICMP Echo Request → R1

PC-A ← ICMP Echo Reply ← R1

Receiving the Echo Reply confirmed that communication between the devices was successful.

### Understanding the Ping Results

A successful response looked similar to:

```text
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
```

- `bytes=32` — 32 bytes of test data were sent.
- `time<1ms` — The response took less than one millisecond.
- `TTL` — Time To Live. This limits how many router hops an IP packet can travel before being discarded.
- `0% loss` — All test packets successfully received responses.

### Same-Network Communication

PC-A (`192.168.10.21`) and PC-B (`192.168.10.22`) are both members of the `192.168.10.0/24` network.

Because both computers are on the same IP network, R1 does not need to route traffic between them. The computers communicate through SW1 (Switch 1) at the local network level.

Tested two different types of communication:

PC-A → R1
192.168.10.21 → 192.168.10.1

Tests communication with the
default gateway.

PC-A → SW1 → PC-B
.21             .22

Both devices are on
192.168.10.0/24

## ARP and MAC Address Verification

After verifying connectivity, I examined how devices on the LAN (Local Area Network) locate and communicate with each other.

### ARP Table

ARP stands for Address Resolution Protocol.

ARP is used to map an IPv4 (Internet Protocol version 4) address to a MAC (Media Access Control) address on the local network.

On PC-A, I used:

```text
arp -a
```

The ARP table showed:

| IPv4 Address | MAC Address | Device |
|---|---|---|
| 192.168.10.1 | 0005.5e6c.9d01 | R1 |
| 192.168.10.22 | 0060.3e11.2150 | PC-B |
| 192.168.10.23 | 0007.eca7.b538 | PC-C |

![ARP Table](screenshots/06-arp-table.png)

### How ARP Works

When PC-A wanted to communicate with PC-B, it knew PC-B's IPv4 address:

```text
192.168.10.22
```

However, because PC-B was on the same local network, PC-A also needed PC-B's MAC address.

PC-A used ARP to determine which MAC address belonged to `192.168.10.22`.

The process was:

1. PC-A determined that `192.168.10.22` was on the same local network.
2. PC-A sent an ARP request asking which device owned `192.168.10.22`.
3. PC-B responded with its MAC address.
4. PC-A stored the IPv4-to-MAC mapping in its ARP table.
5. PC-A could then send Ethernet frames to PC-B.

## Switch MAC Address Table

I also examined the MAC address table on SW1 (Switch 1).

The following Cisco command was used:

```text
show mac address-table
```

The switch learned the following MAC addresses:

| Device | MAC Address | Switch Port |
|---|---|---|
| R1 | 0005.5e6c.9d01 | GigabitEthernet0/1 |
| PC-A | 0060.3e90.247e | FastEthernet0/1 |
| PC-B | 0060.3e11.2150 | FastEthernet0/2 |
| PC-C | 0007.eca7.b538 | FastEthernet0/3 |

![Switch MAC Address Table](screenshots/07-mac-table.png)

### How the Switch Uses MAC Addresses

SW1 automatically learns which MAC addresses are reachable through each switch port.

For example:

```text
PC-B
IPv4: 192.168.10.22
MAC: 0060.3e11.2150
        |
        |
SW1 FastEthernet0/2
```

When SW1 receives an Ethernet frame destined for PC-B's MAC address, it checks its MAC address table and forwards the frame through FastEthernet0/2.

This allows the switch to efficiently forward Ethernet traffic between devices on the LAN.

There are three different pieces of information working together:

IP Address
192.168.10.22
      ↓
     ARP
      ↓
MAC Address
0060.3e11.2150
      ↓
Switch MAC Table
      ↓
FastEthernet0/2
      ↓
    PC-B

IP = Internet Protocol: identifies where the device exists logically on the IP network.

ARP = Address Resolution Protocol: figures out which MAC address belongs to a local IPv4 address.

MAC = Media Access Control: identifies the network interface for local Ethernet communication.

FastEthernet0/2 is the physical switch interface leading to PC-B.

There are three different pieces of information working together:

IP Address
192.168.10.22
      ↓
     ARP
      ↓
MAC Address
0060.3e11.2150
      ↓
Switch MAC Table
      ↓
FastEthernet0/2
      ↓
    PC-B

IP = Internet Protocol: identifies where the device exists logically on the IP network.

ARP = Address Resolution Protocol: figures out which MAC address belongs to a local IPv4 address.

MAC = Media Access Control: identifies the network interface for local Ethernet communication.

FastEthernet0/2 is the physical switch interface leading to PC-B.

There are three different pieces of information working together:

IP Address
192.168.10.22
      ↓
     ARP
      ↓
MAC Address
0060.3e11.2150
      ↓
Switch MAC Table
      ↓
FastEthernet0/2
      ↓
    PC-B

IP = Internet Protocol: identifies where the device exists logically on the IP network.

ARP = Address Resolution Protocol: figures out which MAC address belongs to a local IPv4 address.

MAC = Media Access Control: identifies the network interface for local Ethernet communication.

FastEthernet0/2 is the physical switch interface leading to PC-B.