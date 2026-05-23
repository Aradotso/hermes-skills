---
name: openclaw-executive-assistant-workshop
description: Local-first OpenClaw workflows for building executive assistant automation with data intake, operational memory, and email triage
triggers:
  - how do I use OpenClaw for executive assistant tasks
  - set up OpenClaw data intake review workflow
  - create operational memory with OpenClaw
  - build email triage system with OpenClaw
  - OpenClaw local-first automation patterns
  - generate markdown reports with OpenClaw prompts
  - schedule OpenClaw tasks with cron
  - OpenClaw communications triage workflow
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This project provides a structured workshop for building local-first executive assistant automation using OpenClaw. It demonstrates three core workflows: data intake review, operational memory management, and offline communications triage. All operations stay local and produce reviewable markdown artifacts.

## What This Project Does

OpenClaw Executive Assistant Workshop teaches you to:

1. **Data Intake Review** — Transform unknown files into trustworthy intake reports
2. **Operational Memory** — Convert work residue into daily logs and weekly summaries
3. **Offline Communications Triage** — Turn exported emails into actionable task lists

Everything operates on local files with no live integrations, making it ideal for privacy-conscious automation.

## Installation

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the main walkthrough
open webinar-runbook.html
```

No dependencies required — this is a prompt-engineering workshop using local files.

## Project Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/
│   │   └── intake-review.md
│   ├── outputs/
│   └── expected/
│       └── report-outline.md
├── 02-operational-memory/
│   ├── inbox/             # Notes and work residue
│   ├── prompts/
│   │   ├── daily-log.md
│   │   └── weekly-hype.md
│   ├── outputs/
│   └── schedule/
│       ├── cron-examples.md
│       └── heartbeat-note.md
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/
│   │   └── email-triage.md
│   ├── outputs/
│   └── expected/
│       └── report-outline.md
└── mission-control/       # Optional dashboard
```

## Workflow 1: Data Intake Review

Transform incoming files into structured reports.

### Setup

```bash
cd code-along/01-data-intake-review
```

### Process

1. Place unknown files in `incoming/`
2. Read the prompt template: `prompts/intake-review.md`
3. Feed prompt + files to your AI assistant (Claude, ChatGPT, etc.)
4. Save output to `outputs/intake-review.md`

### Prompt Pattern

```markdown
# Data Intake Review Prompt

Review all files in the incoming/ directory and generate a report with:

- **File inventory**: List each file with type, size, last modified
- **Content summary**: Brief description of what each file contains
- **Risk assessment**: Security or privacy concerns
- **Action items**: What should be done with each file
- **Priority ranking**: High/Medium/Low urgency

Output format: Markdown with clear sections
```

### Expected Output Structure

```markdown
# Intake Review Report
Date: YYYY-MM-DD

## Executive Summary
[2-3 sentence overview]

## File Inventory
- `filename.ext` (type, size, modified date)
  - Summary: [brief description]
  - Risk: [none/low/medium/high]
  - Action: [file/archive/review/delete]
  - Priority: [high/medium/low]

## Recommended Next Steps
1. [Prioritized action]
2. [Prioritized action]
```

## Workflow 2: Operational Memory

Convert daily work residue into momentum documentation.

### Setup

```bash
cd code-along/02-operational-memory
```

### Daily Log

```markdown
# Daily Log Prompt (prompts/daily-log.md)

Review all files in inbox/ and generate a daily log with:

- **Today's completed work**: What got done
- **Open threads**: Conversations or tasks in progress
- **Blockers**: What's preventing progress
- **Tomorrow's priorities**: Top 3 things to tackle
- **Brain dump**: Quick captures, ideas, notes

Keep tone informal and actionable.
Output: outputs/daily-log.md
```

### Weekly Summary

```markdown
# Weekly Hype Prompt (prompts/weekly-hype.md)

Review the week's daily logs and generate a weekly summary with:

- **Week overview**: Major themes and accomplishments
- **Wins**: What went well (celebrate!)
- **Challenges**: What was hard or didn't work
- **Patterns**: Recurring themes or issues
- **Next week**: Strategic priorities and intentions

Tone: Positive, momentum-building, executive-friendly.
Output: outputs/weekly-hype.md
```

### Automation with Cron

```bash
# Daily log generation (runs at 6 PM weekdays)
0 18 * * 1-5 cd ~/path/to/02-operational-memory && your-ai-cli --prompt prompts/daily-log.md --output outputs/daily-log-$(date +\%Y-\%m-\%d).md

# Weekly summary (runs Friday at 5 PM)
0 17 * * 5 cd ~/path/to/02-operational-memory && your-ai-cli --prompt prompts/weekly-hype.md --output outputs/weekly-hype-$(date +\%Y-\%W).md
```

### Heartbeat Pattern

Create a recurring note that captures:
- Current projects and status
- Team velocity/capacity
- Upcoming deadlines
- Decision points needing attention

Store in `inbox/heartbeat-YYYY-MM-DD.md` and process with daily log.

## Workflow 3: Offline Communications Triage

Turn exported emails into action lists.

### Setup

```bash
cd code-along/03-offline-communications-triage
```

