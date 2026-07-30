```markdown
---
name: clawteam-openclaw-swarm
description: Multi-agent swarm coordination using ClawTeam with OpenClaw, Claude Code, Codex, or any CLI coding agent
triggers:
  - "spawn multiple agents to work on this"
  - "create a team of AI agents"
  - "use clawteam to coordinate agents"
  - "split this work across a swarm"
  - "launch workers to handle subtasks"
  - "set up multi-agent collaboration"
  - "coordinate openclaw agents in parallel"
  - "distribute tasks across agent team"
---

# ClawTeam-OpenClaw Swarm Coordination

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

ClawTeam-OpenClaw is a multi-agent swarm coordination framework that enables AI coding agents to spawn, manage, and coordinate other agents. It's a fork of HKUDS/ClawTeam with deep OpenClaw integration, but supports any CLI agent (Claude Code, Codex, Hermes Agent, nanobot, Cursor, custom scripts).

**Key capabilities:**
- Agents spawn other agents with isolated git worktrees and sessions
- Built-in task queue, inbox system, and progress tracking
- Live monitoring via Web UI or tmux board
- File-based or ZeroMQ P2P transport
- Cross-platform (Linux, macOS, Windows native or WSL)

## Installation

### Prerequisites

- Python 3.10+
- At least one CLI coding agent (OpenClaw, Claude Code, Codex, etc.)
- `tmux` (Linux/macOS/WSL only — Windows uses subprocess backend)

### Install ClawTeam

```bash
git clone https://github.com/win4r/ClawTeam-OpenClaw.git
cd ClawTeam-OpenClaw
pip install -e .

# Optional: P2P transport
pip install -e ".[p2p]"
```

### Ensure clawteam is on PATH

```bash
mkdir -p ~/bin
ln -sf "$(which clawteam)" ~/bin/clawteam
export PATH="$HOME/bin:$PATH"
```

### OpenClaw-specific setup

```bash
# Install skill
mkdir -p ~/.openclaw/workspace/skills/clawteam
cp skills/openclaw/SKILL.md ~/.openclaw/workspace/skills/clawteam/SKILL.md

# Configure exec approvals
python3 -c "
import json, pathlib
p = pathlib.Path.home() / '.openclaw' / 'exec-approvals.json'
if p.exists():
    d = json.loads(p.read_text())
    d.setdefault('defaults', {})['security'] = 'allowlist'
    p.write_text(json.dumps(d, indent=2))
"

openclaw approvals allowlist add --agent "*" "$(which clawteam)"
```

### Hermes Agent setup

```bash
mkdir -p ~/.hermes/skills/openclaw-imports/clawteam
cp skills/hermes/SKILL.md ~/.hermes/skills/openclaw-imports/clawteam/SKILL.md
```

### Verify installation

```bash
clawteam --version
clawteam config health
```

## Core Concepts

### Teams
A team is a workspace where agents collaborate. Each team has:
- A root directory with git worktrees for each agent
- A shared task queue
- Inbox system for agent-to-agent messages
- Progress tracking and monitoring

### Agents
Each spawned agent gets:
- Its own git worktree (isolated branch)
- Dedicated session (tmux pane or subprocess)
- Unique identity and workspace
- Access to team resources (tasks, inboxes, board)

### Transport Modes
- **file** (default): Filesystem-based coordination
- **p2p**: ZeroMQ-based peer-to-peer messaging

## Key Commands

### Team Management

```bash
# Launch a team with a leader agent
clawteam team launch my-team \
  --goal "Build a REST API with auth" \
  --command openclaw

# List teams
clawteam team list

# Get team status
clawteam team status my-team

# Cleanup team (kills agents, preserves worktrees)
clawteam team cleanup my-team

# Delete team entirely
clawteam team delete my-team
```

### Spawning Agents

```bash
# Spawn OpenClaw agent (default)
clawteam spawn --team my-team \
  --agent-name worker1 \
  --task "Implement user authentication"

# Spawn Claude Code agent
clawteam spawn claude --team my-team \
  --agent-name worker2 \
  --task "Write integration tests"

# Spawn Codex agent
clawteam spawn codex --team my-team \
  --agent-name worker3 \
  --task "Optimize database queries"

# Spawn Hermes agent
clawteam spawn --team my-team \
  --agent-name worker4 \
  --task "Add logging and monitoring" \
  hermes

