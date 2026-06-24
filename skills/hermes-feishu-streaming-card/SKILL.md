---
name: hermes-feishu-streaming-card
description: Stream Hermes Gateway agent responses to Feishu/Lark as live-updating interactive cards with thinking process, tool calls, and in-card interactions
triggers:
  - install hermes feishu streaming card plugin
  - configure feishu streaming cards for hermes
  - set up lark cards for hermes gateway
  - stream hermes responses to feishu cards
  - add interactive feishu cards to hermes agent
  - troubleshoot hermes feishu card not updating
  - debug hermes feishu streaming card sidecar
  - configure feishu card interactions for hermes
---

# Hermes Feishu Streaming Card

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

The Hermes Feishu Streaming Card plugin transforms Hermes Agent Gateway's Feishu/Lark responses into continuously-updating interactive cards. Instead of fragmented gray text messages, you get a single card showing thinking process, tool calls, final answers, authorization requests, option selections, and execution stats — all in one place.

## What It Does

This plugin solves key pain points when connecting Hermes to Feishu:

- **Unified streaming experience**: `thinking.delta`, `answer.delta`, `tool.updated`, and `message.completed` all update the same Feishu card
- **In-card interactions**: Hermes approval/clarify choices render as Feishu buttons (callback mode) or numbered options (localhost/private sidecar)
- **Long content stability**: Markdown tables and code blocks split at structure boundaries to avoid raw markdown rendering
- **Multi-bot/profile support**: Multiple Feishu bots, multiple Hermes profiles, group chat bindings, routing diagnostics
- **Sidecar-only architecture**: Independent process handles Feishu API calls, state machine, retries, health checks
- **Fail-open design**: Hermes hook failures don't break your gateway; original text messages serve as fallback

## Installation

### Prerequisites

- Python 3.9+
- Hermes Gateway v0.13.0+ (or date-based version `v2026.4.23+`)
- Active Feishu/Lark bot with app credentials

### Quick Install

```bash
# Install plugin
pip install hermes-feishu-streaming-card

# Verify installation and Hermes compatibility
hermes-feishu-card doctor --hermes-dir ~/.hermes/hermes-agent

# Install hooks into Hermes
hermes-feishu-card install --hermes-dir ~/.hermes/hermes-agent --yes

# Restart Hermes Gateway
# (kill existing process and start again)
```

### From Release Package

```bash
# Download latest release
wget https://github.com/baileyh8/hermes-feishu-streaming-card/releases/latest/download/hermes-feishu-streaming-card.tar.gz
tar -xzf hermes-feishu-streaming-card.tar.gz
cd hermes-feishu-streaming-card

# Install
pip install .
hermes-feishu-card install --hermes-dir ~/.hermes/hermes-agent --yes
```

### Installation State Management

The installer creates manifest and backup files in `~/.hermes-feishu-card/`:

```bash
# Check installation state
hermes-feishu-card doctor --explain

# Repair corrupted state (safe, validates before overwriting)
hermes-feishu-card repair --hermes-dir ~/.hermes/hermes-agent --yes

# Restore original Hermes files
hermes-feishu-card restore --hermes-dir ~/.hermes/hermes-agent --yes

# Complete uninstall
hermes-feishu-card uninstall --hermes-dir ~/.hermes/hermes-agent --yes
```

## Configuration

### Basic Setup

Create or update `~/.hermes-feishu-card/config.yaml`:

```yaml
# Feishu bot credentials
feishu:
  - app_id: cli_a1b2c3d4e5f6g7h8
    app_secret: ${FEISHU_APP_SECRET_1}  # Use environment variable
    verification_token: v1a2b3c4d5e6f7g8h9i0
    encrypt_key: ${FEISHU_ENCRYPT_KEY_1}
    bot_name: "Production Bot"
    
# Sidecar server
sidecar:
  host: 127.0.0.1
  port: 8765
  log_level: INFO  # DEBUG for troubleshooting

# Card appearance and behavior
card:
  title: "🤖 Hermes Agent"
  thinking_emoji: "💭"
  answer_emoji: "💬"
  tool_emoji: "🔧"
  show_footer_stats: true
  thinking_mode: append_block  # or replace
  interaction_mode: auto  # auto, callback, or text
  
# Content handling
content:
  main_content_chunk_chars: 3500
  code_fence_preserve: true
  table_header_repeat: true
  feishu_table_row_limit: 50
```

