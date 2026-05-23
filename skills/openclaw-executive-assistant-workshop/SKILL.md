---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows using OpenClaw for data intake, operational memory, and communications triage
triggers:
  - create an executive assistant workflow with OpenClaw
  - set up local-first AI assistant for email triage
  - build data intake review system
  - generate daily and weekly work summaries
  - implement offline communications triage
  - create operational memory system with AI
  - set up OpenClaw workshop exercises
  - build markdown-based productivity workflows
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill teaches you how to build local-first AI executive assistant workflows using OpenClaw. The workshop focuses on three core capabilities: data intake review, operational memory (daily/weekly summaries), and offline communications triage. All workflows use local files, markdown outputs, and copy/paste prompts with no live integrations required.

## What This Project Does

The OpenClaw Executive Assistant Workshop provides a hands-on framework for creating AI-powered productivity workflows that:

- Transform unknown files into structured intake reports
- Convert work residue into daily logs and weekly summaries
- Triage exported emails into actionable task lists
- Keep everything local with reviewable markdown artifacts
- Use simple copy/paste prompts instead of complex integrations

## Repository Structure

```
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
│   ├── outputs/           # Generated logs
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/email-triage.md
│   ├── outputs/           # Triage reports
│   └── expected/report-outline.md
└── mission-control/       # Optional dashboard
```

## Installation and Setup

1. **Clone the repository:**
```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

2. **Open the walkthrough:**
```bash
# Open in your browser
open webinar-runbook.html
```

3. **Set up your workspace:**
```bash
# Navigate to the code-along directory
cd code-along

# Verify structure
ls -la
```

No additional dependencies or installations required — this workshop uses local files and manual prompt execution.

## Exercise 1: Data Intake Review

Transform unknown files into trustworthy intake reports.

### File Structure
```
01-data-intake-review/
├── incoming/              # Place unknown files here
├── prompts/intake-review.md
├── outputs/
└── expected/report-outline.md
```

### Workflow

1. **Place files to review in `incoming/`:**
```bash
cd code-along/01-data-intake-review
# Add files to incoming/ folder
```

2. **Use the intake review prompt:**
```bash
cat prompts/intake-review.md
```

3. **Copy the prompt template and provide context:**
   - List all files in `incoming/`
   - Include file types, sizes, and basic metadata
   - Ask your AI assistant to analyze and categorize

4. **Expected output structure:**
```markdown
# Data Intake Review Report

## Summary
- Total files reviewed: X
- Date: YYYY-MM-DD

## File Inventory
1. **filename.ext**
   - Type: [document/spreadsheet/image/etc]
   - Size: XXX KB
   - Initial assessment: [brief description]

## Categorization
### Actionable Items
- Files requiring immediate action

### Reference Materials
- Files for archival/reference

### Follow-up Required
- Files needing more information

## Recommendations
[Next steps for each category]
```

5. **Save to outputs:**
```bash
# Save AI response to
outputs/intake-review.md
```

## Exercise 2: Operational Memory

Convert work residue into daily logs and weekly summaries.

### File Structure
```
02-operational-memory/
├── inbox/                 # Work notes, residue
├── prompts/
│   ├── daily-log.md
│   └── weekly-hype.md
├── outputs/
└── schedule/
    ├── cron-examples.md
    └── heartbeat-note.md
```

### Daily Log Workflow

1. **Collect work residue in `inbox/`:**
```bash
cd code-along/02-operational-memory
# Add: meeting notes, task updates, quick captures
```

2. **Use the daily log prompt:**
```bash
cat prompts/daily-log.md
```

3. **Expected daily log format:**
```markdown
# Daily Log — YYYY-MM-DD

## Completed Today
- [Task/achievement 1]
- [Task/achievement 2]

## In Progress
- [Active work item 1]
- [Active work item 2]

## Blocked/Waiting
- [Item awaiting response]

## Notes & Observations
[Key insights, decisions, or context]

