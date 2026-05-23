---
name: openclaw-executive-assistant-webinar
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - set up openclaw executive assistant workflow
  - create ai assistant for email triage
  - build operational memory system with openclaw
  - implement data intake review automation
  - generate daily log with openclaw
  - triage emails with local ai assistant
  - create weekly summary automation
  - set up local-first executive assistant
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This project provides a hands-on workshop for building local-first AI executive assistant workflows using OpenClaw. It demonstrates three core patterns: data intake review, operational memory management, and offline communications triage. All operations are local-only with markdown outputs.

## What It Does

The workshop teaches you to build an AI executive assistant that:

1. **Data Intake Review** - Converts unknown files into structured intake reports
2. **Operational Memory** - Transforms work notes into daily logs and weekly summaries
3. **Offline Communications Triage** - Processes exported emails into actionable lists

All workflows produce reviewable markdown artifacts and stay entirely within local folders.

## Project Structure

```
code-along/
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/intake-review.md
│   ├── outputs/           # Generated reports
│   └── expected/report-outline.md
├── 02-operational-memory/
│   ├── inbox/             # Work notes
│   ├── prompts/daily-log.md
│   ├── prompts/weekly-hype.md
│   ├── outputs/           # Daily/weekly logs
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported emails
│   ├── prompts/email-triage.md
│   ├── outputs/           # Triage reports
│   └── expected/report-outline.md
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

No additional dependencies required - this is a workshop template using prompt engineering patterns.

## Workshop Flow

### Exercise 1: Data Intake Review

Turn unknown files into trustworthy reports.

**Process:**
1. Place files in `code-along/01-data-intake-review/incoming/`
2. Copy prompt from `prompts/intake-review.md`
3. Feed prompt + files to your AI assistant
4. Save output to `outputs/intake-review.md`

**Example Prompt Pattern:**

```markdown
You are reviewing incoming files for an executive.

Analyze all files in the incoming/ folder and create a report with:

1. **File Inventory** - List each file with type and size
2. **Content Summary** - Brief description of each file's contents
3. **Priority Assessment** - High/Medium/Low urgency
4. **Recommended Actions** - What should be done with each file
5. **Risk Flags** - Any concerns or items needing attention

Format as clean markdown suitable for executive review.
```

**Expected Output Structure:**

```markdown
# Data Intake Review - [Date]

## File Inventory
- document1.pdf (245 KB) - Contract proposal
- notes.txt (12 KB) - Meeting notes
- data.csv (890 KB) - Q1 metrics

## Priority Assessment
### High Priority
- document1.pdf - Requires signature by EOW

### Medium Priority
- data.csv - Review for weekly meeting

### Low Priority
- notes.txt - Informational only

## Recommended Actions
...
```

### Exercise 2: Operational Memory

Transform work residue into momentum documents.

**Daily Log Pattern:**

```markdown
You are maintaining an operational memory system.

Review all notes in the inbox/ folder and create a daily log with:

1. **Completed Today** - What got done
2. **Active Threads** - Ongoing work
3. **Blockers** - What's stuck and why
4. **Next Actions** - Clear next steps
5. **Notes & Context** - Important details to remember

Keep entries concise. Focus on forward momentum.
```

**Weekly Summary Pattern:**

```markdown
You are creating a weekly hype document.

Review the past week's daily logs and create:

1. **Week in Review** - Major accomplishments
2. **Momentum Indicators** - What's accelerating
3. **Challenges Navigated** - Problems solved
4. **Upcoming Focus** - Next week priorities
5. **Wins to Celebrate** - Highlight reel

Make it energizing and forward-looking.
```

**Automation Example:**

```bash
# Add to crontab for daily log generation
# Run every weekday at 5 PM
0 17 * * 1-5 /path/to/generate-daily-log.sh

# Weekly summary every Friday at 6 PM
0 18 * * 5 /path/to/generate-weekly-hype.sh
```

**Shell Script Template:**

```bash
#!/bin/bash
# generate-daily-log.sh

DATE=$(date +%Y-%m-%d)
INBOX_DIR="code-along/02-operational-memory/inbox"
OUTPUT_FILE="code-along/02-operational-memory/outputs/daily-log-${DATE}.md"
PROMPT_FILE="code-along/02-operational-memory/prompts/daily-log.md"

# Use your preferred AI CLI tool
# Example with hypothetical 'ai' command:
cat "$PROMPT_FILE" | ai process --context "$INBOX_DIR" > "$OUTPUT_FILE"

echo "Daily log generated: $OUTPUT_FILE"
```

### Exercise 3: Offline Communications Triage

Process exported emails into action lists.

**Email Triage Pattern:**

```markdown
You are triaging communications for an executive.

Review all .eml files and create a triage report with:

