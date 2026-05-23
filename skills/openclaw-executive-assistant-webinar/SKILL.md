---
name: openclaw-executive-assistant-webinar
description: Build local-first OpenClaw executive assistant workflows for data intake, operational memory, and communications triage
triggers:
  - how do I build an executive assistant with OpenClaw
  - set up local OpenClaw workflow for data intake
  - create operational memory system with OpenClaw
  - triage emails offline with OpenClaw
  - build local-first AI assistant workflows
  - implement OpenClaw for daily logs and weekly summaries
  - create markdown-based executive assistant system
  - use OpenClaw for communications triage
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill covers building local-first executive assistant workflows using OpenClaw, based on the DataCamp webinar materials. The project teaches three core patterns: data intake review, operational memory management, and offline communications triage—all using local files and markdown outputs.

## What This Project Does

This is a workshop/tutorial project that demonstrates how to build an AI-powered executive assistant that:

- **Reviews incoming data** and generates structured intake reports
- **Maintains operational memory** through daily logs and weekly summaries
- **Triages communications** by analyzing exported emails and creating action lists

All operations are local-only with no live integrations, producing reviewable markdown artifacts.

## Project Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/           # Prompt templates
│   ├── outputs/           # Generated reports
│   └── expected/          # Reference outputs
├── 02-operational-memory/
│   ├── inbox/             # Work notes and residue
│   ├── prompts/           # Daily/weekly prompts
│   ├── outputs/           # Generated logs
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/           # Triage prompts
│   ├── outputs/           # Generated action lists
│   └── expected/          # Reference outputs
└── mission-control/       # Optional dashboard
```

## Installation & Setup

Clone the repository:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

No dependencies required—this is a workshop using copy/paste prompts with your preferred AI assistant (Claude, ChatGPT, etc.).

## Workflow Patterns

### 1. Data Intake Review

Turn unknown files into trustworthy intake reports.

**Location**: `code-along/01-data-intake-review/`

**Process**:
1. Place files to review in `incoming/`
2. Use the prompt from `prompts/intake-review.md`
3. Generate structured report to `outputs/intake-review.md`

**Prompt Template Pattern**:
```markdown
# Data Intake Review Prompt

Review the files in the incoming/ directory and create a structured report:

## Files Reviewed
- List each file with name, type, and size

## Content Summary
- Brief description of what each file contains

## Recommended Actions
- What should be done with each file
- Priority level (high/medium/low)
- Suggested categorization

## Potential Issues
- Any concerns or red flags
- Missing information
- Files that need clarification

Output as: intake-review.md
```

**Expected Output**: `outputs/intake-review.md`

### 2. Operational Memory

Turn work residue into momentum documents.

**Location**: `code-along/02-operational-memory/`

**Daily Log Pattern**:
```markdown
# Daily Log Prompt

Review today's work artifacts in inbox/ and create a daily log:

## Date
[Current date]

## Completed
- What got done today
- Key decisions made
- Problems solved

## In Progress
- What's actively being worked on
- Current blockers

## Tomorrow's Focus
- Top 3 priorities
- Scheduled commitments

## Notes & Context
- Useful context for future reference
- Links to related work

Output as: outputs/daily-log.md
```

**Weekly Summary Pattern**:
```markdown
# Weekly Hype Prompt

Review the past week's daily logs and create a weekly summary:

## Week of [Date Range]

## Highlights
- Major wins and completions
- Key milestones reached

## Momentum
- Themes and patterns
- What's building energy

## Challenges
- Recurring issues
- What needs attention

## Next Week Preview
- Upcoming priorities
- Scheduled events

Output as: outputs/weekly-hype.md
```

**Automation Example** (`schedule/cron-examples.md`):
```bash
# Daily log generation (9 PM every day)
0 21 * * * cd /path/to/project/02-operational-memory && ./generate-daily-log.sh

# Weekly summary (Friday 5 PM)
0 17 * * 5 cd /path/to/project/02-operational-memory && ./generate-weekly-hype.sh
```

### 3. Offline Communications Triage

Turn exported emails into action lists.

**Location**: `code-along/03-offline-communications-triage/`

**Process**:
1. Export emails as `.eml` files to `eml/` directory
2. Use prompt from `prompts/email-triage.md`
3. Generate action list to `outputs/email-triage.md`

**Triage Prompt Pattern**:
```markdown
# Email Triage Prompt

Analyze the emails in eml/ and create a triage report:

## High Priority
- Sender
- Subject
- Why it's urgent
- Recommended action
- Deadline (if any)

## Medium Priority
- Same structure as above

## Low Priority / FYI
- Brief list

## Can Archive
- Emails that need no action

## Waiting For
- Emails expecting a response from others

## Draft Responses Needed
- Which emails need replies
- Suggested tone/approach

