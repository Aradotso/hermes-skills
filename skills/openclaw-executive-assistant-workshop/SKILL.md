---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "help me build an executive assistant with OpenClaw"
  - "set up OpenClaw data intake workflow"
  - "create operational memory system with OpenClaw"
  - "triage emails using OpenClaw"
  - "build local-first AI assistant workflows"
  - "set up OpenClaw daily log automation"
  - "create markdown-based executive assistant"
  - "implement OpenClaw communications workflow"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

This project provides a local-first workshop for building executive assistant workflows using OpenClaw. It teaches three core patterns:

1. **Data intake review** — Turn unknown files into trustworthy intake reports
2. **Operational memory** — Convert work residue into daily/weekly momentum docs
3. **Offline communications triage** — Transform exported emails into action lists

All workflows:
- Stay local (no live integrations)
- Produce reviewable markdown artifacts
- Use copy/paste prompts
- Keep data in local folders

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
│   ├── inbox/             # Work notes/residue
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

## Installation

Clone the repository:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

Open the walkthrough:

```bash
# Open in browser
open webinar-runbook.html
```

## Workflow Patterns

### 1. Data Intake Review

**Purpose:** Convert unknown incoming files into structured intake reports.

**Steps:**

1. Place files to review in `code-along/01-data-intake-review/incoming/`
2. Read the prompt from `prompts/intake-review.md`
3. Feed prompt + files to your AI assistant (Claude, ChatGPT, etc.)
4. Save output to `outputs/intake-review.md`

**Expected Output Structure:**

```markdown
# Data Intake Review

## Summary
[High-level overview of incoming files]

## Files Analyzed
- `filename.ext` - [description]
- `another.txt` - [description]

## Recommendations
1. [Action item]
2. [Action item]

## Risk Assessment
[Security/quality concerns]
```

**Example Usage:**

```bash
# Navigate to exercise
cd code-along/01-data-intake-review

# List incoming files
ls incoming/

# Read prompt
cat prompts/intake-review.md

# Copy prompt content + file paths to AI assistant
# Generate report and save to outputs/intake-review.md
```

### 2. Operational Memory

**Purpose:** Transform daily work notes into momentum documents.

**Daily Log Workflow:**

1. Collect work notes/snippets in `inbox/`
2. Use `prompts/daily-log.md` to generate daily summary
3. Output to `outputs/daily-log.md`

**Weekly Hype Workflow:**

1. Gather week's daily logs
2. Use `prompts/weekly-hype.md` to generate weekly summary
3. Output to `outputs/weekly-hype.md`

**Example Daily Log Generation:**

```bash
cd code-along/02-operational-memory

# Add work notes to inbox/
echo "Finished API integration\nReviewed PR #234\nPlanned Q2 roadmap" > inbox/today.txt

# Read daily prompt
cat prompts/daily-log.md

# Generate daily log with AI assistant
# Save to outputs/daily-log.md
```

**Automation with Cron:**

```bash
# Example cron entry for daily log generation
# Run at 6 PM daily
0 18 * * * cd /path/to/code-along/02-operational-memory && /path/to/generate-daily-log.sh

# Weekly summary on Friday at 5 PM
0 17 * * 5 cd /path/to/code-along/02-operational-memory && /path/to/generate-weekly-hype.sh
```

**Daily Log Output Format:**

```markdown
# Daily Log — [Date]

## Completed
- [Task/achievement]
- [Task/achievement]

## In Progress
- [Ongoing work]

## Blocked
- [Blockers]

## Notes
[Context, decisions, learnings]
```

### 3. Offline Communications Triage

**Purpose:** Convert exported emails into actionable triage reports.

**Steps:**

1. Export emails as `.eml` files to `eml/` folder
2. Read `prompts/email-triage.md`
3. Process emails with AI assistant
4. Save triage report to `outputs/email-triage.md`

**Example:**

```bash
cd code-along/03-offline-communications-triage

# List emails
ls eml/

# Read triage prompt
cat prompts/email-triage.md

# Generate triage report with AI
# Save to outputs/email-triage.md
```

**Triage Output Format:**

```markdown
# Email Triage Report

## Urgent Actions
- **From:** sender@example.com
  **Subject:** [subject]
  **Action:** [required response/task]
  **Deadline:** [if any]

## Follow-ups
- [Items requiring response]

## FYI / Archive
- [Informational only]

## Delegated
- [Items to hand off]
```

## Prompt Engineering Tips

### Effective Prompt Structure

1. **Context:** Define the role and task
2. **Input:** Specify what files/data to analyze
3. **Output:** Describe desired format
4. **Constraints:** Local-only, markdown output

### Example Intake Review Prompt Pattern

```markdown
You are an executive assistant reviewing incoming files.

ANALYZE these files in the incoming/ directory:
[list files]

PRODUCE a markdown report with:
- Summary
- File-by-file breakdown
- Priority recommendations
- Risk assessment

FORMAT: Use headings, bullets, and clear sections.
```

## Common Patterns

### File Organization

Keep clear input/output separation:

```
exercise/
├── incoming/     # Raw inputs
├── prompts/      # Reusable instructions
├── outputs/      # Generated artifacts
└── expected/     # Reference examples
```

### Prompt Reusability

Store prompts as markdown files for:
- Version control
- Iteration
- Sharing across team
- Automation

### Artifact Versioning

```bash
# Timestamp outputs for history
outputs/daily-log-2026-05-11.md
outputs/weekly-hype-week-19.md
outputs/intake-review-2026-05-11-batch-1.md
```

## Troubleshooting

### AI Assistant Not Understanding Context

**Problem:** Generic or off-target outputs

**Solution:**
- Include more specific file details in prompt
- Reference expected output format from `expected/` folder
- Break complex requests into smaller steps

### Missing Files in Analysis

**Problem:** AI doesn't see all files

**Solution:**
- Explicitly list files in prompt
- Paste file contents directly if small
- Use directory listings: `ls -la incoming/`

### Output Format Inconsistent

**Problem:** Reports vary in structure

**Solution:**
- Reference `expected/` examples in prompt
- Use explicit format templates
- Add example output in prompt

### Automation Not Running

**Problem:** Cron jobs fail silently

**Solution:**
- Check cron logs: `grep CRON /var/log/syslog`
- Use absolute paths in cron scripts
- Redirect output: `>> /path/to/log.txt 2>&1`
- Test script manually first

## Integration Ideas

### With Git

```bash
# Track generated reports
git add outputs/*.md
git commit -m "Daily log: 2026-05-11"
```

### With Obsidian/Logseq

```bash
# Link outputs to knowledge base
ln -s $(pwd)/outputs/ ~/ObsidianVault/executive-assistant/
```

### With Notification Systems

```bash
# Send daily summary via notification
cat outputs/daily-log.md | notify-send "Daily Log Ready"
```

## Best Practices

1. **Review outputs before acting** — AI summaries need human verification
2. **Iterate prompts** — Refine based on output quality
3. **Keep raw inputs** — Never delete original files
4. **Version control prompts** — Track what works
5. **Local-first** — No API keys, no cloud dependencies
6. **Markdown everywhere** — Universal, searchable, version-controllable

## Further Resources

- Workshop walkthrough: `webinar-runbook.html`
- DataCamp webinar: https://www.datacamp.com/webinars/build-your-own-executive-assistant-with-openclaw
- Exercise index: `code-along/INDEX.md`
