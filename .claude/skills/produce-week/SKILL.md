# produce-week

Produce all remaining drafts for the current ISO week, then update the approval page and push to GitHub.

## When to use

- Jeff says "produce this week," "make all posts," "run the week"

## Steps

1. Compute current ISO week (`YYYY-W##`).
2. For each slot (`01-tue-hiring`, `02-fri-hiring`):
   a. Check if `content/<week>/<slot>/drafts/draft.json` already exists with `status: draft-ready`. If yes, skip.
   b. Check if `brief.md` has a job title filled in. If blank, skip and note it for Jeff.
   c. Otherwise invoke `produce-post` for that slot.
3. After all slots are processed, invoke `update-approval-page`.
4. Push to GitHub:
   ```
   git add content/ docs/
   git commit -m "ACA W<##> — hiring drafts"
   git push
   ```
5. Report: list each slot with its status (produced / skipped-existing / skipped-no-brief), Canva edit URL, and the approval page URL.

## Notes

- Always process `01-tue-hiring` before `02-fri-hiring` (posting order).
- If both briefs are blank, stop and tell Jeff to fill them in before running.
