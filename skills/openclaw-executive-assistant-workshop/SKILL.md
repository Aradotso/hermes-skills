---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows using OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "set up an OpenClaw executive assistant workflow"
  - "create a data intake review system with OpenClaw"
  - "build operational memory daily and weekly logs"
  - "triage emails offline with AI"
  - "use OpenClaw for executive assistant tasks"
  - "create markdown-based workflow automation"
  - "set up local-only AI assistant workflows"
  - "generate intake reports from unknown files"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill covers building local-first AI executive assistant workflows using OpenClaw. The workshop demonstrates three core patterns: data intake review, operational memory management, and offline communications triage. All workflows operate on local files and produce reviewable markdown artifacts.

## What This Project Does

The OpenClaw Executive Assistant Workshop provides:

1. **Data Intake Review** - Transform unknown files into structured intake reports
2. **Operational Memory** - Convert work residue into daily logs and weekly summaries
3. **Offline Communications Triage** - Process exported emails into actionable task lists

All workflows use:
- Local-only file processing
- Copy/paste prompt patterns
- Markdown output artifacts
- No live API integrations during workshop

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/           # Files to inspect
│   ├── prompts/
│   │   └── intake-review.md
│   ├── outputs/            # Generated reports
│   └── expected/
│       └── report-outline.md
├── 02-operational-memory/
│   ├── inbox/              # Notes and work residue
│   ├── prompts/
│   │   ├── daily-log.md
│   │   └── weekly-hype.md
│   ├── outputs/
│   ├── schedule/
│   │   ├── cron-examples.md
│   │   └── heartbeat-note.md
│   └── expected/
├── 03-offline-communications-triage/
│   ├── eml/                # Exported email files
│   ├── prompts/
│   │   └── email-triage.md
│   ├── outputs/
│   └── expected/
│       └── report-outline.md
└── mission-control/        # Optional dashboard
```

## Exercise 1: Data Intake Review

Transform unknown incoming files into a trustworthy intake report.

### Setup

```bash
cd code-along/01-data-intake-review
ls incoming/  # Review files to process
cat prompts/intake-review.md  # View the prompt
```

### Workflow

1. Review files in `incoming/` directory
2. Use prompt from `prompts/intake-review.md` with your AI assistant
3. Include file contents, names, and metadata in context
4. Generate structured report to `outputs/intake-review.md`

### Expected Output Structure

```markdown
# Intake Review Report

## Executive Summary
[Brief overview of files received]

## Files Processed
- File 1: [name] - [type] - [key findings]
- File 2: [name] - [type] - [key findings]

## Action Items
1. [Required action]
2. [Required action]

## Risk Assessment
[Any concerns or flags]

## Recommended Next Steps
[Prioritized recommendations]
```

### Prompt Pattern

```markdown
Review the following files from my intake folder and create a structured 
intake report. For each file:

1. Identify the file type and purpose
2. Extract key information
3. Flag any risks or concerns
4. Suggest appropriate actions

Files to review:
[Paste file contents or descriptions here]

Generate a markdown report with sections: Executive Summary, Files Processed,
Action Items, Risk Assessment, and Recommended Next Steps.
```

## Exercise 2: Operational Memory

Convert work residue into daily logs and weekly momentum documents.

### Daily Log Workflow

```bash
cd code-along/02-operational-memory
ls inbox/  # Review notes and work residue
cat prompts/daily-log.md
```

Generate daily log:

```markdown
# Prompt pattern for daily log

Review my work notes from today and create a daily log that captures:

1. **Completed** - What was finished
2. **In Progress** - Active work streams
3. **Blocked** - Issues needing attention
4. **Tomorrow** - Planned priorities

Work notes:
[Paste contents of inbox/ files]

Format as clean markdown saved to outputs/daily-log.md
```

### Weekly Summary Workflow

```bash
cat prompts/weekly-hype.md
```

Generate weekly summary:

```markdown
# Prompt pattern for weekly summary

Review this week's daily logs and create a weekly summary with:

1. **Wins** - Key accomplishments
2. **Momentum** - Active initiatives
3. **Challenges** - Obstacles encountered
4. **Next Week** - Priorities and goals

