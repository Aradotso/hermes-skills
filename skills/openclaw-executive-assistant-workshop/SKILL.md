---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "help me build an executive assistant with OpenClaw"
  - "how do I triage emails with OpenClaw"
  - "create a daily log workflow with OpenClaw"
  - "set up operational memory system"
  - "build data intake review with AI"
  - "how to use OpenClaw for communications management"
  - "create local-first AI assistant workflows"
  - "set up weekly summary automation with OpenClaw"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill teaches you to build local-first, markdown-based executive assistant workflows using OpenClaw. The workshop covers three core patterns: data intake review, operational memory (daily/weekly logs), and offline communications triage. All workflows are local-only with no external integrations.

## What This Project Does

This is a workshop repository demonstrating how to build AI-powered executive assistant capabilities that:

- Stay entirely local (no cloud dependencies)
- Process unknown files into structured reports
- Turn work residue into momentum documentation
- Triage exported emails into action lists
- Generate reviewable markdown artifacts

**Core Philosophy**: Copy/paste prompts, local files only, markdown outputs.

## Repository Structure

```text
code-along/
├── INDEX.md
├── 01-data-intake-review/        # Unknown files → intake report
│   ├── incoming/                 # Files to inspect
│   ├── prompts/intake-review.md
│   ├── outputs/
│   └── expected/report-outline.md
├── 02-operational-memory/        # Work residue → momentum docs
│   ├── inbox/                    # Notes and residue
│   ├── prompts/daily-log.md
│   ├── prompts/weekly-hype.md
│   ├── outputs/
│   └── schedule/                 # Cron examples
└── 03-offline-communications-triage/  # Exported mail → action list
    ├── eml/                      # Email files
    ├── prompts/email-triage.md
    ├── outputs/
    └── expected/report-outline.md
```

## Installation

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the walkthrough
open webinar-runbook.html
```

No package installation required — this is a prompt-based workflow.

## Exercise 1: Data Intake Review

**Goal**: Turn unknown files into a trustworthy intake report.

### Workflow

1. Place files to review in `code-along/01-data-intake-review/incoming/`
2. Read the prompt template at `code-along/01-data-intake-review/prompts/intake-review.md`
3. Pass the prompt + files to your AI agent (Claude, GPT-4, etc.)
4. Save output to `code-along/01-data-intake-review/outputs/intake-review.md`

### Example Prompt Pattern

```markdown
# Data Intake Review

Review all files in the incoming/ directory and create a structured report.

## Required Sections

1. **Summary**: High-level overview of what was found
2. **File Inventory**: List each file with type and size
3. **Content Analysis**: Key findings from each file
4. **Risk Assessment**: Security or privacy concerns
5. **Recommended Actions**: What to do with each file

## Output Format

Markdown with clear headings and bullet points.
```

### Example Agent Command

```bash
# Using Claude Code or similar
claude "Read code-along/01-data-intake-review/prompts/intake-review.md and all files in code-along/01-data-intake-review/incoming/, then generate intake-review.md in outputs/"
```

## Exercise 2: Operational Memory

**Goal**: Turn work residue into daily logs and weekly summaries.

### Daily Log Workflow

```markdown
# Daily Log Prompt Structure

Review inbox/ folder contents from today and create a daily log.

## Sections

1. **Date**: ISO format (YYYY-MM-DD)
2. **Completed**: What got done
3. **In Progress**: Active work
4. **Blocked**: Issues preventing progress
5. **Notes**: Quick observations
6. **Tomorrow**: Planned focus

Output: `outputs/daily-log-YYYY-MM-DD.md`
```

### Weekly Summary Workflow

```markdown
# Weekly Hype Prompt Structure

Review all daily logs from the past week and create a weekly summary.

## Sections

1. **Week Of**: Date range
2. **Highlights**: Top 3-5 accomplishments
3. **Momentum**: Themes and patterns
4. **Challenges**: Recurring blockers
5. **Next Week**: Forward-looking priorities

Output: `outputs/weekly-hype-YYYY-WW.md`
```

### Automation Example

```bash
# Add to crontab for daily execution
# Run at 6 PM daily
0 18 * * * cd /path/to/code-along/02-operational-memory && /usr/local/bin/ai-agent "$(cat prompts/daily-log.md)" > outputs/daily-log-$(date +\%Y-\%m-\%d).md

# Run weekly summary on Friday at 5 PM
0 17 * * 5 cd /path/to/code-along/02-operational-memory && /usr/local/bin/ai-agent "$(cat prompts/weekly-hype.md)" > outputs/weekly-hype-$(date +\%Y-W\%V).md
```

### Heartbeat Pattern

Create a recurring note to trigger memory updates:

```markdown
<!-- code-along/02-operational-memory/inbox/HEARTBEAT.md -->

# Daily Heartbeat - {{DATE}}

## What happened today?

- 

## What's top of mind?

- 

## Any blockers?

- 

---
This file is processed daily by the operational memory system.
```

## Exercise 3: Offline Communications Triage

**Goal**: Turn exported email files into an action list.

### Workflow

1. Export emails to `.eml` format and save to `code-along/03-offline-communications-triage/eml/`
2. Use the triage prompt at `code-along/03-offline-communications-triage/prompts/email-triage.md`
3. Generate action list at `outputs/email-triage.md`

### Example Triage Prompt

```markdown
# Email Triage

Analyze all .eml files in the eml/ directory.

## Classification

For each email, determine:

1. **Priority**: High / Medium / Low
2. **Action Required**: Response / Review / Delegate / Archive
3. **Time Estimate**: How long to handle
4. **Deadline**: If any mentioned

