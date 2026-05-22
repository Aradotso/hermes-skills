---
name: openclaw-executive-assistant-workshop
description: Build local-first AI executive assistant workflows using OpenClaw for data intake, operational memory, and communication triage
triggers:
  - "help me build an executive assistant with OpenClaw"
  - "set up local AI workflow automation"
  - "create an OpenClaw data intake review"
  - "build operational memory system with OpenClaw"
  - "set up email triage automation locally"
  - "use OpenClaw for document processing"
  - "create markdown-based AI workflow"
  - "build local-first AI assistant"
---

# OpenClaw Executive Assistant Workshop

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill teaches you to build local-first AI executive assistant workflows using OpenClaw. The workshop covers three core patterns: data intake review, operational memory management, and offline communications triage. All workflows stay local, process files in folders, and produce reviewable markdown artifacts.

## What This Project Does

This is a workshop/starter kit that demonstrates how to:
- Turn unknown files into trustworthy intake reports
- Convert work residue into daily logs and weekly summaries
- Transform exported emails into actionable triage lists
- Build entirely local AI workflows with no cloud dependencies

The project emphasizes **local-first, markdown-based, copy-paste-prompt** workflows ideal for AI coding agents.

## Repository Structure

```
code-along/
├── INDEX.md
├── 01-data-intake-review/
│   ├── incoming/          # Files to inspect
│   ├── prompts/           # Prompts for LLM
│   ├── outputs/           # Generated reports
│   └── expected/          # Reference outputs
├── 02-operational-memory/
│   ├── inbox/             # Work notes and residue
│   ├── prompts/           # Daily/weekly prompts
│   ├── outputs/           # Generated logs
│   └── schedule/          # Cron examples
├── 03-offline-communications-triage/
│   ├── eml/               # Exported email files
│   ├── prompts/           # Triage prompts
│   ├── outputs/           # Action lists
│   └── expected/          # Reference outputs
└── mission-control/       # Optional dashboard
```

## Installation & Setup

1. Clone the repository:
```bash
git clone https://github.com/dandenney/webinars-build-your-own-executive-assistant-with-openclaw.git
cd webinars-build-your-own-executive-assistant-with-openclaw
```

2. Open the walkthrough:
```bash
# Open in your browser
open webinar-runbook.html
```

3. Set up your local AI tool (Claude Desktop, Cursor, etc.) to access the `code-along/` directory.

## Core Workflows

### 1. Data Intake Review

**Purpose**: Turn unknown incoming files into a structured intake report.

**Pattern**:
```bash
# Directory structure
01-data-intake-review/
├── incoming/              # Place files here
├── prompts/intake-review.md
└── outputs/intake-review.md
```

**Workflow**:
1. Place files to review in `incoming/`
2. Read the prompt from `prompts/intake-review.md`
3. Process files with your AI agent using the prompt
4. Generate `outputs/intake-review.md`

**Example prompt structure** (from `prompts/intake-review.md`):
```markdown
# Data Intake Review

Review all files in the incoming/ folder and produce a structured report:

## Files Reviewed
- List each file with type and size

## Summary
- What types of content are present
- Any immediate concerns or priorities

## Next Actions
- Recommended steps for each file
- Priority order

## Questions
- What needs clarification
```

**Expected output format**:
```markdown
# Intake Review — [DATE]

## Files Reviewed
- `document.pdf` (245 KB) — Project proposal
- `data.csv` (12 KB) — User metrics

## Summary
Mixed business documents requiring categorization...

## Next Actions
1. Review project proposal for budget approval
2. Import metrics to tracking sheet

## Questions
- Which budget code applies to the proposal?
```

### 2. Operational Memory

**Purpose**: Convert daily work residue into momentum documents.

**Pattern**:
```bash
# Directory structure
02-operational-memory/
├── inbox/                 # Notes, snippets, logs
├── prompts/
│   ├── daily-log.md
│   └── weekly-hype.md
└── outputs/
    ├── daily-log.md
    └── weekly-hype.md
```

**Daily Log Workflow**:
1. Accumulate work notes in `inbox/`
2. Use `prompts/daily-log.md` to generate daily summary
3. Output to `outputs/daily-log.md`

**Example daily log prompt pattern**:
```markdown
# Daily Log Generator

Review all files in inbox/ and create a daily log:

## Completed Today
- Key accomplishments

## In Progress
- Active work items

## Blockers
- What's stuck and why

## Tomorrow
- Planned priorities
```

**Weekly Summary Workflow**:
1. Review accumulated daily logs
2. Use `prompts/weekly-hype.md` to generate weekly summary
3. Output to `outputs/weekly-hype.md`

**Example weekly summary prompt pattern**:
```markdown
# Weekly Hype Generator

Review this week's daily logs and create an executive summary:

## Week Highlights
- Major wins and completions

## Momentum Shifts
- What changed direction

## Next Week Focus
- Top 3 priorities

## Team Shoutouts
- Recognition opportunities
```

