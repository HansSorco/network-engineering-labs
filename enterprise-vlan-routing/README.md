[README_enterprise_switching_routing_lab.md](https://github.com/user-attachments/files/33906774/README_enterprise_switching_routing_lab.1.md)
# Enterprise Switching and Routing Lab

## Project Overview

This project demonstrates an enterprise-style campus network lab using multilayer switching, Layer 2 redundancy, gateway redundancy, and dynamic routing.

The lab was validated after configuration fixes. End-to-end ping tests from PCs to the tested IP addresses succeeded. The command outputs shared for review showed bundled EtherChannels, operational trunks, spanning-tree operation, GLBP gateway redundancy, and OSPF neighbor adjacencies in the `FULL` state.

## Objectives

- Configure redundant Layer 2 links using EtherChannel.
- Carry selected VLANs over 802.1Q trunks.
- Use Spanning Tree Protocol (STP) to prevent Layer 2 loops and control root-bridge placement.
- Provide redundant default gateways with GLBP.
- Establish OSPF neighbor adjacencies between multilayer switches.
- Verify end-to-end connectivity with PC ping tests.

## Architecture Summary

The observed lab includes multilayer switches and access switches. VLANs 10, 20, 30, and 40 appear in the gateway and spanning-tree configuration. VLAN 1001 appears in trunk/STP output but, according to the lab author, is unused by end devices and has no access ports assigned to it.

The design uses:
- **EtherChannel:** Port-channels Po2 and Po3.
- **802.1Q trunks:** Inter-switch links carrying explicitly allowed VLANs.
- **STP:** Root placement differs by VLAN, providing deliberate distribution of Layer 2 forwarding paths.
- **GLBP:** Virtual default gateways with different active gateway roles across VLANs.
- **OSPF:** Neighbor relationships between multilayer switches, with adjacencies reaching `FULL`.

<img width="1652" height="800" alt="image" src="https://github.com/user-attachments/assets/b17533e8-20bf-437f-9e39-b66c6aeedb42" />


## VLAN and Gateway Summary

The following gateway addresses were visible in the supplied GLBP output. Confirm these against your final configuration before publishing.

| VLAN | Subnet / mask | GLBP virtual IP | Notes |
|---|---|---|---|
| 10 | 192.168.10.0/25 | 192.168.10.126 | GLBP configured on both multilayer switches |
| 20 | 192.168.10.128/25 | 192.168.10.254 | GLBP configured on both multilayer switches |
| 30 | 192.168.20.0/24 | 192.168.20.254 | GLBP configured on both multilayer switches |
| 40 | 192.168.30.0/24 | 192.168.30.254 | GLBP configured on both multilayer switches |
| 1001 | Unused by end devices; no access ports are members |

## Key Technologies and Their Roles

### VLANs and trunking

VLANs separate Layer 2 broadcast domains. Trunk links carry multiple VLANs between switches. The supplied outputs show different allowed-VLAN lists on some trunks; document these as intentional pruning if that is the design goal, and confirm that each VLAN is allowed on the paths it needs.

<img width="843" height="275" alt="image" src="https://github.com/user-attachments/assets/4296767d-ed42-44b0-9574-859f327aa026" />




SVIs


<img width="843" height="73" alt="image" src="https://github.com/user-attachments/assets/65670a98-d458-449e-8079-d0ad5fc6f0c8" />



Ent-MLSW1


<img width="897" height="672" alt="image" src="https://github.com/user-attachments/assets/c8a92235-253a-408f-a2eb-2f33a3bb06b9" />




Ent-MLSW2


<img width="897" height="680" alt="image" src="https://github.com/user-attachments/assets/24fef879-18f4-49d4-bc43-44ffd3c2528f" />




ACSW1


<img width="898" height="667" alt="image" src="https://github.com/user-attachments/assets/ce472fc8-4659-4cb8-902c-40a5edf390c0" />




ACSW2


<img width="897" height="691" alt="image" src="https://github.com/user-attachments/assets/feba9769-52dd-4198-b876-95a263f6649c" />


### EtherChannel

EtherChannel bundles multiple physical links into a logical port-channel. The supplied `show etherchannel summary` output showed Po2 and Po3 as `SU`, with member interfaces marked `P`, indicating Layer 2 port-channels in use with bundled member ports.

Ent-MLSW1
<img width="893" height="669" alt="image" src="https://github.com/user-attachments/assets/7cdcec91-6ba8-4c4f-8f21-239598df5872" />


Ent-MLSW2
<img width="896" height="675" alt="image" src="https://github.com/user-attachments/assets/df6e4871-68bb-4353-84e6-9d14d9cc44e7" />


ACSW1
<img width="894" height="673" alt="image" src="https://github.com/user-attachments/assets/897328df-39da-476e-b6a0-2c7e8b7a2fba" />


ACSW2
<img width="895" height="673" alt="image" src="https://github.com/user-attachments/assets/3f7a8b59-95d0-4174-ad91-fa90b0274605" />

### Spanning Tree Protocol

STP prevents Layer 2 loops while selecting forwarding and alternate paths. The supplied STP outputs showed different root bridges for different VLANs, consistent with per-VLAN root placement and load distribution.

Ent-MLSW1
<img width="1055" height="1034" alt="image" src="https://github.com/user-attachments/assets/6a110000-d96c-4ea0-b33e-0a8b109931d6" />
<img width="1053" height="399" alt="image" src="https://github.com/user-attachments/assets/01dee86e-f0c2-4e5e-b680-4935f0d66323" />


Ent-MLSW2
<img width="1056" height="1037" alt="image" src="https://github.com/user-attachments/assets/0731fcb0-dacc-41b1-b9c1-a3d980694f33" />
<img width="1054" height="399" alt="image" src="https://github.com/user-attachments/assets/1e551cf5-14db-49e9-9e0c-efe6908efb0f" />


ACSW1
<img width="1054" height="1040" alt="image" src="https://github.com/user-attachments/assets/a9c649a7-1681-4367-890d-673c31fbf9bb" />
<img width="1052" height="333" alt="image" src="https://github.com/user-attachments/assets/acc552b3-f55f-4d73-bd39-c1cfd6f36444" />


ACSW2
<img width="1050" height="1050" alt="image" src="https://github.com/user-attachments/assets/5ba952d1-7b08-4821-b257-d94f96ed5558" />
<img width="1055" height="321" alt="image" src="https://github.com/user-attachments/assets/69a40daa-8e96-4c6a-b0f0-70bb23007143" />


### GLBP

GLBP provides a virtual default gateway and can distribute gateway forwarding roles across participating routers or multilayer switches. The supplied output showed GLBP configured for VLANs 10, 20, 30, and 40, with active roles distributed across the two multilayer switches.
Ent-R1
<img width="852" height="620" alt="image" src="https://github.com/user-attachments/assets/8977bed4-e141-4be6-9b56-9cd7e06b63d3" />


Ent-R2
<img width="857" height="637" alt="image" src="https://github.com/user-attachments/assets/05739de4-b5b2-4753-a98a-1ed4d4cdbbcf" />


Ent-MLSW1
<img width="861" height="636" alt="image" src="https://github.com/user-attachments/assets/8ecc8abf-6b57-4571-a887-448c6679bbc1" />


Ent-MLSW2
<img width="834" height="618" alt="image" src="https://github.com/user-attachments/assets/ca148edf-6afb-4f8a-ae21-3d9e10afe75c" />


### OSPF

OSPF dynamically exchanges routing information. The supplied outputs showed OSPF neighbors in the `FULL` state, including DR/BDR roles on the displayed multiaccess segments.

Ent-R1
<img width="848" height="1044" alt="image" src="https://github.com/user-attachments/assets/c5580f7c-7dcd-45a4-aeb7-f83f7715f782" />


Ent-R2
<img width="851" height="1047" alt="image" src="https://github.com/user-attachments/assets/bdede01a-5fbf-433b-9933-6a2f6ef6d878" />


Ent-MLSW1
<img width="848" height="653" alt="image" src="https://github.com/user-attachments/assets/2493e26c-c1e5-4e7b-ab11-9787f3a280cd" />
<img width="841" height="576" alt="image" src="https://github.com/user-attachments/assets/067d3b99-b826-4993-a0d0-5ceba04700ed" />


Ent-MLSW2
<img width="855" height="651" alt="image" src="https://github.com/user-attachments/assets/e9fe891a-a274-478c-b421-6a739f7ecab8" />
<img width="847" height="579" alt="image" src="https://github.com/user-attachments/assets/6373544b-9bd2-45f7-b6b4-90a56b8fee82" />



## Validation and Test Results

| Area | Verification | Result |
|---|---|---|
| EtherChannel | `show etherchannel summary` | Port-channels Po2 and Po3 bundled successfully (`SU`, member links `P`). |
| Trunking | `show interfaces trunk` | Trunks operational; allowed VLANs verified. |
| STP | `show spanning-tree` | Root bridge placement and forwarding paths verified across VLANs. |
| GLBP | `show glbp brief` | Gateway redundancy configured for VLANs 10, 20, 30, and 40. |
| OSPF | `show ip ospf neighbor` | Neighbor adjacencies reached `FULL`. |
| End-to-end connectivity | PC ping tests | Successful replies from the tested source and destination IP addresses. |


<img width="844" height="763" alt="image" src="https://github.com/user-attachments/assets/4ab04425-de17-4812-86b5-b538c4adeee4" />

<img width="849" height="1040" alt="image" src="https://github.com/user-attachments/assets/77f5d3c7-01ce-43fc-8626-c9f1d30d9786" />

<img width="887" height="1040" alt="image" src="https://github.com/user-attachments/assets/cd65d377-48e6-42b1-9f58-006da3285d2e" />

<img width="899" height="1044" alt="image" src="https://github.com/user-attachments/assets/abf63e93-78b1-4e76-b948-d2e4317c4aee" />



## Design Notes and Follow-up Checks

- VLAN 1001 is intentionally unused by end devices and has no access ports assigned, according to the lab author.
- Confirm the different allowed-VLAN lists on trunk links are intentional and do not isolate a VLAN from a required destination.
- Review DHCP snooping trust settings if DHCP snooping is part of the security design. Trust only links that should legitimately receive DHCP server messages; do not trust end-device-facing ports without a specific reason.
- If Dynamic ARP Inspection (DAI) is enabled for a VLAN, confirm the expected ARP validation source is available, such as DHCP snooping bindings or appropriate static ARP ACLs.
- Re-test connectivity after changes to VLAN pruning, STP, GLBP, or routing.



## Skills Demonstrated

- VLAN segmentation and 802.1Q trunking
- EtherChannel
- STP troubleshooting and root-bridge placement
- GLBP gateway redundancy
- OSPF neighbor and route verification
- Layer 2 / Layer 3 troubleshooting
- Network validation and technical documentation

## Disclaimer

This is a personal lab project for learning and portfolio purposes. The documentation describes a simulated environment and should not be interpreted as a production deployment guide. Verify all addresses, device names, and configuration details against the final lab before publishing.
