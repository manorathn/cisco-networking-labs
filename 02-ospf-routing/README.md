# Cisco OSPF Routing Lab

## Overview

This lab demonstrates dynamic routing using OSPF
(Open Shortest Path First) in Cisco Packet Tracer.

Three Cisco routers were configured in OSPF Area 0,
allowing two separate LANs (Local Area Networks) to
automatically learn routes to each other.

## Network Topology

PC-A --- R1 --- R2 --- R3 --- PC-B

The network contains:

- 3 Cisco routers
- 2 PCs (Personal Computers)
- 2 LANs (Local Area Networks)
- 2 router-to-router networks
- OSPF Area 0

![Network Topology](screenshots/topology.png)

## IP Addressing

| Device | Interface | IPv4 Address | Subnet Mask |
|---|---|---|---|
| PC-A | FastEthernet0 | 192.168.1.10 | 255.255.255.0 |
| R1 | G0/0 | 192.168.1.1 | 255.255.255.0 |
| R1 | G0/1 | 10.0.12.1 | 255.255.255.252 |
| R2 | G0/0 | 10.0.12.2 | 255.255.255.252 |
| R2 | G0/1 | 10.0.23.1 | 255.255.255.252 |
| R3 | G0/0 | 10.0.23.2 | 255.255.255.252 |
| R3 | G0/1 | 192.168.3.1 | 255.255.255.0 |
| PC-B | FastEthernet0 | 192.168.3.10 | 255.255.255.0 |

IPv4 means Internet Protocol version 4.

## OSPF Configuration

OSPF was configured on all three routers using Area 0.

### R1

```cisco
router ospf 1
 router-id 1.1.1.1
 network 192.168.1.0 0.0.0.255 area 0
 network 10.0.12.0 0.0.0.3 area 0

 R2
 router ospf 1
 router-id 2.2.2.2
 network 10.0.12.0 0.0.0.3 area 0
 network 10.0.23.0 0.0.0.3 area 0

 R3
 router ospf 1
 router-id 3.3.3.3
 network 10.0.23.0 0.0.0.3 area 0
 network 192.168.3.0 0.0.0.255 area 0

 OSPF Neighbor Verification
OSPF neighbor relationships were verified using:
show ip ospf neighbor

R2 successfully formed neighbor relationships with R1 and R3.

A neighbor state of FULL confirms that the routers successfully
exchanged OSPF routing information.

Routing Table Verification

The routing table on R1 was verified using:
show ip route

R1 learned the following networks through OSPF:

10.0.23.0/30
192.168.3.0/24

Routes beginning with O indicate routes learned through
OSPF (Open Shortest Path First).

Connectivity Testing

End-to-end connectivity was tested from PC-A to PC-B:
ping 192.168.3.10
The result was:
Sent = 4
Received = 4
Lost = 0
0% packet loss

This confirms that traffic successfully traveled through:

PC-A → R1 → R2 → R3 → PC-B

Trace Route Testing

The route to PC-B was tested using:

tracert 192.168.3.10

tracert displays each routing hop between the source and destination.

Files
ospf-routing.pkt — Cisco Packet Tracer project
configs/R1-config.txt — R1 configuration
configs/R2-config.txt — R2 configuration
configs/R3-config.txt — R3 configuration
configs/ospf-neighbors.txt — OSPF neighbor verification
configs/routing-table.txt — Routing table verification
Skills Demonstrated
OSPF (Open Shortest Path First)
Dynamic routing
IPv4 (Internet Protocol version 4) addressing
Subnetting
Cisco CLI (Command-Line Interface)
Cisco IOS (Internetwork Operating System)
OSPF neighbor relationships
Routing table analysis
ICMP (Internet Control Message Protocol) connectivity testing
Trace route testing
Basic network troubleshooting
Tools Used
Cisco Packet Tracer
Cisco routers
VS Code (Visual Studio Code)
GitHub