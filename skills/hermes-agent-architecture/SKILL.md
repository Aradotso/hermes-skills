---
name: hermes-agent-architecture
description: Expert in Hermes Agent architecture, setup, tool development, memory systems, and advanced LLM agent workflows
triggers:
  - "how do I set up hermes agent"
  - "create a custom tool for hermes"
  - "configure hermes memory system"
  - "implement a hermes toolset"
  - "hermes agent prompt architecture"
  - "add a new provider to hermes"
  - "hermes multi-agent setup"
  - "debug hermes tool execution"
---

# Hermes Agent Architecture

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

Expert knowledge for working with Nous Research's Hermes Agent - an advanced LLM agent framework with sophisticated memory, tool execution, multi-agent collaboration, and 18+ platform integrations.

## What is Hermes Agent?

Hermes Agent is a production-grade LLM agent framework featuring:

- **Tool System**: 14+ built-in toolsets (browser automation, code execution, web search, file operations)
- **Memory Architecture**: Three-tier system with FTS5 search and LLM-powered summarization
- **Multi-Agent**: 4 runtime mechanisms (delegate_task, MoA, Background Review, send_message)
- **Platform Gateway**: 18+ integrations (Discord, Slack, WeChat, QQ, IRC, Telegram, etc.)
- **Skills System**: Progressive disclosure with conditional activation
- **Context Management**: Automatic compression with MD5 dedup and smart collapse
- **Security**: Multi-layer defense with dangerous command approval system

## Installation

```bash
# Clone the repository
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent

# Install dependencies (Python 3.10+)
pip install -r requirements.txt

# Initialize configuration
python -m hermes.cli init

# Run interactive CLI
python -m hermes.cli
```

## Core Configuration

### Basic config.yaml Structure

```yaml
# Primary model configuration
model:
  provider: anthropic  # or openai, bedrock, ollama, etc.
  name: claude-3-7-sonnet-20250219
  temperature: 0.7
  max_tokens: 8192

# API credentials
credentials:
  anthropic:
    api_key: ${ANTHROPIC_API_KEY}
  openai:
    api_key: ${OPENAI_API_KEY}

# Memory system
memory:
  enabled: true
  store_type: sqlite  # Default: ~/.hermes/memory.db
  max_messages: 100
  compression:
    enabled: true
    version: v3  # MD5 dedup + smart collapse + param truncation

# Skills system
skills:
  enabled: true
  discovery_mode: progressive  # or full
  config:
    wiki:
      path: ~/Hermes-Wiki

# Tool configuration
tools:
  browser:
    backend: playwright  # or selenium, pyppeteer
    headless: true
  code_execution:
    sandbox: true
    allowed_imports: [os, sys, json, math, datetime]
  web_search:
    provider: duckduckgo  # or brave, google

# Security
security:
  dangerous_command_mode: smart  # manual, smart, or off
  command_review:
    model: claude-3-haiku-20240307  # Cheaper model for approval
```

### Profile-Based Configuration

```yaml
# config.yaml - Multiple isolated agents
profiles:
  default:
    model:
      provider: anthropic
      name: claude-3-7-sonnet-20250219
    personality: "Helpful coding assistant"
  
  researcher:
    model:
      provider: openai
      name: gpt-4-turbo
    personality: "Research analyst focused on accuracy"
    tools:
      enabled: [web_search, browser, read_file]
  
  ops:
    model:
      provider: bedrock
      name: anthropic.claude-3-sonnet
    personality: "DevOps specialist"
    security:
      dangerous_command_mode: manual

# Switch profiles
active_profile: researcher
```

## Creating Custom Tools

### Simple Tool Registration

```python
# tools/my_tools.py
from hermes.tools import tool

@tool(
    name="get_weather",
    description="Get current weather for a city",
    parameters={
        "city": {
            "type": "string",
            "description": "City name"
        },
        "units": {
            "type": "string",
            "enum": ["celsius", "fahrenheit"],
            "default": "celsius"
        }
    }
)
async def get_weather(city: str, units: str = "celsius"):
    """Fetch weather data from API"""
    import aiohttp
    
    api_key = os.getenv("WEATHER_API_KEY")
    url = f"https://api.weather.example/current?city={city}&units={units}"
    
    async with aiohttp.ClientSession() as session:
        async with session.get(url, headers={"X-API-Key": api_key}) as resp:
            data = await resp.json()
            return {
                "temperature": data["temp"],
                "conditions": data["weather"],
                "city": city
            }
```

### Creating a Toolset

