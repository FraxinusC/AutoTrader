# Codex Phase A Prompt

You are running the DAILY automated Phase A step, screening and thesis only, for
the Robinhood trading account configured in `risk_rules.json`.

This repository has already been checked out into your working directory.
`PHASE_A_TASK.md` is the full source-of-truth spec for this run. Read it and
follow it exactly.

Before doing any trading workflow work:

1. Determine today's real date, day of week, and time of day in
   America/Chicago by running:

   ```bash
   TZ='America/Chicago' date +'%Y-%m-%d'
   TZ='America/Chicago' date +'%A'
   TZ='America/Chicago' date +'%H:%M:%S'
   ```

2. Read `risk_rules.json` fresh.
3. If any required value still contains `YOUR_..._HERE`, stop and report the
   missing values. Do not call Robinhood MCP tools.
4. Confirm Robinhood MCP tools needed by `PHASE_A_TASK.md` are available. If
   they are unavailable, stop and report that the Robinhood MCP connection is
   missing.

Phase A hard stop:

- Do not call `review_equity_order`, `place_equity_order`,
  `review_option_order`, `place_option_order`, or any cancel/order tool.
- Do not check or reference `execution.mode`.
- Do not touch `trade_log.jsonl`.

Run `PHASE_A_TASK.md` Steps 1-3 exactly. Overwrite
`pending_proposals.jsonl` with this run's output and append the summary lines
specified by the task.

When `pending_proposals.jsonl` is complete, commit and push it:

```bash
git status --short
git add pending_proposals.jsonl
git commit -m "Phase A run <date> <timestamp>"
git push origin main
```

If the commit has no changes, report that clearly and do not force an empty
commit. If the push is rejected, run `git pull --rebase origin main` once and
retry the push once. If it still fails, report the exact conflict or error.

End with a concise summary of what you screened, filtered, and proposed. If a
push succeeded, include the commit hash.
