# produce-post

Produce one hiring post draft end-to-end: copy the Canva hiring template, update text fields with the job details from brief.md, export the draft URL, save metadata, generate caption + hashtags for all 3 platforms.

## When to use

- Jeff says "make Tuesday's post," "run Friday's hiring post," "produce this week's post," etc.
- `produce-week` calls this per slot

## Inputs

- **slot ID** — `01-tue-hiring` or `02-fri-hiring`. If Jeff says "tomorrow's post," compute tomorrow's day and map to slot.
- **week** — defaults to current ISO week (`YYYY-W##`). Override if Jeff specifies.

## Roster

`content/hiring-roster.json` is the source of truth for open positions. The brief.md for each slot is pre-filled from this data but can be overridden manually. If Jeff says "update the roster" or "we filled the RN slot," update `hiring-roster.json` first, then re-generate the brief.

## Required tools

- `Canva:copy-design`, `Canva:get-design-content`, `Canva:get-design-pages`, `Canva:start-editing-transaction`, `Canva:perform-editing-operations`, `Canva:commit-editing-transaction`, `Canva:cancel-editing-transaction`, `Canva:get-design`

If any are unavailable, stop and tell Jeff.

## Step-by-step

### 1. Read the brief

1. Read `templates.json`. Get `template_design_id`, slot config, and schedule.
2. Read `content/<week>/<slot>/brief.md`. Extract: `job_title`, `location`, `requirements`, `cta`, `template_page` (default to 1 if blank), and `photo_asset` (optional).
3. If `job_title` is blank, stop and ask Jeff to fill in the brief first.

### 2. Ensure week folder exists

If `content/<week>/` doesn't exist, copy `content/_template/` to `content/<week>/`.
Ensure `raw/`, `drafts/`, `approved/` subdirs exist under the slot folder.

### 3. Copy the template design

Call `Canva:copy-design`:
- `design_id`: `templates.json → canva_workspace.template_design_id` (`DAG2tE1DHiI`)
- `title`: `ACA-<slot>-<week>` (e.g. `ACA-01-tue-hiring-2026-W21`)

Save the new `design_id` and edit URL.

### 4. Update text via editing transaction

1. `Canva:start-editing-transaction` on the copied design.
2. `Canva:get-design-pages` to inspect available pages. If `template_page` from brief is not 1, note which page is active.
3. Use `Canva:perform-editing-operations` with `find_and_replace_text` ops to update:
   - Job title placeholder → `job_title` from brief
   - Location placeholder → `location`
   - Requirements placeholder → bullet list of requirements
   - CTA placeholder → `cta`
4. `Canva:commit-editing-transaction`. On failure, `cancel-editing-transaction` and keep the un-edited copy — log the issue in `draft.json`.

### 5. Save metadata

Create `content/<week>/<slot>/drafts/draft.json`:

```json
{
  "generated_at": "<ISO datetime>",
  "slot": "<slot ID>",
  "week": "<YYYY-W##>",
  "canva_design_id": "<copied design id>",
  "canva_edit_url": "<edit URL>",
  "job_title": "<from brief>",
  "location": "<from brief>",
  "template_page_used": <number>,
  "schedule": {
    "post_day": "<weekday>",
    "drop_pst": "<HH:MM>",
    "drop_mla": "<Manila time>"
  },
  "platforms": ["instagram", "facebook", "linkedin"],
  "status": "draft-ready"
}
```

### 6. Generate captions + hashtags

Write `content/<week>/<slot>/drafts/caption.md` with platform-specific captions:

```
## Instagram
<2–3 sentence caption, hiring tone, 1 CTA, 8–12 hashtags>

## Facebook
<Same message, slightly longer — can add 1 extra sentence about company culture>

## LinkedIn
<Professional tone, 3–4 sentences, emphasize role impact, 3–5 hashtags>
```

Hashtag guidelines:
- IG/FB: mix of role-specific (#RegisteredNurse), location (#LosAngeles), and company (#AngelcareHHS #HomeHealthCareJobs #NowHiring)
- LinkedIn: fewer, more professional (#HomeHealthcare #HealthcareJobs #NowHiring)
- Rotate sets to avoid repeat flags

Write `content/<week>/<slot>/drafts/hashtags.md` with the full hashtag pool used.

### 7. Report to Jeff

```
✓ <slot> draft ready
  Canva: <edit URL>
  Role: <job_title> — <location>
  Drops: <weekday> at <drop_pst> PST (<drop_mla> Manila)
  Platforms: Instagram · Facebook · LinkedIn
  Files: content/<week>/<slot>/drafts/
```

## Failure modes

| Failure | Action |
|---------|--------|
| brief.md is empty / no job title | Stop. Ask Jeff to fill in the brief. |
| `copy-design` fails | Retry once. If it fails again, stop. |
| Editing transaction fails | Keep un-edited copy. Log in draft.json. |
| Canva not connected | Stop and surface the connection step. |
