🔧 Cisco ACI – Switch Replacement & Upgrade Precautions (MW Checklist)

This document describes the precautions and best practices to be followed during a Maintenance Window (MW) when replacing a faulty switch and commissioning a new switch in Cisco ACI (APIC).

📌 Objective

* Safely replace a faulty Leaf / Spine switch
* Avoid fabric inconsistency or traffic impact
* Ensure smooth decommission → replacement → commissioning
* Maintain fabric health and application availability

🛑 Pre-Maintenance Window (Pre-MW) Precautions

✅ APIC Cluster Health

* Ensure all APIC nodes are UP & fully fit
* No APIC should be in failed or disconnected state

# acidiag avread

✅ Fabric Health Check

* Verify Fabric Health Score
* Check for existing critical or major faults
* Clear old faults before MW if possible

# show fabric membership

# show system internal fault info

✅ Identify Faulty Switch

Confirm:
* Node ID
* Serial Number
* Switch Role (Leaf / Spine)

Validate switch status in APIC

✅ Backup & Documentation

Take full APIC backup
* Configuration
* Tech Support bundle

Capture:
* Interface policies
* vPC details
* EPG bindings

📊 Impact Analysis

Check if the switch is:
 * Dual-homed (vPC)
 * Single-homed (higher risk)

Confirm redundancy availability
Notify application / business teams if required

⏱️ Maintenance Window Execution (MW)
🔹 Step 1: Graceful Decommission

Never power off directly

Decommission the faulty switch from APIC:
 * Fabric → Inventory → Fabric Membership → Decommission

Why this is important:

* Prevents stale Node ID
* Avoids fabric inconsistency
* Ensures clean re-commissioning

🔹 Step 2: Physical Replacement

Replace with:
 * Same switch model
 * Same role (Leaf / Spine)

Verify:
* Power supplies
* Fan modules
* Optics and cables

🔹 Step 3: Commission New Switch

Assign:
* Correct Node ID
* Correct Switch Role

Commission via APIC GUI
Wait for switch state = Active

🔍 Post-Maintenance Validation

✅ Fabric Validation
show fabric membership
show system internal fault info

Confirm:
* Switch status is Active
* No critical or major faults

✅ Interface & Policy Checks

Verify:
* Interface policy application
* vPC consistency
* Port-channels are UP

Ensure correct EPG bindings

✅ Traffic Verification

Validate:
 * Endpoint learning
 * Application reachability
No packet drops or errors

🔄 Rollback Plan (Mandatory)

* Keep faulty switch isolated but available
* Backup ready for restore
* If new switch fails:
   * Decommission new node
   * Reinsert old switch (if hardware allows)

🚫 Common Mistakes to Avoid

❌ Powering off switch without decommission
❌ Assigning wrong Node ID
❌ Model or port-count mismatch
❌ Ignoring pre-existing fabric faults
❌ Skipping post-MW traffic validation

📝 One-Line Summary (Interview Ready)

Always validate APIC and fabric health, take backups, gracefully decommission the faulty switch, replace and commission the new switch with the correct Node ID and role, 
and perform full post-MW verification to ensure fabric stability.

## Architecture Diagrams

Faulty Switch
     |
     v
[ Decommission from APIC ]
     |
     v
[ Physical Replacement ]
     |
     v
[ New Switch Discovered ]
     |
     v
[ Assign Node ID & Role ]
     |
     v
[ Commission via APIC ]
     |
     v
[ Validate Fabric & Traffic ]
