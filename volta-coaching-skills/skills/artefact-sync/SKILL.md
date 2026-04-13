---
name: artefact-sync
description: This skill should be used when any coaching methodology, process, framework, or terminology changes and needs to be propagated across all Volta artefacts. Also activates for "sync artefacts", "update all docs", "propagate change", "keep everything aligned", "check for inconsistencies", "something changed and I need to update everywhere", or "make sure all sites match".
version: 1.0.0
---

# Artefact Sync

Propagate changes across all Volta coaching artefacts when methodology, processes, frameworks, or terminology evolve. Ensures consistency across the handover hub, coach playbook, Claude plugin, and any other documentation.

## When to Use

Invoke this skill whenever:
- A coaching framework element changes (e.g., Foundation Sprint becomes optional, a new milestone stage is added, risk scoring criteria change)
- Terminology shifts (e.g., a role name changes, a program is renamed)
- A process is updated (e.g., monthly evaluation cycle adds a step)
- New content is added that should be reflected across multiple artefacts
- You want to verify everything is still consistent after a series of changes

## The Artefact Map

All Volta coaching content lives in these locations:

### 1. Handover Hub (Laura/Matt — operations)
Location: `/Volumes/Seagate/Volta Strategic Planning/handover/`
Deployed: https://volta-startup-hub.netlify.app
Files:
- `index.html` — hub landing page
- `startup-success-playbook.html` — residency ops manual
- `ops-calendar.html` — monthly operations visual
- `monthly-evaluation-cycle.html` — evaluation process + risk framework
- `interview-guide.html` — V5.0 interview guide + interactive scorecard
- `coaching-operations.html` — session workflows, Fireflies, coaching intelligence
- `coach-profiles.html` — 5 coach profiles + matching guide
- `data-systems.html` — Supabase, accounts, data flows
- `company-updates-transition.html` — form-to-agent migration
- `community-builders.html` — community ops, Sprint, builder-to-Lab pipeline
- `role-clarity.html` — post-May 6 org structure, decision rights
- `jaco-contracted.html` — Jaco's contracted role
- `coaching-playbook.html` — full methodology (3 pillars, stages, experiments, red flags)

### 2. Coach Playbook (Coaches — methodology)
Location: `/Volumes/Seagate/Volta Strategic Planning/coach-playbook/`
Deployed: https://volta-coach-playbook.netlify.app
Files:
- `index.html` — coach dashboard
- `stage-guide.html` — interactive 5-stage selector
- `session-prep.html` — before/during/after workflow
- `problem-hypothesis.html` — template + interactive builder
- `methodology.html` — three pillars overview
- `risk-framework.html` — 4 risks + interactive sliders
- `experiments.html` — catalog by stage + builder
- `red-flags.html` — warning signs by observation
- `accountability.html` — commitments, off-ramps
- `foundation-sprint.html` — optional workshop tool
- `ai-landscape.html` — living quarterly updates

### 3. Claude Plugin — Community Ops (Laura's skills)
Location: `/Volumes/Seagate/Volta Strategic Planning/volta-community-ops-plugin/`
Files:
- `skills/review-applications/SKILL.md` + `references/` (rubric, templates, pipeline, history, interview guide v5)
- `skills/portfolio-check/SKILL.md`
- `agents/` (application-screener, risk-assessor, context-builder, comms-drafter, data-auditor)
- `references/` (volta-context, ops-calendar, supabase-queries)

### 4. Claude Plugin — Coaching Skills (Coach tools)
Location: `/Volumes/Seagate/Volta Strategic Planning/volta-coaching-skills/`
Files: This plugin's own skills and references.

### 5. Design Specs
Location: `/Volumes/Seagate/Volta Strategic Planning/docs/superpowers/specs/`
Files:
- `2026-04-08-founder-interview-guide-v5-design.md`

## Sync Process

### Step 1: Identify the Change
Ask the user: "What changed?" Get the specific detail — a term, a framework element, a process step, a threshold, a role name.

### Step 2: Search All Artefacts
Use Grep to search ALL locations in the artefact map for the affected term/concept. Search across:
- All `.html` files in `handover/` and `coach-playbook/`
- All `.md` files in `volta-community-ops-plugin/` and `volta-coaching-skills/`
- All `.md` files in `docs/superpowers/specs/`

Report every occurrence with file path and line number.

### Step 3: Present the Change Plan
Show the user:
- Every file that needs updating
- The current text in each location
- The proposed replacement text
- Any locations where the change doesn't apply (and why)

Wait for user approval before making changes.

### Step 4: Execute Changes
Apply the approved changes across all files. Use Edit tool for each file.

### Step 5: Redeploy
After changes are complete, redeploy both Netlify sites:
```bash
cd "/Volumes/Seagate/Volta Strategic Planning"
netlify deploy --dir=handover --site=1b9c4ce1-e404-4141-8d12-423e517dbcc3 --prod
netlify deploy --dir=coach-playbook --site=00e370de-5f19-4ce4-b60a-a242d30ef461 --prod
```

### Step 6: Verify
Confirm all changes are live. Report the count of files updated and sites redeployed.

## Consistency Check Mode

When invoked with "check for inconsistencies" or "verify alignment":

1. Read the coaching-methodology.md reference file as the source of truth
2. Compare key terms and frameworks against all artefact locations
3. Report any discrepancies: terms that differ, numbers that don't match, processes described differently
4. Propose corrections

## Reference Files

- `references/artefact-map.md` — Complete map of all Volta coaching artefacts with file paths
- `references/netlify-deploy.md` — Deployment commands and site IDs

## Quality Standards

- Never update a file without showing the user the before/after
- Never redeploy without confirming all changes are complete
- If a change affects the interactive elements (scorecards, sliders, builders), verify the JavaScript still works
- Report any files where the change intentionally does NOT apply
