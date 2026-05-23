---
name: openclaw-executive-assistant-webinar
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and email triage
triggers:
  - build an executive assistant with openclaw
  - set up openclaw data intake workflow
  - create operational memory system with openclaw
  - triage emails with openclaw locally
  - implement openclaw daily logs
  - configure openclaw weekly summaries
  - use openclaw for communications management
  - build local-first assistant workflows
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This project provides starter files and workflows for building a local-first executive assistant using OpenClaw. It teaches three core patterns: data intake review, operational memory management, and offline communications triage. All workflows use local files only, producing reviewable markdown artifacts without requiring live integrations.

## What This Project Does

The webinar teaches how to build three AI assistant capabilities:

1. **Data intake review** — Turn unknown files into trustworthy intake reports
2. **Operational memory** — Convert work residue into daily logs and weekly summaries
3. **Offline communications triage** — Process exported emails into actionable task lists

All outputs are markdown files stored locally, making them version-controllable and human-reviewable.

## Repository Structure

```
.
├── webinar-runbook.html              # Main walkthrough guide
└── code-along/
    ├── INDEX.md                      # Overview
    ├── 01-data-intake-review/        # Exercise 1
    ├── 02-operational-memory/        # Exercise 2
    ├── 03-offline-communications-triage/  # Exercise 3
    └── mission-control/              # Optional dashboard
```

Each exercise contains:
- `incoming/` or `inbox/` or `eml/` — Input files
- `prompts/*.md` — OpenClaw prompt templates
- `outputs/` — Generated artifacts
- `expected/*.md` — Example outputs

## Getting Started

### Prerequisites

