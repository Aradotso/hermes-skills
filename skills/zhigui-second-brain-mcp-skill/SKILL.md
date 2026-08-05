---
name: zhigui-second-brain-mcp-skill
description: AI second brain system with MCP tools for goals, schedules, notes, decisions, and long-term memory management
triggers:
  - "help me plan my day with ZhiGui"
  - "show me my current goals and schedule"
  - "create a note linked to my project goals"
  - "what tasks should I prioritize today"
  - "generate my morning briefing"
  - "reflect on what I completed today"
  - "set up ZhiGui for personal productivity"
  - "link this schedule item to my notes"
---

# ZhiGui Second Brain MCP Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## What is ZhiGui?

ZhiGui (知归, "knowing where to return") is a personal intelligence system that combines a JSON-file-driven knowledge graph with MCP (Model Context Protocol) tools. It provides 40+ MCP tools for managing goals, schedules, notes, decisions, errands, and reminders with automatic relationship tracking, long-term memory, and intelligent planning assistance.

**Key capabilities:**
- **Relationship Graph**: Every entity (goal, schedule, note, decision) is interlinked through foreign keys
- **Tiered Indexing**: Lightweight bootstrap loads titles first, full content on-demand
- **Automatic Linking**: AI suggests related notes, goals, and decisions based on context
- **Long-term Memory**: Lifecycle states track entity freshness (Active → Stale → Archive Candidate)
- **Morning Briefings & Reflections**: AI-generated daily summaries frozen to specific dates
- **Reference Integrity**: Deletion previews impact and cleans broken references

## Installation

### Prerequisites
- Node.js ≥ 17
- MCP-capable AI tool (Claude Desktop, Cursor, Trae, etc.)

### Step 1: Clone or Download
```bash
git clone https://github.com/CarlWangChina/zhigui-openclaw-ui-second-brain-skill.git
cd zhigui-openclaw-ui-second-brain-skill
```

### Step 2: Install Dependencies
```bash
cd skill
npm install
```

### Step 3: Configure MCP

Add to your AI tool's MCP configuration (e.g., `claude_desktop_config.json` or similar):

```json
{
  "mcpServers": {
    "zhigui": {
      "command": "node",
      "args": ["/absolute/path/to/zhigui-openclaw-ui-second-brain-skill/skill/engine/server.js"]
    }
  }
}
```

Replace `/absolute/path/to/` with your actual installation path. Use forward slashes even on Windows.

### Step 4: Verify Installation

Start a conversation with your AI and say:
```
"Show me today's schedule"
```

If the AI calls `zhigui_get_assistant_bootstrap` and returns data, the configuration is successful.

### Optional: Load Demo Data

```bash
cd skill
node scripts/seed-demo-data-en.js  # English demo data
# or
node scripts/seed-demo-data.js     # Chinese demo data
```

### Optional: Launch Desktop Panel

```bash
# From project root
npm install
npm start

# Or use platform-specific launchers
# Windows: start.bat
# macOS/Linux: ./start.sh
```

## Core MCP Tools

### Bootstrap & Context Loading

**`zhigui_get_assistant_bootstrap`**
Load a compact index of all active entities (goals, schedules, notes, decisions). Always call this at conversation start.

```javascript
// The AI automatically calls this when conversation begins
// Returns: { goals, schedules, notes, decisions, topics, briefings, reflections }
```

**`zhigui_get_schedules_by_date`**
```javascript
// Get all schedules for a specific date
{
  "date": "2026-08-15"  // YYYY-MM-DD format
}
// Returns: Array of schedule items with referenced notes/goals/decisions
```

**`zhigui_get_goal_detail`**
```javascript
{
  "goalId": "goal_12345"
}
// Returns: Full goal with linked schedules, notes, decisions
```

**`zhigui_get_note_detail`**
```javascript
{
  "noteId": "note_67890"
}
// Returns: Full note content with tags, topic, linked entities
```

### Creating Entities

**`zhigui_create_schedule`**
```javascript
{
  "title": "Review Q3 marketing strategy",
  "date": "2026-08-20",
  "time": "14:00",           // Optional, omit if time not set
  "topicId": "topic_123",    // Required
  "noteIds": ["note_456"],   // Optional, suggested by AI
  "goalId": "goal_789",      // Optional, link to parent goal
  "decisionIds": ["dec_111"], // Optional, cite decisions
  "tags": ["marketing", "quarterly-review"],
  "priority": "high",        // low | medium | high
  "estimatedMinutes": 90
}
```