```python
# toolsets/database_tools.py
from hermes.toolsets import Toolset, tool

class DatabaseToolset(Toolset):
    """Database query and management tools"""
    
    name = "database"
    description = "Tools for database operations"
    
    def __init__(self, config):
        super().__init__(config)
        self.connection_string = config.get("connection_string")
    
    @tool(
        description="Execute a SQL query",
        parameters={
            "query": {"type": "string", "description": "SQL query"},
            "safe_mode": {"type": "boolean", "default": True}
        }
    )
    async def execute_query(self, query: str, safe_mode: bool = True):
        """Execute SQL with optional safety checks"""
        if safe_mode and any(kw in query.upper() for kw in ["DROP", "DELETE", "TRUNCATE"]):
            return {"error": "Dangerous operation blocked in safe mode"}
        
        # Execute query (example with asyncpg)
        import asyncpg
        conn = await asyncpg.connect(self.connection_string)
        try:
            results = await conn.fetch(query)
            return {"rows": [dict(row) for row in results]}
        finally:
            await conn.close()
    
    @tool(description="List all database tables")
    async def list_tables(self):
        query = """
            SELECT table_name 
            FROM information_schema.tables 
            WHERE table_schema = 'public'
        """
        return await self.execute_query(query, safe_mode=False)

# Register in config.yaml
# toolsets:
#   database:
#     enabled: true
#     connection_string: ${DATABASE_URL}
```

### Advanced Tool with State and Parallelization

```python
from hermes.tools import tool, ParallelSafety

@tool(
    name="file_search",
    description="Search for text in files",
    parameters={
        "pattern": {"type": "string"},
        "directory": {"type": "string", "default": "."},
        "file_types": {"type": "array", "items": {"type": "string"}}
    },
    parallel_safety=ParallelSafety.READ_ONLY  # Safe for parallel execution
)
async def file_search(pattern: str, directory: str = ".", file_types: list = None):
    """Search files with ripgrep"""
    import asyncio
    
    cmd = ["rg", "--json", pattern, directory]
    if file_types:
        for ft in file_types:
            cmd.extend(["--type", ft])
    
    proc = await asyncio.create_subprocess_exec(
        *cmd,
        stdout=asyncio.subprocess.PIPE,
        stderr=asyncio.subprocess.PIPE
    )
    
    stdout, stderr = await proc.communicate()
    
    # Parse ripgrep JSON output
    import json
    matches = []
    for line in stdout.decode().splitlines():
        try:
            data = json.loads(line)
            if data["type"] == "match":
                matches.append({
                    "file": data["data"]["path"]["text"],
                    "line": data["data"]["line_number"],
                    "text": data["data"]["lines"]["text"]
                })
        except:
            continue
    
    return {"matches": matches, "count": len(matches)}
```

## Memory System Usage

### Programmatic Memory Access

```python
from hermes.memory import MemoryManager, MemoryStore

# Initialize memory
store = MemoryStore("~/.hermes/memory.db")
manager = MemoryManager(store, config={"max_messages": 100})

# Add messages
await manager.add_message("user", "What's the capital of France?")
await manager.add_message("assistant", "The capital of France is Paris.")

# Search memory
results = await manager.search("capital France")
# Returns: [{"role": "user", "content": "What's...", "timestamp": ...}, ...]

# Get recent context
context = await manager.get_recent_context(limit=10)

# Create frozen snapshot for prompt caching
snapshot = manager.create_snapshot()
# Snapshot is immutable - preserves Anthropic prompt cache
```

### Session Management

```python
from hermes.session import SessionDB

session_db = SessionDB("~/.hermes/sessions.db")

# Create session
session_id = await session_db.create_session(
    platform="cli",
    user_id="developer@example.com",
    metadata={"project": "acme-app"}
)

# Search across sessions
results = await session_db.search_sessions(
    query="deployment error",
    user_id="developer@example.com",
    limit=5
)

# Get session summary (LLM-generated)
summary = await session_db.get_session_summary(session_id)
```

## Multi-Agent Patterns

### Delegate Task Pattern

```python
# Agent delegates to specialist sub-agent
from hermes.agent import Agent

async def main():
    orchestrator = Agent(profile="default")
    
    # Orchestrator receives: "Analyze this codebase and write a report"
    response = await orchestrator.process_message(
        "Analyze the /src directory and create a security audit report"
    )
    
    # Internally uses delegate_task tool:
    # delegate_task(
    #   agent_profile="security_specialist",
    #   task="Perform security audit on /src",
    #   context={...}
    # )
```

### Mixture of Agents (MoA)

