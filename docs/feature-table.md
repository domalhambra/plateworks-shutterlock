# Feature table: A7S III 5.01

What the A7S III 5.01 application firmware carries for each target feature, and what gates it. Read from the decrypted update file only. Nothing here was run on a camera.

**Status:** Task 1 of the plan, first pass, 2026-10-14. Name-level evidence: generated name records, availability rules and backup properties. Instruction-level traces are not done yet; each row says where one is still needed.

## The two images

| Image | Version | Model code | Crypter | `appFw.so` built | Product classes |
|---|---|---|---|---|---|
| A7S III (ILCE-7SM3) | 5.01 | `0x91030083` | `CXD90057_k8` | 2026-04-15 | 27 |
| FX3 (ILME-FX3) | 7.02 | `0x91030080` | `CXD90057_k8` | 2025-12-24 | 9 |

Both run the `MIRA_CETUS2` platform. The A7S III build is newer and larger. It is the shared application that also carries the A7 IV's classes.

## Which class each camera is

| Camera | Class | Evidence |
|---|---|---|
| A7S III | `OT_UUD` | The update's backup profiles sit in the folders OT and OT_HIBIKI2 under SYSIPSX-DSLR, and `MainDest.txt` names `OT`. The feature manager gives `OT_UUD` the IMX510 sensor, a mode dial, product concept `OTHER`, and two CFexpress slots. |
| FX3 | `ALCIN_UUD` | The FX3 update's backup profiles sit in `ALCIN` and `ALCIN_HIBIKI2`. `ALCIN_UUD` is the IMX510 class with product concept `CINEMA_LINE`. |

The two cameras share a sensor but not a class. The A7S III also has its own menu catalogue, `TYPE_MENU_ITEM_OT_UUD`, separate from the FX3's. A whole menu page can be absent on the A7S III even when the code behind it is present.

## Classes used in this table

| Class | Meaning |
|---|---|
| Flag-gated candidate | The setting and its code are present, and no availability rule names a product class. A settings write may be enough. Not proven on an A7S III. |
| Code-gated, probable | An availability rule names the FX3 class and not the A7S III class. Only a code patch would open it. |
| Not separated | The rule that could gate it treats the A7S III class and the FX3 class the same way. |
| Unknown | The evidence does not decide it. The row names the trace still needed. |

## The table

| Feature | A7S III 5.01 | FX3 7.02 | Class | Evidence |
|---|---|---|---|---|
| Shutter angle display | Present. Mode property `0x02cf15a5`, angle property `0x02cf15a7`. | Present. | Flag-gated candidate | No availability rule names a class for `setting_mode_shutter`; the only shutter rule with a class value concerns the silent-mode curtain. Name records: `BKID_APP_SETTING_MODE_SHUTTER` at file offset `0x4e49322`, `BKID_APP_SETTING_SHUTTER_ANGLE` at `0x4e493ac`. On the A7 IV, the same pattern showed the angle icon after one settings write. |
| Shutter angle control by dial | Present. Angle drive and next and previous actions at `0x4bdc59d`, `0x4bdc5bb`, `0x4bdc5d7`. | Present. | Unknown | The A7 IV's dial guard, `PRM_HAITA_setting_shutter_speed_a_m_switching_exposure_mode` (A7S III offset `0x4bc2dd5`), has the same five values in both binaries and names no class. On the A7 IV it is runtime state and blocked the dial. Needs an instruction-level trace of how that state is set for `OT_UUD` against `ALCIN_UUD`. |
| Shutter Mode menu item | Shutter UI keys present, including `UIBIZ_KEY_TACT_SHUTTER_MODE` at `0x4f0b359`. | Present and shown. | Unknown | The per-class menu catalogue most likely decides it, as on the A7 IV. Needs a trace of the `OT_UUD` catalogue. |
| Log Shooting, Flexible ISO | Present. Property `0x02cf1020`. | Present and shown. | Not separated | `PRM_HAITA_FEATURE_MANAGER_model_diff_setting_log_shooting` (offset `0x4bbbcfe`) lists `LOG_SHOOTING_OFF` and `LOG_SHOOTING_FLEXIBLE_ISO` in Movie mode for `OT_UUD` (`0x4cbcae8`) exactly as for `ALCIN_UUD` (`0x4cbc576`). The A7 IV class is not on that list. Check the camera's own Movie menu for Log Shooting Setting before anything else. |
| Cine EI and Cine EI Quick | Present. Rule values `LOG_SHOOTING_CINE_EI` and `_QUICK` at `0x4daeaa3`. Base ISO properties `0x02cf1026`, `0x02cf1028`, `0x02cf102a` to `0x02cf102e`, `0x02cf10f9`, `0x02cf10fb`. | Present and shown. | Code-gated, probable | Cine EI uses base sensitivity selection. `PRM_HAITA_FEATURE_MANAGER_menu_item_setting_base_sensitivity_general_shooting_mode` (offset `0x4bbbcab`) names the menu catalogues of `ALCIN_UUD` (`0x4cbc11a`), `LAX` and `DOC`. It does not name `OT_UUD`. No Cine EI value names any class directly. |
| User LUT selection | Present. Select LUT `0x02cf1030`, Apply LUT `0x02cf1033`, Embed LUT `0x02cf1024`. | Present and shown. | Unknown | Apply LUT depends on Log Shooting and Movie mode. It follows the Log Shooting row. |
| De-squeeze display | Present. Property `0x02cf1771`, plus extern properties `0x02cf1198` and `0x02cf1774`. | Present. | Flag-gated candidate | Both de-squeeze rules depend only on Movie mode and recording state, with 1.3x and 2.0x values. No class is named. The A7 IV repo graded the same path "strong candidate, untested". |

## What the table does not say

- A flag-gated candidate is not a working feature. The A7 IV shows that a settings write can change the display while the controls stay blocked.
- Offsets are file offsets of name strings in `appFw.so`, found by searching the decrypted file. They locate the evidence. They are not patch addresses.
- The FX3's property IDs are not listed. The property script resolved no records in the FX3 binary, so its backup layout is unread.

## Evidence files

These hold identifiers only: class names, rule labels, property IDs and per-class values. They contain no firmware bytes, keys, or camera data. The A7 IV repo publishes the same kind of file.

| File | What it is |
|---|---|
| `evidence/feature-manager-a7s3-501.json` | The A7S III feature-manager matrix for every class, from `feature_manager_matrix.py`. |
| `evidence/feature-manager-fx3-702.json` | The same for the FX3 7.02, for the `LS`, `LAX` and `ALCIN_UUD` classes. |
| `evidence/properties-a7s3-501.txt` | The A7S III backup property IDs for shutter mode, shutter angle, Log Shooting, the LUT settings and de-squeeze, from `bkid_inventory.py`. |

## Next traces

1. The dial guard: how `setting_shutter_speed_a_m_switching_exposure_mode` is set for `OT_UUD`. This needs Ghidra.
2. The `OT_UUD` menu catalogue: whether it holds the Shutter Mode and Log Shooting nodes.
3. The FX3 backup layout, so the two cameras' property IDs can be compared.
