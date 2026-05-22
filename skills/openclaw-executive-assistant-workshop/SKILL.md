---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows using OpenClaw with file-based data intake, operational memory, and communications triage
triggers:
  - "help me set up an OpenClaw executive assistant workflow"
  - "how do I create a data intake review with OpenClaw"
  - "build an operational memory system with daily and weekly logs"
  - "set up email triage automation using OpenClaw"
  - "create a local-first executive assistant with OpenClaw"
  - "how do I structure OpenClaw prompts for workflow automation"
  - "configure OpenClaw for offline communications processing"
  - "build markdown-based workflow artifacts with OpenClaw"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill covers building local-first AI executive assistant workflows using the OpenClaw framework. The project demonstrates three core patterns: data intake review, operational memory management, and offline communications triage—all using local files and markdown artifacts.

## What This Project Does

The OpenClaw Executive Assistant workshop teaches you to build AI-powered workflow automation that:

- **Stays local-only**: No cloud integrations, all files on disk
- **Produces reviewable artifacts**: Everything outputs to markdown
- **Follows copy/paste patterns**: Simple prompt-based workflows
- **Builds momentum systems**: Daily logs, weekly summaries, intake reports

The three core workflows are:

1. **Data Intake Review**: Turn unknown files into trustworthy reports
2. **Operational Memory**: Turn work residue into momentum docs
3. **Offline Communications Triage**: Turn exported emails into action lists

## Repository Structure

```
code-along/
├── INDEX.md                                    # Workshop overview
├── 01-data-intake-review/
│   ├── incoming/                               # Files to inspect
│   ├── prompts/intake-review.md                # Review prompt
│   ├── outputs/intake-review.md                # Generated report
│   └── expected/report-outline.md              # Expected structure
├── 02-operational-memory/
│   ├── inbox/                                  # Work notes and residue
│   ├── prompts/daily-log.md                    # Daily log prompt
│   ├── prompts/weekly-hype.md                  # Weekly summary prompt
│   ├── outputs/daily-log.md                    # Generated daily log
│   ├── outputs/weekly-hype.md                  # Generated weekly summary
│   └── schedule/cron-examples.md               # Automation examples
└── 03-offline-communications-triage/
    ├── eml/                                    # Exported email files
    ├── prompts/email-triage.md                 # Triage prompt
    ├── outputs/email-triage.md                 # Generated action list
    └── expected/report-outline.md              # Expected structure
```

## Getting Started

### Installation

1. Clone the repository:
```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

2. Open the walkthrough:
```bash
open webinar-runbook.html
```

3. Navigate to the code-along folder:
```bash
cd code-along
```

### Prerequisites

- An AI assistant (Claude, ChatGPT, or local LLM)
- A text editor or IDE
- Access to local file system

## Workflow 1: Data Intake Review

Transform unknown files in a directory into a structured intake report.

### Directory Setup

```bash
cd code-along/01-data-intake-review
ls incoming/  # View files to review
```

### Prompt Pattern

The `prompts/intake-review.md` file contains instructions like:

```markdown
# Data Intake Review

Review all files in the `incoming/` directory and create a report with:

1. **File inventory**: List each file with type and size
2. **Content summary**: Brief description of what each file contains
3. **Risk assessment**: Flag any potential security concerns
4. **Action items**: Suggest next steps for each file
5. **Priority ranking**: Order files by importance

Output to: `outputs/intake-review.md`
```

### Using the Prompt

1. Read the contents of `incoming/` directory
2. Copy the prompt from `prompts/intake-review.md`
3. Provide the prompt + file contents to your AI assistant
4. Save the output to `outputs/intake-review.md`

### Expected Output Structure

```markdown
# Data Intake Review — [DATE]

## File Inventory
- `document.pdf` (245 KB) — PDF document
- `data.csv` (12 KB) — Spreadsheet data
- `notes.txt` (3 KB) — Plain text notes

## Content Summaries
### document.pdf
[Description of contents]

## Risk Assessment
- ✅ `notes.txt` — Clean
- ⚠️  `data.csv` — Contains email addresses

## Action Items
1. Review data.csv for PII compliance
2. File document.pdf in appropriate folder

## Priority Ranking
1. document.pdf (High)
2. data.csv (Medium)
3. notes.txt (Low)
```

## Workflow 2: Operational Memory

Build daily and weekly momentum documents from work residue.

### Directory Setup

```bash
cd code-along/02-operational-memory
ls inbox/  # View work notes and residue
```

### Daily Log Pattern

```markdown
# Daily Log — [DATE]

## Wins Today
- [Completed items from inbox]

