---
name: openclaw-executive-assistant-webinar
description: Build local-first AI executive assistant workflows with OpenClaw for data intake, operational memory, and communications triage
triggers:
  - "set up openclaw executive assistant workflow"
  - "create local AI assistant with openclaw"
  - "build data intake review system"
  - "set up operational memory logs"
  - "configure email triage with openclaw"
  - "implement offline communications workflow"
  - "create executive assistant automation"
  - "process incoming files with AI locally"
---

# OpenClaw Executive Assistant Webinar

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill covers building local-first AI executive assistant workflows using OpenClaw. The project demonstrates three core patterns: data intake review, operational memory management, and offline communications triage—all using local files and markdown outputs with no live integrations.

## What This Project Does

This is a workshop/starter template for practicing local-first OpenClaw workflows that act as an executive assistant. It teaches you to:

1. **Data Intake Review** — Turn unknown incoming files into trustworthy intake reports
2. **Operational Memory** — Convert work residue into daily logs and weekly summaries
3. **Offline Communications Triage** — Process exported emails into actionable lists

All operations use local folders, produce reviewable markdown artifacts, and require no API integrations beyond your local AI model.

## Repository Structure

```
.
├── webinar-runbook.html          # Main walkthrough documentation
└── code-along/
    ├── INDEX.md                  # Overview of exercises
    ├── 01-data-intake-review/
    │   ├── incoming/             # Files to inspect
    │   ├── prompts/
    │   │   └── intake-review.md
    │   ├── outputs/
    │   └── expected/
    ├── 02-operational-memory/
    │   ├── inbox/                # Work notes and residue
    │   ├── prompts/
    │   │   ├── daily-log.md
    │   │   └── weekly-hype.md
    │   ├── outputs/
    │   └── schedule/
    └── 03-offline-communications-triage/
        ├── eml/                  # Exported email files
        ├── prompts/
        │   └── email-triage.md
        ├── outputs/
        └── expected/
```

## Installation & Setup

```bash
# Clone the repository
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw

# Open the main walkthrough
open webinar-runbook.html

# Navigate to exercises
cd code-along
```

No additional dependencies required—this is a template for manual or automated workflow execution.

## Key Workflow Patterns

### 1. Data Intake Review

Process unknown incoming files and generate structured intake reports.

**Input:** Files in `01-data-intake-review/incoming/`
**Prompt:** `01-data-intake-review/prompts/intake-review.md`
**Output:** `01-data-intake-review/outputs/intake-review.md`

**Typical prompt structure:**
```markdown
Review all files in the incoming/ folder and create an intake report with:

1. File inventory (name, type, size, timestamp)
2. Content summary for each file
3. Recommended actions
4. Priority level (urgent/high/normal/low)
5. Any security or data concerns

Output as structured markdown.
```

**Use with AI coding agent:**
```bash
# Point your AI agent to the exercise folder
cd code-along/01-data-intake-review

# Ask the agent:
# "Review the files in incoming/ using the prompt in prompts/intake-review.md
#  and generate outputs/intake-review.md"
```

### 2. Operational Memory

Transform daily work notes into momentum documents.

**Daily Log Pattern:**
```markdown
# Daily Log — [DATE]

## What Got Done
- [List of completed tasks from inbox/]

## What's in Flight
- [Active work items]

## Blockers & Questions
- [Items needing attention]

## Tomorrow's Focus
- [Top 3 priorities]
```

**Weekly Summary Pattern:**
```markdown
# Weekly Hype — Week of [DATE]

## Wins
- [Major accomplishments]

## Momentum
- [Projects advancing]

## Learnings
- [Key insights]

## Next Week's Targets
- [Strategic focus areas]
```

**Example automation with cron:**
```bash
# Run daily log generation at 5 PM
0 17 * * * cd ~/projects/openclaw-assistant/code-along/02-operational-memory && \
  your-ai-tool generate --prompt prompts/daily-log.md \
  --input inbox/ --output outputs/daily-log-$(date +\%Y-\%m-\%d).md

# Run weekly summary on Friday at 4 PM
0 16 * * 5 cd ~/projects/openclaw-assistant/code-along/02-operational-memory && \
  your-ai-tool generate --prompt prompts/weekly-hype.md \
  --input outputs/ --output outputs/weekly-hype-$(date +\%Y-\%V).md
```

### 3. Offline Communications Triage

Process exported email files into action lists.

**Input:** `.eml` files in `03-offline-communications-triage/eml/`
**Prompt:** `03-offline-communications-triage/prompts/email-triage.md`
**Output:** `03-offline-communications-triage/outputs/email-triage.md`

**Expected triage structure:**
```markdown
# Email Triage — [DATE]

## Immediate Action Required
- **From:** [sender]
  **Subject:** [subject]
  **Action:** [what to do]
  **Deadline:** [if applicable]

## Responses Needed (This Week)
- [Similar format]

## FYI / No Action
- [Similar format]

## Low Priority / Archive
- [Similar format]
```

**Use with AI agent:**
```bash
cd code-along/03-offline-communications-triage

# Ask: "Process all .eml files using prompts/email-triage.md
#       and create outputs/email-triage.md with categorized actions"
```

