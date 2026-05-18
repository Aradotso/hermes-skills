---
name: hermes-agent-optimization
description: Expert in deploying, configuring, securing, and optimizing Hermes Agent multi-platform AI orchestration
triggers:
  - "help me set up hermes agent"
  - "how do i configure hermes for production"
  - "add a telegram bot to hermes"
  - "optimize hermes agent costs"
  - "secure my hermes deployment"
  - "integrate MCP servers with hermes"
  - "set up hermes coding agents"
  - "configure hermes memory system"
---

# Hermes Agent Optimization

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

Hermes Agent is a production-grade AI orchestration framework that connects 20+ platforms (Telegram, Discord, Slack, Google Chat, SMS, email, webhooks, cron, CLI, TUI) to multiple LLM providers (Anthropic, OpenAI, Google, Cerebras, xAI, local models) with built-in memory, MCP tool integration, coding agent orchestration, durable Kanban execution, security layers, and observability.

This skill covers installation, configuration, platform setup, cost optimization, security hardening, MCP integration, coding agent orchestration, memory systems, and production deployment patterns.

## Installation

### Quick Local Setup (5 minutes)

```bash
# Install Hermes (requires Node.js 18+)
npm install -g hermes-agent

# Verify installation
hermes --version

# Initialize config directory
mkdir -p ~/.hermes/skills
hermes init

# Start interactive TUI
hermes --tui
```

### Production VPS Bootstrap (Debian 12 / Ubuntu 24.04)

```bash
# One-command deployment (installs Hermes, Caddy, UFW, fail2ban, systemd units)
curl -sSL https://raw.githubusercontent.com/OnlyTerp/hermes-optimization-guide/main/scripts/vps-bootstrap.sh | sudo bash

# What it installs:
# - Hermes Agent v0.13.0+
# - Node.js 20 LTS
# - Caddy (reverse proxy with auto-TLS)
# - UFW firewall (22, 80, 443 only)
# - fail2ban (SSH brute-force protection)
# - systemd units (hermes.service, hermes-dashboard.service)
# - Non-root hermes user with proper permissions
# - All guide skills symlinked into ~/.hermes/skills/

# Check status
sudo systemctl status hermes
sudo systemctl status hermes-dashboard
```

### Update Hermes

```bash
# Update to latest version
npm update -g hermes-agent

# Or use built-in updater (in TUI or dashboard)
hermes update

# Reload config without restart
hermes reload
```

## Configuration Architecture

Hermes uses `~/.hermes/config.yaml` for all settings. Configuration cascades: defaults → `config.yaml` → environment variables → runtime flags.

### Minimal Config (Anthropic + CLI only)

```yaml
# ~/.hermes/config.yaml
providers:
  anthropic:
    api_key: ${ANTHROPIC_API_KEY}
    models:
      - claude-3-5-sonnet-20241022
      - claude-3-5-haiku-20241022

model_routing:
  default: claude-3-5-sonnet-20241022
  fast: claude-3-5-haiku-20241022

security:
  secret_redaction: true
  approval_mode: off

gateways:
  cli:
    enabled: true
```

### Production Config Pattern

