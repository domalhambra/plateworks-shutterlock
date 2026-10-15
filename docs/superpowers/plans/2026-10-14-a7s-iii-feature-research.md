# A7S III feature research: shutter angle and Cine EI

**Date:** 2026-10-14.
**Asked by Dom (2026-10-14):** a Reddit thread showed shutter angle switched on in an A7 IV by reverse engineering. Plan a similar path for the A7S III.
**Decisions (Dom, 2026-10-14):** the camera is Dom's only A7S III, on firmware 5.01. The first milestone is working shutter angle and Cine EI. Dom said this before the gate below was explained; the gate stands.
**Drift:** this work is not on the Now page, and the Now page is 18 days old (updated 2026-09-26, review 2026-10-21). Dom made it a Plateworks Tools property on 2026-10-14: Plateworks ShutterLock, folder 29, repo `plateworks-shutterlock`, public. This copy of the plan governs; the Plateworks OS copy is history.
**Register:** `ste-writing-standard.md`.

## Terms

- **Service mode** is Sony's USB maintenance mode. Sony-PMCA-RE's `serviceshell` enters it and authenticates.
- **Settings backup** is the camera's table of numbered properties. `bk r` reads one, `bk w` writes one, `bk s` saves.
- **Update file** is Sony's `BODYDATA.DAT`, the encrypted firmware image inside the 5.01 download.
- **Product class** is the model gate inside the firmware. The A7 IV runs as `PRODUCT_MODEL_LS`.
- **Flag-gated** means a feature switches on from a settings-backup byte alone, with no product check in code.
- **Code-gated** means a product check in the firmware blocks the feature. Only a code patch opens it.
- **Serial boot door** is the bootrom's serial boot mode. It is documented for the A7 IV only.
- **Only-camera rule:** nothing writes to the camera until the gate in Task 3 passes for that feature.

## What is known (2026-10-14)