**Automation with cron** (from `schedule/cron-examples.md`):
```bash
# Daily log at 5 PM
0 17 * * * cd /path/to/02-operational-memory && ./generate-daily-log.sh

# Weekly summary Friday at 4 PM
0 16 * * 5 cd /path/to/02-operational-memory && ./generate-weekly-hype.sh
```

### 3. Offline Communications Triage

**Purpose**: Turn exported emails into actionable triage reports.

**Pattern**:
```bash
# Directory structure
03-offline-communications-triage/
├── eml/                   # Exported .eml files
├── prompts/email-triage.md
└── outputs/email-triage.md
```

**Workflow**:
1. Export emails to `eml/` folder as `.eml` files
2. Use `prompts/email-triage.md` for batch processing
3. Generate `outputs/email-triage.md` with categorized actions

**Example triage prompt pattern**:
```markdown
# Email Triage

Process all .eml files and categorize:

## Urgent Action Required
- Sender, subject, deadline, required action

## Needs Response (This Week)
- Sender, subject, suggested reply approach

## FYI / Archive
- Quick summary of informational emails

## Waiting On Others
- What you're blocked on

## Spam/Noise
- Can be deleted
```

**Expected output format**:
```markdown
# Email Triage — [DATE]

## Urgent Action Required
- **From**: client@company.com | **Subject**: Contract approval needed
  - **Deadline**: Tomorrow 3 PM
  - **Action**: Review and sign contract in DocuSign

## Needs Response (This Week)
- **From**: teammate@org.com | **Subject**: Q2 planning input
  - **Approach**: Share priorities doc and schedule sync

## FYI / Archive
- Newsletter from industry blog (3 articles)
- Team lunch confirmation

## Waiting On Others
- Design mockups from Sarah (requested Monday)

## Spam/Noise
- Marketing emails (2)
```

## Configuration Patterns

### Environment Variables

```bash
# Optional: Set default output paths
export OPENCLAW_INTAKE_OUTPUT="$HOME/Documents/intake-reports"
export OPENCLAW_MEMORY_OUTPUT="$HOME/Documents/operational-memory"
export OPENCLAW_TRIAGE_OUTPUT="$HOME/Documents/email-triage"

# Optional: AI model preferences
export OPENCLAW_MODEL="claude-3-5-sonnet-20241022"
export ANTHROPIC_API_KEY="$YOUR_API_KEY"
```

### Prompt Customization

All prompts are markdown files. Customize them for your workflow:

```bash
# Edit intake review prompt
vim code-along/01-data-intake-review/prompts/intake-review.md

# Add custom sections like:
## Security Review
- Any sensitive data found
- Recommended access controls
```

## Real-World Integration Examples

### Example 1: Automated Daily Standup

```bash
#!/bin/bash
# generate-standup.sh

MEMORY_DIR="code-along/02-operational-memory"
PROMPT="$MEMORY_DIR/prompts/daily-log.md"
OUTPUT="$MEMORY_DIR/outputs/standup-$(date +%Y-%m-%d).md"

# Collect today's commits
git log --since="1 day ago" --pretty=format:"- %s" > "$MEMORY_DIR/inbox/git-activity.txt"

# Collect today's notes (assuming note-taking app exports)
cp "$HOME/Notes/work-$(date +%Y-%m-%d).md" "$MEMORY_DIR/inbox/" 2>/dev/null || true

# Generate standup (requires AI tool integration)
echo "Ready to generate standup. Process with AI agent using:"
echo "Prompt: $PROMPT"
echo "Context: $MEMORY_DIR/inbox/"
echo "Output: $OUTPUT"
```

### Example 2: Weekly Report Pipeline

```bash
#!/bin/bash
# weekly-report.sh

MEMORY_DIR="code-along/02-operational-memory"
WEEK_START=$(date -v-7d +%Y-%m-%d)
WEEK_END=$(date +%Y-%m-%d)

# Collect week's daily logs
cat "$MEMORY_DIR/outputs/daily-log-"*.md > "$MEMORY_DIR/inbox/week-aggregated.md"

# Generate weekly summary
echo "Generate weekly report from:"
echo "$MEMORY_DIR/inbox/week-aggregated.md"
echo "Using prompt: $MEMORY_DIR/prompts/weekly-hype.md"
echo "Output: $MEMORY_DIR/outputs/weekly-report-$WEEK_END.md"
```

### Example 3: Email Export to Triage

```bash
#!/bin/bash
# process-emails.sh

TRIAGE_DIR="code-along/03-offline-communications-triage"

# Assume emails exported from mail client to ~/Downloads/exported-emails/
cp ~/Downloads/exported-emails/*.eml "$TRIAGE_DIR/eml/"

# Count emails
EMAIL_COUNT=$(ls -1 "$TRIAGE_DIR/eml"/*.eml 2>/dev/null | wc -l)

echo "Found $EMAIL_COUNT emails to triage"
echo "Process with AI agent using:"
echo "Prompt: $TRIAGE_DIR/prompts/email-triage.md"
echo "Output: $TRIAGE_DIR/outputs/email-triage-$(date +%Y-%m-%d).md"
```