```yaml
# ~/.hermes/config.yaml
providers:
  anthropic:
    api_key: ${ANTHROPIC_API_KEY}
    models: [claude-3-5-sonnet-20241022, claude-3-5-haiku-20241022]
  openai:
    api_key: ${OPENAI_API_KEY}
    models: [gpt-4o, gpt-4o-mini]
  google:
    api_key: ${GOOGLE_AI_API_KEY}
    models: [gemini-2.0-flash-exp, gemini-1.5-flash-002]

model_routing:
  default: claude-3-5-sonnet-20241022
  fast: gemini-2.0-flash-exp
  coding: claude-3-5-sonnet-20241022
  cheap: gpt-4o-mini
  multimodal: gemini-2.0-flash-exp

gateways:
  telegram:
    enabled: true
    token: ${TELEGRAM_BOT_TOKEN}
    allowed_users: [123456789]  # Your Telegram user ID
  discord:
    enabled: true
    token: ${DISCORD_BOT_TOKEN}
    guild_role_allowlist:
      "987654321098765432": ["Admin", "Developer"]  # Guild ID: roles
  webhooks:
    enabled: true
    port: 3000
    secret: ${WEBHOOK_SECRET}
  dashboard:
    enabled: true
    port: 8080
    auth_password: ${DASHBOARD_PASSWORD}

memory:
  provider: lightrag
  lightrag:
    working_dir: ~/.hermes/lightrag
    embedding_model: text-embedding-3-small
    embedding_provider: openai
    llm_model: gpt-4o-mini

tools:
  mcp:
    enabled: true
    servers:
      filesystem:
        command: npx
        args: ["-y", "@modelcontextprotocol/server-filesystem", "/home/user/projects"]
      github:
        command: npx
        args: ["-y", "@modelcontextprotocol/server-github"]
        env:
          GITHUB_PERSONAL_ACCESS_TOKEN: ${GITHUB_TOKEN}

coding_agents:
  claude_code:
    enabled: true
    workspace: ~/projects
    model: claude-3-5-sonnet-20241022
  codex:
    enabled: true
    workspace: ~/projects

kanban:
  enabled: true
  boards_dir: ~/.hermes/kanban
  heartbeat_interval: 60
  zombie_timeout: 300
  max_retries: 3

checkpoints:
  enabled: true
  max_size_mb: 100
  prune_after_days: 7
  syntax_validation: true

observability:
  langfuse:
    enabled: true
    public_key: ${LANGFUSE_PUBLIC_KEY}
    secret_key: ${LANGFUSE_SECRET_KEY}
    host: https://cloud.langfuse.com
  audit_log: ~/.hermes/audit.jsonl

security:
  secret_redaction: true
  approval_mode: interactive  # off | interactive | strict
  tool_denylist: [rm, dd, mkfs]
  allowed_domains: [github.com, api.anthropic.com]
  rate_limits:
    per_user_per_hour: 100
    per_ip_per_minute: 20

cron:
  - schedule: "0 */6 * * *"
    skill: backup-check
    no_agent: true
  - schedule: "0 2 * * *"
    skill: disk-cleanup
    no_agent: true

curator:
  enabled: true
  schedule: "0 3 * * 0"  # Sunday 3 AM
  min_score: 0.6
  auto_archive_days: 30
```

## Platform Setup

### Telegram Bot

```bash
# 1. Get token from @BotFather
# /newbot → choose name → copy token

# 2. Get your user ID from @userinfobot
# /start → copy your ID

# 3. Add to config
cat >> ~/.hermes/config.yaml <<EOF
gateways:
  telegram:
    enabled: true
    token: ${TELEGRAM_BOT_TOKEN}
    allowed_users: [YOUR_USER_ID]
    enable_voice: true  # Voice message transcription
    enable_images: true  # Image analysis
EOF

# 4. Restart Hermes
sudo systemctl restart hermes

# 5. Test in Telegram
# /start
# /help
# What's the weather in San Francisco?
```

### Discord Bot

```bash
# 1. Create bot at https://discord.com/developers/applications
# New Application → Bot → Reset Token → Copy token
# OAuth2 → Scopes: bot → Permissions: Read Messages, Send Messages, Manage Messages
# Copy invite URL → paste in browser → add to server

# 2. Get Guild ID (right-click server → Copy Server ID, enable Developer Mode first)

# 3. Add to config
cat >> ~/.hermes/config.yaml <<EOF
gateways:
  discord:
    enabled: true
    token: ${DISCORD_BOT_TOKEN}
    guild_role_allowlist:
      "YOUR_GUILD_ID": ["Admin", "Developer"]  # Only these roles can use bot
    mention_mode: true  # Respond only when @mentioned
EOF

# 4. Restart and test
sudo systemctl restart hermes
# In Discord: @YourBot what time is it?
```

