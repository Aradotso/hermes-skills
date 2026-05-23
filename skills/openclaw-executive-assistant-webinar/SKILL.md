---
name: openclaw-executive-assistant-webinar
description: Workshop materials for building local-first executive assistant workflows with OpenClaw using file-based prompts and markdown outputs
triggers:
  - build an executive assistant with openclaw
  - set up openclaw workflow automation
  - create local-first AI assistant workflows
  - implement openclaw data intake review
  - configure openclaw operational memory
  - triage emails with openclaw locally
  - run openclaw workshop exercises
  - automate daily logs with openclaw
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

This project provides starter files and exercises for building local-first executive assistant workflows using OpenClaw. It focuses on three core patterns:

1. **Data intake review** - Turn unknown files into trustworthy intake reports
2. **Operational memory** - Convert work residue into daily/weekly momentum documents
3. **Offline communications triage** - Transform exported emails into action lists

All workflows stay local, use copy/paste prompts, and produce reviewable markdown artifacts.

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
│   └── expected/
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/
│   │   └── email-triage.md
│   ├── outputs/
│   └── expected/
│       └── report-outline.md
└── mission-control/       # Optional dashboard
```

## Getting Started

### Prerequisites

- OpenClaw or compatible AI assistant (Claude, Cursor, etc.)
- Local file system access
- Text editor

### Installation

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git

# Navigate to the project
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the walkthrough
open webinar-runbook.html
```

## Core Workflows

### 1. Data Intake Review

Convert unknown files into structured intake reports.

**Input:** Files in `code-along/01-data-intake-review/incoming/`

**Prompt:** Use contents from `code-along/01-data-intake-review/prompts/intake-review.md`

**Example workflow:**

```bash
# Navigate to the exercise
cd code-along/01-data-intake-review

# Review incoming files
ls -la incoming/

# Copy the prompt content
cat prompts/intake-review.md

# Pass to AI agent with context:
# "Review all files in ./incoming/ and generate an intake report"

# Expected output location
# outputs/intake-review.md
```

**Output structure:**
- File inventory
- Content summaries
- Risk assessment
- Recommended actions
- Priority ranking

### 2. Operational Memory

Transform daily work residue into momentum documents.

**Daily Log Pattern:**

```bash
cd code-along/02-operational-memory

# Review inbox materials
ls -la inbox/

# Use daily log prompt
cat prompts/daily-log.md

# Generate daily log
# Output: outputs/daily-log.md
```

**Weekly Summary Pattern:**

```bash
# Use weekly hype prompt
cat prompts/weekly-hype.md

# Generate weekly summary
# Output: outputs/weekly-hype.md
```

**Automation with cron:**

```bash
# View cron examples
cat schedule/cron-examples.md

# Example daily log generation (9 AM weekdays)
# 0 9 * * 1-5 /path/to/generate-daily-log.sh

# Example weekly summary (Friday 4 PM)
# 0 16 * * 5 /path/to/generate-weekly-hype.sh
```

**Expected artifacts:**
- `outputs/daily-log.md` - Daily accomplishments and blockers
- `outputs/weekly-hype.md` - Weekly momentum summary

### 3. Offline Communications Triage

Convert exported emails into actionable task lists.

```bash
cd code-along/03-offline-communications-triage

# Review exported emails
ls -la eml/

# Use triage prompt
cat prompts/email-triage.md

# Generate triage report
# Output: outputs/email-triage.md
```

**Email export workflow:**
1. Export emails as `.eml` files from your mail client
2. Place in `eml/` directory
3. Run triage prompt against all files
4. Review generated action list

**Output structure:**
- Urgent actions
- Follow-ups needed
- FYI items
- Delegatable tasks
- Archive candidates

## Prompt Engineering Patterns

### Standard Prompt Structure

All prompts follow this pattern:

```markdown
# Task: [Clear objective]

## Context
[Background information]

## Input
[Location of files to process]

## Output Format
[Desired structure]

## Instructions
1. [Step by step]
2. [Clear directives]
3. [Quality criteria]
```

### Using Prompts with AI Agents

