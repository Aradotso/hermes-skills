---
name: openclaw-executive-assistant-webinar
description: Build local-first executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "build an executive assistant with OpenClaw"
  - "set up OpenClaw workflow automation"
  - "create local-first assistant workflows"
  - "automate data intake and triage with OpenClaw"
  - "build operational memory system"
  - "set up offline communications triage"
  - "create markdown-based workflow automation"
  - "implement local executive assistant patterns"
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill covers the OpenClaw workshop patterns for building local-first executive assistant workflows. The project demonstrates three core patterns: data intake review, operational memory management, and offline communications triage—all using local files and markdown outputs.

## What This Project Does

This is a workshop/starter repository that teaches local-only OpenClaw workflows for executive assistant automation:

1. **Data intake review** - Transform unknown files into trustworthy intake reports
2. **Operational memory** - Convert work residue into daily logs and weekly summaries
3. **Offline communications triage** - Process exported emails into action lists

All workflows are:
- Local files only (no live integrations)
- Markdown-based outputs
- Copy/paste prompt driven
- Reviewable artifacts

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/           # intake-review.md
│   ├── outputs/           # Generated reports
│   └── expected/          # Expected output shapes
├── 02-operational-memory/
│   ├── inbox/             # Work notes and residue
│   ├── prompts/           # daily-log.md, weekly-hype.md
│   ├── outputs/           # Generated logs
│   └── schedule/          # Cron examples, heartbeat guidance
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/           # email-triage.md
│   ├── outputs/           # Triage reports
│   └── expected/          # Expected output shapes
└── mission-control/       # Dashboard/extension linking
```

## Installation & Setup

1. **Clone the repository:**

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

2. **Open the walkthrough:**

```bash
# Open in browser
open webinar-runbook.html
```

3. **Keep the code-along folder visible** in your editor to work through exercises

## Workflow Patterns

### Pattern 1: Data Intake Review

Transform unknown files in a folder into a structured intake report.

**Directory:** `code-along/01-data-intake-review/`

**Workflow:**

1. Place files to review in `incoming/`
2. Use the prompt in `prompts/intake-review.md`
3. Generate report to `outputs/intake-review.md`

**Example Prompt Structure:**

```markdown
# Data Intake Review

Review all files in the incoming/ directory and produce a structured report.

## Report Format

- File inventory (name, type, size)
- Content summaries
- Potential actions required
- Risk assessment
- Recommended filing location

## Output

Generate: outputs/intake-review.md
```

**Expected Output:**

```markdown
# Intake Review Report
Date: 2026-05-15

## File Inventory
- document1.pdf (245KB) - Contract draft
- notes.txt (3KB) - Meeting notes
- spreadsheet.xlsx (128KB) - Q2 financials

## Actions Required
1. Review contract terms (document1.pdf)
2. Follow up on meeting action items (notes.txt)
3. Validate Q2 numbers (spreadsheet.xlsx)

## Risk Assessment
- Medium: Contract requires legal review
- Low: Meeting notes are informational only
```

### Pattern 2: Operational Memory

Convert daily work residue into momentum documents.

**Directory:** `code-along/02-operational-memory/`

**Workflow:**

1. Drop work notes, snippets, and residue into `inbox/`
2. Use `prompts/daily-log.md` for daily summaries
3. Use `prompts/weekly-hype.md` for weekly rollups
4. Generate to `outputs/daily-log.md` and `outputs/weekly-hype.md`

**Daily Log Example:**

```markdown
# Daily Log Prompt

Review all items in inbox/ and generate a daily momentum log.

## Structure
- What got done
- What's in progress
- What's blocked
- Tomorrow's focus

Output: outputs/daily-log.md
```

**Daily Log Output:**

```markdown
# Daily Log - 2026-05-15

## Completed
- Shipped API endpoint for user authentication
- Reviewed 12 pull requests
- Drafted Q2 goals document

## In Progress
- Database migration (80% complete)
- Design system documentation

## Blocked
- Waiting on legal review for terms update

## Tomorrow's Focus
- Complete database migration
- Ship design system v2.0
```

**Weekly Hype Example:**

```markdown
# Weekly Hype Prompt

Synthesize the week's daily logs into a weekly summary.

## Structure
- Key wins
- Momentum metrics
- Next week's priorities
- Energy check