### Slack Bot

```bash
# 1. Create app at https://api.slack.com/apps
# Create New App → From scratch
# OAuth & Permissions → Scopes: chat:write, channels:history, groups:history, im:history
# Install to Workspace → copy Bot User OAuth Token

# 2. Event Subscriptions → Enable → Request URL: https://your-domain.com/slack/events
# Subscribe to: message.channels, message.groups, message.im

# 3. Add to config
cat >> ~/.hermes/config.yaml <<EOF
gateways:
  slack:
    enabled: true
    bot_token: ${SLACK_BOT_TOKEN}
    signing_secret: ${SLACK_SIGNING_SECRET}
    allowed_channels: [C01ABC123, C02DEF456]  # Channel IDs
EOF
```

### Webhook Gateway

```bash
# Receive HTTP POST requests as agent triggers

cat >> ~/.hermes/config.yaml <<EOF
gateways:
  webhooks:
    enabled: true
    port: 3000
    secret: ${WEBHOOK_SECRET}  # HMAC signature verification
    endpoints:
      github:
        path: /github
        verify_signature: true
      stripe:
        path: /stripe
        verify_signature: true
      generic:
        path: /webhook
        verify_signature: false
EOF

# Test webhook
curl -X POST http://localhost:3000/webhook \
  -H "Content-Type: application/json" \
  -H "X-Webhook-Secret: your-secret" \
  -d '{"message": "Deploy completed", "repo": "myapp"}'
```

### Cron Jobs (Scheduled Tasks)

```yaml
# ~/.hermes/config.yaml
cron:
  # Agent-powered task (uses LLM)
  - schedule: "0 9 * * *"  # Daily 9 AM
    skill: daily-standup-summary
    model: gpt-4o-mini
    
  # No-agent task (deterministic script, zero LLM cost)
  - schedule: "*/15 * * * *"  # Every 15 min
    skill: disk-space-alert
    no_agent: true  # Just runs script, delivers stdout
    
  # Watchdog with context
  - schedule: "0 */6 * * *"  # Every 6 hours
    skill: backup-verify
    no_agent: true
    env:
      BACKUP_DIR: /var/backups/hermes
```

**No-agent cron pattern** (Part 23):
```bash
# Create watchdog skill
cat > ~/.hermes/skills/disk-watchdog.md <<'EOF'
---
name: disk-watchdog
description: Alert if disk usage exceeds 80%
---
#!/bin/bash
USAGE=$(df -h / | tail -1 | awk '{print $5}' | sed 's/%//')
if [ $USAGE -gt 80 ]; then
  echo "⚠️ ALERT: Disk usage at ${USAGE}%"
  exit 1
else
  echo "✓ Disk usage OK (${USAGE}%)"
fi
EOF

# Schedule as no-agent cron
# Output delivered to all gateways, zero LLM spend
```

## Model Routing & Cost Optimization

### Smart Routing by Capability

```yaml
model_routing:
  default: claude-3-5-sonnet-20241022  # Best reasoning
  fast: gemini-2.0-flash-exp           # Fastest, cheapest
  coding: claude-3-5-sonnet-20241022   # Best for code
  cheap: gpt-4o-mini                   # Fallback budget
  multimodal: gemini-2.0-flash-exp     # Images/video
  long_context: claude-3-5-sonnet-20241022  # 200K context
  
  # Route by task
  route_by_complexity: true
  complexity_threshold: 0.7  # 0-1 score, >0.7 uses default, <0.7 uses fast
```

### Cost Table (as of May 2026)

