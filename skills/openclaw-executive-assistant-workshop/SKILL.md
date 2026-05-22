---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "help me build an executive assistant with OpenClaw"
  - "how do I use OpenClaw for data intake review"
  - "set up operational memory system with OpenClaw"
  - "create email triage workflow with OpenClaw"
  - "build local-first AI assistant workflows"
  - "implement OpenClaw executive assistant patterns"
  - "generate daily logs and weekly summaries with AI"
  - "automate communications triage with OpenClaw"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill provides expertise in building local-first AI executive assistant workflows using OpenClaw patterns. The workshop covers three core workflows: data intake review, operational memory systems, and offline communications triage. All workflows use local files, markdown outputs, and copy-paste prompts with no live integrations required.

## What This Project Does

The OpenClaw Executive Assistant Workshop teaches you to build three practical AI assistant workflows:

1. **Data Intake Review** — Turn unknown files into trustworthy intake reports
2. **Operational Memory** — Transform work residue into daily logs and weekly summaries
3. **Offline Communications Triage** — Convert exported emails into actionable task lists

All workflows operate on local files and produce reviewable markdown artifacts, making them auditable and version-controllable.

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/           # Prompt templates
│   ├── outputs/           # Generated reports
│   └── expected/          # Expected output shapes
├── 02-operational-memory/
│   ├── inbox/             # Notes and work residue
│   ├── prompts/           # Daily and weekly prompts
│   ├── outputs/           # Generated logs
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/           # Triage prompts
│   ├── outputs/           # Generated reports
│   └── expected/          # Expected output shapes
└── mission-control/       # Optional dashboard
```

## Installation

Clone the repository:

```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

No additional dependencies required — this is a workshop structure using standard files and folders.

## Workshop Flow

### Starting the Workshop

1. Open `webinar-runbook.html` in your browser
2. Keep the `code-along/` folder visible in your editor
3. Work through exercises sequentially
4. Copy prompts and paste into your AI assistant

## Workflow 1: Data Intake Review

**Purpose:** Inspect unknown files and generate structured intake reports.

**Location:** `code-along/01-data-intake-review/`

### File Structure

```
01-data-intake-review/
├── incoming/                    # Place files to review here
├── prompts/intake-review.md     # Review instructions
├── outputs/intake-review.md     # Generated report
└── expected/report-outline.md   # Target report structure
```

### Usage Pattern

1. Place files to review in `incoming/`
2. Read the prompt template from `prompts/intake-review.md`
3. Provide the prompt to your AI assistant along with file contents
4. Save the generated report to `outputs/intake-review.md`

### Example Prompt Structure

```markdown
# Data Intake Review

Review the files in the incoming/ directory and generate a structured report.

For each file, document:
- Filename and type
- Purpose and content summary
- Actionability (immediate, scheduled, reference, archive)
- Suggested next steps
- Risk assessment

Output format: Markdown with clear sections and bullet points.
```

### Expected Output

```markdown
# Intake Review Report
Date: 2026-05-11

## Summary
Reviewed 5 files from incoming directory.

## Files Reviewed

### file1.pdf
- **Type:** PDF document
- **Purpose:** Project proposal for Q3
- **Actionability:** Immediate - requires review by Friday
- **Next Steps:** Schedule review meeting, prepare feedback
- **Risk:** Medium - deadline sensitive

[Additional files...]

## Priority Actions
1. [Highest priority items]
2. [Medium priority items]

## Archive Candidates
- [Files that can be archived]
```

## Workflow 2: Operational Memory

**Purpose:** Convert daily work residue into momentum documents (daily logs and weekly summaries).

**Location:** `code-along/02-operational-memory/`

### File Structure

```
02-operational-memory/
├── inbox/                      # Notes and work residue
├── prompts/
│   ├── daily-log.md           # Daily log generation
│   └── weekly-hype.md         # Weekly summary generation
├── outputs/
│   ├── daily-log.md
│   └── weekly-hype.md
└── schedule/
    ├── cron-examples.md       # Automation examples
    └── heartbeat-note.md      # Heartbeat guidance
```

### Daily Log Generation

Place work notes in `inbox/`, then use the daily log prompt:

```markdown
# Daily Log Generator

Review all files in inbox/ and generate a daily log.

Include:
- Date and day of week
- Completed tasks
- In-progress work
- Blockers and questions
- Tomorrow's priorities
- Energy level and notes

Format: Structured markdown with consistent headings.
```

**Example Daily Log Output:**