```python
# Multiple agents vote on best response
moa_config = {
    "agents": [
        {"profile": "creative", "weight": 1.0},
        {"profile": "analytical", "weight": 1.0},
        {"profile": "conservative", "weight": 0.5}
    ],
    "aggregation": "llm_synthesis"  # or "voting"
}

# Framework automatically aggregates responses
```

### Background Review

```python
# Asynchronous review by another agent
@tool(name="request_review")
async def request_review(content: str, reviewer_profile: str):
    """Send content for async review"""
    review_agent = Agent(profile=reviewer_profile)
    
    # Runs in background, stores result in memory
    review_task = asyncio.create_task(
        review_agent.process_message(f"Review this: {content}")
    )
    
    return {"status": "review_requested", "task_id": id(review_task)}
```

## Platform Gateway Integration

### Discord Bot Example

```python
# platforms/discord_bot.py
from hermes.gateway import PlatformAdapter, GatewayRegistry
import discord

class DiscordAdapter(PlatformAdapter):
    platform_name = "discord"
    
    def __init__(self, config):
        super().__init__(config)
        self.token = config["token"]
        self.client = discord.Client(intents=discord.Intents.default())
        
        @self.client.event
        async def on_message(message):
            if message.author.bot:
                return
            
            # Route to Hermes agent
            response = await self.handle_message(
                user_id=str(message.author.id),
                channel_id=str(message.channel.id),
                content=message.content,
                metadata={
                    "guild_id": str(message.guild.id) if message.guild else None,
                    "username": message.author.name
                }
            )
            
            await message.channel.send(response)
    
    async def send_message(self, channel_id: str, content: str):
        channel = self.client.get_channel(int(channel_id))
        await channel.send(content)
    
    async def start(self):
        await self.client.start(self.token)

# Register adapter
GatewayRegistry.register(DiscordAdapter)

# config.yaml
# gateway:
#   enabled: true
#   platforms:
#     discord:
#       enabled: true
#       token: ${DISCORD_BOT_TOKEN}
```

### Custom Platform with PII Scrubbing

```python
from hermes.gateway import PlatformAdapter
import re

class CustomPlatformAdapter(PlatformAdapter):
    platform_name = "custom_chat"
    
    async def scrub_pii(self, content: str) -> str:
        """Remove sensitive data before sending to LLM"""
        # Email addresses
        content = re.sub(r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b', 
                        '[EMAIL]', content)
        # Phone numbers
        content = re.sub(r'\b\d{3}[-.]?\d{3}[-.]?\d{4}\b', '[PHONE]', content)
        # SSN
        content = re.sub(r'\b\d{3}-\d{2}-\d{4}\b', '[SSN]', content)
        
        return content
    
    async def handle_message(self, user_id, channel_id, content, metadata=None):
        # Scrub before processing
        scrubbed = await self.scrub_pii(content)
        return await super().handle_message(user_id, channel_id, scrubbed, metadata)
```

## Skills System

### Creating a Skill

```markdown
<!-- skills/git-workflow.md -->
---
name: git-workflow
description: Advanced Git workflow operations
activation_conditions:
  - keywords: [git, branch, merge, rebase]
  - context: repository
requires_keys: [GITHUB_TOKEN]
---

# Git Workflow Skills

## Branch Management

Use `git worktree` for parallel development:
- Main branch: `git worktree add ../feature-main main`
- Feature: `git worktree add ../feature-x feature-x`

## Conflict Resolution

1. `git merge --no-commit feature`
2. Review conflicts
3. Use `git checkout --ours` or `--theirs` for whole-file resolution
```

### Skill Discovery Configuration

```yaml
# config.yaml
skills:
  enabled: true
  discovery_mode: progressive  # Only show relevant skills
  paths:
    - ~/.hermes/skills
    - ./project-specific-skills
  
  config:
    # Skill-specific configuration
    wiki:
      path: ~/Hermes-Wiki
    git_workflow:
      default_remote: origin
      protected_branches: [main, master, production]
```

## Advanced Patterns

### Context References

```python
# User message with references
message = """
Review these files for security issues:
@file src/auth.py
@folder tests/
@diff HEAD~1
@url https://owasp.org/www-project-top-ten/
"""

# Framework automatically injects content into context
# Respects injection limits (default 100KB per reference)
```

### Prompt Caching Optimization

```python
from hermes.prompt_builder import PromptBuilder

builder = PromptBuilder(config)

# Build system prompt (cached)
system_prompt = builder.build_system_prompt(
    personality="coding assistant",
    skills=["python", "debugging"],
    toolsets=["file_operations", "code_execution"]
)

# Create frozen snapshot for cache hit
memory_snapshot = memory_manager.create_snapshot()

# This structure maintains Anthropic's prompt cache
# System prompt + frozen message history = 75% cost savings
```

