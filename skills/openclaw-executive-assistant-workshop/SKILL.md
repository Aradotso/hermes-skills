---
name: openclaw-executive-assistant-workshop
description: Build local-first executive assistant workflows using OpenClaw for data intake, operational memory, and communications triage
triggers:
  - build an executive assistant with openclaw
  - create openclaw workflow for task management
  - setup openclaw data intake review
  - generate operational memory with openclaw
  - triage emails using openclaw
  - create local-first assistant workflow
  - setup openclaw daily and weekly logs
  - build openclaw communications processor
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## What This Project Does

This workshop teaches you to build a local-first executive assistant using OpenClaw workflows. It demonstrates three core patterns:

1. **Data intake review** — Transform unknown files into structured reports
2. **Operational memory** — Convert work residue into daily/weekly momentum docs
3. **Offline communications triage** — Process exported emails into action lists

All workflows operate on local files and produce reviewable markdown artifacts. No external integrations or API calls required.

## Installation

Clone the repository:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

No dependencies to install — this is a prompt-based workshop using local files and your AI assistant.

## Repository Structure

```
.
├── webinar-runbook.html              # Main workshop walkthrough
└── code-along/
    ├── INDEX.md                       # Workshop overview
    ├── 01-data-intake-review/         # Exercise 1
    ├── 02-operational-memory/         # Exercise 2
    ├── 03-offline-communications-triage/  # Exercise 3
    └── mission-control/               # Optional dashboard
```

## Workshop Flow

### Exercise 1: Data Intake Review

**Location:** `code-along/01-data-intake-review/`

Transform unknown files in `incoming/` into a structured intake report.

**Files:**
- `incoming/` — Files to inspect
- `prompts/intake-review.md` — Report generation instructions
- `outputs/` — Generated reports
- `expected/report-outline.md` — Reference format

**Workflow:**

1. Read the prompt template:
```bash
cat code-along/01-data-intake-review/prompts/intake-review.md
```

2. Apply prompt to files in `incoming/` directory

3. Generate output:
```bash
# Output should be saved to:
# code-along/01-data-intake-review/outputs/intake-review.md
```

**Expected Output Structure:**
- File inventory with types and sizes
- Content summaries
- Recommended actions
- Priority classification

### Exercise 2: Operational Memory

**Location:** `code-along/02-operational-memory/`

Convert work notes and residue into daily logs and weekly summaries.

**Files:**
- `inbox/` — Work notes and residue
- `prompts/daily-log.md` — Daily log generation prompt
- `prompts/weekly-hype.md` — Weekly summary prompt
- `schedule/cron-examples.md` — Automation examples
- `schedule/heartbeat-note.md` — Scheduling guidance
- `outputs/` — Generated logs

**Daily Log Workflow:**

1. Read daily log prompt:
```bash
cat code-along/02-operational-memory/prompts/daily-log.md
```

2. Process inbox files with prompt

3. Generate daily log:
```bash
# Output: code-along/02-operational-memory/outputs/daily-log.md
```

**Weekly Summary Workflow:**

1. Read weekly prompt:
```bash
cat code-along/02-operational-memory/prompts/weekly-hype.md
```

2. Aggregate daily logs

3. Generate weekly summary:
```bash
# Output: code-along/02-operational-memory/outputs/weekly-hype.md
```

**Automation Pattern:**

Set up cron jobs for recurring generation (see `schedule/cron-examples.md`):

```bash
# Example: Daily log at 5 PM
0 17 * * * /path/to/generate-daily-log.sh

# Example: Weekly summary on Friday at 4 PM
0 16 * * 5 /path/to/generate-weekly-hype.sh
```

### Exercise 3: Offline Communications Triage

**Location:** `code-along/03-offline-communications-triage/`

Process exported email files into actionable triage reports.

**Files:**
- `eml/` — Exported email files
- `prompts/email-triage.md` — Triage instructions
- `outputs/` — Generated reports
- `expected/report-outline.md` — Reference format

**Workflow:**

1. Export emails to `eml/` directory as `.eml` files

2. Read triage prompt:
```bash
cat code-along/03-offline-communications-triage/prompts/email-triage.md
```

3. Apply prompt to email files

4. Generate triage report:
```bash
# Output: code-along/03-offline-communications-triage/outputs/email-triage.md
```

**Expected Output Structure:**
- High priority items requiring immediate action
- Medium priority items for this week
- Low priority items or FYI
- Email metadata (sender, subject, date)
- Recommended responses or next steps

## Key Patterns

### Local-First Workflow

All exercises operate on local files:

```bash
# Input directory structure
exercise/
├── incoming/     # or inbox/ or eml/
├── prompts/      # Prompt templates
├── outputs/      # Generated artifacts
└── expected/     # Reference outputs
```

### Markdown Artifacts

All outputs are markdown for:
- Version control compatibility
- Human readability
- Easy searching and linking
- Portability

### Copy-Paste Prompts

Each prompt file contains complete instructions:

```markdown
# Example prompt structure
## Context
[What you're analyzing]

## Task
[What to generate]

## Output Format
[Structure and requirements]

## Guidelines
[Specific rules and constraints]
```

## Configuration

No configuration files needed. Customize by:

1. **Editing prompts** — Modify templates in `prompts/` directories
2. **Adjusting output locations** — Change paths in your workflow scripts
3. **Scheduling automation** — Use cron or similar for recurring tasks

## Mission Control (Optional)

**Location:** `code-along/mission-control/`

Create a dashboard linking to generated artifacts:

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
    <li><a href="../02-operational-memory/outputs/weekly-hype.md">This Week's Summary</a></li>
    <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
  </ul>
</body>
</html>
```

## Troubleshooting

**Prompt not producing expected output:**
- Check `expected/` folders for reference format
- Ensure all input files are present in source directories
- Verify prompt template hasn't been truncated

**Missing outputs directory:**
```bash
mkdir -p code-along/01-data-intake-review/outputs
mkdir -p code-along/02-operational-memory/outputs
mkdir -p code-along/03-offline-communications-triage/outputs
```

**Cron job not running:**
- Verify script has execute permissions: `chmod +x script.sh`
- Check cron syntax with `crontab -l`
- Ensure full paths are used in cron commands
- Check system logs: `grep CRON /var/log/syslog`

**Email export issues:**
- Ensure emails are exported as `.eml` format
- Verify file encoding is UTF-8
- Check that email headers are intact

## Best Practices

1. **Review outputs** — Always review generated artifacts before acting
2. **Version control** — Commit outputs to track evolution over time
3. **Iterate prompts** — Refine prompt templates based on output quality
4. **Automate gradually** — Start manual, automate after validating workflow
5. **Archive regularly** — Move old outputs to dated folders for history
