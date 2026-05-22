---
name: openclaw-executive-assistant-webinar
description: Build local-first AI executive assistant workflows using OpenClaw for data intake, operational memory, and communications triage
triggers:
  - how do I build an executive assistant with OpenClaw
  - set up OpenClaw local workflows
  - create data intake review with OpenClaw
  - build operational memory system
  - automate email triage locally
  - OpenClaw webinar code along
  - local-first AI assistant patterns
  - OpenClaw markdown workflow automation
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This project teaches you to build a local-first AI executive assistant using OpenClaw workflows. All processing stays local, uses markdown artifacts, and requires no live integrations. The system handles three core capabilities: data intake review, operational memory management, and offline communications triage.

## What This Project Does

The webinar starter kit demonstrates three OpenClaw workflow patterns:

1. **Data intake review** — Transform unknown files into trustworthy intake reports
2. **Operational memory** — Convert work residue into daily logs and weekly summaries
3. **Offline communications triage** — Process exported emails into actionable task lists

All workflows produce reviewable markdown outputs in local folders.

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/              # Files to inspect
│   ├── prompts/intake-review.md
│   ├── outputs/               # Generated reports
│   └── expected/report-outline.md
├── 02-operational-memory/
│   ├── inbox/                 # Work notes and residue
│   ├── prompts/daily-log.md
│   ├── prompts/weekly-hype.md
│   ├── outputs/
│   ├── schedule/cron-examples.md
│   └── schedule/heartbeat-note.md
├── 03-offline-communications-triage/
│   ├── eml/                   # Exported email files
│   ├── prompts/email-triage.md
│   ├── outputs/
│   └── expected/report-outline.md
└── mission-control/           # Optional dashboard
```

## Installation & Setup

1. Clone the repository:
```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

2. Open the main walkthrough:
```bash
open webinar-runbook.html
```

3. Keep the `code-along/` folder visible while working through exercises.

## Workflow 1: Data Intake Review

Convert unknown files into structured intake reports.

### Process

1. Place files to review in `code-along/01-data-intake-review/incoming/`
2. Read the prompt template at `code-along/01-data-intake-review/prompts/intake-review.md`
3. Use OpenClaw (or any AI assistant) to process files using the prompt
4. Save output to `code-along/01-data-intake-review/outputs/intake-review.md`

### Expected Output Structure

```markdown
# Intake Review Report

## Executive Summary
[High-level overview of discovered files]

## File Inventory
- **filename.ext** — [description, purpose, trustworthiness]

## Recommended Actions
1. [Action items based on findings]

## Risk Assessment
[Security or quality concerns]
```

### Example Prompt Pattern

When using the intake review prompt, provide context:

```
I have [N] files in my incoming folder that need review.
Please analyze them for:
- Content type and purpose
- Trustworthiness indicators
- Recommended next actions
- Any security concerns

Files: [list or attach files]
```

## Workflow 2: Operational Memory

Transform work residue into momentum documentation.

### Daily Log Generation

1. Add work notes to `code-along/02-operational-memory/inbox/`
2. Use prompt at `code-along/02-operational-memory/prompts/daily-log.md`
3. Generate `code-along/02-operational-memory/outputs/daily-log.md`

### Weekly Summary Generation

1. Collect daily logs from the week
2. Use prompt at `code-along/02-operational-memory/prompts/weekly-hype.md`
3. Generate `code-along/02-operational-memory/outputs/weekly-hype.md`

### Automation with Cron

Example cron schedule for daily log generation:

```bash
# Generate daily log at 6 PM every weekday
0 18 * * 1-5 cd /path/to/code-along/02-operational-memory && \
  your-openclaw-command --prompt prompts/daily-log.md --output outputs/daily-log-$(date +\%Y-\%m-\%d).md
```

Example weekly summary (Friday at 5 PM):

```bash
# Generate weekly summary every Friday
0 17 * * 5 cd /path/to/code-along/02-operational-memory && \
  your-openclaw-command --prompt prompts/weekly-hype.md --output outputs/weekly-hype-$(date +\%Y-\%W).md
```

See `code-along/02-operational-memory/schedule/cron-examples.md` for complete examples.

