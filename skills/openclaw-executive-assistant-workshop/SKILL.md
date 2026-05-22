---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows using OpenClaw for data intake, operational memory, and communication triage
triggers:
  - "set up OpenClaw executive assistant"
  - "create local AI workflow with OpenClaw"
  - "build data intake review system"
  - "implement operational memory tracking"
  - "set up email triage automation"
  - "configure local-only AI assistant"
  - "create markdown reporting workflow"
  - "build offline communications processor"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## What This Project Does

This is a workshop-style repository that teaches you to build a local-first AI executive assistant using OpenClaw. It provides three practical workflows:

1. **Data Intake Review** - Transform unknown files into structured intake reports
2. **Operational Memory** - Convert work notes into daily logs and weekly summaries
3. **Offline Communications Triage** - Process exported emails into actionable task lists

All workflows operate entirely locally with no external integrations, producing reviewable markdown artifacts.

## Core Philosophy

- **Local files only** - No cloud dependencies
- **No live integrations** - Work with exported/saved data
- **Copy/paste prompts** - Manual LLM interaction
- **Markdown outputs** - Human-readable, version-controllable artifacts

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to review
│   ├── prompts/           # Prompt templates
│   ├── outputs/           # Generated reports
│   └── expected/          # Reference outputs
├── 02-operational-memory/
│   ├── inbox/             # Work notes/residue
│   ├── prompts/           # Daily & weekly prompts
│   ├── outputs/           # Generated logs
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/           # Triage prompts
│   ├── outputs/           # Action lists
│   └── expected/          # Reference outputs
└── mission-control/       # Optional dashboard
```

## Getting Started

### Prerequisites

- Access to an LLM interface (Claude, ChatGPT, local model, etc.)
- Text editor for markdown files
- Terminal for file operations (optional)

### Installation

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the walkthrough
open webinar-runbook.html  # macOS
# or
xdg-open webinar-runbook.html  # Linux
# or just open in browser
```

## Workflow 1: Data Intake Review

### Purpose
Convert unknown/incoming files into a structured intake report.

### Setup

```bash
cd code-along/01-data-intake-review
ls incoming/  # See what files need review
```

### Usage Pattern

1. Read the prompt template:
```bash
cat prompts/intake-review.md
```

2. Customize the prompt with your context:
```markdown
You are reviewing files in the incoming/ folder.
Generate a structured intake report covering:
- File inventory
- Content summaries
- Risk assessment
- Recommended actions
```

3. Provide the prompt + file contents to your LLM

4. Save the output:
```bash
# LLM response goes here
cat > outputs/intake-review.md << 'EOF'
# Data Intake Review - [DATE]

## File Inventory
- file1.pdf (243 KB)
- data.csv (15 KB)

## Content Summaries
...
EOF
```

### Expected Output Structure

```markdown
# Data Intake Review

## Executive Summary
Brief overview of intake batch

## File Inventory
- Filename, size, type for each file

## Content Analysis
- Purpose/topic of each file
- Key information extracted

## Risk Assessment
- Security concerns
- Data sensitivity
- Compliance notes

## Recommended Actions
- Files to archive
- Files requiring immediate attention
- Follow-up tasks
```

## Workflow 2: Operational Memory

### Purpose
Transform scattered work notes into structured daily logs and weekly summaries.

### Setup

```bash
cd code-along/02-operational-memory
ls inbox/  # Review your work residue
```

### Daily Log Pattern

1. Use the daily log prompt:
```bash
cat prompts/daily-log.md
```

2. Feed your inbox notes to the LLM with this structure:
```markdown
Review today's work artifacts in inbox/:
- meeting-notes.md
- quick-tasks.txt
- research-findings.md

Create a daily log with:
- Key accomplishments
- Open threads
- Tomorrow's priorities
```

3. Save to outputs:
```bash
cat > outputs/daily-log.md << 'EOF'
# Daily Log - 2026-05-11

## Accomplished Today
- Completed API integration review
- Drafted Q2 roadmap outline

## Open Threads
- Waiting on security review
- Database migration pending

## Tomorrow's Focus
- Finalize roadmap
- Team sync at 10am
EOF
```