### Export Emails

Most email clients support exporting to `.eml` format:

- **Gmail**: Select emails → More → Download message
- **Outlook**: Drag email to desktop (saves as `.eml`)
- **Apple Mail**: File → Save As

Place exported `.eml` files in `eml/` directory.

### Triage Prompt

```markdown
# Email Triage Prompt (prompts/email-triage.md)

Review all .eml files and generate a triage report with:

## High Priority Actions
- [Sender] [Subject]: [Required action + deadline]

## Medium Priority
- [Sender] [Subject]: [Action needed]

## Low Priority / FYI
- [Sender] [Subject]: [One-line summary]

## Waiting On Others
- [What you're waiting for] from [who] by [when]

## Archive / No Action
- [Sender] [Subject]: [Why no action needed]

For each item, extract:
- Key decision points
- Hard deadlines
- People who need responses
- Information that updates existing projects
```

### Expected Output

```markdown
# Email Triage Report
Generated: YYYY-MM-DD HH:MM

## High Priority Actions
- **Jane Doe** "Q2 Budget Approval Needed" 
  - Action: Review and approve budget spreadsheet
  - Deadline: End of week
  - Link: [attachment or reference]

## Medium Priority
- **John Smith** "Meeting time for project kickoff?"
  - Action: Reply with availability next week
  - Context: New client onboarding

## Waiting On Others
- Design mockups from Sarah (promised by Tuesday)
- Legal review of contract from compliance team

## Archive / No Action
- **Newsletter** "Weekly industry roundup"
  - FYI only, no action required
```

## Common Patterns

### Prompt Template Structure

All prompts follow this pattern:

```markdown
# [Workflow Name] Prompt

## Context
[What you're working with]

## Task
[What the AI should do]

## Output Requirements
- Format: Markdown
- Structure: [Expected sections]
- Tone: [Professional/Casual/Executive-friendly]
- File: outputs/[filename].md

## Special Instructions
- [Any domain-specific guidance]
- [Privacy/security considerations]
- [Edge cases to handle]
```

### File Organization

```bash
# Keep prompts versioned
prompts/
  intake-review-v1.md
  intake-review-v2.md  # After refinement

# Archive outputs by date
outputs/
  2026-05-11-daily-log.md
  2026-05-11-intake-review.md
  archive/
    2026-05/
```

### Iterative Refinement

1. Run prompt with sample data
2. Review output quality
3. Update prompt with refinements
4. Document what works in prompt itself
5. Version your prompts

## Configuration

### Environment Variables

```bash
# If integrating with AI CLI tools
export OPENAI_API_KEY="your-key-here"
export ANTHROPIC_API_KEY="your-key-here"

# Workflow paths
export OPENCLAW_DATA_INTAKE="$HOME/openclaw/01-data-intake-review"
export OPENCLAW_MEMORY="$HOME/openclaw/02-operational-memory"
export OPENCLAW_TRIAGE="$HOME/openclaw/03-offline-communications-triage"
```

### Mission Control Dashboard

Create a simple `index.html` to link all outputs:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Executive Assistant Mission Control</title>
</head>
<body>
  <h1>Mission Control</h1>
  
  <h2>Recent Reports</h2>
  <ul>
    <li><a href="../01-data-intake-review/outputs/intake-review.md">Latest Intake Review</a></li>
    <li><a href="../02-operational-memory/outputs/daily-log.md">Today's Log</a></li>
    <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
  </ul>
  
  <h2>Weekly Summaries</h2>
  <ul>
    <li><a href="../02-operational-memory/outputs/weekly-hype.md">This Week</a></li>
  </ul>
</body>
</html>
```

## Troubleshooting

### Prompt Not Producing Expected Output

- Verify input files are in correct directory
- Check prompt instructions are clear and specific
- Add example output format to prompt
- Break complex prompts into smaller steps

### File Path Issues

```bash
# Use absolute paths in cron
0 18 * * 1-5 cd /full/path/to/02-operational-memory && ./generate.sh

# Or set working directory in script
#!/bin/bash
cd "$(dirname "$0")"
```

### Large Email Files

If `.eml` files are too large:

```bash
# Split into batches
mkdir eml/batch-1 eml/batch-2
# Process separately, then merge outputs
```

### Markdown Formatting Issues

- Ensure AI outputs valid markdown
- Use markdown linter: `markdownlint outputs/*.md`
- Preview outputs before committing

### Privacy Concerns

- Never commit sensitive data to git
- Add to `.gitignore`:

```
incoming/*
inbox/*
eml/*
outputs/*
!outputs/.gitkeep
```

## Best Practices

1. **Review before archiving** — Always human-verify AI outputs
2. **Version your prompts** — Track what works as you refine
3. **Keep originals** — Never delete source files until verified
4. **Automate gradually** — Start manual, automate proven workflows
5. **Document context** — Add notes about why decisions were made

## Resources

- Workshop walkthrough: `webinar-runbook.html`
- DataCamp webinar: https://www.datacamp.com/webinars/build-your-own-executive-assistant-with-openclaw
- Exercise index: `code-along/INDEX.md`