## In Progress
- [Active work items]

## Blockers
- [Issues preventing progress]

## Tomorrow's Focus
- [Top 3 priorities]

## Notes & Context
- [Additional observations]
```

### Prompt Usage for Daily Log

1. Collect all notes from `inbox/` directory
2. Use the prompt from `prompts/daily-log.md`
3. Generate structured output to `outputs/daily-log.md`

### Weekly Hype Pattern

```markdown
# Weekly Hype — Week of [DATE]

## Major Wins
- [Significant accomplishments]

## Momentum Indicators
- [Metrics and progress signals]

## Learning & Growth
- [Skills developed, insights gained]

## Next Week's Priorities
1. [Priority 1]
2. [Priority 2]
3. [Priority 3]

## Team/Collaboration Highlights
- [Notable interactions]
```

### Automation Example

The `schedule/cron-examples.md` shows how to automate:

```bash
# Daily log generation at 5 PM
0 17 * * * cd /path/to/project && ./generate-daily-log.sh

# Weekly summary on Friday at 4 PM
0 16 * * 5 cd /path/to/project && ./generate-weekly-hype.sh
```

### Script Pattern for Daily Log

```bash
#!/bin/bash
# generate-daily-log.sh

DATE=$(date +%Y-%m-%d)
INBOX_DIR="code-along/02-operational-memory/inbox"
PROMPT_FILE="code-along/02-operational-memory/prompts/daily-log.md"
OUTPUT_FILE="code-along/02-operational-memory/outputs/daily-log-${DATE}.md"

