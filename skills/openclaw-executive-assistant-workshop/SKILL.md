---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "build an executive assistant with OpenClaw"
  - "set up local OpenClaw workflows"
  - "create AI assistant for email triage"
  - "implement operational memory with OpenClaw"
  - "generate daily logs from work notes"
  - "triage communications with AI locally"
  - "build markdown-based AI workflows"
  - "create file intake review system"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This project provides a hands-on workshop for building local-first AI executive assistant workflows using OpenClaw. It focuses on three core workflows: data intake review, operational memory generation, and offline communications triage. All processing stays local and produces reviewable markdown artifacts.

## What This Project Does

The OpenClaw Executive Assistant Workshop teaches you to build AI workflows that:

1. **Data Intake Review** — Turn unknown files into trustworthy intake reports
2. **Operational Memory** — Convert work residue into daily and weekly momentum docs
3. **Offline Communications Triage** — Process exported emails into actionable lists

All workflows are:
- Local files only (no cloud dependencies)
- Copy/paste prompt-based
- Markdown output focused
- Reviewable and version-controllable

## Project Structure

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
│   ├── outputs/
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/email-triage.md
│   ├── outputs/
│   └── expected/report-outline.md
└── mission-control/       # Optional dashboard
```

## Installation

Clone the repository:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

No dependencies to install — this is a prompt-based workflow using your AI assistant of choice.

## Workshop Walkthrough

Open `webinar-runbook.html` in your browser for the full guided experience.

## Exercise 1: Data Intake Review

**Goal:** Turn unknown files into a trustworthy intake report.

**Workflow:**

1. Place unknown files in `code-along/01-data-intake-review/incoming/`
2. Read the prompt template in `prompts/intake-review.md`
3. Give your AI assistant context about the files:

```
I have files in code-along/01-data-intake-review/incoming/ that need review.
Please read the prompt in prompts/intake-review.md and generate an intake 
report following that template. Output to outputs/intake-review.md
```

**Expected Output:** `outputs/intake-review.md` containing:
- File inventory
- Content summaries
- Risk assessment
- Recommended actions
- Priority ranking

**Prompt Pattern:**

The intake review prompt should guide the AI to:
- List all files with sizes and types
- Identify content categories
- Flag potential issues (malware risk, sensitive data, etc.)
- Suggest next steps for each file
- Create a prioritized action list

## Exercise 2: Operational Memory

**Goal:** Convert daily work residue into momentum documentation.

**Workflow for Daily Log:**

1. Drop work notes, chat logs, or TODO scraps into `code-along/02-operational-memory/inbox/`
2. Use the daily log prompt:

```
I have work residue in code-along/02-operational-memory/inbox/.
Please read prompts/daily-log.md and generate a daily log summary.
Output to outputs/daily-log.md
```

**Expected Output:** `outputs/daily-log.md` containing:
- Date and context
- Key accomplishments
- Decisions made
- Open threads
- Tomorrow's focus

**Workflow for Weekly Summary:**

```
Review the daily logs from this week in outputs/ and generate a weekly 
summary using prompts/weekly-hype.md. Output to outputs/weekly-hype.md
```

**Expected Output:** `outputs/weekly-hype.md` containing:
- Week overview
- Major wins
- Momentum builders
- Blockers resolved
- Next week priorities

**Automation Pattern:**

The `schedule/cron-examples.md` shows how to automate this with cron:

```bash
# Daily log generation at 5pm
0 17 * * * cd ~/projects/openclaw-assistant && ./generate-daily-log.sh

# Weekly summary on Friday at 4pm
0 16 * * 5 cd ~/projects/openclaw-assistant && ./generate-weekly-summary.sh
```

Example shell script (`generate-daily-log.sh`):

```bash
#!/bin/bash
DATE=$(date +%Y-%m-%d)
AI_COMMAND="claude" # or cursor, codex, etc.