```markdown
# Daily Log — Monday, May 11, 2026

## Completed Today
- Reviewed project proposals in intake queue
- Updated documentation for API v2
- Three team sync meetings

## In Progress
- Database migration planning
- Q3 budget review

## Blockers
- Waiting on legal approval for vendor contract

## Tomorrow's Priorities
1. Complete database migration plan
2. Review Q3 budget with finance
3. Send vendor contract for signatures

## Notes
Energy: 7/10. Productive morning, afternoon meetings slowed momentum.
```

### Weekly Summary Generation

At week's end, use the weekly summary prompt to synthesize daily logs:

```markdown
# Weekly Hype Generator

Review all daily logs from the past week and generate a weekly summary.

Include:
- Week date range
- Major accomplishments
- Key metrics and progress
- Challenges and learnings
- Next week's focus areas
- Wins to celebrate

Tone: Energetic and momentum-building.
```

**Example Weekly Summary:**

```markdown
# Weekly Hype — Week of May 5-11, 2026

## This Week's Wins 🎉
- Launched API v2 documentation
- Completed 12 intake reviews
- Closed 3 major project milestones

## By The Numbers
- 47 tasks completed
- 8 meetings facilitated
- 5 documents published

## Challenges & Learnings
- Database migration more complex than estimated
- Need better handoff process for vendor contracts

## Next Week's Focus
1. Complete database migration
2. Finalize Q3 budget
3. Launch new onboarding workflow

## Momentum Notes
Strong week overall. Documentation efforts paying off with reduced support requests.
```

### Automation with Cron

Schedule automatic log generation:

```bash
# Daily log at 5 PM weekdays
0 17 * * 1-5 cd /path/to/code-along/02-operational-memory && ./generate-daily-log.sh

# Weekly summary at 4 PM Friday
0 16 * * 5 cd /path/to/code-along/02-operational-memory && ./generate-weekly-summary.sh
```

## Workflow 3: Offline Communications Triage

**Purpose:** Process exported emails into actionable task lists.

**Location:** `code-along/03-offline-communications-triage/`

### File Structure

```
03-offline-communications-triage/
├── eml/                        # Exported .eml files
├── prompts/email-triage.md     # Triage instructions
├── outputs/email-triage.md     # Generated report
└── expected/report-outline.md  # Target structure
```

### Usage Pattern

1. Export emails as `.eml` files to the `eml/` directory
2. Use the triage prompt with your AI assistant
3. Generate actionable task list in `outputs/email-triage.md`

### Example Triage Prompt

```markdown
# Email Triage

Process all .eml files and generate a triage report.

For each email:
- Sender and subject
- Priority level (urgent, high, normal, low)
- Category (action, info, decision, waiting)
- Suggested response or action
- Deadline if applicable

Output: Prioritized action list grouped by category.
```

### Expected Triage Output

```markdown
# Email Triage Report
Date: 2026-05-11
Emails Processed: 23

## Urgent Actions (Respond Today)

### 1. Project approval needed
- **From:** jane.smith@company.com
- **Subject:** Q3 Budget requires sign-off by EOD
- **Action:** Review budget spreadsheet and approve or request changes
- **Deadline:** Today 5 PM

### 2. Client escalation
- **From:** support@client.com
- **Subject:** Production issue affecting 500 users
- **Action:** Coordinate with engineering team, provide status update
- **Deadline:** Within 2 hours

## High Priority (This Week)

### 3. Vendor contract review
- **From:** legal@company.com
- **Subject:** New vendor agreement for review
- **Action:** Review 5-page contract, provide feedback
- **Deadline:** Friday

## Informational (No Action Required)

### 4. Team newsletter
- **From:** comms@company.com
- **Subject:** Monthly team updates
- **Action:** Read when available

## Waiting On Others

### 5. Budget approval
- **From:** finance@company.com
- **Subject:** Re: Q2 expense report
- **Action:** Already submitted, waiting for finance approval

## Summary
- 2 urgent items requiring immediate action
- 5 high-priority items for this week
- 10 informational messages
- 6 items waiting on others
```

## Common Patterns

### Pattern 1: Morning Intake Ritual

```bash
# 1. Process overnight emails
cd code-along/03-offline-communications-triage
# Export new emails to eml/
# Run triage prompt with AI assistant

# 2. Review yesterday's work
cd ../02-operational-memory
# Add notes to inbox/
# Generate daily log

# 3. Check for new data
cd ../01-data-intake-review
# Add new files to incoming/
# Run intake review
```

### Pattern 2: Weekly Review

```bash
# Friday afternoon workflow
cd code-along/02-operational-memory
# Generate weekly summary from daily logs
# Archive processed inbox/ files
# Set priorities for next week in new inbox note
```

### Pattern 3: Project Handoff

