🌐 1. Fabric

Definition: The ACI fabric is the entire network infrastructure (spine, leaf, APIC, endpoints).

Purpose: Provides high-performance, scalable, automated connectivity.

Components:

* Leaf switches: Connect to endpoints (servers, routers, firewalls)
* Spine switches: Connect only to leafs (no spine-to-spine or leaf-to-leaf)
* APICs: Centralized management & policy control

🧠 2. APIC (Application Policy Infrastructure Controller)

Definition: The brain of the ACI fabric, controlling policies, configurations, and monitoring.

Cluster: Usually 3 or more nodes for HA.

Functions:

* Policy management
* Fabric discovery
* Health monitoring

Integration with orchestration tools (vCenter, OpenStack, Kubernetes)

🧩 3. Cluster

Definition: A group of APIC controllers working together.

Purpose: Redundancy, scalability, fault tolerance.

🏗️ 4. POD (Point of Delivery)

Definition: One independent ACI fabric (spine + leaf + APICs).

Purpose: Scalability, resiliency, multi-location deployments.

## Multi-POD ##

Definition: Multiple PODs under one APIC cluster connected via IPN (Inter-POD Network).

Benefits: Centralized management, fault isolation, disaster recovery.
     
🧱 5. Bridge Domain (BD)

Definition: Layer 2 forwarding domain, like a VLAN.

Purpose: Connects EPGs; endpoints in same BD communicate at L2.

L3 Capability:

* Associate BD with VRF
* Configure Subnet/Gateway
* Now BD can route traffic (acts like an SVI)

BD Modes Comparison:

| Mode  | Subnet | VRF   | Routing | Use Case                   |
| ----- | ------ | ----- | ------- | -------------------------- |
| L2 BD | ❌ No   | ❌ No  | ❌ No    | Same-subnet communication  |
| L3 BD | ✅ Yes  | ✅ Yes | ✅ Yes   | Inter-subnet communication |

🧩 6. Endpoint Group (EPG)

Definition: Logical group of endpoints sharing policies.

Purpose: Define traffic rules between groups.

Example: Web EPG ↔ DB EPG

🏢 7. Tenant

Definition: Logical container holding policies, BDs, EPGs, VRFs.

Purpose: Segmentation of applications or environments.

Types:

Infrastructure Tenant (system)

Common Tenant (shared)

User-defined Tenant (apps, Prod/Dev/Testing)

🌍 8. VRF (Virtual Routing and Forwarding)

Definition: Layer 3 routing domain, isolates traffic.

Purpose: Keeps tenants/applications separate.

🔄 9. Contract

Definition: Policy defining communication rules between EPGs.

Contains: Filters (TCP/80, ICMP), Actions (permit/deny).

☁️ 10. Orchestration

Definition: Integration with external automation tools (vCenter, OpenStack, Kubernetes).

Purpose: Automates network provisioning for dynamic workloads.

🧭 11. Policy Model

ACI is policy-driven:

Define intent → APIC translates to configurations automatically

⚙️ 12. Fabric Discovery

Automatic detection of leaf/spine switches via LLDP.

Simplifies topology setup.

📡 13. Access Policy

Defines how endpoints connect to leaf switches (interface policies, profiles).

| Term          | Layer             | Purpose                      | Example                 |
| ------------- | ----------------- | ---------------------------- | ----------------------- |
| Fabric        | Physical          | Core infra                   | Spine-Leaf topology     |
| APIC          | Control Plane     | Centralized management       | Policy engine           |
| Cluster       | Control Plane     | Redundancy of APICs          | 3-node cluster          |
| POD           | Physical          | Independent fabric           | One DC fabric           |
| Multi-POD     | Physical          | Multiple PODs under one APIC | Multi-building DC       |
| BD            | L2/L3             | Forwarding domain            | Web-BD with gateway     |
| EPG           | Policy Group      | Endpoint grouping            | Web servers             |
| Contract      | Policy            | Control communication        | Allow HTTP traffic      |
| Tenant        | Logical Container | Segmentation                 | Prod/Dev                |
| VRF           | Layer 3           | Routing isolation            | Separate routing tables |
| Orchestration | Automation        | Auto provisioning            | vCenter integration     |
| Access Policy | Interface         | Endpoint connection rules    | Leaf port-profile       |
