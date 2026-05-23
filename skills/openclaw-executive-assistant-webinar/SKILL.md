---
name: openclaw-executive-assistant-webinar
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - how do I build an executive assistant with OpenClaw
  - set up OpenClaw for local data intake review
  - create operational memory system with OpenClaw
  - triage emails offline with OpenClaw
  - build local-first AI assistant workflows
  - use OpenClaw for communications and task management
  - set up daily logs and weekly summaries with AI
  - process incoming files with OpenClaw assistant
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill covers building a local-first AI executive assistant using OpenClaw workflows. The project demonstrates three core patterns: data intake review, operational memory management, and offline communications triage. All workflows stay local, produce markdown artifacts, and require no live integrations.

## What This Project Does

This is a workshop repository that teaches you to build AI-powered executive assistant capabilities:

1. **Data Intake Review** - Process unknown files into structured intake reports
2. **Operational Memory** - Convert work residue into daily logs and weekly summaries
3. **Offline Communications Triage** - Transform exported emails into action lists

All operations are local-only, copy/paste driven, and produce reviewable markdown outputs.

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/           # Prompt templates
│   ├── outputs/           # Generated reports
│   └── expected/          # Expected output shape
├── 02-operational-memory/
│   ├── inbox/             # Work notes and residue
│   ├── prompts/           # Daily/weekly prompts
│   ├── outputs/           # Generated logs
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/           # Triage prompt
│   ├── outputs/           # Generated action lists
│   └── expected/          # Expected output shape
└── mission-control/       # Optional dashboard
```

## Installation & Setup

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the walkthrough guide
open webinar-runbook.html

# Navigate to code-along directory
cd code-along
```

No additional dependencies required - this is a prompt-driven workflow using AI assistants.

## Core Workflow Patterns

### 1. Data Intake Review

Process unknown incoming files into a structured report.

**Location**: `code-along/01-data-intake-review/`

**Workflow**:
```bash
# 1. Place files in incoming/
ls incoming/

# 2. Use the intake review prompt
cat prompts/intake-review.md

# 3. Feed prompt + files to your AI assistant (Claude, GPT, etc.)
# 4. Save output to outputs/intake-review.md
```

**Expected Output Structure** (from `expected/report-outline.md`):
```markdown
# Intake Review Report

## Summary
- Total files processed: X
- File types: [list]
- Priority items: [count]

## File Analysis
### [filename]
- Type: [file type]
- Size: [size]
- Content summary: [brief description]
- Recommended action: [action]
- Priority: [High/Medium/Low]

## Recommended Actions
1. [Action item 1]
2. [Action item 2]
...
```

### 2. Operational Memory System

Transform work residue into daily logs and weekly summaries.

**Location**: `code-along/02-operational-memory/`

**Daily Log Workflow**:
```bash
# 1. Collect work notes in inbox/
# (meeting notes, task snippets, ideas, etc.)

# 2. Use daily log prompt
cat prompts/daily-log.md

# 3. Generate daily-log.md
# Output saves to outputs/daily-log.md
```

**Weekly Summary Workflow**:
```bash
# 1. Accumulate daily logs throughout the week

# 2. Use weekly hype prompt
cat prompts/weekly-hype.md

# 3. Generate weekly-hype.md
# Output saves to outputs/weekly-hype.md
```

**Automation Example** (from `schedule/cron-examples.md`):
```bash
# Daily log generation at 5 PM
0 17 * * * cd /path/to/project && ./generate-daily-log.sh

# Weekly summary on Friday at 4 PM
0 16 * * 5 cd /path/to/project && ./generate-weekly-summary.sh
```

**Daily Log Output Pattern**:
```markdown
# Daily Log - [Date]

## Top Accomplishments
- [Accomplishment 1]
- [Accomplishment 2]

## Key Decisions
- [Decision 1] - [Context]

## Blockers & Questions
- [Blocker 1]

## Tomorrow's Focus
1. [Priority 1]
2. [Priority 2]

## Notes & Context
[Additional context from inbox/]
```

**Weekly Summary Pattern**:
```markdown
# Weekly Hype - Week of [Date Range]

## Week in Review
[High-level summary]

## Major Wins
1. [Win 1]
2. [Win 2]

## Momentum Builders
- [Item that increased momentum]

## Next Week Priorities
1. [Priority 1]
2. [Priority 2]

## Metrics & Progress
[Relevant metrics if available]
```

### 3. Offline Communications Triage

Process exported emails into actionable lists.

**Location**: `code-along/03-offline-communications-triage/`

**Workflow**:
```bash
# 1. Export emails to .eml files in eml/
# (Use your email client's "Save As" or export feature)

# 2. Use email triage prompt
cat prompts/email-triage.md

# 3. Generate triage report
# Output saves to outputs/email-triage.md
```

**Expected Triage Output** (from `expected/report-outline.md`):
```markdown
# Email Triage Report

## Immediate Action Required (Today)
- [ ] [Email subject] - [Sender] - [Required action]

## This Week
- [ ] [Email subject] - [Sender] - [Required action]

## For Review (No immediate action)
- [Email subject] - [Sender] - [Summary]

## FYI / Archive
- [Email subject] - [Sender] - [One-line summary]

## Summary Statistics
- Total emails: X
- Urgent: X
- This week: X
- Review: X
- Archive: X
```

