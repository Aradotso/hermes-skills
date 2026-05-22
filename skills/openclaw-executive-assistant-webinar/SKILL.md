---
name: openclaw-executive-assistant-webinar
description: Build local-first executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "help me build an executive assistant with OpenClaw"
  - "set up local-first OpenClaw workflows"
  - "create data intake review with OpenClaw"
  - "build operational memory system"
  - "triage emails offline with OpenClaw"
  - "generate daily logs and weekly summaries"
  - "how do I use OpenClaw for task automation"
  - "set up markdown-based workflow automation"
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill covers building local-first executive assistant workflows using OpenClaw patterns from the DataCamp webinar starter project. The system processes files locally, generates markdown artifacts, and maintains operational memory through structured prompts.

## What This Project Does

A workshop framework for building three core executive assistant capabilities:

1. **Data Intake Review** - Turn unknown files into trustworthy intake reports
2. **Operational Memory** - Convert work residue into daily logs and weekly summaries
3. **Offline Communications Triage** - Transform exported emails into action lists

All operations are local-only, producing reviewable markdown artifacts without live integrations.

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/intake-review.md
│   ├── outputs/intake-review.md
│   └── expected/report-outline.md
├── 02-operational-memory/
│   ├── inbox/             # Notes and work residue
│   ├── prompts/daily-log.md
│   ├── prompts/weekly-hype.md
│   ├── outputs/daily-log.md
│   ├── outputs/weekly-hype.md
│   └── schedule/cron-examples.md
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/email-triage.md
│   ├── outputs/email-triage.md
│   └── expected/report-outline.md
└── mission-control/       # Dashboard links
```

## Installation & Setup

Clone the repository:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

No dependencies required - this is a prompt-based workflow using markdown files.

## Workshop Flow

Follow the exercises in order:

1. Open `webinar-runbook.html` in a browser
2. Keep the `code-along/` folder visible in your editor
3. Execute prompts against each exercise folder
4. Review generated markdown outputs

## Exercise 1: Data Intake Review

### Purpose
Process unknown files in `incoming/` and generate a structured intake report.

### Workflow

1. Navigate to the exercise folder:
```bash
cd code-along/01-data-intake-review
```

2. Review files in `incoming/` directory

3. Open `prompts/intake-review.md` to see the prompt template

4. Feed the prompt and incoming files to your AI assistant

5. Save output to `outputs/intake-review.md`

### Expected Output Structure

The intake report should include:
- File inventory with types and sizes
- Content summaries
- Priority classifications
- Recommended actions
- Risk assessments

### Example Prompt Pattern

```markdown
You are reviewing files for an executive assistant intake process.

Files to review: [list files from incoming/]

For each file, provide:
1. Filename and type
2. Brief content summary
3. Priority level (high/medium/low)
4. Recommended next action
5. Any concerns or flags

Output format: structured markdown with sections.
```

## Exercise 2: Operational Memory

### Purpose
Transform work residue into momentum documents: daily logs and weekly summaries.

### Daily Log Workflow

1. Navigate to the exercise folder:
```bash
cd code-along/02-operational-memory
```

2. Review notes in `inbox/` directory

3. Use `prompts/daily-log.md` to generate `outputs/daily-log.md`

### Weekly Summary Workflow

1. Use accumulated daily logs
2. Apply `prompts/weekly-hype.md` prompt
3. Generate `outputs/weekly-hype.md`

### Example Daily Log Prompt

```markdown
Review today's work residue and create a daily log.

Input sources: [files from inbox/]

Generate a daily log with:
- Date
- Key accomplishments
- Decisions made
- Blocked items
- Tomorrow's priorities

Format: clean markdown with bullet points.
```

### Example Weekly Summary Prompt

```markdown
Synthesize this week's daily logs into a weekly summary.

Input: [daily logs from outputs/]

Create a weekly summary with:
- Week ending date
- Top 3 wins
- Key metrics or milestones
- Learnings or insights
- Next week's focus areas

Format: narrative markdown with emphasis on momentum.
```

### Automation with Cron

See `schedule/cron-examples.md` for automation patterns:

```bash
# Daily log generation at 6 PM
0 18 * * * cd /path/to/project && ./generate-daily-log.sh

