---
name: openclaw-executive-assistant-webinar
description: Build local-first AI executive assistants with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "help me build an executive assistant with OpenClaw"
  - "how do I use OpenClaw for email triage"
  - "create a daily log with OpenClaw"
  - "set up operational memory workflow"
  - "process incoming files with OpenClaw"
  - "generate weekly summary reports"
  - "OpenClaw local-first workflows"
  - "build AI assistant for data intake"
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

This project provides a workshop framework for building local-first AI executive assistants using OpenClaw. It demonstrates three core workflows: data intake review, operational memory management, and offline communications triage. All processing happens locally with markdown artifacts, using copy/paste prompts with AI assistants.

The workshop teaches you to transform unstructured work inputs (files, notes, emails) into structured, actionable markdown reports without external integrations.

## Installation

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# No dependencies to install - this is a file-based workshop
# Open webinar-runbook.html in your browser for the full walkthrough
```

## Project Structure

```
code-along/
├── INDEX.md                              # Workshop overview
├── 01-data-intake-review/                # Exercise 1: File intake
│   ├── incoming/                         # Files to process
│   ├── prompts/intake-review.md          # Prompt template
│   ├── outputs/                          # Generated reports
│   └── expected/report-outline.md        # Expected output format
├── 02-operational-memory/                # Exercise 2: Daily/weekly logs
│   ├── inbox/                            # Work notes and residue
│   ├── prompts/daily-log.md              # Daily prompt
│   ├── prompts/weekly-hype.md            # Weekly prompt
│   ├── outputs/                          # Generated logs
│   └── schedule/                         # Automation examples
├── 03-offline-communications-triage/     # Exercise 3: Email processing
│   ├── eml/                              # Exported email files
│   ├── prompts/email-triage.md           # Triage prompt
│   ├── outputs/                          # Generated action lists
│   └── expected/report-outline.md        # Expected output format
└── mission-control/                      # Optional dashboard
```

## Core Workflows

### 1. Data Intake Review

Transform unknown files into structured intake reports.

**Input**: Mixed files in `01-data-intake-review/incoming/`

**Prompt location**: `01-data-intake-review/prompts/intake-review.md`

**Usage pattern**:
```markdown
# Copy the prompt from intake-review.md
# Provide the prompt to your AI assistant (Claude, ChatGPT, etc.)
# Include the contents of files from incoming/
# Save the generated report to outputs/intake-review.md
```

**Expected output structure**:
```markdown
# Intake Review Report
Generated: [DATE]

## Summary
[High-level overview]

## Files Processed
- file1.ext - [description]
- file2.ext - [description]

## Key Findings
- [Finding 1]
- [Finding 2]

## Recommended Actions
1. [Action item]
2. [Action item]

## Risk Assessment
[Security, privacy, or other concerns]
```

### 2. Operational Memory

Create daily logs and weekly summaries from work notes.

**Daily Log Workflow**:
```markdown
# Input: Notes from inbox/ folder
# Prompt: prompts/daily-log.md
# Output: outputs/daily-log.md

## Daily Log Format
- Date: [DATE]
- Completed Today:
  - [Task]
  - [Task]
- In Progress:
  - [Task] - [status]
- Blockers:
  - [Issue]
- Tomorrow's Focus:
  - [Priority]
```

**Weekly Summary Workflow**:
```markdown
# Input: Multiple daily logs or week's work notes
# Prompt: prompts/weekly-hype.md
# Output: outputs/weekly-hype.md

## Weekly Summary Format
- Week of: [DATE]
- Major Accomplishments:
  - [Achievement]
- Momentum Indicators:
  - [Metric/progress]
- Upcoming Priorities:
  - [Priority]
- Learnings:
  - [Insight]
```

**Automation example** (from `schedule/cron-examples.md`):
```bash
# Daily log generation at 5 PM
0 17 * * * cd /path/to/project && ./generate-daily-log.sh

# Weekly summary on Friday at 4 PM
0 16 * * 5 cd /path/to/project && ./generate-weekly-summary.sh
```

### 3. Offline Communications Triage

Process exported emails into prioritized action lists.

**Input**: `.eml` files in `03-offline-communications-triage/eml/`

**Prompt location**: `03-offline-communications-triage/prompts/email-triage.md`

**Usage pattern**:
```markdown
# Export emails as .eml files to the eml/ folder
# Copy the triage prompt
# Provide email contents to your AI assistant
# Save the triage report to outputs/email-triage.md
```

**Expected output structure**:
```markdown
# Email Triage Report
Processed: [DATE]

## High Priority (Respond Today)
- From: [sender]
  Subject: [subject]
  Action: [required response]
  Deadline: [if any]

