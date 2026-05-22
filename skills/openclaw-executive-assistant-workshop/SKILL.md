---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows using OpenClaw with file-based data intake, operational memory, and communications triage
triggers:
  - build an executive assistant with openclaw
  - set up openclaw workflow for data intake
  - create operational memory system with openclaw
  - triage emails using openclaw
  - implement local-first ai assistant
  - use openclaw for daily and weekly logs
  - build file-based ai workflow
  - create openclaw communications triage
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This project provides a practical workshop for building local-first AI executive assistant workflows using OpenClaw. It focuses on three core capabilities: data intake review, operational memory management, and offline communications triage. All operations use local files and produce reviewable markdown artifacts without requiring live integrations.

## What This Project Does

The workshop teaches you to build an AI executive assistant that:

1. **Reviews unknown files** and generates trustworthy intake reports
2. **Manages operational memory** by turning work residue into daily logs and weekly summaries
3. **Triages communications** by processing exported emails into actionable lists

All workflows are:
- **Local-only** — no cloud dependencies
- **File-based** — inputs and outputs are markdown/files
- **Reviewable** — human-readable artifacts at every stage
- **Copy-paste friendly** — structured prompts for any LLM interface

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/           # Prompt templates
│   ├── outputs/           # Generated reports
│   └── expected/          # Expected output format
├── 02-operational-memory/
│   ├── inbox/             # Work notes and residue
│   ├── prompts/           # Daily/weekly prompts
│   ├── outputs/           # Generated logs
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/           # Triage prompt
│   ├── outputs/           # Generated action lists
│   └── expected/          # Expected output format
└── mission-control/       # Optional dashboard
```

## Getting Started

### Installation

1. **Clone the repository:**
```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

2. **Open the walkthrough:**
```bash
open webinar-runbook.html  # macOS
# or
xdg-open webinar-runbook.html  # Linux
# or just open the file in your browser
```

3. **Navigate to the code-along folder:**
```bash
cd code-along
```

No package installation required — this is a methodology workshop that works with any LLM interface.

## Exercise 1: Data Intake Review

Transform unknown files into structured intake reports.

### Workflow

1. **Place files to review in:**
```
code-along/01-data-intake-review/incoming/
```

2. **Read the prompt template:**
```
code-along/01-data-intake-review/prompts/intake-review.md
```

3. **Copy the prompt and provide context:**
```markdown
I have files in my incoming/ folder that need review.

Please analyze these files and generate an intake report that includes:
- File inventory with types and sizes
- Content summaries
- Risk assessment
- Recommended actions
- Priority classification

Output the report to outputs/intake-review.md
```

4. **Expected output location:**
```
code-along/01-data-intake-review/outputs/intake-review.md
```

### Example Prompt Pattern

```markdown
# Data Intake Review

## Context
Files located in: ./incoming/

## Task
Generate a structured intake report covering:
1. **Inventory**: List all files with metadata
2. **Content Analysis**: Summarize what each file contains
3. **Risk Assessment**: Flag sensitive data, malware concerns
4. **Actions**: Recommend next steps for each file
5. **Priority**: Assign urgency levels

## Output Format
Markdown report saved to: ./outputs/intake-review.md

## Example Structure
```
# Intake Review Report
Generated: YYYY-MM-DD

## Summary
- Total files: X
- High priority: Y
- Requires action: Z

## Inventory
| File | Type | Size | Status |
|------|------|------|--------|
...

## Detailed Analysis
### [Filename]
- **Content**: ...
- **Risk**: ...
- **Action**: ...
```
```

## Exercise 2: Operational Memory

Turn daily work residue into momentum documents.

### Daily Log Workflow

1. **Place work notes in:**
```
code-along/02-operational-memory/inbox/
```

2. **Use the daily log prompt:**
```
code-along/02-operational-memory/prompts/daily-log.md
```

3. **Generate daily log:**
```markdown
Review the notes in ./inbox/ and create a daily log that captures:
- What got done today
- What's in progress
- What's blocked
- Tomorrow's focus

Save to: ./outputs/daily-log.md
```

### Weekly Summary Workflow

```markdown
Review the past week's daily logs and generate a weekly summary:
- Key accomplishments
- Momentum areas
- Blockers resolved
- Next week's priorities
- Energy assessment

Save to: ./outputs/weekly-hype.md
```

### Automation Example

Set up a cron job for daily processing:

```bash
# Edit crontab
crontab -e

# Add entry to run at 5 PM weekdays
0 17 * * 1-5 cd /path/to/code-along/02-operational-memory && your-llm-cli process daily-log.md
```

See `schedule/cron-examples.md` for more automation patterns.

### Example Daily Log Output

```markdown
# Daily Log — 2026-05-11

## ✅ Completed
- Reviewed 3 PRs for authentication module
- Shipped dashboard performance fix
- Documented API endpoint changes

## 🚧 In Progress
- Database migration planning (60% complete)
- User onboarding redesign mockups

## 🚫 Blocked
- Payment integration waiting on vendor API keys

## 🎯 Tomorrow
- Finalize migration runbook
- Review onboarding flow with design team
- Ship notification batching feature
```

## Exercise 3: Offline Communications Triage

Process exported emails into actionable lists.

### Workflow

1. **Export emails to `.eml` format** and place in:
```
code-along/03-offline-communications-triage/eml/
```

2. **Use the triage prompt:**
```
code-along/03-offline-communications-triage/prompts/email-triage.md
```

