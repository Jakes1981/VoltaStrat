# Application Pipeline Reference

## Pipeline Stages

```
Submitted → [Stage 1 Screen] → Invited → [Stage 2 Interview] → Approved / Declined
                                                                      ↓
                                                               [Onboarded]
                                                                      ↓
                                                            [Eligible for Seat]
                                                                      ↓
                                                         [Monthly Eval Cycle]
                                                                      ↓
                                                           [Residency Seat]
```

## Application Data Structure

Applications are stored in the `applications` table in Supabase. The `data` column is JSONB containing:

### Founder Information
- `first_name`, `last_name` — founder name
- `email` — contact email
- `province` — location (Atlantic Canada check)
- `canada_status` — citizenship/PR/work-permit status
- `weekly_hours` — commitment level

### Company Information
- `company_name` — startup name
- `problem_statement` — the problem being solved
- `solution_description` — the product/approach
- `target_audience` — ICP description
- `competitive_advantage` — differentiation
- `current_users` — user/customer count
- `ai_usage` — how AI is used in the product

### Review Data (written by the reviewer)
Stored in `data.volta_review`:
- `reviewed_at` — timestamp
- `reviewer` — who reviewed
- `recommendation` — "Go" or "No-Go"
- `rejection_variant` — null, "3D-1", "3D-2", "3D-3", or "3D-4"
- `scores` — object with location, ai_first, problem_solution, disallowed (each has score, evidence, confidence)
- `key_strengths` — array
- `key_risks` — array
- `rationale` — summary

## Key Supabase Queries

### Fetch unreviewed applications
```sql
SELECT id, data, submitted_at, created_at
FROM applications
WHERE submitted_at IS NOT NULL
  AND invited_at IS NULL
  AND approved_at IS NULL
  AND declined_at IS NULL
  AND deleted_at IS NULL
ORDER BY submitted_at ASC;
```

### Write review back
```sql
UPDATE applications
SET data = jsonb_set(
  data,
  '{volta_review}',
  '{"reviewed_at": "...", "reviewer": "Laura", "recommendation": "...", "scores": {...}}'::jsonb
)
WHERE id = '<application_id>';
```

### Mark as invited (after Stage 1 Go)
```sql
UPDATE applications
SET invited_at = NOW()
WHERE id = '<application_id>';
```

### Mark as approved (after Stage 2 pass)
```sql
UPDATE applications
SET approved_at = NOW()
WHERE id = '<application_id>';
```

### Mark as declined
```sql
UPDATE applications
SET declined_at = NOW()
WHERE id = '<application_id>';
```

### Get eligibility list (approved but not yet in residency)
```sql
SELECT a.id, a.data, a.approved_at
FROM applications a
LEFT JOIN program_cohort_companies pcc ON (a.data->>'company_id')::uuid = pcc.company_id
WHERE a.approved_at IS NOT NULL
  AND a.declined_at IS NULL
  AND a.deleted_at IS NULL
  AND pcc.id IS NULL
ORDER BY a.approved_at ASC;
```

## Scheduling Link

**Current:** `https://calendly.com/jaco-voltaeffect/foundermindset`
**TODO:** Update to Laura's Calendly or a shared `residency@voltaeffect.com` link.

## Email Account

All application communications sent from: **residency@voltaeffect.com**
