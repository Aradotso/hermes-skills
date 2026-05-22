---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and email triage
triggers:
  - "set up OpenClaw executive assistant workflow"
  - "create a local AI assistant with OpenClaw"
  - "build data intake review system"
  - "implement operational memory with OpenClaw"
  - "triage emails offline with AI"
  - "generate daily logs and weekly summaries"
  - "create markdown artifacts from work residue"
  - "build local-first assistant workflows"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This project provides a hands-on workshop for building local-first AI executive assistant workflows using OpenClaw. It focuses on three core use cases: data intake review, operational memory (daily/weekly summaries), and offline email triage. All workflows stay local, use markdown artifacts, and require no live integrations.

## What This Project Does

The workshop teaches you to build an AI executive assistant that:

1. **Reviews unknown files** and generates trustworthy intake reports
2. **Transforms work residue** into daily logs and weekly momentum documents
3. **Triages exported emails** offline and creates action lists

All outputs are reviewable markdown files stored locally.

## Project Structure

```
code-along/
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

## Installation & Setup

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the walkthrough
open webinar-runbook.html
```

No additional dependencies required. This is a copy/paste prompt workshop.

## Workshop Flow

### Exercise 1: Data Intake Review

Turn unknown files into a structured intake report.

**Directory**: `code-along/01-data-intake-review/`

**Workflow**:
1. Place files to review in `incoming/`
2. Read the prompt from `prompts/intake-review.md`
3. Provide the prompt + file contents to your AI assistant
4. Save output to `outputs/intake-review.md`

**Example prompt structure**:
```markdown
# Data Intake Review

Review all files in the incoming/ directory and generate a report with:

## Summary
- Total files processed
- File types found
- Overall risk assessment

## Detailed Analysis
For each file:
- Filename
- Type/format
- Size
- Content summary
- Recommended action
- Priority level

## Action Items
Prioritized list of next steps
```

**Expected output**: `outputs/intake-review.md` with structured analysis of all incoming files.

### Exercise 2: Operational Memory

Transform daily work residue into momentum documents.

**Directory**: `code-along/02-operational-memory/`

#### Daily Log

**Workflow**:
1. Collect work notes, commit messages, chat logs in `inbox/`
2. Use prompt from `prompts/daily-log.md`
3. Generate `outputs/daily-log.md`

**Example daily log prompt**:
```markdown
# Daily Log Generator

Review today's work residue and create a daily log with:

## Today's Focus
What was the primary objective?

## Completed
- Concrete accomplishments
- Decisions made
- Problems solved

## In Progress
- Active threads
- Blockers
- Next steps

## Notes
- Insights
- Questions
- Context for future you
```

#### Weekly Summary

**Workflow**:
1. Collect 5-7 daily logs
2. Use prompt from `prompts/weekly-hype.md`
3. Generate `outputs/weekly-hype.md`

**Example weekly summary prompt**:
```markdown
# Weekly Hype Generator

Review this week's daily logs and create an energizing summary:

## Week Highlights
Major wins and progress

## Momentum Builders
Patterns of good work

## Next Week Setup
Prepared ground for what's coming

## Learnings
What you discovered this week
```

#### Automation with Cron

**Example cron setup** (from `schedule/cron-examples.md`):

```bash
# Daily log at 5 PM weekdays
0 17 * * 1-5 cd /path/to/code-along/02-operational-memory && /usr/local/bin/generate-daily-log.sh

# Weekly summary Sunday evening
0 20 * * 0 cd /path/to/code-along/02-operational-memory && /usr/local/bin/generate-weekly-hype.sh
```

**Script example** (`generate-daily-log.sh`):
```bash
#!/bin/bash

WORKSPACE="/path/to/code-along/02-operational-memory"
PROMPT_FILE="$WORKSPACE/prompts/daily-log.md"
INBOX_DIR="$WORKSPACE/inbox"
OUTPUT_FILE="$WORKSPACE/outputs/daily-log-$(date +%Y-%m-%d).md"

# Combine prompt with inbox contents
{
  cat "$PROMPT_FILE"
  echo ""
  echo "## Work Residue"
  find "$INBOX_DIR" -type f -exec echo "### {}" \; -exec cat {} \; -exec echo "" \;
} | openclaw-cli --model="$AI_MODEL" > "$OUTPUT_FILE"

echo "Daily log generated: $OUTPUT_FILE"
```

