---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - build an executive assistant with OpenClaw
  - set up OpenClaw workflow automation
  - create local-first AI assistant
  - process emails with OpenClaw offline
  - generate daily logs with AI assistant
  - triage communications with OpenClaw
  - OpenClaw operational memory setup
  - local AI workflow with OpenClaw
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

This project provides a workshop framework for building local-first AI executive assistant workflows using OpenClaw. It demonstrates three core patterns:

1. **Data intake review** — Turn unknown files into trustworthy intake reports
2. **Operational memory** — Convert work residue into daily/weekly momentum docs
3. **Offline communications triage** — Transform exported emails into action lists

Everything stays in local folders and produces reviewable markdown artifacts with no live integrations required.

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/
│   │   └── intake-review.md
│   ├── outputs/           # Generated reports
│   └── expected/
│       └── report-outline.md
├── 02-operational-memory/
│   ├── inbox/             # Notes and work residue
│   ├── prompts/
│   │   ├── daily-log.md
│   │   └── weekly-hype.md
│   ├── outputs/
│   ├── schedule/
│   │   ├── cron-examples.md
│   │   └── heartbeat-note.md
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/
│   │   └── email-triage.md
│   ├── outputs/
│   └── expected/
│       └── report-outline.md
└── mission-control/       # Optional dashboard
```

## Getting Started

### Setup

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Navigate to workshop folder
cd code-along

# Open the walkthrough in browser
open ../webinar-runbook.html
```

### Prerequisites

- OpenClaw CLI or API access
- Text editor
- Terminal/command line access
- Email client that can export .eml files (for exercise 3)

## Exercise 1: Data Intake Review

Convert unknown files into a structured intake report.

```bash
cd code-along/01-data-intake-review
```

### Workflow

1. Place files to review in `incoming/`
2. Read the prompt template from `prompts/intake-review.md`
3. Execute OpenClaw with the prompt against `incoming/` folder
4. Generate `outputs/intake-review.md`

### Example Prompt Pattern

```markdown
# Data Intake Review Prompt

Analyze all files in the incoming/ directory and create a structured intake report:

## Files Analyzed
- List each file with type and size

## Key Findings
- Extract main topics, dates, and priorities

## Recommended Actions
- Suggest next steps for each file

## Red Flags
- Highlight anything requiring immediate attention

Output format: Markdown
```

### Expected Output Structure

```markdown
# Intake Review Report
Generated: YYYY-MM-DD HH:MM

## Summary
Brief overview of all files analyzed

## File Inventory
| File | Type | Size | Priority |
|------|------|------|----------|

## Action Items
- [ ] High priority action 1
- [ ] Medium priority action 2

## Notes
Additional context and observations
```

## Exercise 2: Operational Memory

Transform daily work residue into momentum documentation.

```bash
cd code-along/02-operational-memory
```

### Daily Log Pattern

```bash
# Read work residue from inbox/
# Apply prompts/daily-log.md prompt
# Generate outputs/daily-log.md
```

### Daily Log Prompt Template

```markdown
# Daily Log Prompt

Review all notes in inbox/ from today and create:

## Completed Today
- What got done

## Decisions Made
- Key choices and rationale

## Blockers
- What's stuck and why

## Tomorrow's Focus
- Top 3 priorities

Keep it concise. Use bullet points.
```

### Weekly Summary Pattern

```bash
# Aggregate 5-7 daily logs
# Apply prompts/weekly-hype.md prompt
# Generate outputs/weekly-hype.md
```

### Weekly Hype Prompt Template

```markdown
# Weekly Hype Prompt

Synthesize the week's daily logs into:

## Wins
- Major accomplishments
- Shipped features
- Problems solved

## Momentum
- Patterns in progress
- Velocity indicators

## Next Week
- Carried-over priorities
- New initiatives

Tone: Energizing but honest
```

### Automation with Cron

```bash
# Daily log generation (9 PM every day)
0 21 * * * cd ~/code-along/02-operational-memory && openclaw generate daily-log

# Weekly summary (Friday 5 PM)
0 17 * * 5 cd ~/code-along/02-operational-memory && openclaw generate weekly-hype
```

### Expected Outputs

**outputs/daily-log.md:**
```markdown
# Daily Log — 2026-05-11

## ✅ Completed
- Shipped intake review feature
- Fixed email parsing bug

## 🧭 Decisions
- Using markdown for all outputs (portability)

## 🚧 Blockers
- None

## 🎯 Tomorrow
1. Test communications triage
2. Document cron setup
3. Review mission control dashboard
```

**outputs/weekly-hype.md:**
```markdown
# Weekly Hype — Week of May 5, 2026

## 🏆 Wins
- All three exercises shipped and tested
- Zero critical bugs
- 5 community contributions

## 📈 Momentum
- Daily generation running smoothly
- Adoption growing (28 stars)

## ➡️ Next Week
- Add mission control dashboard
- Document API integration patterns
```

