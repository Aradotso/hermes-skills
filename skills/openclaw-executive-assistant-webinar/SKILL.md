---
name: openclaw-executive-assistant-webinar
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communication triage
triggers:
  - how do I use OpenClaw for executive assistant tasks
  - set up local-first AI workflow with OpenClaw
  - create intake reports with OpenClaw
  - build operational memory system
  - triage emails with OpenClaw
  - automate daily logs and weekly summaries
  - use OpenClaw for communications management
  - create markdown artifacts with OpenClaw
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill covers building local-first AI executive assistant workflows using OpenClaw. The project provides a workshop structure for three core workflows: data intake review, operational memory management, and offline communications triage. All operations produce reviewable markdown artifacts without cloud integrations.

## What This Project Does

OpenClaw Executive Assistant is a workshop/starter kit for building AI-powered personal assistant workflows that:

- Review and categorize incoming files into structured intake reports
- Transform work notes into daily logs and weekly summaries
- Triage exported emails into actionable task lists
- Keep all data local with markdown outputs
- Use copy/paste prompts (no API integrations required during workshop)

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
│   ├── inbox/             # Work residue & notes
│   ├── prompts/daily-log.md
│   ├── prompts/weekly-hype.md
│   ├── outputs/
│   └── schedule/
├── 03-offline-communications-triage/
│   ├── eml/               # Exported emails
│   ├── prompts/email-triage.md
│   ├── outputs/
│   └── expected/report-outline.md
└── mission-control/       # Optional dashboard
```

## Installation & Setup

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the main walkthrough
open webinar-runbook.html

# Navigate to code-along directory
cd code-along
```

No additional dependencies required for the basic workshop — it uses copy/paste workflows with your preferred AI assistant.

## Exercise 1: Data Intake Review

Turn unknown files into trustworthy intake reports.

### Structure

```
01-data-intake-review/
├── incoming/              # Place files here
├── prompts/intake-review.md
└── outputs/intake-review.md
```

### Workflow

1. Add files to `incoming/` folder
2. Read the prompt from `prompts/intake-review.md`
3. Point your AI assistant to scan `incoming/`
4. Generate `outputs/intake-review.md`

### Example Prompt Pattern

```markdown
# Data Intake Review

Review all files in the incoming/ directory and create a structured report:

## Files Discovered
- List each file with name, type, size

## Content Summary
- Brief description of each file's contents

## Priority Assessment
- HIGH / MEDIUM / LOW classification
- Reasoning for each

## Recommended Actions
- What should be done with each file

Output to: outputs/intake-review.md
```

### Expected Output Structure

```markdown
# Intake Review Report
Generated: 2026-05-15

## Summary
- Total files: 5
- High priority: 2
- Requires action: 3

## Files Analyzed

### 1. contract-draft.pdf
- **Type**: PDF Document
- **Size**: 245 KB
- **Priority**: HIGH
- **Summary**: Q2 vendor contract requiring signature
- **Action**: Review legal terms, route to Finance

### 2. meeting-notes.txt
- **Type**: Text file
- **Size**: 8 KB
- **Priority**: MEDIUM
- **Summary**: Project kickoff notes from 2026-05-10
- **Action**: File in project folder, extract action items
```

## Exercise 2: Operational Memory

Transform daily work residue into momentum documents.

### Structure

```
02-operational-memory/
├── inbox/                 # Work notes, snippets
├── prompts/
│   ├── daily-log.md
│   └── weekly-hype.md
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

Review all notes in inbox/ from today and create a daily log:

## What Got Done
- Completed tasks and achievements

## What's In Progress
- Active work items

## Blockers & Questions
- Issues needing attention

## Tomorrow's Focus
- Top 3 priorities

Output to: outputs/daily-log.md
```

### Weekly Summary Workflow

```markdown
# Weekly Hype Prompt

Review the last 7 daily logs and create a weekly summary:

## Week Highlights
- Major accomplishments
- Key decisions made

## Momentum Builders
- What's working well
- Positive trends

## Next Week Priorities
- Top goals
- Key meetings/deadlines

Output to: outputs/weekly-hype.md
```

### Automation Pattern (Optional)

```bash
# Cron example for daily 5pm log generation
0 17 * * * cd ~/openclaw-assistant/code-along/02-operational-memory && ./generate-daily-log.sh

# Cron example for weekly Friday summary
0 16 * * 5 cd ~/openclaw-assistant/code-along/02-operational-memory && ./generate-weekly-hype.sh
```

### Example Output: Daily Log

```markdown
# Daily Log — 2026-05-15

## What Got Done ✅
- Reviewed 12 customer support tickets
- Shipped bugfix for login timeout issue
- Completed draft of Q2 roadmap presentation

## In Progress 🔄
- Database migration planning (60% complete)
- Hiring pipeline: 3 candidates in final round
- API documentation updates

## Blockers 🚧
- Waiting on legal approval for vendor contract
- Need design mockups for dashboard refresh

## Tomorrow's Focus 🎯
1. Present Q2 roadmap to leadership
2. Interview final engineering candidates
3. Finalize database migration plan
```

## Exercise 3: Offline Communications Triage

Turn exported emails into action lists.

### Structure

```
03-offline-communications-triage/
├── eml/                   # Exported .eml files
├── prompts/email-triage.md
├── outputs/email-triage.md
└── expected/report-outline.md
```

### Workflow

1. Export emails as `.eml` files to `eml/` folder
2. Use the triage prompt
3. Generate `outputs/email-triage.md`

### Example Prompt

