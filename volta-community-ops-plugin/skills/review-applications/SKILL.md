---
name: review-applications
description: This skill should be used when reviewing new Volta residency applications, screening applicants, preparing for founder interviews, or drafting application response emails. Also activates for "check applications", "any new apps", "review the pipeline", "screen applicants", "application review", "interview prep", "founder mindset fit", or "residency readiness check".
version: 1.0.0
---

# Review Applications

Screen, interview, and evaluate candidates for Volta's 1:1 residency program using a unified three-stage rubric. Every stage uses the same +1 / 0 / -1 scoring system. Scores guide focus and shortlisting — they do not replace judgment.

## Three Stages

This skill covers the full application-to-residency pipeline:

1. **Stage 1: Screening** — Gate criteria applied to written applications (daily, 30 min)
2. **Stage 2: Founder & Mindset Fit** — 30-minute interview with 6 scored dimensions (week 2 of each month)
3. **Stage 3: Residency Readiness** — Ongoing assessment for Network members being considered for a seat (monthly eval cycle)

Invoke with a mode to indicate which stage:

- `/review-applications` or `/review-applications --screen` → Stage 1 screening
- `/review-applications --interview [company]` → Stage 2 interview prep and scoring
- `/review-applications --readiness [company]` → Stage 3 residency readiness check

## Stage 1: Screening (Daily)

### Process

1. Query Supabase for unreviewed applications:
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
   Use `mcp__supabase__execute_sql` to run this query.

2. For each application, extract from the `data` JSONB column:
   - Founder name, company name, province, Canada status, weekly hours
   - Problem statement, solution description, target audience
   - Competitive advantage, current user count, AI usage

3. Apply the Stage 1 rubric by reading `references/application-rubric.md` and deploying the `application-screener` agent.

4. For each application, present the assessment to the user:
   - Scores for all 4 criteria with evidence
   - Go / No-Go recommendation
   - Rejection variant if No-Go (3D-1 through 3D-4)
   - Key strengths and risks
   - Draft email (invite or rejection)

5. Wait for user approval before proceeding. Never write to Supabase or send email without explicit confirmation.

6. On approval, write the review to Supabase:
   ```sql
   UPDATE applications
   SET data = jsonb_set(data, '{volta_review}', '<review_json>')
   WHERE id = '<application_id>';
   ```

### Key Rules
- Score 0 is NEUTRAL — it means uncertainty, NOT rejection
- Only a -1 on gate criteria (Location, AI-First, Disallowed) triggers No-Go
- Problem/Solution Fit can be -1 without triggering No-Go
- Products that SERVE regulated industries are NOT themselves regulated
- Every rejection email must include personalized feedback — no form letters

## Stage 2: Founder & Mindset Fit Interview (V5.0)

### Before the Interview
When invoked with `--interview [company]`:
1. Pull the Stage 1 screening results from Supabase
2. Pull any available company context (website, prior interactions)
3. Present a brief reminder of the 5 dimensions and key questions to explore
4. Read `references/interview-guide-v5.md` for the full conversational framework

### After the Interview
When the user returns from the interview and is ready to score, walk through each dimension interactively:

For each of the 5 dimensions, ask the user:
- "How would you score [dimension name]? (1-5)" 
- "What's the key evidence for that score? (1-2 sentences)"

**The 5 Dimensions (each scored 1-5):**
1. **Problem & Customer Evidence** — Real problem with real evidence, or building on assumptions?
2. **AI-Native & Technical Depth** — Is AI genuinely core? Does the founder understand the landscape?
3. **Commercial & Go-to-Market Readiness** — Can they sell, not just build?
4. **Founder Quality** — Who are they under pressure? Will they grow through coaching?
5. **Alignment & Commitment** — Is Volta's model right for where they are? 20+ hrs/week sales AND 20+ hrs/week product?

Also ask:
- "Describe the live coaching moment — what did you challenge, and how did they respond?"
- "Key strengths?"
- "Key risks?"

### Decision Thresholds
- **21-25:** Strong Residency candidate → Accept to Network → Onboard → Eligible for seat
- **15-20:** Network — revisit in 30-60 days with specific development areas
- **10-14:** Network — longer horizon, community access only
- **≤9:** No-Go — personalized rejection with feedback

