# Enterprise IP Services Implementation Lab


A Cisco-based enterprise network lab built in GNS3, focusing on the implementation, configuration, and verification of essential IP services in a segmented enterprise network.

The project demonstrates practical experience with DHCP, DNS, Network Address Translation (NAT/PAT), Network Time Protocol (NTP), inter-VLAN routing, static routing, and access control lists (ACLs).

## 1. Project Overview

Project Name: Enterprise IP Services Implementation Lab
Platform: GNS3
Network Devices: Cisco IOS Routers and Layer 2 Switch
Routing: Static Routing
IP Addressing: IPv4
Status: Completed

## Objectives

Implement centralized DHCP services for multiple VLANs.
Configure inter-VLAN routing using router-on-a-stick. 
Enable DNS name resolution for internal and simulated external hostnames.
Implement PAT (NAT Overload) for translating private IP addresses.
Configure an NTP server and synchronize network device clocks.
Establish connectivity between enterprise LAN segments and a simulated ISP network.
Apply ACLs for basic traffic filtering.
Verify IP services through Cisco IOS operational commands and end-to-end testing.

## 2. Network Topology

The lab consists of four primary network devices:

Device | Role | Description |
|--- |--- |
ENT-SW1| Layer 2 Switch | VLAN segmentation, trunking, and Layer 2 security|
ENTR1| Enterprise Router| DHCP, inter-VLAN routing, DNS, NAT/PAT, and NTP client|
INTENET| Simulated ISP Router| External network simulation, DNS services, and traffic filtering|
NTP-PH| NTP Server| Provides centralized network time synchronization|
