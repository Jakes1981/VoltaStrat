# Volta Agentic Operating Model

## Design Principle

Volta is building a human-centric organization where AI agents handle the work that benefits from consistency, speed, and synthesis — so humans can do the work that requires judgment, trust, and relationships. The goal is not to automate the community. It is to protect the community from the overhead that would otherwise erode it.

**The test for any agent deployment:**
> "Does this free a human to do something more valuable — or does it replace a human interaction that creates trust?"

If the answer is "replace trust-building," the agent should not be deployed there.

---

## Agent Architecture for Strategic Planning

### Tier 1: Continuous Monitoring (Weekly/Automated)

These agents run on a cadence without requiring a human to trigger them. They surface signals for human review.

#### `okr-tracker`
- **Runs:** Weekly, every Monday
- **Inputs:** Impact OS data, Lab engagement tracker, Pulse responses
- **Outputs:** OKR progress dashboard, risk flags, trend lines
- **Human action required when:** Any metric is >15% behind target, or a leading indicator has declined for 2 consecutive weeks

#### `portfolio-analyzer`
- **Runs:** Bi-weekly, aligned with coach check-in cycles
- **Inputs:** Company update data from Impact OS, revenue stage records, coach engagement logs
- **Outputs:** Portfolio health snapshot, companies needing intervention, cohort analysis
- **Human action required when:** A company has not submitted an update in 30+ days, or revenue stage has not progressed in 60+ days

---

### Tier 2: Session Support (Before/After Key Meetings)

These agents are triggered by a human ahead of a planning session, board meeting, or quarterly review.

#### `strategy-diagnostician`
- **Trigger:** 48-72 hours before a quarterly planning session
- **Inputs:** Strategic plan, current OKR data, prior session decisions, external signals
- **Outputs:** Gap analysis (current vs. targets), risk inventory, 3-5 strategic questions for human discussion
- **Human action required:** Review and validate the diagnosis before the session — agent can miss context it doesn't have access to

#### `session-synthesizer`
- **Trigger:** Within 24 hours after a planning session (provide transcript)
- **Inputs:** Session transcript(s), prior action list
- **Outputs:** Decision log, action list (owner, item, deadline), open questions, parking lot items
- **Human action required:** Approve the action list before distributing to team

#### `stakeholder-brief-writer`
- **Trigger:** 1 week before board meeting, funder report, or team all-hands
- **Inputs:** OKR data, portfolio snapshot, Lab performance data, community metrics, prior communications
- **Outputs:** Draft communication appropriate for audience (board, ACOA, Province, team)
- **Human action required:** Full review before any external communication. Never send agent output without human approval.

---

### Tier 3: Deep Analysis (On Demand)

These agents run when a human asks a specific strategic question and needs thorough analysis.

#### `strategy-diagnostician` (deep mode)
- **Trigger:** Explicit request for strategic diagnosis
- **Scope:** Full strategic analysis including benchmarks, risk scenarios, option analysis
- **Time:** Can take 5-15 minutes to complete
- **Human action required:** Treat as input to a decision conversation, not as the decision

---

## Deployment Calendar

### Monthly Rhythm

| Week | Agent Activity | Human Activity |
|------|---------------|----------------|
| Week 1 | `portfolio-analyzer` runs; `okr-tracker` weekly check | Coach check-ins; eval decisions |
| Week 2 | `okr-tracker` weekly check | Team sync; founder updates processed |
| Week 3 | `portfolio-analyzer` runs; `okr-tracker` weekly check | Mid-month review; intervention calls |
| Week 4 | `okr-tracker` + prep for monthly reporting | Monthly report; planning for next month |

### Quarterly Rhythm

| Timing | Agent Activity | Human Activity |
|--------|---------------|----------------|
| -2 weeks | `strategy-diagnostician` triggered | CINO + CEO review diagnosis |
| -1 week | `stakeholder-brief-writer` (board prep) | Team prep session |
| Planning days | `session-synthesizer` active throughout | Full team planning session |
| +24 hours | `session-synthesizer` produces output | Approve and distribute action list |
| +1 week | `okr-tracker` rebaselines against new targets | Team commits to actions |

---

## Human-AI Handoff Protocols

### When an Agent Surfaces a Risk
1. Agent flags the risk with supporting data
2. Human reviews within 48 hours (for monitoring alerts) or before session (for planning alerts)
3. Human determines if intervention is needed
4. If yes: human takes the action (calls founder, contacts funder, reassigns resource)
5. Agent logs the intervention and monitors for change

### When an Agent Produces a Draft
1. Agent produces draft with clear labeling: "DRAFT — NOT FOR DISTRIBUTION"
2. Human reviews for accuracy, tone, relationship context
3. Human edits as needed
4. Human approves and sends under their own name
5. Agent never sends external communications autonomously

### When a Human Overrides an Agent's Analysis
1. Human notes the override and the reason
2. Agent incorporates the correction for future analysis
3. Override data is reviewed quarterly — if a pattern emerges, agent's model is updated

---

## Trust Architecture

Volta's community runs on trust. The agentic model must not erode it.

**What builders and founders should experience:**
- A human who knows them by name and context
- Responses that feel personal, not templated
- Feedback that reflects their specific situation
- Connections that feel like they came from someone who actually thought about them

**What agents handle behind the scenes:**
- Aggregating all the data that makes that human conversation informed
- Drafting the communication so the human can focus on personalizing it
- Tracking what was said and committed, so nothing falls through
- Flagging when someone needs a check-in, before they have to ask

**The rule:** The more trust-sensitive the interaction, the more human it should be.

---

## Scaling the Model

As Volta grows the agent capability, follow this sequence:

1. **Observe** — have a human do the task fully for 2+ cycles
2. **Document** — capture what the human is doing and deciding
3. **Assist** — agent produces first draft, human refines
4. **Augment** — agent handles routine cases, human handles exceptions
5. **Never automate** — any interaction where the quality of the human relationship is the product

Volta's community is the product. Protect it.
