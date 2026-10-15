# Agents

Read `CLAUDE.md` first; it is the router. The index of every document is
`docs/README.md`.

- Test: `python3 scripts/docs_check.py --require docs/decisions.md --known-absent tools/ --known-absent firmware/ --known-absent decrypted/ --known-absent backups/`
- There is no build. The outputs are Markdown tables and Python scripts.

Two rules an agent breaks most easily:

1. Nothing writes to the camera. Every camera task in the plan is read-only
   until Dom passes the gate in Task 3, and he passes it, not you.
2. No Sony material enters the repo: no firmware, decrypted tree, key,
   backup, or serial number. `.gitignore` names the paths; do not loosen it.
