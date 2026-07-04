# Cisco ACI - APIC Replacement & Cluster Join (Production Runbook)

## Scenario: One APIC in a 5-node production cluster has failed permanently. You have received a replacement APIC (RMA) and need to add it back into the existing cluster.

A production ACI Fabric has **5 APIC Controllers**.

```
                 Cisco ACI Fabric

               +------------------+
               |      Spine       |
               +------------------+
                 /      |       \
                /       |        \
         +--------+ +--------+ +--------+
         | Leaf-1 | | Leaf-2 | | Leaf-3 |
         +--------+ +--------+ +--------+
             |          |          |

      APIC1    APIC2    APIC3    APIC4
        ✔        ✔        ✔        ✔

      APIC5
        ✖ Hardware Failure
```

Production traffic **does NOT stop** because APICs are **Management Plane Controllers**, not the Data Plane. The Leaf switches continue forwarding traffic using the policies already programmed into hardware.

---

# Step 1 - Verify Cluster Health

Before replacing any APIC, verify the cluster status.

```bash
show cluster extended-state
```

or

```bash
acidiag avread
```

Expected Output

```
Node1   UP
Node2   UP
Node3   UP
Node4   UP
Node5   DOWN
```

Also verify:

```bash
show controller
show fabric membership
show fault
```

Take both:

- Configuration Backup
- Snapshot Backup

---

# Step 2 - Remove Failed APIC

If the APIC is permanently damaged:

GUI

```
Fabric
 └── Inventory
      └── Fabric Membership
           └── Decommission Controller
```

If the APIC is still accessible:

```bash
acidiag touch clean
```

This removes the old controller information from the cluster.

---

# Step 3 - Rack the New APIC

Connect:

- Power
- Management Port
- Fabric Port (eth2)

Power ON the APIC.

---

# Step 4 - Install Correct ACI Version

Example

Existing Cluster

```
6.1(2h)
```

Replacement APIC

```
5.2(3)
```

Upgrade the replacement APIC first.

After upgrade

```
6.1(2h)
```

**Important**

Every APIC in the cluster **must run exactly the same ACI version.**

---

# Step 5 - Initial APIC Setup

Run the setup wizard.

Configure

- APIC Name
- Management IP
- Fabric Name
- TEP Information
- Admin Password

At this stage

**The APIC is NOT yet part of the cluster.**

---

# Step 6 - How Does Existing APIC Discover the New APIC?

This is the most common interview question.

## Physical Connection

```
New APIC
    │
    │ eth2
    ▼
 Leaf Switch
```

The APIC connects using its **Fabric Interface (eth2)**.

---

## Discovery Process

The new APIC advertises:

- Controller Identity
- Serial Number
- Software Version
- Fabric Name
- TEP Information

The Leaf switch detects the controller.

Then the Leaf advertises this information through the Fabric.

```
New APIC
      │
      ▼
 Leaf Switch
      │
      ▼
     Spine
      │
      ▼
 Existing APIC Cluster
```

The existing APIC Cluster now detects

```
Pending Controller
```

CLI

```bash
show controller
```

GUI

```
Fabric
 └── Inventory
      └── Fabric Membership
```

Example

```
Controller Found

Serial Number
FOX12345678

Status

Pending Registration
```

The APIC is discovered automatically through the Fabric.

---

# Step 7 - Administrator Approves the APIC

Assign

```
Node ID : 5
Pod ID  : 1
Name    : APIC5
```

After approval

The APIC joins the Cluster.

---

# Step 8 - What Happens Internally?

Immediately after approval

The APIC downloads the entire cluster database.

It automatically receives

- Tenants
- VRFs
- Bridge Domains
- EPGs
- Contracts
- Fabric Policies
- AAA Configuration
- Monitoring Policies
- Certificates

No manual configuration is required.

---

# Step 9 - Cluster Synchronization

Verify

```bash
show cluster extended-state
```

Expected

```
Node1 UP
Node2 UP
Node3 UP
Node4 UP
Node5 UP
```

Verify Database

```bash
acidiag avread
```

Expected

```
All replicas synchronized
```

Verify

```bash
show controller
show fabric membership
show fault
```

Cluster Status should become

```
Fully Fit
```

---

# Complete Flow

```
APIC Hardware Failure
          │
          ▼
Verify Cluster Health
          │
          ▼
Take Configuration Backup
          │
          ▼
Decommission Failed APIC
          │
          ▼
Rack New APIC
          │
          ▼
Install Same ACI Version
          │
          ▼
Run Initial Setup Wizard
          │
          ▼
Connect eth2 to Leaf
          │
          ▼
Leaf Detects New APIC
          │
          ▼
Leaf Advertises Controller Information
          │
          ▼
Existing APIC Detects Pending Controller
          │
          ▼
Administrator Approves Controller
          │
          ▼
Certificate Exchange
          │
          ▼
Database Synchronization
          │
          ▼
Policies Synchronization
          │
          ▼
Controller Joins Cluster
          │
          ▼
Cluster Status = Fully Fit
```

---

# Important Notes

✅ APIC is the Management Plane.

✅ Leaf switches are the Data Plane.

✅ One APIC failure does NOT stop traffic.

✅ Replacement APIC must run the same ACI version.

✅ APIC is discovered automatically through the Leaf switch.

✅ Administrator must approve the Pending Controller.

✅ After approval, the APIC automatically downloads the entire cluster database.

✅ No manual tenant configuration is required.

---

# Interview Questions

### Q1. Does traffic stop if one APIC fails?

**Answer:**

No. APIC manages the control and policy plane. The Leaf switches continue forwarding traffic using the policies already programmed into hardware.

---

### Q2. Why must the replacement APIC run the same ACI version?

**Answer:**

The APIC cluster synchronizes databases only between compatible software versions. A different version cannot join the cluster.

---

### Q3. How does the existing APIC know about the new APIC?

**Answer:**

The new APIC connects its fabric interface (eth2) to a Leaf switch. The Leaf discovers the controller and advertises its identity (serial number, software version, fabric name, and TEP information) throughout the ACI fabric. The existing APIC cluster detects it as a **Pending Controller**, and after administrator approval, it joins the cluster through certificate exchange and database synchronization.

---

### Q4. What configuration is synchronized?

**Answer:**

The new APIC automatically receives the complete cluster database, including tenants, VRFs, bridge domains, EPGs, contracts, policies, AAA configuration, monitoring settings, and certificates.

---

## One-Line Summary

**APIC Replacement Flow: Verify → Backup → Decommission → Rack → Upgrade → Initial Setup → Connect eth2 → Leaf Discovers APIC → Pending Controller → Approve → Database Sync → Fully Fit.**