| Model | Input $/1M | Output $/1M | Context | Speed | Best For |
|---|---|---|---|---|---|
| Claude 3.5 Sonnet | $3 | $15 | 200K | Medium | Reasoning, coding, long docs |
| Claude 3.5 Haiku | $0.80 | $4 | 200K | Fast | Quick tasks, tool use |
| GPT-4o | $2.50 | $10 | 128K | Medium | General purpose |
| GPT-4o-mini | $0.15 | $0.60 | 128K | Fast | Cheap fallback |
| Gemini 2.0 Flash | $0.075 | $0.30 | 1M | Very fast | Multimodal, huge context |
| Gemini 1.5 Flash | $0.075 | $0.30 | 1M | Very fast | Best $/token ratio |
| Cerebras Llama 3.1 70B | $0.60 | $0.60 | 8K | Instant | Ultra-fast, but small context |

### Routing Playbook (Part 20)

```yaml
# Example: $100/day → $10/day with smart routing

providers:
  anthropic:
    api_key: ${ANTHROPIC_API_KEY}
    models: [claude-3-5-sonnet-20241022]
  google:
    api_key: ${GOOGLE_AI_API_KEY}
    models: [gemini-2.0-flash-exp]
  openai:
    api_key: ${OPENAI_API_KEY}
    models: [gpt-4o-mini]

model_routing:
  default: gemini-2.0-flash-exp         # 95% of requests ($0.075/$0.30)
  coding: claude-3-5-sonnet-20241022    # Only for code ($3/$15)
  cheap: gpt-4o-mini                    # Tiny tasks ($0.15/$0.60)
  
  route_by_complexity: true
  complexity_threshold: 0.8  # Only 20% go to Claude
  
  # Force cheap model for low-stakes tools
  tool_routing:
    web_search: gpt-4o-mini
    calculator: gpt-4o-mini
    time: gpt-4o-mini
```

**Result**: Same UX, 90% cost reduction.

## Memory Systems

### LightRAG Setup (Part 3)

```yaml
# ~/.hermes/config.yaml
memory:
  provider: lightrag
  lightrag:
    working_dir: ~/.hermes/lightrag
    embedding_model: text-embedding-3-small
    embedding_provider: openai
    llm_model: gpt-4o-mini  # Cheap model for graph construction
    graph_enabled: true
    chunk_size: 1200
    chunk_overlap: 100
```

```bash
# Install LightRAG
pip install lightrag-hku

# Index a knowledge base
hermes memory index ~/projects/documentation --recursive

# Query memory
hermes memory query "How do I deploy to production?"

# Inspect graph
hermes memory graph --visualize
```

### Vector Memory (Alternative)

```yaml
memory:
  provider: chromadb
  chromadb:
    persist_directory: ~/.hermes/chroma
    embedding_model: text-embedding-3-small
    collection_name: hermes-memory
```

### mem0 Integration

```yaml
memory:
  provider: mem0
  mem0:
    api_key: ${MEM0_API_KEY}
    user_id: ${USER_ID}
    memory_type: hybrid  # vector + graph
```

## MCP Server Integration (Part 17)

Model Context Protocol servers give Hermes tools like filesystem access, GitHub API, Slack, PostgreSQL, browser control.

### Filesystem MCP

```yaml
tools:
  mcp:
    enabled: true
    servers:
      filesystem:
        command: npx
        args:
          - "-y"
          - "@modelcontextprotocol/server-filesystem"
          - "/home/user/projects"  # Allowed directory
          - "/home/user/documents"
        description: "Read/write project files"
```

### GitHub MCP

```yaml
tools:
  mcp:
    servers:
      github:
        command: npx
        args: ["-y", "@modelcontextprotocol/server-github"]
        env:
          GITHUB_PERSONAL_ACCESS_TOKEN: ${GITHUB_TOKEN}
        description: "GitHub API: repos, issues, PRs"
```

### PostgreSQL MCP

```yaml
tools:
  mcp:
    servers:
      postgres:
        command: npx
        args: ["-y", "@modelcontextprotocol/server-postgres"]
        env:
          DATABASE_URL: ${DATABASE_URL}
        description: "Query production database"
```

### Brave Search MCP

