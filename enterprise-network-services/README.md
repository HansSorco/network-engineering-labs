# 🌐 Enterprise IP Services Lab

A GNS3 networking project where I configured and tested different network services using Cisco IOS.

The main focus of this lab is implementing DHCP, DNS, NAT/PAT, NTP, static routing, and ACLs in a simulated enterprise network.

## 🖥️ Lab Setup

| Device | Role |
|---|---|
| ENTR1 | Enterprise router |
| ENT-SW1 | Layer 2 switch |
| INTENET | Simulated ISP router |
| NTP-PH | NTP server |
| PC1–PC4 | End devices |

**Tools used:** GNS3 and Cisco IOS

## 🗺️ Network Topology

The enterprise LAN has two VLANs connected to ENTR1 through an 802.1Q trunk. ENTR1 connects to INTENET through a serial link. The simulated external network also contains the NTP server.

<!-- Add your GNS3 topology screenshot here -->
![Network Topology](images/topology.png)

## 📋 IP Addressing

### VLANs

| VLAN | Name | Network | Gateway |
|---|---|---|---|
| 10 | DeptA | `192.168.1.0/25` | `192.168.1.1` |
| 20 | DeptB | `192.168.1.128/25` | `192.168.1.129` |

### End Devices

| Device | VLAN | IP Address |
|---|---:|---|
| PC1 | 10 | `192.168.1.2` |
| PC2 | 10 | `192.168.1.3` |
| PC3 | 20 | `192.168.1.130` |
| PC4 | 20 | `192.168.1.131` |

### Router Interfaces

| Device | Interface | IP Address |
|---|---|---|
| ENTR1 | G0/0.10 | `192.168.1.1/25` |
| ENTR1 | G0/0.20 | `192.168.1.129/25` |
| ENTR1 | Serial3/0 | `203.100.110.1/30` |
| INTENET | Serial3/0 | `203.100.110.2/30` |
| INTENET | G0/0 | `142.250.207.1/24` |
| INTENET | G1/0 | `202.90.142.2/30` |
| INTENET | G2/0 | `142.251.153.1/24` |
| NTP-PH | G0/0 | `202.90.142.1/30` |

## ⚙️ Configured Services

### 1. DHCP

Configured ENTR1 as the DHCP server for both VLANs.

- Created separate DHCP pools for VLAN 10 and VLAN 20.
- Configured the default gateway and DNS server for each pool.
- Excluded gateway addresses from DHCP allocation.
- Set the domain name to `internal.local`.

**Verification:**

```cisco
show ip dhcp binding
show ip dhcp pool
```

All four PCs successfully received their expected IP addresses.

### 2. DNS

Configured Cisco IOS DNS services on ENTR1 and INTENET.

**ENTR1:**
- Enabled the DNS server.
- Configured the primary domain `internal.local`.
- Added static hostname records for the four PCs.

**INTENET:**

Added local DNS records for the simulated external destinations:

| Hostname | IP Address |
|---|---|
| `www.google.com` | `142.250.207.46` |
| `google.com` | `142.250.207.46` |
| `www.youtube.com` | `142.251.153.4` |
| `youtube.com` | `142.251.153.4` |

These are locally configured records pointing to simulated lab destinations, not actual Google or YouTube servers.

**Verification:**

```cisco
show hosts
ping www.google.com
ping www.youtube.com
```

Hostname resolution and ping tests were successful within the lab.

### 3. NAT/PAT

Configured NAT Overload on ENTR1 to translate internal private IP addresses using the router's Serial3/0 address.

```cisco
access-list 1 permit 192.168.1.0 0.0.0.255

ip nat inside source list 1 interface Serial3/0 overload
```

The VLAN subinterfaces were configured as NAT inside interfaces, while Serial3/0 was configured as the NAT outside interface.

**Verification:**

```cisco
show ip nat translations
show ip nat statistics
```

Observed translation:

| Inside Local | Inside Global |
|---|---|
| `192.168.1.2` | `203.100.110.1` |

NAT statistics also showed successful translation hits.

