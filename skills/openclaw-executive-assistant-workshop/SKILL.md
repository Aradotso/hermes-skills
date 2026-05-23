---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows using OpenClaw with file-based data intake, operational memory, and communications triage
triggers:
  - build an executive assistant with openclaw
  - set up openclaw workflow for email triage
  - create operational memory system with openclaw
  - implement data intake review process
  - build local-first assistant workflows
  - automate communications triage with openclaw
  - set up daily and weekly log automation
  - process incoming files with openclaw
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This project teaches local-first OpenClaw workflows for building an AI executive assistant. It covers three core patterns: data intake review, operational memory management, and offline communications triage. All workflows use local files and produce reviewable markdown artifacts with no external integrations.

## What This Project Does

The workshop demonstrates three practical AI assistant patterns:

1. **Data intake review** — Turn unknown files into trustworthy intake reports
2. **Operational memory** — Convert work residue into daily/weekly momentum docs
3. **Offline communications triage** — Transform exported emails into action lists

All exercises are copy/paste prompt-based, use local folders only, and generate markdown outputs.

## Installation

Clone the repository:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

No dependencies to install — this is a prompt-based workshop using your preferred AI tool (Claude, ChatGPT, etc.).

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
│   ├── inbox/             # Notes and work residue
│   ├── prompts/
│   │   ├── daily-log.md
│   │   └── weekly-hype.md
│   ├── outputs/
│   ├── schedule/
│   │   ├── cron-examples.md
│   │   └── heartbeat-note.md
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/
│   │   └── email-triage.md
│   ├── outputs/
│   └── expected/
│       └── report-outline.md
└── mission-control/       # Optional dashboard
```

## Workshop Flow

### Exercise 1: Data Intake Review

Process unknown files in the `incoming/` folder:

```bash
cd code-along/01-data-intake-review
```

1. Review files in `incoming/`
2. Copy the prompt from `prompts/intake-review.md`
3. Provide the prompt and file contents to your AI assistant
4. Save output as `outputs/intake-review.md`

**Expected output structure** (see `expected/report-outline.md`):
- File inventory with types and sizes
- Content summary for each file
- Risk assessment
- Recommended actions
- Priority classification

### Exercise 2: Operational Memory

Generate daily and weekly summaries from work notes:

```bash
cd code-along/02-operational-memory
```

**Daily log:**
1. Review files in `inbox/`
2. Use `prompts/daily-log.md` prompt
3. Generate `outputs/daily-log.md`

**Weekly summary:**
1. Use `prompts/weekly-hype.md` prompt
2. Generate `outputs/weekly-hype.md`

**Automation pattern** (from `schedule/cron-examples.md`):

```bash
# Daily log at 6 PM
0 18 * * * /path/to/generate-daily-log.sh

# Weekly summary Friday at 5 PM
0 17 * * 5 /path/to/generate-weekly-hype.sh
```

**Example daily log structure:**
```markdown
# Daily Log — 2026-05-15

## Completed Today
- [Task descriptions from inbox notes]

## In Progress
- [Active work items]

## Blockers
- [Issues needing resolution]

## Tomorrow's Focus
- [Planned priorities]
```

### Exercise 3: Offline Communications Triage

Process exported emails into action items:

```bash
cd code-along/03-offline-communications-triage
```

1. Place `.eml` files in `eml/` folder
2. Use `prompts/email-triage.md` prompt
3. Generate `outputs/email-triage.md`

**Expected output structure**:
```markdown
# Email Triage Report

## Immediate Action Required
- **From:** sender@example.com
- **Subject:** Urgent deadline
- **Action:** Respond by EOD with status update
- **Priority:** HIGH

## This Week
[Medium priority items]

## FYI / Low Priority
[Informational messages]

## Can Archive
[Already resolved or irrelevant]
```

## Prompt Pattern Examples

### Data Intake Review Prompt Template

```markdown
Review the files in the incoming/ directory and create a comprehensive intake report.

For each file, provide:
1. Filename, type, and size
2. Content summary (2-3 sentences)
3. Risk assessment (low/medium/high)
4. Recommended action
5. Priority level

Group by priority and include an executive summary at the top.
Output as markdown to intake-review.md
```

### Daily Log Prompt Template

```markdown
Review all notes in inbox/ from today and create a daily log.

Include:
- Completed tasks
- In-progress work
- Blockers or issues
- Tomorrow's top 3 priorities

Use bullet points, keep it concise, output as markdown.
```

### Email Triage Prompt Template

```markdown
Analyze all .eml files and categorize emails by urgency.

Categories:
1. Immediate action (today)
2. This week
3. FYI / Low priority
4. Can archive

For each email include: sender, subject, required action, deadline if applicable.
Output as markdown with clear sections.
```

## Common Patterns

### Local-First Workflow
All exercises follow this pattern:
1. Place source files in designated input folder
2. Copy prompt from `prompts/` directory
3. Paste prompt + file contents into AI assistant
4. Review and edit AI output
5. Save to `outputs/` folder as markdown

### File Organization
```
exercise-name/
├── incoming/ or inbox/ or eml/    # Input files
├── prompts/                        # Prompt templates
├── outputs/                        # Generated artifacts
└── expected/                       # Example outputs
```

### Prompt Engineering
- Be specific about input location
- Define clear output format (markdown)
- Specify output filename
- Include structure/template in prompt
- Request prioritization or categorization

## Automation Considerations

### Scheduled Generation

For operational memory, consider cron jobs:

```bash
# Daily at end of workday
0 18 * * 1-5 cd /path/to/02-operational-memory && ./generate-daily.sh

# Weekly summary
0 17 * * 5 cd /path/to/02-operational-memory && ./generate-weekly.sh
```

### Shell Script Example

```bash
#!/bin/bash
# generate-daily.sh

PROMPT_FILE="prompts/daily-log.md"
INBOX_DIR="inbox"
OUTPUT_FILE="outputs/daily-log-$(date +%Y-%m-%d).md"

# Combine prompt with file contents
echo "Generating daily log from $INBOX_DIR..."

# (Here you'd call your AI API with the prompt and inbox contents)
# For manual workflow, this script just prepares the context

echo "Output should be saved to: $OUTPUT_FILE"
```

## Troubleshooting

**No output generated:**
- Verify input files exist in correct folder
- Check prompt includes output filename
- Ensure AI has access to file contents

**Output format incorrect:**
- Review `expected/` folder for structure reference
- Add explicit markdown formatting to prompt
- Request specific sections/headers

**Automation not running:**
- Verify cron syntax: `crontab -l`
- Check script permissions: `chmod +x script.sh`
- Test script manually before scheduling
- Review cron logs: `/var/log/cron` or `grep CRON /var/log/syslog`

**Large email files:**
- Process emails in batches
- Prioritize by sender/date first
- Split triage into multiple runs

## Best Practices

1. **Review before saving** — Always verify AI outputs before committing
2. **Version outputs** — Use dates in filenames: `daily-log-2026-05-15.md`
3. **Iterate prompts** — Refine prompts based on output quality
4. **Archive processed files** — Move items from `inbox/` to `archive/` after processing
5. **Keep prompts updated** — Adjust prompts as your needs evolve

## Resources

- Workshop walkthrough: `webinar-runbook.html`
- Main index: `code-along/INDEX.md`
- DataCamp webinar: https://www.datacamp.com/webinars/build-your-own-executive-assistant-with-openclaw
