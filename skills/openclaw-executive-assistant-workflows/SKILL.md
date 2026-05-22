---
name: openclaw-executive-assistant-workflows
description: Build local-first executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - set up an OpenClaw executive assistant workflow
  - create a local data intake review system
  - build an operational memory system with OpenClaw
  - triage emails offline with OpenClaw
  - generate daily and weekly momentum reports
  - process incoming files with OpenClaw prompts
  - create markdown artifacts from work residue
  - build a local-only AI assistant workflow
---

# OpenClaw Executive Assistant Workflows

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

This project provides local-first executive assistant workflows using OpenClaw. It demonstrates three core patterns:

1. **Data intake review** - Turn unknown files into trustworthy reports
2. **Operational memory** - Convert work residue into daily/weekly momentum docs
3. **Offline communications triage** - Process exported emails into action lists

All workflows stay in local folders and produce reviewable markdown artifacts with no live integrations required.

## Installation

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

No additional dependencies required - this is a prompt-based workflow using local files and markdown.

## Project Structure

```text
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/           # Files to inspect
│   ├── prompts/            # Prompt templates
│   ├── outputs/            # Generated reports
│   └── expected/           # Expected output shape
├── 02-operational-memory/
│   ├── inbox/              # Work residue and notes
│   ├── prompts/            # Daily and weekly prompts
│   ├── outputs/            # Generated logs
│   └── schedule/           # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/                # Exported email files
│   ├── prompts/            # Triage prompts
│   ├── outputs/            # Action lists
│   └── expected/           # Expected output shape
└── mission-control/        # Optional dashboard
```

## Workflow 1: Data Intake Review

Process unknown incoming files into structured reports.

### Setup

```bash
cd code-along/01-data-intake-review
```

### Usage Pattern

1. Place files to review in `incoming/`
2. Read the prompt template in `prompts/intake-review.md`
3. Provide the prompt and files to your AI assistant
4. Save output to `outputs/intake-review.md`

### Example Prompt Structure

```markdown
# Data Intake Review

Analyze all files in the incoming/ directory and generate a report with:

## Summary
- Total files processed
- File types encountered
- Overall assessment

## Detailed Review
For each file:
- Filename
- Type/format
- Size
- Content summary
- Recommendations
- Priority level

## Action Items
- Immediate actions needed
- Follow-up required
- Files to archive
```

### Expected Output

```markdown
# Intake Review Report
Generated: 2026-05-11

## Summary
- 5 files processed
- Types: PDF (2), CSV (1), TXT (2)
- Status: 3 actionable, 2 informational

## Files

### document.pdf
- Type: PDF contract
- Size: 245 KB
- Priority: HIGH
- Action: Legal review required by EOW
...
```

## Workflow 2: Operational Memory

Transform daily work residue into momentum documentation.

### Setup

```bash
cd code-along/02-operational-memory
```

### Daily Log Workflow

Place notes, snippets, and work residue in `inbox/`, then use the daily log prompt:

```markdown
# Daily Log Prompt

Review all items in inbox/ and generate a daily log with:

## Today's Focus
What was the primary work?

## Completed
- Specific accomplishments
- Decisions made
- Problems solved

## In Progress
- Active work streams
- Blockers

## Notes & Context
- Key learnings
- Links and references
- Ideas captured

## Tomorrow's Queue
Priority items for next session
```

### Weekly Summary Workflow

```markdown
# Weekly Hype Prompt

Review the past week's daily logs and generate:

## Week Highlights
Top 3-5 achievements

## Momentum
What's building? What shipped?

## Patterns
Recurring themes or blockers

## Next Week
Strategic priorities
```

### Automation Example

Add to crontab for daily automation:

```bash
# Daily log generation at 6 PM
0 18 * * * cd ~/openclaw-assistant/code-along/02-operational-memory && ./generate-daily-log.sh

# Weekly summary on Friday at 5 PM
0 17 * * 5 cd ~/openclaw-assistant/code-along/02-operational-memory && ./generate-weekly-hype.sh
```

### Example Shell Script

```bash
#!/bin/bash
# generate-daily-log.sh

INBOX_DIR="./inbox"
PROMPT_FILE="./prompts/daily-log.md"
OUTPUT_FILE="./outputs/daily-log-$(date +%Y-%m-%d).md"

# Use your preferred AI CLI tool
# Example with hypothetical openclaw CLI:
cat $PROMPT_FILE | openclaw --context $INBOX_DIR > $OUTPUT_FILE

echo "Daily log generated: $OUTPUT_FILE"
```

