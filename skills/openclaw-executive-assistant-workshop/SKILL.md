---
name: openclaw-executive-assistant-workshop
description: Workshop for building local-first executive assistant workflows with OpenClaw using file-based data intake, operational memory, and communications triage
triggers:
  - build an executive assistant with openclaw
  - set up openclaw data intake review
  - create operational memory with openclaw
  - triage emails with openclaw
  - run openclaw workshop exercises
  - use openclaw for local file processing
  - build local-first ai assistant
  - process files with openclaw prompts
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## What This Project Does

This is a hands-on workshop for building local-first executive assistant workflows using OpenClaw. It teaches three core patterns:

1. **Data intake review** — Turn unknown files into trustworthy reports
2. **Operational memory** — Transform work residue into daily/weekly momentum docs
3. **Offline communications triage** — Convert exported emails into action lists

All processing stays local, produces markdown artifacts, and uses copy/paste prompts with AI assistants.

## Workshop Structure

The repository contains three progressive exercises in `code-along/`:

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/intake-review.md
│   ├── outputs/
│   └── expected/report-outline.md
├── 02-operational-memory/
│   ├── inbox/             # Work notes/residue
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

### Installation

```bash
# Clone the workshop repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the walkthrough
open webinar-runbook.html
# or
firefox webinar-runbook.html
```

No dependencies to install — this is a prompt-based workshop using local files.

## Exercise 1: Data Intake Review

Process unknown files into structured reports.

### File Layout

```
01-data-intake-review/
├── incoming/              # Place files to review here
├── prompts/intake-review.md
├── outputs/intake-review.md
└── expected/report-outline.md
```

### Workflow

```bash
# 1. Add files to review
cp ~/Downloads/*.pdf code-along/01-data-intake-review/incoming/

# 2. Read the prompt
cat code-along/01-data-intake-review/prompts/intake-review.md

# 3. Give prompt + files to AI assistant
# 4. Save output to:
# code-along/01-data-intake-review/outputs/intake-review.md
```

### Prompt Pattern

The intake review prompt instructs the AI to:
- List all files with metadata (size, type, modified date)
- Identify file purposes and relationships
- Flag suspicious or malformed files
- Suggest next actions (archive, process, delete)
- Output structured markdown

### Expected Output Structure

```markdown
# Data Intake Review - [DATE]

## Files Reviewed
- file1.pdf (245KB, modified 2026-05-10)
- file2.csv (12KB, modified 2026-05-11)

## Analysis
### Trustworthy
- file1.pdf: Invoice from vendor...

### Needs Review
- file2.csv: Unknown data format...

## Recommended Actions
1. Archive file1.pdf to accounting/invoices/
2. Verify source of file2.csv before processing
```

## Exercise 2: Operational Memory

Transform daily work residue into momentum documentation.

### File Layout

```
02-operational-memory/
├── inbox/                 # Drop notes, snippets, logs
├── prompts/
│   ├── daily-log.md
│   └── weekly-hype.md
├── outputs/
│   ├── daily-log.md
│   └── weekly-hype.md
└── schedule/
    ├── cron-examples.md
    └── heartbeat-note.md
```

### Daily Log Workflow

```bash
# 1. Add work residue throughout the day
echo "- Fixed bug in user auth" >> code-along/02-operational-memory/inbox/notes.txt
echo "- Meeting with design team re: dashboard" >> code-along/02-operational-memory/inbox/notes.txt

# 2. At end of day, read prompt
cat code-along/02-operational-memory/prompts/daily-log.md

# 3. Process with AI assistant
# Input: All files in inbox/
# Output: code-along/02-operational-memory/outputs/daily-log.md
```

### Weekly Summary Workflow

```bash
# 1. Collect all daily logs from the week
ls code-along/02-operational-memory/outputs/daily-*.md

# 2. Read weekly prompt
cat code-along/02-operational-memory/prompts/weekly-hype.md

# 3. Process week's logs with AI
# Output: code-along/02-operational-memory/outputs/weekly-hype.md
```

### Automation with Cron

```bash
# Daily log generation at 6 PM
0 18 * * * cd /path/to/workshop && ./scripts/generate-daily-log.sh

# Weekly summary on Friday at 5 PM
0 17 * * 5 cd /path/to/workshop && ./scripts/generate-weekly-hype.sh
```

See `schedule/cron-examples.md` for detailed automation patterns.

### Expected Outputs

**Daily Log:**
```markdown
# Daily Log - 2026-05-11

## Completed
- Fixed authentication bug affecting mobile users
- Dashboard design review meeting

## In Progress
- API rate limiting implementation

## Blocked
- Need design approval for settings page

## Tomorrow
- Complete rate limiting
- Deploy auth fix to staging
```

