---
name: openclaw-executive-assistant-webinar
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - how do I build an executive assistant with OpenClaw
  - set up local-first OpenClaw workflows
  - create data intake review with OpenClaw
  - build operational memory system with OpenClaw
  - triage emails offline with OpenClaw
  - implement OpenClaw assistant workflows
  - use OpenClaw for communications triage
  - build AI executive assistant locally
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

This project provides a hands-on workshop for building local-first AI executive assistant workflows using OpenClaw. It demonstrates three core patterns: **data intake review**, **operational memory**, and **offline communications triage**. All workflows operate on local files and produce reviewable markdown artifacts without requiring live integrations.

The webinar teaches practical AI agent patterns for:
- Processing unknown files into structured reports
- Converting work residue into daily/weekly momentum docs
- Triaging exported emails into action lists

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/              # Files to inspect
│   ├── prompts/intake-review.md
│   ├── outputs/               # Generated reports
│   └── expected/report-outline.md
├── 02-operational-memory/
│   ├── inbox/                 # Work notes and residue
│   ├── prompts/daily-log.md
│   ├── prompts/weekly-hype.md
│   ├── outputs/
│   └── schedule/              # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/                   # Exported email files
│   ├── prompts/email-triage.md
│   ├── outputs/
│   └── expected/report-outline.md
└── mission-control/           # Optional dashboard
```

## Getting Started

### Prerequisites

- An AI coding agent (Claude Code, Cursor, Codex, etc.)
- Access to the workshop repository
- Text editor for reviewing markdown outputs

### Installation

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

### Workshop Flow

1. Open `webinar-runbook.html` in a browser
2. Keep the `code-along/` folder visible in your editor
3. Work through exercises sequentially
4. Copy/paste prompts to your AI agent
5. Review generated markdown outputs

## Exercise 1: Data Intake Review

**Goal:** Transform unknown files into a trustworthy intake report.

### Directory Setup

```
01-data-intake-review/
├── incoming/          # Place files to review here
├── prompts/intake-review.md
├── outputs/
└── expected/report-outline.md
```

### Workflow

1. Add files to `incoming/` directory
2. Read the prompt from `prompts/intake-review.md`
3. Run AI agent against the incoming files with the prompt
4. Generate `outputs/intake-review.md`

### Expected Output

The report should include:
- File inventory (name, type, size)
- Content summary for each file
- Risk assessment
- Recommended actions
- Priority classification

### Example Prompt Pattern

```markdown
# Data Intake Review

Review all files in the incoming/ directory and create a structured report:

1. File Inventory
   - List each file with metadata
   
2. Content Analysis
   - Summarize contents
   - Identify file purpose
   
3. Risk Assessment
   - Flag sensitive data
   - Note security concerns
   
4. Action Items
   - Recommended next steps
   - Priority level
```

## Exercise 2: Operational Memory

**Goal:** Convert work residue into daily logs and weekly summaries.

### Directory Setup

```
02-operational-memory/
├── inbox/             # Notes, drafts, work artifacts
├── prompts/
│   ├── daily-log.md
│   └── weekly-hype.md
├── outputs/
└── schedule/
    ├── cron-examples.md
    └── heartbeat-note.md
```

### Daily Log Workflow

1. Collect work artifacts in `inbox/`
2. Use `prompts/daily-log.md` with your AI agent
3. Generate `outputs/daily-log.md`

### Weekly Summary Workflow

1. Accumulate daily logs throughout the week
2. Use `prompts/weekly-hype.md` with your AI agent
3. Generate `outputs/weekly-hype.md`

### Example Daily Log Prompt

```markdown
# Daily Log Generator

Review all files in inbox/ and create a daily log:

## What Got Done
- List completed items
- Include links to artifacts

## In Progress
- Current work streams
- Blockers

## Tomorrow's Focus
- Top 3 priorities
- Dependencies

## Notes & Insights
- Learnings
- Ideas to revisit
```

### Automation Pattern

Set up cron jobs to trigger daily/weekly processing:

```bash
# Daily log at 5pm
0 17 * * * cd /path/to/code-along/02-operational-memory && /usr/local/bin/process-daily-log.sh

# Weekly summary on Friday at 4pm
0 16 * * 5 cd /path/to/code-along/02-operational-memory && /usr/local/bin/process-weekly-hype.sh
```

See `schedule/cron-examples.md` for complete automation examples.

## Exercise 3: Offline Communications Triage

**Goal:** Process exported emails into actionable lists.

### Directory Setup

```
03-offline-communications-triage/
├── eml/               # Exported .eml files
├── prompts/email-triage.md
├── outputs/
└── expected/report-outline.md
```

### Workflow

1. Export emails as `.eml` files to `eml/` directory
2. Use `prompts/email-triage.md` with your AI agent
3. Generate `outputs/email-triage.md`

### Example Triage Prompt

```markdown
# Email Triage

