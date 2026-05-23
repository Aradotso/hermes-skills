---
name: openclaw-executive-assistant-workshop
description: Local-first OpenClaw workflows for data intake, operational memory, and communications triage using AI prompts and markdown outputs
triggers:
  - how do I build an executive assistant with OpenClaw
  - set up OpenClaw data intake review
  - create operational memory workflow with OpenClaw
  - implement OpenClaw email triage
  - use OpenClaw for local file processing
  - build markdown-based AI assistant workflows
  - set up local-first executive assistant
  - OpenClaw communications triage workflow
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This project teaches local-first OpenClaw workflows for building an executive assistant that processes files, maintains operational memory, and triages communications—all using local folders and markdown artifacts.

## What It Does

The workshop covers three core workflows:

1. **Data intake review** — Turn unknown files into trustworthy intake reports
2. **Operational memory** — Convert work residue into daily and weekly momentum docs
3. **Offline communications triage** — Transform exported emails into action lists

All processing stays local with no live integrations, using copy/paste prompts and markdown outputs.

## Installation

Clone the repository:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

No dependencies required—this is a file-based workshop using your AI agent.

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/intake-review.md
│   ├── outputs/
│   └── expected/report-outline.md
├── 02-operational-memory/
│   ├── inbox/             # Notes and work residue
│   ├── prompts/daily-log.md
│   ├── prompts/weekly-hype.md
│   ├── outputs/
│   └── schedule/
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/email-triage.md
│   ├── outputs/
│   └── expected/report-outline.md
└── mission-control/       # Dashboard links (optional)
```

## Workflow 1: Data Intake Review

Turn unknown incoming files into a structured report.

### Setup

Navigate to the exercise:

```bash
cd code-along/01-data-intake-review
```

### Process

1. Review files in `incoming/` directory
2. Read the prompt from `prompts/intake-review.md`
3. Apply prompt to files using your AI agent
4. Generate `outputs/intake-review.md`

### Expected Output Structure

The output should include:

- File inventory with types and sizes
- Content summaries
- Risk assessment
- Recommended actions
- Priority categorization

### Example Prompt Pattern

```markdown
Review all files in the incoming/ directory and create an intake report that includes:

1. File inventory (name, type, size)
2. Content summary for each file
3. Risk or sensitivity flags
4. Recommended next actions
5. Priority ranking

Output as structured markdown.
```

## Workflow 2: Operational Memory

Convert scattered notes into daily logs and weekly summaries.

### Setup

```bash
cd code-along/02-operational-memory
```

### Daily Log Process

1. Review content in `inbox/` directory
2. Apply `prompts/daily-log.md` prompt
3. Generate `outputs/daily-log.md`

### Weekly Summary Process

1. Apply `prompts/weekly-hype.md` prompt to accumulated logs
2. Generate `outputs/weekly-hype.md`

### Automation with Cron

Reference `schedule/cron-examples.md` for scheduling patterns:

```bash
# Daily log at 5 PM
0 17 * * * /path/to/generate-daily-log.sh

# Weekly summary on Friday at 4 PM
0 16 * * 5 /path/to/generate-weekly-summary.sh
```

### Example Daily Log Prompt

```markdown
Review all notes and work artifacts from today in the inbox/ directory.

Create a daily log with:
- Key accomplishments
- Decisions made
- Open questions
- Tomorrow's priorities
- Links to relevant files

Format as markdown with clear sections.
```

### Example Weekly Summary Prompt

```markdown
Review all daily logs from this week.

Create a weekly summary with:
- Weekly highlights
- Momentum indicators
- Blockers resolved
- Next week's focus areas
- Metrics or wins

Use an energizing tone. Format as markdown.
```

## Workflow 3: Offline Communications Triage

Process exported emails into actionable reports.

### Setup

```bash
cd code-along/03-offline-communications-triage
```

### Process

1. Place exported `.eml` files in `eml/` directory
2. Apply `prompts/email-triage.md` prompt
3. Generate `outputs/email-triage.md`

### Expected Output Structure

- Urgent items requiring immediate action
- Important items for this week
- FYI items (no action needed)
- Spam/low-priority items
- Recommended responses or next steps

### Example Triage Prompt

```markdown
Review all .eml files in the eml/ directory.

Create an email triage report with:

## Urgent (Action Today)
- Sender, subject, deadline
- Recommended response

## Important (This Week)
- Sender, subject, context
- Suggested action

## FYI (No Action)
- Brief summary

## Low Priority / Spam
- List only

Include time estimates for responses where applicable.
```

## Common Patterns

### Local-First Processing

All workflows follow this pattern:

1. **Input directory** — Place raw files here
2. **Prompt file** — Contains instructions for AI agent
3. **Output directory** — Generated markdown artifacts
4. **Expected directory** — Reference examples (where applicable)

### Prompt Execution Workflow

```bash
# 1. Navigate to exercise
cd code-along/01-data-intake-review

# 2. View prompt
cat prompts/intake-review.md

# 3. Apply with AI agent to input files
# (use your agent's file reading capabilities)

# 4. Save output
# outputs/intake-review.md
```

### Reviewing Outputs

```bash
# View generated report
cat outputs/intake-review.md

# Compare with expected structure
diff outputs/intake-review.md expected/report-outline.md
```

## Mission Control Dashboard

The optional `mission-control/` directory can host a simple HTML dashboard:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Executive Assistant Dashboard</title>
</head>
<body>
  <h1>Mission Control</h1>
  <nav>
    <a href="../01-data-intake-review/outputs/intake-review.md">Latest Intake Report</a>
    <a href="../02-operational-memory/outputs/daily-log.md">Today's Log</a>
    <a href="../02-operational-memory/outputs/weekly-hype.md">Weekly Summary</a>
    <a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a>
  </nav>
</body>
</html>
```

## Configuration

### Environment Variables

No environment variables required—all processing is local.

### Customizing Prompts

Edit prompt files to match your needs:

```bash
# Customize daily log format
vim code-along/02-operational-memory/prompts/daily-log.md

# Adjust triage categories
vim code-along/03-offline-communications-triage/prompts/email-triage.md
```

## Troubleshooting

### No Output Generated

- Ensure input files exist in the correct directory
- Verify your AI agent has file reading permissions
- Check prompt file syntax

### Output Quality Issues

- Refine prompt specificity in `prompts/*.md` files
- Add examples to prompts
- Break complex prompts into smaller steps

### File Organization

```bash
# Reset an exercise
rm -rf code-along/01-data-intake-review/outputs/*

# Check structure
tree code-along/
```

## Best Practices

1. **Version control outputs** — Track generated reports in git
2. **Iterate on prompts** — Refine prompts based on output quality
3. **Regular cadence** — Run operational memory workflows daily/weekly
4. **Review before action** — Always review AI-generated triage before acting
5. **Backup inputs** — Keep copies of original files before processing

## Walkthrough

Open `webinar-runbook.html` in a browser for the full guided workshop experience.