### Multi-Bot Configuration

```yaml
feishu:
  - app_id: cli_prod_bot
    app_secret: ${FEISHU_PROD_SECRET}
    verification_token: ${FEISHU_PROD_TOKEN}
    encrypt_key: ${FEISHU_PROD_ENCRYPT}
    bot_name: "Production Bot"
    
  - app_id: cli_test_bot
    app_secret: ${FEISHU_TEST_SECRET}
    verification_token: ${FEISHU_TEST_TOKEN}
    encrypt_key: ${FEISHU_TEST_ENCRYPT}
    bot_name: "Test Bot"

# Bind specific group chats to profiles
bindings:
  chats:
    oc_a1b2c3d4e5f6g7h8:
      profile_id: thinking
      bot_name: "Production Bot"
    oc_x9y8z7w6v5u4t3s2:
      profile_id: research
      bot_name: "Test Bot"
```

### Interaction Modes

```yaml
card:
  # auto: localhost/private sidecar uses text mode, public uses callback
  interaction_mode: auto
  
  # callback: render real Feishu buttons (requires public callback URL)
  # interaction_mode: callback
  # callback_base_url: https://your-domain.com
  
  # text: always use numbered options in card, return to Hermes text flow
  # interaction_mode: text
```

## Key Commands

### Sidecar Management

```bash
# Start sidecar (runs in background)
hermes-feishu-card start

# Check sidecar status
hermes-feishu-card status

# Stop sidecar
hermes-feishu-card stop

# View logs
hermes-feishu-card logs

# Follow logs in real-time
hermes-feishu-card logs --tail 50 --follow
```

### Diagnostics

```bash
# Human-readable diagnostic summary
hermes-feishu-card doctor --explain --hermes-dir ~/.hermes/hermes-agent

# Machine-readable JSON output
hermes-feishu-card doctor --json --hermes-dir ~/.hermes/hermes-agent

# Check runtime import (does Hermes venv have the plugin?)
hermes-feishu-card doctor --explain  # Look for runtime_import section
```

### Bot Testing

```bash
# Test single bot (default config)
hermes-feishu-card bots test

# Test specific bot by app_id
hermes-feishu-card bots test --app-id cli_a1b2c3d4e5f6g7h8

# Test all configured bots
hermes-feishu-card bots test --all

# Test with specific profile
hermes-feishu-card bots test --profile-id thinking
```

### Smoke Testing

```bash
# Send test card to Feishu chat
hermes-feishu-card smoke-feishu-card \
  --chat-id oc_a1b2c3d4e5f6g7h8 \
  --bot-name "Production Bot" \
  --profile-id thinking

# Test with custom message
hermes-feishu-card smoke-feishu-card \
  --chat-id oc_a1b2c3d4e5f6g7h8 \
  --test-message "Complex query with\n**markdown** and `code`"
```

## Working with the Sidecar

### Health Check Endpoints

The sidecar exposes health check endpoints:

```bash
# Basic health
curl http://localhost:8765/health

# Detailed routing info
curl http://localhost:8765/health.routing

# Profile-specific routing
curl http://localhost:8765/health.routing.profiles
```

Example response:

```json
{
  "status": "healthy",
  "sidecar_version": "3.6.5",
  "uptime_seconds": 3600,
  "bots_configured": 2,
  "chats_bound": 5,
  "profiles": {
    "thinking": {
      "bot_name": "Production Bot",
      "bound_chats": 3,
      "last_route": "2026-06-24T10:30:00Z",
      "last_route_error": null
    }
  }
}
```

### Event Flow