- Clone the repository:
```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

- Open `webinar-runbook.html` in a browser for the guided walkthrough
- Have an OpenClaw-compatible AI assistant ready (Claude, ChatGPT, etc.)

### Workshop Flow

1. Navigate to each exercise folder in `code-along/`
2. Review the input files in `incoming/`, `inbox/`, or `eml/`
3. Copy the prompt from `prompts/*.md`
4. Paste into your AI assistant with context of the input files
5. Save generated output to `outputs/`
6. Compare with `expected/*.md` examples

## Exercise 1: Data Intake Review

**Location:** `code-along/01-data-intake-review/`

**Goal:** Process unknown files into a structured intake report.

**Workflow:**

1. Review files in `incoming/`
2. Use the prompt template:

```markdown
# Data Intake Review Prompt

Analyze the files in the incoming/ directory and create a comprehensive intake report.

## Tasks:
1. List each file with its type and size
2. Identify the content category (document, data, media, etc.)
3. Flag any security or privacy concerns
4. Suggest appropriate actions (archive, process, delete, etc.)
5. Highlight dependencies or relationships between files

## Output Format:
Create a markdown report with sections:
- Summary
- File Inventory
- Risk Assessment
- Recommended Actions
```

3. Generate `outputs/intake-review.md`

**Expected Output Structure:**

```markdown
# Intake Review Report
Date: YYYY-MM-DD

## Summary
Brief overview of file batch

## File Inventory
| File | Type | Size | Category |
|------|------|------|----------|
| ... | ... | ... | ... |

## Risk Assessment
- Security concerns
- Privacy considerations

## Recommended Actions
- [ ] Action item 1
- [ ] Action item 2
```

## Exercise 2: Operational Memory

**Location:** `code-along/02-operational-memory/`

**Goal:** Transform work notes into daily logs and weekly summaries.

**Workflow:**

### Daily Log

1. Review notes in `inbox/`
2. Use `prompts/daily-log.md`:

```markdown
# Daily Log Prompt

Convert today's work residue into a structured daily log.

## Input:
Review all files in inbox/ from today

## Tasks:
1. Extract completed tasks
2. Identify in-progress work
3. Note blockers or questions
4. Capture decisions made
5. List tomorrow's priorities

## Output:
Create outputs/daily-log.md with:
- Date header
- Completed section
- In Progress section
- Blockers section
- Tomorrow section
```

3. Generate `outputs/daily-log.md`

### Weekly Summary

1. Review multiple daily logs
2. Use `prompts/weekly-hype.md`:

```markdown
# Weekly Hype Prompt

Synthesize the week's daily logs into an energizing weekly summary.

## Tasks:
1. List major accomplishments
2. Highlight momentum and wins
3. Identify patterns or themes
4. Note learning moments
5. Set intentions for next week

## Tone:
Positive, momentum-building, celebration-focused

## Output:
Create outputs/weekly-hype.md
```

3. Generate `outputs/weekly-hype.md`

### Automation Example

The exercise includes `schedule/cron-examples.md` for scheduling:

```bash
# Generate daily log at 5pm weekdays
0 17 * * 1-5 cd /path/to/code-along/02-operational-memory && \
  ai-assistant --prompt prompts/daily-log.md --output outputs/daily-log-$(date +\%Y-\%m-\%d).md

# Generate weekly summary Friday 4pm
0 16 * * 5 cd /path/to/code-along/02-operational-memory && \
  ai-assistant --prompt prompts/weekly-hype.md --output outputs/weekly-hype-$(date +\%Y-\%W).md
```

## Exercise 3: Offline Communications Triage

**Location:** `code-along/03-offline-communications-triage/`

**Goal:** Process exported emails into actionable task lists.

**Workflow:**

1. Review `.eml` files in `eml/`
2. Use `prompts/email-triage.md`:

```markdown
# Email Triage Prompt

Process exported emails and create an action-oriented triage report.

## Input:
All .eml files in eml/ directory

## Tasks:
1. Extract sender, subject, date for each email
2. Classify urgency (high/medium/low)
3. Identify required actions
4. Note any deadlines
5. Flag threads requiring multiple responses
6. Detect delegation opportunities

## Output:
Create outputs/email-triage.md with:
- High Priority section
- Medium Priority section
- Low Priority / FYI section
- Action items with owners
```

3. Generate `outputs/email-triage.md`

**Expected Output Structure:**

```markdown
# Email Triage Report
Generated: YYYY-MM-DD HH:MM

## High Priority
- [ ] **Subject**: Action required by [date]
  - From: sender@example.com
  - Action: Specific next step
  - Deadline: YYYY-MM-DD

## Medium Priority
...

## Low Priority / FYI
...

## Delegation Opportunities
- Item suitable for delegation
```

## Configuration Patterns

### Prompt Engineering Tips

1. **Be specific about output format** — Include markdown templates in prompts
2. **Set context boundaries** — Specify which folders to analyze
3. **Define tone and style** — Especially for weekly summaries
4. **Include examples** — Show the AI what "good" looks like
5. **Request structured data** — Tables, checklists, and headers improve scannability

### File Organization

```
your-project/
├── prompts/           # Reusable prompt templates
├── inbox/            # Input staging area
├── outputs/          # Generated artifacts (gitignore recommended)
└── archive/          # Processed items
```

### Environment Variables

For extended workflows that might integrate APIs:

```bash
export OPENCLAW_WORKSPACE="/path/to/code-along"
export OPENCLAW_OUTPUT_DIR="$OPENCLAW_WORKSPACE/outputs"
export OPENCLAW_ARCHIVE_DIR="$OPENCLAW_WORKSPACE/archive"
```

## Common Patterns

### Batch Processing Pattern

```markdown
# Prompt Template
Process all files in [directory] and generate a report in outputs/

For each file:
1. Action item 1
2. Action item 2

Output format: markdown with sections A, B, C
```

### Incremental Memory Pattern

```markdown
# Prompt Template
Review outputs/daily-log-*.md from the past 7 days.

Synthesize into a weekly summary focusing on:
- Patterns
- Progress
- Priorities
```

### Triage Classification Pattern

```markdown
# Prompt Template
Classify each item as:
- 🔴 Urgent/Important
- 🟡 Important/Not Urgent
- 🟢 Nice to Have
- ⚪ FYI Only

For each category, list actionable items.
```

## Troubleshooting

### AI Assistant Not Finding Files

**Problem:** Assistant cannot access local files

**Solution:** Explicitly list file paths or paste file contents into the prompt when using web-based AI tools

### Inconsistent Output Format

**Problem:** Generated markdown varies in structure

**Solution:** Include explicit markdown templates in prompts:

```markdown
## Output Template:
# Report Title
## Section 1
- Item

## Section 2
| Column | Column |
|--------|--------|
```

### Missing Context Between Exercises

**Problem:** Each exercise feels disconnected

**Solution:** Reference previous outputs in later prompts:

```markdown
Review outputs/intake-review.md from Exercise 1 and 
outputs/daily-log.md from Exercise 2, then...
```

### Outputs Not Showing Progress Over Time

**Problem:** Hard to track improvements

**Solution:** Use date-stamped output files:

```bash
outputs/daily-log-2026-05-11.md
outputs/weekly-hype-2026-W19.md
```

## Mission Control (Optional)

The `mission-control/` directory is a placeholder for building a simple dashboard that links to all generated artifacts. Example structure:

```markdown
# Mission Control Dashboard

## Today's Focus
[Link to latest daily log](../02-operational-memory/outputs/daily-log.md)

## Recent Intake
[Link to latest intake review](../01-data-intake-review/outputs/intake-review.md)

## Email Actions
[Link to latest triage](../03-offline-communications-triage/outputs/email-triage.md)

## This Week
[Link to weekly summary](../02-operational-memory/outputs/weekly-hype.md)
```

## Best Practices

1. **Keep prompts in version control** — Iterate on prompt quality over time
2. **Review all AI outputs** — These are assistants, not autopilot
3. **Use consistent naming** — Date-stamped outputs enable chronological review
4. **Archive processed inputs** — Keep inbox/ clean after generating reports
5. **Iterate on prompts** — Compare outputs with expected/ examples and refine
6. **Start manual, automate later** — Validate workflows before scheduling cron jobs