## Output Format

### Summary
- Total emails: X
- High priority: Y
- Action required: Z

### High Priority Actions
- [ ] [From] Subject - Action needed (deadline)

### Medium Priority
- [ ] [From] Subject - Action needed

### Low Priority / FYI
- [From] Subject - Note

### Archive/No Action
- [From] Subject
```

### Example Agent Command

```bash
# Process all emails in eml/ folder
ai-agent "$(cat code-along/03-offline-communications-triage/prompts/email-triage.md)" \
  --context "code-along/03-offline-communications-triage/eml/*.eml" \
  > code-along/03-offline-communications-triage/outputs/email-triage-$(date +%Y-%m-%d).md
```

## Configuration Patterns

### Environment Variables

```bash
# ~/.zshrc or ~/.bashrc

export OPENCLAW_WORKSPACE="$HOME/openclaw-assistant"
export OPENCLAW_DAILY_TIME="18:00"
export OPENCLAW_WEEKLY_DAY="Friday"
export AI_AGENT_CMD="claude"  # or "cursor", "codex", etc.
```

### Folder Structure Convention

```text
$OPENCLAW_WORKSPACE/
├── data-intake/
│   ├── incoming/
│   ├── prompts/
│   └── outputs/
├── operational-memory/
│   ├── inbox/
│   ├── daily-logs/
│   ├── weekly-summaries/
│   └── prompts/
└── communications/
    ├── eml/
    ├── outputs/
    └── prompts/
```

## Common Patterns

### Pattern: Batch Processing

```bash
#!/bin/bash
# batch-intake.sh

INTAKE_DIR="code-along/01-data-intake-review"

for file in "$INTAKE_DIR/incoming"/*; do
  echo "Processing: $file"
  ai-agent "Review $file and append findings to $INTAKE_DIR/outputs/batch-report.md"
done
```

### Pattern: Template Reuse

```bash
# Save your best prompts as templates
mkdir -p ~/.openclaw-templates

cp code-along/02-operational-memory/prompts/daily-log.md \
   ~/.openclaw-templates/daily-log-template.md

# Use them anywhere
ai-agent "$(cat ~/.openclaw-templates/daily-log-template.md)"
```

### Pattern: Incremental Updates

```markdown
<!-- outputs/daily-log.md -->

# Daily Log

## 2026-05-15
- Completed project review
- Started new feature

## 2026-05-14
- Team meeting
- Code review

---

**Prompt for updates**: Append today's work to this log following the date format.
```

## Troubleshooting

### Issue: Files Not Found

```bash
# Verify paths
ls -la code-along/01-data-intake-review/incoming/

# Use absolute paths
AI_WORKSPACE="$(pwd)/code-along"
ai-agent --context "$AI_WORKSPACE/01-data-intake-review/incoming/*"
```

### Issue: Prompt Not Working

```bash
# Test prompt syntax
cat code-along/02-operational-memory/prompts/daily-log.md | head -20

# Verify file encoding
file code-along/02-operational-memory/prompts/daily-log.md
```

### Issue: Cron Job Fails

```bash
# Test cron command manually
cd /path/to/code-along/02-operational-memory
/usr/local/bin/ai-agent "$(cat prompts/daily-log.md)"

# Check cron logs
grep CRON /var/log/syslog  # Linux
log show --predicate 'process == "cron"' --last 1h  # macOS
```

### Issue: Output Quality Poor

1. **Add examples to prompts**: Show desired format
2. **Increase context**: Include more reference files
3. **Iterate prompt wording**: Be more specific about structure
4. **Use templates**: Start with expected outline in prompt

## Integration Example

```bash
#!/bin/bash
# run-daily-workflow.sh

set -e

WORKSPACE="$HOME/openclaw-assistant"
DATE=$(date +%Y-%m-%d)

echo "Starting daily workflow for $DATE"

# 1. Process any incoming data
if [ -n "$(ls -A $WORKSPACE/data-intake/incoming/ 2>/dev/null)" ]; then
  echo "Processing incoming files..."
  ai-agent "$(cat $WORKSPACE/data-intake/prompts/intake-review.md)" \
    > "$WORKSPACE/data-intake/outputs/intake-$DATE.md"
fi

# 2. Generate daily log
echo "Generating daily log..."
ai-agent "$(cat $WORKSPACE/operational-memory/prompts/daily-log.md)" \
  > "$WORKSPACE/operational-memory/daily-logs/log-$DATE.md"

# 3. Triage communications if any
if [ -n "$(ls -A $WORKSPACE/communications/eml/ 2>/dev/null)" ]; then
  echo "Triaging communications..."
  ai-agent "$(cat $WORKSPACE/communications/prompts/email-triage.md)" \
    > "$WORKSPACE/communications/outputs/triage-$DATE.md"
fi

echo "Daily workflow complete. Outputs in $WORKSPACE"
```

## Best Practices

1. **Review outputs manually** — AI assists, you decide
2. **Keep prompts in version control** — Track what works
3. **Start simple** — One workflow at a time
4. **Use consistent naming** — YYYY-MM-DD for dates
5. **Archive regularly** — Move old outputs to archive/ folders
6. **Iterate prompts** — Refine based on output quality
7. **Local first** — No API keys, no cloud dependencies

## Next Steps

After mastering these three exercises:

1. Customize prompts for your workflow
2. Add new exercise folders for different use cases
3. Build a simple dashboard in `mission-control/`
4. Schedule automations with cron
5. Create prompt libraries for repeated tasks

---

This workshop teaches OpenClaw patterns, but the techniques work with any AI agent that can read local files and follow markdown prompts.