### Expected Daily Log Structure

```markdown
# Daily Log — [Date]

## Completed Today
- [Accomplishment with context]

## In Progress
- [Active work items]

## Blockers
- [Issues requiring attention]

## Tomorrow's Priority
- [Next focus area]
```

## Workflow 3: Offline Communications Triage

Process exported emails into actionable task lists.

### Process

1. Export emails as `.eml` files to `code-along/03-offline-communications-triage/eml/`
2. Read prompt at `code-along/03-offline-communications-triage/prompts/email-triage.md`
3. Process emails using the prompt
4. Save to `code-along/03-offline-communications-triage/outputs/email-triage.md`

### Expected Triage Output Structure

```markdown
# Email Triage Report

## Urgent Action Required
- **Subject**: [email subject]
  - From: [sender]
  - Action: [specific next step]
  - Deadline: [if applicable]

## Follow-up This Week
- **Subject**: [email subject]
  - From: [sender]
  - Action: [specific next step]

## FYI / Archive
- **Subject**: [email subject]
  - Summary: [one-line summary]

## Spam / Ignore
- [Subject lines to ignore]
```

### Example Triage Prompt Pattern

```
Please triage these [N] exported emails:

Prioritize by:
- Time sensitivity
- Sender importance
- Action required vs informational

For each email, provide:
- Priority level (Urgent/This Week/FYI/Ignore)
- Recommended action
- Estimated time to handle

Files: [attach .eml files]
```

## Common Patterns

### Copy-Paste Workflow

All exercises follow this pattern:

1. **Prepare inputs** — Add files to exercise `incoming/`, `inbox/`, or `eml/` folder
2. **Load prompt** — Copy prompt content from `prompts/*.md`
3. **Run through AI** — Paste prompt + reference files into OpenClaw/Claude/GPT
4. **Save output** — Copy result to `outputs/*.md`
5. **Review** — Compare against `expected/*.md` if provided

### Markdown-First Outputs

All generated artifacts use markdown for:
- Easy version control with git
- Human readability without special tools
- Simple automation with text processing tools
- Copy-paste friendly formatting

### Local-Only Processing

This workshop emphasizes:
- No API integrations required
- Files stay on your machine
- Manual copy-paste for learning
- Gradual automation as you understand the pattern

## Mission Control Dashboard (Optional)

The `code-along/mission-control/` folder is reserved for creating a simple HTML dashboard that links to all generated artifacts:

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
      <li><a href="../01-data-intake-review/outputs/intake-review.md">Latest Intake Review</a></li>
      <li><a href="../02-operational-memory/outputs/daily-log.md">Today's Log</a></li>
      <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
    </ul>
  </section>
</body>
</html>
```

## Troubleshooting

### Prompt Not Producing Expected Output

- Verify you're including all context files referenced in the prompt
- Check that input files are in the correct format (not corrupted)
- Compare against `expected/*.md` files to see target structure
- Try breaking complex prompts into smaller steps

### Files Not Processing

- Ensure file paths are correct relative to exercise folder
- Check file permissions if running automated scripts
- Verify `.eml` files are valid RFC 822 format for email triage

### Automation Not Running

- Check cron syntax with `crontab -l`
- Verify full paths to scripts and OpenClaw command
- Check cron logs: `grep CRON /var/log/syslog` (Linux) or `log show --predicate 'process == "cron"'` (macOS)
- Test commands manually before scheduling

### Output Quality Issues

- Add more specific instructions to prompts
- Include examples of desired output format
- Iterate on prompts based on actual outputs
- Reference the `expected/*.md` files as templates

## Environment Variables

If automating with API-based AI services:

```bash
# Set in your shell profile (~/.bashrc, ~/.zshrc)
export OPENAI_API_KEY="your-key-here"
export ANTHROPIC_API_KEY="your-key-here"
```

For local-only workflows, no API keys are needed—use copy-paste with web interfaces.

## Next Steps

After completing the webinar exercises:

1. Customize prompts for your specific workflows
2. Set up cron jobs for operational memory
3. Create your own mission control dashboard
4. Build additional workflows following the same pattern
5. Version control your prompts and outputs with git