Output as: outputs/email-triage.md
```

**Expected Output**: `outputs/email-triage.md`

## Key Configuration Patterns

### Prompt Structure

All prompts follow a consistent pattern:
```markdown
# [Task Name] Prompt

[Context and instructions]

## Section 1
[What to include]

## Section 2
[What to include]

Output as: [filename]
```

### File Organization

```
exercise/
├── incoming/ or inbox/ or eml/    # Input files
├── prompts/                        # Reusable prompt templates
├── outputs/                        # Generated artifacts
└── expected/                       # Reference examples
```

## Real Working Example

**Complete Data Intake Workflow**:

```markdown
# Example: Reviewing Project Files

Given files in incoming/:
- proposal.pdf
- contract-draft.docx
- meeting-notes.txt
- budget-v2.xlsx

Prompt:
"Review the files in code-along/01-data-intake-review/incoming/ 
and create an intake report following the structure in 
prompts/intake-review.md"

Generated output/intake-review.md:

---
# Intake Review Report
Generated: 2026-05-11

## Files Reviewed

### proposal.pdf
- Type: PDF Document
- Size: 2.3 MB
- Status: Complete

### contract-draft.docx
- Type: Word Document
- Size: 156 KB
- Status: Needs review

### meeting-notes.txt
- Type: Plain text
- Size: 8 KB
- Status: Complete

### budget-v2.xlsx
- Type: Excel Spreadsheet
- Size: 89 KB
- Status: Complete

## Content Summary

**proposal.pdf**: Q2 marketing proposal with budget breakdown
**contract-draft.docx**: Service agreement, sections 3-7 highlighted for review
**meeting-notes.txt**: Stakeholder sync from 5/10, action items listed
**budget-v2.xlsx**: Updated budget with scenario modeling

## Recommended Actions

**HIGH**: contract-draft.docx
- Review highlighted sections
- Legal review required
- Deadline: 5/15

**MEDIUM**: proposal.pdf
- Share with marketing team
- Schedule review meeting

**LOW**: meeting-notes.txt
- Archive with project docs
- Update task tracker

**LOW**: budget-v2.xlsx
- Finalize and send to finance

## Potential Issues

- Contract sections 3-7 need stakeholder sign-off
- Budget scenarios need validation
- Meeting notes reference missing attachment
---
```

## Common Patterns

### Morning Review Workflow
```bash
# 1. Generate yesterday's daily log
# 2. Review intake folder for new files
# 3. Check email triage from overnight exports
# 4. Plan today based on all three artifacts
```

### Weekly Shutdown Workflow
```bash
# 1. Generate Friday's daily log
# 2. Generate weekly summary
# 3. Review all pending items
# 4. Set priorities for next week
```

### Ad-Hoc File Review
```bash
# 1. Drop files in appropriate incoming/ folder
# 2. Run relevant prompt
# 3. Review generated markdown
# 4. Take action based on recommendations
```

## Troubleshooting

**Issue**: Prompt doesn't generate expected structure
- **Solution**: Check `expected/` folder for reference format, ensure you're including all prompt sections

**Issue**: Generated report lacks detail
- **Solution**: Provide more context in input files, reference previous reports as examples

**Issue**: Daily logs feel repetitive
- **Solution**: Focus on deltas and momentum, not just task lists. Ask "what changed today?"

**Issue**: Email triage misses urgent items
- **Solution**: Export important threads separately, use "urgent" keyword in prompt

**Issue**: Output location unclear
- **Solution**: Always specify full path in prompt: `outputs/[filename].md`

## Best Practices

1. **Consistent naming**: Use ISO date format `YYYY-MM-DD` in filenames
2. **Prompt versioning**: Keep working prompts in `prompts/`, iterate as needed
3. **Regular review**: Check outputs weekly to refine prompts
4. **Context preservation**: Include relevant previous reports when generating new ones
5. **Atomic commits**: If versioning outputs, commit after each generation

## Extending the System

**Add new exercise**:
```bash
mkdir -p code-along/04-new-workflow/{incoming,prompts,outputs,expected}
```

**Create mission control dashboard**:
```markdown
# Mission Control

## Today
- [Daily Log](../02-operational-memory/outputs/daily-log.md)

## This Week
- [Weekly Hype](../02-operational-memory/outputs/weekly-hype.md)

## Inbox
- [Latest Intake](../01-data-intake-review/outputs/intake-review.md)

## Communications
- [Email Triage](../03-offline-communications-triage/outputs/email-triage.md)
```

## Environment Variables

No API keys or external services required. All operations are local-only.

For custom automation scripts:
```bash
export OPENCLAW_WORKSPACE="/path/to/code-along"
export OPENCLAW_OUTPUT_DIR="${OPENCLAW_WORKSPACE}/outputs"
```
