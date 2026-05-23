---
name: openclaw-executive-assistant-webinar
description: Build local-first executive assistant workflows with OpenClaw using file-based prompts and markdown outputs
triggers:
  - create an executive assistant workflow with OpenClaw
  - process incoming files with OpenClaw triage
  - generate daily log from work notes
  - create weekly summary with OpenClaw
  - triage emails offline with OpenClaw
  - build OpenClaw data intake review
  - set up OpenClaw operational memory
  - automate communications triage locally
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This project demonstrates building a local-first executive assistant using OpenClaw workflows. It processes files, notes, and communications into structured markdown reports without requiring live integrations or external APIs.

## What It Does

The workshop teaches three core OpenClaw patterns:

1. **Data Intake Review** — Turn unknown files into trustworthy intake reports
2. **Operational Memory** — Convert work residue into daily logs and weekly summaries
3. **Offline Communications Triage** — Process exported emails into action lists

All workflows:
- Use local files only
- Generate markdown artifacts
- Follow copy/paste prompt patterns
- Produce reviewable outputs

## Project Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/           # Prompt templates
│   ├── outputs/           # Generated reports
│   └── expected/          # Expected output shapes
├── 02-operational-memory/
│   ├── inbox/             # Work notes and residue
│   ├── prompts/           # Daily and weekly prompts
│   ├── outputs/           # Generated logs
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/           # Triage prompt
│   ├── outputs/           # Generated reports
│   └── expected/          # Expected output shapes
└── mission-control/       # Optional dashboard links
```

## Workshop Flow

### Exercise 1: Data Intake Review

Process unknown files into a structured intake report.

**Input files location:**
```bash
code-along/01-data-intake-review/incoming/
```

**Prompt location:**
```bash
code-along/01-data-intake-review/prompts/intake-review.md
```

**Expected output:**
```bash
code-along/01-data-intake-review/outputs/intake-review.md
```

**Typical prompt structure:**
```markdown
# Data Intake Review

Review all files in the `incoming/` folder and generate a report that:

- Lists each file with size and type
- Summarizes content and purpose
- Identifies any action items or follow-up needed
- Flags potential concerns or blockers
- Recommends next steps

Output as structured markdown with clear sections.
```

### Exercise 2: Operational Memory

Turn work notes into momentum documents.

**Daily log workflow:**
```bash
# Input
code-along/02-operational-memory/inbox/

# Prompt
code-along/02-operational-memory/prompts/daily-log.md

# Output
code-along/02-operational-memory/outputs/daily-log.md
```

**Weekly summary workflow:**
```bash
# Prompt
code-along/02-operational-memory/prompts/weekly-hype.md

# Output
code-along/02-operational-memory/outputs/weekly-hype.md
```

**Example daily log prompt pattern:**
```markdown
# Daily Log Generator

Review all notes in `inbox/` from today and create a daily log with:

## Completed Today
- Finished tasks with context

## In Progress
- Active work streams with status

## Blocked or Waiting
- Items needing external input

## Tomorrow's Focus
- Top 3 priorities

Keep entries concise and action-oriented.
```

**Example weekly hype prompt pattern:**
```markdown
# Weekly Summary Generator

Review the week's daily logs and generate a momentum summary:

## Week Highlights
- Key accomplishments
- Major milestones

## Momentum Metrics
- Tasks completed
- Projects advanced
- Blockers resolved

## Next Week Preview
- Priorities
- Scheduled work

Celebrate wins and maintain forward energy.
```

**Automation with cron:**
```bash
# Example cron entries
# Daily log at 5pm weekdays
0 17 * * 1-5 /path/to/generate-daily-log.sh