Process all .eml files and create an action report:

## High Priority
- Urgent requests requiring immediate response
- Include sender, subject, deadline

## Needs Response
- Emails requiring thoughtful reply
- Suggested response approach

## FYI / Archive
- Informational items
- Can be archived

## Waiting On
- Pending responses from others
- Track follow-up dates

## Delegate
- Items for team members
- Suggested assignee
```

### Expected Output Structure

```markdown
# Email Triage Report
Generated: 2026-05-15

## Summary
- Total emails processed: 47
- High priority: 3
- Needs response: 12
- FYI: 28
- Waiting on: 4

## High Priority

### Re: Q2 Budget Approval Needed
- **From:** CFO
- **Date:** 2026-05-14
- **Deadline:** EOD Friday
- **Action:** Review attached spreadsheet and approve

[Detailed breakdown continues...]
```

## Mission Control (Optional)

Create a simple dashboard linking to all generated artifacts:

```markdown
# Mission Control Dashboard

## Today's Artifacts
- [Daily Log](../02-operational-memory/outputs/daily-log.md)
- [Email Triage](../03-offline-communications-triage/outputs/email-triage.md)

## This Week
- [Weekly Hype](../02-operational-memory/outputs/weekly-hype.md)
- [Intake Reviews](../01-data-intake-review/outputs/)

## Archive
- [Previous Weeks](./archive/)
```

## Key Principles

### Local-First Architecture
- All processing happens on local files
- No API calls or external services required
- Complete data privacy and control

### Reviewable Artifacts
- All outputs are markdown
- Human-readable and version-controllable
- Easy to edit and refine

### Copy/Paste Workflows
- Prompts are templates to customize
- No complex tooling required
- Works with any AI coding agent

## Common Patterns

### File Organization

Always maintain clear input/output separation:

```
exercise-name/
├── inputs/     # Source material
├── prompts/    # AI instructions
├── outputs/    # Generated artifacts
└── expected/   # Reference examples
```

### Prompt Engineering

Structure prompts with:
1. **Context**: What files to process
2. **Task**: What to generate
3. **Format**: Expected structure
4. **Tone**: Professional, concise, actionable

### Incremental Processing

Build habits around regular processing:
- Daily: Process work artifacts
- Weekly: Summarize progress
- Monthly: Review patterns and optimize

## Troubleshooting

### AI Agent Not Finding Files

Ensure you're running the agent from the correct directory:

```bash
cd code-along/01-data-intake-review
# Then invoke your AI agent
```

### Incomplete Reports

Check that:
- All source files are in the designated input directory
- Prompts include clear formatting instructions
- AI agent has sufficient context window

### Automation Not Running

Verify cron job setup:

```bash
# List current cron jobs
crontab -l

# Edit cron jobs
crontab -e

# Check cron logs
grep CRON /var/log/syslog
```

## Best Practices

1. **Version control outputs**: Track generated reports in git to see evolution
2. **Customize prompts**: Adapt templates to your workflow needs
3. **Regular cadence**: Set consistent processing schedules
4. **Review and refine**: AI outputs are starting points, not final drafts
5. **Archive old artifacts**: Keep mission control clean and current

## Extensions

### Add New Workflows

Create additional exercises following the pattern:

```
code-along/04-your-workflow/
├── inputs/
├── prompts/your-prompt.md
├── outputs/
└── README.md
```

### Integrate with Tools

While the workshop is local-first, outputs can feed into:
- Task managers (import markdown)
- Note-taking apps (Obsidian, Notion)
- Calendar apps (parse action items)

### Build Custom Dashboards

Use `mission-control/` to create HTML dashboards:

```html
<!DOCTYPE html>
<html>
<head><title>My Executive Assistant</title></head>
<body>
  <h1>Mission Control</h1>
  <ul>
    <li><a href="../02-operational-memory/outputs/daily-log.md">Today's Log</a></li>
    <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
  </ul>
</body>
</html>
```

## Resources

- Workshop Runbook: `webinar-runbook.html`
- Exercise Index: `code-along/INDEX.md`
- DataCamp Webinar: https://www.datacamp.com/webinars/build-your-own-executive-assistant-with-openclaw
