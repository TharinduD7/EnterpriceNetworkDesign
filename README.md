# Enterprise Network Infrastructure — Packet Tracer Project

# Overview

This project simulates a multi-floor enterprise network with dual-ISP redundancy, a redundant core routing layer, and a distribution/access layer serving six departmental VLANs across three floors.

First Floor Second Floor Third Floor
Sales | HR Finance | ICT Admin | Server Room

# WAN Layer (Internet Edge)

Link Subnet Description
ISP-MAIN ↔ R1 195.136.17.0/30 Primary ISP link
ISP-MAIN ↔ R2 195.136.17.8/30 Cross-redundant link
ISP-BACKUP ↔ R2 195.136.17.12/30 Backup ISP link
ISP-BACKUP ↔ R1 195.136.17.4/30 Cross-redundant link
ISP-MAIN and ISP-BACKUP provide dual-WAN redundancy.
R1 and R2 (Cisco 2911 routers) are each cross-connected to both ISPs for failover.

# Core/Distribution Layer

Link Subnet
R1 ↔ Multilayer1 172.16.3.144/30
R1 ↔ Multilayer2 172.16.3.152/30
R2 ↔ Multilayer1 172.16.3.148/30
R2 ↔ Multilayer2 172.16.3.156/30

Multilayer1 and Multilayer2 (Cisco 3650-24PS) form a redundant distribution/core switching layer, each dual-homed to both R1 and R2.

Each access-layer switch is dual-homed to both Multilayer1 and Multilayer2 for link redundancy.

# Access Layer / VLANs

VLAN Department Floor Network Switch Devices
10 Sales & Marketing 1st 172.16.1.0/25 2960-24TT (Sales) PC, Printer, AP, Laptop, Tablet
20 HR & Logistics 1st 172.16.1.128/25 2960-24TT (HR) PC, Printer, AP, Laptop, Tablet
30 Finance & Accounting 2nd 172.16.2.0/25 2960-24TT (Finance) PC, Printer, AP, Laptop, Tablet
40 ICT 2nd 172.16.2.128/25 2960-24TT (ICT) PC, Printer, AP, Laptop, Tablet
50 Admin 3rd 172.16.3.0/25 2960-24TT (Admin) PC, Printer, AP, Laptop, Tablet
60 Server Room 3rd 172.16.3.128/28 2960-24TT (Server Room) DHCP Server, DNS Server, Email Server, SysAdmin PC

Each departmental switch (except Server Room) connects an Access Point that serves wireless Laptop and Tablet clients, plus wired PC and Printer endpoints.

Server Room (VLAN 60 — 172.16.3.128/28)
DHCP-Server — assigns IP addressing to all VLANs
DNS-Server — name resolution
Email-Server — internal mail service
SysAdmin-PC — management/administration workstation

Redundancy Design
Dual ISP uplinks with cross-connections (R1/R2 each reach both ISPs).
Dual core routers (R1, R2) cross-linked to dual multilayer switches (Multilayer1, Multilayer2).
Dual-homed access switches — every floor/department switch connects to both multilayer switches, avoiding a single point of failure.
Recommended protocols to configure: HSRP/VRRP (gateway redundancy), EIGRP/OSPF (core routing), spanning-tree (loop prevention on dual-homed links), DHCP relay (ip helper-address) from each VLAN to the Server Room.

# IP Addressing Plan

Segment Network

WAN links 195.136.17.0/30, .4/30, .8/30, .12/30
Core links (R–Multilayer) 172.16.3.144/30, .148/30, .152/30, .156/30
VLAN 10 – Sales & Marketing 172.16.1.0/25
VLAN 20 – HR & Logistic 172.16.1.128/25
VLAN 30 – Finance & Accounting 172.16.2.0/25
VLAN 40 – ICT 172.16.2.128/25
VLAN 50 – Admin 172.16.3.0/25
VLAN 60 – Server Room 172.16.3.128/28

# Equipment List

2x Cisco 2911 Routers (R1, R2)
2x Cisco 3650-24PS Multilayer Switches (Multilayer1, Multilayer2)
6x Cisco 2960-24TT Access Switches (Sales, HR, Finance, ICT, Admin, Server Room)
6x Access Points (one per department, except Server Room)
3x Servers (DHCP, DNS, Email)
End devices: PCs, Printers, Laptops, Tablets per department

# Floors

First Floor: Sales & Marketing, HR & Logistics
Second Floor: Finance & Accounting, ICT
Third Floor: Admin, Server Room

# Notes / Next Steps

Configure HSRP/VRRP virtual gateways per VLAN across Multilayer1/Multilayer2.
Configure trunking (802.1Q) between multilayer switches and access switches.
Configure inter-VLAN routing on the multilayer switches (SVIs) or on R1/R2.
Configure DHCP scopes per VLAN, relayed via ip helper-address to 172.16.3.128/28.
Configure static/default routes or a dynamic routing protocol (OSPF/EIGRP) between R1, R2 and the ISPs/core.
Enable Rapid PVST+ or similar STP variant on redundant access-layer trunks to avoid loops.
