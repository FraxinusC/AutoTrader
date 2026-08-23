# AGENTS.md

## Project Overview

This repository runs a two-phase autonomous trading workflow against a
Robinhood Agentic Trading MCP connection:

- Phase A reads `PHASE_A_TASK.md`, screens candidates, and overwrites
  `pending_proposals.jsonl`.
- Phase B reads `PHASE_B_TASK.md`, enforces deterministic risk rules, appends
  `trade_log.jsonl`, and regenerates `trade_log_recent.md`.

The Python scripts in `scripts/` are the source of truth for mechanical risk
math. Use their JSON output directly. Do not reimplement their calculations in
prose or with ad hoc arithmetic.

## Safety Rules

- Never change `execution.mode` or any value in `risk_rules.json` unless the
  human explicitly asks for that exact edit.
- Never place or preview orders in Phase A.
- In Phase B, never call a live order placement tool unless every live-order
  gate in `PHASE_B_TASK.md` is satisfied at that moment.
- If `risk_rules.json` still contains placeholder account, watchlist, scan, or
  linked-account values, stop and report the missing configuration instead of
  trading.
- If the Robinhood MCP tools are unavailable, stop and report the missing MCP
  connection. Do not fabricate market, account, portfolio, position, order, P&L,
  or fill data.
- Treat `trade_log.jsonl` as append-only audit state. Do not rewrite or delete
  existing lines unless the human explicitly asks for a repair.
- Treat `pending_proposals.jsonl` as Phase A's replaceable daily output. Phase B
  reads it but must not modify it.

## Required Validation

After editing Python scripts, run:

```bash
python3 -m py_compile scripts/*.py
```

If the shell only has `python`, use:

```bash
python -m py_compile scripts/*.py
```

After editing GitHub workflow files, inspect the YAML for syntax-sensitive
indentation and keep workflow permissions as narrow as the task permits.

## Git Workflow

- Stage only files relevant to the requested change.
- Do not force-push.
- If a scheduled Phase A or Phase B run cannot push because the remote changed,
  run `git pull --rebase origin main` once, retry the push once, and report the
  exact failure if it still does not push.