## Configuration Patterns

### Prompt Template Best Practices

Each prompt should include:

1. **Context:** What files/folders to process
2. **Task:** Clear objective
3. **Output format:** Markdown structure expected
4. **Constraints:** What to include/exclude
5. **Quality checks:** Validation criteria

**Example prompt template:**
```markdown
# Task: [Clear objective]

## Input
Process all files in: `./folder-name/`

## Output Format
Generate markdown with:
- Section 1: [description]
- Section 2: [description]
- Section 3: [description]

## Requirements
- Use bullet points for lists
- Include timestamps where relevant
- Highlight action items with **bold**
- Add priority tags: [URGENT], [HIGH], [NORMAL], [LOW]

## Quality Checks
- [ ] All input files processed
- [ ] Output is valid markdown
- [ ] Action items are specific
- [ ] No sensitive data exposed
```

## Working with AI Coding Agents

### Claude/Cursor/Copilot Integration

```bash
# 1. Open project in your AI-enabled editor
code code-along/

# 2. For each exercise, prompt the agent:
# "Execute the workflow in [exercise-folder]:
#  1. Read the prompt in prompts/
#  2. Process the input files
#  3. Generate the output file as specified
#  4. Validate against expected/ if present"

# 3. Review generated outputs before committing
```

### Programmatic Execution Pattern

```python
# Example Python wrapper for automated execution
import os
import subprocess
from pathlib import Path

def run_openclaw_workflow(exercise_dir: str, ai_command: str):
    """
    Execute an OpenClaw exercise workflow.
    
    Args:
        exercise_dir: Path to exercise (e.g., '01-data-intake-review')
        ai_command: Command to invoke your AI tool
    """
    base = Path('code-along') / exercise_dir
    prompt_file = base / 'prompts' / (base.name.split('-', 1)[1] + '.md')
    
    # Read prompt
    with open(prompt_file) as f:
        prompt = f.read()
    
    # Execute AI tool (example - adapt to your tool)
    result = subprocess.run(
        [ai_command, '--prompt', str(prompt_file), '--context', str(base)],
        capture_output=True,
        text=True
    )
    
    return result.stdout

# Usage
output = run_openclaw_workflow(
    '01-data-intake-review',
    os.environ.get('AI_TOOL_PATH', 'claude-cli')
)
```

## Common Patterns

### Daily Automation Setup

```bash
# Create automation script
cat > ~/bin/openclaw-daily.sh << 'EOF'
#!/bin/bash
OPENCLAW_DIR="${OPENCLAW_PROJECT_DIR:-$HOME/openclaw-assistant}"
DATE=$(date +%Y-%m-%d)

cd "$OPENCLAW_DIR/code-along/02-operational-memory"

# Generate daily log
$AI_CLI generate \
  --prompt prompts/daily-log.md \
  --input inbox/ \
  --output "outputs/daily-log-$DATE.md"

# Archive processed items
mv inbox/* archive/$(date +%Y-%m)/ 2>/dev/null || true
EOF

chmod +x ~/bin/openclaw-daily.sh
```

### Batch File Processing

```bash
# Process multiple incoming files
cd code-along/01-data-intake-review

for file in incoming/*; do
  echo "Processing: $file"
  # Feed to AI agent with context
done

# Generate consolidated report
# (Agent combines individual analyses)
```

## Troubleshooting

### Issue: Outputs are incomplete

**Solution:** Ensure your AI context window includes all input files:
```bash
# List all files being processed
ls -lah incoming/
# If too large, process in batches
```

### Issue: Generated markdown is malformed

**Solution:** Add validation step to prompts:
```markdown
## Validation
Before outputting, verify:
- All headers use proper markdown (#, ##, ###)
- Lists use consistent formatting (- or *)
- Code blocks use triple backticks with language tags
- No unclosed markdown elements
```

### Issue: Cron jobs not running

**Solution:** Check environment variables and paths:
```bash
# Test manually first
cd ~/openclaw-assistant/code-along/02-operational-memory
bash -c "$(crontab -l | grep openclaw)"

# Ensure cron has access to needed tools
# Add to crontab:
PATH=/usr/local/bin:/usr/bin:/bin
AI_CLI=/path/to/your/ai/tool
```

### Issue: Email files not parsing correctly

**Solution:** Validate `.eml` format:
```bash
# Check file encoding
file eml/*.eml

# Ensure proper export from email client
# Gmail: Select > More > Download message
# Outlook: File > Save As > Outlook Message Format
```

## Mission Control Dashboard

For advanced usage, create a unified view:

```markdown
# Mission Control

## Today's Status
- [Link to latest daily log](./02-operational-memory/outputs/daily-log-latest.md)

## This Week
- [Link to weekly hype](./02-operational-memory/outputs/weekly-hype-latest.md)

## Action Items
- [Link to email triage](./03-offline-communications-triage/outputs/email-triage.md)

## Recent Intakes
- [Link to intake reviews](./01-data-intake-review/outputs/)
```

This project teaches the patterns for building local-first AI assistant workflows that respect privacy, produce auditable outputs, and require no cloud dependencies.