$AI_COMMAND "Generate daily log for $DATE using 
code-along/02-operational-memory/prompts/daily-log.md 
from inbox files. Output to outputs/daily-log-$DATE.md"
```

## Exercise 3: Offline Communications Triage

**Goal:** Turn exported emails into an action list.

**Workflow:**

1. Export emails as `.eml` files to `code-along/03-offline-communications-triage/eml/`
2. Use the triage prompt:

```
I have exported emails in code-along/03-offline-communications-triage/eml/.
Please read prompts/email-triage.md and generate a triage report.
Output to outputs/email-triage.md
```

**Expected Output:** `outputs/email-triage.md` containing:
- High priority items requiring immediate response
- Medium priority items for this week
- Low priority FYI items
- Spam/irrelevant messages to delete
- Suggested reply templates for key messages

**Email Export Pattern:**

Most email clients support `.eml` export:

- **Gmail:** Select emails → More → Download message
- **Outlook:** Drag email to folder, save as `.eml`
- **Apple Mail:** Select → File → Save As

## Configuration

No configuration files needed. Customize by editing prompt templates:

- `prompts/intake-review.md` — Adjust risk thresholds, categorization
- `prompts/daily-log.md` — Change summary format, emphasis areas
- `prompts/weekly-hype.md` — Modify tone (professional, casual, etc.)
- `prompts/email-triage.md` — Set priority criteria, response templates

## Common Patterns

**Pattern: Incremental Processing**

Process files in batches to avoid context overload:

```
Process the first 5 files in incoming/ and generate partial report.
Then process the next 5 and merge into the report.
```

**Pattern: Context Persistence**

Link related workflows:

```
Read yesterday's daily-log.md for context, then generate today's log
incorporating continuity from open threads.
```

**Pattern: Review Checkpoints**

Always generate reviewable artifacts before taking action:

```
Generate the triage report first. I'll review it before you draft any replies.
```

## Troubleshooting

**Issue:** AI output is too generic

**Solution:** Add specific examples to prompts. Reference `expected/` folders for desired output structure.

**Issue:** Context window exceeded with many files

**Solution:** Process in batches or summarize files first:

```
First, create a file inventory with one-line summaries.
Then generate full report from summaries.
```

**Issue:** Prompts not producing consistent format

**Solution:** Use explicit markdown templates in your prompts:

```markdown
## Expected Format

### File Inventory
- **filename.ext** (size) — one-line summary

### Risk Assessment
...
```

**Issue:** Can't automate with cron

**Solution:** Ensure your AI tool has a CLI interface. For Claude, Cursor, or similar:

1. Check CLI availability: `which claude` or `which cursor`
2. Test command manually before adding to cron
3. Use full paths in cron scripts
4. Redirect output for debugging: `>> /tmp/openclaw.log 2>&1`

## Mission Control (Optional)

The `mission-control/` folder is reserved for building a simple dashboard that links to generated artifacts. Example HTML structure:

```html
<!DOCTYPE html>
<html>
<head><title>Executive Assistant Dashboard</title></head>
<body>
  <h1>Mission Control</h1>
  <section>
    <h2>Recent Reports</h2>
    <ul>
      <li><a href="../01-data-intake-review/outputs/intake-review.md">Latest Intake Review</a></li>
      <li><a href="../02-operational-memory/outputs/daily-log.md">Today's Log</a></li>
      <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
    </ul>
  </section>
</body>
</html>
```

## Best Practices

1. **Review Before Action** — Always review AI-generated reports before executing suggested actions
2. **Version Control** — Commit output files to track evolution of workflows
3. **Iterate Prompts** — Refine prompts based on output quality
4. **Local First** — Keep sensitive data local; never upload to external services
5. **Markdown Everything** — Maintain plain-text artifacts for maximum portability

## Integration with AI Coding Agents

When using this workshop with Claude Code, Cursor, or similar:

```
Load the OpenClaw executive assistant workshop context. I want to set up
the data intake review workflow. Guide me through Exercise 1 step by step.
```

Or for ongoing use:

```
Using the operational memory pattern from the OpenClaw workshop, generate
my daily log from the notes in inbox/.
```