# Spawn with custom backend
clawteam spawn subprocess python --team my-team \
  --agent-name worker5 \
  --task "Run data migration"
```

### Task Management

```bash
# List all tasks
clawteam task list my-team

# List my tasks
clawteam task list my-team --owner me

# Update task status
clawteam task update my-team task-001 \
  --status in_progress \
  --notes "Auth flow implemented, testing needed"

# Mark task complete
clawteam task update my-team task-001 --status done
```

### Inbox Communication

```bash
# Send message to another agent
clawteam inbox send my-team leader \
  "Auth module complete. All tests passing."

# Read my inbox
clawteam inbox read my-team me

# Mark message as read
clawteam inbox mark-read my-team message-id-123
```

### Monitoring

```bash
# Web UI (cross-platform)
clawteam board serve --port 8080

# Tmux board (Linux/macOS/WSL)
clawteam board attach my-team

# Get progress summary
clawteam board progress my-team
```

### Configuration

```bash
# Get config value
clawteam config get default_backend

# Set config value
clawteam config set default_backend subprocess

# List all config
clawteam config list

# Health check
clawteam config health
```

## Usage Patterns

### Pattern 1: Agent-Driven Swarm (Recommended)

Let the agent orchestrate everything:

```python
# User prompt to your agent:
"""
Build a web app with:
- FastAPI backend with user auth
- React frontend with login/signup
- PostgreSQL database
- Docker deployment

Use clawteam to coordinate multiple agents.
Split work across frontend, backend, database, and devops specialists.
"""
```

The agent will:
1. Launch a team
2. Spawn specialized worker agents
3. Assign tasks via the task queue
4. Monitor progress and coordinate
5. Merge results and report back

### Pattern 2: Manual Team Launch

```bash
# Launch team with OpenClaw leader
clawteam team launch ecommerce \
  --goal "Build e-commerce platform" \
  --command openclaw

# Wait 60s for leader to boot
sleep 60

# Spawn specialized workers
clawteam spawn --team ecommerce \
  --agent-name backend \
  --task "Implement product catalog API"

clawteam spawn --team ecommerce \
  --agent-name frontend \
  --task "Build shopping cart UI"

clawteam spawn --team ecommerce \
  --agent-name database \
  --task "Design and migrate database schema"

# Monitor
clawteam board serve --port 8080
```

### Pattern 3: Hermes Agent Swarm

When using Hermes Agent, always specify `hermes` and use proper flags:

```bash
# Launch with Hermes leader
clawteam team launch data-pipeline \
  --team-name data-pipeline \
  -g "Build ETL pipeline for customer data" \
  --command hermes \
  --force

# Wait for boot
sleep 60

# Spawn Hermes workers (note: hermes is positional arg)
clawteam spawn --team data-pipeline \
  --agent-name extractor \
  --task "Extract data from legacy CRM" \
  hermes

clawteam spawn --team data-pipeline \
  --agent-name transformer \
  --task "Transform and normalize data" \
  hermes

# Wait, then check inboxes (never before 60s)
sleep 90
clawteam inbox read data-pipeline leader

# Monitor every 30s
while true; do
  clawteam board progress data-pipeline
  sleep 30
done
```

### Pattern 4: Programmatic Control (Python)

```python
import subprocess
import time
import json

def run_cmd(cmd):
    """Run clawteam command and return output"""
    result = subprocess.run(
        cmd, shell=True, capture_output=True, text=True
    )
    return result.stdout.strip()

# Launch team
run_cmd("clawteam team launch ml-project --goal 'Train sentiment model' --command openclaw")
time.sleep(60)

# Spawn workers
workers = [
    ("data-engineer", "Prepare and clean dataset"),
    ("ml-engineer", "Train sentiment classification model"),
    ("devops", "Set up training infrastructure"),
]

for name, task in workers:
    run_cmd(f"clawteam spawn --team ml-project --agent-name {name} --task '{task}'")

# Monitor progress
while True:
    progress = run_cmd("clawteam board progress ml-project")
    print(progress)
    
    # Check if all tasks done
    tasks = run_cmd("clawteam task list ml-project")
    if "status: done" in tasks and tasks.count("status: done") == len(workers):
        break
    
    time.sleep(30)

# Read final messages
inbox = run_cmd("clawteam inbox read ml-project leader")
print(f"Final inbox:\n{inbox}")

