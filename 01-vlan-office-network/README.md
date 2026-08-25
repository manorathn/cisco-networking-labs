# Cisco VLAN Office Network Lab

## Overview

This lab demonstrates the configuration of VLANs
(Virtual Local Area Networks) on a Cisco 2960 switch using
Cisco Packet Tracer.

The goal of the lab was to create three separate office
departments and isolate their network traffic using VLANs.

## Network Topology

The network contains:

- 1 Cisco 2960 switch
- 3 PCs (Personal Computers)
- VLAN 10 — ADMIN
- VLAN 20 — SALES
- VLAN 30 — IT (Information Technology)

## VLAN Configuration

| Department | VLAN | Switch Port | IPv4 Address |
|---|---:|---|---|
| ADMIN | 10 | Fa0/1 | 192.168.10.10 |
| SALES | 20 | Fa0/2 | 192.168.20.10 |
| IT | 30 | Fa0/3 | 192.168.30.10 |

Fa means FastEthernet.

## IPv4 Addressing

### ADMIN-PC

IPv4 (Internet Protocol version 4) Address:

192.168.10.10

Subnet Mask:

255.255.255.0

### SALES-PC

IPv4 Address:

192.168.20.10

Subnet Mask:

255.255.255.0

### IT-PC

IPv4 Address:

192.168.30.10

Subnet Mask:

255.255.255.0

## Switch Configuration

The VLANs were created with:

```cisco
vlan 10
 name ADMIN

vlan 20
 name SALES

vlan 30
 name IT

The switch ports were assigned as access ports:

interface FastEthernet0/1
 switchport access vlan 10
 switchport mode access

interface FastEthernet0/2
 switchport access vlan 20
 switchport mode access

interface FastEthernet0/3
 switchport access vlan 30
 switchport mode access
Verification

The VLAN configuration was verified using:

show vlan brief

The output confirmed:

Fa0/1 belongs to VLAN 10
Fa0/2 belongs to VLAN 20
Fa0/3 belongs to VLAN 30

## Screenshots

### Network Topology
![Network Topology](screenshots/topology.png)

### VLAN Verification
![VLAN Verification](screenshots/vlan-brief.png)

### VLAN Isolation Test
![Failed Ping](screenshots/failed-ping-sales.png)

Connectivity Testing

From ADMIN-PC, I tested connectivity to SALES-PC using:

ping 192.168.20.10

The ping failed as expected.

ADMIN-PC is in VLAN 10 while SALES-PC is in VLAN 20.

Because there is no router or Layer 3 routing device configured,
traffic cannot travel between the VLANs.

This confirms that the VLANs are successfully isolating traffic.

Files
vlan-office-network.pkt — Cisco Packet Tracer project
configs/SW1-config.txt — Full SW1 running configuration
configs/vlan-verification.txt — Output of show vlan brief

Skills Demonstrated
VLAN (Virtual Local Area Network) configuration
Cisco CLI (Command-Line Interface)
Cisco IOS (Internetwork Operating System)
IPv4 (Internet Protocol version 4) addressing
Switch access port configuration
Network segmentation
ICMP (Internet Control Message Protocol) connectivity testing
Basic network troubleshooting

Tools Used
Cisco Packet Tracer
Cisco 2960 Switch
VS Code (Visual Studio Code)
GitHub
