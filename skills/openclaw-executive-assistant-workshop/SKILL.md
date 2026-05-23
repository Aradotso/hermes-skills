---
name: openclaw-executive-assistant-workshop
description: Local-first OpenClaw workflows for building executive assistant automations with data intake, operational memory, and communications triage
triggers:
  - how do I use OpenClaw for executive assistant tasks
  - set up local OpenClaw workflows for productivity
  - build an executive assistant with OpenClaw
  - create OpenClaw intake and triage systems
  - automate daily logs and weekly summaries with OpenClaw
  - process emails offline with OpenClaw
  - set up OpenClaw operational memory
  - create markdown reports with OpenClaw prompts
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill covers building local-first executive assistant workflows using OpenClaw. The project demonstrates three core automation patterns: data intake review, operational memory management, and offline communications triage. All workflows operate on local files and produce reviewable markdown artifacts.

## What This Project Does

This is a workshop/starter project that teaches how to build executive assistant automations using OpenClaw with:

1. **Data Intake Review** - Transform unknown files into trustworthy intake reports
2. **Operational Memory** - Convert work residue into daily logs and weekly summaries
3. **Offline Communications Triage** - Process exported emails into action lists

Everything runs locally with no live integrations, using copy/paste prompts that generate markdown outputs.

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/
│   │   └── intake-review.md
│   ├── outputs/
│   │   └── intake-review.md
│   └── expected/
│       └── report-outline.md
├── 02-operational-memory/
│   ├── inbox/             # Notes and work residue
│   ├── prompts/
│   │   ├── daily-log.md
│   │   └── weekly-hype.md
│   ├── outputs/
│   │   ├── daily-log.md
│   │   └── weekly-hype.md
│   └── schedule/
│       ├── cron-examples.md
│       └── heartbeat-note.md
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/
│   │   └── email-triage.md
│   ├── outputs/
│   │   └── email-triage.md
│   └── expected/
│       └── report-outline.md
└── mission-control/       # Optional dashboard
```

## Getting Started

### Installation

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the main walkthrough
open webinar-runbook.html
```

### Prerequisites

- OpenClaw or compatible AI assistant (Claude Code, Cursor, Codex)
- Local file system access
- Markdown viewer/editor

## Core Workflows

### 1. Data Intake Review

Process unknown files from `incoming/` directory into structured reports.

**Workflow:**

```bash
cd code-along/01-data-intake-review
# Review files in incoming/
# Apply prompt from prompts/intake-review.md
# Generate outputs/intake-review.md
```

**Expected Output Structure:**

```markdown
# Intake Review Report

## Summary
[High-level overview of files]

## Files Reviewed
- file1.txt: [description and assessment]
- file2.csv: [description and assessment]

## Recommendations
[Action items and next steps]

## Risk Assessment
[Security or quality concerns]
```

**Prompt Pattern:**

The `prompts/intake-review.md` file contains instructions for:
- Cataloging file types and contents
- Assessing data quality and completeness
- Identifying security or privacy concerns
- Generating actionable recommendations

### 2. Operational Memory

Transform daily work residue into momentum documents.

**Daily Log Workflow:**

```bash
cd code-along/02-operational-memory
# Process files from inbox/
# Apply prompts/daily-log.md
# Generate outputs/daily-log.md
```

**Daily Log Output:**

```markdown
# Daily Log - [DATE]

## Completed
- [Task completed with context]
- [Another completed item]

## In Progress
- [Active work item]
- [Blockers if any]

## Notes
[Important observations or decisions]

## Tomorrow
[Priorities for next day]
```

**Weekly Summary Workflow:**

```bash
# Apply prompts/weekly-hype.md to week's daily logs
# Generate outputs/weekly-hype.md
```

**Weekly Summary Output:**

```markdown
# Weekly Hype - Week of [DATE]

## Highlights
[Major accomplishments]

## Momentum
[Progress on key initiatives]

## Blockers Resolved
[Problems solved]

## Next Week Focus
[Priorities and goals]
```

**Automation with Cron:**

```bash
# Example cron for daily log generation (schedule/cron-examples.md)
0 17 * * * cd /path/to/project/code-along/02-operational-memory && ./generate-daily-log.sh

# Example cron for weekly summary
0 9 * * 1 cd /path/to/project/code-along/02-operational-memory && ./generate-weekly-hype.sh
```

### 3. Offline Communications Triage

Process exported email files into actionable lists.

**Workflow:**

```bash
cd code-along/03-offline-communications-triage
# Place .eml files in eml/ directory
# Apply prompts/email-triage.md
# Generate outputs/email-triage.md
```

**Email Triage Output:**

```markdown
# Email Triage - [DATE]

## Urgent Action Required
- [Sender]: [Subject] - [Action needed by when]

## Follow-up This Week
- [Sender]: [Subject] - [What to do]

## FYI / Reading
- [Sender]: [Subject] - [Brief summary]

## Archive/No Action
- [Sender]: [Subject] - [Why no action needed]

## Statistics
Total emails: X
Action required: Y
```

**Email Export Process:**

Most email clients support .eml export:
- Outlook: Drag email to desktop
- Gmail: Use Google Takeout or "Download message"
- Apple Mail: File → Save As

## Prompt Engineering Patterns

### Standard Prompt Structure

All prompts in this project follow this pattern:

```markdown
# [Task Name]

## Context
[What files to process, where they are]

## Instructions
[Step-by-step processing requirements]

## Output Format
[Exact markdown structure expected]

## Guidelines
- [Specific rule 1]
- [Specific rule 2]
```

### Reusable Prompt Components

**File Cataloging:**
```
List each file with:
- Filename
- Type/format
- Size
- Last modified
- Brief content description
```

**Risk Assessment:**
```
Evaluate for:
- Sensitive data (PII, credentials)
- Incomplete or corrupted content
- Version conflicts
- Missing dependencies
```

**Action Generation:**
```
For each actionable item, specify:
- What needs to be done
- Who should do it
- When it's needed
- Why it matters
```

## Configuration

### Environment Variables

```bash
# Optional: Set default paths
export OPENCLAW_INTAKE_DIR="$HOME/inbox"
export OPENCLAW_MEMORY_DIR="$HOME/work-logs"
export OPENCLAW_OUTPUT_DIR="$HOME/assistant-reports"

# Optional: Set AI provider
export OPENCLAW_AI_PROVIDER="claude"
export ANTHROPIC_API_KEY="$YOUR_API_KEY"
```

### Customizing Prompts

Edit prompt files to match your workflow:

```bash
# Copy and modify default prompts
cp prompts/daily-log.md prompts/daily-log-custom.md

# Add custom sections
# Adjust tone and format
# Include domain-specific requirements
```

## Common Use Cases

### Morning Briefing Automation

```bash
#!/bin/bash
# morning-briefing.sh

cd code-along/02-operational-memory
cat inbox/*.md | openclaw apply prompts/daily-log.md > outputs/today.md

cd ../03-offline-communications-triage
openclaw apply prompts/email-triage.md eml/*.eml > outputs/emails-today.md

# Combine reports
cat outputs/today.md outputs/emails-today.md > mission-control/morning-brief.md
```

### End of Day Wrap-up

```bash
#!/bin/bash
# eod-wrap.sh

# Capture today's work
echo "## Work completed:" > inbox/eod-$(date +%Y-%m-%d).md
# Add your notes here or pipe from other tools

cd code-along/02-operational-memory
openclaw apply prompts/daily-log.md > outputs/daily-log-$(date +%Y-%m-%d).md
```

### Weekly Review

```bash
#!/bin/bash
# weekly-review.sh

cd code-along/02-operational-memory
# Concatenate week's daily logs
cat outputs/daily-log-*.md | openclaw apply prompts/weekly-hype.md > outputs/weekly-$(date +%Y-W%V).md
```

## Troubleshooting

### Prompts Not Generating Expected Output

**Issue:** Output doesn't match expected structure

**Solutions:**
- Review `expected/report-outline.md` for reference format
- Ensure all input files are in correct directory
- Check that prompt references correct file paths
- Verify markdown syntax in prompt instructions

### File Processing Errors

**Issue:** Cannot read files from incoming/inbox directories

**Solutions:**
```bash
# Check file permissions
ls -la code-along/01-data-intake-review/incoming/

# Verify file encoding
file incoming/*

# Check for hidden characters
cat -A incoming/problematic-file.txt
```

### Missing Dependencies

**Issue:** Cron jobs fail silently

**Solutions:**
```bash
# Test script manually first
bash schedule/generate-daily-log.sh

# Check cron logs
grep CRON /var/log/syslog

# Use full paths in cron
0 17 * * * /usr/bin/bash /full/path/to/script.sh >> /tmp/cron.log 2>&1
```

### Output Quality Issues

**Issue:** Reports lack detail or miss important items

**Solutions:**
- Add more examples to prompt instructions
- Increase specificity in "Guidelines" section
- Include edge cases in prompt
- Review and iterate on prompt wording

## Best Practices

### Prompt Maintenance

```markdown
# Version your prompts
prompts/
  daily-log-v1.md
  daily-log-v2.md
  daily-log.md -> daily-log-v2.md

# Document changes
## Changelog
- v2: Added "Blockers" section, improved action item detection
- v1: Initial version
```

### Data Organization

```bash
# Use dated directories for outputs
outputs/
  2026-05/
    daily-log-2026-05-11.md
    daily-log-2026-05-12.md
  2026-04/
    ...
```

### Security

```bash
# Never commit sensitive data
echo "incoming/*.eml" >> .gitignore
echo "outputs/*" >> .gitignore
echo "inbox/*" >> .gitignore

# Review generated outputs before sharing
grep -i "password\|secret\|key\|token" outputs/*.md
```

## Integration Examples

### With Task Managers

```bash
# Extract action items from daily log
grep "^- \[ \]" outputs/daily-log.md > tasks.txt

# Convert to your task manager format
# (Todoist, Things, etc.)
```

### With Note-Taking Systems

```bash
# Copy to Obsidian vault
cp outputs/daily-log-$(date +%Y-%m-%d).md "$OBSIDIAN_VAULT/Logs/"

# Add frontmatter
sed -i '1i---\ndate: '$(date +%Y-%m-%d)'\ntags: [daily-log]\n---\n' \
  "$OBSIDIAN_VAULT/Logs/daily-log-$(date +%Y-%m-%d).md"
```

## Additional Resources

- Workshop walkthrough: `webinar-runbook.html`
- DataCamp webinar: https://www.datacamp.com/webinars/build-your-own-executive-assistant-with-openclaw
- Exercise index: `code-along/INDEX.md`
