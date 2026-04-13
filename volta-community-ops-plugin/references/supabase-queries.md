# Common Supabase Queries

All queries run against Volta's Impact OS: project `keolicewwymohxahikei`.

Use `mcp__supabase__execute_sql` in Claude Code. If MCP is unavailable, run these in the Supabase SQL Editor at supabase.com.

---

## Portfolio Overview

### All active residents
```sql
SELECT c.id, c.business_name, c.traction_level, c.operating_status,
       pcc.company_start_date, pcc.company_end_date
FROM companies c
JOIN program_cohort_companies pcc ON c.id = pcc.company_id
WHERE pcc.company_end_date IS NULL
ORDER BY c.business_name;
```

### Companies by milestone stage
```sql
SELECT c.traction_level, COUNT(*) AS count
FROM companies c
JOIN program_cohort_companies pcc ON c.id = pcc.company_id
WHERE pcc.company_end_date IS NULL
GROUP BY c.traction_level
ORDER BY count DESC;
```

### Companies with their assigned coaches
```sql
SELECT c.business_name, p.first_name || ' ' || p.last_name AS coach,
       se.title AS engagement, se.status
FROM companies c
JOIN support_engagements se ON c.id = se.company_id
LEFT JOIN people p ON se.advisor_id = p.id
WHERE se.status = 'active'
ORDER BY c.business_name;
```

## Application Pipeline

### Pipeline summary by status
```sql
SELECT
  COUNT(*) FILTER (WHERE submitted_at IS NULL) AS incomplete,
  COUNT(*) FILTER (WHERE submitted_at IS NOT NULL AND invited_at IS NULL AND approved_at IS NULL AND declined_at IS NULL AND deleted_at IS NULL) AS submitted,
  COUNT(*) FILTER (WHERE invited_at IS NOT NULL AND approved_at IS NULL AND declined_at IS NULL) AS invited,
  COUNT(*) FILTER (WHERE approved_at IS NOT NULL) AS approved,
  COUNT(*) FILTER (WHERE declined_at IS NOT NULL) AS declined
FROM applications;
```

## Coaching Activity

### Recent meetings by company
```sql
SELECT c.business_name, i.date, i.name,
       CASE WHEN mt.id IS NOT NULL THEN 'Yes' ELSE 'No' END AS has_transcript
FROM interaction i
JOIN interaction_companies ic ON i.id = ic.interaction_id
JOIN companies c ON ic.company_id = c.id
LEFT JOIN meeting_transcripts mt ON i.id = mt.interaction_id
WHERE i.date > CURRENT_DATE - INTERVAL '30 days'
ORDER BY i.date DESC;
```

### Coach session counts (last 30 days)
```sql
SELECT p.first_name || ' ' || p.last_name AS coach,
       COUNT(DISTINCT i.id) AS sessions
FROM interaction i
JOIN interaction_companies ic ON i.id = ic.interaction_id
JOIN support_engagements se ON ic.company_id = se.company_id AND se.status = 'active'
JOIN people p ON se.advisor_id = p.id
WHERE i.date > CURRENT_DATE - INTERVAL '30 days'
GROUP BY p.id, p.first_name, p.last_name
ORDER BY sessions DESC;
```

## Company Updates

### Latest update per active company
```sql
SELECT c.business_name,
       MAX(fs.created_at) AS last_update,
       CURRENT_DATE - MAX(fs.created_at)::date AS days_ago
FROM companies c
JOIN program_cohort_companies pcc ON c.id = pcc.company_id
LEFT JOIN form_submissions fs ON (fs.submission_data->>'company_id')::uuid = c.id
  AND fs.submitted = true
WHERE pcc.company_end_date IS NULL
GROUP BY c.id, c.business_name
ORDER BY days_ago DESC NULLS FIRST;
```

## Engagement Management

### Expiring engagements (current month)
```sql
SELECT c.business_name, se.title, se.date_identified,
       se.date_identified + INTERVAL '3 months' AS expires,
       p.first_name || ' ' || p.last_name AS coach
FROM support_engagements se
JOIN companies c ON se.company_id = c.id
LEFT JOIN people p ON se.advisor_id = p.id
WHERE se.status = 'active'
  AND se.date_identified + INTERVAL '3 months'
      BETWEEN DATE_TRUNC('month', CURRENT_DATE)
      AND DATE_TRUNC('month', CURRENT_DATE) + INTERVAL '1 month';
```

## Action Items

### All open action items
```sql
SELECT c.business_name, ai.text, ai.owner_type, ai.meeting_date,
       CURRENT_DATE - ai.meeting_date AS days_open
FROM action_items ai
JOIN companies c ON ai.company_id = c.id
WHERE ai.status = 'open'
ORDER BY days_open DESC;
```
