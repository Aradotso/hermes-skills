---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows using OpenClaw for data intake, operational memory, and communication triage
triggers:
  - how do I build an executive assistant with OpenClaw
  - set up local-first OpenClaw workflow
  - create data intake review with OpenClaw
  - implement operational memory system
  - triage emails with OpenClaw
  - generate daily logs and weekly summaries
  - use OpenClaw for executive assistant tasks
  - set up offline communications triage
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill covers building local-first AI executive assistant workflows using OpenClaw. The workshop teaches three core patterns: data intake review, operational memory management, and offline communications triage. All workflows stay local, produce markdown artifacts, and require no live integrations.

## What This Workshop Covers

This is a hands-on workshop for creating an AI-powered executive assistant using OpenClaw. The three main workflows are:

1. **Data Intake Review** - Turn unknown files into trustworthy intake reports
2. **Operational Memory** - Transform work residue into daily logs and weekly summaries
3. **Offline Communications Triage** - Convert exported emails into actionable lists

All outputs are reviewable markdown files stored locally.

## Repository Structure

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
    │   ├── inbox/                 # Notes and work residue
    │   ├── prompts/daily-log.md
    │   ├── prompts/weekly-hype.md
    │   ├── outputs/
    │   └── schedule/              # Cron examples
    ├── 03-offline-communications-triage/
    │   ├── eml/                   # Exported email files
    │   ├── prompts/email-triage.md
    │   ├── outputs/
    │   └── expected/report-outline.md
    └── mission-control/           # Optional dashboard
```

## Getting Started

1. **Clone the repository**:
```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

2. **Open the walkthrough**:
```bash
open webinar-runbook.html
# Or on Linux:
xdg-open webinar-runbook.html
```

3. **Navigate to the code-along folder**:
```bash
cd code-along
```

## Workshop Flow

### Exercise 1: Data Intake Review

**Goal**: Process unknown files in `incoming/` and generate a structured intake report.

**Location**: `code-along/01-data-intake-review/`

**Steps**:
1. Review files in `incoming/` directory
2. Read the prompt template in `prompts/intake-review.md`
3. Use OpenClaw to analyze the files
4. Generate `outputs/intake-review.md`

**Expected Output Structure**:
```markdown
# Data Intake Review

## Summary
Brief overview of files processed

## Files Analyzed
- filename1.ext - description
- filename2.ext - description

## Key Findings
- Finding 1
- Finding 2

## Recommended Actions
1. Action item 1
2. Action item 2

## Risk Assessment
Any concerns or red flags
```

**Typical Prompt Pattern**:
```markdown
Please review all files in the incoming/ directory and create a comprehensive
intake report. For each file, identify:
- File type and format
- Primary content/purpose
- Data quality assessment
- Recommended handling

Output should be saved to outputs/intake-review.md
```

### Exercise 2: Operational Memory

**Goal**: Transform daily work residue into momentum documents.

**Location**: `code-along/02-operational-memory/`

**Steps**:
1. Review notes and fragments in `inbox/`
2. Generate daily log using `prompts/daily-log.md`
3. Generate weekly summary using `prompts/weekly-hype.md`
4. Review cron automation examples in `schedule/cron-examples.md`

**Daily Log Output** (`outputs/daily-log.md`):
```markdown
# Daily Log - [DATE]

## Completed Today
- Task 1
- Task 2

## In Progress
- Project A - status
- Project B - status

## Blockers
- Blocker 1 - details

## Tomorrow's Focus
- Priority 1
- Priority 2

## Notes
Quick captures and observations
```

**Weekly Summary Output** (`outputs/weekly-hype.md`):
```markdown
# Weekly Summary - Week of [DATE]

## Wins This Week
- Major accomplishment 1
- Major accomplishment 2

## Progress Made
- Project A - milestone reached
- Project B - next steps

## Learnings
- Insight 1
- Insight 2

## Next Week's Priorities
1. Priority 1
2. Priority 2
3. Priority 3
```

**Automation Example** (`schedule/cron-examples.md`):
```bash
# Daily log generation at 5 PM
0 17 * * * cd /path/to/project && openclaw process daily-log

# Weekly summary every Friday at 4 PM
0 16 * * 5 cd /path/to/project && openclaw process weekly-summary
```

### Exercise 3: Offline Communications Triage

**Goal**: Process exported email files and create actionable triage reports.

**Location**: `code-along/03-offline-communications-triage/`

**Steps**:
1. Review exported `.eml` files in `eml/` directory
2. Use prompt from `prompts/email-triage.md`
3. Generate `outputs/email-triage.md`

