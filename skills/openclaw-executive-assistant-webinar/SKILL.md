---
name: openclaw-executive-assistant-webinar
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - set up openclaw executive assistant
  - create local AI workflow with openclaw
  - build data intake review system
  - set up operational memory with openclaw
  - triage emails with openclaw locally
  - create openclaw daily log automation
  - build local-first assistant workflow
  - use openclaw for communications management
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## What This Project Does

This is a workshop/starter repository for building a local-first AI executive assistant using OpenClaw. It teaches three core workflows that keep everything in local folders and produce reviewable markdown artifacts:

1. **Data intake review** — Turn unknown files into trustworthy intake reports
2. **Operational memory** — Turn work residue into daily and weekly momentum docs
3. **Offline communications triage** — Turn exported emails into action lists

All workflows are designed to be local-only with no live integrations, using copy/paste prompts and markdown outputs.

## Installation

Clone the repository:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

Open the main walkthrough:

```bash
# Open webinar-runbook.html in your browser
open webinar-runbook.html
```

## Repository Structure

```
.
├── webinar-runbook.html          # Main workshop walkthrough
└── code-along/
    ├── INDEX.md                   # Overview of exercises
    ├── 01-data-intake-review/     # Exercise 1
    ├── 02-operational-memory/     # Exercise 2
    ├── 03-offline-communications-triage/  # Exercise 3
    └── mission-control/           # Optional dashboard
```

## Exercise 1: Data Intake Review

Turn unknown files into a trustworthy intake report.

### Structure

```
01-data-intake-review/
├── incoming/              # Files to inspect
├── prompts/
│   └── intake-review.md  # Instructions for report generation
├── outputs/              # Generated markdown
└── expected/
    └── report-outline.md # Expected report shape
```

### Workflow

1. Place files to review in `incoming/`
2. Use the prompt from `prompts/intake-review.md` with your AI assistant
3. Review generated `outputs/intake-review.md`

### Expected Output Format

The intake review should produce a markdown report with:

- File inventory and metadata
- Content summaries
- Risk assessment
- Recommended actions
- Priority categorization

### Example Prompt Pattern

```markdown
Review the files in incoming/ and create a structured intake report.

For each file, provide:
- Filename and type
- Size and date
- Content summary
- Risk level (low/medium/high)
- Suggested action

Output as markdown to outputs/intake-review.md
```

## Exercise 2: Operational Memory

Turn work residue into daily and weekly momentum documents.

### Structure

```
02-operational-memory/
├── inbox/                 # Notes and work residue
├── prompts/
│   ├── daily-log.md      # Daily log prompt
│   └── weekly-hype.md    # Weekly summary prompt
├── outputs/              # Generated logs
├── schedule/
│   ├── cron-examples.md  # Automation examples
│   └── heartbeat-note.md # Heartbeat guidance
```

### Daily Log Workflow

```markdown
Process the notes in inbox/ and generate a daily log.

Include:
- Key accomplishments
- Blockers encountered
- Decisions made
- Tomorrow's priorities

Format: Simple markdown with bullet points
Output: outputs/daily-log.md
```

### Weekly Hype Workflow

```markdown
Synthesize the week's daily logs into a weekly summary.

Include:
- Major wins
- Momentum themes
- Recurring blockers
- Week-ahead focus

Format: Motivational tone, markdown
Output: outputs/weekly-hype.md
```

### Automation with Cron

Example cron schedule for daily log generation:

```bash
# Generate daily log at 5 PM every weekday
0 17 * * 1-5 /path/to/generate-daily-log.sh

# Generate weekly hype every Friday at 6 PM
0 18 * * 5 /path/to/generate-weekly-hype.sh
```

### Expected Artifacts

- `outputs/daily-log.md` — Daily work summary
- `outputs/weekly-hype.md` — Weekly momentum report

## Exercise 3: Offline Communications Triage

Turn exported email into an action list.

### Structure

```
03-offline-communications-triage/
├── eml/                   # Exported email files
├── prompts/
│   └── email-triage.md   # Triage prompt
├── outputs/              # Generated triage reports
└── expected/
    └── report-outline.md # Expected report shape
```

