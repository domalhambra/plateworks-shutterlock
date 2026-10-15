# Plateworks ShutterLock

A map of what the Sony A7S III firmware carries and how each feature is gated: present or absent, flag-gated or code-gated, with an evidence address per row. Read from the decrypted 5.01 update file, diffed against the FX3's, and from the camera's settings table over USB service mode. Python scripts in `scripts/`, Markdown tables in `docs/`. The upstream tools are cloned into the gitignored `tools/`. It serves A7S III owners who want to know what is possible before touching their camera. The human entry point is `PROJECT_CHARTER.md`.

This file is a router: the rules that must not break, and the one document to read for each change. Everything else lives in `docs/`, indexed at `docs/README.md`.

## Read this before changing that

| Changing | Read first |
|---|---|
| Anything, for the first time | `README.md`, then the plan |
| The plan, the tasks, the gate | `docs/superpowers/plans/2026-10-14-a7s-iii-feature-research.md` |
| A feature-table row | `docs/feature-table.md`, then `docs/decisions.md` for the evidence rule |
| Anything that touches the camera | The plan's Tasks 2 to 4, in full, before the USB cable |
| Something that looks like a past decision | `docs/decisions.md` |

New specs are written into `docs/superpowers/specs/` and added to `docs/README.md`.

## Invariants

1. **Nothing writes to the camera until the gate passes.** The camera is Dom's only A7S III and it shoots the prints. A write is allowed only for a feature shown to be flag-gated, one property at a time, with the restore procedure written first and a restore confirmed by a read before the next write. Code-gated features stop. Dom passes the gate, not a session.
2. **No Sony material in the repo.** Firmware, decrypted trees, keys, camera backups, serial numbers. `.gitignore` names the paths: firmware, decrypted, backups, tools, every DAT and bin file. Never loosen it. The README's claim depends on it.
3. **Evidence over assertion.** A feature-table row carries an address in the decrypted tree or a diff hunk. No address means "unknown", never "absent".
4. **A settings backup is saved twice and the hashes match** before any read from it is trusted.
5. **Credit upstream.** Every method here came from DavidBuchanan314, ma1co and leogenot. Their repos stay named at the top of `README.md`.
6. **Nothing is flashed.** fwtool.py cannot re-encrypt BIONZ XR updates, and the serial boot door is documented only for the A7 IV. Flashing is Task 5, which never runs on this camera.

## Working here

- `python3 scripts/docs_check.py --require docs/decisions.md --known-absent tools/ --known-absent firmware/ --known-absent decrypted/ --known-absent backups/` before any claim of done. It holds this file under 2,000 tokens.
- The fwtool.py clone must be on the pull request 52 branch (`pr52`); `master` has no BIONZ XR keys and fails with "No decrypter found".
- The update files are 743 MB and 812 MB. Keep each in its own folder named for the camera and version.
- Sony's support pages refuse plain fetches; the download links come from the page in a browser. The files themselves are served from `di.update.sony.net`.
- `Sony-PMCA-RE` needs `sudo` to switch the camera into service mode. Read its source for every command before running one against the camera; the A7 IV repo's docs were agent-written.

## Session logging

Log every session to the repo `plateworks-session-log`. On Dom's Mac it is checked out at `00-09 System/04 Session Log/` in the workspace; elsewhere, clone it beside this one. Its convention file, CONVENTION.md, is self-contained. If the repo is unreachable, append the entry to `SESSION_LOG.md` here under a heading saying it belongs in the log repo, and say the push is pending.
