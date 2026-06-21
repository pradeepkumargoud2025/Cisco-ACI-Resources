# 🔄 Cisco ACI Leaf-to-Spine Conversion Procedure

> **Purpose:** Convert an existing Cisco ACI switch to operate as a Spine node within the fabric.

---

## 📋 Pre-Conversion Verification

### Verify Current Node Status

```bash
moquery -c topSystem
```

### Generate Debug Token (if required)

```bash
acidiag dbgtoken
```

Example Output:

```text
MEUCIQCk/K5Vt24sWaBDtU9kJsAD5huVBJkty65HnZlIsdL9MgIgNF1T3uN/r/xOnry+FdCeA0QEgqWYlebRF6LvcC6YvLM==
```

---

# Step 1: Remove Existing Registration

If the switch is already registered in the ACI Fabric:

1. Navigate to the APIC GUI.
2. Remove the switch from the fabric inventory.
3. Wait until the node is completely removed from the controller.

---

# Step 2: Clean the Existing Configuration

From the switch CLI:

```bash
setup-clean-config.sh
```

Allow the process to complete successfully.

Reload the switch:

```bash
reload
```

---

# Step 3: Verify Pending Registration State

After reboot, verify that the switch reaches the following prompt:

```text
(none)#
```

The switch should now appear under:

```text
Fabric → Inventory → Nodes Pending Registration
```

Do not proceed until the switch is visible in the pending registration list.

---

# Step 4: Gain Root Access

Connect to the switch using root credentials:

```bash
ssh root@0
```

Enter the root password when prompted.

---

# Step 5: Remove Immutable Attribute

Remove the read-only attribute from the card mode configuration file:

```bash
chattr -i /bootflash/crdcfg_card_mode.cfg
```

---

# Step 6: Verify Current File Attributes

Check the current file attributes:

```bash
lsattr /bootflash/crdcfg_card_mode.cfg
```

---

# Step 7: Change Card Mode

Update the configuration value:

```bash
echo 0 > /bootflash/crdcfg_card_mode.cfg
```

---

# Step 8: Verify the Updated Value

Confirm the file now contains the correct value:

```bash
cat /bootflash/crdcfg_card_mode.cfg
```

Expected Output:

```text
0
```

---

# Step 9: Restore Immutable Attribute

Re-apply the read-only protection:

```bash
chattr +i /bootflash/crdcfg_card_mode.cfg
```

Exit root shell:

```bash
exit
```

---

# Step 10: Clean Configuration Again

Run the cleanup process one more time:

```bash
setup-clean-config.sh
```

Reload the switch:

```bash
reload
```

---

# Step 11: Verify Node Status

After the switch boots up, verify its state:

```bash
moquery -c topSystem
```

Ensure the switch appears healthy and ready for registration.

---

# Step 12: Register as Spine Node

From the APIC GUI:

```text
Fabric → Inventory → Nodes Pending Registration
```

1. Select the switch.
2. Click Register.
3. Assign the appropriate Node ID.
4. Select the Role:

```text
Spine
```

5. Complete the registration process.

---

# ✅ Post-Conversion Validation

Verify:

* Node status is Active
* Node role is Spine
* Fabric membership is healthy
* IS-IS adjacency is established
* Switch appears under Fabric Inventory
* No critical faults are present

---

# ⚠️ Important Notes

* Ensure the node is completely removed from APIC before starting the conversion.
* Do not modify any commands in this procedure.
* Wait for each reload and cleanup operation to complete before proceeding.
* Always verify the node appears in Pending Registration before attempting registration as a Spine.