## Exercise 3: Offline Communications Triage

Turn exported emails into actionable lists.

```bash
cd code-along/03-offline-communications-triage
```

### Workflow

1. Export emails from your client to `eml/` folder as .eml files
2. Read `prompts/email-triage.md`
3. Run OpenClaw against `eml/` directory
4. Generate `outputs/email-triage.md`

### Email Triage Prompt Template

```markdown
# Email Triage Prompt

Process all .eml files and categorize:

## Needs Response
- Urgent replies required
- Include: sender, subject, deadline

## FYI / Read
- Informational only
- Summarize key points

## Waiting On Others
- Threads you're blocked on

## Archive
- No action needed

For each, extract: sender, date, subject, 1-line summary
```

### Expected Output

```markdown
# Email Triage Report
Processed: 2026-05-11 14:30

## 🚨 Needs Response (3)

### From: client@example.com
**Subject:** Q2 Budget Approval Needed
**Deadline:** May 15
**Summary:** Awaiting sign-off on $50K spend
**Action:** Review budget doc and approve

---

## 📖 FYI / Read (7)

### From: team@company.com
**Subject:** Weekly Newsletter
**Summary:** Product updates, new hires, upcoming events

---

## ⏳ Waiting On Others (2)

### From: vendor@service.com
**Subject:** API Access Request
**Sent:** May 8
**Summary:** Waiting for credentials (3 days)

---

## 📦 Archive (12)

- Automated notifications (5)
- Old threads resolved (7)
```

## Mission Control (Optional)

Create a simple dashboard linking to all generated artifacts.

```bash
cd code-along/mission-control
```

### Example Dashboard Structure

```markdown
# Mission Control Dashboard

## Latest Artifacts

### Today
- [Daily Log](../02-operational-memory/outputs/daily-log.md)
- [Email Triage](../03-offline-communications-triage/outputs/email-triage.md)

### This Week
- [Weekly Hype](../02-operational-memory/outputs/weekly-hype.md)

### Recent Intakes
- [May 11 Intake](../01-data-intake-review/outputs/intake-review.md)

## Quick Links
- [All Daily Logs](../02-operational-memory/outputs/)
- [All Triage Reports](../03-offline-communications-triage/outputs/)
```

## Best Practices

### Local-First Principles

- All data stays on disk
- No API calls to external services during workshop
- Reviewable markdown for all outputs
- Version control friendly

### Prompt Engineering

- Be specific about output format (markdown, structure)
- Include examples in prompts when helpful
- Request concise bullet points over prose
- Specify tone (factual, energizing, etc.)

### File Organization

```bash
# Keep consistent naming
daily-log-YYYY-MM-DD.md
weekly-hype-YYYY-Www.md
intake-review-YYYY-MM-DD.md
email-triage-YYYY-MM-DD.md

# Archive old outputs
mkdir outputs/archive/2026-04/
mv outputs/daily-log-2026-04-*.md outputs/archive/2026-04/
```

## Common Patterns

### Chain Multiple Prompts

```bash
# Generate daily log
openclaw --prompt prompts/daily-log.md --input inbox/ --output outputs/daily-log.md

# Use daily log as input for planning
openclaw --prompt prompts/tomorrow-plan.md --input outputs/daily-log.md --output outputs/tomorrow.md
```

### Environment Variables

```bash
# Set OpenClaw config
export OPENCLAW_MODEL="claude-3-5-sonnet"
export OPENCLAW_MAX_TOKENS=4096
export OPENCLAW_TEMPERATURE=0.3
```

## Troubleshooting

### No Output Generated

- Check input folder has files
- Verify prompt file exists and is readable
- Ensure output directory has write permissions

### Output Too Verbose

- Add "Be concise" to prompt
- Specify bullet points over paragraphs
- Request summaries over full details

### Missing Context

- Include more examples in prompt
- Reference expected output structure
- Add "Focus on X, Y, Z" constraints

### Cron Jobs Not Running

```bash
# Check cron is running
pgrep cron

# Verify cron syntax
crontab -l

# Check logs
grep CRON /var/log/syslog

# Use absolute paths in crontab
0 21 * * * /usr/local/bin/openclaw generate daily-log
```

## Integration Ideas

- **Git hooks**: Auto-generate intake reports on new files
- **Calendar sync**: Export calendar to markdown, process with operational memory
- **Slack export**: Download workspace history, run triage
- **Meeting notes**: Parse transcripts, extract action items

## Resources

- Workshop walkthrough: `webinar-runbook.html`
- DataCamp webinar: https://www.datacamp.com/webinars/build-your-own-executive-assistant-with-openclaw