# Cleanup
run_cmd("clawteam team cleanup ml-project")
```

### Pattern 5: Cross-Agent Collaboration

Agents communicate via inboxes to coordinate work:

```bash
# Agent A discovers dependency
clawteam inbox send my-team agent-b \
  "Need auth module completed before I can test user endpoints"

# Agent B acknowledges
clawteam inbox send my-team agent-a \
  "Auth module pushed to auth-branch. Ready for integration."

# Agent A integrates
cd $(clawteam config get teams_dir)/my-team/agent-a
git fetch
git merge origin/auth-branch
```

## Real-World Example: Microservices Architecture

```bash
# Launch team
clawteam team launch microservices \
  --goal "Build microservices platform: API gateway, auth service, user service, notification service" \
  --command openclaw

sleep 60

# Spawn service specialists
clawteam spawn --team microservices \
  --agent-name gateway \
  --task "Implement API gateway with rate limiting and routing"

clawteam spawn --team microservices \
  --agent-name auth \
  --task "Build JWT-based auth service with refresh tokens"

clawteam spawn --team microservices \
  --agent-name users \
  --task "Create user CRUD service with validation"

clawteam spawn --team microservices \
  --agent-name notifications \
  --task "Build email and SMS notification service"

clawteam spawn --team microservices \
  --agent-name devops \
  --task "Create docker-compose and k8s deployment configs"

# Start monitoring UI
clawteam board serve --port 8080 &

# Wait for completion (check every minute)
while true; do
  status=$(clawteam board progress microservices)
  echo "$status"
  
  if echo "$status" | grep -q "All tasks complete"; then
    echo "Microservices platform ready!"
    break
  fi
  
  sleep 60
done

# Review final state
clawteam inbox read microservices leader
clawteam task list microservices
```

## Configuration

ClawTeam stores config in `~/.clawteam/config.yaml`:

```yaml
default_backend: tmux  # or subprocess (Windows default)
teams_dir: ~/.clawteam/teams
transport: file  # or p2p
log_level: INFO

# P2P transport settings (optional)
p2p:
  port: 5555
  discovery: multicast
```

### Environment Variables

```bash
# Override teams directory
export CLAWTEAM_TEAMS_DIR=/path/to/teams

# Set default backend
export CLAWTEAM_DEFAULT_BACKEND=subprocess

# Enable debug logging
export CLAWTEAM_LOG_LEVEL=DEBUG

# P2P transport port
export CLAWTEAM_P2P_PORT=5555
```

## Backend Selection

### tmux backend (Linux/macOS/WSL)
- Visual monitoring with `board attach`
- Session persistence
- Full terminal support

### subprocess backend (Windows native, fallback)
- Cross-platform compatibility
- No tmux dependency
- Suitable for CI/CD

### Backend priority
1. Explicitly set via `--backend` flag
2. Environment variable `CLAWTEAM_DEFAULT_BACKEND`
3. Config file `default_backend`
4. Auto-detect (tmux on Unix, subprocess on Windows)

## Transport Modes

### File transport (default)
```bash
clawteam config set transport file
```
- Uses filesystem for coordination
- Simple, no dependencies
- Works everywhere

### P2P transport (advanced)
```bash
pip install -e ".[p2p]"
clawteam config set transport p2p
clawteam config set p2p.port 5555
```
- ZeroMQ-based messaging
- Lower latency
- Better for large swarms

## Troubleshooting

### "clawteam: command not found" in spawned agents

The agent's shell can't find clawteam. Add to PATH:

```bash
mkdir -p ~/bin
ln -sf "$(which clawteam)" ~/bin/clawteam
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
```

### OpenClaw agents block on exec approvals

Security mode must be "allowlist" and clawteam must be allowed:

```bash
openclaw approvals allowlist add --agent "*" "$(which clawteam)"
```

### "Team not found" errors

Check team name matches exactly (case-sensitive):

```bash
clawteam team list
```

### Agents not responding

Check process status:

```bash
# tmux backend
tmux list-sessions | grep clawteam-

# subprocess backend
ps aux | grep clawteam

# Or use built-in health check
clawteam config health
```

### Tasks stuck in "pending"

Agents may not be checking the queue. Send inbox reminder:

```bash
clawteam inbox send my-team worker1 \
  "Check task queue: clawteam task list my-team --owner me"