```yaml
tools:
  mcp:
    servers:
      brave:
        command: npx
        args: ["-y", "@modelcontextprotocol/server-brave-search"]
        env:
          BRAVE_API_KEY: ${BRAVE_API_KEY}
        description: "Web search"
```

### Browser Control (Playwright MCP)

```yaml
tools:
  mcp:
    servers:
      playwright:
        command: npx
        args: ["-y", "@playwright/mcp-server"]
        description: "Headless browser automation"
```

### Custom MCP Server

```javascript
// my-api-server.js
import { McpServer } from '@modelcontextprotocol/sdk';

const server = new McpServer({
  name: 'my-api',
  version: '1.0.0',
});

server.tool('deploy', {
  description: 'Deploy application to production',
  parameters: {
    type: 'object',
    properties: {
      service: { type: 'string' },
      version: { type: 'string' },
    },
    required: ['service', 'version'],
  },
}, async ({ service, version }) => {
  // Your deployment logic
  return { success: true, deployed: `${service}@${version}` };
});

server.start({ transport: 'stdio' });
```

```yaml
# Add to config
tools:
  mcp:
    servers:
      my-api:
        command: node
        args: ["/path/to/my-api-server.js"]
```

## Coding Agent Orchestration (Part 18)

Hermes can delegate to specialized coding agents: Claude Code, Cursor, Codex, Gemini CLI.

### Claude Code Integration

```yaml
coding_agents:
  claude_code:
    enabled: true
    workspace: ~/projects
    model: claude-3-5-sonnet-20241022
    api_key: ${ANTHROPIC_API_KEY}
    max_iterations: 10
```

```bash
# From Telegram/Discord/Slack:
# "Use Claude Code to add user authentication to myapp"

# Hermes delegates to Claude Code, monitors progress, reports back
```

### Cursor Integration

```yaml
coding_agents:
  cursor:
    enabled: true
    workspace: ~/projects
    composer_mode: true  # Use Cursor Composer
```

### Codex Integration

```yaml
coding_agents:
  codex:
    enabled: true
    workspace: ~/projects
    api_key: ${OPENAI_API_KEY}
```

### Gemini CLI (experimental)

```yaml
coding_agents:
  gemini_cli:
    enabled: true
    workspace: ~/projects
    api_key: ${GOOGLE_AI_API_KEY}
    model: gemini-2.0-flash-exp
```

## Durable Execution: Kanban + Goals + Checkpoints (Part 23)

Hermes v0.13 "Tenacity" makes long-running work reliable.

### Kanban for Multi-Step Tasks

```yaml
kanban:
  enabled: true
  boards_dir: ~/.hermes/kanban
  heartbeat_interval: 60  # seconds
  zombie_timeout: 300  # 5 min no heartbeat = zombie
  max_retries: 3
  human_review_on_block: true
```

**Lanes**: TODO → IN_PROGRESS → REVIEW → DONE → FAILED

```bash
# View Kanban board
hermes kanban list

# Example output:
# TODO:
#   - [TASK-001] Refactor auth module
#   - [TASK-002] Add rate limiting
# IN_PROGRESS:
#   - [TASK-003] Deploy to staging (2/10 retries, last heartbeat 30s ago)
# REVIEW:
#   - [TASK-004] Update docs (needs approval)
# DONE:
#   - [TASK-005] Fix login bug

# Unblock stuck task
hermes kanban unblock TASK-003 --reason "Missing env var fixed"

# Retry failed task
hermes kanban retry TASK-007
```

### `/goal` Persistent Objectives

```bash
# In chat (Telegram/Discord/etc):
/goal Deploy feature-x to production without breaking anything

# Hermes stays locked on this goal across sessions, restarts, errors
# Persists in ~/.hermes/goals.json

/goal_status
# Goal: Deploy feature-x to production
# Status: IN_PROGRESS (3/10 checkpoints passed)
# Last update: 2m ago

/goal_pause  # Pause work, keep context
/goal_resume  # Continue
/goal_clear  # Abandon goal
```

