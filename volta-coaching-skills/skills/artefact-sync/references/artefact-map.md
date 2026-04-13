# Volta Coaching Artefact Map

Complete inventory of all files containing coaching methodology, processes, and frameworks.

## Handover Hub
**Location:** `/Volumes/Seagate/Volta Strategic Planning/handover/`
**Netlify Site ID:** `1b9c4ce1-e404-4141-8d12-423e517dbcc3`
**URL:** https://volta-startup-hub.netlify.app
**Password:** VoltaStrat2026 (key: volta_auth)

| File | Primary Content |
|------|----------------|
| index.html | Hub navigation, page count stat |
| startup-success-playbook.html | Residency operations, milestone framework, coach roster, monthly calendar |
| ops-calendar.html | Weekly ops rhythm, daily habits, monthly checklist |
| monthly-evaluation-cycle.html | 4-risk framework, evaluation process, decision thresholds |
| interview-guide.html | V5.0 interview dimensions, scoring, interactive scorecard with Supabase search |
| coaching-operations.html | Session workflow, Fireflies, coaching intelligence, commitment tracking |
| coach-profiles.html | 5 coach profiles, matching criteria, quick reference grid |
| data-systems.html | Supabase schema, accounts, data flows, what stops May 6 |
| company-updates-transition.html | Form-to-agent migration plan |
| community-builders.html | Community ops, Sprint program, builder-to-Lab pipeline |
| role-clarity.html | Post-May 6 org structure, decision rights, escalation |
| jaco-contracted.html | Jaco's contracted coach arrangement |
| coaching-playbook.html | FULL METHODOLOGY: 3 pillars, stages, experiments, red flags, coaching arc, accountability |

## Coach Playbook
**Location:** `/Volumes/Seagate/Volta Strategic Planning/coach-playbook/`
**Netlify Site ID:** `00e370de-5f19-4ce4-b60a-a242d30ef461`
**URL:** https://volta-coach-playbook.netlify.app
**Password:** VoltaCoach2026 (key: volta_coach_auth)

| File | Primary Content |
|------|----------------|
| index.html | Coach dashboard, navigation cards |
| stage-guide.html | Interactive 5-stage selector with full coaching guidance |
| session-prep.html | Before/During/After workflow, checklist, scenarios |
| problem-hypothesis.html | Template, component breakdown, TWU example, interactive builder |
| methodology.html | Three pillars, coaching arc, two models, success patterns |
| risk-framework.html | 4 risks, stage shift table, assessment questions, interactive sliders |
| experiments.html | 20 experiments by stage, interactive builder |
| red-flags.html | 8 observable warning signs |
| accountability.html | 20% MoM, escalation path, off-ramp language |
| foundation-sprint.html | Optional workshop tool, "Click" book reference |
| ai-landscape.html | Living quarterly section, AI assessment questions |

## Community Ops Plugin
**Location:** `/Volumes/Seagate/Volta Strategic Planning/volta-community-ops-plugin/`

| File | Primary Content |
|------|----------------|
| skills/review-applications/SKILL.md | 3-stage application review workflow |
| skills/review-applications/references/application-rubric.md | Unified v1.1 rubric (screening + interview + readiness) |
| skills/review-applications/references/interview-guide-v5.md | V5.0 interview framework spec |
| skills/review-applications/references/email-templates.md | All application response templates |
| skills/review-applications/references/application-pipeline.md | Pipeline stages, SQL queries |
| skills/review-applications/references/assessment-history.md | 15 prior screening decisions |
| skills/portfolio-check/SKILL.md | Data hygiene + portfolio snapshot |
| agents/application-screener.md | Stage 1 rubric scoring |
| agents/risk-assessor.md | 4-dimension risk framework |
| agents/context-builder.md | Company context from Supabase |
| agents/comms-drafter.md | Email drafting in Volta voice |
| agents/data-auditor.md | Data freshness checks |
| references/volta-context.md | Org structure, team, terminology |
| references/ops-calendar.md | Monthly rhythm reference |
| references/supabase-queries.md | Common SQL patterns |

## Coaching Skills Plugin
**Location:** `/Volumes/Seagate/Volta Strategic Planning/volta-coaching-skills/`

| File | Primary Content |
|------|----------------|
| skills/artefact-sync/SKILL.md | This skill — cross-artefact synchronization |
| skills/coach-data-query/SKILL.md | Coach data queries against Supabase |
| skills/session-workflow/SKILL.md | Pre/post session coaching workflow |
| skills/methodology-reference/SKILL.md | Quick methodology lookup |

## Design Specs
**Location:** `/Volumes/Seagate/Volta Strategic Planning/docs/superpowers/specs/`

| File | Primary Content |
|------|----------------|
| 2026-04-08-founder-interview-guide-v5-design.md | V5.0 interview guide full spec |

## Deployment Commands

```bash
# Redeploy handover hub
cd "/Volumes/Seagate/Volta Strategic Planning"
netlify deploy --dir=handover --site=1b9c4ce1-e404-4141-8d12-423e517dbcc3 --prod

# Redeploy coach playbook
netlify deploy --dir=coach-playbook --site=00e370de-5f19-4ce4-b60a-a242d30ef461 --prod
```