```bash
# Pattern 1: Direct file reference
"Use the prompt in prompts/intake-review.md to analyze all files in incoming/"

# Pattern 2: Copy-paste
# 1. Open prompt file
# 2. Copy contents
# 3. Paste to AI agent with file context

# Pattern 3: Batch processing
"Process each .eml file in eml/ using the triage rules in prompts/email-triage.md"
```

## Configuration

### Environment Variables

```bash
# Optional: Set output directories
export OPENCLAW_OUTPUT_DIR="$HOME/Documents/openclaw-outputs"

# Optional: Set default prompt directory
export OPENCLAW_PROMPTS="$HOME/.openclaw/prompts"

# Optional: Archive location
export OPENCLAW_ARCHIVE="$HOME/Documents/openclaw-archive"
```

### Customizing Prompts

Edit prompt files directly:

```bash
# Edit intake review prompt
nano code-along/01-data-intake-review/prompts/intake-review.md

# Add custom sections:
## Custom Section
- Your specific requirement
- Additional criteria
```

## Common Patterns

### Daily Automation Workflow

```bash
#!/bin/bash
# daily-assistant.sh

DATE=$(date +%Y-%m-%d)
WORK_DIR="$HOME/work-inbox"
OUTPUT_DIR="$HOME/logs"

# Collect daily residue
cd "$WORK_DIR"

# Generate log (with AI agent)
# Pass to Claude/Cursor: "Generate daily log from files in $WORK_DIR"

# Save with date
mv daily-log.md "$OUTPUT_DIR/$DATE-log.md"
```

### Weekly Review Workflow

```bash
#!/bin/bash
# weekly-review.sh

WEEK=$(date +%Y-W%V)
LOGS_DIR="$HOME/logs"
OUTPUT="$HOME/reviews/$WEEK-review.md"

# Aggregate week's logs
cat "$LOGS_DIR"/*-log.md > /tmp/week-aggregate.md

# Generate summary (with AI agent)
# "Create weekly summary from /tmp/week-aggregate.md"
```

### Inbox Zero Pattern

```bash
# 1. Export emails to eml/
# 2. Run triage
# 3. Generate action list
# 4. Move processed emails to archive/

cd code-along/03-offline-communications-triage
mkdir -p archive
# After triage: mv eml/*.eml archive/
```

## Troubleshooting

### Missing Output Files

```bash
# Check output directory exists
mkdir -p code-along/01-data-intake-review/outputs

# Verify permissions
ls -la code-along/01-data-intake-review/outputs/
```

### Empty Incoming Directories

```bash
# Verify files exist
ls -la code-along/*/incoming/
ls -la code-along/*/inbox/
ls -la code-along/*/eml/

# Add sample files if needed
touch code-along/01-data-intake-review/incoming/sample.txt
```

### Prompt Not Working

- Ensure full file context is provided to AI agent
- Check that relative paths are correct
- Verify input files are accessible
- Review expected output format in `expected/` directories

### Automation Issues

```bash
# Test cron job manually
bash -x /path/to/script.sh

# Check cron logs
tail -f /var/log/cron

# Verify paths are absolute in cron scripts
which openclaw
```

## Best Practices

1. **Review before acting** - Always review generated reports before taking action
2. **Version control** - Keep outputs in dated folders or git
3. **Iterate prompts** - Refine prompts based on output quality
4. **Local-first** - Keep sensitive data local; don't send to external APIs
5. **Backup originals** - Archive source files before processing
6. **Scheduled reviews** - Run operational memory workflows daily/weekly

## Integration Examples

### With Mission Control Dashboard

```bash
# Create index page
cd code-along/mission-control

# Link to latest outputs
echo "# Mission Control" > index.md
echo "- [Latest Intake](../01-data-intake-review/outputs/intake-review.md)" >> index.md
echo "- [Today's Log](../02-operational-memory/outputs/daily-log.md)" >> index.md
echo "- [Email Triage](../03-offline-communications-triage/outputs/email-triage.md)" >> index.md
```

### With Git for Version Control

```bash
# Track outputs
git add code-along/*/outputs/*.md
git commit -m "Daily assistant outputs $(date +%Y-%m-%d)"
```
