---
name: openclaw-executive-assistant-workflows
description: Build local-first executive assistant workflows using OpenClaw for data intake, operational memory, and communications triage
triggers:
  - set up an openclaw executive assistant workflow
  - create a local data intake review system
  - build an operational memory system with openclaw
  - triage emails offline with openclaw
  - generate daily logs and weekly summaries
  - review incoming files with openclaw prompts
  - set up markdown-based executive assistant
  - create openclaw communication workflows
---

# OpenClaw Executive Assistant Workflows

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

This project provides a local-first executive assistant framework using OpenClaw workflows. It demonstrates three core patterns: data intake review, operational memory management, and offline communications triage. All workflows stay local, use markdown artifacts, and follow a copy/paste prompt methodology.

The system is designed for manual execution with AI assistants, producing reviewable markdown outputs without requiring live integrations or API connections.

## Installation

Clone the repository:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

No additional dependencies required. The project uses:
- Local file system
- Markdown for all outputs
- Manual prompt execution with AI assistants (Claude, ChatGPT, etc.)

## Repository Structure

```
code-along/
├── INDEX.md                              # Overview of exercises
├── 01-data-intake-review/
│   ├── incoming/                         # Files to inspect
│   ├── prompts/intake-review.md          # Review prompt
│   ├── outputs/                          # Generated reports
│   └── expected/report-outline.md        # Expected output format
├── 02-operational-memory/
│   ├── inbox/                            # Work notes and residue
│   ├── prompts/daily-log.md              # Daily logging prompt
│   ├── prompts/weekly-hype.md            # Weekly summary prompt
│   ├── outputs/                          # Generated logs
│   └── schedule/                         # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/                              # Exported email files
│   ├── prompts/email-triage.md           # Triage prompt
│   ├── outputs/                          # Triage reports
│   └── expected/report-outline.md        # Expected output format
└── mission-control/                      # Optional dashboard area
```

## Core Workflows

### 1. Data Intake Review

Turn unknown files into a structured intake report.

**Workflow:**

1. Place files in `code-along/01-data-intake-review/incoming/`
2. Read the prompt from `prompts/intake-review.md`
3. Execute the prompt with your AI assistant, referencing the incoming files
4. Save output to `outputs/intake-review.md`

**Expected Output Structure:**

```markdown
# Data Intake Review
Date: YYYY-MM-DD

## Summary
Brief overview of files reviewed.

## Files Analyzed
- filename1.ext - description and classification
- filename2.ext - description and classification

## Actions Required
- [ ] Action item 1
- [ ] Action item 2

## Notes
Additional context or observations.
```

### 2. Operational Memory

Transform daily work residue into momentum documentation.

**Daily Log Workflow:**

1. Drop notes, links, or snippets into `code-along/02-operational-memory/inbox/`
2. Use the prompt from `prompts/daily-log.md`
3. Generate `outputs/daily-log.md`

**Daily Log Format:**

```markdown
# Daily Log - YYYY-MM-DD

## What Shipped
- Completed item 1
- Completed item 2

## In Progress
- Active work item 1
- Active work item 2

## Blockers
- Blocker description

## Tomorrow's Focus
- Priority 1
- Priority 2
```

**Weekly Hype Workflow:**

1. Collect daily logs from the week
2. Use the prompt from `prompts/weekly-hype.md`
3. Generate `outputs/weekly-hype.md`

**Weekly Summary Format:**

```markdown
# Weekly Hype - Week of YYYY-MM-DD

## Highlights
Top achievements and wins from the week.

## Momentum Builders
Patterns of progress and positive trends.

## Attention Needed
Items requiring focus next week.

## Metrics
Quantifiable progress indicators.
```

**Automation Example:**

The `schedule/cron-examples.md` includes patterns for automated execution:

