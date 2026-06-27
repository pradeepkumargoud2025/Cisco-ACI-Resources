# COOP (Council of Oracle Protocol)

## 1. What is COOP?

COOP (Council of Oracle Protocol) is a proprietary Cisco ACI protocol that runs on the Spine switches. It maintains a centralized endpoint database containing endpoint information such as MAC address, IP address, EPG, and the leaf switch where the endpoint is connected.

---

## 2. Why is COOP Required?

In a traditional Layer 2 network, switches flood unknown unicast traffic to discover the destination MAC address.

Cisco ACI avoids unnecessary flooding by using COOP.

Instead of flooding the entire fabric, the ingress leaf queries the Spine (COOP database) to identify the destination endpoint's location. This improves scalability and reduces unnecessary traffic.

---

## 3. Where Does COOP Run?

* COOP runs on the Spine switches.
* Every Spine maintains the endpoint database.
* Leaf switches register their locally learned endpoints with the Spine.

---

## 4. How Does COOP Work?

1. A server connects to Leaf-101.
2. Leaf-101 learns the server's MAC address, IP address, and EPG information.
3. Leaf-101 registers this endpoint information with the Spine using COOP.
4. The Spine stores the endpoint information in its COOP database.
5. When another leaf (for example, Leaf-102) needs to communicate with this endpoint, it queries the Spine.
6. The Spine replies with the destination leaf information.
7. Leaf-102 encapsulates the packet with VXLAN and forwards it to Leaf-101.
8. Leaf-101 decapsulates the VXLAN packet and forwards it to the destination server.

---

## 5. What Information Does COOP Store?

* MAC Address
* IP Address
* Endpoint Location (Leaf Switch)
* EPG Information
* VTEP Information
* Endpoint Mobility Information

---

## 6. Interview Answer (2 Minutes)

"COOP stands for Council of Oracle Protocol. It is a Cisco ACI proprietary protocol that runs on the Spine switches. Its primary function is to maintain a centralized endpoint database. When a leaf switch learns a local endpoint, it registers the endpoint information, such as MAC address, IP address, EPG, and leaf location, with the Spine through COOP. If another leaf needs to communicate with that endpoint, it queries the Spine instead of flooding the entire fabric. The Spine responds with the endpoint location, allowing the ingress leaf to encapsulate the packet using VXLAN and send it directly to the correct destination leaf. This makes endpoint lookup efficient and minimizes unnecessary flooding in the fabric."

---

## 7. Interview Keywords

* Spine Resident Database
* Endpoint Registration
* Endpoint Lookup
* MAC/IP Learning
* VXLAN Forwarding
* Flood Reduction
* Centralized Endpoint Database

---

## 8. Common Interview Questions

Q. Why do we need COOP?

Answer:
To eliminate unnecessary flooding by maintaining a centralized endpoint database and providing endpoint lookup services for leaf switches.

Q. Does COOP run on the Leaf or Spine?

Answer:
COOP runs on the Spine switches. Leaf switches only register and query endpoint information.

Q. Does COOP carry user data traffic?

Answer:
No. COOP is a control-plane protocol used for endpoint registration and lookup. User data traffic is forwarded between leaf switches using VXLAN encapsulation.

---

## Quick Revision (30 Seconds)

COOP = Endpoint Database

Runs on = Spine

Stores = MAC, IP, EPG, Leaf Location

Purpose = Endpoint lookup without flooding

Benefit = Faster forwarding and better scalability
