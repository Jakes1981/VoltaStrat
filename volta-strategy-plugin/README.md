# Volta Strategy Plugin

An agentic strategic planning system for Volta, built on the principle that **agents handle consistency, synthesis, and analysis — humans make the judgments that matter**.

## Philosophy

Volta's competitive edge is the quality of its human relationships — with founders, builders, SMEs, and funders. AI agents protect that edge by absorbing the work that doesn't require human judgment: data synthesis, pattern recognition, draft generation, consistency checking, and progress tracking. This frees the team to do what only humans can do well: build trust, make nuanced calls, and lead with values.

## Plugin Structure

```
volta-strategy-plugin/
├── plugin.json
├── README.md
├── skills/
│   └── volta-strategic-planning/
│       ├── SKILL.md                    # Core planning skill
│       └── references/
│           ├── agentic-model.md        # Agent architecture & human oversight model
│           ├── volta-context.md        # Volta-specific context (OKRs, roles, metrics)
│           └── planning-frameworks.md  # Frameworks used across planning work
└── agents/
    ├── strategy-diagnostician.md       # Gap analysis: current state vs. strategy
    ├── session-synthesizer.md          # Planning session → decisions + action list
    ├── okr-tracker.md                  # Progress monitoring + risk flagging
    ├── stakeholder-brief-writer.md     # Board, funder, and team communications
    └── portfolio-analyzer.md           # Startup portfolio health + pattern analysis
```

## Agents Overview

| Agent | Trigger | Human Oversight |
|-------|---------|-----------------|
| `strategy-diagnostician` | "diagnose our strategy", "where are we vs plan" | Review gap analysis before sharing |
| `session-synthesizer` | After planning session transcripts provided | Approve action list, assign owners |
| `okr-tracker` | "how are we tracking", "OKR update" | Flag interventions needed |
| `stakeholder-brief-writer` | "draft board report", "write funder update" | Always review before sending |
| `portfolio-analyzer` | "portfolio health", "how are our startups doing" | Go/no-go decisions stay human |

## Human-AI Boundary

### Agents Do
- Synthesize transcripts into decisions and action items
- Monitor OKR progress against targets
- Generate first drafts of reports and communications
- Identify patterns across portfolio data
- Flag risks and anomalies for human review
- Maintain consistency in frameworks and language

### Humans Decide
- All go/no-go decisions on individual founders
- Funder relationship strategy and ask timing
- Compensation and hiring decisions
- Community conflict resolution
- Any decision involving a specific person's future at Volta
- Final approval on all external communications

## Installation

```bash
# Add to ~/.claude/plugins/installed_plugins.json
{
  "plugins": {
    "volta-strategy-plugin": {
      "path": "/Volumes/Extreme SSD/Volta Strategic Planning/volta-strategy-plugin"
    }
  }
}
```
