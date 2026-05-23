---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows using OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "set up OpenClaw executive assistant"
  - "create local AI workflow with OpenClaw"
  - "build data intake review system"
  - "implement operational memory with daily logs"
  - "triage emails offline with AI"
  - "create executive assistant automation"
  - "set up local-first AI workflows"
  - "build OpenClaw code-along exercises"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

This workshop teaches you to build local-first AI executive assistant workflows using OpenClaw. The system processes files, notes, and communications entirely locally, producing reviewable markdown artifacts without cloud integrations.

**Core workflows:**
1. **Data intake review** - Turn unknown files into structured reports
2. **Operational memory** - Convert work residue into daily/weekly summaries
3. **Offline communications triage** - Transform exported emails into action lists

All outputs are markdown files that stay in your local filesystem.

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/           # Files to inspect
│   ├── prompts/
│   │   └── intake-review.md
│   ├── outputs/
│   └── expected/
│       └── report-outline.md
├── 02-operational-memory/
│   ├── inbox/              # Notes and work residue
│   ├── prompts/
│   │   ├── daily-log.md
│   │   └── weekly-hype.md
│   ├── outputs/
│   └── schedule/
│       ├── cron-examples.md
│       └── heartbeat-note.md
├── 03-offline-communications-triage/
│   ├── eml/                # Exported email files
│   ├── prompts/
│   │   └── email-triage.md
│   ├── outputs/
│   └── expected/
│       └── report-outline.md
└── mission-control/        # Optional dashboard
```

## Installation

Clone the repository:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

**No additional dependencies required.** This is a prompt-driven workflow using your AI assistant of choice.

## Workflow 1: Data Intake Review

Transform unknown files into a trustworthy intake report.

### Setup

```bash
cd code-along/01-data-intake-review
ls incoming/  # View files to process
```

### Prompt Pattern

Read the prompt template:

```bash
cat prompts/intake-review.md
```

Provide to your AI agent with context:

```
Review all files in code-along/01-data-intake-review/incoming/ and create a structured intake report. Follow the prompt in prompts/intake-review.md. Output to outputs/intake-review.md.
```

### Expected Output

`outputs/intake-review.md` should contain:
- File inventory with types and sizes
- Content summary for each file
- Risk assessment
- Recommended actions
- Priority ranking

### Code Example

If automating with a script:

```html
<!-- Example: Reading prompt and generating report -->
<!DOCTYPE html>
<html>
<head><title>Intake Review</title></head>
<body>
  <h1>Data Intake Report</h1>
  <script>
    // Read files from incoming/
    const files = ['document1.pdf', 'spreadsheet.xlsx', 'notes.txt'];
    
    // Process each file
    files.forEach(file => {
      console.log(`Processing: ${file}`);
      // AI assistant analyzes file content
      // Generates markdown section
    });
    
    // Compile to outputs/intake-review.md
  </script>
</body>
</html>
```

## Workflow 2: Operational Memory

Convert daily work residue into momentum documents.

### Daily Log

```bash
cd code-along/02-operational-memory
```

Create daily summary:

```
Review all notes in code-along/02-operational-memory/inbox/ and create a daily log following prompts/daily-log.md. Output to outputs/daily-log.md.
```

**Daily log includes:**
- Tasks completed
- Decisions made
- Blockers identified
- Tomorrow's priorities

### Weekly Summary

```
Using the daily logs and inbox notes, create a weekly summary following prompts/weekly-hype.md. Output to outputs/weekly-hype.md.
```

**Weekly hype includes:**
- Week's wins
- Momentum indicators
- Pattern recognition
- Next week's focus

### Automation with Cron

Set up scheduled processing:

```bash
# Review cron examples
cat schedule/cron-examples.md
```

Example cron pattern for daily 6 PM review:

```bash
0 18 * * * cd ~/executive-assistant/02-operational-memory && ai-agent --prompt prompts/daily-log.md --output outputs/daily-log-$(date +\%Y-\%m-\%d).md
```

Example heartbeat check:

```bash
cat schedule/heartbeat-note.md
```

## Workflow 3: Offline Communications Triage

Transform exported emails into actionable lists.

### Setup

```bash
cd code-along/03-offline-communications-triage
ls eml/  # View exported .eml files
```

### Email Export Process

Export emails from your client as `.eml` files:
- **Gmail**: Select emails → More → Download message
- **Outlook**: Drag email to filesystem
- **Apple Mail**: File → Save As

Place in `eml/` directory.

### Triage Prompt

```
Analyze all .eml files in code-along/03-offline-communications-triage/eml/ and create a triage report following prompts/email-triage.md. Output to outputs/email-triage.md.
```

### Expected Output Structure

`outputs/email-triage.md` contains:

```markdown
# Email Triage Report

