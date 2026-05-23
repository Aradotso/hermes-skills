---
name: openclaw-executive-assistant-webinar
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - set up openclaw executive assistant workflow
  - create local ai assistant with openclaw
  - build data intake review system
  - generate operational memory reports
  - triage emails with openclaw
  - set up openclaw daily logs
  - create weekly summary with openclaw
  - implement openclaw communications workflow
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## What This Project Does

This is a workshop/tutorial project that teaches how to build a **local-first AI executive assistant** using OpenClaw. It demonstrates three core workflows:

1. **Data Intake Review** - Turn unknown files into trustworthy intake reports
2. **Operational Memory** - Turn work residue into daily and weekly momentum docs
3. **Offline Communications Triage** - Turn exported emails into action lists

All workflows are **local-only**, use **markdown artifacts**, and rely on **copy/paste prompts** with no live integrations.

## Project Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/
│   │   └── intake-review.md
│   ├── outputs/
│   │   └── intake-review.md
│   └── expected/
│       └── report-outline.md
├── 02-operational-memory/
│   ├── inbox/             # Work residue and notes
│   ├── prompts/
│   │   ├── daily-log.md
│   │   └── weekly-hype.md
│   ├── outputs/
│   │   ├── daily-log.md
│   │   └── weekly-hype.md
│   └── schedule/
│       ├── cron-examples.md
│       └── heartbeat-note.md
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/
│   │   └── email-triage.md
│   ├── outputs/
│   │   └── email-triage.md
│   └── expected/
│       └── report-outline.md
└── mission-control/       # Optional dashboard
```

## Installation

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the walkthrough
open webinar-runbook.html
```

## Exercise 1: Data Intake Review

**Goal:** Process unknown files in `incoming/` and generate a structured intake report.

### Setup

```bash
cd code-along/01-data-intake-review
ls incoming/  # View files to process
```

### Workflow

1. **Read the prompt:**
   ```bash
   cat prompts/intake-review.md
   ```

2. **Run OpenClaw with the prompt against `incoming/` files**

3. **Expected output:** `outputs/intake-review.md` with:
   - File inventory
   - Content summaries
   - Risk assessment
   - Recommended actions

### Example Prompt Pattern

```markdown
# Data Intake Review Prompt

Review all files in the incoming/ directory and generate a structured report.

## Output Format

### File Inventory
- List each file with type, size, date

### Content Summary
- Brief description of each file's contents
- Key information extracted

### Risk Assessment
- Security concerns
- Data quality issues

### Recommended Actions
- What to do with each file
- Priority level
```

### Expected Output Structure

```markdown
# Intake Review Report
Date: YYYY-MM-DD

## File Inventory
- `document.pdf` - PDF, 245KB, 2026-04-20
- `data.csv` - CSV, 12KB, 2026-04-22

## Content Summary
**document.pdf**: Contract proposal for Q2...
**data.csv**: Customer contact list with 47 entries...

## Risk Assessment
- ✅ No sensitive credentials detected
- ⚠️  `data.csv` contains PII - needs secure storage

## Recommended Actions
1. HIGH: Review contract terms in `document.pdf`
2. MEDIUM: Import `data.csv` to CRM
```

## Exercise 2: Operational Memory

**Goal:** Transform work notes and residue into daily logs and weekly summaries.

### Setup

```bash
cd code-along/02-operational-memory
ls inbox/  # View work notes
```

### Daily Log Workflow

1. **Read the prompt:**
   ```bash
   cat prompts/daily-log.md
   ```

2. **Process inbox files to generate:** `outputs/daily-log.md`

### Example Daily Log Prompt

```markdown
# Daily Log Generator

Process all notes in inbox/ and create a daily momentum log.

## Output Format

### Today's Focus
- Main objectives accomplished

### Completed Items
- Bulleted list of completed tasks

### In Progress
- Current work streams

### Blockers & Questions
- Issues needing attention

### Tomorrow's Priorities
- Top 3 items for next day
```

### Weekly Summary Workflow

1. **Read the prompt:**
   ```bash
   cat prompts/weekly-hype.md
   ```

2. **Aggregate daily logs to generate:** `outputs/weekly-hype.md`

### Example Weekly Summary Output

```markdown
# Weekly Summary - Week of May 5, 2026

## Highlights
- ✅ Shipped v2.3 with new dashboard
- ✅ Closed 3 customer escalations
- 🎯 Hit 95% uptime target

## Key Metrics
- Deploys: 8
- Incidents: 1 (resolved)
- Customer NPS: +42

## Next Week's Focus
1. Launch marketing campaign
2. Complete security audit
3. Team offsite planning

## Shoutouts
- Alice: Crushed the performance optimization
- Bob: Excellent customer communication
```

### Automation with Cron

```bash
# Example cron setup for daily logs
# Run at 6 PM every weekday
0 18 * * 1-5 cd /path/to/code-along/02-operational-memory && openclaw-process daily

# Run weekly summary Friday at 5 PM
0 17 * * 5 cd /path/to/code-along/02-operational-memory && openclaw-process weekly
```

See `schedule/cron-examples.md` for detailed cron patterns.

## Exercise 3: Offline Communications Triage

**Goal:** Process exported email files (`.eml`) into actionable triage reports.

### Setup

