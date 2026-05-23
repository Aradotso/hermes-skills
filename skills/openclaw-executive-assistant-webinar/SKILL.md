---
name: openclaw-executive-assistant-webinar
description: Build local-first AI executive assistant workflows using OpenClaw for data intake, operational memory, and communications triage
triggers:
  - how do I build an executive assistant with OpenClaw
  - set up local AI assistant workflow
  - create data intake review with OpenClaw
  - build operational memory system
  - triage emails offline with AI
  - set up daily log and weekly summary automation
  - review incoming files with AI locally
  - create local-first AI assistant
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## What This Project Does

This is a workshop repository teaching you to build a local-first AI executive assistant using OpenClaw. It demonstrates three core workflows that keep everything in local folders and produce reviewable markdown artifacts:

1. **Data intake review** - Turn unknown files into trustworthy intake reports
2. **Operational memory** - Turn work residue into daily and weekly momentum docs
3. **Offline communications triage** - Turn exported mail into action lists

All exercises use copy/paste prompts with no live integrations, making this ideal for learning local-first AI workflows.

## Repository Structure

```
.
├── webinar-runbook.html              # Main walkthrough
└── code-along/
    ├── INDEX.md
    ├── 01-data-intake-review/        # Exercise 1
    │   ├── incoming/                 # Files to inspect
    │   ├── prompts/intake-review.md  # Prompt template
    │   ├── outputs/                  # Generated reports
    │   └── expected/                 # Expected output shape
    ├── 02-operational-memory/        # Exercise 2
    │   ├── inbox/                    # Work notes
    │   ├── prompts/
    │   │   ├── daily-log.md
    │   │   └── weekly-hype.md
    │   ├── outputs/
    │   └── schedule/                 # Cron examples
    ├── 03-offline-communications-triage/  # Exercise 3
    │   ├── eml/                      # Email exports
    │   ├── prompts/email-triage.md
    │   ├── outputs/
    │   └── expected/
    └── mission-control/              # Optional dashboard
```

## Installation

Clone the repository:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

No dependencies required - this is a file-based workshop focused on prompt patterns.

## Workshop Flow

Open `webinar-runbook.html` in your browser and follow along with the exercises in the `code-along/` folder.

## Exercise 1: Data Intake Review

**Goal:** Transform unknown files into structured intake reports.

**Location:** `code-along/01-data-intake-review/`

### Workflow

1. Place files to review in `incoming/`
2. Use the prompt from `prompts/intake-review.md`
3. Generate report to `outputs/intake-review.md`

### Prompt Pattern

The intake review prompt should:
- List all files found
- Identify file types and purposes
- Flag security concerns
- Suggest next actions
- Prioritize by urgency

### Expected Output Structure

```markdown
# Data Intake Review - [DATE]

## Summary
- Total files: X
- Flagged for review: Y
- Ready to process: Z

## File Inventory

### High Priority
- **filename.ext** - [description] - ACTION REQUIRED

### Medium Priority
- **filename.ext** - [description] - Review recommended

### Low Priority / Archive
- **filename.ext** - [description] - No action needed

## Security Flags
[Any concerns]

## Recommended Next Steps
1. [Action item]
2. [Action item]
```

## Exercise 2: Operational Memory

**Goal:** Convert work residue into daily logs and weekly summaries.

**Location:** `code-along/02-operational-memory/`

### Workflow for Daily Log

1. Drop notes/todos/fragments in `inbox/`
2. Use prompt from `prompts/daily-log.md`
3. Generate `outputs/daily-log.md`

### Daily Log Prompt Pattern

```markdown
Review all files in the inbox/ folder and create a daily log that:

- Extracts completed tasks
- Lists pending items
- Captures important decisions
- Notes blockers or questions
- Suggests tomorrow's priorities

Format as markdown with clear sections.
```

### Expected Daily Log Output

```markdown
# Daily Log - [DATE]

## Completed Today
- [Task with context]
- [Task with context]

## In Progress
- [Item with status]

## Decisions Made
- [Decision and rationale]

## Blockers / Questions
- [Issue to resolve]

## Tomorrow's Focus
1. [Priority item]
2. [Priority item]
```

### Workflow for Weekly Summary

1. Collect daily logs from the week
2. Use prompt from `prompts/weekly-hype.md`
3. Generate `outputs/weekly-hype.md`