## Urgent Actions Required
- [ ] Response needed: Project deadline (from: john@example.com)
- [ ] Approval needed: Budget review (from: finance@example.com)

## Follow-ups This Week
- [ ] Schedule meeting: Q4 planning
- [ ] Send information: Client onboarding docs

## FYI / Archive
- Newsletter: Industry trends
- Notification: Calendar update

## Delegatable
- [ ] Assign to team: Support ticket #1234

## Low Priority / Later
- Conference invitation (Next month)
```

## Mission Control Dashboard

Optional: Create a central view of all artifacts.

```bash
cd code-along/mission-control
```

### Example Dashboard HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Executive Assistant Mission Control</title>
  <style>
    body { font-family: system-ui; max-width: 1200px; margin: 2rem auto; padding: 0 1rem; }
    .section { margin: 2rem 0; padding: 1rem; border: 1px solid #ddd; border-radius: 8px; }
    h2 { color: #333; }
    ul { list-style: none; padding: 0; }
    li { margin: 0.5rem 0; }
    a { color: #0066cc; text-decoration: none; }
    a:hover { text-decoration: underline; }
  </style>
</head>
<body>
  <h1>📋 Mission Control</h1>
  
  <div class="section">
    <h2>📥 Data Intake</h2>
    <ul>
      <li><a href="../01-data-intake-review/outputs/intake-review.md">Latest Intake Report</a></li>
      <li><a href="../01-data-intake-review/incoming/">Pending Files</a></li>
    </ul>
  </div>
  
  <div class="section">
    <h2>🧠 Operational Memory</h2>
    <ul>
      <li><a href="../02-operational-memory/outputs/daily-log.md">Today's Log</a></li>
      <li><a href="../02-operational-memory/outputs/weekly-hype.md">This Week's Summary</a></li>
      <li><a href="../02-operational-memory/inbox/">Work Inbox</a></li>
    </ul>
  </div>
  
  <div class="section">
    <h2>📧 Communications</h2>
    <ul>
      <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage Report</a></li>
      <li><a href="../03-offline-communications-triage/eml/">Email Archive</a></li>
    </ul>
  </div>
</body>
</html>
```

Open in browser:

```bash
open mission-control/dashboard.html
```

## Best Practices

### File Organization

Keep inputs and outputs separated:
```
exercise/
├── incoming/    # Raw input files
├── prompts/     # Reusable prompt templates
├── outputs/     # Generated artifacts
└── expected/    # Reference examples
```

### Prompt Templates

Structure prompts for consistency:

```markdown
# Task: [Clear objective]

## Context
[What the AI needs to know]

## Input
[Where to find source files]

## Output Format
[Specific structure required]

## Examples
[Show expected patterns]

## Rules
- [Constraint 1]
- [Constraint 2]
```

### Incremental Processing

Process in batches rather than bulk:
```bash
# Daily: Process last 24 hours
# Weekly: Roll up daily logs
# Monthly: Archive and reset
```

### Review Before Action

All outputs are markdown for human review:
```bash
# Review before committing to actions
cat outputs/email-triage.md
# Edit if needed
vim outputs/email-triage.md
```

## Troubleshooting

### Files Not Processing

Check file permissions:
```bash
ls -la incoming/
chmod 644 incoming/*
```

### Missing Output

Verify output directory exists:
```bash
mkdir -p outputs
```

### Prompt Not Working

Compare against expected output:
```bash
diff outputs/intake-review.md expected/report-outline.md
```

### Encoding Issues with .eml Files

Convert encoding if needed:
```bash
iconv -f ISO-8859-1 -t UTF-8 eml/message.eml > eml/message-utf8.eml
```

## Advanced Patterns

### Chained Workflows

Link exercises together:
```
1. Intake review identifies priority items
2. Add priority items to operational memory inbox
3. Daily log captures progress on priority items
4. Weekly hype summarizes completion
```

### Custom Prompts

Extend with domain-specific prompts:
```bash
cp prompts/daily-log.md prompts/project-standup.md
# Edit for project-specific needs
```

### Archive Strategy

```bash
# Archive completed work monthly
mkdir -p archive/$(date +%Y-%m)
mv outputs/*.md archive/$(date +%Y-%m)/
```

## Integration Ideas

While this workshop is local-first, you can integrate outputs:

- **Task managers**: Parse action items into Todoist/Notion
- **Note systems**: Import markdown into Obsidian/Roam
- **Version control**: Git commit outputs for history
- **Automation**: Trigger workflows via folder watchers

Example git workflow:
```bash
git add outputs/*.md
git commit -m "Daily log $(date +%Y-%m-%d)"
git push
```

## Resources

- **Workshop HTML**: `webinar-runbook.html` (full walkthrough)
- **Index**: `code-along/INDEX.md` (exercise overview)
- **Cron examples**: `code-along/02-operational-memory/schedule/cron-examples.md`