1. **Urgent & Action Required** - Needs immediate response
2. **Decision Needed** - Requires executive input
3. **FYI & Monitoring** - Informational, low priority
4. **Delegable** - Can be handled by others
5. **Archive/Ignore** - No action needed

For each email include:
- From/Subject
- Key point (one sentence)
- Recommended action
- Suggested response (if applicable)
```

**Example Output:**

```markdown
# Email Triage Report - [Date]

## Urgent & Action Required

### Email: Contract Review from legal@company.com
**Subject:** Q2 Partnership Agreement - Signature Needed
**Key Point:** Legal cleared partnership agreement, needs signature by Friday
**Action:** Review sections 4.2 and 7.1, sign and return
**Priority:** HIGH - 2 day deadline

## Decision Needed

### Email: Budget Proposal from finance@company.com
**Subject:** Marketing Budget Reallocation Request
**Key Point:** Marketing requests 15K budget shift from print to digital
**Action:** Review attached analysis and approve/deny
**Priority:** MEDIUM - Needed for monthly planning

## Delegable

### Email: Conference Invitation from events@industry.org
**Subject:** Speaking Opportunity - Tech Summit 2026
**Action:** Forward to marketing team for evaluation
```

## Common Patterns

### Folder-Based Workflows

All exercises follow this structure:

```
exercise-name/
├── incoming/ or inbox/ or eml/    # Input files
├── prompts/                        # Reusable prompt templates
├── outputs/                        # Generated artifacts
└── expected/                       # Reference examples
```

### Prompt Design Principles

1. **Clear Role Definition** - "You are reviewing/triaging/maintaining..."
2. **Structured Output** - Use numbered sections and markdown formatting
3. **Actionable Results** - Focus on next steps, not just summaries
4. **Context Preservation** - Include enough detail for follow-up

### Markdown Output Standards

All outputs should be:
- Clean, readable markdown
- Dated for version tracking
- Structured with clear headings
- Suitable for executive review
- Actionable and forward-looking

## Integration Patterns

### With AI Coding Agents

```bash
# Copy prompt to clipboard
cat code-along/01-data-intake-review/prompts/intake-review.md | pbcopy

# In your AI agent:
# 1. Paste prompt
# 2. Reference the incoming/ folder
# 3. Request markdown output
# 4. Save to outputs/ folder
```

### With Cron Jobs

```cron
# Daily operations at 5 PM weekdays
0 17 * * 1-5 cd /path/to/project && ./scripts/daily-log.sh

# Weekly summary Friday 6 PM
0 18 * * 5 cd /path/to/project && ./scripts/weekly-hype.sh

# Email triage every morning 9 AM
0 9 * * * cd /path/to/project && ./scripts/email-triage.sh
```

### With Mission Control Dashboard

Create a simple HTML index linking to generated artifacts:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Mission Control</title>
</head>
<body>
    <h1>Executive Assistant Dashboard</h1>
    
    <h2>Recent Outputs</h2>
    <ul>
        <li><a href="./01-data-intake-review/outputs/">Intake Reviews</a></li>
        <li><a href="./02-operational-memory/outputs/">Daily Logs</a></li>
        <li><a href="./03-offline-communications-triage/outputs/">Email Triage</a></li>
    </ul>
</body>
</html>
```

## Troubleshooting

### Outputs Are Too Verbose

Modify prompts to emphasize brevity:
```markdown
Keep each entry to 1-2 sentences max. Focus only on actionable items.
```

### Missing Context Between Reports

Add cross-referencing to prompts:
```markdown
Reference previous reports in outputs/. Highlight what's changed or progressed.
```

### Inconsistent Formatting

Create output templates in `expected/` folders and reference them:
```markdown
Format output exactly like the template in expected/report-outline.md
```

### Files Not Being Processed

Ensure file paths are explicit in prompts:
```markdown
Process ALL files in the incoming/ directory, including subdirectories.
```

### Automation Not Running

Check cron logs:
```bash
# View cron logs
grep CRON /var/log/syslog

# Test script manually
bash -x ./scripts/daily-log.sh
```

## Best Practices

1. **Review Before Acting** - Always review AI-generated triage before taking action
2. **Version Control** - Date-stamp all outputs for historical tracking
3. **Iterate Prompts** - Refine prompts based on output quality
4. **Privacy First** - Keep sensitive data local, never upload to external services
5. **Consistent Naming** - Use ISO date format (YYYY-MM-DD) in filenames
6. **Archive Regularly** - Move old outputs to archive/ folders monthly

## Resources

- Workshop HTML: `webinar-runbook.html`
- Index: `code-along/INDEX.md`
- DataCamp Webinar: https://www.datacamp.com/webinars/build-your-own-executive-assistant-with-openclaw
