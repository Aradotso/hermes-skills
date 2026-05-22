---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "help me build an executive assistant with OpenClaw"
  - "set up OpenClaw data intake review"
  - "create operational memory with OpenClaw"
  - "triage emails using OpenClaw"
  - "build local-first AI assistant workflows"
  - "set up OpenClaw daily and weekly logs"
  - "create markdown artifacts with OpenClaw"
  - "implement OpenClaw communications triage"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill covers building local-first AI executive assistant workflows using OpenClaw. The project provides three main workflow patterns: data intake review, operational memory management, and offline communications triage. All workflows process local files and generate reviewable markdown artifacts without external integrations.

## What This Project Does

OpenClaw Executive Assistant is a workshop framework for practicing local-first AI workflows that:

- **Data Intake Review**: Transforms unknown files into trustworthy intake reports
- **Operational Memory**: Converts work residue into daily logs and weekly momentum documents
- **Offline Communications Triage**: Processes exported emails into actionable task lists

All operations are file-based, creating markdown outputs that can be reviewed, versioned, and refined.

## Installation

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Navigate to the code-along directory
cd code-along
```

No additional dependencies are required. The workshop uses copy/paste prompts with your AI coding agent.

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

## Workflow 1: Data Intake Review

Transform unknown files into structured intake reports.

**Input**: Files in `01-data-intake-review/incoming/`
**Output**: `01-data-intake-review/outputs/intake-review.md`

### Running Data Intake Review

```bash
# Navigate to the exercise
cd code-along/01-data-intake-review

# Review the prompt
cat prompts/intake-review.md

# Send to your AI agent with context about incoming/ files
# The agent will analyze files and generate outputs/intake-review.md
```

### Example Prompt Pattern

```markdown
Review all files in the incoming/ directory and create a comprehensive intake report.

For each file, provide:
- File name and type
- Size and format
- Content summary
- Potential use cases
- Any concerns or required actions
- Priority level (High/Medium/Low)

Output the report to outputs/intake-review.md in markdown format.
```

### Expected Output Structure

```markdown
# Data Intake Review Report

Generated: YYYY-MM-DD

## Summary
- Total files processed: X
- High priority items: Y
- Action required: Z

## Files Analyzed

### [Filename]
- **Type**: document/spreadsheet/code/etc
- **Size**: XX KB
- **Summary**: Brief content description
- **Use Case**: What this could be used for
- **Priority**: High/Medium/Low
- **Actions**: What needs to happen next
```

## Workflow 2: Operational Memory

Convert work residue into daily logs and weekly summaries for maintaining momentum.

**Inputs**: Files in `02-operational-memory/inbox/`
**Outputs**: 
- `02-operational-memory/outputs/daily-log.md`
- `02-operational-memory/outputs/weekly-hype.md`

### Running Daily Log

```bash
cd code-along/02-operational-memory

# Review the prompt
cat prompts/daily-log.md

# Process inbox/ files into a daily log
```

### Daily Log Prompt Pattern

```markdown
Review all files in inbox/ and create a daily log capturing:

1. **Completed Today**: What got done
2. **In Progress**: What's ongoing
3. **Blocked**: What's stuck and why
4. **Notes**: Quick captures and observations
5. **Tomorrow**: Next actions

Output to outputs/daily-log.md
```

### Weekly Summary (Hype) Prompt Pattern

```markdown
Review all daily logs and work residue from the past week in inbox/

Create a weekly momentum document with:

1. **Wins This Week**: Accomplishments and progress
2. **Key Learnings**: Insights and discoveries
3. **Momentum Items**: What's building
4. **Next Week Focus**: Top 3 priorities
5. **Needs Attention**: Blockers or decisions needed

Keep it energizing and forward-looking.
Output to outputs/weekly-hype.md
```

### Automation with Cron

The workshop includes cron examples for scheduled generation:

```bash
# Example: Daily log at 6 PM
0 18 * * * cd /path/to/02-operational-memory && your-ai-agent process daily-log

# Example: Weekly hype on Friday at 4 PM
0 16 * * 5 cd /path/to/02-operational-memory && your-ai-agent process weekly-hype
```

See `schedule/cron-examples.md` for detailed automation patterns.

## Workflow 3: Offline Communications Triage

Process exported emails into actionable task lists.

**Input**: Email files in `03-offline-communications-triage/eml/`
**Output**: `03-offline-communications-triage/outputs/email-triage.md`

### Running Email Triage

```bash
cd code-along/03-offline-communications-triage

# Review the prompt
cat prompts/email-triage.md