# Concatenate inbox contents
INBOX_CONTENTS=$(cat ${INBOX_DIR}/*.md 2>/dev/null || echo "No notes today")

# Combine prompt with inbox contents
FULL_PROMPT=$(cat ${PROMPT_FILE})
echo "${FULL_PROMPT}\n\nInbox contents:\n${INBOX_CONTENTS}"

# Send to AI assistant (example using Claude API)
# curl https://api.anthropic.com/v1/messages \
#   -H "x-api-key: $ANTHROPIC_API_KEY" \
#   -H "content-type: application/json" \
#   -d "{...}" > ${OUTPUT_FILE}
```

## Workflow 3: Offline Communications Triage

Convert exported email files into actionable task lists.

### Directory Setup

```bash
cd code-along/03-offline-communications-triage
ls eml/  # View exported .eml files
```

### Email Triage Prompt Pattern

```markdown
# Email Triage

Process all .eml files and create a triage report with:

1. **Urgent**: Requires response within 24 hours
2. **Important**: Requires response this week
3. **FYI**: For awareness only
4. **Delegate**: Can be handled by someone else
5. **Archive**: No action needed

For each email include:
- Subject line
- Sender
- Date received
- Key action items
- Suggested response time

Output to: `outputs/email-triage.md`
```

### Expected Triage Output

```markdown
# Email Triage — [DATE]

## 🔴 Urgent (Response in 24h)
### "Q4 Budget Approval Needed"
- **From**: finance@company.com
- **Date**: 2026-05-10
- **Action**: Review and approve budget spreadsheet
- **Deadline**: EOD today

## 🟡 Important (This Week)
### "Project Milestone Review"
- **From**: pm@company.com
- **Date**: 2026-05-09
- **Action**: Schedule 30-min review meeting
- **Deadline**: By Friday

## 🟢 FYI (No Action Required)
### "Team Newsletter - May Edition"
- **From**: comms@company.com
- **Date**: 2026-05-08
- **Action**: None (informational)

## 👥 Delegate
### "Customer Support Question"
- **From**: customer@example.com
- **Date**: 2026-05-07
- **Action**: Forward to support team
- **To**: support@company.com

## 📦 Archive
### "Conference Registration Confirmed"
- **From**: events@conference.com
- **Date**: 2026-05-06
- **Action**: File for reference
```

### Processing .eml Files

```bash
# List all .eml files
find eml/ -name "*.eml" -type f

# Extract email contents (example using Python)
python3 << 'EOF'
import email
import os
from pathlib import Path

eml_dir = Path("eml")
for eml_file in eml_dir.glob("*.eml"):
    with open(eml_file, 'r') as f:
        msg = email.message_from_file(f)
        print(f"Subject: {msg['subject']}")
        print(f"From: {msg['from']}")
        print(f"Date: {msg['date']}")
        print("---")
EOF
```

## Common Patterns

### Pattern 1: Prompt + Content + Output

All workflows follow this structure:

1. **Read source files** (incoming/, inbox/, eml/)
2. **Load prompt template** (prompts/*.md)
3. **Combine and process** (send to AI)
4. **Write artifact** (outputs/*.md)

### Pattern 2: Markdown-First Artifacts

Every output is markdown for:
- Easy version control (git)
- Plain-text searchability
- Human-readable review
- Future automation input

### Pattern 3: Directory-Based Organization

```
[workflow-name]/
├── incoming/   or inbox/ or eml/     # Source files
├── prompts/                           # Prompt templates
├── outputs/                           # Generated artifacts
└── expected/                          # Example outputs
```

### Pattern 4: Progressive Enhancement

Start simple, then add:
1. Manual copy/paste
2. Shell scripts
3. Cron automation
4. Dashboard aggregation

## Integration Example: Mission Control Dashboard

Create a simple HTML dashboard linking all artifacts:

```html
<!-- code-along/mission-control/index.html -->
<!DOCTYPE html>
<html>
<head>
    <title>Executive Assistant Dashboard</title>
    <style>
        body { font-family: system-ui; max-width: 1200px; margin: 2rem auto; }
        .workflow { border: 1px solid #ddd; padding: 1rem; margin: 1rem 0; }
        .artifact { margin: 0.5rem 0; }
        .artifact a { color: #0066cc; text-decoration: none; }
    </style>
</head>
<body>
    <h1>📋 Executive Assistant Dashboard</h1>
    
    <div class="workflow">
        <h2>📥 Data Intake Review</h2>
        <div class="artifact">
            <a href="../01-data-intake-review/outputs/intake-review.md">
                Latest Intake Review
            </a>
        </div>
    </div>
    
    <div class="workflow">
        <h2>🧠 Operational Memory</h2>
        <div class="artifact">
            <a href="../02-operational-memory/outputs/daily-log.md">
                Today's Log
            </a>
        </div>
        <div class="artifact">
            <a href="../02-operational-memory/outputs/weekly-hype.md">
                This Week's Summary
            </a>
        </div>
    </div>
    
    <div class="workflow">
        <h2>📧 Communications Triage</h2>
        <div class="artifact">
            <a href="../03-offline-communications-triage/outputs/email-triage.md">
                Latest Email Triage
            </a>
        </div>
    </div>
</body>
</html>
```

## Troubleshooting

### Issue: Prompt produces inconsistent output

**Solution**: Add explicit structure constraints to your prompt:

```markdown
Your output MUST follow this exact structure:

## Section 1
[content]

## Section 2
[content]

Do not add extra sections or deviate from this format.
```

### Issue: Files not found in directory

**Solution**: Check your working directory and use absolute paths:

```bash
# Verify current directory
pwd

# Use absolute path in scripts
INBOX_DIR="/full/path/to/code-along/02-operational-memory/inbox"
```

### Issue: Automation not running

**Solution**: Check cron syntax and permissions:

```bash
# Edit crontab
crontab -e

# View cron logs
grep CRON /var/log/syslog

# Ensure scripts are executable
chmod +x generate-daily-log.sh
```

### Issue: Large email files cause processing errors

**Solution**: Process emails in batches:

```bash
# Process first 10 emails only
find eml/ -name "*.eml" -type f | head -n 10 | while read file; do
    # Process $file
done
```

### Issue: Markdown formatting breaks

**Solution**: Escape special characters in prompts:

```markdown
Use proper markdown:
- Lists start with `-` or `1.`
- Headings use `##` not `**bold**`
- Code blocks use triple backticks
```

## Environment Variables

For automated workflows that might eventually integrate with APIs:

```bash
# .env file (never commit this)
ANTHROPIC_API_KEY=your_key_here
OPENAI_API_KEY=your_key_here
WORKSPACE_ROOT=/path/to/code-along
```

Reference in scripts:

```bash
#!/bin/bash
source .env
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "content-type: application/json" \
  -d @prompt.json
```

## Best Practices

1. **Keep prompts version-controlled**: Track prompt evolution in git
2. **Archive outputs by date**: Use timestamps in filenames
3. **Review before acting**: Always human-review AI-generated triage
4. **Start manual, automate later**: Prove the workflow before scripting
5. **Use consistent naming**: Follow the pattern `outputs/[workflow]-[date].md`
6. **Document your tweaks**: Add comments when you customize prompts

## Advanced: Chaining Workflows

Combine multiple workflows:

```bash
#!/bin/bash
# master-workflow.sh

# 1. Review new data
./01-data-intake-review/process.sh

# 2. Generate daily log
./02-operational-memory/generate-daily-log.sh

# 3. Triage emails
./03-offline-communications-triage/triage.sh

# 4. Update dashboard
echo "Last updated: $(date)" >> mission-control/index.html
```