**Email Triage Output Structure**:
```markdown
# Email Triage Report - [DATE]

## High Priority (Respond Today)
- **From**: sender@example.com
  **Subject**: Urgent matter
  **Action**: Draft response by EOD
  **Context**: Brief summary

## Medium Priority (Respond This Week)
- **From**: sender2@example.com
  **Subject**: Follow-up needed
  **Action**: Schedule meeting
  **Context**: Brief summary

## Low Priority (FYI / Archive)
- **From**: sender3@example.com
  **Subject**: Newsletter
  **Action**: Read when time permits

## Delegatable
- **From**: sender4@example.com
  **Subject**: Task for team
  **Delegate To**: Team member name
  **Why**: Reasoning

## Summary Stats
- Total emails processed: X
- High priority: Y
- Estimated response time needed: Z hours
```

**Typical Triage Prompt**:
```markdown
Analyze all .eml files in the eml/ directory and create a prioritized action list.

For each email, determine:
- Urgency level (High/Medium/Low)
- Required action
- Suggested timeline
- Whether it can be delegated

Group by priority and provide time estimates for responses.
Output to outputs/email-triage.md
```

## Common Patterns

### File-Based Workflow Pattern

All exercises follow this pattern:
1. **Input** - Raw files in an `incoming/` or `inbox/` directory
2. **Processing** - Structured prompts in `prompts/` directory
3. **Output** - Markdown artifacts in `outputs/` directory
4. **Validation** - Expected shapes in `expected/` directory

### Prompt Template Pattern

Prompts are stored as markdown files with clear instructions:

```markdown
# [Workflow Name]

## Context
[What you're processing and why]

## Instructions
1. Step one
2. Step two
3. Step three

## Output Format
[Expected structure]

## Quality Checklist
- [ ] Criterion 1
- [ ] Criterion 2
```

### Local-First Pattern

All workflows are designed to work offline:
- No API calls required during processing
- All inputs are local files
- All outputs are local markdown
- Reviewable and version-controllable

## Configuration

### Directory Setup

Ensure proper directory structure before running workflows:

```bash
# Create output directories if they don't exist
mkdir -p code-along/01-data-intake-review/outputs
mkdir -p code-along/02-operational-memory/outputs
mkdir -p code-along/03-offline-communications-triage/outputs
```

### Environment Variables

For automated workflows, configure:

```bash
# Set workspace root
export OPENCLAW_WORKSPACE="/path/to/webinars-build-your-own-executive-assistant-with-openclaw"

# Set default output directory
export OPENCLAW_OUTPUTS="${OPENCLAW_WORKSPACE}/code-along/outputs"
```

## Troubleshooting

### Missing Output Files

If generated files don't appear in `outputs/`:
- Check that the `outputs/` directory exists
- Verify write permissions on the directory
- Ensure the prompt specifies the correct output path

### Incomplete Reports

If reports are missing sections:
- Review the prompt template in `prompts/`
- Compare against the expected outline in `expected/`
- Ensure all input files are accessible
- Check that file paths in prompts are relative to the exercise directory

### Email Processing Issues

If `.eml` files aren't processing correctly:
- Verify files are valid RFC 822 format
- Check for encoding issues (UTF-8 recommended)
- Ensure filenames don't have special characters
- Try processing one file at a time first

### Automation Problems

If scheduled tasks aren't running:
- Verify cron syntax in `schedule/cron-examples.md`
- Check system cron logs: `tail -f /var/log/cron`
- Ensure full paths are used in cron commands
- Test commands manually before scheduling

## Best Practices

### Incremental Processing

Start small and iterate:
```bash
# Process one file first
# Then process a small batch
# Finally process all files
```

### Version Control Outputs

Track your generated artifacts:
```bash
git add code-along/*/outputs/*.md
git commit -m "Add generated reports for [date]"
```

### Review Before Action

Always review generated reports before acting:
- Check for hallucinations or errors
- Verify action items are realistic
- Confirm priority assessments make sense

### Customize Prompts

Adapt the starter prompts to your needs:
- Adjust urgency criteria for your context
- Add domain-specific terminology
- Include your preferred formatting styles

## Extending the Workshop

### Mission Control Dashboard

Create a central dashboard in `mission-control/`:

```markdown
# Executive Assistant Mission Control

## Today's Status
- [Daily Log](../02-operational-memory/outputs/daily-log.md)
- [Email Triage](../03-offline-communications-triage/outputs/email-triage.md)

## This Week
- [Weekly Summary](../02-operational-memory/outputs/weekly-hype.md)

## Recent Intakes
- [Latest Intake Review](../01-data-intake-review/outputs/intake-review.md)

## Quick Links
- [All Outputs](../outputs/)
- [Prompts Library](../prompts/)
```

### Custom Workflows

Add new exercise directories following the pattern:
```
code-along/04-your-workflow/
├── inputs/
├── prompts/
├── outputs/
└── expected/
```

This skill provides the foundation for building practical, local-first AI assistant workflows that prioritize privacy, reviewability, and incremental automation.