## Workflow 3: Offline Communications Triage

Process exported emails into actionable markdown.

### Setup

```bash
cd code-along/03-offline-communications-triage
```

### Email Export

Export emails from your client as `.eml` files into the `eml/` directory.

### Triage Prompt

```markdown
# Email Triage Prompt

Review all .eml files and generate an action report:

## Urgent (Respond Today)
- Sender
- Subject
- Key ask
- Suggested response

## Important (This Week)
- Sender
- Subject
- Context
- Next step

## Informational
- Subject
- Summary
- Archive/keep decision

## Delegated/Waiting
- Subject
- Who/what waiting on
- Follow-up date

## Summary Stats
- Total emails processed
- Action required count
- Estimated time to clear
```

### Expected Output

```markdown
# Email Triage Report
Generated: 2026-05-11 09:00

## Urgent (3)

### From: sarah@example.com
**Subject:** Q2 Budget Approval Needed
**Ask:** Sign off on $50K marketing spend
**Suggested Response:** Approve with note about ROI tracking
**Deadline:** Today EOD

## Important (7)
...

## Summary
- 45 emails processed
- 10 require action
- Estimated 2 hours to clear urgent/important
```

## Key Patterns

### 1. Prompt-First Design

Store reusable prompts in `prompts/` directories:

```markdown
prompts/
├── intake-review.md
├── daily-log.md
├── weekly-hype.md
└── email-triage.md
```

### 2. Folder-Based Context

Organize work by context directories that AI can scan:

```text
inbox/          # Unprocessed work
outputs/        # Generated artifacts
expected/       # Reference examples
```

### 3. Markdown Artifacts

All outputs are reviewable, versionable markdown:

```bash
git add outputs/
git commit -m "Weekly hype report 2026-W19"
```

### 4. Copy-Paste First, Automate Later

Start with manual copy-paste workflows, add cron/scripts once patterns stabilize.

## Configuration

### Environment Setup

```bash
# Set your preferred AI assistant CLI
export OPENCLAW_CLI="claude"  # or cursor, codex, etc.

# Set working directory
export OPENCLAW_WORKSPACE="$HOME/openclaw-assistant"
```

### Customizing Prompts

Edit prompt templates to match your workflow:

```bash
# Edit the daily log prompt
code code-along/02-operational-memory/prompts/daily-log.md
```

## Troubleshooting

### Outputs are too generic

**Solution:** Add more specific context in prompts. Include examples of desired format.

```markdown
## Example Good Output
- ✅ "Shipped: User auth flow with OAuth2"
Not:
- ❌ "Made progress on auth"
```

### Too much manual work

**Solution:** Start with the exercise that saves the most time. Automate one workflow fully before expanding.

### Files not being processed

**Solution:** Check file paths are relative to the exercise directory:

```bash
# Run from exercise root
cd code-along/01-data-intake-review
# Not from repo root
```

### Cron jobs not running

**Solution:** Use absolute paths in cron scripts:

```bash
0 18 * * * /usr/bin/bash /home/user/openclaw-assistant/code-along/02-operational-memory/generate-daily-log.sh
```

## Advanced Usage

### Chain Workflows

Use outputs from one workflow as inputs to another:

```bash
# Triage creates action items
./triage-emails.sh

# Feed actions into daily log
cp outputs/email-triage.md ../02-operational-memory/inbox/
```

### Mission Control Dashboard

Create a simple HTML index linking to all artifacts:

```html
<!DOCTYPE html>
<html>
<head><title>Mission Control</title></head>
<body>
  <h1>OpenClaw Assistant</h1>
  <h2>Recent Reports</h2>
  <ul>
    <li><a href="../01-data-intake-review/outputs/intake-review.md">Latest Intake</a></li>
    <li><a href="../02-operational-memory/outputs/daily-log.md">Today's Log</a></li>
    <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
  </ul>
</body>
</html>
```

## Best Practices

1. **Review all generated content** - AI assistants can hallucinate; always verify outputs
2. **Version control outputs** - Commit markdown artifacts to track changes over time
3. **Start small** - Master one workflow before adding complexity
4. **Iterate prompts** - Refine prompt templates based on output quality
5. **Keep it local** - Maintain the local-first principle; no live API integrations required