The sidecar receives events from Hermes hooks and manages card lifecycle:

```python
# Hook emits events to sidecar
import requests

def emit_event(event: dict):
    """Send event from Hermes hook to sidecar"""
    try:
        response = requests.post(
            "http://localhost:8765/events",
            json=event,
            timeout=1.0
        )
        return response.status_code == 200
    except Exception as e:
        # Fail-open: hook errors don't break Hermes
        print(f"[hermes-feishu-card] hook failed: {e}")
        return False
```

Event types:
- `message.started`: Create new card
- `thinking.delta`: Update thinking section
- `answer.delta`: Update answer section
- `tool.updated`: Show tool call progress
- `message.completed`: Finalize card
- `interaction.requested`: Add approval/clarify buttons
- `interaction.selected`: Record user choice

## Real Code Examples

### Custom Card Template

```python
from hermes_feishu_card.card_builder import CardBuilder

def create_custom_card():
    """Build a custom Feishu card"""
    builder = CardBuilder(
        title="Custom Agent",
        thinking_emoji="🧠",
        answer_emoji="✨",
        tool_emoji="⚙️"
    )
    
    # Add thinking section
    builder.update_thinking(
        "Analyzing user request...",
        mode="append_block"
    )
    
    # Add tool call
    builder.update_tool(
        tool_name="web_search",
        status="running",
        input_data={"query": "latest news"}
    )
    
    # Add answer
    builder.update_answer(
        "Based on the search results, here's what I found..."
    )
    
    # Add footer stats
    builder.update_footer(
        elapsed_sec=2.5,
        tokens_used=1234,
        model_name="gpt-4"
    )
    
    return builder.build()
```

### Handling Long Content

```python
from hermes_feishu_card.content_splitter import split_markdown_aware

def send_long_content(content: str, max_chunk: int = 3500):
    """Split long content intelligently"""
    chunks = split_markdown_aware(
        content,
        max_chunk_chars=max_chunk,
        preserve_code_fence=True,
        repeat_table_header=True
    )
    
    for i, chunk in enumerate(chunks):
        print(f"Chunk {i+1}/{len(chunks)}: {len(chunk)} chars")
        # Each chunk is valid markdown with complete structures
```

### Custom Interaction Handler

```python
from hermes_feishu_card.interaction import InteractionManager

async def handle_custom_approval(interaction_id: str, choices: list):
    """Custom approval flow"""
    manager = InteractionManager(
        sidecar_url="http://localhost:8765"
    )
    
    # Request interaction
    await manager.request_interaction(
        interaction_id=interaction_id,
        interaction_type="approval",
        choices=choices,
        message_id="msg_123",
        user_id="ou_456"
    )
    
    # Poll for user selection
    while True:
        result = await manager.get_interaction(interaction_id)
        if result and result.get("selected_choice") is not None:
            return result["selected_choice"]
        await asyncio.sleep(0.5)
```

### Profile-Specific Bot Routing

```python
from hermes_feishu_card.routing import resolve_bot_for_profile

def get_bot_for_message(profile_id: str, chat_id: str):
    """Determine which bot to use"""
    config = load_config()  # Your config loader
    
    # Check chat binding first
    chat_binding = config.get("bindings", {}).get("chats", {}).get(chat_id)
    if chat_binding and chat_binding.get("profile_id") == profile_id:
        bot_name = chat_binding["bot_name"]
        return next(
            bot for bot in config["feishu"]
            if bot["bot_name"] == bot_name
        )
    
    # Fall back to first bot
    return config["feishu"][0]
```

## Common Patterns

### Streaming Delta Aggregation

```python
# Hermes hook pattern for delta events
class StreamingCardHook:
    def __init__(self):
        self.answer_buffer = {}
        self.thinking_buffer = {}
    
    def on_answer_delta(self, message_id: str, delta: str):
        """Aggregate answer deltas"""
        if message_id not in self.answer_buffer:
            self.answer_buffer[message_id] = ""
        
        # Preserve boundary spaces
        self.answer_buffer[message_id] += delta
        
        # Send to sidecar
        emit_event({
            "type": "answer.delta",
            "message_id": message_id,
            "delta": delta,
            "accumulated": self.answer_buffer[message_id]
        })
```

