---
name: openclaw-executive-assistant-workflows
description: Build local-first AI executive assistant workflows using OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "set up an OpenClaw executive assistant workflow"
  - "create a local-first AI assistant with OpenClaw"
  - "build data intake review automation"
  - "implement operational memory with daily and weekly logs"
  - "triage emails offline with OpenClaw"
  - "generate markdown reports from files with OpenClaw"
  - "create a mission control dashboard for AI workflows"
  - "automate work residue processing"
---

# OpenClaw Executive Assistant Workflows

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill teaches you how to build local-first AI executive assistant workflows using OpenClaw. The project demonstrates three core patterns: data intake review (turning unknown files into reports), operational memory (processing work residue into logs), and offline communications triage (converting exported emails into action lists). All workflows use local files and produce reviewable markdown artifacts.

## What This Project Does

OpenClaw Executive Assistant is a workshop demonstrating local-only AI workflows that:

- **Data Intake Review**: Transform unknown incoming files into structured intake reports
- **Operational Memory**: Convert daily work notes into daily logs and weekly summaries
- **Offline Communications Triage**: Process exported email files into prioritized action lists
- All outputs are markdown files stored locally
- No live integrations required—everything copy/paste driven
- Designed for AI assistants (Claude, GPT, etc.) to process local files

## Project Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/           # Files to inspect
│   ├── prompts/
│   │   └── intake-review.md
│   ├── outputs/            # Generated reports
│   └── expected/
├── 02-operational-memory/
│   ├── inbox/              # Work notes and residue
│   ├── prompts/
│   │   ├── daily-log.md
│   │   └── weekly-hype.md
│   ├── outputs/
│   └── schedule/
└── 03-offline-communications-triage/
    ├── eml/                # Exported email files
    ├── prompts/
    │   └── email-triage.md
    ├── outputs/
    └── expected/
```

## Installation & Setup

This is a workshop repository—clone and use the structure:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

No dependencies to install. The project uses:
- Local filesystem
- Markdown files
- Copy/paste prompts to AI assistants
- Manual execution (or cron for automation)

## Core Workflow Pattern

Each exercise follows this pattern:

1. **Input**: Files in an `incoming/`, `inbox/`, or `eml/` directory
2. **Prompt**: Structured instructions in `prompts/` directory
3. **Process**: Copy prompt + files to AI assistant
4. **Output**: Markdown report in `outputs/` directory

## Exercise 1: Data Intake Review

**Goal**: Turn unknown files into a trustworthy intake report.

### Workflow

```bash
# Navigate to exercise directory
cd code-along/01-data-intake-review

# View incoming files
ls incoming/

# Read the prompt
cat prompts/intake-review.md

# Process with AI assistant
# Copy prompt content + incoming files to Claude/GPT
# Paste response into:
# outputs/intake-review.md
```

### Example Prompt Structure

```markdown
# Data Intake Review Prompt

Review all files in the incoming/ directory and create a structured intake report.

## Output Format

- Executive Summary
- File Inventory (name, type, size, timestamp)
- Content Assessment (what each file contains)
- Risk Analysis (security, privacy, quality concerns)
- Action Recommendations
- Next Steps

## Analysis Guidelines

- Identify file types and purposes
- Flag any sensitive information
- Assess data quality and completeness
- Recommend processing priorities
```

### Expected Output

```markdown
# Intake Review Report
Generated: 2026-05-15

## Executive Summary
3 files received. Mix of documents and data files.
No immediate security concerns. Ready for processing.

## File Inventory
1. `project-notes.txt` (2.3 KB, text)
2. `data-export.csv` (45 KB, CSV)
3. `meeting-recording.mp3` (8.2 MB, audio)

## Content Assessment
...
```

## Exercise 2: Operational Memory

**Goal**: Turn work residue into daily and weekly momentum docs.

### Daily Log Workflow

```bash
cd code-along/02-operational-memory

# Place work notes in inbox/
# Examples: meeting notes, todo lists, snippets

# Generate daily log
cat prompts/daily-log.md
# Copy to AI with inbox/ contents
# Save to outputs/daily-log.md
```

### Daily Log Prompt Pattern

```markdown
# Daily Log Prompt

Process all files in inbox/ into a daily momentum log.

## Output Structure