**`zhigui_create_goal`**
```javascript
{
  "title": "Launch new product feature",
  "description": "Ship the analytics dashboard by end of Q3",
  "topicId": "topic_product",
  "deadline": "2026-09-30",
  "status": "in_progress",   // not_started | in_progress | completed | blocked | deferred
  "priority": "high",
  "noteIds": ["note_spec_123", "note_design_456"]
}
```

**`zhigui_create_note`**
```javascript
{
  "title": "Product analytics requirements",
  "content": "- Real-time user tracking\n- Custom event funnels\n- Export to CSV",
  "topicId": "topic_product",
  "tags": ["analytics", "requirements"],
  "goalIds": ["goal_789"],   // Optional, link to goals
  "decisionIds": ["dec_222"] // Optional, cite decisions
}
```

**`zhigui_create_decision`**
```javascript
{
  "title": "Use PostgreSQL for analytics storage",
  "context": "Evaluated options: PostgreSQL, ClickHouse, BigQuery",
  "decision": "PostgreSQL",
  "reasoning": "Best balance of performance, team expertise, and cost",
  "topicId": "topic_product",
  "status": "accepted",      // proposed | accepted | rejected | superseded
  "noteIds": ["note_eval_123"],
  "goalIds": ["goal_789"]
}
```

### Updating Entities

**`zhigui_update_schedule`**
```javascript
{
  "scheduleId": "sched_12345",
  "updates": {
    "status": "completed",
    "actualMinutes": 120,
    "completionNotes": "Decided to pivot to mobile-first strategy",
    "impactedGoalIds": ["goal_789"],      // Update goal status
    "impactedNoteIds": ["note_456"],      // Update note content
    "followUpScheduleIds": ["sched_999"]  // Created follow-up tasks
  }
}
```

**`zhigui_update_goal`**
```javascript
{
  "goalId": "goal_789",
  "updates": {
    "status": "completed",
    "progressNotes": "All acceptance criteria met",
    "completionDate": "2026-08-15"
  }
}
```

**`zhigui_update_note`**
```javascript
{
  "noteId": "note_456",
  "updates": {
    "content": "Updated content after review meeting",
    "tags": ["marketing", "quarterly-review", "mobile-first"]
  }
}
```

### Deleting Entities

**`zhigui_delete_schedule`**
```javascript
{
  "scheduleId": "sched_12345",
  "confirmed": true  // Must preview impact first, then confirm
}
// AI should first call without confirmed:true to preview impact
```

**`zhigui_delete_goal`**
```javascript
{
  "goalId": "goal_789",
  "confirmed": true
}
```

**`zhigui_delete_note`**
```javascript
{
  "noteId": "note_456",
  "confirmed": true
}
```

### Briefings & Reflections

**`zhigui_generate_briefing`**
```javascript
{
  "date": "2026-08-15"  // Generate morning briefing for this date
}
// Returns: { mustDos, recommended, strategic, warnings }
```

**`zhigui_generate_reflection`**
```javascript
{
  "date": "2026-08-15",  // Generate evening reflection
  "completedScheduleIds": ["sched_123", "sched_456"]
}
// Returns: { completed, goalHealth, attentionShifts, tomorrow }
```

### Long-term Memory & Cleanup

**`zhigui_get_stale_entities`**
```javascript
{
  "days": 30  // Find entities unreferenced for 30+ days
}
// Returns: { staleNotes, staleGoals, staleDecisions }
```

**`zhigui_archive_entity`**
```javascript
{
  "entityType": "note",  // note | goal | decision
  "entityId": "note_456",
  "confirmed": true
}
// Moves to .zhigui/archive/ with timestamp
```

## Configuration

### Data Directory Structure

```
skill/.zhigui/
├── goals.json
├── schedules.json
├── notes.json
├── decisions.json
├── topics.json
├── briefings.json
├── reflections.json
└── archive/
    ├── goals/
    ├── schedules/
    ├── notes/
    └── decisions/
```

### Modify AI Behavior