### Fail-Open Error Handling

```python
def safe_emit_event(event: dict) -> bool:
    """Emit event with fail-open behavior"""
    try:
        response = requests.post(
            "http://localhost:8765/events",
            json=event,
            timeout=1.0
        )
        
        if response.status_code != 200:
            print(f"[hermes-feishu-card] sidecar returned {response.status_code}")
            return False
            
        return True
        
    except requests.exceptions.Timeout:
        print("[hermes-feishu-card] sidecar timeout (continuing)")
        return False
        
    except Exception as e:
        # Don't break Hermes flow
        print(f"[hermes-feishu-card] hook failed: {e}")
        return False
```

### Media/Attachment Handling

```python
def format_attachments(message_locals: dict) -> str:
    """Format media/file attachments for card"""
    attachments = []
    
    # Check various attachment structures
    if "attachments" in message_locals:
        attachments.extend(message_locals["attachments"])
    
    if "media_files" in message_locals:
        attachments.extend(message_locals["media_files"])
    
    if "files" in message_locals:
        attachments.extend(message_locals["files"])
    
    # Format summary
    summary_parts = []
    for att in attachments:
        if isinstance(att, dict):
            name = att.get("name") or att.get("file_name") or "file"
            size = att.get("size", 0)
            summary_parts.append(f"📎 {name} ({size} bytes)")
        else:
            summary_parts.append(f"📎 {att}")
    
    return "\n".join(summary_parts)
```

## Troubleshooting

### Card Not Appearing

```bash
# 1. Check sidecar is running
hermes-feishu-card status

# 2. Check sidecar can reach Feishu
hermes-feishu-card bots test --all

# 3. Verify hook is installed
hermes-feishu-card doctor --explain --hermes-dir ~/.hermes/hermes-agent

# 4. Check Hermes runtime can import plugin
hermes-feishu-card doctor --explain  # Look for runtime_import: success

# 5. Check Hermes logs for hook errors
tail -f ~/.hermes/hermes-agent/logs/gateway.log | grep hermes-feishu-card
```

### Card Stuck in "Thinking"

This often happens after Hermes v0.17.0+ where streaming callbacks moved to `_run_agent_inner`:

```bash
# 1. Check Hermes version
hermes-feishu-card doctor --explain --hermes-dir ~/.hermes/hermes-agent

# 2. Verify hook strategy matches version
# Output should show: hook_strategy: gateway_run_013_plus

# 3. Re-install hooks
hermes-feishu-card install --hermes-dir ~/.hermes/hermes-agent --yes

# 4. Restart Hermes Gateway
```

### Multiple Gray Messages After Card

The plugin should suppress Hermes native message resend, but if you still see duplicates:

```yaml
# In config.yaml, ensure:
card:
  suppress_native_resend: true  # Default is true

content:
  # For DeepSeek and models that return final answer without deltas:
  fallback_to_completed: true  # Default is true
```

### Interaction Buttons Not Working

```yaml
# For localhost/private sidecar:
card:
  interaction_mode: auto  # Will use text mode automatically

# For public sidecar with callback URL:
card:
  interaction_mode: callback
  callback_base_url: https://your-public-domain.com

# Verify callback endpoint is reachable:
curl https://your-public-domain.com/health
```

### Long Tables Showing Raw Markdown

```yaml
# Ensure markdown-aware splitting is enabled:
content:
  table_header_repeat: true
  feishu_table_row_limit: 50  # Feishu limit
  main_content_chunk_chars: 3500
```

### Multiple Bots/Profiles Routing Wrong