**Weekly Summary:**
```markdown
# Week of May 5-11, 2026

## Wins
- Shipped user authentication improvements
- Resolved 8 customer support tickets

## Momentum
- Dashboard redesign 60% complete
- API performance improvements in progress

## Next Week
- Launch dashboard beta
- Performance optimization sprint
```

## Exercise 3: Offline Communications Triage

Convert exported email files into actionable lists.

### File Layout

```
03-offline-communications-triage/
├── eml/                   # Exported .eml files
├── prompts/email-triage.md
├── outputs/email-triage.md
└── expected/report-outline.md
```

### Workflow

```bash
# 1. Export emails from mail client to .eml format
# Save to: code-along/03-offline-communications-triage/eml/

# 2. Read triage prompt
cat code-along/03-offline-communications-triage/prompts/email-triage.md

# 3. Process with AI assistant
# Input: All .eml files
# Output: code-along/03-offline-communications-triage/outputs/email-triage.md
```

### Expected Output

```markdown
# Email Triage - [DATE]

## Urgent (Respond Today)
1. **From:** jane@client.com  
   **Subject:** Project deadline moved up  
   **Action:** Confirm new timeline with team

## Important (Respond This Week)
2. **From:** team@vendor.com  
   **Subject:** Invoice #1234  
   **Action:** Forward to accounting

## FYI (No Action Needed)
3. **From:** newsletter@tech.com  
   **Subject:** Weekly roundup  
   **Action:** Archive

## Spam/Delete
4. **From:** suspicious@example.com  
   **Subject:** You won a prize  
   **Action:** Delete
```

## Common Patterns

### Pattern: File-Based Processing

All exercises follow this flow:
1. Drop files in designated input folder
2. Read appropriate prompt from `prompts/`
3. Copy prompt + file contents to AI assistant
4. Save AI output to `outputs/` folder
5. Review generated markdown

### Pattern: Prompt Structure

Workshop prompts follow this template:
```markdown
# [Task Name]

## Context
[What files you're processing and why]

## Instructions
[Step-by-step processing logic]

## Output Format
[Exact markdown structure expected]

## Example
[Sample output]
```

### Pattern: Iterative Refinement

```bash
# First pass
cat prompts/task.md | ai-assistant

# Review output
less outputs/task.md

# Refine with follow-up
echo "Add more detail to the 'Blocked' section" | ai-assistant

# Save final version
```

## Mission Control (Optional)

Create a simple dashboard linking all outputs:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Executive Assistant Dashboard</title>
</head>
<body>
  <h1>Mission Control</h1>
  <ul>
    <li><a href="../01-data-intake-review/outputs/intake-review.md">Latest Intake Review</a></li>
    <li><a href="../02-operational-memory/outputs/daily-log.md">Today's Log</a></li>
    <li><a href="../02-operational-memory/outputs/weekly-hype.md">Weekly Summary</a></li>
    <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
  </ul>
</body>
</html>
```

## Best Practices

1. **Keep files local** — No cloud uploads, no API integrations
2. **Use markdown** — Human-readable, version-controllable
3. **Review outputs** — AI suggestions are starting points, not commands
4. **Version your prompts** — Iterate on prompt quality over time
5. **Automate gradually** — Start manual, automate what works
6. **Archive regularly** — Move processed files out of input folders

## Troubleshooting

### Files Not Processing

**Problem:** AI assistant doesn't see files  
**Solution:** Copy file contents directly into prompt or use AI with file upload capability

### Output Format Issues

**Problem:** Generated markdown doesn't match expected structure  
**Solution:** Be more explicit in prompt about exact headings and formatting

### Prompt Too Long

**Problem:** Too many files exceed context window  
**Solution:** Process in batches or summarize older files first

### Automation Fails

**Problem:** Cron job doesn't run  
**Solution:** Check paths are absolute, test script manually first

```bash
# Debug cron
* * * * * /usr/bin/env > /tmp/cron-env.txt

# Test script manually
cd /path/to/workshop && bash -x ./scripts/generate-daily-log.sh
```

## Integration Tips

### With Task Managers

```bash
# Extract action items and send to task manager
grep "Action:" outputs/email-triage.md | while read line; do
  echo "$line" >> ~/tasks/inbox.txt
done
```

### With Git

```bash
# Version control your outputs
cd code-along
git add outputs/
git commit -m "Daily log $(date +%Y-%m-%d)"
```

### With Obsidian/Notion

Copy generated markdown directly into your knowledge base for linking and search.

## Further Learning

- Study the `expected/` folders to understand output goals
- Modify prompts in `prompts/` to match your workflow
- Check `schedule/` for automation examples
- Review `webinar-runbook.html` for full workshop narrative

This workshop teaches portable patterns — adapt prompts and folder structure to your specific executive assistant needs.
