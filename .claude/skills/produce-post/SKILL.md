# produce-post

Produce one hiring post draft end-to-end: copy pages 1–4 of the Canva hiring template as a single 4-page carousel design, update all text fields per slide, save metadata, generate captions for all 3 platforms.

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

1. Read `templates.json`. Get `template_design_id` and slot schedule.
2. Read `content/<week>/<slot>/brief.md`. Extract: `county`, `total_openings`, `roles` (grouped by category), `cta_email`.
3. If county or openings are blank, stop and ask Jeff to fill in the brief.

### 2. Ensure week folder exists

If `content/<week>/` doesn't exist, copy `content/_template/` to `content/<week>/`.
Ensure `raw/`, `drafts/`, `approved/` subdirs exist under the slot folder.

### 3. Copy the template — pages 1–4 only

Call `Canva:copy-design`:
- `design_id`: `DAG2tE1DHiI`
- `page_numbers`: `[1, 2, 3, 4]`
- `title`: `ACA-<slot>-<week>` (e.g. `ACA-01-tue-hiring-2026-W23`)

This produces a single 4-page design. Save the new `design_id` and edit URL.

### 4. Edit all 4 pages via one editing transaction

1. `Canva:start-editing-transaction` on the copied design.
2. `Canva:get-design-pages` — note all 4 page IDs (p1, p2, p3, p4).
3. `Canva:get-design-content` — inspect text elements per page to identify placeholder text.
4. Build `perform-editing-operations` with `find_and_replace_text` ops covering all 4 pages:

   **Page 1 — Cover**
   - County name placeholder → `<County>, CA`
   - Openings count placeholder → `<total_openings> Open Positions`
   - Apply/CTA text → `Apply Now · <cta_email>`

   **Page 2 — Nursing Roles**
   - County placeholder → `<County>, CA`
   - Section header → `Nursing Roles`
   - Role list → formatted list: `RN ×<n>`, `LVN ×<n>`, `Weekend RN ×<n>` + contact on last line
   - Element suffixes (appended to page ID): `-LBTl2H17syQSVsxQ` (headline word 1), `-LBvqqdmQ60QR7VLc` (headline word 2), `-LBZlTyTjzdhJjjXX` (county), `-LBZwGF3kHzpN7p7j` (section header), `-LBTfGPnZ43gwxwgg` (role list + contact)

   **Page 3 — Therapy Roles**
   - County placeholder → `<County>, CA`
   - Section header → `Therapy Roles`
   - Role list → `PT ×<n>`, `PTA ×<n>`, `OT ×<n>`, `COTA ×<n>`, `ST ×<n>` + contact on last line
   - Element suffixes: `-LBvfNBV9JLbXP0hg`, `-LBsfZtJ34WnzNFg2`, `-LB43sLrKl1tZ9JdK`, `-LBn01ffyFRTWVV8p`, `-LBDf0kSD2JD1Gk8d`

   **Page 4 — Support + CTA**
   - County placeholder → `<County>, CA`
   - Section header → `Support & More`
   - Role list → `CHHA ×<n>`, `MSW ×<n>` + contact + website on last line
   - Element suffixes: `-LBfP62ZGMJmWZPf3`, `-LBp2F9Mb9hH6gmZN`, `-LBdlhDsm81HCWs02`, `-LBr0yJqws0Srh1Vn`, `-LBwFPnV7y9CyBfb4`

   > **Note on element IDs:** The full element ID = `<page_id><suffix>`. Get page IDs from step 2. If find_and_replace_text is used instead of element IDs, match on the placeholder text visible in the template.

5. `Canva:commit-editing-transaction`. On failure, `cancel-editing-transaction` and keep the un-edited copy — log the issue in `draft.json`.

### 5. Save metadata

Create `content/<week>/<slot>/drafts/draft.json`:

```json
{
  "generated_at": "<ISO datetime>",
  "slot": "<slot ID>",
  "week": "<YYYY-W##>",
  "canva_design_id": "<copied design id>",
  "canva_edit_url": "<edit URL>",
  "county": "<county name>",
  "total_openings": <number>,
  "roles": { "<role>": <count>, ... },
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

Write `content/<week>/<slot>/drafts/caption.md`:

```
## Instagram
<2–3 sentence caption, hiring tone, 1 CTA, 8–12 hashtags>

## Facebook
<Same message, slightly longer — 1 extra sentence about company culture>

## LinkedIn
<Professional tone, 3–4 sentences, emphasize role impact, 3–5 hashtags>
```

Hashtag guidelines:
- IG/FB: mix of role-specific (#RegisteredNurse), location (#SolanoCounty / #SantaClaraCounty), and brand (#AngelcareHHS #HomeHealthCareJobs #NowHiring)
- LinkedIn: fewer, professional (#HomeHealthcare #HealthcareJobs #NowHiring)
- Rotate sets week to week to avoid repeat flags

### 7. Report to Jeff

```
✓ <slot> draft ready — 4-slide carousel
  Canva: <edit URL>
  County: <county> · <total_openings> openings
  Drops: <weekday> at <drop_pst> PST (<drop_mla> Manila)
  Platforms: Instagram · Facebook · LinkedIn
  Files: content/<week>/<slot>/drafts/
```

## Failure modes

| Failure | Action |
|---------|--------|
| brief.md is empty / no county | Stop. Ask Jeff to fill in the brief. |
| `copy-design` fails | Retry once. If it fails again, stop. |
| Editing transaction fails | Keep un-edited copy. Log in draft.json. |
| Page count ≠ 4 after copy | Log warning; continue with available pages. |
| Canva not connected | Stop and surface the connection step. |