Edit `skill/SKILL.md` to customize:
- Assistant personality
- Auto-linking rules
- Reflection prompts
- Briefing format
- Priority scoring logic

Example customization in `SKILL.md`:
```markdown
## Auto-linking Rules

When creating a schedule:
1. If title mentions a goal keyword, suggest linking that goal
2. If topic has recent notes with matching tags, suggest linking those notes
3. If a decision exists on the same topic with status "accepted", suggest citing it
4. Limit suggestions to top 3 most relevant entities
```

### Environment Variables

```bash
# Optional: Override data directory
export ZHIGUI_DATA_DIR="/custom/path/to/.zhigui"

# Optional: Change dashboard port (default 7788)
export ZHIGUI_DASHBOARD_PORT=8080
```

## Common Patterns

### Pattern 1: Daily Planning Flow

```javascript
// 1. Morning: Load bootstrap and generate briefing
await zhigui_get_assistant_bootstrap();
await zhigui_generate_briefing({ date: "2026-08-15" });

// 2. User reviews briefing, creates/adjusts schedules
await zhigui_create_schedule({
  title: "Review analytics spec",
  date: "2026-08-15",
  time: "10:00",
  topicId: "topic_product",
  noteIds: ["note_spec_123"],  // AI suggests based on title
  priority: "high"
});

// 3. Evening: Mark completed, generate reflection
await zhigui_update_schedule({
  scheduleId: "sched_123",
  updates: {
    status: "completed",
    completionNotes: "Spec approved, ready for dev",
    impactedGoalIds: ["goal_789"]
  }
});
await zhigui_generate_reflection({
  date: "2026-08-15",
  completedScheduleIds: ["sched_123"]
});
```

### Pattern 2: Goal-Driven Note Creation

```javascript
// User says: "I need to research database options for the analytics project"

// 1. AI finds related goal
const bootstrap = await zhigui_get_assistant_bootstrap();
const goal = bootstrap.goals.find(g => g.title.includes("analytics"));

// 2. Create note linked to goal
await zhigui_create_note({
  title: "Database evaluation for analytics",
  content: "Options: PostgreSQL, ClickHouse, BigQuery\n\nCriteria:\n- Query performance\n- Cost\n- Team expertise",
  topicId: goal.topicId,
  tags: ["database", "research"],
  goalIds: [goal.id]
});

// 3. Suggest creating a decision once research complete
// (AI prompts user: "When you decide, I can record it as a decision linked to this note")
```

### Pattern 3: Completing a Task with Follow-ups

```javascript
// User: "Finished the marketing review. We need to create mobile landing pages."

// 1. Complete original schedule
await zhigui_update_schedule({
  scheduleId: "sched_marketing_review",
  updates: {
    status: "completed",
    completionNotes: "Decided to pivot to mobile-first strategy",
    impactedNoteIds: ["note_marketing_plan"]  // Update plan note
  }
});

// 2. Create follow-up schedule
const followUp = await zhigui_create_schedule({
  title: "Design mobile landing pages",
  date: "2026-08-18",
  topicId: "topic_marketing",
  noteIds: ["note_marketing_plan"],
  priority: "high"
});

// 3. Link follow-up back to original
await zhigui_update_schedule({
  scheduleId: "sched_marketing_review",
  updates: {
    followUpScheduleIds: [followUp.id]
  }
});
```

### Pattern 4: Decision Recording

```javascript
// User: "We decided to use PostgreSQL after evaluating options"

// 1. Create decision
await zhigui_create_decision({
  title: "Use PostgreSQL for analytics storage",
  context: "Evaluated PostgreSQL, ClickHouse, BigQuery",
  decision: "PostgreSQL",
  reasoning: "Best balance of performance, team expertise, and cost. ClickHouse overkill for our scale, BigQuery too expensive.",
  topicId: "topic_product",
  status: "accepted",
  noteIds: ["note_db_eval"],  // Link to research note
  goalIds: ["goal_analytics"]
});

// 2. AI suggests creating implementation schedule
// "Should I schedule the PostgreSQL setup task?"
```

## Troubleshooting

### MCP connection fails

**Symptoms:** AI says "ZhiGui tools not available"

**Solutions:**
1. Check MCP config path is absolute (no `~` or relative paths)
2. Verify `skill/engine/server.js` exists
3. Test manually:
   ```bash
   node /absolute/path/to/skill/engine/server.js
   ```
   Should not crash immediately