## Tomorrow's Focus
1. [Priority 1]
2. [Priority 2]
3. [Priority 3]
```

4. **Save output:**
```bash
# Save to
outputs/daily-log.md
# Or use date-based naming
outputs/daily-log-2026-05-15.md
```

### Weekly Summary Workflow

1. **Collect week's daily logs:**
```bash
# Gather daily logs from the past week
ls outputs/daily-log-*.md
```

2. **Use the weekly hype prompt:**
```bash
cat prompts/weekly-hype.md
```

3. **Expected weekly summary format:**
```markdown
# Weekly Summary — Week of YYYY-MM-DD

## Highlights & Wins
[Major accomplishments and positive momentum]

## Key Metrics
- Tasks completed: X
- Projects advanced: Y
- Blockers resolved: Z

## Insights & Patterns
[Observations about the week's work]

## Next Week's Priorities
1. [Priority 1]
2. [Priority 2]
3. [Priority 3]

## Blockers to Address
[Items requiring attention]
```

4. **Save output:**
```bash
outputs/weekly-hype.md
# Or use date-based naming
outputs/weekly-hype-2026-W20.md
```

### Automation with Cron

The `schedule/` folder contains examples for automated scheduling:

```bash
# View cron examples
cat schedule/cron-examples.md

# Example cron job for daily log reminder (9 PM daily)
0 21 * * * /path/to/reminder-script.sh

# Example for weekly summary (Friday 5 PM)
0 17 * * 5 /path/to/weekly-script.sh
```

## Exercise 3: Offline Communications Triage

Turn exported emails into action lists.

### File Structure
```
03-offline-communications-triage/
├── eml/                   # Exported .eml files
├── prompts/email-triage.md
├── outputs/
└── expected/report-outline.md
```

### Workflow

1. **Export emails to `eml/` folder:**
```bash
cd code-along/03-offline-communications-triage
# Add exported .eml files from your email client
```

2. **Use the triage prompt:**
```bash
cat prompts/email-triage.md
```

3. **Expected triage report format:**
```markdown
# Email Triage Report — YYYY-MM-DD

## Summary
- Total emails reviewed: X
- Action required: Y
- FYI/Reference: Z

## Urgent & Important
### Email Subject 1
- **From:** sender@example.com
- **Date:** YYYY-MM-DD
- **Action needed:** [specific action]
- **Deadline:** [if applicable]
- **Priority:** HIGH

## Action Required (Non-Urgent)
### Email Subject 2
- **From:** sender@example.com
- **Action needed:** [specific action]
- **Priority:** MEDIUM

## FYI / No Action
- Email Subject 3 (reference material)
- Email Subject 4 (informational)

## Follow-up Tracking
| Subject | Sender | Action | Due Date | Status |
|---------|--------|--------|----------|--------|
| ...     | ...    | ...    | ...      | ...    |

## Recommendations
[Suggested next steps or patterns noticed]
```

4. **Save output:**
```bash
outputs/email-triage.md
# Or use date-based naming
outputs/email-triage-2026-05-15.md
```

## Mission Control (Optional)

The `mission-control/` folder is reserved for creating a simple dashboard that links to all generated artifacts.

### Example Dashboard Structure

```markdown
# Executive Assistant Mission Control

Last updated: YYYY-MM-DD HH:MM

## Recent Reports

### Data Intake
- [Latest Intake Review](../01-data-intake-review/outputs/intake-review.md)

### Operational Memory
- [Today's Log](../02-operational-memory/outputs/daily-log.md)
- [This Week's Summary](../02-operational-memory/outputs/weekly-hype.md)

### Communications
- [Latest Email Triage](../03-offline-communications-triage/outputs/email-triage.md)

## Quick Actions
- [ ] Review pending intake items
- [ ] Update daily log
- [ ] Process new emails
```

## Common Patterns

### Pattern 1: Daily Review Routine

```bash
# Morning: Review yesterday's work
cd code-along/02-operational-memory
cat outputs/daily-log-$(date -v-1d +%Y-%m-%d).md

# Add today's notes to inbox/
echo "## Meeting notes..." >> inbox/$(date +%Y-%m-%d)-notes.md

# Evening: Generate today's log
cat prompts/daily-log.md
# [Copy prompt, feed to AI with inbox contents]
# Save output to outputs/daily-log-$(date +%Y-%m-%d).md
```

### Pattern 2: Weekly Wrap-Up

```bash
# Friday afternoon: Generate weekly summary
cd code-along/02-operational-memory

# Collect week's logs
cat outputs/daily-log-*.md

# Run weekly summary prompt
cat prompts/weekly-hype.md
# [Copy prompt, feed to AI with week's logs]
# Save to outputs/weekly-hype-$(date +%Y-W%V).md
```

### Pattern 3: Email Batch Processing

```bash
# Export emails from your client to .eml format
# Place in 03-offline-communications-triage/eml/

cd code-along/03-offline-communications-triage

# Process batch
cat prompts/email-triage.md
# [Provide .eml file contents to AI]
# Save triage report to outputs/

# Archive processed emails
mkdir -p eml/processed
mv eml/*.eml eml/processed/
```

## Configuration Tips

### Customizing Prompts

All prompts are markdown files that can be edited:

```bash
# Edit intake review prompt
vim code-along/01-data-intake-review/prompts/intake-review.md

# Add project-specific categories or priorities
# Save changes and use updated prompt
```

### Output Organization

Use date-based naming for better tracking:

```bash
# Naming convention examples
daily-log-2026-05-15.md
weekly-hype-2026-W20.md
email-triage-2026-05-15-batch-01.md
intake-review-2026-05-15-client-docs.md
```

### Environment Setup

For automated workflows, set environment variables:

```bash
# .env file or shell profile
export OPENCLAW_WORKSPACE="${HOME}/openclaw-workspace"
export OPENCLAW_ARCHIVE="${OPENCLAW_WORKSPACE}/archive"
export OPENCLAW_OUTPUTS="${OPENCLAW_WORKSPACE}/outputs"
```

## Troubleshooting

### Files Not Processing Correctly

**Issue:** AI doesn't understand file contents

**Solution:** Ensure files are text-readable. For binary files, provide metadata:
```markdown
File: document.pdf
Type: PDF
Pages: 15
Summary: [Manual description]
```

### Prompts Not Generating Expected Output

**Issue:** Output doesn't match expected format

**Solution:** Be more explicit in your prompt:
```markdown
Generate output in exactly this format:

# Daily Log — [DATE]

## Section 1
[content]

## Section 2
[content]
```

### Daily Logs Becoming Repetitive

**Issue:** Logs lack useful detail

**Solution:** Enhance inbox notes:
```markdown
# Instead of:
- Met with team

# Write:
- Met with team about Q3 roadmap
  - Decision: Prioritize feature X
  - Action: Draft spec by Friday
  - Blocker: Waiting on design mockups
```

### Email Triage Overwhelming

**Issue:** Too many emails to process

**Solution:** Batch by category:
```bash
# Process in groups
eml/urgent/       # Process first
eml/team/         # Process second
eml/newsletters/  # Process last or skip
```

## Best Practices

1. **Consistent naming:** Use ISO date format (YYYY-MM-DD) for all outputs
2. **Regular cadence:** Set specific times for daily/weekly reviews
3. **Archive processed items:** Move files after processing to keep workspaces clean
4. **Version prompts:** Track changes to prompts as you refine them
5. **Review outputs:** Always read and refine AI-generated reports before filing
6. **Maintain inbox hygiene:** Clear processed items from inbox folders regularly

## Integration with Other Tools

While this workshop is local-first, outputs can feed into:

```bash
# Sync to knowledge base
cp outputs/*.md ~/Obsidian/ExecutiveAssistant/

# Export to task manager
grep -r "Action:" outputs/ > tasks.txt

# Archive to git
git add outputs/
git commit -m "Weekly summary $(date +%Y-W%V)"
```

---

**Workshop Flow:** Follow `webinar-runbook.html` for the complete guided experience. Each exercise builds on the previous one, creating a full local-first executive assistant workflow.
