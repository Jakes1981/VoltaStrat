---
name: session-workflow
description: This skill should be used when preparing for a coaching session, structuring a conversation during a session, or completing follow-up after a session. Also activates for "prep for my session with [company]", "session prep", "how should I structure this session", "what do I focus on", "post-session report", "session follow-up", "close the loop", or "generate prep brief".
version: 1.0.0
---

# Session Workflow

Guide the full coaching session lifecycle — before, during, and after.

## Pre-Session Prep

When the coach says "prep for [company]" or "I have a session with [company]":

### Step 1: Pull Company Context
Deploy the coach-data-query skill to pull:
- Company stage (traction_level)
- Last 3 meetings with Fireflies summaries
- Latest company update with metrics (MRR, customers, burn, runway)
- Open action items (coach + founder, with days open)
- Active support engagement (who's the coach, when did it start)

### Step 2: Identify the Stage
Map the company to one of the 5 milestone stages:
- Value Baseline (pre-revenue)
- First Paying Customer
- X ($10K ARR)
- 10X ($100K ARR)
- $1M ARR

Read `references/stage-coaching-guide.md` for what to focus on at this stage.

### Step 3: Generate the Prep Brief
Format as a 3-bullet card:

```
SESSION PREP: [Company] — [Date]
Stage: [milestone stage]

1. CONTEXT: [Where the company is now — stage, momentum, key challenge based on latest data]
2. LAST TIME: [What was discussed in last session — focus on commitments made by both sides]
3. COME IN READY TO: [The ONE thing to address today based on stage-appropriate risk focus]

OPEN COMMITMENTS:
  Coach: [list with days open]
  Founder: [list with days open]

LATEST METRICS: MRR $X | Customers X | Burn $X/mo | Runway X months
```

### Step 4: Flag Concerns
If any of these are true, add a warning:
- No meeting in 21+ days → "STALE: No coaching session in [X] days"
- No company update in 30+ days → "DATA GAP: No update in [X] days"
- Action items overdue 14+ days → "OVERDUE: [X] open items past 14 days"
- Founder showing pattern of missed commitments → "PATTERN: Review accountability"

## During-Session Reference

When the coach asks "what should I focus on" or "how do I structure this session":

### Session Structure (35-40 minutes)

**Opening (5 min):** "What's happened since last time?" Review commitments from both sides.

**Middle (25-30 min):** Focus on the primary risk for this stage.
- Value Baseline → Probe customer discovery evidence
- First Customer → Probe pricing and conversion mechanics
- $10K ARR → Probe growth channel and CAC
- $100K ARR → Probe market position and team scaling
- $1M ARR → Probe institutional readiness

**Closing (5 min):** Set bidirectional commitments. Confirm next session.

### Quick Scenario Guidance
If the coach describes a situation, match it:
- "Founder can't describe the problem" → Redirect to Problem Hypothesis workshop
- "No metrics" → Prescribe an experiment (read experiments catalog)
- "Building not selling" → Challenge: who's spending time on sales?
- "Founder seems off" → Address wellness directly. Founder emotional state is a business variable.
- "Wants to show me the product" → Redirect: "Tell me about the problem first"

## Post-Session Follow-Up

When the coach says "post-session" or "close the loop" or "generate report":

### Step 1: Pull Transcript
Query the most recent Fireflies transcript for this company:
```sql
SELECT mt.fireflies_summary, mt.fireflies_action_items, mt.meeting_date
FROM meeting_transcripts mt
WHERE mt.company_id = '[company_id]'
ORDER BY mt.meeting_date DESC LIMIT 1;
```

Verify the transcript is from today or very recent. If it's old, the Fireflies sync may not have run yet — inform the coach.

### Step 2: Extract from Transcript
From the Fireflies summary and action items, extract:
- Key discussion points (3-5 bullets)
- Decisions made
- Founder commitments (with implied deadlines)
- Coach commitments (with implied deadlines)

### Step 3: Generate Post-Session Report

```
POST-SESSION: [Company] — [Date]
Coach: [name]

KEY DISCUSSION POINTS
• [point]
• [point]

DECISIONS MADE
• [decision]

2-WEEK SPRINT PLAN
The founder committed to:
• [sprint item] — [deadline/milestone]
• [sprint item]

MINIMUM COMMITMENTS (before next session)
• Founder: [commitment]
• Coach: [commitment]

SUPPORT NEEDED
• [introduction/resource/advisor needed]

ACTION ITEMS — FOUNDER
• [item] (due: [date])

ACTION ITEMS — COACH
• [item] (due: [date])
```

### Step 4: Draft Follow-Up Email
Generate a brief (200 words max) follow-up email to the founder:
- Personal opener referencing something specific from the session
- Recap of commitments (theirs and yours)
- One concrete next step
- Warm close
- Signed from the coach's name, sent via residency@voltaeffect.com

### Step 5: Offer to Write Back
Offer to write action items to Supabase:
```sql
INSERT INTO action_items (company_id, text, owner_type, status, meeting_date)
VALUES ('[company_id]', '[item]', '[volta|founder]', 'open', CURRENT_DATE);
```

Wait for coach approval before writing.

### Transcript Privacy Rules
- Only use content from the recorded portion of the meeting
- If the transcript shows the notetaker was removed ("kick out the note taker"), everything after is off-record
- Never include off-record content in reports, emails, or documentation
- When in doubt: less is more

## Reference Files

- `references/stage-coaching-guide.md` — Full coaching focus per milestone stage
- `references/post-session-template.md` — Report and email templates
- `references/transcript-privacy.md` — Privacy rules for Fireflies content

## Quality Standards

- Always pull live data before generating prep briefs — never use stale context
- Flag data gaps explicitly rather than working around them
- Prep briefs should be scannable in 2 minutes — not a novel
- Post-session reports should be copy-pasteable into Impact OS
- Follow-up emails use Volta's direct, personal voice — not corporate language
- Never write to Supabase without coach approval
