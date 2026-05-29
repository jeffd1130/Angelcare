# Angelcare HHS — Social Media Automation

You are helping Jeff (Senior Marketing Analyst, based in Manila) run the weekly hiring post production system for **Angelcare Home Health Services**.

The goal is to produce 2 hiring posts per week for **Instagram, Facebook, and LinkedIn** — drafted, reviewed, then posted.

---

## Weekly schedule

| Slot | Day | Drop (PST) | Drop (Manila) |
|------|-----|------------|---------------|
| `01-tue-hiring` | Tuesday | 9:00 AM | 1:00 AM Wed |
| `02-fri-hiring` | Friday | 9:00 AM | 1:00 AM Sat |

---

## Workflow

| Stage | Day | Owner | Action |
|-------|-----|-------|--------|
| Brief | D-2 | Jeff | Fill `content/<week>/<slot>/brief.md` with job role, location, requirements |
| Design | D-1 | Jeff | Run `produce-post` or `produce-week` → Canva draft + captions ready |
| Approve | D-1 | Jeff | Review at **https://jeffd1130.github.io/Angelcare/** — edit Canva if needed |
| Post | D-0 | Jeff | Schedule/publish at 9:00 AM PST across all 3 platforms |

---

## Content focus

**Hiring posts** — Angelcare is actively recruiting. Each post highlights:
- Job title and location
- 2–3 key requirements or role highlights
- Clear CTA (apply link, DM, or email)

Tone: professional, warm, healthcare-industry appropriate. Not clinical. Not corporate-stiff.
The brand voice says: *"We care about our staff as much as our patients."*

### Platform tone split

| Platform | Tone | Length | Hashtags |
|----------|------|--------|----------|
| Instagram | Visual-first, brief caption | 2–3 sentences | 8–12 |
| Facebook | Slightly longer, community feel | 3–4 sentences | 5–8 |
| LinkedIn | Professional, role-impact focused | 3–5 sentences | 3–5 |

---

## Design system

- **Master template**: `DAG2tE1DHiI` — "Blue Photo Job Vacancy Instagram Post" (19 pages / variants)
- **Brand reference**: `DAHKZvtfKl4` — Angelcare brand colors, fonts, logo
- **Mode**: copy-template — each run copies the master and edits text fields
- **No Canva brand kit yet** — create one and add ID to `templates.json → canva_workspace.brand_kit_id` when ready

### Template page roles (for 4-slide carousels)

| Slide | Template page | Photo | Text structure |
|-------|--------------|-------|----------------|
| S1 Cover | Page 1 | Original nurse (`MAG2tRZ191s`) | Big headline + county + total openings + Apply Now bar + contact footer |
| S2 Nursing | Page 2 | Asian female clinician (`MAG2tFsHEK4`) | Big headline + county + section header + role list + contact in text block |
| S3 Therapy | Page 3 | Black female clinician (`MAG2tP5PtlU`) | Same as S2 |
| S4 Support+CTA | Page 4 | Male clinician (`MAG2tZmjLW0`) | Same as S2 |

**Key constraint**: `update_fill` only works with assets already in a design's media bundle. To get different photos per slide, copy from the matching template page (not always page 1). Trying to swap a photo from page 2 into a page-1 copy will fail with "media bundle not found."

**Page 2–4 text structure** differs from page 1: no "Apply Now" button bar or separate contact footer elements. Include email/phone/website inline in the role list text block instead.

### Carousel text elements (per slide)

Pages 2–4 copies use these richtext element suffixes (page ID varies per copy):
- `-LBTl2H17syQSVsxQ` / `-LBvfNBV9JLbXP0hg` / `-LBfP62ZGMJmWZPf3` — headline word 1
- `-LBvqqdmQ60QR7VLc` / `-LBsfZtJ34WnzNFg2` / `-LBp2F9Mb9hH6gmZN` — headline word 2
- `-LBZlTyTjzdhJjjXX` / `-LB43sLrKl1tZ9JdK` / `-LBdlhDsm81HCWs02` — county
- `-LBZwGF3kHzpN7p7j` / `-LBn01ffyFRTWVV8p` / `-LBr0yJqws0Srh1Vn` — section header
- `-LBTfGPnZ43gwxwgg` / `-LBDf0kSD2JD1Gk8d` / `-LBwFPnV7y9CyBfb4` — role list + contact

---

## GitHub

- **Repo**: `jeffd1130/Angelcare`
- **Approval page**: `https://jeffd1130.github.io/Angelcare/`
- **Source**: `main` branch `/docs` folder
- Auto-push hook fires on Write/Edit within this project directory

---

## Skills

| Skill | Trigger | Purpose |
|-------|---------|---------|
| `produce-post` | "make Tuesday's post," "run Friday's hiring post" | Produce ONE draft end-to-end |
| `produce-week` | "produce this week," "make all posts" | Produce both drafts, update approval page, push |
| `weekly-status` | "where are we," "what's pending," "weekly status" | Read-only state of current week |
| `update-approval-page` | "update the approval page" | Rebuild `docs/index.html` and push |

Full specs in `.claude/skills/<skill>/SKILL.md`.

---

## File conventions

```
content/
  2026-W22/
    01-tue-hiring/
      brief.md         ← fill before running produce-post
      raw/             ← optional photo assets
      drafts/          ← draft.json, caption.md, hashtags.md
      approved/        ← move here after approval
    02-fri-hiring/
      ...
```

---

## Tools required

- **Canva MCP** — required. Used to copy the template, edit text fields, and export drafts.

---

## Working principles

1. **Brief before design.** Never run `produce-post` if `brief.md` has no job title. Ask Jeff to fill it in first.
2. **One question max.** If clarification is needed, ask the single most important thing.
3. **Surface both times.** Every draft output states drop time in both PST and Manila.
4. **Three platforms, one brief.** One Canva design + three caption variants per post.
5. **Don't post.** You produce drafts. Posting is always Jeff's call.