4. Restart AI tool after config changes

### Data directory not found

**Symptoms:** Error "Cannot read goals.json"

**Solutions:**
1. Run initialization:
   ```bash
   cd skill
   node scripts/install.js
   ```
2. Check `skill/.zhigui/` exists and contains JSON files
3. Set custom data dir if needed:
   ```bash
   export ZHIGUI_DATA_DIR="/custom/path"
   ```

### Auto-linking not working

**Symptoms:** AI doesn't suggest related notes/goals when creating schedules

**Solutions:**
1. Ensure entities share the same `topicId`
2. Check tags overlap between notes and schedule title keywords
3. Verify bootstrap data is loaded (AI should call `zhigui_get_assistant_bootstrap` at conversation start)
4. Review auto-linking rules in `skill/SKILL.md`

### Deletion fails with "broken references"

**Symptoms:** Cannot delete note/goal that's referenced elsewhere

**Solutions:**
1. First preview impact:
   ```javascript
   await zhigui_delete_note({ noteId: "note_123" }); // without confirmed:true
   ```
2. Review what entities reference it
3. Confirm deletion to auto-clean references:
   ```javascript
   await zhigui_delete_note({ noteId: "note_123", confirmed: true });
   ```

### Reflection/briefing empty or generic

**Symptoms:** Generated text lacks specifics

**Solutions:**
1. Ensure `zhigui_get_assistant_bootstrap` was called first
2. Pass `completedScheduleIds` to reflection:
   ```javascript
   await zhigui_generate_reflection({
     date: "2026-08-15",
     completedScheduleIds: ["sched_123", "sched_456"]
   });
   ```
3. Check schedules have `completionNotes` and `impactedGoalIds` filled in
4. Customize reflection template in `skill/SKILL.md`

## Advanced Usage

### Custom Topics

Topics organize all entities. Create topic via panel UI or directly edit `skill/.zhigui/topics.json`:

```json
{
  "id": "topic_research",
  "name": "Research & Innovation",
  "description": "Long-term R&D initiatives",
  "color": "#9C27B0",
  "createdAt": "2026-08-01T12:00:00Z"
}
```

Then reference in entities:
```javascript
await zhigui_create_goal({
  title: "Explore quantum computing applications",
  topicId: "topic_research",
  // ...
});
```

### Recurring Schedules

Mark schedules as recurring in panel UI, or set in JSON:

```json
{
  "id": "sched_weekly_standup",
  "title": "Team standup",
  "recurrence": "weekly",
  "recurrenceDays": [1, 3, 5],  // Mon, Wed, Fri
  "time": "09:00"
}
```

AI will auto-generate instances on those days.

### Exporting Data

All data is plain JSON. Backup or export:

```bash
# Backup entire data directory
cp -r skill/.zhigui /backup/location/

# Export specific entity type
cat skill/.zhigui/goals.json | jq '.'
```

### Testing & Development

Run test suite:
```bash
cd skill
npm test
```

Watch file changes during development:
```bash
cd skill/dashboard
nodemon server.js
```

## Best Practices

1. **Always load bootstrap first**: Call `zhigui_get_assistant_bootstrap` at conversation start for context
2. **Link intentionally**: Only link notes to schedules when there's execution value
3. **Update on completion**: Fill `completionNotes`, `impactedGoalIds`, `impactedNoteIds` when marking done
4. **Preview deletions**: Never confirm deletion without seeing impact first
5. **Use time sparingly**: Only set `time` field when genuinely time-bound; date-only items are valid
6. **Tag consistently**: Reuse existing tags for better auto-linking
7. **Archive regularly**: Review stale entities monthly, archive confirmed inactive ones
8. **Customize SKILL.md**: Tailor AI behavior to your workflow and priorities

## Reference

- **GitHub**: https://github.com/CarlWangChina/zhigui-openclaw-ui-second-brain-skill
- **User Manual (EN)**: `zhigui-user-manual-en/zhigui-user-manual-en.html`
- **User Manual (CN)**: `zhigui-user-manual/zhigui-user-manual.html`
- **AI Behavior Spec**: `skill/SKILL.md`
- **Contact**: huangkkkke16@gmail.com