### Weekly Summary Pattern

1. Use the weekly hype prompt:
```bash
cat prompts/weekly-hype.md
```

2. Combine multiple daily logs:
```markdown
Synthesize daily-log-*.md files from the past week.
Create a weekly summary highlighting:
- Major wins
- Momentum areas
- Blockers cleared
- Next week's priorities
```

3. Output example:
```markdown
# Weekly Hype - Week of 2026-05-05

## 🎉 Major Wins
- Shipped v2.0 with zero critical bugs
- Onboarded 3 new team members

## 📈 Momentum
- API adoption up 40%
- Documentation overhaul complete

## 🚧 Blockers Cleared
- Infrastructure bottleneck resolved
- Budget approval secured

## ⏭️ Next Week
- Begin mobile app development
- Q2 planning sprint
```

### Automation Setup

For recurring generation, use cron:

```bash
# Edit crontab
crontab -e

# Daily log at 6 PM weekdays
0 18 * * 1-5 cd ~/code-along/02-operational-memory && ./generate-daily.sh

# Weekly summary Sunday at 8 PM
0 20 * * 0 cd ~/code-along/02-operational-memory && ./generate-weekly.sh
```

Example script (`generate-daily.sh`):
```bash
#!/bin/bash
DATE=$(date +%Y-%m-%d)
PROMPT=$(cat prompts/daily-log.md)

# Use your preferred LLM CLI tool
# llm "$PROMPT" --context inbox/* > "outputs/daily-log-$DATE.md"

# Or trigger a manual review
echo "Daily log due for $DATE"
echo "Run prompt from prompts/daily-log.md"
```

## Workflow 3: Offline Communications Triage

### Purpose
Process exported email files into prioritized action lists.

### Setup

```bash
cd code-along/03-offline-communications-triage
ls eml/  # Review exported emails
```

### Triage Pattern

1. Export emails from your client to `.eml` format:
   - Gmail: Open message → More → Download message
   - Outlook: File → Save As → Outlook Message Format
   - Apple Mail: Message → Save As

2. Place `.eml` files in the `eml/` folder

3. Use the triage prompt:
```bash
cat prompts/email-triage.md
```

4. LLM prompt structure:
```markdown
Review the emails in eml/ folder.
For each message extract:
- Sender & subject
- Priority level (high/medium/low)
- Required action
- Deadline (if any)
- Context/summary

Output a prioritized action list.
```

5. Expected output:
```markdown
# Email Triage - 2026-05-11

## High Priority (Action Required)

### 1. Budget Approval Needed - CFO
- **From:** cfo@company.com
- **Subject:** Q2 Budget Review - Response by Friday
- **Action:** Submit revised budget with justifications
- **Deadline:** 2026-05-13
- **Context:** Need to address 15% cut to engineering budget

### 2. Client Escalation - AccountManager
- **From:** am@company.com
- **Subject:** URGENT: Client X threatening to churn
- **Action:** Schedule call with client stakeholders
- **Deadline:** Within 24 hours
- **Context:** Integration issues causing production delays

## Medium Priority

### 3. Team Feedback Request
- **From:** team-lead@company.com
- **Subject:** Input on hiring plan
- **Action:** Review candidate profiles, provide feedback
- **Deadline:** 2026-05-15
- **Context:** Three positions open, need direction

## Low Priority (FYI)

### 4. Newsletter Digest
- **From:** newsletter@industry.com
- **Subject:** Weekly roundup
- **Action:** Read when time permits
- **Context:** Industry trends summary

## Deferred

- [3 emails moved to backlog]
```

## Configuration Patterns

### Prompt Customization

Each workflow includes a base prompt. Customize for your context:

```markdown
# Base prompt structure
You are an executive assistant specializing in [YOUR DOMAIN].

Context:
- Role: [YOUR ROLE]
- Team: [TEAM INFO]
- Current priorities: [PRIORITIES]

Task:
[SPECIFIC WORKFLOW TASK]

Output format:
[MARKDOWN STRUCTURE]
```

### File Organization

Maintain consistent folder structure:

```bash
# Workflow template
workflow-name/
├── input-folder/      # Raw data (incoming/, inbox/, eml/)
├── prompts/           # Reusable prompt templates
├── outputs/           # Generated artifacts (gitignore if sensitive)
└── expected/          # Reference examples
```

### Output Management

```bash
# Archive old outputs
mkdir -p archive/$(date +%Y-%m)
mv outputs/*.md archive/$(date +%Y-%m)/

# Version control consideration
echo "outputs/*.md" >> .gitignore  # If outputs contain sensitive data
echo "!outputs/.gitkeep" >> .gitignore
```

## Common Patterns

### Pattern 1: Batch Processing

```bash
# Process multiple files at once
for file in incoming/*; do
  echo "Processing $file"
  # Add to LLM context
done
```

### Pattern 2: Incremental Updates

```markdown
# Daily log update prompt
Review new items added to inbox/ since last log.
Append to existing daily-log.md:
- New accomplishments
- Updated open threads
```

### Pattern 3: Context Injection

```markdown
# Include context from previous outputs
Previous week's summary: [PASTE outputs/weekly-hype-2026-05-04.md]
This week's daily logs: [PASTE all daily-log-*.md from this week]

Generate updated weekly summary considering momentum trends.
```

## Troubleshooting

### Issue: LLM Output Formatting Inconsistent

**Solution:** Add explicit formatting instructions:
```markdown
Output MUST follow this exact markdown structure:

# Title
## Section 1
- Bullet point
## Section 2
- Bullet point

Do not add commentary outside this structure.
```

### Issue: Too Much Context for LLM

**Solution:** Summarize first, then detail:
```markdown
Step 1: Create brief summary of all files
Step 2: For high-priority items, provide detailed analysis
```

### Issue: Missing Key Information

**Solution:** Use extraction checklists:
```markdown
For each email extract:
[ ] Sender
[ ] Subject
[ ] Date
[ ] Action required (Y/N)
[ ] Deadline
[ ] Priority (1-3)
```

### Issue: Outputs Not Actionable

**Solution:** Require action verbs:
```markdown
For each item, specify action using verbs:
- RESPOND to...
- REVIEW...
- SCHEDULE...
- APPROVE...
- DELEGATE...
```

## Integration Ideas

### With Git

```bash
# Track workflow outputs
git add code-along/*/outputs/*.md
git commit -m "Daily standup: $(date +%Y-%m-%d)"
```

### With Task Managers

```bash
# Convert email triage to todo.txt format
grep "Action:" outputs/email-triage.md | \
  sed 's/Action: //' > todo.txt
```

### With Calendar

```markdown
# Include calendar events in daily log prompt
Today's meetings:
- 9am: Team sync
- 2pm: Client call

Generate daily log acknowledging these commitments.
```

## Best Practices

1. **Review before archiving** - Always read LLM output before considering it final
2. **Iterate prompts** - Refine prompts based on output quality
3. **Maintain context** - Link related outputs (daily → weekly → monthly)
4. **Separate sensitive data** - Keep confidential info in ignored folders
5. **Version prompts** - Track prompt changes when outputs improve
6. **Batch similar tasks** - Process all emails at once, not one-by-one
7. **Schedule regular runs** - Make workflows habitual (daily/weekly rhythms)

## Advanced Usage

### Mission Control Dashboard

Create a simple HTML dashboard:

```html
<!-- mission-control/index.html -->
<!DOCTYPE html>
<html>
<head><title>Executive Assistant Dashboard</title></head>
<body>
  <h1>Mission Control</h1>
  <h2>Recent Outputs</h2>
  <ul>
    <li><a href="../02-operational-memory/outputs/daily-log.md">Today's Log</a></li>
    <li><a href="../02-operational-memory/outputs/weekly-hype.md">This Week</a></li>
    <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Actions</a></li>
  </ul>
</body>
</html>
```

### Prompt Chaining

```markdown
# Chain workflows together
1. Run data intake review
2. Extract high-priority items
3. Add to operational memory inbox
4. Include in today's daily log
```

This skill provides a foundation for building sophisticated local-first AI assistant workflows without external dependencies.