Daily logs:
[Paste week's daily logs]

Create an energizing, momentum-focused summary in outputs/weekly-hype.md
```

### Automation with Cron

Example cron setup for automated daily logs:

```bash
# Run daily log generation every weekday at 5pm
0 17 * * 1-5 cd /path/to/code-along/02-operational-memory && ./generate-daily-log.sh

# Run weekly summary every Friday at 6pm
0 18 * * 5 cd /path/to/code-along/02-operational-memory && ./generate-weekly-hype.sh
```

Example `generate-daily-log.sh`:

```bash
#!/bin/bash
# Generate daily log from inbox files

DATE=$(date +%Y-%m-%d)
PROMPT_FILE="prompts/daily-log.md"
OUTPUT_FILE="outputs/daily-log-${DATE}.md"

# Combine inbox files
NOTES=$(cat inbox/*.md 2>/dev/null)

# Call AI assistant with prompt (pseudo-code)
# In practice, integrate with your preferred AI CLI tool
echo "Generating daily log for ${DATE}..."
# ai-cli --prompt-file "${PROMPT_FILE}" --context "${NOTES}" > "${OUTPUT_FILE}"

echo "Daily log saved to ${OUTPUT_FILE}"
```

## Exercise 3: Offline Communications Triage

Process exported email files into actionable task lists.

### Setup

```bash
cd code-along/03-offline-communications-triage
ls eml/  # Review exported .eml files
cat prompts/email-triage.md
```

### Workflow

1. Export emails from your client as `.eml` files to `eml/` directory
2. Use triage prompt with email contents
3. Generate prioritized action list

### Prompt Pattern

```markdown
Triage these exported emails and create an action list:

For each email:
1. Sender and subject
2. Priority level (High/Medium/Low)
3. Required action
4. Deadline (if applicable)
5. Context needed

Emails to triage:
[Paste .eml file contents]

Output sections:
- High Priority Actions
- Medium Priority Actions
- Low Priority Actions
- FYI Only (no action needed)

Save to outputs/email-triage.md
```

### Expected Output

```markdown
# Email Triage Report
Generated: 2026-05-11

## High Priority Actions
- [ ] **From:** John Doe | **Subject:** Budget approval needed
  - **Action:** Review and approve Q2 budget by EOD Tuesday
  - **Deadline:** 2026-05-13
  - **Context:** Finance team blocked until approved

## Medium Priority Actions
- [ ] **From:** Jane Smith | **Subject:** Meeting reschedule
  - **Action:** Propose 3 alternative times this week
  - **Deadline:** 2026-05-15

## Low Priority Actions
- [ ] **From:** Newsletter | **Subject:** Industry updates
  - **Action:** Review for relevant trends
  - **Deadline:** None

## FYI Only
- **From:** HR | **Subject:** Company update
  - No action required, informational only
```

## Common Patterns

### Batch Processing Multiple Files

```bash
# Process all files in a directory
for file in incoming/*; do
  echo "Processing: $file"
  # Pass to AI assistant with appropriate prompt
done
```

### Context Window Management

When dealing with many files:

```markdown
# For large batches, create a manifest first

Files to process:
1. document1.pdf - 50 pages - Contract
2. report.xlsx - Financial data Q1
3. notes.txt - Meeting notes

Then process high-priority items first with full context.
```

### Linking Workflows

```bash
# Chain exercises together
code-along/
├── 01-data-intake-review/outputs/intake-review.md
├── 02-operational-memory/inbox/
│   └── [link to intake items requiring follow-up]
└── 03-offline-communications-triage/outputs/
    └── [link to communication-driven action items]
```

## Configuration

### Setting Up Your Workspace

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw/code-along

# Create outputs directories
mkdir -p 01-data-intake-review/outputs
mkdir -p 02-operational-memory/outputs
mkdir -p 03-offline-communications-triage/outputs
```

### Environment Variables

```bash
# If integrating with AI APIs
export OPENAI_API_KEY="your-api-key"
export ANTHROPIC_API_KEY="your-api-key"

# Workspace paths
export OPENCLAW_WORKSPACE="/path/to/code-along"
export OPENCLAW_ARCHIVE="/path/to/archive"
```

## Troubleshooting

### Empty Output Files

**Problem:** Generated markdown files are empty or incomplete.

**Solution:** 
- Verify input files exist in the correct directories
- Check that prompts include sufficient context
- Ensure AI assistant has access to file contents

### Prompt Context Too Large

**Problem:** Too many files to process in single prompt.

**Solution:**
```markdown
# Break into smaller batches
Batch 1: Process files 1-5
Batch 2: Process files 6-10
Then combine summaries
```

### Missing Expected Sections

**Problem:** Generated reports missing key sections.

**Solution:**
- Review prompt templates in `prompts/` directories
- Compare against `expected/` outline files
- Add explicit section requirements to prompt

### Cron Jobs Not Running

**Problem:** Automated generation scripts not executing.

**Solution:**
```bash
# Check cron logs
grep CRON /var/log/syslog

# Verify script permissions
chmod +x generate-daily-log.sh

# Test script manually first
./generate-daily-log.sh
```

## Best Practices

1. **Review Before Archiving** - Always review generated artifacts before moving source files
2. **Consistent Naming** - Use ISO date formats: `YYYY-MM-DD` for time-based artifacts
3. **Version Control** - Commit prompts to git, but consider `.gitignore` for sensitive outputs
4. **Iterative Refinement** - Adjust prompts based on output quality
5. **Manual Override** - Keep workflows reviewable and editable by humans

## Integration Examples

### With Task Management

```markdown
# Convert triage output to task format
outputs/email-triage.md → import to task manager as individual tasks
```

### With Note-Taking Systems

```markdown
# Link operational memory to knowledge base
outputs/daily-log.md → [[Daily Logs/2026-05-11]]
outputs/weekly-hype.md → [[Weekly Reviews/2026-W19]]
```

### With File Organization

```bash
# Archive processed files
mv 01-data-intake-review/incoming/* archive/2026-05/processed/
```