### Checkpoints v2

```yaml
checkpoints:
  enabled: true
  max_size_mb: 100  # Per checkpoint
  prune_after_days: 7
  syntax_validation: true  # Lint Python/JSON/YAML/TOML after write
  shadow_repo: ~/.hermes/checkpoints/shadow
```

**How it works**:
1. Before risky operation (code edit, config change, deployment), Hermes creates checkpoint
2. Operation executes
3. If operation fails, auto-rollback to checkpoint
4. Checkpoints pruned after 7 days
5. Syntax validation catches malformed files immediately

```bash
# List checkpoints
hermes checkpoint list

# Restore checkpoint
hermes checkpoint restore abc123

# Prune old checkpoints
hermes checkpoint prune --older-than 7d
```

## Security Hardening (Part 19)

### v0.13 Security Defaults

```yaml
security:
  secret_redaction: true  # Default ON (was OFF in v0.12)
  approval_mode: interactive  # off | interactive | strict
  tool_denylist:
    - rm
    - dd
    - mkfs
    - curl  # Prevent SSRF
  tool_allowlist:  # If set, ONLY these tools allowed
    - calculator
    - time
    - web_search
  allowed_domains:  # Outbound HTTP allowlist
    - github.com
    - api.anthropic.com
    - api.openai.com
  rate_limits:
    per_user_per_hour: 100
    per_ip_per_minute: 20
    per_session_per_minute: 10
```

### Approval Workflow

```yaml
security:
  approval_mode: strict
  approval_rules:
    - pattern: "rm -rf"
      action: deny
    - pattern: "DROP TABLE"
      action: require_approval
    - pattern: "git push"
      action: require_approval
      allowed_users: [123456789]  # Only these users can approve
```

**Approval flow**:
1. Agent wants to run `git push origin main`
2. Hermes pauses, sends approval request to Telegram
3. User taps Approve/Deny
4. Agent continues or aborts

### Discord Guild Role Allowlist (v0.13 fix)

```yaml
gateways:
  discord:
    guild_role_allowlist:
      "987654321098765432":  # Guild ID (server ID)
        - "Admin"
        - "Developer"
      "111222333444555666":
        - "Team"
```

**Before v0.13**: Role names collided across guilds.  
**After v0.13**: Guild-scoped, no collision.

### WhatsApp Stranger Rejection (v0.13 default)

```yaml
gateways:
  whatsapp:
    reject_strangers: true  # Default true, was false in v0.12
    allowed_numbers: ["+1234567890"]
```

### MCP OAuth Security

```yaml
tools:
  mcp:
    servers:
      github:
        command: npx
        args: ["-y", "@modelcontextprotocol/server-github"]
        env:
          GITHUB_PERSONAL_ACCESS_TOKEN: ${GITHUB_TOKEN}
        oauth_file: ~/.hermes/mcp-oauth.json  # v0.13: atomic write, no TOCTOU
```

### Firewall (UFW)

```bash
# Production VPS setup
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp  # SSH
sudo ufw allow 80/tcp  # HTTP
sudo ufw allow 443/tcp  # HTTPS
sudo ufw enable

# Rate limit SSH
sudo ufw limit 22/tcp
```

### fail2ban (SSH Brute-Force Protection)

```bash
sudo apt install fail2ban -y

# Default config bans IP after 5 failed SSH attempts in 10 min
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

### Reverse Proxy (Caddy with Auto-TLS)

```caddyfile
# /etc/caddy/Caddyfile
hermes.example.com {
    reverse_proxy localhost:8080  # Dashboard
    
    # Security headers
    header {
        Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
        X-Content-Type-Options "nosniff"
        X-Frame-Options "DENY"
        Referrer-Policy "strict-origin-when-cross-origin"
    }
    
    # Basic auth for dashboard
    basicauth {
        admin $2a$14$... # bcrypt hash
    }
}

