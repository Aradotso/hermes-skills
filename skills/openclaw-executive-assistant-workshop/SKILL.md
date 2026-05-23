---
name: openclaw-executive-assistant-workshop
description: Workshop for building local-first OpenClaw executive assistant workflows with data intake, operational memory, and communications triage
triggers:
  - "help me build an OpenClaw executive assistant"
  - "how do I set up OpenClaw for personal workflow automation"
  - "show me OpenClaw data intake patterns"
  - "create operational memory with OpenClaw"
  - "triage emails with OpenClaw locally"
  - "build local-first AI assistant workflows"
  - "OpenClaw workshop walkthrough"
  - "set up OpenClaw for executive assistant tasks"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

This workshop teaches you to build a local-first executive assistant using OpenClaw. The project demonstrates three core workflows:

1. **Data intake review** - Transform unknown files into structured reports
2. **Operational memory** - Convert work residue into daily/weekly momentum docs
3. **Offline communications triage** - Process exported emails into action lists

All operations use local folders, markdown artifacts, and copy/paste prompts. No live integrations or external APIs required.

## Workshop Structure

```text
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

### Prerequisites

- OpenClaw (or any AI coding assistant)
- Local file system access
- Markdown viewer

### Installation

```bash
# Clone the workshop repo
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the main walkthrough
open webinar-runbook.html
```

## Exercise 1: Data Intake Review

Transform unknown files into a trustworthy intake report.

### Workflow

```bash
cd code-along/01-data-intake-review
```

1. Place files to review in `incoming/`
2. Open `prompts/intake-review.md` and copy the prompt
3. Run the prompt against files in `incoming/`
4. Save output to `outputs/intake-review.md`

### Expected Output Structure

```markdown
# Data Intake Review

## Summary
Brief overview of files processed

## Files Processed
- filename.ext: description, purpose, risk assessment

## Recommendations
- Action items
- Follow-up needed
- Archive/delete suggestions

## Risk Assessment
Overall security and quality notes
```

### Prompt Pattern

```markdown
Review the files in the incoming/ folder.

For each file:
- Identify file type and purpose
- Extract key information
- Assess risk level (low/medium/high)
- Recommend actions

Generate a structured markdown report.
```

## Exercise 2: Operational Memory

Convert work residue into daily and weekly momentum documents.

### Workflow

```bash
cd code-along/02-operational-memory
```

#### Daily Log

1. Place work notes in `inbox/`
2. Copy prompt from `prompts/daily-log.md`
3. Generate `outputs/daily-log.md`

```markdown
# Daily Log - YYYY-MM-DD

## Completed
- Task descriptions

## In Progress
- Current work items

## Blockers
- Issues needing resolution

## Tomorrow
- Planned activities
```

#### Weekly Summary

1. Use existing daily logs
2. Copy prompt from `prompts/weekly-hype.md`
3. Generate `outputs/weekly-hype.md`

```markdown
# Weekly Hype - Week of YYYY-MM-DD

## Wins
Major accomplishments this week

## Momentum
Ongoing initiatives and progress

## Learn
Insights and improvements

## Next Week
Priorities and focus areas
```

### Automation Pattern

Set up scheduled generation using cron (see `schedule/cron-examples.md`):

```bash
# Daily log at 5 PM
0 17 * * * /path/to/generate-daily-log.sh

# Weekly summary on Friday at 4 PM
0 16 * * 5 /path/to/generate-weekly-hype.sh
```

### Heartbeat Pattern

Create a recurring note check (see `schedule/heartbeat-note.md`):

```bash
#!/bin/bash
# Check for new notes and trigger log generation
if [ -n "$(ls -A code-along/02-operational-memory/inbox/)" ]; then
    echo "New notes detected - generate daily log"
fi
```

## Exercise 3: Offline Communications Triage

Process exported emails into prioritized action lists.

### Workflow

```bash
cd code-along/03-offline-communications-triage
```

1. Export emails as `.eml` files to `eml/`
2. Copy prompt from `prompts/email-triage.md`
3. Generate `outputs/email-triage.md`

### Expected Output Structure

```markdown
# Email Triage - YYYY-MM-DD

## Urgent
- Subject: brief description [action needed]

## Important
- Subject: brief description [action needed]

## FYI
- Subject: brief description [no action]

## Archive
- Subject: can be archived

## Summary
Total processed, action count, response time estimates
```

### Triage Prompt Pattern

```markdown
Process all .eml files in the eml/ folder.

For each email:
- Extract sender, subject, date
- Classify urgency (urgent/important/FYI/archive)
- Identify required actions
- Estimate response time

Generate prioritized markdown report.
```

## Mission Control (Optional)

Create a central dashboard linking to all generated artifacts:

```markdown
# Mission Control

Last updated: YYYY-MM-DD HH:MM

## Today
- [Daily Log](../02-operational-memory/outputs/daily-log.md)
- [Email Triage](../03-offline-communications-triage/outputs/email-triage.md)

## This Week
- [Weekly Hype](../02-operational-memory/outputs/weekly-hype.md)

## Recent Intake
- [Latest Review](../01-data-intake-review/outputs/intake-review.md)
```

## Common Patterns

### Local-First Workflow

```bash
# 1. Prepare input
cp ~/Downloads/document.pdf code-along/01-data-intake-review/incoming/

# 2. Run prompt via AI assistant
# (copy/paste prompt content)

# 3. Save output
# (save assistant response to outputs/)

# 4. Review and iterate
open code-along/01-data-intake-review/outputs/intake-review.md
```

### Batch Processing

```bash
# Process multiple files at once
for file in incoming/*; do
    echo "Processing: $file"
done
```

### Archive Pattern

```bash
# Move processed files to archive
mkdir -p archive/$(date +%Y-%m-%d)
mv incoming/* archive/$(date +%Y-%m-%d)/
```

## Configuration

### Environment Variables

```bash
# Set your preferred AI assistant
export AI_ASSISTANT="openclaw"

# Set workspace root
export WORKSPACE_ROOT="/path/to/code-along"

# Set output format
export OUTPUT_FORMAT="markdown"
```

### Folder Conventions

- `incoming/`, `inbox/`, `eml/` - Input folders
- `outputs/` - Generated artifacts
- `prompts/` - Reusable prompt templates
- `expected/` - Reference outputs
- `archive/` - Processed files (create as needed)

## Troubleshooting

### Files Not Processing

```bash
# Check file permissions
ls -la code-along/01-data-intake-review/incoming/

# Verify file types
file incoming/*
```

### Output Not Generated

- Verify prompt was copied completely
- Check AI assistant has file system access
- Ensure outputs/ folder exists and is writable

### Cron Jobs Not Running

```bash
# Check cron syntax
crontab -l

# Test script manually
bash /path/to/generate-daily-log.sh

# Check cron logs
grep CRON /var/log/syslog
```

### Missing Expected Sections

- Review expected output templates in `expected/` folders
- Adjust prompts to be more specific about required sections
- Provide examples in the prompt

## Best Practices

1. **Keep prompts reusable** - Store in `prompts/` for consistency
2. **Review before archive** - Always check outputs before moving inputs
3. **Use dated folders** - Organize archives by date for easy retrieval
4. **Iterate prompts** - Refine based on output quality
5. **Start small** - Test with 1-2 files before batch processing
6. **Local only** - No external APIs or credentials needed

## Extension Ideas

- Create HTML dashboard for mission-control
- Add PDF export for weekly summaries
- Integrate with local calendar for scheduling
- Build search index over generated artifacts
- Add tagging and categorization system
