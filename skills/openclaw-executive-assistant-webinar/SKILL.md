---
name: openclaw-executive-assistant-webinar
description: Build local-first executive assistant workflows with OpenClaw using file-based data intake, operational memory, and email triage
triggers:
  - set up openclaw executive assistant
  - create an openclaw workflow for data intake
  - build operational memory with openclaw
  - triage emails with openclaw
  - implement local-first openclaw assistant
  - generate markdown reports with openclaw
  - set up openclaw daily logs
  - create openclaw weekly summaries
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

This project teaches local-first OpenClaw workflows for building an executive assistant that:

1. **Reviews incoming data** (files, documents) and generates intake reports
2. **Maintains operational memory** via daily logs and weekly summaries
3. **Triages communications** (exported emails) into action lists

All processing stays local with markdown outputs. No live integrations required.

## Project Structure

```
code-along/
├── INDEX.md                              # Main navigation
├── 01-data-intake-review/
│   ├── incoming/                         # Files to inspect
│   ├── prompts/intake-review.md          # Prompt template
│   ├── outputs/intake-review.md          # Generated report
│   └── expected/report-outline.md        # Target format
├── 02-operational-memory/
│   ├── inbox/                            # Work notes/residue
│   ├── prompts/daily-log.md              # Daily prompt
│   ├── prompts/weekly-hype.md            # Weekly prompt
│   ├── outputs/daily-log.md              # Generated daily
│   ├── outputs/weekly-hype.md            # Generated weekly
│   └── schedule/cron-examples.md         # Automation examples
├── 03-offline-communications-triage/
│   ├── eml/                              # Exported emails
│   ├── prompts/email-triage.md           # Triage prompt
│   ├── outputs/email-triage.md           # Generated triage
│   └── expected/report-outline.md        # Target format
└── mission-control/                      # Dashboard (optional)
```

## Installation

```bash
# Clone the workshop repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the main walkthrough
open webinar-runbook.html
```

## Workflow Patterns

### 1. Data Intake Review

Process unknown files into a structured intake report.

**Steps:**
1. Place files in `code-along/01-data-intake-review/incoming/`
2. Read the prompt at `prompts/intake-review.md`
3. Pass files + prompt to OpenClaw
4. Save output to `outputs/intake-review.md`

**Expected Output Structure:**
```markdown
# Data Intake Review
Date: YYYY-MM-DD

## Summary
[High-level overview]

## Files Reviewed
- file1.pdf - [description]
- file2.docx - [description]

## Key Findings
[Important points]

## Recommended Actions
- [ ] Action item 1
- [ ] Action item 2

## Questions/Concerns
[Items needing clarification]
```

### 2. Operational Memory

Transform daily work residue into momentum documentation.

**Daily Log Pattern:**
```bash
# Review inbox notes
ls code-along/02-operational-memory/inbox/

# Apply daily log prompt
# Input: inbox/*.md
# Prompt: prompts/daily-log.md
# Output: outputs/daily-log.md
```

**Daily Log Output:**
```markdown
# Daily Log - YYYY-MM-DD

## Completed Today
- Task 1
- Task 2

## In Progress
- Ongoing item 1

## Blockers
- Issue needing resolution

## Tomorrow's Focus
- Priority 1
- Priority 2
```

**Weekly Summary Pattern:**
```bash
# Aggregate daily logs
# Input: outputs/daily-log-*.md (week's worth)
# Prompt: prompts/weekly-hype.md
# Output: outputs/weekly-hype.md
```

**Weekly Hype Output:**
```markdown
# Weekly Summary - Week of YYYY-MM-DD

## Wins This Week
[Major accomplishments]

## Momentum Items
[Work progressing well]

## Next Week Priorities
1. Priority one
2. Priority two

## Team Shoutouts
[Recognition]
```

### 3. Offline Communications Triage

Convert exported emails into actionable lists.