## Common Patterns

### Pattern: File Context Assembly

```bash
# Gather context for AI processing
function assemble_context() {
  local input_dir=$1
  local context_file="$input_dir/CONTEXT.md"
  
  echo "# Processing Context" > "$context_file"
  echo "Generated: $(date)" >> "$context_file"
  echo "" >> "$context_file"
  
  for file in "$input_dir"/*; do
    if [[ -f "$file" && "$file" != "$context_file" ]]; then
      echo "## File: $(basename "$file")" >> "$context_file"
      echo '```' >> "$context_file"
      cat "$file" >> "$context_file"
      echo '```' >> "$context_file"
      echo "" >> "$context_file"
    fi
  done
  
  echo "$context_file"
}

# Usage
CONTEXT=$(assemble_context "code-along/01-data-intake-review/incoming")
echo "Context assembled: $CONTEXT"
```

### Pattern: Markdown Report Template

```markdown
# [WORKFLOW NAME] — [DATE]

> Generated by OpenClaw Executive Assistant
> Source: [DIRECTORY PATH]

---

## Executive Summary
[2-3 sentence overview]

## Detailed Findings
[Main content sections]

## Recommended Actions
1. [Priority 1]
2. [Priority 2]
3. [Priority 3]

## Questions & Blockers
- [Open question 1]
- [Blocker 1]

---
**Next Review**: [DATE]
```

### Pattern: Incremental Processing

```bash
# Process new files only (avoid reprocessing)
function find_new_files() {
  local input_dir=$1
  local processed_log="$input_dir/.processed"
  
  touch "$processed_log"
  
  find "$input_dir" -type f -newer "$processed_log" | while read file; do
    echo "$file"
  done
  
  touch "$processed_log"  # Update timestamp
}

# Usage
NEW_FILES=$(find_new_files "code-along/01-data-intake-review/incoming")
if [[ -z "$NEW_FILES" ]]; then
  echo "No new files to process"
else
  echo "Processing new files:"
  echo "$NEW_FILES"
fi
```

## Troubleshooting

### Issue: Prompt not generating expected output

**Solution**: Check prompt clarity and context assembly
```bash
# Verify prompt structure
cat code-along/01-data-intake-review/prompts/intake-review.md

# Check available context
ls -lh code-along/01-data-intake-review/incoming/

# Compare to expected output
diff code-along/01-data-intake-review/outputs/intake-review.md \
     code-along/01-data-intake-review/expected/report-outline.md
```

### Issue: Missing files in output

**Solution**: Verify directory structure and permissions
```bash
# Check structure
tree code-along/

# Verify permissions
find code-along/ -type d -exec chmod 755 {} \;
find code-along/ -type f -exec chmod 644 {} \;
```

### Issue: Cron job not running

**Solution**: Check cron syntax and paths
```bash
# Test script manually first
cd code-along/02-operational-memory
./generate-daily-log.sh

# Verify cron entry
crontab -l

# Check cron logs
grep CRON /var/log/syslog  # Linux
log show --predicate 'process == "cron"' --last 1h  # macOS
```

### Issue: Large file processing slow

**Solution**: Implement chunking or filtering
```bash
# Filter by file type
find incoming/ -name "*.txt" -o -name "*.md"

# Process in batches
find incoming/ -type f | head -10 | xargs -I {} cp {} batch-1/
```

## Best Practices

1. **Keep prompts version controlled**: Track prompt evolution in git
2. **Review outputs manually**: AI-generated content should be reviewed before acting
3. **Use dated outputs**: Include timestamps in output filenames for history
4. **Start simple**: Begin with one workflow, add complexity gradually
5. **Document customizations**: Note any prompt or workflow changes
6. **Archive processed files**: Move completed work to `archive/` folders
7. **Test prompts iteratively**: Refine based on actual output quality

## Advanced Usage

### Custom Mission Control Dashboard

Create an HTML dashboard to link all outputs:

```html
<!DOCTYPE html>
<html>
<head>
  <title>OpenClaw Mission Control</title>
</head>
<body>
  <h1>Executive Assistant Dashboard</h1>
  
  <section>
    <h2>Latest Reports</h2>
    <ul>
      <li><a href="01-data-intake-review/outputs/intake-review.md">Data Intake</a></li>
      <li><a href="02-operational-memory/outputs/daily-log.md">Daily Log</a></li>
      <li><a href="03-offline-communications-triage/outputs/email-triage.md">Email Triage</a></li>
    </ul>
  </section>
  
  <section>
    <h2>Quick Actions</h2>
    <ul>
      <li><a href="file://./01-data-intake-review/incoming">Add files to intake</a></li>
      <li><a href="file://./02-operational-memory/inbox">Add work notes</a></li>
    </ul>
  </section>
</body>
</html>
```

This skill provides the foundation for building local-first AI assistant workflows that remain under your control, produce auditable artifacts, and integrate with existing tools.