## Medium Priority (This Week)
- [Similar format]

## Low Priority (FYI/Archive)
- [Similar format]

## Spam/Ignore
- [List of irrelevant emails]

## Draft Responses
### Email to [recipient]
[Suggested response text]
```

## Configuration

This is a file-based workshop with no configuration files. Customize by:

1. **Modify prompts** in `prompts/*.md` files to match your workflow
2. **Adjust output formats** by editing `expected/*.md` templates
3. **Add automation** using the cron examples in `schedule/`

## Real-World Implementation Examples

### Example: Daily Standup Report

```markdown
# File: 02-operational-memory/inbox/2026-05-12-notes.md
- Finished user auth refactor
- Started API rate limiting implementation
- Blocked on database migration approval
- Meeting with product team re: Q3 roadmap

# Processed with prompts/daily-log.md → outputs/daily-log.md
# Daily Log - 2026-05-12

## Completed
- ✅ User authentication refactor merged to main
- ✅ Code review for payment service updates

## In Progress
- 🔄 API rate limiting (60% complete, testing phase)
- 🔄 Database migration plan (awaiting approval)

## Blockers
- ⚠️ Database migration needs DevOps sign-off

## Tomorrow
- Continue rate limiting tests
- Follow up on migration approval
- Prep for Q3 planning meeting
```

### Example: Email Triage Automation Script

```bash
#!/bin/bash
# File: 03-offline-communications-triage/triage-emails.sh

EMAILS_DIR="./eml"
PROMPT_FILE="./prompts/email-triage.md"
OUTPUT_FILE="./outputs/email-triage-$(date +%Y-%m-%d).md"

# Check for new .eml files
if [ -n "$(ls -A $EMAILS_DIR/*.eml 2>/dev/null)" ]; then
    echo "Processing emails..."
    # In practice, pipe to your AI CLI tool here
    # Example: openclaw-cli process --prompt "$PROMPT_FILE" --input "$EMAILS_DIR" --output "$OUTPUT_FILE"
    echo "Triage report generated: $OUTPUT_FILE"
else
    echo "No emails to process"
fi
```

## Common Patterns

### Pattern: Batch Processing

```markdown
# Process multiple days of notes at once
for file in 02-operational-memory/inbox/*.md; do
    echo "Processing $file"
    # Pipe to AI assistant with daily-log.md prompt
    # Save to outputs/daily-log-$(basename $file)
done
```

### Pattern: Template Customization

```markdown
# Customize intake-review.md for security-focused review
Add to prompt:
"Focus specifically on:
- API keys or credentials
- PII or sensitive data
- Security vulnerabilities
- Compliance requirements"
```

### Pattern: Weekly Rollup

```markdown
# Combine daily logs into weekly summary
cat outputs/daily-log-*.md | \
    # Feed to AI with weekly-hype.md prompt
    # Generate outputs/weekly-hype-2026-W19.md
```

## Troubleshooting

### No output generated
- Verify you're copying the full prompt from `prompts/*.md`
- Ensure input files exist in the correct `incoming/`, `inbox/`, or `eml/` folder
- Check that your AI assistant received all file contents

### Output format doesn't match expected
- Review the `expected/*.md` files for required structure
- Add explicit formatting instructions to your prompt
- Consider including the expected outline in your prompt

### Automation not running
- Verify cron syntax: `crontab -l` to list current jobs
- Check script permissions: `chmod +x your-script.sh`
- Test scripts manually before scheduling
- Review system logs: `grep CRON /var/log/syslog`

### Missing email context
- Ensure `.eml` files are complete exports (not truncated)
- Include email headers for proper threading
- Export entire conversations, not just individual messages

## Best Practices

1. **Archive processed inputs** - Move files from `incoming/` to `processed/` after review
2. **Version control outputs** - Commit generated markdown to track changes over time
3. **Iterate on prompts** - Refine prompt templates based on output quality
4. **Schedule regular reviews** - Use cron for daily/weekly automation
5. **Maintain prompt library** - Create variations for different contexts (security review, client communications, etc.)

## Integration Tips

While this workshop focuses on local-only workflows, you can extend it:

- **CLI wrapper**: Create shell scripts that call AI APIs programmatically
- **Git hooks**: Auto-generate logs on commit or push
- **Obsidian integration**: Use outputs as input for knowledge base
- **Notification system**: Alert on high-priority triage items

## Workshop Flow

1. Open `webinar-runbook.html` for guided walkthrough
2. Complete exercises in order: 01 → 02 → 03
3. Review expected outputs in `expected/` folders
4. Experiment with prompt modifications
5. Set up automation for recurring workflows