# Weekly summary Friday at 4pm
0 16 * * 5 /path/to/generate-weekly-hype.sh
```

See `code-along/02-operational-memory/schedule/cron-examples.md` for full automation examples.

### Exercise 3: Offline Communications Triage

Process exported emails into actionable summaries.

**Input files location:**
```bash
code-along/03-offline-communications-triage/eml/
```

**Prompt location:**
```bash
code-along/03-offline-communications-triage/prompts/email-triage.md
```

**Expected output:**
```bash
code-along/03-offline-communications-triage/outputs/email-triage.md
```

**Example triage prompt:**
```markdown
# Email Triage

Process all .eml files and create an action report:

## Requires Response
- From, subject, deadline
- Suggested response approach

## FYI / No Action
- From, subject, key info

## Delegatable
- From, subject, who should handle

## Follow-Up Later
- From, subject, when to revisit

Prioritize by urgency and importance.
```

## Using OpenClaw Patterns

### Basic Workflow

1. **Prepare inputs** — Place files in the exercise's input folder
2. **Load prompt** — Open the relevant prompt from `prompts/`
3. **Run through OpenClaw** — Copy prompt and file contents to your AI assistant
4. **Review output** — Check generated markdown in `outputs/`
5. **Iterate** — Refine prompts based on results

### Key Principles

**Local-first:**
```
✓ All files stay on your machine
✓ No API calls to external services
✓ Full control over data
✓ Works offline
```

**Markdown artifacts:**
```
✓ Human-readable outputs
✓ Version control friendly
✓ Easy to review and edit
✓ Future-proof format
```

**Copy/paste prompts:**
```
✓ No code dependencies
✓ Portable across AI tools
✓ Easy to customize
✓ Transparent operation
```

## Common Patterns

### Pattern: File Review
```markdown
Scan folder X, analyze each file, output structured report to outputs/
```

### Pattern: Note Consolidation
```markdown
Read all notes in folder Y, synthesize into single summary document
```

### Pattern: Scheduled Generation
```markdown
Run prompt Z daily/weekly, append or replace output file
```

### Pattern: Multi-Stage Processing
```markdown
Stage 1: Raw intake → structured data
Stage 2: Structured data → action items
Stage 3: Action items → prioritized list
```

## Customization

### Adapting Prompts

All prompts in `prompts/*.md` are templates. Customize for your workflow:

- Change output format (bullets, tables, YAML)
- Add specific criteria (priority thresholds, keyword filters)
- Adjust tone (formal, casual, technical)
- Include custom sections

### Creating New Exercises

```bash
mkdir code-along/04-your-workflow/
cd code-along/04-your-workflow/
mkdir inputs prompts outputs expected
```

Structure follows the existing pattern:
- `inputs/` or `inbox/` — Source material
- `prompts/` — Prompt templates
- `outputs/` — Generated artifacts
- `expected/` — Reference examples

## Troubleshooting

**No output generated:**
- Verify input files exist and are readable
- Check prompt references correct folder paths
- Ensure OpenClaw has file system access

**Output quality issues:**
- Make prompts more specific
- Add examples in prompt template
- Break complex tasks into stages
- Review expected output shapes in `expected/`

**Automation not triggering:**
- Test cron syntax with `crontab -l`
- Check script permissions (`chmod +x`)
- Verify paths are absolute in cron
- Check logs for error messages

**File encoding problems:**
- Use UTF-8 for all text files
- Check .eml files aren't corrupted
- Verify special characters render correctly

## Mission Control (Optional)

The `mission-control/` folder can hold a simple dashboard linking to generated artifacts:

```html
<!-- mission-control/index.html -->
<h2>Today's Reports</h2>
<ul>
  <li><a href="../01-data-intake-review/outputs/intake-review.md">Intake Review</a></li>
  <li><a href="../02-operational-memory/outputs/daily-log.md">Daily Log</a></li>
  <li><a href="../03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
</ul>
```

## Best Practices

- **Review before acting** — Always read generated reports before taking action
- **Version control** — Commit outputs to track changes over time
- **Prompt library** — Save successful prompts as reusable templates
- **Feedback loop** — Refine prompts based on output quality
- **Privacy first** — Keep sensitive data local, never paste into cloud AI without review
