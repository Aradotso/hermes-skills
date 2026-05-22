---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "help me set up an executive assistant with OpenClaw"
  - "create a local data intake review system"
  - "build operational memory automation"
  - "set up email triage with OpenClaw"
  - "generate daily logs and weekly summaries"
  - "process incoming files into reports"
  - "automate communications triage locally"
  - "create markdown artifacts from work residue"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill teaches you to build local-first AI executive assistant workflows using OpenClaw. The project provides structured exercises for creating automated workflows that process files, maintain operational memory, and triage communications—all using local folders and markdown artifacts.

## What This Project Does

OpenClaw Executive Assistant is a workshop-based approach to building AI-powered productivity systems that:

- **Review data intake**: Transform unknown files into trustworthy intake reports
- **Maintain operational memory**: Convert work residue into daily logs and weekly summaries
- **Triage communications**: Process exported emails into actionable task lists

Everything operates locally with no external integrations, producing reviewable markdown outputs.

## Project Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/           # Prompt templates
│   ├── outputs/           # Generated reports
│   └── expected/          # Expected outputs
├── 02-operational-memory/
│   ├── inbox/             # Work notes and residue
│   ├── prompts/           # Daily/weekly prompts
│   ├── outputs/           # Generated logs
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported emails
│   ├── prompts/           # Triage prompts
│   ├── outputs/           # Action lists
│   └── expected/          # Expected outputs
└── mission-control/       # Optional dashboard
```

## Getting Started

### Installation

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the runbook
open webinar-runbook.html
```

### Prerequisites

- An AI assistant that can process local files (Claude, ChatGPT, etc.)
- Text editor for viewing markdown outputs
- Terminal for optional automation setup

## Exercise 1: Data Intake Review

Transform incoming files into structured reports.

### Directory Setup

```bash
cd code-along/01-data-intake-review
ls incoming/  # View files to process
```

### Workflow

1. **Review the prompt template**:

```bash
cat prompts/intake-review.md
```

2. **Process files**: Copy the prompt and point your AI assistant to the `incoming/` folder

3. **Expected output structure** (`outputs/intake-review.md`):

```markdown
# Data Intake Review
Date: YYYY-MM-DD

## Summary
Brief overview of all files processed.

## File Analysis
### [filename-1]
- **Type**: [file type]
- **Size**: [size]
- **Content**: [description]
- **Action needed**: [recommendations]

### [filename-2]
...

## Priority Actions
1. [High priority item]
2. [Medium priority item]

## Archive Candidates
- [Files that can be archived]
```

### Example Prompt Pattern

When using the intake review prompt with your AI:

```
Please analyze all files in the incoming/ directory and generate
an intake review report following the template in prompts/intake-review.md.
Save the output to outputs/intake-review.md.
```

## Exercise 2: Operational Memory

Convert daily work residue into momentum documents.

### Daily Log Generation

```bash
cd code-along/02-operational-memory
cat prompts/daily-log.md
```

**Expected output** (`outputs/daily-log.md`):

```markdown
# Daily Log - [Date]

## Completed Today
- [Task 1 with context]
- [Task 2 with context]

## In Progress
- [Ongoing work item]

## Blockers
- [Any impediments]

## Tomorrow's Focus
- [Priority 1]
- [Priority 2]

## Notes
[Any additional context or observations]
```

### Weekly Summary Generation

```bash
cat prompts/weekly-hype.md
```

**Expected output** (`outputs/weekly-hype.md`):

```markdown
# Weekly Hype - Week of [Date]

## Wins
- [Major accomplishment 1]
- [Major accomplishment 2]

## Momentum
- [Projects moving forward]

## Learnings
- [Key insights from the week]

## Next Week's Priorities
1. [Priority 1]
2. [Priority 2]
3. [Priority 3]
```

### Automation with Cron

For automated daily/weekly processing:

```bash
# Daily log at 5 PM
0 17 * * * cd /path/to/code-along/02-operational-memory && /path/to/process-daily.sh

# Weekly summary on Friday at 4 PM
0 16 * * 5 cd /path/to/code-along/02-operational-memory && /path/to/process-weekly.sh
```

See `schedule/cron-examples.md` for complete setup instructions.

## Exercise 3: Offline Communications Triage

Process exported emails into action lists.

### Directory Setup

```bash
cd code-along/03-offline-communications-triage
ls eml/  # View email files
```

### Workflow

1. **Export emails**: Save emails as `.eml` files in the `eml/` directory

2. **Review triage prompt**:

```bash
cat prompts/email-triage.md
```

3. **Process emails**: Use the prompt to generate triage report