### Large Tool Result Handling

```python
@tool(
    name="analyze_large_file",
    max_result_size=50000  # 50KB limit
)
async def analyze_large_file(filepath: str):
    """Analyze file with automatic truncation"""
    with open(filepath) as f:
        content = f.read()
    
    analysis = perform_analysis(content)  # May be huge
    
    # Framework automatically:
    # 1. Truncates if > max_result_size
    # 2. Persists full result to disk
    # 3. Returns truncated version with file reference
    
    return {
        "summary": analysis["summary"],
        "issues": analysis["issues"][:10],  # First 10
        "full_report": analysis  # Auto-persisted if too large
    }
```

### Parallel Tool Execution Safety

```python
from hermes.tools import tool, ParallelSafety

# Framework automatically detects conflicts
# These two calls can run in parallel:
await asyncio.gather(
    read_file("/data/file1.txt"),  # READ_ONLY
    read_file("/data/file2.txt")   # READ_ONLY
)

# These will run sequentially (path conflict):
# write_file("/data/file.txt", "v1")  # WRITE
# write_file("/data/file.txt", "v2")  # WRITE (waits)
```

## Troubleshooting

### Memory Issues

```bash
# Check memory database
sqlite3 ~/.hermes/memory.db
SELECT COUNT(*) FROM messages;

# Clear old sessions
python -m hermes.cli memory --clear --before 2026-01-01

# Rebuild FTS index
python -m hermes.cli memory --rebuild-index
```

### Tool Execution Failures

```python
# Enable debug logging
import logging
logging.basicConfig(level=logging.DEBUG)

# Check tool registry
from hermes.tools import ToolRegistry
print(ToolRegistry.list_tools())

# Test tool directly
result = await ToolRegistry.execute("tool_name", **params)
```

### Prompt Cache Misses

```yaml
# Enable cache debugging
model:
  provider: anthropic
  debug_cache: true  # Shows cache hit/miss stats

# Common causes:
# - Memory snapshot not frozen (use create_snapshot())
# - System prompt changed (skills/toolsets modified)
# - Temperature/max_tokens changed
```

### Gateway Session Issues

```bash
# List active sessions
python -m hermes.cli gateway --list-sessions

# Reset stuck session
python -m hermes.cli gateway --reset-session <platform>:<channel_id>

# Check session isolation
# Each platform:channel should have separate memory
```

### Provider Connection Errors

```yaml
# Test provider connectivity
providers:
  anthropic:
    api_key: ${ANTHROPIC_API_KEY}
    timeout: 30
    max_retries: 3
    
  bedrock:
    region: us-east-1
    profile: default  # AWS profile
    
  ollama:
    base_url: http://localhost:11434
    models: [llama2, codellama]

# Fallback chain
model:
  primary: anthropic/claude-3-7-sonnet-20250219
  fallback:
    - openai/gpt-4-turbo
    - ollama/llama2
```

### Code Execution Sandbox

```yaml
# Increase security for untrusted code
tools:
  code_execution:
    sandbox: true
    communication: uds  # Unix domain socket (more secure than file RPC)
    allowed_imports:
      - os
      - sys
      - json
      - math
      - datetime
      - requests
    max_execution_time: 10  # seconds
    memory_limit: 512  # MB
```

## CLI Commands

```bash
# Initialize config
hermes init

# Interactive chat
hermes

# Run with specific profile
hermes --profile researcher

# Dump configuration
hermes dump config

# Export session
hermes export --session <session_id> --format json

# Reload skills without restart
/reload-skills

# Switch session
/session <session_id>

# Clear current session
/clear

# Voice mode
/voice
```

## Environment Variables

```bash
# Model providers
export ANTHROPIC_API_KEY=sk-ant-...
export OPENAI_API_KEY=sk-...
export GROQ_API_KEY=gsk_...

# AWS Bedrock
export AWS_PROFILE=default
export AWS_REGION=us-east-1

# Tool integrations
export GITHUB_TOKEN=ghp_...
export WEATHER_API_KEY=...
export BRAVE_SEARCH_API_KEY=...

# Gateway platforms
export DISCORD_BOT_TOKEN=...
export SLACK_BOT_TOKEN=xoxb-...
export TELEGRAM_BOT_TOKEN=...

# Database
export DATABASE_URL=postgresql://user:pass@localhost/db
```
