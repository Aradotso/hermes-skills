---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "build an executive assistant with OpenClaw"
  - "set up OpenClaw data intake review"
  - "create operational memory with OpenClaw"
  - "triage emails with OpenClaw offline"
  - "use OpenClaw for local-only workflows"
  - "generate daily logs and weekly summaries"
  - "review incoming files with AI prompts"
  - "automate personal knowledge management"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This workshop teaches you to build local-first AI executive assistant workflows using OpenClaw. All operations use local files, markdown outputs, and copy/paste prompts—no live integrations required.

## What This Project Does

The OpenClaw Executive Assistant Workshop provides three core automation patterns:

1. **Data Intake Review** — Turn unknown files into trustworthy intake reports
2. **Operational Memory** — Turn work residue into daily logs and weekly summaries
3. **Offline Communications Triage** — Turn exported emails into action lists

All workflows produce reviewable markdown artifacts that stay in local folders.

## Repository Structure

```
.
├── webinar-runbook.html          # Main walkthrough guide
└── code-along/
    ├── INDEX.md                   # Overview
    ├── 01-data-intake-review/     # Exercise 1
    ├── 02-operational-memory/     # Exercise 2
    ├── 03-offline-communications-triage/  # Exercise 3
    └── mission-control/           # Optional dashboard
```

## Installation

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

Open `webinar-runbook.html` in your browser for the full walkthrough.

## Exercise 1: Data Intake Review

**Goal:** Turn unknown files into a trustworthy intake report.

### Folder Structure

```
code-along/01-data-intake-review/
├── incoming/               # Files to inspect
├── prompts/
│   └── intake-review.md   # Prompt template
├── outputs/
│   └── intake-review.md   # Generated report
└── expected/
    └── report-outline.md  # Expected format
```

### Workflow

1. Place unknown files in `incoming/`
2. Open `prompts/intake-review.md` and copy the prompt
3. Provide the prompt to your AI assistant along with the files from `incoming/`
4. Save the generated report to `outputs/intake-review.md`

### Example Prompt Pattern

```markdown
# Data Intake Review Prompt

Review all files in the incoming folder and generate a structured report:

## Files Reviewed
- List each file with type, size, and brief description

## Content Summary
- Summarize key information found

## Recommended Actions
- Suggest next steps for each file

## Risk Assessment
- Flag any suspicious or concerning content

Output format: Markdown
```

### Expected Output Structure

```markdown
# Intake Review Report
Generated: YYYY-MM-DD

## Files Reviewed
- `document.pdf` (245 KB) - Project proposal
- `data.csv` (12 KB) - Contact list
- `notes.txt` (3 KB) - Meeting notes

## Content Summary
[Structured summary of findings]

## Recommended Actions
1. **document.pdf**: Review and file in projects folder
2. **data.csv**: Import to CRM system
3. **notes.txt**: Add to daily log

## Risk Assessment
No security concerns identified.
```

## Exercise 2: Operational Memory

**Goal:** Turn work residue into daily logs and weekly summaries.

### Folder Structure

```
code-along/02-operational-memory/
├── inbox/                 # Notes and work residue
├── prompts/
│   ├── daily-log.md      # Daily prompt
│   └── weekly-hype.md    # Weekly prompt
├── outputs/
│   ├── daily-log.md
│   └── weekly-hype.md
└── schedule/
    ├── cron-examples.md
    └── heartbeat-note.md
```

### Daily Log Workflow

```markdown
# Daily Log Prompt

Review all notes in the inbox folder from today and generate:

## What Got Done
- Completed tasks and accomplishments

## What's In Progress
- Ongoing work with current status

## Blockers & Questions
- Issues requiring attention

## Tomorrow's Priority
- Top 3 items to focus on

Use bullet points. Keep it concise.
```

### Weekly Summary Workflow

```markdown
# Weekly Hype Prompt

Review daily logs from the past week and generate:

## Week at a Glance
- High-level summary of the week

## Key Wins
- Major accomplishments

## Progress Metrics
- Quantifiable progress indicators

## Next Week's Focus
- Top priorities for the coming week

## Learnings & Insights
- Key takeaways

Tone: Positive and momentum-building.
```

### Automation Example (Cron)

```bash
# Daily log generation at 5 PM
0 17 * * * cd ~/openclaw-assistant/code-along/02-operational-memory && ./generate-daily-log.sh

# Weekly summary on Friday at 4 PM
0 16 * * 5 cd ~/openclaw-assistant/code-along/02-operational-memory && ./generate-weekly-summary.sh
```

### Example Daily Log Output

```markdown
# Daily Log - 2026-05-15

## What Got Done
- Completed client proposal review
- Fixed bug in authentication module
- Responded to 12 priority emails

## What's In Progress
- Dashboard redesign (60% complete)
- API documentation update
- Budget planning for Q3

## Blockers & Questions
- Need design approval for homepage
- Waiting on legal review of contract

## Tomorrow's Priority
1. Finish dashboard mockups
2. Deploy authentication fix
3. Schedule Q3 planning meeting
```

## Exercise 3: Offline Communications Triage

**Goal:** Turn exported emails into an action list.

### Folder Structure

