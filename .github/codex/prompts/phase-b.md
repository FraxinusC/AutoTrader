# Codex Phase B Prompt

You are running the DAILY automated Phase B step: re-verify, deterministic risk
enforcement, order review/execution, and logging for the Robinhood trading
account configured in `risk_rules.json`.

This repository has already been checked out into your working directory.
`PHASE_B_TASK.md` is the full source-of-truth spec for this run. Read it and
follow it exactly.

Before doing any trading workflow work:

1. Determine today's real date, day of week, and time of day in
   America/Chicago by running:

   ```bash
   TZ='America/Chicago' date +'%Y-%m-%d'
   TZ='America/Chicago' date +'%A'
   TZ='America/Chicago' date +'%H:%M:%S'
   ```

2. Read `risk_rules.json`, `pending_proposals.jsonl`, and `trade_log.jsonl`
   fresh from this checkout.
3. If any required value in `risk_rules.json` still contains `YOUR_..._HERE`,
   stop and report the missing values. Do not call Robinhood MCP tools.
4. Confirm Robinhood MCP tools needed by `PHASE_B_TASK.md` are available. If
   they are unavailable, stop and report that the Robinhood MCP connection is
   missing.

Phase B hard rules:

- Never change `execution.mode` or any other value in `risk_rules.json`.
- Never place a live order unless the live-order gate in `PHASE_B_TASK.md` is
  satisfied at that moment.
- Use the scripts in `scripts/` for mechanical risk math and read their JSON
  outputs directly.
- Append to `trade_log.jsonl`; do not rewrite existing audit lines.
- Regenerate `trade_log_recent.md` from today's logged decisions after the run.
- Do not modify `pending_proposals.jsonl`.

Run `PHASE_B_TASK.md` Steps 4-9 exactly, including idempotency based on each
candidate's `proposal_date`, dry-run cycle counting, sell-side checks,
buy-side gates, sizing, execution, final `cycle_summary`, and recap generation.

When outputs are complete, commit and push them:

```bash
git status --short
git add trade_log.jsonl trade_log_recent.md
git commit -m "Phase B run <date> <timestamp>"
git push origin main
```

If `trade_log_recent.md` was not generated because the task stopped before any
cycle output, only stage files that actually changed. If the commit has no
changes, report that clearly and do not force an empty commit. If the push is
rejected, run `git pull --rebase origin main` once and retry the push once. If
it still fails, report the exact conflict or error and do not force-push.

End with a concise summary of what you checked, approved, rejected, and placed.
If a push succeeded, include the commit hash.