```bash
cd code-along/03-offline-communications-triage
ls eml/  # View exported emails
```

### Workflow

1. **Read the prompt:**
   ```bash
   cat prompts/email-triage.md
   ```

2. **Process `.eml` files to generate:** `outputs/email-triage.md`

### Example Email Triage Prompt

```markdown
# Email Triage Processor

Process all .eml files and create an action-oriented triage report.

## Output Format

### Urgent Action Required
- Emails needing response within 24h

### Follow-up This Week
- Items for this week's todo list

### FYI / Read Later
- Informational emails

### Can Archive
- No action needed

## For Each Email Include
- From, Subject, Date
- 1-2 sentence summary
- Recommended action
```

### Example Triage Output

```markdown
# Email Triage Report
Generated: 2026-05-11 09:00

## Urgent Action Required (2)

### 1. Contract Review - Legal Team
**From:** legal@company.com  
**Date:** 2026-05-10  
**Summary:** Contract needs signature by EOD Monday for vendor onboarding.  
**Action:** Review section 4.2, sign and return

### 2. Production Alert
**From:** monitoring@ops.com  
**Date:** 2026-05-11 08:45  
**Summary:** API latency spike detected in EU region.  
**Action:** Check dashboards, coordinate with on-call

## Follow-up This Week (5)

### 1. Q2 Planning Meeting Request
**From:** manager@company.com  
**Date:** 2026-05-09  
**Summary:** Schedule 1:1 to discuss Q2 OKRs.  
**Action:** Reply with 3 available times

## FYI / Read Later (3)

### 1. Industry Newsletter
**From:** newsletter@techdigest.com  
**Date:** 2026-05-10  
**Summary:** Weekly roundup of AI/ML developments.  
**Action:** Archive or save to read list

## Can Archive (7)

- Automated build notifications (all passed)
- Marketing team updates (informational)
```

## Common Patterns

### Pattern 1: Batch File Processing

```bash
# Process all files in a directory
cd code-along/01-data-intake-review
for file in incoming/*; do
  echo "Processing: $file"
  # Run OpenClaw with prompt
done
```

### Pattern 2: Scheduled Report Generation

```bash
#!/bin/bash
# generate-daily-log.sh

WORKSPACE="/path/to/code-along/02-operational-memory"
cd "$WORKSPACE"

# Process inbox
# Generate daily log
# Move processed files to archive

echo "Daily log generated at $(date)"
```

### Pattern 3: Email Export Workflow

1. **Export emails from client** (Outlook, Gmail, Apple Mail) as `.eml` files
2. **Place in `eml/` directory**
3. **Run triage prompt**
4. **Review generated action list**
5. **Archive processed emails**

## Configuration

### Environment Variables

```bash
# Set workspace root
export OPENCLAW_WORKSPACE="/path/to/code-along"

# Set output format preference
export OPENCLAW_OUTPUT_FORMAT="markdown"

# Set default timezone for timestamps
export TZ="America/New_York"
```

### Custom Prompts

All prompts are stored as markdown files in `prompts/` directories. Customize by editing:

```bash
# Edit intake review prompt
vim code-along/01-data-intake-review/prompts/intake-review.md

# Edit daily log prompt
vim code-along/02-operational-memory/prompts/daily-log.md
```

## Troubleshooting

### No Output Generated

**Issue:** Running prompt produces no `outputs/*.md` file

**Solution:**
- Verify input files exist in source directory
- Check prompt file formatting
- Ensure output directory has write permissions

```bash
# Check permissions
ls -la code-along/01-data-intake-review/outputs/
chmod 755 code-along/01-data-intake-review/outputs/
```

### Incomplete Reports

**Issue:** Generated reports missing sections

**Solution:**
- Review prompt structure - ensure all sections are clearly defined
- Check input files are readable
- Verify prompt includes explicit output format instructions

### Email Files Not Processing

**Issue:** `.eml` files not being parsed

**Solution:**
- Verify files are valid RFC822 format
- Check file extensions are `.eml`
- Try opening in email client to validate format

```bash
# Validate .eml files
file eml/*.eml
# Should show: "RFC 822 mail text"
```

## Best Practices

1. **Keep prompts version controlled** - Track prompt evolution
2. **Review outputs before acting** - Always validate AI-generated reports
3. **Archive processed inputs** - Move processed files to `archive/` subdirectories
4. **Use consistent naming** - Date-stamp outputs: `YYYY-MM-DD-report-name.md`
5. **Schedule regular runs** - Use cron for daily/weekly automation
6. **Maintain expected outputs** - Keep `expected/` examples updated as templates

## Mission Control (Optional)

Create a simple HTML dashboard linking all generated artifacts:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Executive Assistant Dashboard</title>
</head>
<body>
  <h1>Mission Control</h1>
  
  <section>
    <h2>Latest Reports</h2>
    <ul>
      <li><a href="../01-data-intake-review/outputs/intake-review.md">Intake Review</a></li>
      <li><a href="../02-operational-memory/outputs/daily-log.md">Daily Log</a></li>
      <li><a href="../02-operational-memory/outputs/weekly-hype.md">Weekly Summary</a></li>
      <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
    </ul>
  </section>
</body>
</html>
```

Save to `code-along/mission-control/dashboard.html` and open in browser.