### Completed Today
- List of finished tasks with context

### In Progress
- Active work items with status

### Blocked
- Blockers with details and dependencies

### Tomorrow's Focus
- Top 3 priorities

### Notes & Context
- Key decisions, learnings, updates
```

### Weekly Summary Workflow

```bash
# Aggregate multiple daily logs
cat prompts/weekly-hype.md
# Process week's daily logs
# Save to outputs/weekly-hype.md
```

### Weekly Hype Prompt Pattern

```markdown
# Weekly Hype Prompt

Create an energizing weekly summary from daily logs.

## Output Structure

### Wins This Week
- Major accomplishments
- Problems solved
- Milestones reached

### Momentum Builders
- Positive trends
- Skills developed
- Relationships strengthened

### Next Week's Opportunities
- Strategic priorities
- Growth areas

### Metrics Snapshot
- Quantitative progress
```

### Automation with Cron

```bash
# Example cron for daily log generation
# Run at 6 PM daily
0 18 * * * cd ~/code-along/02-operational-memory && ./generate-daily-log.sh

# Example cron for weekly summary
# Run Friday at 5 PM
0 17 * * 5 cd ~/code-along/02-operational-memory && ./generate-weekly-hype.sh
```

### Automation Script Example

```bash
#!/bin/bash
# generate-daily-log.sh

DATE=$(date +%Y-%m-%d)
INBOX_DIR="inbox"
OUTPUT_FILE="outputs/daily-log-${DATE}.md"
PROMPT_FILE="prompts/daily-log.md"

# Collect inbox contents
echo "Processing inbox for ${DATE}..."

# Copy prompt + inbox to AI (use API or manual)
# This example assumes you have an AI CLI tool
ai-assistant process \
  --prompt "$(cat ${PROMPT_FILE})" \
  --context "${INBOX_DIR}/*" \
  --output "${OUTPUT_FILE}"