### Workflow

1. Export emails as `.eml` files to `eml/` directory
2. Use the triage prompt with your AI assistant
3. Review generated `outputs/email-triage.md`

### Example Triage Prompt

```markdown
Process all .eml files in eml/ and create an email triage report.

For each email, extract:
- Sender and subject
- Date received
- Urgency level (low/medium/high/urgent)
- Category (action required/FYI/delegation/archive)
- Suggested response or action
- Estimated time to handle

Group by urgency, then by category.
Output: outputs/email-triage.md
```

### Expected Output Format

```markdown
# Email Triage Report

## Urgent - Action Required
- [ ] Sender: John Doe | Subject: Q4 Budget Review | Time: 30min
      Action: Review attached spreadsheet and respond by EOD

## High - Action Required
- [ ] Sender: Jane Smith | Subject: Project Kickoff | Time: 15min
      Action: Confirm meeting time for next week

## Medium - FYI
- Sender: Team Newsletter | Subject: Weekly Update
  Action: Read when time permits

## Low - Archive
- Sender: Marketing | Subject: New feature announcement
  Action: Archive for reference
```

## Mission Control (Optional)

The `mission-control/` directory is reserved for a simple dashboard or HTML page that links to all generated artifacts:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Executive Assistant Dashboard</title>
</head>
<body>
    <h1>Mission Control</h1>
    <nav>
        <ul>
            <li><a href="../01-data-intake-review/outputs/intake-review.md">Latest Intake Review</a></li>
            <li><a href="../02-operational-memory/outputs/daily-log.md">Today's Log</a></li>
            <li><a href="../02-operational-memory/outputs/weekly-hype.md">This Week's Hype</a></li>
            <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
        </ul>
    </nav>
</body>
</html>
```

## Common Patterns

### Local-First Philosophy

All workflows emphasize:
- No external API calls
- No live integrations
- Human-reviewable markdown artifacts
- Copy/paste prompts (no automation required)
- Version-controllable outputs

### Prompt Engineering Best Practices

1. **Be specific about input location**: Always reference exact directory paths
2. **Define output format**: Specify markdown structure and required sections
3. **Set context boundaries**: Make clear what files/time period to process
4. **Request actionable outputs**: Focus on decisions and next steps

### File Organization

Keep inputs and outputs separated:

```
exercise-name/
├── incoming/     # Input files
├── prompts/      # Reusable prompt templates
├── outputs/      # Generated artifacts
└── expected/     # Documentation of expected formats
```

## Troubleshooting

### Files Not Processing

**Problem**: AI assistant can't find files in directories

**Solution**: Ensure you're providing the full relative path from your working directory:

```markdown
Process files in code-along/01-data-intake-review/incoming/
```

### Output Format Issues

**Problem**: Generated reports don't match expected structure

**Solution**: Be more explicit in prompts about required sections:

```markdown
Create a report with exactly these sections:
## Executive Summary
## File Inventory
## Risk Assessment
## Recommended Actions
```

### Inconsistent Daily Logs

**Problem**: Daily logs vary too much in format

**Solution**: Create a template in your prompt:

```markdown
Use this exact format:
# Daily Log - [DATE]
## Completed
- 
## In Progress
- 
## Blockers
- 
## Tomorrow
- 
```

### Cron Jobs Not Running

**Problem**: Scheduled automation doesn't execute

**Solution**: Check cron syntax and script permissions:

```bash
# Verify cron entry
crontab -l

# Ensure script is executable
chmod +x /path/to/script.sh

# Test script manually
/path/to/script.sh
```

## Integration with AI Coding Agents

When working with Claude, Cursor, or similar agents:

1. **Set context**: Share the exercise directory structure
2. **Reference prompts**: Point to the specific prompt file in `prompts/`
3. **Specify output location**: Always include the target path in `outputs/`
4. **Request review**: Ask the agent to validate against `expected/` formats

Example interaction:

```
I'm working on exercise 2. Please read the prompt from 
code-along/02-operational-memory/prompts/daily-log.md and 
process the files in inbox/ to generate a daily log in 
outputs/daily-log.md
```
