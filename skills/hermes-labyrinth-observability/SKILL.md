---
name: hermes-labyrinth-observability
description: Hermes Labyrinth observability plugin for monitoring autonomous agent journeys, crossings, and execution traces
triggers:
  - install hermes labyrinth plugin
  - view agent journey traces
  - inspect hermes agent crossings
  - monitor autonomous agent execution
  - analyze hermes agent behavior
  - export agent journey reports
  - debug hermes agent failures
  - track agent tool calls
---

# Hermes Labyrinth Observability Plugin

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

Hermes Labyrinth is a read-only observability plugin for [Hermes Agent](https://github.com/NousResearch/hermes-agent) that provides detailed monitoring of autonomous agent execution. It visualizes agent "journeys" as sequences of "crossings" (prompts, tool calls, results, failures, model switches, subagents) and generates exportable reports with built-in secret redaction.

## Installation

Install into the Hermes user plugin directory:

```bash
mkdir -p ~/.hermes/plugins
git clone https://github.com/stainlu/hermes-labyrinth.git ~/.hermes/plugins/hermes-labyrinth
```

After installation, restart the Hermes dashboard:

```bash
hermes dashboard
```

The Labyrinth tab will appear in the dashboard UI at `http://127.0.0.1:9119`.

### Docker Installation

For Docker-based Hermes installations, mount the plugin directory:

```bash
mkdir -p ~/.hermes/plugins
git clone https://github.com/stainlu/hermes-labyrinth.git ~/.hermes/plugins/hermes-labyrinth
cd ~/.hermes/plugins/hermes-labyrinth
git checkout v0.1.3  # Pin to reviewed version
```

Mount the Hermes home directory into your container and restart the dashboard.

## Plugin Management

### Rescan Plugins (Frontend Only)

Refresh discovered frontend manifests without full restart:

```bash
curl http://127.0.0.1:9119/api/dashboard/plugins/rescan
```

**Note:** Backend API changes require a full dashboard restart.

### Disable/Remove Plugin

```bash
hermes plugins disable hermes-labyrinth
rm -rf ~/.hermes/plugins/hermes-labyrinth
curl http://127.0.0.1:9119/api/dashboard/plugins/rescan
```

## API Reference

All endpoints are read-only and include automatic secret redaction.

### Health Check

```bash
curl http://127.0.0.1:9119/api/plugins/hermes-labyrinth/health
```

Returns plugin status and Hermes state accessibility.

### List Journeys

```bash
curl http://127.0.0.1:9119/api/plugins/hermes-labyrinth/journeys
```

Returns recent CLI, dashboard, gateway, cron, and delegated agent sessions.

### Get Journey Details

```bash
curl http://127.0.0.1:9119/api/plugins/hermes-labyrinth/journeys/{journey_id}
```

Returns metadata for a specific journey including start time, status, and model usage.

### Get Journey Crossings

```bash
curl http://127.0.0.1:9119/api/plugins/hermes-labyrinth/journeys/{journey_id}/crossings
```

Returns ordered sequence of crossings (messages, tool calls, results) for a journey.

### Get Skills Inventory

```bash
curl http://127.0.0.1:9119/api/plugins/hermes-labyrinth/skills
```

Returns:
- `skills`: effective skills loaded
- `shadowed`: expected user-over-bundled overrides
- `duplicates`: true duplicate diagnostics
- `errors`: skill scan errors

### Get Cron Configuration

```bash
curl http://127.0.0.1:9119/api/plugins/hermes-labyrinth/cron
```

Returns scheduled autonomy jobs, next run times, last failures, and working directories.

### Get Guideposts

```bash
curl http://127.0.0.1:9119/api/plugins/hermes-labyrinth/guideposts
```

Returns generated observations backed by local evidence across journeys.

### Export Reports

JSON format:
```bash
curl http://127.0.0.1:9119/api/plugins/hermes-labyrinth/reports/{journey_id}.json > journey.json
```

Markdown format:
```bash
curl http://127.0.0.1:9119/api/plugins/hermes-labyrinth/reports/{journey_id}.md > journey.md
```

Both formats include automatic secret redaction.

## Python Plugin API Extension

Hermes Labyrinth extends the dashboard with custom backend routes. The core implementation is in `dashboard/plugin_api.py`.

### Example: Custom API Route

```python
from fastapi import APIRouter, HTTPException
from typing import Dict, Any
import os

router = APIRouter(prefix="/api/plugins/hermes-labyrinth")

@router.get("/custom-endpoint")
async def custom_endpoint() -> Dict[str, Any]:
    """Example custom endpoint for plugin."""
    hermes_home = os.environ.get("HERMES_HOME", os.path.expanduser("~/.hermes"))
    
    # Read-only access to Hermes state
    state_db = os.path.join(hermes_home, "state.db")
    
    if not os.path.exists(state_db):
        raise HTTPException(status_code=503, detail="Hermes state unavailable")
    
    return {
        "status": "ok",
        "hermes_home": hermes_home
    }
```

### Accessing Hermes State

The plugin reads from:
- `~/.hermes/state.db`: Sessions and messages (SQLite)
- `~/.hermes/skills/`: User and bundled skills
- `~/.hermes/cron/`: Scheduled job configurations

```python
import sqlite3
import os

def query_journeys(hermes_home: str):
    """Query recent sessions from Hermes state.db"""
    state_db = os.path.join(hermes_home, "state.db")
    
    conn = sqlite3.connect(state_db)
    conn.row_factory = sqlite3.Row
    
    cursor = conn.cursor()
    cursor.execute("""
        SELECT session_id, created_at, updated_at, status, model
        FROM sessions
        ORDER BY updated_at DESC
        LIMIT 100
    """)
    
    sessions = [dict(row) for row in cursor.fetchall()]
    conn.close()
    
    return sessions
```

## Frontend Development

The dashboard UI is built from modular JavaScript components.

### Build Process

```bash
npm run build
```

Generates:
- `dashboard/dist/labyrinth-bundle.js`: Combined JS from `src/parts/*.js`
- `dashboard/dist/labyrinth.css`: Copied from `src/labyrinth.css`
- `index.html`: GitHub Pages demo with content hashes

### Development Workflow

```bash
# Build and verify
npm run build
npm run check

# Run smoke tests
npm run smoke

# Test live demo
npm run smoke:live
```

### Frontend Structure

```javascript
// src/parts/01-state.js - Global state management
const LabyrinthState = {
  journeys: [],
  selectedJourney: null,
  crossings: [],
  mode: 'chronological'
};

// src/parts/02-api.js - API client
async function fetchJourneys() {
  const response = await fetch('/api/plugins/hermes-labyrinth/journeys');
  return response.json();
}

// src/parts/03-components.js - UI components
function renderJourneyCard(journey) {
  return `
    <div class="journey-card" data-id="${journey.id}">
      <h3>${escapeHtml(journey.title)}</h3>
      <time>${new Date(journey.created_at * 1000).toLocaleString()}</time>
    </div>
  `;
}
```

## Configuration

### Optional Theme

```bash
mkdir -p ~/.hermes/dashboard-themes
cp ~/.hermes/plugins/hermes-labyrinth/theme/hermes-labyrinth.yaml ~/.hermes/dashboard-themes/
```

### Plugin Manifest

Located at `dashboard/manifest.json`:

```json
{
  "name": "hermes-labyrinth",
  "version": "0.1.3",
  "title": "Labyrinth",
  "description": "Agent journey observability",
  "entry_js": "dist/labyrinth-bundle.js",
  "entry_css": "dist/labyrinth.css",
  "api_module": "plugin_api"
}
```

## Common Patterns

### Filtering Journeys by Status

```bash
# Get all journeys and filter with jq
curl -s http://127.0.0.1:9119/api/plugins/hermes-labyrinth/journeys | \
  jq '.journeys[] | select(.status == "completed")'
```

### Analyzing Tool Call Patterns

```bash
# Get crossings and count tool types
curl -s http://127.0.0.1:9119/api/plugins/hermes-labyrinth/journeys/{journey_id}/crossings | \
  jq '.crossings[] | select(.type == "tool_call") | .tool_name' | \
  sort | uniq -c
```

### Exporting Multiple Journeys

```bash
# Export last 10 journeys as JSON
curl -s http://127.0.0.1:9119/api/plugins/hermes-labyrinth/journeys | \
  jq -r '.journeys[].id' | head -10 | while read id; do
    curl -s "http://127.0.0.1:9119/api/plugins/hermes-labyrinth/reports/$id.json" > "journey_$id.json"
  done
```

### Monitoring Agent Failures

```bash
# List failed journeys with error details
curl -s http://127.0.0.1:9119/api/plugins/hermes-labyrinth/journeys | \
  jq '.journeys[] | select(.status == "failed") | {id, error, updated_at}'
```

## Security and Redaction

Labyrinth applies Hermes core secret redaction to all outputs:

```python
from hermes.redactor import redact_secrets

def safe_export(content: str) -> str:
    """Apply redaction before export."""
    try:
        return redact_secrets(content)
    except Exception:
        # Fail closed if redactor unavailable
        return "[redaction unavailable]"
```

### Verification Before Production

Test redaction with dummy secrets:

```bash
# Create test journey with dummy API keys in prompts/outputs
hermes chat "Test: sk-dummy-key-12345"

# Verify redaction in UI and exports
curl http://127.0.0.1:9119/api/plugins/hermes-labyrinth/reports/{journey_id}.json | grep "sk-dummy"
# Should return nothing if redaction works

curl http://127.0.0.1:9119/api/plugins/hermes-labyrinth/reports/{journey_id}.md | grep "sk-dummy"
# Should return nothing if redaction works
```

## Testing

### Run Full Test Suite

```bash
npm test
```

Includes:
- Build reproducibility checks
- JavaScript/Python parse validation
- API normalization fixture tests
- Headless Chrome smoke tests

### Backend API Tests

```bash
python3 scripts/test-plugin-api.py
```

Tests timestamp normalization, redaction, and data structure handling.

### Manual Smoke Test

```bash
npm run smoke
```

Runs headless Chrome against built demo:
- Map mode switching
- Route navigation
- Search functionality
- Dataset switching
- Threshold filtering

## Troubleshooting

### Plugin Not Appearing in Dashboard

1. Verify installation path:
```bash
ls -la ~/.hermes/plugins/hermes-labyrinth/dashboard/manifest.json
```

2. Restart dashboard (rescan doesn't mount new Python routes):
```bash
# Stop dashboard
hermes dashboard stop

# Start dashboard
hermes dashboard
```

3. Check dashboard logs for plugin API import errors.

### Backend/API Diagnostic in UI

This means the Python API routes failed to load:

1. Restart dashboard completely (not just rescan)
2. Check Python imports:
```bash
python3 -c "import sys; sys.path.insert(0, '$HOME/.hermes/plugins/hermes-labyrinth/dashboard'); import plugin_api"
```

3. Verify Hermes home exists:
```bash
ls -la ~/.hermes/state.db
```

### Redaction Unavailable Error

If reports show `[redaction unavailable]`:

1. Verify Hermes core is installed properly
2. Check redactor import:
```python
from hermes.redactor import redact_secrets
```

3. This is a fail-closed safety mechanism — plugin won't export unredacted content

### Empty Journeys List

1. Verify agent sessions exist:
```bash
sqlite3 ~/.hermes/state.db "SELECT COUNT(*) FROM sessions;"
```

2. Check file permissions:
```bash
ls -la ~/.hermes/state.db
```

3. Run health check:
```bash
curl http://127.0.0.1:9119/api/plugins/hermes-labyrinth/health
```

### Build Failures

```bash
# Clean and rebuild
rm -rf dashboard/dist index.html
npm run build
npm run check
```

## Development References

- **Live demo**: https://stainlu.github.io/hermes-labyrinth/
- **Hermes Agent**: https://github.com/NousResearch/hermes-agent
- **Plugin docs**: `docs/CONCEPT.md`, `docs/DESIGN_BRIEF.md`, `docs/FUNCTIONAL_SPEC.md`
- **License**: MIT