| Fact | Source |
|---|---|
| BIONZ XR update files decrypt. `ILCE-7SM3` and `ILME-FX3` are on the tested list. | [fwtool.py PR #52](https://github.com/ma1co/fwtool.py/pull/52) |
| The A7 IV has a serial boot door, a fastboot payload, and eMMC dump and restore. No signature check on partitions. `boot0` is not covered. | [ILCE-7M4-RE](https://github.com/DavidBuchanan314/ILCE-7M4-RE) |
| On the A7 IV, one settings byte shows the shutter-angle icon. The dial does not reach the angle setter. LUT persists but never appears. | [sony-a7iv-feature-research](https://github.com/leogenot/sony-a7iv-feature-research) |
| Sony-PMCA-RE's page says BIONZ XR is unsupported. `serviceshell` still works on the A7 IV 6.02. | [Sony-PMCA-RE](https://github.com/ma1co/Sony-PMCA-RE) |
| Nobody has run any of this on an A7S III. | The Reddit thread, 2026-10-12 to 2026-10-14 |

## The gate, stated first

On the A7 IV, shutter-angle display is flag-gated. Angle control and the LUT path are code-gated. Cine EI is an FX3 feature, so it is most likely code-gated or absent on the A7S III.

A code-gated feature needs a patched partition flashed to the eMMC. The only recovery from a bad flash is the serial boot door, which nobody has opened on an A7S III. So on Dom's only camera, code-gated features stop at Task 3. The way past the gate is a second body or a proven A7S III recovery. That is Dom's decision at the gate, not before.

Expected outcome, stated now so it is not a surprise: shutter-angle display on this camera, and a published map that moves the community toward the rest.

## Tasks

### 0. Setup, no camera

1. The project folder is `20-29 Properties/29 Plateworks ShutterLock/`, chartered 2026-10-14. Tool clones, update files and decrypted trees go under its gitignored `tools/`, `firmware/` and `decrypted/`.
2. Create a Python virtual environment.
3. Clone `fwtool.py` and check out the PR #52 branch.
4. Clone `Sony-PMCA-RE`. Install `libusb` with Homebrew.
5. Download the A7S III 5.01 update from Sony. Download the current FX3 update too (7.02 on 2026-10-14). Both are a `BODYDATA.DAT`; Sony's pages refuse plain fetches, so read the link in a browser.
6. Decrypt and unpack both with `fwtool.py`.

**Verify:** `fwtool.py` lists the filesystem of both images. Both decrypted trees are outside the repo and outside git.

### 1. Offline firmware analysis, no camera

This task answers the decisive question: is each feature present in the A7S III binary, and how is it gated?

1. Search both decrypted trees for the shutter-angle and Cine EI strings and the code that references them.
2. Find the product-class checks around each feature. Record the A7S III class name.
3. Diff the FX3 and A7S III application firmware around each feature.
4. Run `feature_manager_matrix.py` from the A7 IV repo against the A7S III tree. Adapt it where the layout differs.
5. Write the result as a table: feature, present in A7S III, present in FX3, gate type, evidence address.

**Verify:** the table has an evidence address for every row. A row with no address is "unknown", not "absent".

Ghidra is the disassembler. Claude Code can drive it headless; the strings pass and the diff are Python.

### 2. Read-only camera inventory

This is the first time the camera is touched. The A7 IV is the only precedent for `serviceshell` on BIONZ XR.

1. Fully charge the battery. Set USB connection mode to Mass Storage.
2. Run `serviceshell` from `Sony-PMCA-RE`. If it times out while the camera changes modes, run it again.
3. Save the full settings backup. Save it twice. Compare the hashes.
4. Store the backup and hashes outside the repo.
5. Run `bkid_inventory.py` on the backup.
6. Map the A7 IV's four property IDs to their A7S III equivalents by value pattern and by the Task 1 evidence.
7. Exit. Power off. Remove the battery for ten seconds. Boot in Movie mode.

**Verify:** both backup hashes match. The camera boots and records a clip after the session. The inventory names a candidate ID for shutter-angle mode, angle value, de-squeeze, and LUT.

If `serviceshell` fails to authenticate, stop. Report the error to the A7 IV repo's issues. The project waits here.

### 3. Gate

Classify each feature from Tasks 1 and 2:

| Class | Meaning | Allowed on this camera |
|---|---|---|
| Flag-gated | A settings byte alone opens it; no product check in code | Yes, one write, with the restore procedure |
| Code-gated | A product check blocks it | No. Stop. |
| Absent | Not in the A7S III binary | No. Nothing to open. |

Dom decides at the gate, with the table in hand:

- Stop here, publish the map, and watch the community.
- Buy a used A7S III body for the code-patch path.
- Wait for a proven A7S III serial boot door and eMMC backup.

Benefit of stopping: the map is the contribution nobody has made, at zero risk. Cost: no Cine EI. Risk of pushing on with this camera: a brick with no recovery on the body that shoots the prints.

### 4. Flag writes, this camera, flag-gated features only

Run for each flag-gated feature, one at a time.

1. Read the property. Record the original value.
2. Write the new value. Save with `bk s`. Exit.
3. Power off. Disconnect USB. Remove the battery for ten seconds. Boot in Movie mode.
4. Test the feature. Record the result with a screenshot of the display.
5. Restore the original value by the same steps before the next feature.

**Verify:** each feature has a before value, an after value, a screenshot, and a restore confirmed by `bk r`.

### 5. Code-patch path, not this camera

Runs only after the gate opens with a second body or a proven recovery. It gets its own plan then. Its shape:

1. Build the RP2040 serial adapter from the A7 IV repo. Find the A7S III accessory-connector pins.
2. Open the serial boot door. Load the fastboot payload.
3. Dump every eMMC partition. Store two copies.
4. Patch the product check found in Task 1. Flash the one partition.
5. Test. Restore from the dump on any failure.

### 6. Publish and log

1. Publish the Task 1 table and the Task 2 inventory as an A7S III repo, on the A7 IV repo's model. No firmware, keys, backups, or serial numbers in it.
2. Post the link in the A7 IV repo's issues and the Reddit thread.
3. Session Log entry.

**Verify:** the repo's README has the same warning as the A7 IV repo. `git status` shows no decrypted tree.

## Risks

| Risk | Where | Odds | Mitigation |
|---|---|---|---|
| `serviceshell` leaves the camera in service mode | Task 2 | Low; A7 IV precedent | Battery pull restores normal boot on the A7 IV |
| A write lands on the wrong property | Task 4 | Low with the Task 1 evidence | Original value recorded first; restore before the next write |
| Brick with no recovery | Task 5 | Real | Task 5 never runs on this camera |
| Warranty and Sony's terms | All camera tasks | Certain | Dom's call; it is his device |
| The A7 IV docs were agent-written and carry an error | Tasks 2 and 4 | Medium | Check every command against the Sony-PMCA-RE source first |

## Open questions

- Does `serviceshell` authenticate on an A7S III at all?
- Is Cine EI code in the A7S III 5.01 binary, or only in the FX3's?
- Which A7S III connector carries the bootrom's serial RX pin?
