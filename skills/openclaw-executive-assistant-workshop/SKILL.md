---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "help me set up an OpenClaw executive assistant"
  - "create a data intake review with OpenClaw"
  - "build operational memory system for daily logs"
  - "triage emails with OpenClaw offline"
  - "set up local-first AI assistant workflow"
  - "generate markdown reports from unstructured files"
  - "create weekly summary with OpenClaw"
  - "automate communications triage locally"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

This project provides a hands-on workshop for building local-first executive assistant workflows using OpenClaw. It teaches three core patterns:

1. **Data intake review** - Turn unknown files into trustworthy reports
2. **Operational memory** - Convert work residue into daily/weekly momentum docs
3. **Offline communications triage** - Transform exported emails into action lists

All workflows operate on local files only, produce reviewable markdown artifacts, and require no live API integrations.

## Workshop Structure

```text
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/           # intake-review.md
│   ├── outputs/           # Generated reports
│   └── expected/          # report-outline.md
├── 02-operational-memory/
│   ├── inbox/             # Notes and work residue
│   ├── prompts/           # daily-log.md, weekly-hype.md
│   ├── outputs/           # Generated logs
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/           # email-triage.md
│   ├── outputs/           # Triage reports
│   └── expected/          # report-outline.md
└── mission-control/       # Optional dashboard
```

## Getting Started

### Installation

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git

cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the walkthrough
open webinar-runbook.html
```

### Prerequisites

- OpenClaw or compatible AI assistant (Claude, Cursor, etc.)
- Local file system access
- Markdown viewer/editor

## Exercise 1: Data Intake Review

**Goal**: Process unknown files in `incoming/` folder and generate a structured intake report.

### Workflow

```bash
# Navigate to exercise
cd code-along/01-data-intake-review/

# Review incoming files
ls incoming/

# Read the prompt template
cat prompts/intake-review.md
```

### Example Prompt Pattern

```markdown
You are reviewing files for intake. Analyze all files in the incoming/ folder and produce:

1. **Summary**: Quick overview of what arrived
2. **File inventory**: Name, type, size, key content
3. **Risk assessment**: Security, quality, completeness
4. **Recommended actions**: What to do with each file
5. **Priority ranking**: Urgent, normal, low

Output format: Single markdown document
Output location: outputs/intake-review.md
```

### Expected Output Structure

```markdown
# Intake Review - [DATE]

## Summary
Brief overview of files received...

## File Inventory
| File | Type | Size | Key Content |
|------|------|------|-------------|
| ... | ... | ... | ... |

## Risk Assessment
- Security concerns
- Quality issues
- Completeness checks

## Recommended Actions
1. File A: [action]
2. File B: [action]

## Priority Ranking
**Urgent**: ...
**Normal**: ...
**Low**: ...
```

## Exercise 2: Operational Memory

**Goal**: Transform daily work residue into structured logs and weekly summaries.

### Daily Log Workflow

```bash
cd code-along/02-operational-memory/

# Review inbox contents
ls inbox/

# Use daily log prompt
cat prompts/daily-log.md
```

### Daily Log Prompt Pattern

```markdown
Review all notes in inbox/ from today and create a daily log with:

1. **What shipped**: Completed work
2. **What's blocked**: Obstacles and dependencies
3. **What's next**: Tomorrow's priorities
4. **Energy notes**: What worked/didn't work today
5. **Carry forward**: Items for tomorrow

Output: outputs/daily-log.md
Date: [TODAY]
```

### Weekly Summary Workflow

```markdown
Review the past 7 daily logs and generate a weekly summary:

1. **Week in review**: Narrative summary
2. **Key wins**: Major accomplishments
3. **Patterns**: Recurring themes
4. **Momentum builders**: What's accelerating
5. **Friction points**: What's slowing progress
6. **Week ahead**: Strategic priorities

Output: outputs/weekly-hype.md
```

### Automation with Cron

```bash
# Daily log at 5 PM weekdays
0 17 * * 1-5 /path/to/openclaw-daily-log.sh

# Weekly summary Sunday evening
0 18 * * 0 /path/to/openclaw-weekly-summary.sh
```

Example automation script:

```bash
#!/bin/bash
# openclaw-daily-log.sh

DATE=$(date +%Y-%m-%d)
PROMPT_FILE="code-along/02-operational-memory/prompts/daily-log.md"
OUTPUT_FILE="code-along/02-operational-memory/outputs/daily-log-${DATE}.md"

# Execute OpenClaw with prompt
openclaw execute \
  --prompt "${PROMPT_FILE}" \
  --context "code-along/02-operational-memory/inbox/" \
  --output "${OUTPUT_FILE}"
```

## Exercise 3: Offline Communications Triage

**Goal**: Process exported email files and generate actionable triage reports.

### Workflow

```bash
cd code-along/03-offline-communications-triage/

# Review exported emails
ls eml/

# Use triage prompt
cat prompts/email-triage.md
```

### Email Triage Prompt Pattern

```markdown
Analyze all .eml files and create a triage report:

1. **Executive summary**: Volume, themes, urgency
2. **Action required**: Emails needing response
3. **FYI only**: Informational emails
4. **Delegatable**: Items to forward
5. **Archive candidates**: Low-priority items
6. **Draft responses**: Suggested reply templates

Output: outputs/email-triage.md
Prioritize by: Urgency, sender importance, topic relevance
```

### Expected Triage Output

```markdown
# Email Triage - [DATE]

## Executive Summary
- Total emails: 47
- Require response: 8
- FYI only: 32
- Archive: 7

## Action Required (Priority Order)

### HIGH: Client escalation from Jane Smith
**Subject**: Production issue affecting key account
**Action**: Schedule call, loop in engineering
**Draft**: "Hi Jane, I'm scheduling a call for..."

### MEDIUM: Budget approval needed
**Subject**: Q2 Marketing spend request
**Action**: Review numbers, approve by Friday
**Draft**: "Thanks for putting this together..."

## FYI Only
- Newsletter subscriptions (15)
- Team updates (12)
- Industry news (5)

## Delegatable
- HR policy update → Forward to team lead
- Conference invite → Forward to events coordinator

## Archive Candidates
- Old thread completions (7)
```

## Configuration Patterns

### Directory Structure Setup

```bash
# Initialize a new project
mkdir -p my-assistant/{intake,memory,triage}
cd my-assistant

# Create standard folders
mkdir -p intake/{incoming,outputs,prompts}
mkdir -p memory/{inbox,outputs,prompts,schedule}
mkdir -p triage/{eml,outputs,prompts}
```

### Prompt Template Variables

Use environment variables for customization:

```bash
export ASSISTANT_NAME="My Executive Assistant"
export TIMEZONE="America/New_York"
export WORK_HOURS="9-5"
export PRIORITY_CONTACTS="boss@company.com,client@important.com"
```

Reference in prompts:

```markdown
Assistant: ${ASSISTANT_NAME}
Working hours: ${WORK_HOURS} ${TIMEZONE}
Priority contacts: ${PRIORITY_CONTACTS}
```

## Common Patterns

### Pattern 1: File-to-Markdown Pipeline

```markdown
1. Drop files in incoming/
2. Run intake prompt against folder
3. Review generated markdown
4. Move files to processed/
5. Archive or delete originals
```

### Pattern 2: Accumulate-and-Summarize

```markdown
1. Collect daily notes in inbox/
2. Run daily summarizer each evening
3. Keep week's worth of daily logs
4. Run weekly aggregator on Sundays
5. Archive weekly summaries monthly
```

### Pattern 3: Export-Process-Action

```markdown
1. Export emails to .eml files
2. Run triage prompt on batch
3. Review action items
4. Draft responses
5. Delete processed .eml files
```

## Troubleshooting

### Files Not Being Processed

```bash
# Check file permissions
ls -la code-along/01-data-intake-review/incoming/

# Verify file types are readable
file incoming/*

# Ensure output directory exists
mkdir -p outputs
```

### Prompt Not Generating Expected Output

- Verify prompt file path is correct
- Check that context folder contains files
- Review expected output examples in `expected/` folders
- Ensure markdown formatting in prompt is valid

### Automation Not Running

```bash
# Check cron syntax
crontab -l

# Verify script permissions
chmod +x openclaw-daily-log.sh

# Test script manually
./openclaw-daily-log.sh

# Check logs
tail -f /var/log/cron.log
```

### Large File Processing

For files over 10MB, chunk the processing:

```bash
# Split large email exports
split -l 50 emails.mbox split-emails-

# Process each chunk
for chunk in split-emails-*; do
  openclaw execute --prompt triage.md --context "$chunk"
done
```

## Best Practices

1. **Review before committing**: Always review AI-generated outputs before taking action
2. **Version control prompts**: Track prompt evolution in git
3. **Regular cleanup**: Archive processed files weekly
4. **Test prompts incrementally**: Start with small file sets
5. **Maintain audit trail**: Keep generated markdown for reference
6. **Secure sensitive data**: Never commit real emails or private data
7. **Iterate on prompts**: Refine based on output quality

## Advanced Usage

### Custom Mission Control Dashboard

Create a simple HTML dashboard linking all outputs:

```html
<!DOCTYPE html>
<html>
<head><title>Mission Control</title></head>
<body>
  <h1>Executive Assistant Dashboard</h1>
  <ul>
    <li><a href="01-data-intake-review/outputs/intake-review.md">Latest Intake</a></li>
    <li><a href="02-operational-memory/outputs/daily-log.md">Today's Log</a></li>
    <li><a href="03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
  </ul>
</body>
</html>
```

### Integration with Other Tools

```bash
# Convert markdown to PDF
pandoc outputs/weekly-hype.md -o weekly-hype.pdf

# Send summary via email
cat outputs/daily-log.md | mail -s "Daily Log" ${YOUR_EMAIL}

# Sync to cloud backup
rsync -av outputs/ ~/Dropbox/assistant-logs/
```