**Floor rule:** Any dimension at 1 is a flag. Two or more at 1 = No-Go regardless of total.

### Produce the Interview Artefact

After scoring is complete, generate a structured artefact in this exact format. This is what gets copied into Impact OS (application comments) or attached as a document:

```
═══════════════════════════════════════════════════════════
VOLTA FOUNDER & MINDSET FIT SCORECARD — V5.0
═══════════════════════════════════════════════════════════

Founder(s): [name(s)]
Company:    [company name]
Date:       [interview date]
Interviewer: [name]

───────────────────────────────────────────────────────────
DIMENSION SCORES
───────────────────────────────────────────────────────────

1. Problem & Customer Evidence:          [X]/5
   [1-2 sentence evidence]

2. AI-Native & Technical Depth:          [X]/5
   [1-2 sentence evidence]

3. Commercial & Go-to-Market Readiness:  [X]/5
   [1-2 sentence evidence]

4. Founder Quality:                      [X]/5
   [1-2 sentence evidence]

5. Alignment & Commitment:               [X]/5
   [1-2 sentence evidence]

───────────────────────────────────────────────────────────
TOTAL: [XX]/25 → [OUTCOME: Residency / Network 30-60d / Network Longer / No-Go]
───────────────────────────────────────────────────────────

Live Coaching Moment:
[What was challenged and how the founder responded]

Key Strengths:
- [strength 1]
- [strength 2]

Key Risks:
- [risk 1]
- [risk 2]

Recommendation: [Residency / Network (revisit [date]) / No-Go]
Notes: [any additional context for the decision meeting]

═══════════════════════════════════════════════════════════
```

Present this artefact to the user and offer:
1. "Copy this to clipboard for pasting into Impact OS"
2. "Draft the outcome email?" → delegate to `comms-drafter` agent

If the user wants to push the score to Supabase, write to the application's `data.interview_review` field:
```sql
UPDATE applications
SET data = jsonb_set(data, '{interview_review}', '{
  "scored_at": "[timestamp]",
  "interviewer": "[name]",
  "scores": {
    "problem_customer_evidence": {"score": X, "evidence": "..."},
    "ai_native_technical_depth": {"score": X, "evidence": "..."},
    "commercial_gtm_readiness": {"score": X, "evidence": "..."},
    "founder_quality": {"score": X, "evidence": "..."},
    "alignment_commitment": {"score": X, "evidence": "..."}
  },
  "total": XX,
  "outcome": "...",
  "coaching_moment": "...",
  "key_strengths": ["...", "..."],
  "key_risks": ["...", "..."],
  "recommendation": "...",
  "notes": "..."
}'::jsonb)
WHERE id = '[application_id]';
```

Draft the outcome email via `comms-drafter` agent based on the recommendation.

## Stage 3: Residency Readiness

When invoked with `--readiness [company]`:
1. Pull company data from Supabase (updates, interactions, coach notes)
2. Score 9 readiness criteria from `references/application-rubric.md` Section C
3. Check threshold: Interview ≥ +3 AND Readiness sum ≥ +3 (last 30-60 days)
4. If threshold met and seats available → recommend for Residency acceptance
5. Present assessment for user review

## Reference Files

- `references/application-rubric.md` — Complete unified rubric (Stage 1 screening + Stage 3 residency readiness)
- `references/interview-guide-v5.md` — V5.0 Founder & Mindset Fit conversational framework (5 dimensions, 1-5 scoring, principles)
- `references/email-templates.md` — Email templates for all outcomes
- `references/application-pipeline.md` — Pipeline stages, data fields, system details
- `references/assessment-history.md` — Prior screening decisions with rationale and cross-portfolio patterns. Read this when encountering edge cases or when the user asks "how did we handle something like this before?"

## Quality Standards

- Every assessment grounded in application data, not assumptions
- Confidence level (Low/Medium/High) noted for each score
- Distinguish between what is stated, what is inferred, and what is missing
- Personalize every email — reference the founder's actual problem, ICP, and company
- Present all results for human review before any action
