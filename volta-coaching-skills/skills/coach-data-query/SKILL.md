---
name: coach-data-query
description: This skill should be used when a coach needs to query data about their portfolio companies from Volta's Impact OS (Supabase). Also activates for "show me my companies", "what's the latest on [company]", "pull metrics for [company]", "any stale data", "company context", "what are the open action items", "portfolio health", or "who needs attention".
version: 1.0.0
---

# Coach Data Query

Query Volta's Impact OS (Supabase) for live portfolio company data. Pull company context, metrics, transcripts, action items, and data hygiene alerts for the coach's assigned teams.

## Data Access

All queries run against Volta's Supabase project: `keolicewwymohxahikei`

Connection method: Use `mcp__supabase__execute_sql` if available. If not, use the REST API approach:

```bash
cd "/Volumes/Seagate/CINO-Worklow-Optimization" && DOTENV_PRIVATE_KEY="5000514a21c3313987d1f456ad2e844b5fae54b59f1cbb4baa54d6167868089d" npx --yes @dotenvx/dotenvx run --env-file=/Volumes/SecureVault/secrets/CINO-Workflow/.env -- python3 -c "CODE"
```

If neither is available, provide the SQL query for the coach to run in the Supabase dashboard.

## Queries

### My Companies
Pull all active companies assigned to a specific coach:
```sql
SELECT c.business_name, c.traction_level, c.operating_status,
       se.date_identified AS engagement_start,
       p.first_name || ' ' || p.last_name AS coach
FROM companies c
JOIN support_engagements se ON c.id = se.company_id
JOIN people p ON se.advisor_id = p.id
JOIN emails e ON p.id = e.people_id
WHERE se.status = 'active'
  AND e.email = '[coach_email]'
ORDER BY c.business_name;
```

### Company Full Context
Pull everything about a specific company:
```sql
-- Company overview
SELECT id, business_name, traction_level, operating_status FROM companies WHERE business_name ILIKE '%[name]%';

-- Recent meetings with transcripts
SELECT i.date, i.name, mt.fireflies_summary, mt.fireflies_action_items
FROM interaction i
JOIN interaction_companies ic ON i.id = ic.interaction_id
LEFT JOIN meeting_transcripts mt ON i.id = mt.interaction_id
WHERE ic.company_id = '[id]' ORDER BY i.date DESC LIMIT 5;

-- Latest metrics
SELECT submission_data FROM form_submissions
WHERE (submission_data->>'company_id')::uuid = '[id]' AND submitted = true
ORDER BY created_at DESC LIMIT 1;

-- Open action items
SELECT text, owner_type, meeting_date, CURRENT_DATE - meeting_date AS days_open
FROM action_items WHERE company_id = '[id]' AND status = 'open' ORDER BY meeting_date ASC;

-- Team members
SELECT p.first_name, p.last_name, p.title, e.email
FROM people p JOIN people_companies pc ON p.id = pc.person_id
LEFT JOIN emails e ON p.id = e.people_id AND e.is_primary = true
WHERE pc.company_id = '[id]';
```

### Data Hygiene Check
Flag issues across the coach's portfolio:
```sql
-- Stale companies (no meeting 21+ days)
SELECT c.business_name, MAX(i.date) AS last_meeting,
       CURRENT_DATE - MAX(i.date) AS days_since
FROM companies c
JOIN support_engagements se ON c.id = se.company_id
JOIN emails e ON se.advisor_id = (SELECT id FROM people WHERE id = (SELECT people_id FROM emails WHERE email = '[coach_email]'))
JOIN interaction_companies ic ON c.id = ic.company_id
JOIN interaction i ON ic.interaction_id = i.id
WHERE se.status = 'active'
GROUP BY c.id, c.business_name
HAVING MAX(i.date) < CURRENT_DATE - INTERVAL '21 days';

-- Overdue action items (14+ days)
SELECT c.business_name, ai.text, ai.owner_type, ai.meeting_date,
       CURRENT_DATE - ai.meeting_date AS days_open
FROM action_items ai
JOIN companies c ON ai.company_id = c.id
WHERE ai.status = 'open' AND ai.meeting_date < CURRENT_DATE - INTERVAL '14 days'
ORDER BY days_open DESC;
```

### Prep Brief
Generate a pre-session brief for a specific company:
1. Pull company full context (queries above)
2. Synthesize into the 3-bullet prep card format:
   - **Context:** Where the company is now — stage, momentum, key challenge
   - **Last time:** What was discussed/decided, commitments made
   - **Come in ready to:** The ONE thing to address today
3. Identify the primary risk to probe based on milestone stage (read `references/stage-risk-map.md`)

### Post-Session Report
After a session, pull the Fireflies transcript and generate:
1. Key discussion points
2. Decisions made
3. 2-week sprint plan (founder commitments)
4. Minimum commitments (before next session)
5. Support needed (introductions, resources)
6. Action items — founder + coach
7. Follow-up email draft

## Coach Email Lookup

| Coach | Email |
|-------|-------|
| Jaco Burger | jaco@voltaeffect.com |
| Jared Perry | (look up in people/emails tables) |
| Shahriar Amin | shahriar.amin.21@gmail.com |
| Jeremy Koenig | jeremy@irisinfinity.com |
| Shri | shri@voltaeffect.com |

## Reference Files

- `references/stage-risk-map.md` — Which risk to focus on at each milestone stage
- `references/supabase-connection.md` — Connection details and fallback methods

## Quality Standards

- Always query live data — never recall from memory
- If data is missing or stale, flag it explicitly
- Distinguish between data from the system and inferences
- If MCP tools are unavailable, provide the SQL for manual execution
- Never expose service role keys in outputs — use anon key for any client-side references