### Exercise 3: Offline Email Triage

Turn exported emails into actionable triage reports.

**Directory**: `code-along/03-offline-communications-triage/`

**Workflow**:
1. Export emails to `eml/` directory (as .eml files)
2. Use prompt from `prompts/email-triage.md`
3. Generate `outputs/email-triage.md`

**Example triage prompt**:
```markdown
# Email Triage

Analyze exported emails and create a triage report:

## High Priority
Emails requiring immediate attention with:
- Sender
- Subject
- Why urgent
- Recommended action

## Medium Priority
Important but not urgent

## Low Priority / FYI
Can be batch processed or filed

## Delegatable
Should be handled by someone else

## Archive
No action needed
```

**Expected output structure**:
```markdown
# Email Triage Report
Generated: 2026-05-12

## Summary
- Total emails: 47
- High priority: 3
- Action required: 8
- Can delegate: 5

## High Priority

### Email 1
- **From**: client@example.com
- **Subject**: Production issue - site down
- **Why urgent**: Revenue impact
- **Action**: Investigate immediately, loop in DevOps

[Additional priority levels...]

## Next Actions
1. Respond to production issue
2. Schedule call with stakeholder
3. Delegate newsletter review to marketing
```

## Configuration

All configuration is folder-based:

- **Input folders**: `incoming/`, `inbox/`, `eml/`
- **Prompt templates**: `prompts/*.md`
- **Output destination**: `outputs/`
- **Expected examples**: `expected/`

To customize:
1. Modify prompt templates in `prompts/` directories
2. Adjust folder structure as needed
3. Update cron schedules for your timezone

## Common Patterns

### Pattern: Batch Processing Files

```bash
#!/bin/bash
# Process all files in incoming directory

INCOMING="code-along/01-data-intake-review/incoming"
OUTPUT="code-along/01-data-intake-review/outputs"

for file in "$INCOMING"/*; do
  echo "Processing: $file"
  # Your AI CLI command here
done
```

### Pattern: Timestamped Outputs

```bash
TIMESTAMP=$(date +%Y-%m-%d-%H%M)
OUTPUT_FILE="outputs/report-${TIMESTAMP}.md"
```

### Pattern: Combining Multiple Inputs

```bash
# Collect all inbox items into single context
find inbox/ -type f -name "*.md" -exec cat {} \; > combined-input.txt
```

### Pattern: Local-First Review Workflow

```bash
# 1. Generate report
./generate-report.sh

# 2. Review in editor
code outputs/latest-report.md

# 3. Manual approval gate
read -p "Approve? (y/n) " -n 1 -r
echo
if [[ $REPLY =~ ^[Yy]$ ]]; then
  mv outputs/latest-report.md outputs/approved/
fi
```

## Troubleshooting

### No output generated

- Verify input files exist in the correct directory
- Check prompt file paths are correct
- Ensure output directory exists and is writable

### Cron job not running

```bash
# Check cron logs
grep CRON /var/log/syslog

# Test script manually
cd /path/to/exercise && ./script.sh

# Verify cron has correct environment
env - $(cat /path/to/.env) /bin/bash -c 'your-command'
```

### Large file processing

For large inbox directories or many emails:
- Process in batches
- Use file limits in find commands
- Consider splitting by date or priority

```bash
# Process only last 24 hours
find inbox/ -type f -mtime -1 -name "*.md"
```

### Output quality issues

- Refine prompts with more specific instructions
- Add examples to prompt templates
- Include formatting requirements in prompts
- Use expected output files as references

## AI Agent Integration

When using with Claude Code, Cursor, or similar:

```markdown
# Prompt template
I need to [trigger phrase]. 

Context:
- Exercise: [01, 02, or 03]
- Input files: [list files]
- Output needed: [type of report]

Please use the prompt template from prompts/ and generate the output.
```

The agent will reference this skill to understand the structure and generate appropriate markdown artifacts.