# Weekly summary on Friday at 5 PM
0 17 * * 5 cd /path/to/project && ./generate-weekly-summary.sh
```

## Exercise 3: Offline Communications Triage

### Purpose
Process exported email files and generate actionable triage reports.

### Workflow

1. Navigate to the exercise folder:
```bash
cd code-along/03-offline-communications-triage
```

2. Export emails to `eml/` directory (`.eml` format)

3. Apply `prompts/email-triage.md` to all messages

4. Generate `outputs/email-triage.md`

### Example Triage Prompt

```markdown
Triage these exported emails and create an action list.

Input: [.eml files from eml/ directory]

For each email, extract:
- Sender and subject
- Summary (1-2 sentences)
- Required action (reply/delegate/file/delete)
- Priority (urgent/normal/low)
- Deadline (if mentioned)

Group by action type and priority.
Output: structured markdown report.
```

### Expected Output Structure

```markdown
# Email Triage Report

Generated: 2026-05-11

## Urgent Actions Required
- [Sender Name] RE: [Subject] - [Action needed] - Due: [Date]

## Normal Priority
- [Sender Name] RE: [Subject] - [Action needed]

## Low Priority / FYI
- [Sender Name] RE: [Subject] - [Action: file]

## Deletable
- [Sender Name] RE: [Subject] - [Reason]
```

## Configuration Patterns

### Environment Variables

Store sensitive paths and preferences:

```bash
export OPENCLAW_WORKSPACE="/path/to/code-along"
export OPENCLAW_OUTPUT_DIR="${OPENCLAW_WORKSPACE}/outputs"
export OPENCLAW_ARCHIVE_DIR="${OPENCLAW_WORKSPACE}/archive"
```

### Prompt Customization

All prompts are markdown files in `prompts/` directories. Customize by:

1. Copying the original prompt
2. Adjusting sections or format requirements
3. Saving with a descriptive name
4. Referencing in your workflow

## Common Patterns

### Pattern 1: File-to-Report Pipeline

```bash
# 1. Place files in appropriate incoming/ directory
# 2. Load the prompt template
# 3. Pass files and prompt to AI assistant
# 4. Save markdown output
# 5. Review and iterate if needed
```

### Pattern 2: Incremental Memory Building

```bash
# Daily: inbox/ → daily-log.md
# Weekly: daily logs → weekly-hype.md
# Monthly: weekly summaries → monthly-review.md
```

### Pattern 3: Batch Email Processing

```bash
# Export emails to .eml
# Run triage prompt on entire batch
# Generate single consolidated report
# Archive processed emails
```

## Troubleshooting

### Output Formatting Issues

**Problem**: Generated markdown is inconsistent

**Solution**: Be explicit in prompts about:
- Section headers (use `##` for subsections)
- List formatting (bullets vs numbered)
- Date formats (ISO 8601: `YYYY-MM-DD`)

### Missing Context in Reports

**Problem**: AI doesn't have enough background

**Solution**: Add context files:
```markdown
Context files to review:
- project-background.md
- stakeholder-map.md
- current-priorities.md

Then process: [actual files]
```

### Prompt Drift Over Time

**Problem**: Output quality degrades

**Solution**: Version your prompts:
```bash
prompts/
├── daily-log-v1.md
├── daily-log-v2.md  # current
└── daily-log-archive/
```

## Integration with AI Coding Agents

When using with Claude, Cursor, or similar:

1. **Project Context**: Share the entire `code-along/` directory structure
2. **Exercise Context**: Specify which exercise folder you're working in
3. **Prompt Loading**: Reference prompt files explicitly: "Use the prompt from `prompts/intake-review.md`"
4. **Output Destination**: Always specify: "Save to `outputs/[filename].md`"

## Best Practices

1. **Keep prompts in version control** - Track what works
2. **Review before archiving** - Human-in-the-loop validation
3. **Use consistent naming** - Date-stamp outputs: `daily-log-2026-05-11.md`
4. **Archive processed inputs** - Move files after successful processing
5. **Maintain a changelog** - Document prompt modifications

## Extending the System

### Adding New Workflows

Create new exercise folders following the pattern:

```
code-along/04-custom-workflow/
├── inputs/
├── prompts/
│   └── custom-prompt.md
├── outputs/
└── expected/
```

### Mission Control Dashboard

Use `mission-control/` to build a simple HTML dashboard:

```html
<!DOCTYPE html>
<html>
<head><title>Executive Assistant Dashboard</title></head>
<body>
  <h1>Today's Reports</h1>
  <ul>
    <li><a href="../01-data-intake-review/outputs/intake-review.md">Intake Review</a></li>
    <li><a href="../02-operational-memory/outputs/daily-log.md">Daily Log</a></li>
    <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
  </ul>
</body>
</html>
```
