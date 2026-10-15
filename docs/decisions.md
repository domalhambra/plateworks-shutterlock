# Decisions

Dated sections, newest last. Each row: the decision, why, and the source. A reversed decision stays in place, marked superseded, and the new one goes under its own date.

## 2026-10-14

| Decision | Why | Source |
|---|---|---|
| The project exists, as a Plateworks Tools property. | A Reddit thread on 2026-10-12 showed shutter angle switched on in an A7 IV. Nobody had run any of the published work on an A7S III. The gap is the map, and a published feature map fits the Tools arm. | Dom, in session, 2026-10-14. |
| The name is Plateworks ShutterLock. Folder 29, repo `plateworks-shutterlock`, public. | Dom's choice from a set of three. Number 29 was registered for Plateworks Payment Links for one commit on 2026-10-13 and withdrawn before any folder existed; no document cited a folder 29, so the number was free under the never-reuse rule. | Dom, in session; plateworks-os commits 5e39c51 and 61fae0e. |
| The register is a research notebook. No accent. No privacy page. | The output is evidence, procedures and a table, not a product. There is no interface to color and no page that loads or stores anything; GitHub's own policy covers the repo page. | `00_Resources/plateworks-principles.md` §Property registers; `00_Resources/color-standards.md` §5. |
| The gate: nothing writes to the camera until a feature is shown to be flag-gated and the restore procedure is written. Code-gated features stop. | The camera is Dom's only A7S III and shoots the prints. A code patch needs a flashed partition, and the only recovery is the serial boot door, which nobody has opened on an A7S III. | The plan, §The gate; Dom's answers in session (only camera, firmware 5.01). |
| The comparison image is the FX3 firmware 7.02, not 6.00. | Sony's support page lists 7.02 (released 2026-03-17) as current. A web search had reported 6.00; the page is the authority. | Sony Asia Pacific support page for ILME-FX3, read 2026-10-14. |
| Tool clones, update files, decrypted trees and backups live inside the repo folder under gitignored paths. | One folder per project, per the workspace's working-files rule, with the Sony material kept out of git by path rather than by care. | `.gitignore`; workspace `CLAUDE.md` working-files rule. |
| Flashing is out of scope for this repo on this camera. | fwtool.py raises "Encryption not supported" for the BIONZ XR cipher, so a repacked update is not even possible with the tool. The serial boot door is documented for the A7 IV only. | `tools/fwtool.py/fwtool/sony/fdat.py`, class AesCbcCrypter, method encrypt. |
| The A7S III is analysed as product class `OT_UUD`, not the FX3's `ALCIN_UUD`. | The update's backup profiles are in `OT` and `OT_HIBIKI2`, and the feature manager gives `OT_UUD` the IMX510 sensor and a mode dial. The FX3's profiles are in `ALCIN`. | `docs/feature-table.md`, Which class each camera is. |
| Cine EI is classed code-gated, probable. | The base-sensitivity menu rule names the FX3's catalogue and not the A7S III's. No settings write can add a class to a rule. | `docs/feature-table.md`, Cine EI row. |
| The evidence files are published. | They hold class names, rule labels and property IDs only, with no firmware bytes. The A7 IV repo publishes the same kind of file. Dom approved it in session. | `evidence/`; Dom, in session, 2026-10-14. |

## Rejected and deferred

| Item | Status | Why |
|---|---|---|
| Internal RAW, open gate | Rejected as targets | Not switches. They need a recording pipeline the firmware may not carry. Treat as fantasy until someone shows a file. |
| FX6 features (NR off, waveform, false color) | Deferred | The FX6 is a different firmware line. A feature absent from the A7S III binary cannot be flipped. The diff in Task 1 will say. |
| A second A7S III body | Deferred to the gate | Dom decides at Task 3 with the feature table in hand, not before. |