```
code-along/03-offline-communications-triage/
├── eml/                   # Exported email files
├── prompts/
│   └── email-triage.md   # Triage prompt
├── outputs/
│   └── email-triage.md   # Generated report
└── expected/
    └── report-outline.md
```

### Email Triage Workflow

1. Export emails as `.eml` files to the `eml/` folder
2. Use the triage prompt to analyze all emails
3. Generate an action-oriented report

### Example Triage Prompt

```markdown
# Email Triage Prompt

Analyze all emails in the eml folder and generate:

## Urgent - Respond Today
- Emails requiring immediate response
- Include sender, subject, required action

## Important - This Week
- Important emails with flexible deadlines
- Include context and suggested response approach

## FYI - Read & Archive
- Informational emails requiring no action

## Delegate
- Emails that should be handled by others

## Low Priority
- Can be addressed later or ignored

For each email, provide:
- Sender
- Subject
- 1-sentence summary
- Recommended action
```

### Example Triage Output

```markdown
# Email Triage Report - 2026-05-15

## Urgent - Respond Today

### 1. Client Deliverable Review
- **From:** sarah@client.com
- **Summary:** Requesting feedback on Q2 deliverables
- **Action:** Review attached document and send approval by EOD

### 2. Team Meeting Conflict
- **From:** john@company.com
- **Summary:** Schedule conflict for Thursday's planning session
- **Action:** Propose alternate time slots

## Important - This Week

### 3. Budget Approval Request
- **From:** finance@company.com
- **Summary:** Q3 budget needs sign-off
- **Action:** Review spreadsheet and approve by Friday

## FYI - Read & Archive

### 4. Industry Newsletter
- **From:** newsletter@industry.org
- **Summary:** Weekly roundup of industry news
- **Action:** Skim and archive

## Delegate

### 5. Technical Support Request
- **From:** support@client.com
- **Summary:** API integration question
- **Action:** Forward to development team lead
```

## Configuration Patterns

### Environment Setup

Create a `.env` file for any API configurations (if extending beyond local-only):

```bash
# .env
OPENCLAW_WORKSPACE=~/openclaw-assistant/code-along
OUTPUT_FORMAT=markdown
TIMEZONE=America/New_York
```

### Prompt Template Variables

Structure prompts with consistent sections:

```markdown
# [Prompt Name]

**Input:** [Description of input files/data]
**Output:** [Description of expected output]
**Tone:** [Formal/Casual/Technical]

## Instructions
[Detailed steps]

## Output Format
[Markdown structure template]
```

## Common Patterns

### 1. Batch File Processing

```bash
#!/bin/bash
# Process all files in a directory

INPUT_DIR="./incoming"
OUTPUT_FILE="./outputs/report.md"

echo "# Batch Processing Report" > "$OUTPUT_FILE"
echo "Generated: $(date)" >> "$OUTPUT_FILE"

for file in "$INPUT_DIR"/*; do
  echo "Processing $file..."
  # Add AI processing command here
done
```

### 2. Incremental Note Accumulation

```bash
#!/bin/bash
# Append daily notes to a running log

DATE=$(date +%Y-%m-%d)
DAILY_LOG="./inbox/daily-notes-$DATE.md"
WEEKLY_LOG="./outputs/weekly-log.md"

cat "$DAILY_LOG" >> "$WEEKLY_LOG"
echo "" >> "$WEEKLY_LOG"
```

### 3. Template-Based Output Generation

Always structure outputs with consistent headers:

```markdown
# [Report Type]
Generated: [Timestamp]

## Summary
[High-level overview]

## Details
[Structured findings]

## Next Steps
[Action items]

---
*Generated by OpenClaw Executive Assistant*
```

## Troubleshooting

### Issue: Prompts produce inconsistent output

**Solution:** Add explicit formatting instructions to prompts:

```markdown
IMPORTANT: Use this exact structure:
## Section Name
- Bullet point format only
- No additional commentary
- Maximum 3 sentences per item
```

### Issue: File processing fails

**Solution:** Verify file permissions and paths:

```bash
# Check file permissions
ls -la code-along/01-data-intake-review/incoming/

# Fix permissions if needed
chmod 644 code-along/01-data-intake-review/incoming/*
```

### Issue: Large email exports overwhelm processing

**Solution:** Batch process in smaller groups:

```bash
# Process 10 emails at a time
ls eml/*.eml | head -10 | xargs -I {} cp {} eml/batch1/
# Process batch1, then continue
```

## Best Practices

1. **Keep prompts version-controlled** — Track prompt changes in git
2. **Review outputs before committing** — AI-generated content should be verified
3. **Use consistent naming conventions** — `YYYY-MM-DD-description.md`
4. **Archive processed files** — Move to `./archive/` after processing
5. **Maintain a changelog** — Document workflow improvements

## Extension Ideas

- **Mission Control Dashboard:** Aggregate all reports into a single HTML view
- **Search Interface:** Add full-text search across all generated reports
- **Automation Scripts:** Create shell scripts to automate recurring workflows
- **Custom Prompt Library:** Build organization-specific prompt templates

---

This workshop is designed for local-first, privacy-respecting AI workflows. All processing happens on your machine with your choice of AI assistant.