```bash
# 1. Check routing diagnostics
curl http://localhost:8765/health.routing.profiles

# 2. Verify chat bindings in config
cat ~/.hermes-feishu-card/config.yaml

# 3. Test specific profile
hermes-feishu-card smoke-feishu-card \
  --chat-id oc_your_chat_id \
  --profile-id thinking \
  --bot-name "Production Bot"

# 4. Check recent routing errors in logs
hermes-feishu-card logs | grep "route_error"
```

### Upgrade Broke Installation

```bash
# 1. Check if state is corrupted
hermes-feishu-card doctor --explain

# 2. Try repair (safe, validates before overwriting)
hermes-feishu-card repair --hermes-dir ~/.hermes/hermes-agent --yes

# 3. If repair fails, restore and reinstall
hermes-feishu-card restore --hermes-dir ~/.hermes/hermes-agent --yes
hermes-feishu-card install --hermes-dir ~/.hermes/hermes-agent --yes

# 4. Restart Hermes
```

### Hermes Version Compatibility

Supported versions:
- Hermes v0.13.0+ (uses `gateway_run_013_plus`)
- Hermes v0.14.x (uses `gateway_run_013_plus`)
- Hermes v0.15.x (uses `gateway_run_013_plus`)
- Hermes v0.17.x (uses `gateway_run_013_plus`, hooks `_run_agent_inner`)
- Date-based v2026.4.23+ (uses `gateway_run_013_plus`)
- Date-based v2026.6.19+ (uses `gateway_run_013_plus`, hooks `_run_agent_inner`)

Older versions (`v2026.4.23` to `v2026.4.x`) use `legacy_gateway_run`.

```bash
# Check compatibility
hermes-feishu-card doctor --explain --hermes-dir ~/.hermes/hermes-agent
# Look for: hook_strategy, compatibility: supported/unsupported
```

### Feishu Thread/Topic Replies

Since v3.6.4, initial cards in Feishu threads use reply API:

```python
# Automatic behavior - no config needed
# If user message is in thread, card replies to same thread
# Subsequent PATCH updates use the same card
```

### Cron Job Cards

Since v3.6.4, cron jobs can deliver cards:

```yaml
# In Hermes cron config:
jobs:
  - id: daily-report
    schedule: "0 9 * * *"
    agent: reporting
    deliver: "feishu:oc_your_chat_id"  # Plugin will parse this
```

## Advanced Configuration

### Custom Sidecar Port

```yaml
sidecar:
  host: 127.0.0.1
  port: 9876  # Custom port
  
# Update hook to match:
# Edit ~/.hermes/hermes-agent/gateway/run.py
# Change SIDECAR_URL = "http://localhost:9876"
```

### Debug Logging

```yaml
sidecar:
  log_level: DEBUG  # Show all events
  log_file: ~/.hermes-feishu-card/sidecar.log
  
# Then tail logs:
tail -f ~/.hermes-feishu-card/sidecar.log
```

### Performance Tuning

```yaml
card:
  # Faster updates (more API calls)
  patch_interval_sec: 0.1
  
  # Or slower, more batched updates
  # patch_interval_sec: 0.5
  
content:
  # Larger chunks (fewer cards, more content per PATCH)
  main_content_chunk_chars: 4000
  
  # Or smaller chunks (more responsive, more PATCH calls)
  # main_content_chunk_chars: 2000
```

### Environment Variables

```bash
# Common environment variables
export FEISHU_APP_SECRET_1="your-app-secret"
export FEISHU_ENCRYPT_KEY_1="your-encrypt-key"
export FEISHU_PROD_SECRET="prod-secret"
export FEISHU_PROD_TOKEN="prod-token"

# Custom config location
export HERMES_FEISHU_CARD_CONFIG=~/custom/config.yaml

# Hermes home (if non-standard)
export HERMES_HOME=~/.hermes/hermes-agent
```

## Resources

- GitHub: https://github.com/baileyh8/hermes-feishu-streaming-card
- Issues: https://github.com/baileyh8/hermes-feishu-streaming-card/issues
- Hermes Gateway: https://github.com/hermesagent/hermes-gateway
- Feishu Open Platform: https://open.feishu.cn/