# Archive processed files
mkdir -p "inbox/processed/${DATE}"
mv ${INBOX_DIR}/* "inbox/processed/${DATE}/"

echo "Daily log generated: ${OUTPUT_FILE}"
```

## Exercise 3: Offline Communications Triage

**Goal**: Turn exported mail into an action list.

### Email Triage Workflow

```bash
cd code-along/03-offline-communications-triage

# Export emails to eml/ directory
# (Use email client's "Save As" feature)

# Process with triage prompt
cat prompts/email-triage.md
# Copy to AI with eml/ contents
# Save to outputs/email-triage.md
```

### Email Triage Prompt Pattern

```markdown
# Email Triage Prompt

Process all .eml files into a prioritized action list.

## Output Structure

### High Priority (Action Required)
- Sender, subject, deadline, action needed

### Medium Priority (Response Needed)
- Sender, subject, suggested response time

### Low Priority (FYI / File)
- Sender, subject, category

### Spam / Unsubscribe
- Emails to delete or unsubscribe

### Follow-Up Tracking
- Items waiting on others

## Triage Rules
- Flag time-sensitive items
- Identify decision requests
- Group related threads
- Surface action dependencies
```

### Example Output

```markdown
# Email Triage Report
Generated: 2026-05-15 | 23 emails processed

## High Priority (Action Required) - 4 items

### 1. Project deadline extension request
- **From**: jane@client.com
- **Deadline**: Tomorrow 5 PM
- **Action**: Review timeline and respond with revised schedule

### 2. Budget approval needed
- **From**: finance@company.com
- **Deadline**: Friday EOD
- **Action**: Approve Q3 budget allocation form

## Medium Priority (Response Needed) - 8 items
...

## Low Priority (FYI / File) - 9 items
...

## Spam / Unsubscribe - 2 items
...
```

## Mission Control Dashboard (Optional)

Create a simple HTML dashboard linking to generated artifacts:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Mission Control</title>
  <style>
    body { font-family: system-ui; max-width: 800px; margin: 40px auto; padding: 20px; }
    .section { margin: 30px 0; padding: 20px; border: 1px solid #ddd; border-radius: 8px; }
    .section h2 { margin-top: 0; }
    ul { list-style: none; padding: 0; }
    li { margin: 10px 0; }
    a { color: #0066cc; text-decoration: none; }
    a:hover { text-decoration: underline; }
    .timestamp { color: #666; font-size: 0.9em; }
  </style>
</head>
<body>
  <h1>🚀 Mission Control</h1>
  
  <div class="section">
    <h2>📥 Data Intake</h2>
    <ul>
      <li><a href="01-data-intake-review/outputs/intake-review.md">Latest Intake Review</a></li>
      <li class="timestamp">Updated: 2026-05-15 10:30</li>
    </ul>
  </div>
  
  <div class="section">
    <h2>🧠 Operational Memory</h2>
    <ul>
      <li><a href="02-operational-memory/outputs/daily-log.md">Today's Log</a></li>
      <li><a href="02-operational-memory/outputs/weekly-hype.md">This Week's Hype</a></li>
      <li class="timestamp">Updated: 2026-05-15 18:00</li>
    </ul>
  </div>
  
  <div class="section">
    <h2>📧 Communications</h2>
    <ul>
      <li><a href="03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
      <li class="timestamp">Updated: 2026-05-15 09:15</li>
    </ul>
  </div>
</body>
</html>
```

## Common Patterns

### Pattern 1: Incremental Processing

```bash
# Process new files only
NEW_FILES=$(find incoming/ -type f -mtime -1)
if [ -n "$NEW_FILES" ]; then
  # Process only new files
  echo "$NEW_FILES" | ai-process --prompt intake-review.md
fi
```

### Pattern 2: Batched Triage

```bash
# Process emails in batches of 20
for batch in eml/*.eml; do
  ai-assistant triage --batch 20 --input "$batch"
done
```

### Pattern 3: Context Accumulation

```markdown
# Context-aware prompt
Review today's work notes in the context of:
- Yesterday's daily log: outputs/daily-log-2026-05-14.md
- This week's priorities: outputs/weekly-hype.md
- Recent intake: 01-data-intake-review/outputs/intake-review.md
```

## Configuration & Customization

### Customize Prompts

Edit prompt files to match your workflow:

```bash
# Add domain-specific instructions
echo "## Our Team's Conventions
- Use JIRA ticket format: PROJ-123
- Tag urgent items with [URGENT]
- Link to internal wiki for context" >> prompts/daily-log.md
```

### Adjust Output Formats

```markdown
# outputs/custom-template.md

# Custom Report Format
Generated: {{DATE}}

## Context
Project: {{PROJECT_NAME}}
Sprint: {{SPRINT_NUMBER}}

## Standard Sections
...
```

### Environment Variables

```bash
# Set default paths
export OPENCLAW_INBOX="$HOME/work/inbox"
export OPENCLAW_OUTPUTS="$HOME/work/outputs"
export AI_MODEL="claude-3-opus-20240229"
```

## Troubleshooting

### Files Not Processing

```bash
# Check file permissions
ls -la incoming/
chmod 644 incoming/*

# Verify file encoding
file incoming/*
```

### Empty Outputs

```bash
# Ensure prompt file exists
cat prompts/intake-review.md

# Check AI assistant context window
# Large files may exceed token limits
# Split into smaller batches
```

### Stale Reports

```bash
# Add timestamps to output filenames
OUTPUT_FILE="outputs/intake-review-$(date +%Y%m%d-%H%M%S).md"

# Create "latest" symlink
ln -sf "$OUTPUT_FILE" outputs/intake-review-latest.md
```

### Cron Not Running

```bash
# Check cron logs
grep CRON /var/log/syslog

# Verify script permissions
chmod +x generate-daily-log.sh

# Test script manually
./generate-daily-log.sh
```

## Best Practices

1. **Review Before Archiving**: Always review AI outputs before moving source files
2. **Version Control**: Commit prompts and templates, gitignore outputs with sensitive data
3. **Consistent Naming**: Use ISO date formats (YYYY-MM-DD) for timestamped files
4. **Atomic Operations**: Process one workflow at a time, verify output quality
5. **Context Preservation**: Keep related artifacts together for future reference
6. **Prompt Evolution**: Refine prompts based on output quality over time

## Integration Ideas

```bash
# Sync to private git repo
git add outputs/*.md && git commit -m "Daily sync" && git push

# Export to Obsidian vault
rsync -av outputs/ ~/Obsidian/work-logs/

# Generate RSS feed
./scripts/markdown-to-rss.sh outputs/ > feed.xml
```