### Expected Output Structure

**File**: `outputs/email-triage.md`

```markdown
# Email Triage - [Date]

## High Priority
### [Subject Line]
- **From**: [sender]
- **Date**: [date]
- **Action**: [specific action needed]
- **Deadline**: [if applicable]

## Medium Priority
### [Subject Line]
- **From**: [sender]
- **Action**: [specific action needed]

## Low Priority / FYI
### [Subject Line]
- **From**: [sender]
- **Summary**: [brief summary]

## Can Archive
- [Email subjects that need no action]

## Follow-ups Needed
1. [Person to follow up with - context]
2. [Person to follow up with - context]
```

### Example Processing Flow

```
Analyze all .eml files in the eml/ directory and create a prioritized
triage report following prompts/email-triage.md. Output to
outputs/email-triage.md.
```

## Common Patterns

### Copy-Paste Workflow

All exercises follow this pattern:

1. Navigate to exercise directory
2. Review files in input folder (`incoming/`, `inbox/`, `eml/`)
3. Copy prompt from `prompts/` directory
4. Paste prompt to AI assistant with folder context
5. Review output in `outputs/` directory
6. Compare with `expected/` examples (where provided)

### Folder Watching Pattern

For automation, create a simple watcher script:

```bash
#!/bin/bash
# watch-and-process.sh

WATCH_DIR="$1"
OUTPUT_DIR="$2"
PROMPT_FILE="$3"

while inotifywait -r -e modify,create "$WATCH_DIR"; do
  echo "Changes detected, processing..."
  # Call your AI processing command here
  # Example: ai-cli process --prompt "$PROMPT_FILE" --input "$WATCH_DIR" --output "$OUTPUT_DIR"
done
```

### Chaining Exercises

Build a complete workflow:

```bash
# Morning: Review intake
cd 01-data-intake-review && process-intake.sh

# Afternoon: Triage communications
cd ../03-offline-communications-triage && process-emails.sh

# Evening: Generate daily log
cd ../02-operational-memory && generate-daily-log.sh

# Friday: Generate weekly summary
cd ../02-operational-memory && generate-weekly-hype.sh
```

## Mission Control (Optional)

Create a simple dashboard linking all artifacts:

```markdown
# Mission Control

## Today's View
- [Daily Log](../02-operational-memory/outputs/daily-log.md)
- [Latest Intake](../01-data-intake-review/outputs/intake-review.md)
- [Email Triage](../03-offline-communications-triage/outputs/email-triage.md)

## This Week
- [Weekly Hype](../02-operational-memory/outputs/weekly-hype.md)

## Quick Actions
- Review new files in intake
- Process pending emails
- Update operational memory
```

## Troubleshooting

### Outputs are empty or incomplete

- Verify input files exist in the correct directories
- Check that prompts reference the correct folder paths
- Ensure your AI assistant has access to local file system

### Prompts not producing expected format

- Review `expected/` examples for reference structure
- Copy prompts exactly as written initially
- Customize prompts only after successful first run

### Automation not triggering

- Check cron syntax: `crontab -l`
- Verify script permissions: `chmod +x script.sh`
- Test scripts manually before scheduling
- Check logs: `grep CRON /var/log/syslog`

### File processing errors

- Ensure file formats are supported (.eml, .txt, .md, etc.)
- Check file permissions: `ls -la incoming/`
- Verify no special characters in filenames

## Best Practices

1. **Start manual**: Run each exercise manually before automating
2. **Review outputs**: Always check generated markdown before archiving
3. **Iterate prompts**: Customize prompts based on your workflow needs
4. **Keep history**: Don't overwrite old reports; date-stamp outputs
5. **Local-first**: Keep everything in version control except sensitive data

## Environment Variables

For any custom processing scripts:

```bash
export OPENCLAW_WORKSPACE="/path/to/code-along"
export OPENCLAW_ARCHIVE="/path/to/archive"
export AI_CLI_PATH="/path/to/ai-cli"  # If using CLI tool
```

## Integration Examples

### With AI CLI Tools

```bash
# Example with hypothetical AI CLI
ai-cli generate \
  --prompt code-along/01-data-intake-review/prompts/intake-review.md \
  --context code-along/01-data-intake-review/incoming/ \
  --output code-along/01-data-intake-review/outputs/intake-review.md
```

### With Git for Version Control

```bash
# Track generated outputs
cd code-along
git add outputs/
git commit -m "Daily log $(date +%Y-%m-%d)"
git push
```

This workshop approach keeps you in control with local, reviewable artifacts while building practical AI assistant workflows.