```markdown
# Email Triage Prompt

Analyze all .eml files and create a triage report:

## Urgent Action Required
- Emails needing response within 24h
- Include sender, subject, deadline

## Follow-up This Week
- Items requiring response within 7 days

## FYI / Archive
- Informational emails

## Delegation Candidates
- Emails that could be handled by others

For each email include:
- Sender
- Subject
- Received date
- Recommended action
- Suggested response points (if applicable)

Output to: outputs/email-triage.md
```

### Example Output

```markdown
# Email Triage Report
Generated: 2026-05-15 09:30 AM
Total emails analyzed: 23

## 🚨 Urgent Action Required (3)

### 1. Budget Approval Needed
- **From**: Sarah Chen (Finance)
- **Subject**: Q2 Marketing Budget - Approval Due Today
- **Received**: 2026-05-15 08:15 AM
- **Deadline**: EOD Today
- **Action**: Review attached spreadsheet, approve or request changes
- **Response points**:
  - Confirm line items
  - Question the $15K conference allocation
  - Approve with modifications

### 2. Client Escalation
- **From**: Mike Johnson (Sales)
- **Subject**: URGENT: ABC Corp Implementation Issue
- **Received**: 2026-05-14 04:45 PM
- **Deadline**: Before their 10am meeting tomorrow
- **Action**: Coordinate with engineering team for technical solution
- **Response points**:
  - Acknowledge issue
  - Provide timeline for fix
  - Offer interim workaround

## 📅 Follow-up This Week (8)

### 1. Conference Speaking Opportunity
- **From**: Events Team
- **Subject**: Invite to speak at DevCon 2026
- **Received**: 2026-05-13
- **Action**: Decide by Friday, coordinate with marketing
- **Response points**:
  - Check calendar availability (Oct 15-17)
  - Confirm travel budget
  - Propose topic if accepting

## 📰 FYI / Archive (10)

### 1. Newsletter: Industry Trends
- **From**: TechWeekly
- **Subject**: Top 10 AI Developments This Week
- **Action**: Skim for relevant insights, archive

## 👥 Delegation Candidates (2)

### 1. Meeting Room Booking
- **From**: Office Manager
- **Subject**: Q3 Planning Session Venue Options
- **Delegate to**: Executive Assistant
- **Action**: Review options and book preferred venue
```

## Common Patterns

### Local-First Workflow

```bash
# Standard flow for any exercise
cd code-along/01-data-intake-review

# 1. Add source files
cp ~/Downloads/new-files/* incoming/

# 2. Copy prompt to AI assistant
cat prompts/intake-review.md | pbcopy

# 3. Point AI to the directory context
# "Review files in code-along/01-data-intake-review/incoming/"

# 4. Save output
# AI generates markdown → save to outputs/intake-review.md
```

### Prompt Composition

All prompts follow this structure:
1. **Context**: What to analyze (folder, date range)
2. **Output format**: Markdown structure expected
3. **Categorization**: How to group items
4. **Action orientation**: Next steps for each item
5. **Output path**: Where to save results

### Mission Control Dashboard

Create a simple index linking to all generated artifacts:

```markdown
# Mission Control

## Today's Status
- [Daily Log](02-operational-memory/outputs/daily-log.md)
- [Intake Review](01-data-intake-review/outputs/intake-review.md)
- [Email Triage](03-offline-communications-triage/outputs/email-triage.md)

## This Week
- [Weekly Hype](02-operational-memory/outputs/weekly-hype.md)

## Archives
- [Previous Daily Logs](02-operational-memory/outputs/archive/)
- [Past Intake Reviews](01-data-intake-review/outputs/archive/)
```

## Configuration

### Environment Variables

If automating with scripts:

```bash
# .env file
OPENCLAW_BASE_DIR="${HOME}/openclaw-assistant/code-along"
OPENCLAW_ARCHIVE_DIR="${HOME}/openclaw-assistant/archive"
OPENCLAW_DAILY_LOG_TIME="17:00"
```

### Custom Prompts

Modify prompts in each exercise's `prompts/` directory to match your workflow:

- Add custom categorization rules
- Change priority levels
- Adjust output format
- Add domain-specific sections

## Troubleshooting

### Files Not Being Analyzed

**Issue**: AI assistant doesn't see files in incoming/

**Solution**:
- Explicitly reference the full path
- Use `ls` or `tree` to show directory contents first
- Some AI assistants need file reading permissions confirmed

### Output Format Inconsistent

**Issue**: Generated markdown doesn't match expected structure

**Solution**:
- Include the `expected/report-outline.md` in your prompt
- Be explicit: "Match this exact format:"
- Ask AI to validate structure before final output

### Automation Not Triggering

**Issue**: Cron jobs not running

**Solution**:
```bash
# Check cron is running
sudo service cron status

# Verify cron syntax
crontab -l

# Check script permissions
chmod +x generate-daily-log.sh

# Test script manually
./generate-daily-log.sh
```

### Large Email Batches

**Issue**: Too many emails to process at once

**Solution**:
- Process in batches of 20-30 emails
- Prioritize by date range
- Use separate folders for different time periods

## Best Practices

1. **Review Before Archiving**: Always review AI-generated outputs before filing
2. **Consistent Naming**: Use ISO date format in filenames (`2026-05-15-daily-log.md`)
3. **Regular Cleanup**: Archive old outputs monthly
4. **Version Prompts**: Track prompt changes in git for reproducibility
5. **Privacy First**: Never include sensitive data in prompts sent to cloud AI services

## Integration Ideas

While the workshop uses copy/paste workflows, you can extend with:

- File watchers to auto-trigger processing
- Git hooks to version outputs
- Local LLM integration (Ollama, LM Studio)
- HTML dashboard generation from markdown
- Calendar integration for scheduling
