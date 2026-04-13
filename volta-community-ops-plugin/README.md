# volta-community-ops-plugin

Operational toolkit for running Volta's 1:1 residency program and community operations.

## Skills

| Skill | Frequency | Purpose |
|-------|-----------|---------|
| `/review-applications` | Daily (30 min) | Screen new applications, apply rubric, draft comms |
| `/monthly-eval` | Monthly | Full evaluation cycle — risk assessment, seat decisions, close the loop |
| `/coach-prep` | Per session | Pre-session briefs, post-session follow-up, coach matching |
| `/portfolio-check` | Weekly | Data hygiene, staleness detection, portfolio snapshot |
| `/onboard-resident` | As needed | New resident onboarding checklist |
| `/outreach` | Monthly | Eligible team outreach and Founder Connect Day prep |

## Agents

| Agent | Purpose |
|-------|---------|
| `application-screener` | Applies Stage 1 screening rubric to application data |
| `risk-assessor` | Scores companies on 4-dimension risk framework |
| `context-builder` | Pulls and synthesizes company context from Supabase |
| `comms-drafter` | Drafts emails in Volta's voice |
| `data-auditor` | Checks data freshness, gaps, and consistency |

## Setup

Requires Supabase MCP tools connected to Volta's Impact OS (project: `keolicewwymohxahikei`).

## Installation

```bash
claude plugins add /path/to/volta-community-ops-plugin
```
