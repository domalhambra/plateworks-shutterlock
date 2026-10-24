# Documentation layout: Plateworks ShutterLock

The layout this repo's documents follow, set when the repo rolled to the
documentation standard on 2026-10-23. The standard itself is
`00_Resources/documentation-standard.md` in Plateworks OS.

## Tier

Floor, plus `PROJECT_CHARTER.md`, which the project was chartered with on
2026-10-14 and keeps. This repo is a research register: Markdown tables,
evidence files and one vendored check. It has no source modules to
describe, so it carries no `docs/architecture.md` and no
`docs/changing-things.md`; the plan carries the procedures. It has no
runbooks and no handoffs folder.

## The files

| File | Job |
|---|---|
| `README.md` | The front door: what this is, Run it, the check command, where to go next. |
| `CLAUDE.md` | The router: the invariants and the one document to read for each change. Under 2,000 tokens. |
| `AGENTS.md` | The two rules an agent breaks most easily, and the test command. |
| `PROJECT_CHARTER.md` | The charter: what the project is, the gate, the milestones. |
| `docs/README.md` | The index of everything under `docs/`. |
| `docs/decisions.md` | Dated decisions, newest last. |
| `docs/feature-table.md` | The project's main output: the feature map with its evidence addresses. |
| `docs/superpowers/plans/` | The governing plan. |
| `docs/superpowers/specs/` | Design specs. This file lives here. |
| `evidence/` | The published evidence files: identifiers only, no firmware bytes. |
| `scripts/docs_check.py` | The vendored check. |

## The check

The check is the repo's test run; there is no build and no CI. The command
stands in `README.md`, `CLAUDE.md` and `AGENTS.md` in the same words:

```bash
python3 scripts/docs_check.py --require docs/decisions.md --known-absent tools/ --known-absent firmware/ --known-absent decrypted/ --known-absent backups/
```

The known-absent prefixes are the gitignored working folders. Run it
rebuilds `tools/`, `firmware/` and `decrypted/`; `backups/` holds the
camera settings backups from the plan's Task 2 procedure. Nothing under
them is in git; that is the repo's second invariant, and the check
enforces the README's claim about it.

`scripts/docs_check.py` is a vendored copy of the canonical script in
Plateworks OS, `00-09 System/01 Docs/scripts/docs_check.py`, re-vendored
at this roll (sha256 begins `23c43f0657923d6f`). The copy is recorded in
`00-09 System/00 Resources/VENDORED.md`. Re-vendor by copying the file;
never edit the copy.

## Parked

The project parked on 2026-10-15 (`docs/decisions.md`). The layout holds
while parked: a change is shipped when it is pushed to `main` with its
docs, and the check runs before any claim of done.