webhooks.example.com {
    reverse_proxy localhost:3000  # Webhook gateway
    
    # Rate limit
    rate_limit {
        zone webhooks {
            key {remote_host}
            events 100
            window 1m
        }
    }
}
```

```bash
sudo systemctl reload caddy
```

## Observability (Part 20)

### Langfuse Integration

```yaml
observability:
  langfuse:
    enabled: true
    public_key: ${LANGFUSE_PUBLIC_KEY}
    secret_key: ${LANGFUSE_SECRET_KEY}
    host: https://cloud.langfuse.com  # Or self-hosted
    trace_all: true
    trace_tools: true
    trace_memory: true
```

**What you get**:
- Every LLM call traced (prompt, response, tokens, latency, cost)
- Tool use traced
- Memory queries traced
- Session analytics (cost per user, per day, per model)
- Error tracking

### Self-Hosted Langfuse (ClickHouse + MinIO)

```yaml
# docker-compose.yml (from templates/compose/)
version: '3.8'
services:
  langfuse:
    image: langfuse/langfuse:latest
    environment:
      DATABASE_URL: postgresql://langfuse:password@postgres:5432/langfuse
      CLICKHOUSE_URL: http://clickhouse:8123
      S3_ENDPOINT: http://minio:9000
      S3_BUCKET: langfuse
      S3_ACCESS_KEY: minioadmin
      S3_SECRET_KEY: minioadmin
    ports:
      - "3001:3000"
  
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: langfuse
      POSTGRES_USER: langfuse
      POSTGRES_PASSWORD: password
  
  clickhouse:
    image: clickhouse/clickhouse-server:latest
    volumes:
      - clickhouse-data:/var/lib/clickhouse
  
  minio:
    image: minio/minio:latest
    command: server /data --console-address ":9001"
    volumes:
      - minio-data:/data
    ports:
      - "9000:9000"
      - "9001:9001"

volumes:
  clickhouse-data:
  minio-data:
```

```bash
docker-compose up -d
# Access Langfuse at http://localhost:3001
```

### Audit Log

```yaml
observability:
  audit_log: ~/.hermes/audit.jsonl
  audit_events:
    - skill_create
    - skill_delete
    - config_change
    - approval_request
    - approval_grant
    - approval_deny
    - tool_execution
    - gateway_message
```

```bash
# Tail audit log
tail -f ~/.hermes/audit.jsonl | jq .

# Search for approvals
jq 'select(.event == "approval_grant")' ~/.hermes/audit.jsonl
```

## Web Dashboard (Part 12)

```yaml
gateways:
  dashboard:
    enabled: true
    port: 8080
    auth_password: ${DASHBOARD_PASSWORD}
    auth_enabled: true
    features:
      - chat  # Chat interface (backed by real hermes --tui)
      - sessions  # Session list + analytics
      - models  # Model config + routing
      - kanban  # Kanban board view
      - plugins  # Installed plugins
      - profiles  # User profiles
      - analytics  # Cost/usage charts
    theme: dark  # dark | light | auto
```

```bash
# Start dashboard
hermes dashboard

# Or via systemd
sudo systemctl start hermes-dashboard

# Access at http://localhost:8080
# Or behind Caddy: https://hermes.example.com
```

**Dashboard features (v0.13)**:
- Chat: Full Hermes TUI in browser
- Sessions: View all sessions, filter by user/platform/date
- Models: Configure providers, routing, test models
- Kanban: Drag-and-drop task board
- Plugins: Install/enable/disable plugins
- Profiles: User preferences, API keys, allowlists
- Analytics: Cost per user/model/day, latency histograms

## Curator (Part 22)

Autonomous skill lifecycle management.

```yaml
curator:
  enabled: true
  schedule: "0 3 * * 0"  # Sunday 3 AM
  review_fork: true  # Spawn isolated agent for reviews
  rubric:
    functionality: 0.3  # Does it work?
    code_quality: 0.2  #