# Process eml/ files into triage report
```

### Email Triage Prompt Pattern

```markdown
Process all email files in eml/ directory.

For each email, extract:
- **From**: Sender
- **Subject**: Email subject
- **Date**: When received
- **Summary**: Brief content summary (2-3 sentences)
- **Action Required**: Yes/No and what action
- **Priority**: Urgent/High/Medium/Low
- **Category**: Decision/Info/Task/FYI

Create sections:
1. Urgent Actions (requires response within 24h)
2. High Priority (needs response this week)
3. Medium Priority (can wait)
4. FYI Only (no action needed)

Output to outputs/email-triage.md
```

### Expected Triage Output

```markdown
# Email Triage Report

Generated: YYYY-MM-DD
Emails processed: X

## Urgent Actions (24h)

### [Subject Line]
- **From**: name@example.com
- **Date**: YYYY-MM-DD
- **Summary**: Quick overview of content
- **Action**: Specific action needed
- **Category**: Decision

## High Priority (This Week)

[Similar structure...]

## Medium Priority

[Similar structure...]

## FYI Only

[Similar structure...]
```

## Mission Control (Optional)

The `mission-control/` directory is reserved for creating a dashboard that links all generated artifacts:

```markdown
# Mission Control Dashboard

## Today's Status
- [Daily Log](../02-operational-memory/outputs/daily-log.md)
- [Latest Intake Review](../01-data-intake-review/outputs/intake-review.md)
- [Email Triage](../03-offline-communications-triage/outputs/email-triage.md)

## This Week
- [Weekly Hype](../02-operational-memory/outputs/weekly-hype.md)

## Archives
- [Previous Daily Logs](../02-operational-memory/outputs/archive/)
- [Previous Triage Reports](../03-offline-communications-triage/outputs/archive/)
```

## Common Patterns

### Archiving Outputs

```bash
# Archive daily logs monthly
mkdir -p outputs/archive/2026-05
mv outputs/daily-log-2026-05-*.md outputs/archive/2026-05/

# Archive intake reviews by quarter
mkdir -p outputs/archive/Q2-2026
mv outputs/intake-review-*.md outputs/archive/Q2-2026/
```

### Combining Workflows

```bash
# Morning routine: process overnight items
cd 01-data-intake-review && process-intake
cd ../03-offline-communications-triage && process-emails

# Evening routine: capture daily progress
cd 02-operational-memory && generate-daily-log
```

### Custom Prompt Modifications

All prompts are markdown files that can be customized:

```bash
# Add custom sections to daily log
echo "\n## Personal Notes\nCapture personal observations\n" >> prompts/daily-log.md

# Adjust triage priorities
sed -i 's/High Priority/Important/g' prompts/email-triage.md
```

## Troubleshooting

### Output Files Not Generated

**Problem**: AI agent doesn't create output files
**Solution**: Explicitly specify the output path in your prompt
```markdown
Write the output to: code-along/01-data-intake-review/outputs/intake-review.md
```

### Missing Context from Prompts

**Problem**: Agent doesn't understand the workflow
**Solution**: Include both the prompt file AND the input directory in your request
```markdown
Using the prompt from prompts/intake-review.md, 
analyze all files in incoming/ directory
```

### Inconsistent Output Format

**Problem**: Reports vary in structure
**Solution**: Reference the expected outline
```markdown
Generate the report matching the structure in expected/report-outline.md
```

### Large File Processing

**Problem**: Too many files to process at once
**Solution**: Batch processing
```bash
# Process in groups
ls incoming/ | head -10 | xargs -I {} process-file {}
```

## Best Practices

1. **Review Before Archiving**: Always review generated artifacts before moving them to archives
2. **Version Control**: Track prompts and outputs in git for iteration
3. **Consistent Naming**: Use ISO dates in filenames (YYYY-MM-DD)
4. **Separate Concerns**: Keep each workflow's files in its own directory
5. **Incremental Improvement**: Refine prompts based on output quality
6. **Regular Cadence**: Run operational memory workflows daily/weekly for best results

## Integration with AI Agents

This workshop is designed for manual AI agent interaction, but can be integrated into automated workflows:

```bash
# Example: Claude CLI integration
claude --prompt "$(cat prompts/daily-log.md)" --files inbox/* > outputs/daily-log.md

# Example: Using environment variables for paths
export OPENCLAW_BASE="$HOME/code-along"
export INTAKE_DIR="$OPENCLAW_BASE/01-data-intake-review"
```

## Workshop Walkthrough

The main walkthrough is in `webinar-runbook.html` at the repository root. Open it in a browser and follow along with the exercises in sequence.
