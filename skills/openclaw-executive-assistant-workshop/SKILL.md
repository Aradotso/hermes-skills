---
name: openclaw-executive-assistant-workshop
description: Local-first OpenClaw workflow patterns for data intake, operational memory, and communications triage with markdown outputs
triggers:
  - how do I build an executive assistant with OpenClaw
  - set up local OpenClaw workflows
  - create data intake review with OpenClaw
  - build operational memory system
  - triage emails offline with OpenClaw
  - generate daily logs and weekly summaries
  - turn work notes into markdown artifacts
  - practice OpenClaw local-first patterns
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This project provides a hands-on workshop for building local-first executive assistant workflows using OpenClaw. It focuses on three core patterns: data intake review, operational memory (daily/weekly logs), and offline communications triage. All workflows use local files and generate reviewable markdown artifacts without requiring live integrations.

## What This Project Does

The workshop teaches you to:

1. **Data Intake Review** — Turn unknown files into trustworthy intake reports
2. **Operational Memory** — Convert work residue into daily logs and weekly summaries
3. **Offline Communications Triage** — Process exported emails into action lists

Everything stays local. No APIs. No cloud services. Copy prompts, get markdown.

## Project Structure

```
.
├── webinar-runbook.html          # Main walkthrough guide
└── code-along/
    ├── INDEX.md
    ├── 01-data-intake-review/
    │   ├── incoming/              # Files to inspect
    │   ├── prompts/intake-review.md
    │   ├── outputs/
    │   └── expected/report-outline.md
    ├── 02-operational-memory/
    │   ├── inbox/                 # Work residue and notes
    │   ├── prompts/daily-log.md
    │   ├── prompts/weekly-hype.md
    │   ├── outputs/
    │   └── schedule/              # Cron examples
    └── 03-offline-communications-triage/
        ├── eml/                   # Exported email files
        ├── prompts/email-triage.md
        ├── outputs/
        └── expected/report-outline.md
```

## Installation

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git

# Navigate to the code-along directory
cd webinars-build-your-own-executive-assistant-with-openclaw/code-along
```

No dependencies to install. This is a prompt-based workshop using your existing OpenClaw/Claude instance.

## Workshop Flow

### Exercise 1: Data Intake Review

Process unknown files in a folder and generate an intake report.

```bash
# Navigate to the exercise
cd 01-data-intake-review

# Review the prompt
cat prompts/intake-review.md

# In your OpenClaw/Claude instance, reference:
# - The incoming/ folder contents
# - The intake-review.md prompt
# Generate: outputs/intake-review.md
```

**Typical prompt structure:**

```markdown
Review all files in the incoming/ folder and create an intake report.

For each file, provide:
- Filename
- Type/format
- Size estimate
- Content summary
- Potential actions
- Risk assessment

Output format: Markdown with clear sections.
```

**Expected output:** `outputs/intake-review.md` with structured file analysis.

### Exercise 2: Operational Memory

Turn daily work residue into momentum documents.

```bash
cd ../02-operational-memory

# Daily log generation
cat prompts/daily-log.md

# Weekly summary generation
cat prompts/weekly-hype.md
```

**Daily log pattern:**

```markdown
Review all notes in inbox/ from today.

Generate a daily-log.md with:
- **Completed**: What got done
- **In Progress**: Active threads
- **Blocked**: Waiting on what/whom
- **Tomorrow**: Next actions

Keep it concise. Use bullet points.
```

**Weekly summary pattern:**

```markdown
Review daily logs from the past 7 days.

Generate weekly-hype.md with:
- **Wins**: Completed objectives
- **Momentum**: What's moving forward
- **Attention Needed**: Blockers and decisions
- **Next Week**: Focus areas

Make it motivating and actionable.
```

**Automation example (cron):**

```bash
# Daily log at 5 PM weekdays
0 17 * * 1-5 /usr/local/bin/generate-daily-log.sh

# Weekly summary Friday at 4 PM
0 16 * * 5 /usr/local/bin/generate-weekly-hype.sh
```

### Exercise 3: Offline Communications Triage

Process exported emails into action lists.

```bash
cd ../03-offline-communications-triage

# Review the prompt
cat prompts/email-triage.md
```

**Email triage pattern:**

```markdown
Review all .eml files in the eml/ folder.

Generate email-triage.md with:

## Urgent Action Required
- [Sender] Subject - Why urgent, what to do

## Response Needed (This Week)
- [Sender] Subject - Context, suggested response

## FYI / Archive
- [Sender] Subject - One-line summary

## Spam / Ignore
- [Sender] Subject

Prioritize by impact and urgency.
```

**Expected output:** `outputs/email-triage.md` with categorized actions.

## Common Patterns

### Pattern: Batch Processing with Context

```markdown
Process all files in [folder].

