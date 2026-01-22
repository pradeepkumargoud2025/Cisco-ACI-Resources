# Cisco ACI – Secondary IP (Side A) and BL Redundancy (Without HSRP/VRRP)

## 📌 Overview

In Cisco ACI, when two Border Leaf (BL) links are connected to a single DCGW (Data Center Gateway), a **Secondary IP** is configured for redundancy.  
Unlike traditional routers, ACI does **not use HSRP/VRRP/GLBP** for gateway redundancy. Instead, ACI uses **fabric routing and ECMP** to achieve failover and load sharing.

---

## 🔹 Topology Example

| Device | IP Address | Role |
|--------|------------|------|
| BL01 | 10.10.10.2 | Border Leaf 01 |
| BL02 | 10.10.10.3 | Border Leaf 02 |
| DCGW | 10.10.10.4 | Primary DCGW |
| Secondary IP | 10.10.10.1 | Shared / Secondary IP |

Both BL01 and BL02 are connected to the same DCGW.

---

## 🧠 What is Secondary IP in ACI?

The **Secondary IP** is a **shared IP address** used by the ACI fabric for redundancy and failover.  
It is **not a virtual gateway** like HSRP/VRRP.

### Key points:
- ACI uses **Secondary IP** for redundancy.
- ACI **does not support HSRP/VRRP/GLBP** on fabric interfaces.
- Both links can be **active simultaneously**.
- Failover is handled by **fabric routing + ECMP**.

---

## ✅ How Redundancy Works in ACI

### Step-by-step Flow:

1. **Both BL01 and BL02 are configured with their own physical IPs.**
2. **Secondary IP is configured as a shared address.**
3. ACI fabric advertises reachability for the Secondary IP via both BL links.
4. ACI uses **ECMP (Equal Cost MultiPath)** to choose the best path.
5. If one link fails, ACI automatically uses the other link.

---

## 🔍 What is the Difference from HSRP/VRRP?

| Feature | HSRP/VRRP | ACI Secondary IP |
|--------|-----------|------------------|
| Active/Standby | Yes | No |
| Virtual Gateway | Yes | No |
| Redundancy Method | Gateway election | ECMP / Fabric routing |
| Protocol | HSRP/VRRP | ACI internal control plane |
| Both links active | No | Yes |

---

## 📌 Traffic Behavior

### When ACI sends traffic to DCGW:
- Destination IP: **10.10.10.1 (Secondary IP)**
- ACI forwards through **BL01 or BL02**
- Path selection is based on **ECMP**
- If one link fails, traffic continues through the other link

### When DCGW sends traffic to ACI:
- DCGW uses the Secondary IP as destination
- ACI replies via the best path

---

## 🔧 Verification Commands (ACI Side)

```bash
show ip route 10.10.10.4
show ip route 10.10.10.4 detail

## These commands help you verify:

* Path selection
* Redundancy
* ECMP behavior