## Prompt Engineering Best Practices

**Effective Prompt Structure**:
```markdown
# [Task Name]

## Context
[Explain the goal and constraints]

## Input
[Describe what files/data will be provided]

## Expected Output
[Specify format, structure, and required sections]

## Rules
- [Rule 1: e.g., "Preserve original context"]
- [Rule 2: e.g., "Prioritize by urgency"]
- [Rule 3: e.g., "Output valid markdown"]

## Example
[Show a sample output if helpful]
```

## Configuration & Customization

**Customize Prompt Templates**:
```bash
# Edit prompts for your workflow
vim code-along/01-data-intake-review/prompts/intake-review.md
vim code-along/02-operational-memory/prompts/daily-log.md
vim code-along/02-operational-memory/prompts/weekly-hype.md
vim code-along/03-offline-communications-triage/prompts/email-triage.md
```

**Adjust Output Directories**:
```bash
# All outputs go to respective outputs/ folders
# Customize paths in your automation scripts
OUTPUT_DIR="./outputs"
ARCHIVE_DIR="./archive/$(date +%Y-%m)"
```

## Mission Control Dashboard

**Optional**: Create a unified view of all generated artifacts.

**Location**: `code-along/mission-control/`

**Simple HTML Dashboard Example**:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Mission Control</title>
</head>
<body>
    <h1>Mission Control</h1>
    
    <section>
        <h2>Latest Reports</h2>
        <ul>
            <li><a href="../01-data-intake-review/outputs/intake-review.md">Data Intake Review</a></li>
            <li><a href="../02-operational-memory/outputs/daily-log.md">Today's Log</a></li>
            <li><a href="../02-operational-memory/outputs/weekly-hype.md">Weekly Summary</a></li>
            <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
        </ul>
    </section>
</body>
</html>
```

## Common Patterns

### Batch Processing Multiple Files

```bash
# Process all files in incoming/
for file in incoming/*; do
    echo "Processing $file"
    # Feed to AI with prompt
    # Append to outputs/intake-review.md
done
```

### Archive Workflow

```bash
# Create dated archive
ARCHIVE_DATE=$(date +%Y-%m-%d)
mkdir -p archive/$ARCHIVE_DATE

# Move processed files
mv incoming/* archive/$ARCHIVE_DATE/
mv outputs/*.md archive/$ARCHIVE_DATE/
```

### Chaining Workflows

```bash
#!/bin/bash
# daily-executive-routine.sh

# 1. Process any new incoming files
cd 01-data-intake-review
# [run intake review]

# 2. Generate daily log
cd ../02-operational-memory
# [run daily log generation]

# 3. Triage new emails
cd ../03-offline-communications-triage
# [run email triage]

echo "Daily executive routine complete!"
```

## Troubleshooting

**Issue**: AI output is not markdown formatted
- **Solution**: Add explicit formatting instructions to prompt: "Output valid markdown with proper headers and lists"

**Issue**: Reports are too verbose
- **Solution**: Add length constraints: "Limit each section to 3-5 bullet points" or "Keep total output under 500 words"

**Issue**: Missing context in generated logs
- **Solution**: Ensure all relevant files are in the inbox/ or incoming/ folder before running prompts

**Issue**: Automation scripts not finding files
- **Solution**: Use absolute paths in cron jobs and verify working directory

**Issue**: Email .eml files not processing correctly
- **Solution**: Ensure .eml files are properly exported with full headers and body content

**Issue**: Reports lack prioritization
- **Solution**: Enhance prompts with explicit priority criteria: "Classify by: Urgent (same day), High (this week), Medium (this month), Low (backlog)"

## Integration with AI Coding Agents

**For Claude Code / Cursor**:
```bash
# Reference prompt files directly
@code-along/01-data-intake-review/prompts/intake-review.md

# Point to input directory
Process all files in code-along/01-data-intake-review/incoming/
```

**For Automated Workflows**:
```bash
# Use environment variables for AI API keys
export OPENAI_API_KEY="${OPENAI_API_KEY}"
export ANTHROPIC_API_KEY="${ANTHROPIC_API_KEY}"

# Call AI from scripts
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -d @request.json > outputs/intake-review.md
```

## Best Practices

1. **Version Control Outputs**: Commit generated reports to track changes over time
2. **Regular Cadence**: Run daily logs at same time each day for consistency
3. **Review Before Archive**: Always manually review AI-generated reports before archiving
4. **Iterate on Prompts**: Refine prompts based on output quality
5. **Privacy First**: Never commit sensitive data; use .gitignore for inbox/ and incoming/
6. **Backup Archives**: Maintain separate backup of archive/ directory

## Workshop Walkthrough

1. Open `webinar-runbook.html` in browser
2. Keep `code-along/` folder visible in file explorer or IDE
3. Execute exercises in order: 01 → 02 → 03
4. Compare your outputs against expected/ directories
5. Customize prompts for your own workflows

---

**Note**: This is a pedagogical project for learning local-first AI assistant patterns. Adapt these workflows to your own executive assistant needs.
