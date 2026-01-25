📌 How Cisco ACI Builds Forwarding Tables

This document explains how ACI learns endpoints and builds forwarding tables using VXLAN, COOP, and contracts.

✅ 1. Endpoint Learning

ACI learns endpoints when a device sends traffic (ARP/DHCP/data packets).

Learning occurs only on the leaf switch directly connected to the endpoint.

Endpoint details include:
* MAC address
* IP address (optional)
* VLAN / VNID
* EPG
* VRF
* Leaf ID

✅ 2. COOP (Control Plane Distribution)

Learned endpoint info is sent to spines using COOP.

Spines act as a central directory, not forwarding devices.

COOP stores:
* Endpoint → Leaf mapping
* IP → MAC mapping
* EPG → VNID mapping

✅ 3. Endpoint Table Creation

Each leaf builds its Endpoint Table (EPM) based on COOP information.

Example entry:

MAC: AA:BB:CC:DD:EE:FF
IP: 10.1.1.10
EPG: Web-EPG
Leaf: 101
VNID: 50001

✅ 4. VXLAN + VNID Mapping

ACI uses VXLAN encapsulation for all traffic.

Mapping:
* VRF → VRF VNID
* BD → BD VNID
* EPG → EPG VNID

✅ 5. L2 Forwarding (Same BD)

Leaf checks MAC → VNID → destination leaf.

Packet is VXLAN encapsulated and sent directly to destination leaf.

No flooding across fabric (known unicast only).

✅ 6. L3 Forwarding (Inter-BD / Inter-EPG)

ACI uses Anycast Gateway.

Routing is distributed on every leaf.

Leaf builds routing table from:

* Connected BDs
* Static routes
* OSPF / BGP via L3Out

✅ 7. Contract Enforcement

Before forwarding, ACI checks contract rules.

If allowed:

* Forwarding entries are programmed.

If not allowed:

* Traffic is dropped.

✅ 8. Hardware Programming

All forwarding information is programmed into leaf ASIC tables:

* L2 forwarding table
* L3 FIB
* Endpoint table
* Contract policy table
* VXLAN tunnel table

🧠 Summary

ACI builds forwarding tables by learning endpoints on leaf switches, distributing endpoint and policy information via COOP on spines, mapping EPGs to VNIDs, enforcing contracts, 
and programming hardware tables for VXLAN-based forwarding.