Output: outputs/weekly-hype.md
```

**Automation with Cron:**

See `schedule/cron-examples.md` for scheduling patterns:

```bash
# Daily log generation at 5 PM
0 17 * * * cd ~/workspace/openclaw-assistant && ./generate-daily-log.sh

# Weekly summary on Friday at 4 PM
0 16 * * 5 cd ~/workspace/openclaw-assistant && ./generate-weekly-hype.sh
```

### Pattern 3: Offline Communications Triage

Process exported emails into actionable lists.

**Directory:** `code-along/03-offline-communications-triage/`

**Workflow:**

1. Export emails to `.eml` files in `eml/`
2. Use prompt in `prompts/email-triage.md`
3. Generate triage report to `outputs/email-triage.md`

**Triage Prompt Structure:**

```markdown
# Email Triage

Process all .eml files and categorize by action required.

## Categories
- URGENT: Requires immediate response
- ACTION: Needs response within 24-48h
- FYI: Informational only
- DEFER: Can wait until next week
- ARCHIVE: No action needed

## Output Format
- Category headers
- Email subject + sender
- Required action (if any)
- Suggested response time

Output: outputs/email-triage.md
```

**Example Output:**

```markdown
# Email Triage Report
Generated: 2026-05-15 14:30

## URGENT (2)
- **RE: Production outage** (ops@company.com)
  - Action: Respond with incident timeline
  - Time: Within 1 hour

- **Board meeting moved to tomorrow** (ceo@company.com)
  - Action: Confirm attendance and prep materials
  - Time: End of day

## ACTION (5)
- **Q2 Budget Review** (finance@company.com)
  - Action: Review spreadsheet and provide feedback
  - Time: 48 hours

## FYI (8)
- **Team lunch on Friday** (admin@company.com)
  - No action required
```

## Mission Control Dashboard

The `mission-control/` folder is reserved for a simple HTML dashboard that links to all generated artifacts:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Mission Control</title>
</head>
<body>
  <h1>Executive Assistant Dashboard</h1>
  
  <section>
    <h2>Today's Summary</h2>
    <a href="../02-operational-memory/outputs/daily-log.md">Daily Log</a>
  </section>
  
  <section>
    <h2>Recent Reports</h2>
    <ul>
      <li><a href="../01-data-intake-review/outputs/intake-review.md">Latest Intake Review</a></li>
      <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
    </ul>
  </section>
</body>
</html>
```

## Configuration

All configuration is embedded in the prompt files. Key customization points:

1. **Report formats** - Edit prompt files to change output structure
2. **Categories** - Modify triage categories in `prompts/email-triage.md`
3. **Scheduling** - Adjust cron timing in `schedule/cron-examples.md`
4. **Output locations** - Change paths in prompt instructions

## Best Practices

1. **Consistent folder structure** - Keep incoming/outputs separation clear
2. **Date your outputs** - Include timestamps in generated reports
3. **Review before acting** - All outputs are drafts for human review
4. **Version control** - Commit prompt changes when you customize workflows
5. **Archive processed items** - Move files from incoming/ to archive/ after review

## Troubleshooting

**Problem:** Outputs are too verbose/not structured

**Solution:** Refine the prompt to specify exact format, bullet counts, or section limits

**Problem:** Daily logs missing context

**Solution:** Add more detail to inbox/ notes; include project names and ticket numbers

**Problem:** Email triage misclassifies urgency

**Solution:** Update the triage prompt with specific keywords or sender patterns for your organization

**Problem:** Cron jobs not running

**Solution:** Check cron logs with `grep CRON /var/log/syslog` and verify script permissions with `chmod +x script.sh`

## Extending the Patterns

### Add New Workflows

Create a new folder following the pattern:

```
code-along/04-your-workflow/
├── input/
├── prompts/
├── outputs/
└── expected/
```

### Integrate with Other Tools

Export data to standard formats for downstream processing:

```bash
# Convert markdown to JSON for API consumption
pandoc outputs/daily-log.md -t json -o outputs/daily-log.json

# Generate CSV from triage report
grep "^-" outputs/email-triage.md | sed 's/^- //' > outputs/triage.csv
```

### Automate with Shell Scripts

Wrap workflows in executable scripts:

```bash
#!/bin/bash
# generate-daily-log.sh

cd "$(dirname "$0")"
prompt=$(cat code-along/02-operational-memory/prompts/daily-log.md)

# Pass to your AI tool
echo "$prompt" | your-ai-cli > code-along/02-operational-memory/outputs/daily-log.md

echo "Daily log generated at $(date)"
```
