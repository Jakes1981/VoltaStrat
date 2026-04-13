---
name: portfolio-check
description: This skill should be used when checking portfolio data health, running weekly hygiene reviews, identifying stale companies, generating portfolio snapshots for reporting, or auditing data quality in Impact OS. Also activates for "data check", "what's stale", "portfolio health", "any gaps", "weekly review", "what needs attention", "run the hygiene check", or "portfolio snapshot for Matt".
version: 1.0.0
---

# Portfolio Check

Weekly data hygiene review and portfolio health snapshot. Identifies what needs attention across Volta's active portfolio — stale companies, missing updates, stuck applications, engagement gaps, and overdue commitments.

## Modes

- `/portfolio-check` or `/portfolio-check --hygiene` → Data hygiene audit (default)
- `/portfolio-check --snapshot` → Full portfolio health snapshot for reporting

## Data Hygiene Audit (Weekly)

### Process

1. Deploy the `data-auditor` agent to run all audit queries against Supabase via MCP.

2. The agent checks:
   - **Stale companies:** No coaching session in 21+ days
   - **Missing updates:** No company update in 30+ days
   - **Stuck applications:** Submitted but unprocessed for 3+ days
   - **Engagement gaps:** Active engagements without assigned coaches
   - **Overdue action items:** Open commitments older than 14 days
   - **Transcript coverage:** Active residents without recent Fireflies transcripts

3. Present results as a prioritized action list:
   - **Critical (act today):** Stuck applications 7+ days, engagements without coaches
   - **Warning (act this week):** Stale companies, missing updates, overdue items
   - **Monitor (check next week):** Approaching thresholds

4. For each item, suggest the specific action to take.

### Thresholds

| Metric | Warning | Critical |
|--------|---------|----------|
| No meeting | 21 days | 35 days |
| No company update | 30 days | 45 days |
| Application unprocessed | 3 days | 7 days |
| Action item open | 14 days | 28 days |
| No transcript | 30 days | 60 days |

## Portfolio Snapshot (Monthly/On-demand)

### Process

1. Deploy the `context-builder` agent to pull portfolio-wide data.

2. Generate a snapshot covering:
   - Total active residents and count by milestone stage
   - Companies at each revenue tier ($1M+, $100K+, $10K+, pre-revenue)
   - Coach coverage (who coaches whom, session counts)
   - Data freshness summary (% with recent meetings, % with recent updates)
   - Recent milestone progressions (who moved up)
   - Flags and risks

3. Format for the audience:
   - For **Laura:** focus on operational gaps and what needs action
   - For **Matt/board:** focus on portfolio health metrics and trajectory

### Key Queries

Active residents by stage:
```sql
SELECT c.traction_level, COUNT(*) AS count
FROM companies c
JOIN program_cohort_companies pcc ON c.id = pcc.company_id
WHERE pcc.company_end_date IS NULL
GROUP BY c.traction_level
ORDER BY count DESC;
```

Coach workload:
```sql
SELECT p.first_name || ' ' || p.last_name AS coach,
       COUNT(DISTINCT se.company_id) AS companies
FROM support_engagements se
JOIN people p ON se.advisor_id = p.id
WHERE se.status = 'active'
GROUP BY p.id, p.first_name, p.last_name
ORDER BY companies DESC;
```

See `references/supabase-queries.md` for the full query library.

## Reference Files

- `references/data-hygiene-rules.md` — Threshold definitions and escalation criteria
- `references/supabase-schema.md` — Key tables and relationships
- `references/reporting-templates.md` — Output formats for different audiences

## Quality Standards

- Always query live data — present what the database shows right now
- Never estimate numbers — always query Supabase
- Flag data quality issues explicitly
- Prioritize by urgency — critical items first
- If MCP tools are unavailable, provide the SQL queries for manual execution in the Supabase dashboard
