# update-approval-page

Rebuild `docs/index.html` from current draft.json files and push to GitHub so the approval page is live.

## When to use

- After `produce-post` or `produce-week` completes
- Jeff says "update the approval page," "refresh the review site"

## Steps

1. Scan `content/<week>/*/drafts/draft.json` for all slots with `status: draft-ready`.
2. Read each `draft.json` plus its `caption.md`.
3. Rebuild `docs/index.html`:
   - Header: "Angelcare HHS — Hiring Posts · Week <##>"
   - One card per slot showing:
     - Canva edit link (button)
     - Job title + location
     - Platform captions (tabbed: IG / FB / LinkedIn)
     - Drop times (PST + Manila)
   - Footer: "Drafts produced by Claude Code · Review and approve before posting"
4. Commit and push:
   ```
   git add docs/
   git commit -m "ACA W<##> — approval page updated"
   git push
   ```
5. Print the live URL: `https://jeffd1130.github.io/Angelcare/`