Context:
- My role: [Engineering Manager]
- Current priorities: [Q1 goals, team health]
- Decision authority: [Can approve <$5k, team structure changes]

For each item, assess:
1. Relevance to current priorities
2. Required response time
3. Who should handle it
4. Next concrete action

Output: Prioritized action list in markdown.
```

### Pattern: Incremental Memory Building

```markdown
Review today's work artifacts:
- Git commits in /projects
- Notes in /inbox
- Calendar events (exported)

Previous context: [Link to yesterday's daily-log.md]

Generate updated daily-log.md maintaining:
- Thread continuity from previous days
- Cross-references to related work
- Progress indicators on multi-day tasks
```

### Pattern: Template-Based Output

Create reusable prompt templates:

```markdown
# Template: intake-review.md

Analyze files in incoming/ folder.

Output structure:
---
# Intake Review - [DATE]

## High Priority
[Files needing immediate attention]

## Standard Processing
[Regular workflow items]

## Archive/Reference
[Low priority or FYI]

## Actions
- [ ] [Concrete next steps]
---
```

## Configuration

### Folder Structure Setup

```bash
# Create a new workflow
mkdir -p my-workflow/{inbox,outputs,prompts}

# Add a prompt template
cat > my-workflow/prompts/process.md <<EOF
Review files in ../inbox/.
Generate summary in ../outputs/report.md.
EOF
```

### Environment Variables (for automation)

```bash
# .env file for scheduled workflows
OPENCLAW_WORKSPACE=/path/to/code-along
DAILY_LOG_TIME="17:00"
WEEKLY_SUMMARY_DAY="Friday"
OUTPUT_FORMAT="markdown"
```

## Troubleshooting

### Prompt Not Producing Expected Output

**Issue:** Generated report missing sections or detail.

**Solution:** Add explicit structure to prompt:

```markdown
Generate report with EXACTLY these sections:
1. Summary (3-5 sentences)
2. Details (bullet list, one per file)
3. Actions (checkbox list)

Use this exact markdown format:
## Summary
[content]

## Details
- **file.txt**: [description]
```

### File Context Too Large

**Issue:** Too many files for single prompt.

**Solution:** Batch process with index:

```markdown
Process files in incoming/ in batches of 10.

Current batch: [1-10]

Generate: outputs/intake-batch-01.md

At end, note: "Next batch: [11-20]"
```

### Output Not Saving to Correct Location

**Issue:** Generated content not in `outputs/` folder.

**Solution:** Be explicit in prompt:

```markdown
Save the generated report to:
`outputs/intake-review.md`

Do not create new folders. Use existing outputs/ directory.
```

### Lost Context Between Sessions

**Issue:** Daily logs don't reference previous work.

**Solution:** Include previous output in prompt:

```markdown
Previous daily log: [paste outputs/daily-log-2026-05-10.md]

Today's inbox: [list new files]

Generate updated daily log maintaining thread continuity.
Reference previous log items that are still active.
```

## Real-World Usage Example

```bash
#!/bin/bash
# generate-intake-review.sh

WORKSPACE="$HOME/openclaw-assistant/code-along"
EXERCISE="01-data-intake-review"
DATE=$(date +%Y-%m-%d)

cd "$WORKSPACE/$EXERCISE"

# Copy prompt to clipboard (macOS)
cat prompts/intake-review.md | pbcopy

echo "Prompt copied to clipboard."
echo "Next steps:"
echo "1. Open OpenClaw/Claude"
echo "2. Paste prompt"
echo "3. Reference incoming/ folder"
echo "4. Save output to: outputs/intake-review-$DATE.md"
```

## Mission Control Dashboard

The `mission-control/` folder is reserved for creating a simple HTML dashboard:

```html
<!DOCTYPE html>
<html>
<head><title>Assistant Dashboard</title></head>
<body>
  <h1>Executive Assistant Outputs</h1>
  <ul>
    <li><a href="../01-data-intake-review/outputs/intake-review.md">Latest Intake Review</a></li>
    <li><a href="../02-operational-memory/outputs/daily-log.md">Today's Log</a></li>
    <li><a href="../02-operational-memory/outputs/weekly-hype.md">This Week's Summary</a></li>
    <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
  </ul>
</body>
</html>
```

## Additional Resources

- Main walkthrough: Open `webinar-runbook.html` in browser
- Exercise index: `code-along/INDEX.md`
- Expected outputs: Each exercise has `expected/` folder with sample structure

This workshop emphasizes **local-first, copy-paste workflows** with **reviewable markdown artifacts**. No API keys. No cloud dependencies. Just prompts, files, and structured thinking.