**Steps:**
1. Export emails as `.eml` files to `code-along/03-offline-communications-triage/eml/`
2. Use prompt from `prompts/email-triage.md`
3. Generate `outputs/email-triage.md`

**Triage Output:**
```markdown
# Email Triage - YYYY-MM-DD

## Urgent (Respond Today)
- **From:** sender@example.com
  **Subject:** Critical issue
  **Action:** [Required response]

## Important (This Week)
- **From:** colleague@example.com
  **Subject:** Project update
  **Action:** [Needed action]

## FYI (Read/Archive)
- **From:** newsletter@example.com
  **Subject:** Industry news

## Delegate
- **From:** support@example.com
  **Subject:** Customer request
  **Delegate to:** [Team member]

## Archive/Defer
[Low priority items]
```

## Automation Examples

### Cron Schedule for Daily Logs

```bash
# Run daily log generation at 6 PM weekdays
0 18 * * 1-5 cd /path/to/code-along/02-operational-memory && \
  openclaw process --prompt prompts/daily-log.md \
  --input inbox/ --output outputs/daily-log-$(date +\%Y-\%m-\%d).md
```

### Weekly Summary Cron

```bash
# Generate weekly summary Friday at 5 PM
0 17 * * 5 cd /path/to/code-along/02-operational-memory && \
  openclaw process --prompt prompts/weekly-hype.md \
  --input outputs/daily-log-*.md --output outputs/weekly-hype-$(date +\%Y-\%W).md
```

## Prompt Engineering Tips

### Effective Intake Review Prompts

```markdown
Analyze all files in this folder and create a structured intake report.

For each file:
1. Identify file type and source
2. Extract key information
3. Flag urgent items
4. Suggest categorization

Output format:
- Executive summary
- File-by-file breakdown
- Action items with priority
- Questions requiring follow-up
```

### Effective Triage Prompts

```markdown
Review these emails and categorize by urgency and action required.

Categories:
- URGENT: Needs response today
- IMPORTANT: Needs response this week
- FYI: Read and archive
- DELEGATE: Route to appropriate person
- DEFER: Low priority

For each email include:
- Sender
- Subject
- Recommended action
- Deadline (if applicable)
```

## Best Practices

1. **Consistent File Naming:**
   ```
   daily-log-2026-05-11.md
   weekly-hype-2026-W19.md
   intake-review-2026-05-11.md
   ```

2. **Prompt Versioning:**
   Keep prompt templates in version control and date significant changes.

3. **Output Review:**
   Always review generated artifacts before acting on them.

4. **Incremental Adoption:**
   Start with one workflow, refine, then add others.

5. **Local-First Security:**
   Keep sensitive data local; use `.gitignore` for private files.

## Troubleshooting

**No output generated:**
- Verify input files exist in specified folders
- Check prompt template syntax
- Ensure output directory has write permissions

**Poor quality summaries:**
- Add more specific instructions to prompts
- Provide example outputs in prompt
- Break complex tasks into smaller steps

**Automation not running:**
- Check cron syntax with `crontab -l`
- Verify file paths are absolute
- Check execution permissions on scripts

**Large email exports:**
- Process in batches (e.g., by date range)
- Filter by sender/subject before export
- Archive processed emails separately

## Environment Setup

Store configuration in environment variables:

```bash
# .env file
OPENCLAW_MODEL=claude-3-5-sonnet
OPENCLAW_OUTPUT_DIR=/path/to/code-along/outputs
OPENCLAW_PROMPT_DIR=/path/to/code-along/prompts
```

## Extension Ideas

1. **Dashboard Generation:** Create HTML index of all outputs
2. **Search Integration:** Build searchable archive of reports
3. **Template Library:** Maintain prompt templates for different scenarios
4. **Metrics Tracking:** Count action items, completion rates over time

## Resources

- Workshop runbook: `webinar-runbook.html`
- Exercise index: `code-along/INDEX.md`
- DataCamp webinar: https://www.datacamp.com/webinars/build-your-own-executive-assistant-with-openclaw