3. **Generate triage report:**
```markdown
Process all .eml files in ./eml/ and create an email triage report:

Categorize each email:
- **Action Required**: Needs response or task
- **FYI**: Informational only
- **Waiting**: Blocked on someone else
- **Archive**: No action needed

For action items, extract:
- From/Subject
- What's needed
- Deadline (if any)
- Priority level

Save to: ./outputs/email-triage.md
```

### Example Triage Output

```markdown
# Email Triage Report — 2026-05-11

## 🔴 Action Required (3)

### 1. Budget approval needed
- **From**: finance@company.com
- **Subject**: Q3 Budget Review
- **Action**: Review attached spreadsheet, approve by Friday
- **Deadline**: 2026-05-15
- **Priority**: High

### 2. Client meeting prep
- **From**: sarah@client.com
- **Subject**: Agenda for Thursday call
- **Action**: Prepare demo, review their questions
- **Deadline**: 2026-05-13
- **Priority**: High

## 📋 FYI (5)
- Team social event next month
- New parking policy
- HR benefits reminder
...

## ⏳ Waiting (2)
- Proposal feedback from legal (sent 2026-05-08)
- Design mockups from external agency

## 📥 Archive (12)
- Newsletter subscriptions
- Automated reports
- Resolved threads
```

## Common Patterns

### Pattern 1: Batch File Processing

```markdown
# Generic Batch Processing Prompt

Process all files in [FOLDER] and:
1. Analyze content
2. Extract key information
3. Generate summary report
4. Flag items needing attention

Output format: Markdown table + detailed notes
Save to: ./outputs/[REPORT-NAME].md
```

### Pattern 2: Incremental Updates

```markdown
# Incremental Daily Update

Previous log: ./outputs/daily-log-2026-05-10.md
New inputs: ./inbox/ (files added since yesterday)

Generate today's log building on yesterday's context:
- Reference ongoing items from previous log
- Add new developments
- Update status of tracked items
```

### Pattern 3: Multi-Stage Pipeline

```bash
# Stage 1: Intake
process-folder incoming/ -> outputs/raw-intake.md

# Stage 2: Categorization
refine outputs/raw-intake.md -> outputs/categorized.md

# Stage 3: Action Items
extract-actions outputs/categorized.md -> outputs/actions.md
```

## Configuration

### Customizing Prompts

All prompts are in `prompts/` folders. Customize them for your needs:

```markdown
# Your Custom Prompt Template

## Your Context
[Describe your specific workflow]

## Your Requirements
[List what you need extracted/generated]

## Your Output Format
[Specify structure, tone, level of detail]
```

### Environment Setup

For automated workflows, set up environment variables:

```bash
# .env file example (DO NOT COMMIT)
WORK_INBOX_PATH=/path/to/code-along/02-operational-memory/inbox
DAILY_LOG_OUTPUT=/path/to/code-along/02-operational-memory/outputs
EMAIL_EML_PATH=/path/to/code-along/03-offline-communications-triage/eml
```

Reference in automation scripts:

```bash
#!/bin/bash
source .env
cd "$WORK_INBOX_PATH" && process-daily-log
```

## Troubleshooting

### Issue: LLM doesn't access local files

**Solution**: Copy file contents into your prompt explicitly:

```markdown
Here is the content of the file to process:

```
[paste file contents]
```

Please analyze this content and generate...
```

### Issue: Output format inconsistent

**Solution**: Provide explicit format template in prompt:

```markdown
Use EXACTLY this format:

# Report Title
Generated: [DATE]

## Section 1
- Item format: `- **Label**: description`

## Section 2
| Column1 | Column2 |
|---------|---------|
| data    | data    |
```

### Issue: Missing context between runs

**Solution**: Include previous outputs in new prompts:

```markdown
Previous daily log:
[paste yesterday's log]

New inbox items:
[list new files]

Generate today's log maintaining continuity...
```

### Issue: Automation not running

**Check cron job syntax:**
```bash
# View current crontab
crontab -l

# Check system log
grep CRON /var/log/syslog  # Linux
log show --predicate 'process == "cron"' --last 1h  # macOS
```

## Integration with AI Coding Agents

### Using with Claude/Cursor/Copilot

1. **Open the code-along folder** in your editor
2. **Reference exercise prompts** in your AI chat
3. **Point to specific folders** for file operations

Example Claude conversation:

```
You: "Follow the prompt in code-along/01-data-intake-review/prompts/intake-review.md 
     and process the files in incoming/"

Claude: [Generates intake-review.md in outputs/]

You: "Now use that report to create action items in mission-control/"
```

### Extending the Workflow

Add custom exercises:

```
code-along/
└── 04-your-custom-workflow/
    ├── inputs/
    ├── prompts/
    │   └── your-prompt.md
    └── outputs/
```

## Best Practices

1. **Keep prompts version-controlled** — track what works
2. **Review all outputs** before taking action
3. **Iterate on prompt templates** based on results
4. **Use consistent folder structures** for reproducibility
5. **Archive processed files** to avoid re-processing
6. **Date-stamp all outputs** for audit trails
7. **Start manual, automate later** — understand the workflow first

## Resources

- **Main walkthrough**: `webinar-runbook.html`
- **Exercise index**: `code-along/INDEX.md`
- **DataCamp webinar**: https://www.datacamp.com/webinars/build-your-own-executive-assistant-with-openclaw