```bash
# When receiving project materials
cd code-along/01-data-intake-review
# Add all project files to incoming/
# Run intake review
# Use generated report for kickoff meeting
```

## Configuration

### Customizing Prompts

Edit prompt templates in each workflow's `prompts/` directory:

```markdown
# In prompts/daily-log.md, customize sections:

Include:
- Date and day of week
- Completed tasks
- In-progress work
- [ADD YOUR CUSTOM SECTIONS HERE]
- Tomorrow's priorities

[ADD YOUR CUSTOM INSTRUCTIONS HERE]
```

### Directory Conventions

Maintain consistent naming:

- `incoming/` or `inbox/` — input files
- `prompts/` — prompt templates
- `outputs/` — generated artifacts
- `expected/` — reference examples

### Output Formatting

Standard markdown conventions:

```markdown
# Main Title — Include Date

## Section Headings

### Subsection Headings

- Bullet points for lists
- **Bold** for emphasis
- Clear line breaks between sections
```

## Troubleshooting

### Issue: Generated reports lack structure

**Solution:** Be explicit in prompts about required sections and formatting:

```markdown
Generate report with EXACTLY these sections:
1. Summary
2. Priority Actions
3. Reference Items
4. Archive Candidates

Use markdown headings (##) for each section.
```

### Issue: Daily logs too verbose

**Solution:** Add length constraints to prompts:

```markdown
Keep each section to 3-5 bullet points maximum.
Focus on actionable items only.
Omit routine/expected tasks.
```

### Issue: Email triage missing deadlines

**Solution:** Enhance triage prompt:

```markdown
For EVERY email, check for:
- Explicit deadlines (dates, times)
- Implicit urgency (words like "ASAP", "urgent")
- Sender importance (executives, clients)

Mark deadline as "Not specified" if none found.
```

### Issue: Workflow automation fails

**Solution:** Check file paths and permissions:

```bash
# Verify script paths
ls -la /path/to/code-along/02-operational-memory/*.sh

# Ensure execute permissions
chmod +x generate-daily-log.sh

# Test cron job manually
cd /path/to/code-along/02-operational-memory && ./generate-daily-log.sh
```

### Issue: AI assistant produces inconsistent outputs

**Solution:** Use reference examples:

```markdown
Generate output matching this structure EXACTLY:

[Paste content from expected/report-outline.md]

Maintain the same:
- Heading hierarchy
- Section order
- Bullet style
- Tone
```

## Advanced Patterns

### Multi-Stage Processing

Chain workflows for complex scenarios:

```bash
# Stage 1: Intake unknown files
cd code-along/01-data-intake-review
# Run intake review, identify emails

# Stage 2: Extract and triage emails
cd ../03-offline-communications-triage
# Move identified emails to eml/
# Run email triage

# Stage 3: Log outcomes
cd ../02-operational-memory
# Add triage results to inbox/
# Generate daily log including triage summary
```

### Environment-Specific Configurations

Use environment variables for paths:

```bash
export OPENCLAW_BASE="${HOME}/openclaw-workshop"
export INTAKE_DIR="${OPENCLAW_BASE}/code-along/01-data-intake-review"
export MEMORY_DIR="${OPENCLAW_BASE}/code-along/02-operational-memory"
export TRIAGE_DIR="${OPENCLAW_BASE}/code-along/03-offline-communications-triage"

# Reference in scripts
cd "${INTAKE_DIR}" && process-intake.sh
```

### Archive Strategy

Implement rotation to prevent clutter:

```bash
# Archive processed files weekly
ARCHIVE_DATE=$(date +%Y-%m-%d)
mkdir -p archive/${ARCHIVE_DATE}
mv outputs/*.md archive/${ARCHIVE_DATE}/
mv inbox/*.* archive/${ARCHIVE_DATE}/
```

## Mission Control Dashboard

Optional central dashboard linking all artifacts:

```markdown
# Mission Control

Last updated: 2026-05-11 17:30

## Today's Status
- [Daily Log](02-operational-memory/outputs/daily-log.md)
- [Latest Intake](01-data-intake-review/outputs/intake-review.md)
- [Email Triage](03-offline-communications-triage/outputs/email-triage.md)

## This Week
- [Weekly Summary](02-operational-memory/outputs/weekly-hype.md)

## Quick Links
- [All Daily Logs](02-operational-memory/outputs/)
- [All Intake Reports](01-data-intake-review/outputs/)
- [All Triage Reports](03-offline-communications-triage/outputs/)
```

This skill enables AI coding agents to guide developers through building practical, local-first executive assistant workflows using OpenClaw patterns.