```

### Worktree merge conflicts

Each agent works in its own branch. To merge:

```bash
cd ~/.clawteam/teams/my-team/leader
git fetch --all
git merge agent-name-branch --no-ff
# Resolve conflicts
git commit
```

### Windows-specific issues

- Use PowerShell or Windows Terminal (not cmd.exe)
- Verify backend: `clawteam config get default_backend` should show `subprocess`
- For tmux features, use WSL instead of native Windows
- Ensure Python Scripts directory is on PATH

## Best Practices

### 1. Always wait after launch
```bash
clawteam team launch my-team --goal "..." --command openclaw
sleep 60  # Let leader boot and initialize
```

### 2. Use descriptive agent names
```bash
# Good
clawteam spawn --team api --agent-name auth-specialist --task "..."

# Bad
clawteam spawn --team api --agent-name w1 --task "..."
```

### 3. Check inbox regularly (but not too early)
```bash
# Wait at least 60s before first inbox check
sleep 60
clawteam inbox read my-team leader

# Then poll every 30-60s
while true; do
  clawteam inbox read my-team leader
  sleep 30
done
```

### 4. Monitor progress, don't micromanage
```bash
# Use board for overview
clawteam board serve --port 8080

# Don't spam inbox checks — let agents work
```

### 5. Clean up after completion
```bash
# Preserve work, kill processes
clawteam team cleanup my-team

# Or delete entirely
clawteam team delete my-team
```

### 6. Use task notes for context
```bash
clawteam task update my-team task-001 \
  --status in_progress \
  --notes "Implemented OAuth2 flow. Need to add refresh token rotation before marking done."
```

### 7. Hermes-specific: Always pass command correctly
```bash
# Launch: use --command
clawteam team launch ... --command hermes

# Spawn: use positional arg
clawteam spawn --team ... hermes
```

## Integration with Other Tools

### Git workflow
```bash
# Each agent's work is in its own worktree/branch
cd ~/.clawteam/teams/my-team/agent-name
git log --oneline
git diff main

# Merge into main when done
cd ~/.clawteam/teams/my-team/leader
git merge agent-name-branch --no-ff
```

### CI/CD integration
```bash
# Use subprocess backend for CI
export CLAWTEAM_DEFAULT_BACKEND=subprocess

# Launch team, wait for completion, collect results
clawteam team launch ci-build --goal "Run full test suite" --command openclaw
sleep 60

# Poll until done
while ! clawteam board progress ci-build | grep -q "All tasks complete"; do
  sleep 30
done

# Collect artifacts
cp ~/.clawteam/teams/ci-build/*/test-results.xml ./artifacts/
```

### MCP servers (Hermes)
Spawned Hermes agents inherit `~/.hermes/config.yaml` MCP server config:

```yaml
# ~/.hermes/config.yaml
mcp_servers:
  - name: knowledge
    command: ["python", "-m", "knowledge_server"]
  - name: browser
    command: ["npx", "browser-mcp"]
```

All Hermes workers automatically have access to these tools.

## API Reference Summary

### Team commands
- `clawteam team launch <name> --goal <goal> [--command <agent>]`
- `clawteam team list`
- `clawteam team status <name>`
- `clawteam team cleanup <name>`
- `clawteam team delete <name>`

### Spawn commands
- `clawteam spawn --team <name> --agent-name <name> --task <task> [agent-type]`
- `clawteam spawn claude --team <name> ...`
- `clawteam spawn codex --team <name> ...`
- `clawteam spawn subprocess <cmd> --team <name> ...`

### Task commands
- `clawteam task list <team> [--owner <agent>]`
- `clawteam task update <team> <task-id> --status <status> [--notes <notes>]`

### Inbox commands
- `clawteam inbox send <team> <recipient> <message>`
- `clawteam inbox read <team> <agent>`
- `clawteam inbox mark-read <team> <message-id>`

### Board commands
- `clawteam board serve [--port <port>]`
- `clawteam board attach <team>`
- `clawteam board progress <team>`

### Config commands
- `clawteam config get <key>`
- `clawteam config set <key> <value>`
- `clawteam config list`
- `clawteam config health`

---

This skill enables AI coding agents to orchestrate multi-agent swarms using ClawTeam. Agents can spawn workers, delegate tasks, monitor progress, and coordinate results — all through the clawteam CLI.
```
