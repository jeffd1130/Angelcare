# weekly-status

Read-only check of all slots in the current ISO week. No files are created or modified.

## When to use

- Jeff says "where are we," "weekly status," "what's pending," "what's due"

## Steps

1. Compute current ISO week.
2. For each slot:
   - Does `content/<week>/<slot>/brief.md` have a job title? → brief filled / blank
   - Does `drafts/draft.json` exist? → draft ready / missing
   - Does `approved/` contain any files? → approved / pending
3. Print a table:

```
Week: 2026-W##

Slot                 | Brief | Draft | Approved | Drops
---------------------|-------|-------|----------|-------
01-tue-hiring        | ✓     | ✓     | —        | Tue 9:00 AM PST (1:00 AM Wed MLA)
02-fri-hiring        | —     | —     | —        | Fri 9:00 AM PST (1:00 AM Sat MLA)
```

4. Note any action items (e.g., "02-fri-hiring brief is blank — fill in before running produce-post").
