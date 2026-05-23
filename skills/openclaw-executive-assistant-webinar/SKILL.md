---
name: openclaw-executive-assistant-webinar
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - build an executive assistant with openclaw
  - create openclaw workflow for data intake
  - set up operational memory system
  - triage emails with openclaw
  - generate daily logs and weekly summaries
  - process incoming files with ai
  - create local-first assistant workflows
  - use openclaw for task automation
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill covers building local-first AI executive assistant workflows using OpenClaw. The project demonstrates three core patterns: data intake review, operational memory management, and offline communications triage. All workflows stay local, use markdown artifacts, and require no live integrations.

## What This Project Does

This webinar starter provides three practical OpenClaw workflow exercises:

1. **Data Intake Review** - Transform unknown files into structured intake reports
2. **Operational Memory** - Convert work residue into daily logs and weekly summaries
3. **Offline Communications Triage** - Process exported emails into action lists

All exercises use copy/paste prompts with local files and generate reviewable markdown outputs.

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/           # Prompt templates
│   ├── outputs/           # Generated reports
│   └── expected/          # Expected output format
├── 02-operational-memory/
│   ├── inbox/             # Work notes and residue
│   ├── prompts/           # Daily and weekly prompts
│   ├── outputs/           # Generated logs
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/           # Triage prompt
│   ├── outputs/           # Generated triage reports
│   └── expected/          # Expected output format
└── mission-control/       # Optional dashboard
```

## Getting Started

### Prerequisites

- An AI coding agent (Claude Code, Cursor, etc.) or access to OpenClaw/Claude
- Local file system access
- No API keys or live integrations needed

### Installation

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git

# Navigate to the code-along directory
cd webinars-build-your-own-executive-assistant-with-openclaw/code-along
```

### Opening the Walkthrough

Open `webinar-runbook.html` in a browser for the full workshop guide.

## Exercise 1: Data Intake Review

Transform unknown files in `incoming/` into a structured intake report.

### Workflow

1. Navigate to `01-data-intake-review/`
2. Review files in `incoming/` folder
3. Use the prompt from `prompts/intake-review.md`
4. Generate output to `outputs/intake-review.md`

### Example Prompt Pattern

```markdown
# Data Intake Review Prompt

Review all files in the incoming/ folder and create a structured report:

## Files Discovered
- List each file with type and size

## Content Summary
- Summarize what each file contains
- Identify file purpose and category

## Recommended Actions
- Suggest next steps for each file
- Flag any security or quality concerns

## Priority Classification
- High / Medium / Low priority for each item

Output format: Clean markdown with sections above.
```

### Expected Output Structure

The generated `outputs/intake-review.md` should include:

- Inventory of all files
- Content summaries
- Action recommendations
- Priority flags

## Exercise 2: Operational Memory

Convert work residue into momentum documents.

### Workflow

1. Navigate to `02-operational-memory/`
2. Add work notes to `inbox/` folder
3. Use prompts from `prompts/` directory
4. Generate daily and weekly outputs

### Daily Log Generation

```markdown
# Daily Log Prompt

Review all notes in inbox/ from today and create a daily log:

## Completed Today
- What got done

## In Progress
- What's actively being worked on

## Blockers
- What's stuck and why

## Tomorrow's Focus
- Top 3 priorities

## Notes & Links
- Important references or decisions

Output: outputs/daily-log.md with today's date in filename
```

### Weekly Summary Generation

```markdown
# Weekly Hype Prompt

Review the past week of daily logs and create a weekly summary:

## Week Highlights
- Major accomplishments

## Momentum Areas
- What's gaining traction

## Attention Needed
- What needs focus next week

## Metrics & Milestones
- Quantifiable progress

## Team Shoutouts
- Recognition moments

Output: outputs/weekly-hype.md with week date range
```

### Automation with Cron

See `schedule/cron-examples.md` for automated scheduling:

```bash
# Daily log generation at 5 PM
0 17 * * * /path/to/generate-daily-log.sh

# Weekly summary on Friday at 4 PM
0 16 * * 5 /path/to/generate-weekly-hype.sh
```

## Exercise 3: Offline Communications Triage

Process exported emails into actionable task lists.

### Workflow

1. Navigate to `03-offline-communications-triage/`
2. Export emails to `eml/` folder as .eml files
3. Use prompt from `prompts/email-triage.md`
4. Generate output to `outputs/email-triage.md`

### Email Triage Prompt Pattern

```markdown
# Email Triage Prompt

Process all .eml files in the eml/ folder:

## Action Required
- Emails needing response or task
- Include sender, subject, deadline
- Priority level for each

## FYI / Informational
- Emails for awareness only
- Brief summary of key points

## Waiting On
- Emails expecting response from others
- Track who owes what

## Can Archive
- Emails needing no action

## Urgent Flags
- Time-sensitive items
- Critical issues

Output: Clean markdown organized by category above.
```

### Expected Output

The generated `outputs/email-triage.md` provides:

- Categorized email inventory
- Action items with priority
- Response tracking
- Archive candidates

## Common Patterns

### Pattern 1: Prompt Template Library

Store reusable prompts in `prompts/` folders:

```
prompts/
├── intake-review.md
├── daily-log.md
├── weekly-hype.md
└── email-triage.md
```

### Pattern 2: Dated Output Files

Generate outputs with timestamps:

```bash
outputs/
├── daily-log-2026-05-11.md
├── daily-log-2026-05-10.md
├── weekly-hype-2026-week19.md
└── intake-review-2026-05-11.md
```

### Pattern 3: Review Before Action

Always generate to `outputs/` for review before taking action:

1. Generate report
2. Review markdown output
3. Edit if needed
4. Move to action or archive

## Configuration

### Environment Variables

No environment variables needed for basic workflows. For custom automation:

```bash
# Optional: Set custom paths
export OPENCLAW_INBOX="/path/to/inbox"
export OPENCLAW_OUTPUTS="/path/to/outputs"
```

### File Organization

Maintain separation between:

- **Incoming** - Unprocessed files
- **Prompts** - Reusable instructions
- **Outputs** - Generated artifacts
- **Expected** - Reference formats

## Troubleshooting

### Issue: Prompt Not Generating Expected Output

**Solution:** Reference `expected/` folders for correct output structure. Refine prompt to match format.

### Issue: Too Much Manual Copy/Paste

**Solution:** Use automation scripts in `schedule/` folder or create shell wrappers for common workflows.

### Issue: Files Not Being Processed

**Solution:** Ensure files are in correct input folders (`incoming/`, `inbox/`, `eml/`). Check file permissions.

### Issue: Output Quality Varies

**Solution:** Add more specific constraints to prompts. Reference examples from `expected/` folders in your prompt.

## Mission Control Dashboard

The optional `mission-control/` folder can house:

- Index page linking to all generated artifacts
- Status dashboard of recent outputs
- Quick links to exercise folders
- Automation status monitors

## Best Practices

1. **Review Generated Outputs** - Always check markdown artifacts before acting
2. **Iterate on Prompts** - Refine prompts based on output quality
3. **Maintain Separation** - Keep raw inputs separate from generated outputs
4. **Use Version Control** - Track prompt evolution and output patterns
5. **Archive Regularly** - Move processed items out of active folders
6. **Date Everything** - Include timestamps in generated filenames

## Local-First Philosophy

This project emphasizes:

- No cloud dependencies during workshop
- All data stays on local filesystem
- Copy/paste workflows for transparency
- Markdown for universal compatibility
- Reviewable artifacts at every step
