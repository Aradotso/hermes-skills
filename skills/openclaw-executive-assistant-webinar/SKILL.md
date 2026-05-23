---
name: openclaw-executive-assistant-webinar
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - how do I build an executive assistant with OpenClaw
  - set up local OpenClaw workflows for task management
  - create data intake review with OpenClaw
  - build operational memory system with OpenClaw
  - triage emails offline with OpenClaw
  - implement daily and weekly logs with OpenClaw
  - use OpenClaw for local-first automation
  - create markdown artifacts with OpenClaw prompts
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This project provides a starter workshop for building local-first AI executive assistant workflows using OpenClaw. It demonstrates three core patterns: data intake review, operational memory management, and offline communications triage. All workflows operate on local files and produce reviewable markdown artifacts without requiring live integrations.

## What This Project Does

The webinar teaches you to build an AI-powered executive assistant that:

1. **Reviews incoming data** - Inspects unknown files and generates trustworthy intake reports
2. **Manages operational memory** - Converts work notes into daily logs and weekly summaries
3. **Triages communications** - Processes exported emails into actionable task lists

All operations are local-only, using copy/paste prompts with markdown outputs for transparency and review.

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/intake-review.md
│   ├── outputs/           # Generated reports
│   └── expected/report-outline.md
├── 02-operational-memory/
│   ├── inbox/             # Work notes and residue
│   ├── prompts/daily-log.md
│   ├── prompts/weekly-hype.md
│   ├── outputs/
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/email-triage.md
│   ├── outputs/
│   └── expected/report-outline.md
└── mission-control/       # Optional dashboard
```

## Getting Started

Clone the repository:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

Open the main walkthrough:

```bash
open webinar-runbook.html
# or
xdg-open webinar-runbook.html  # Linux
```

## Exercise 1: Data Intake Review

Transform unknown files into structured intake reports.

### Pattern

1. Place files to review in `code-along/01-data-intake-review/incoming/`
2. Read the prompt template at `code-along/01-data-intake-review/prompts/intake-review.md`
3. Pass the prompt and files to your AI agent
4. Save the output to `code-along/01-data-intake-review/outputs/intake-review.md`

### Example Prompt Structure

```markdown
# Data Intake Review

Review all files in the incoming/ folder and generate a structured report:

## Summary
- Total files: [count]
- File types: [list]

## Individual File Analysis
For each file:
- Filename:
- Type:
- Size:
- Content summary:
- Risk assessment:
- Recommended action:

## Priority Actions
List top 3 actions needed.
```

### Usage

```bash
cd code-along/01-data-intake-review

# View prompt
cat prompts/intake-review.md

# Pass prompt + incoming/* files to your AI agent
# Agent generates analysis

# Save output
# Your agent should save to outputs/intake-review.md
```

### Expected Output

A markdown file at `outputs/intake-review.md` with:
- File inventory
- Content summaries
- Risk assessments
- Prioritized action items

## Exercise 2: Operational Memory

Convert daily work residue into momentum documents.

### Pattern: Daily Log

1. Add work notes to `code-along/02-operational-memory/inbox/`
2. Use `prompts/daily-log.md` to generate daily summary
3. Output to `outputs/daily-log.md`

### Pattern: Weekly Summary

1. Use accumulated daily logs
2. Apply `prompts/weekly-hype.md` for weekly momentum report
3. Output to `outputs/weekly-hype.md`

### Example Daily Log Prompt

```markdown
# Daily Log Generator

Review all files in inbox/ and create a daily log:

## Date
[Today's date]

## Completed
- [Task 1]
- [Task 2]

## In Progress
- [Item 1]

## Blocked
- [Item with blocker]

## Notes
Key insights or decisions

## Tomorrow's Focus
Top 3 priorities
```

### Automation Example

```bash
cd code-along/02-operational-memory

# View automation guidance
cat schedule/cron-examples.md
cat schedule/heartbeat-note.md
```

Example cron for daily log generation:

```bash
# Run daily at 5 PM
0 17 * * * cd /path/to/code-along/02-operational-memory && \
  your-ai-cli --prompt prompts/daily-log.md --input inbox/ \
  --output outputs/daily-log-$(date +\%Y-\%m-\%d).md
```

### Weekly Summary Pattern

```markdown
# Weekly Hype Generator

Review the past week's daily logs and create a momentum report:

## Week of [Date Range]

## Highlights
Top 3 wins

## Momentum Metrics
- Tasks completed: [count]
- Projects advanced: [list]
- Decisions made: [count]

## Next Week's Focus
Strategic priorities

## Energy Check
Reflection on pace and balance
```

## Exercise 3: Offline Communications Triage

Process exported emails into actionable task lists.

### Pattern

1. Export emails as `.eml` files to `code-along/03-offline-communications-triage/eml/`
2. Apply `prompts/email-triage.md`
3. Generate `outputs/email-triage.md` with categorized actions

### Example Triage Prompt

```markdown
# Email Triage

Process all .eml files and create an action report:

## Urgent
- [ ] Action item (from: sender, subject: xyz)

## This Week
- [ ] Action item

## FYI / Low Priority
- [ ] Action item

## Waiting On
- [ ] Item blocked by external party

## Archive
- No action needed items

## Summary Stats
- Total emails: [count]
- Action required: [count]
- FYI only: [count]
```

### Usage

```bash
cd code-along/03-offline-communications-triage

# Export emails to eml/
# (Use your email client's export function)

# Apply prompt
cat prompts/email-triage.md

# Your AI agent processes eml/*.eml files
# Outputs to outputs/email-triage.md
```

### Expected Output

A markdown file with:
- Categorized action items with checkboxes
- Email metadata (sender, subject)
- Priority indicators
- Summary statistics

## Mission Control (Optional)

Create a simple dashboard linking all generated artifacts:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Mission Control</title>
</head>
<body>
  <h1>Executive Assistant Dashboard</h1>
  
  <h2>Data Intake</h2>
  <a href="../01-data-intake-review/outputs/intake-review.md">Latest Intake Review</a>
  
  <h2>Operational Memory</h2>
  <a href="../02-operational-memory/outputs/daily-log.md">Today's Log</a>
  <a href="../02-operational-memory/outputs/weekly-hype.md">This Week's Summary</a>
  
  <h2>Communications</h2>
  <a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a>
</body>
</html>
```

## Best Practices

### Local-First Workflow

- **No live API integrations** during the workshop
- All inputs are files on disk
- All outputs are reviewable markdown
- Copy/paste prompts for transparency

### Prompt Engineering

- Store prompts as markdown files in `prompts/` folders
- Version control prompt templates
- Include expected output structure in prompts
- Add examples when helpful

### Output Management

- Use consistent filenames (e.g., `daily-log-2026-05-11.md`)
- Date-stamp time-based reports
- Keep outputs in dedicated `outputs/` folders
- Link outputs from a central index

### Automation

- Use cron for scheduled reviews
- Set up file watchers for inbox folders
- Chain exercises (intake → memory → triage)
- Add notifications for high-priority items

## Configuration

No configuration files required. The workshop uses a simple folder-based convention:

```
exercise-folder/
├── incoming/ or inbox/ or eml/  # Input files
├── prompts/                      # Prompt templates
├── outputs/                      # Generated artifacts
└── expected/ or schedule/        # Guidance files
```

## Troubleshooting

### No outputs generated

- Verify your AI agent has read access to input folders
- Check that prompt files are readable
- Ensure output folders exist (create if missing)

### Incomplete reports

- Review prompt templates for clarity
- Add more specific examples to prompts
- Break complex prompts into smaller steps

### Automation not running

- Check cron syntax with `crontab -l`
- Verify file paths are absolute in cron jobs
- Check execution permissions on scripts
- Review system logs for errors

## Advanced Patterns

### Chaining Exercises

```bash
# Run full workflow
./run-intake.sh && ./run-memory.sh && ./run-triage.sh
```

### Custom Prompts

Create your own prompts following the template pattern:

```markdown
# [Task Name]

Context: [What the AI needs to know]

Input: [Where to find files]

Task: [What to generate]

Output format:
- [Structure]
- [Sections]

Example:
[Show sample output]
```

### Integration with Personal Systems

- Export calendar events to `inbox/`
- Sync note-taking app to `incoming/`
- Forward emails to watched folder as `.eml`
- Build weekly review from all outputs

## Resources

- Main walkthrough: `webinar-runbook.html`
- Exercise index: `code-along/INDEX.md`
- Original webinar: https://www.datacamp.com/webinars/build-your-own-executive-assistant-with-openclaw
