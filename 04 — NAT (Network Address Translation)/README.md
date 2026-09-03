# Lab 04 — NAT/PAT Configuration

## Overview

In this lab, I configured NAT (Network Address Translation) and PAT
(Port Address Translation) using Cisco routers in Cisco Packet Tracer.

The goal was to allow a device on a private network to communicate with
an outside network while translating the device's private IP address to
the outside IP address of the router.

---

## Technologies Used

- Cisco Packet Tracer
- Cisco 2911 Routers
- Cisco 2960 Switch
- IPv4 (Internet Protocol version 4)
- Static Routing
- NAT (Network Address Translation)
- PAT (Port Address Translation)
- ACL (Access Control List)
- ICMP (Internet Control Message Protocol)

---

## Network Topology

PC1 ---- SW1 ---- R1 ---- R2 ---- SERVER

### Networks

Private LAN:
192.168.10.0/24

R1-to-R2 Network:
10.0.0.0/30

Outside Server Network:
192.168.20.0/24

---

## IP Addressing

### PC1

IP Address: 192.168.10.10
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.10.1

### R1

GigabitEthernet0/0:
192.168.10.1/24
NAT Inside

GigabitEthernet0/1:
10.0.0.1/30
NAT Outside

### R2

GigabitEthernet0/0:
10.0.0.2/30

GigabitEthernet0/1:
192.168.20.1/24

### SERVER

IP Address: 192.168.20.10
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.20.1

---

## Static Routing

R1 needed a route to the outside server network.

R1:

ip route 192.168.20.0 255.255.255.0 10.0.0.2

This tells R1 to send traffic destined for the 192.168.20.0/24
network to R2 at 10.0.0.2.

A temporary return route was initially configured on R2 during
connectivity testing. After PAT was configured, this route was removed
to demonstrate that the outside network did not need a route directly
to PC1's private network.

---

## NAT Inside and Outside Interfaces

R1 GigabitEthernet0/0 was configured as the NAT inside interface:

interface gigabitEthernet0/0
ip nat inside

R1 GigabitEthernet0/1 was configured as the NAT outside interface:

interface gigabitEthernet0/1
ip nat outside

---

## NAT Access Control List

A standard ACL (Access Control List) was created:

access-list 1 permit 192.168.10.0 0.0.0.255

The ACL identifies the 192.168.10.0/24 private network as traffic
eligible for NAT translation.

The wildcard mask 0.0.0.255 corresponds to the /24 network.

---

## PAT Configuration

PAT was configured using:

ip nat inside source list 1 interface gigabitEthernet0/1 overload

This tells R1 to translate addresses matched by ACL 1 using the IP
address assigned to GigabitEthernet0/1.

The "overload" keyword enables PAT, allowing multiple inside devices
to share the outside interface address.

---

## NAT Translation

PC1 has the private IP address:

192.168.10.10

When PC1 communicated with the server, R1 translated the source address
to:

10.0.0.1

Example:

Inside Local:
192.168.10.10

Inside Global:
10.0.0.1

Outside Global:
192.168.20.10

This translation was verified with:

show ip nat translations

---

## Verification

Connectivity was tested from PC1 using:

ping 192.168.20.10

The successful ping demonstrated end-to-end connectivity.

NAT translations were verified using:

show ip nat translations

NAT statistics were verified using:

show ip nat statistics

The NAT statistics showed active translations, hits, misses, and
expired translations.

---

## What I Learned

In this lab I learned how NAT allows private IPv4 addresses to
communicate with outside networks.

I also learned how PAT allows multiple internal devices to share a
single outside IP address.

I configured NAT inside and outside interfaces, created an ACL to
identify traffic requiring translation, configured NAT overload, and
verified translations using Cisco IOS commands.

I also practiced troubleshooting by testing routing before configuring
NAT and verifying connectivity at each stage.

---

## Important Abbreviations

NAT — Network Address Translation

PAT — Port Address Translation

ACL — Access Control List

IP — Internet Protocol

IPv4 — Internet Protocol version 4

LAN — Local Area Network

ICMP — Internet Control Message Protocol

CLI — Command-Line Interface

MAC — Media Access Control

ARP — Address Resolution Protocol

---

## Verification Screenshots

### Successful PC1 to Server Ping

![Successful Ping](screenshots/01-successful-ping.png)

### NAT Translation Table

![NAT Translations](screenshots/02-nat-translations.png)

### NAT Statistics

![NAT Statistics](screenshots/03-nat-statistics.png)