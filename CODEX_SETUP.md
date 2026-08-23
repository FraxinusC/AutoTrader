# Running FriesTrader with Codex and GitHub

This fork is configured to run the two trading phases through the Codex GitHub
Action. The action runs Codex in GitHub Actions, reads the committed prompt
files, and lets Codex follow `PHASE_A_TASK.md` or `PHASE_B_TASK.md`.

## 1. Configure repository secrets

In GitHub, open this repository's settings and add these Actions secrets:

- `OPENAI_API_KEY`: required by `openai/codex-action@v1`.

The trading workflow also needs a Robinhood Agentic Trading MCP connection that
Codex can access in the runner. This repository does not include Robinhood
credentials or MCP configuration. Add that connection through your Codex/GitHub
environment setup before enabling live trading.

## 2. Fill in trading settings

Edit `risk_rules.json` by hand before running either phase:

- `account_number`
- `starting_capital_usd`
- `universe.watchlist_name`
- `universe.supplementary_scan_id`
- `wash_sale_avoidance.linked_accounts`

Keep `execution.mode` as `"dry_run"` until you have reviewed at least
`execution.dry_run_min_cycles_before_live` successful Phase B dry-run cycles.

## 3. Enable workflows

The workflows are in `.github/workflows/`:

- `codex-phase-a.yml`: screens candidates and writes
  `pending_proposals.jsonl`.
- `codex-phase-b.yml`: enforces risk rules, logs decisions, and writes
  `trade_log.jsonl` plus `trade_log_recent.md`.

Both workflows support manual `workflow_dispatch`. They also include weekday
schedules for Central Time with daylight/standard-time guards:

- Phase A: around 4:30pm America/Chicago on weekdays.
- Phase B: around 8:35am America/Chicago on weekdays.

GitHub scheduled workflows can start late. The prompts require Codex to record
the real America/Chicago date and time from the runner instead of guessing.

## 4. Required GitHub permissions

The workflows request `contents: write` so Codex can commit and push the JSONL
state files back to `main`. Do not change this to broad repository permissions
unless a future workflow genuinely requires them.

## 5. Before live trading

Run multiple manual dry-run cycles first. Confirm:

- Phase A creates plausible `pending_proposals.jsonl` entries.
- Phase B appends one `cycle_summary` per run.
- `trade_log_recent.md` matches the JSONL audit trail.
- No placeholder values remain in `risk_rules.json`.
- The Robinhood MCP tools are available to Codex.

Only then should you manually change `execution.mode` to `"live"`.