### 4. NTP

Configured NTP-PH as the NTP master and ENTR1 as the NTP client.

**NTP-PH:**

```cisco
clock timezone PHT 8 0
ntp master 5
ntp update-calendar
```

**ENTR1:**

```cisco
ntp server 202.90.142.1
```

**Verification:**

```cisco
show ntp status
show ntp associations
```

ENTR1 successfully synchronized with NTP-PH at `202.90.142.1`. The device reported a synchronized status and selected the server as its system peer.

### 5. Static Routing

Configured a default route on ENTR1 pointing toward INTENET.

```cisco
ip route 0.0.0.0 0.0.0.0 203.100.110.2
```

This allows the enterprise router to forward traffic for destinations outside its directly connected networks to the simulated ISP.

### 6. ACL

Configured a standard ACL on INTENET Serial3/0 to demonstrate source-address filtering.

```cisco
access-list 10 deny 192.168.1.0 0.0.0.255
access-list 10 permit any

interface Serial3/0
 ip access-group 10 in
```

The ACL denies packets sourced from `192.168.1.0/24` and permits other source addresses.

One thing I encountered while troubleshooting was the implicit deny at the end of an ACL. Initially, I had only the deny statement, which caused other traffic to be denied as well. Adding `permit any` resolved that issue.

Since NAT happens on ENTR1 before traffic reaches INTENET, the source address seen by INTENET is the translated address `203.100.110.1`, not the original PC address.

## 🔐 Layer 2 Configuration

I also configured supporting Layer 2 features on ENT-SW1.

| Feature | Configuration |
|---|---|
| VLANs | VLAN 10 and VLAN 20 |
| Trunking | 802.1Q |
| Spanning Tree | Rapid-PVST+ |
| PortFast | Enabled by default on edge ports |
| BPDU Guard | Enabled by default on edge ports |
| DHCP Snooping | VLAN 10 and VLAN 20 |
| Dynamic ARP Inspection | VLAN 10 and VLAN 20 |
| Port Security | Sticky MAC |
| Violation Mode | Restrict |

## 🧪 Verification

These are some of the commands I used while configuring and troubleshooting the lab.

| Command | Purpose |
|---|---|
| `show ip interface brief` | Check interface status and IP addresses |
| `show vlan brief` | Check VLAN assignments |
| `show interfaces trunk` | Check trunk ports |
| `show ip dhcp binding` | Check DHCP leases |
| `show hosts` | Check DNS host records |
| `show ip nat translations` | Check NAT translations |
| `show ip nat statistics` | Check NAT statistics |
| `show ntp status` | Check NTP synchronization |
| `show ntp associations` | Check NTP peers |
| `show ip arp` | Check ARP entries |

### Test Results

- [x] DHCP address assignment for all four PCs
- [x] Internal hostname resolution
- [x] Simulated external hostname resolution
- [x] NAT/PAT translations
- [x] NTP synchronization between ENTR1 and NTP-PH
- [x] VLAN and trunk configuration
- [x] ACL configuration and traffic filtering
- [x] Connectivity testing between network segments

## 🛠️ Troubleshooting Notes

Some of the issues I encountered during the lab:

- **NAT:** Had to correct the inside and outside interface assignments before translations worked as expected.
- **ACL:** Initially forgot about the implicit deny rule, which blocked traffic not explicitly permitted.
- **NAT and ACL interaction:** Learned that the destination router sees the translated source address after NAT, which affects how its ACL evaluates traffic.
- **NTP:** Used `show ntp status` and `show ntp associations` to confirm actual synchronization instead of relying only on the configuration.

These troubleshooting steps helped me better understand how the different services interact during packet forwarding.

## 🧰 Technologies Used

- GNS3
- Cisco IOS
- DHCP
- DNS
- NAT/PAT
- NTP
- Static Routing
- Access Control Lists
- VLANs and 802.1Q
- Rapid-PVST+
- DHCP Snooping
- Dynamic ARP Inspection
- Port Security

---

**Project Status:** Completed and tested in GNS3.