```bash
# Daily log generation at 5 PM
0 17 * * * cd /path/to/project && /path/to/generate-daily-log.sh

# Weekly summary on Friday at 4 PM
0 16 * * 5 cd /path/to/project && /path/to/generate-weekly-hype.sh
```

### 3. Offline Communications Triage

Process exported emails into actionable lists.

**Workflow:**

1. Export emails to `.eml` format in `code-along/03-offline-communications-triage/eml/`
2. Read the prompt from `prompts/email-triage.md`
3. Execute with AI assistant
4. Save to `outputs/email-triage.md`

**Expected Triage Output:**

```markdown
# Email Triage Report
Date: YYYY-MM-DD

## High Priority
- [ ] Sender Name - Subject - Required action
- [ ] Sender Name - Subject - Required action

## Medium Priority
- [ ] Sender Name - Subject - Action description

## Low Priority / FYI
- Sender Name - Subject - No action needed

## Deferred
Items to revisit later with context.

## Archive
No action required.
```

## Common Patterns

### Pattern 1: Manual Prompt Execution

```markdown
# In your AI assistant:

1. Load context: "Read all files in code-along/01-data-intake-review/incoming/"
2. Load prompt: "Now read code-along/01-data-intake-review/prompts/intake-review.md"
3. Execute: "Apply this prompt to the incoming files"
4. Save: Copy output to code-along/01-data-intake-review/outputs/intake-review.md
```

### Pattern 2: Iterative Refinement

```markdown
# Initial pass:
"Generate the intake review report"

# Refinement:
"Add a risk assessment section"
"Prioritize items by urgency"
"Include file size and type metadata"
```

### Pattern 3: Cross-Exercise Integration

```markdown
# Combine workflows:
"Take action items from email-triage.md and add them to today's daily-log.md"

# Build continuity:
"Reference yesterday's daily-log.md blockers and update status in today's log"
```

## Configuration

No configuration files needed. Customize by editing prompt files:

- `prompts/intake-review.md` - Adjust review criteria
- `prompts/daily-log.md` - Modify daily structure
- `prompts/weekly-hype.md` - Change summary format
- `prompts/email-triage.md` - Alter triage categories

## Troubleshooting

**Issue:** Output doesn't match expected format

```markdown
Solution: Reference the expected/ outline files explicitly in your prompt:
"Generate the report following the structure in expected/report-outline.md"
```

**Issue:** AI assistant can't access local files

```markdown
Solution: Copy file contents into the chat explicitly:
"Here are the contents of the incoming files: [paste contents]"
```

**Issue:** Missing context between sessions

```markdown
Solution: Build a context file:
"Summarize today's session in code-along/mission-control/context.md for next time"
```

**Issue:** Outputs are too generic

```markdown
Solution: Add specific constraints to prompts:
"Use bullet points, not paragraphs"
"Include specific file names and line numbers"
"Add emoji markers for priority levels"
```

## Best Practices

1. **Version control outputs**: Commit generated markdown to track evolution
2. **Date-stamp everything**: Include ISO dates in filenames and headers
3. **Keep prompts atomic**: One clear purpose per prompt file
4. **Review before archiving**: Manually verify AI outputs before moving to archive
5. **Build prompt libraries**: Save successful prompt variations for reuse
6. **Link artifacts**: Cross-reference between daily logs, reviews, and triage reports

## Extension Ideas

```markdown
# Add to mission-control/:

# dashboard.md
- [ ] [Today's Daily Log](../02-operational-memory/outputs/daily-log.md)
- [ ] [Latest Intake Review](../01-data-intake-review/outputs/intake-review.md)
- [ ] [Email Triage](../03-offline-communications-triage/outputs/email-triage.md)

# Create templated prompts with placeholders:
"Generate a {{FREQUENCY}} {{DOCUMENT_TYPE}} covering {{DATE_RANGE}}"
```

This skill enables AI coding agents to help developers build and maintain local-first executive assistant workflows using the OpenClaw methodology.