### Weekly Summary Prompt Pattern

```markdown
Review all daily logs from this week and create a weekly summary that:

- Highlights major accomplishments
- Shows progress on key initiatives
- Captures lessons learned
- Identifies patterns or themes
- Celebrates wins

Use an encouraging, momentum-building tone.
```

### Automation with Cron

See `schedule/cron-examples.md` for automation patterns:

```bash
# Daily log at end of workday (5 PM weekdays)
0 17 * * 1-5 /path/to/generate-daily-log.sh

# Weekly summary (Friday 6 PM)
0 18 * * 5 /path/to/generate-weekly-hype.sh
```

See `schedule/heartbeat-note.md` for guidance on regular check-ins.

## Exercise 3: Offline Communications Triage

**Goal:** Process exported emails into actionable lists.

**Location:** `code-along/03-offline-communications-triage/`

### Workflow

1. Export emails as `.eml` files to `eml/`
2. Use prompt from `prompts/email-triage.md`
3. Generate `outputs/email-triage.md`

### Email Triage Prompt Pattern

```markdown
Review all .eml files and create a triage report that:

- Categorizes by urgency (urgent/soon/later/archive)
- Extracts action items with context
- Identifies quick wins (< 5 min responses)
- Flags emails needing deep work
- Suggests draft replies where appropriate

Preserve sender info and subject for context.
```

### Expected Triage Output

```markdown
# Email Triage Report - [DATE]

## Quick Wins (< 5 min)
- **From:** sender@example.com | **Subject:** Quick question
  - **Action:** Reply with answer to [question]

## Urgent (Today)
- **From:** sender@example.com | **Subject:** Meeting conflict
  - **Action:** Reschedule Tuesday meeting, propose alternatives

## This Week
- **From:** sender@example.com | **Subject:** Project proposal
  - **Action:** Review attached document, provide feedback by Friday

## Archive / FYI
- **From:** sender@example.com | **Subject:** Newsletter
  - **Action:** None, archive
```

## Common Patterns

### Pattern: Prompt + Review + Iterate

All exercises follow this pattern:

```bash
# 1. Prepare inputs
ls incoming/  # or inbox/, eml/

# 2. Copy prompt from prompts/*.md

# 3. Feed to your AI tool (Claude, ChatGPT, Cursor, etc.)

# 4. Save output to outputs/*.md

# 5. Review and refine prompt if needed
```

### Pattern: Local-First Security

- All processing happens on your machine
- No data leaves your filesystem
- Review outputs before sharing
- Version control with git

### Pattern: Markdown Artifacts

All outputs are markdown for:
- Easy version control
- Plain text searchability
- Tool interoperability
- Human readability

## Configuration

No configuration files needed. Customize by:

1. **Editing prompts** in `prompts/*.md` to match your workflow
2. **Adjusting folder structure** to fit your filing system
3. **Modifying output templates** in expected/ folders

## Troubleshooting

**No outputs generated?**
- Ensure you're copying the full prompt from `prompts/*.md`
- Check that input folders contain files
- Verify your AI tool has access to the folder context

**Output format doesn't match expected?**
- Reference `expected/*.md` files for desired structure
- Add explicit formatting instructions to prompts
- Include example output in your prompt

**Automation not working?**
- Check cron syntax in `schedule/cron-examples.md`
- Verify script paths and permissions
- Test scripts manually before scheduling

**Want to customize for your workflow?**
- Fork the repo
- Modify prompt templates
- Adjust folder structure
- Add your own exercises

## Mission Control Dashboard

Optional: `code-along/mission-control/` is reserved for a simple dashboard linking to all generated artifacts. You can build:

- An HTML index of recent reports
- A script that aggregates all outputs
- A simple web view of your assistant's work

## Best Practices

1. **Start small** - Complete exercises in order
2. **Review outputs** - AI makes mistakes, always verify
3. **Iterate prompts** - Refine based on actual outputs
4. **Version control** - Git commit outputs for history
5. **Schedule wisely** - Start with manual runs before automating

## Additional Resources

- Main webinar: https://www.datacamp.com/webinars/build-your-own-executive-assistant-with-openclaw
- OpenClaw documentation for deeper integration patterns
- Local-first AI workflows for privacy-conscious automation
