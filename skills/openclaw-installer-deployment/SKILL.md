---
name: openclaw-installer-deployment
description: Deploy and configure OpenClaw AI assistant with multi-platform support, AI models, and messaging channels
triggers:
  - install openclaw ai assistant
  - deploy openclaw with telegram bot
  - configure openclaw discord integration
  - set up openclaw with claude or gpt
  - manage openclaw gateway service
  - add messaging channels to openclaw
  - troubleshoot openclaw deployment
  - configure openclaw feishu whatsapp
---

# OpenClaw Installer & Deployment

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

OpenClaw is a private AI assistant with persistent memory, multi-platform messaging support, and proactive capabilities. This installer provides one-click deployment for macOS and Linux with support for Claude, GPT, Gemini, and other AI models.

## What OpenClaw Does

- **Persistent Memory**: Cross-conversation, cross-platform long-term memory
- **Multi-Channel**: Telegram, Discord, WhatsApp, Slack, WeChat, iMessage, Feishu
- **Proactive Push**: Scheduled reminders, morning reports, alerts
- **Skill System**: Custom capabilities via Markdown files
- **Remote Control**: Execute system commands, file operations, web browsing

## Installation

### One-Line Install (Recommended)

```bash
curl -fsSL https://raw.githubusercontent.com/miaoxworld/OpenClawInstaller/main/install.sh | bash
```

The installer automatically:
1. Detects system and installs dependencies (Node.js v22+)
2. Installs OpenClaw globally via npm
3. Guides through core configuration (AI model, identity)
4. Tests API connections
5. Starts OpenClaw service
6. Optionally opens configuration menu for channels

### Manual Installation

```bash
# Clone repository
git clone https://github.com/miaoxworld/OpenClawInstaller.git
cd OpenClawInstaller

# Add execution permissions
chmod +x install.sh config-menu.sh

# Run installer
./install.sh

# Or install OpenClaw directly
npm install -g openclaw
```

### Desktop GUI Alternative

For graphical interface, use OpenClaw Manager (Tauri + React + Rust):
- Download: https://github.com/miaoxworld/openclaw-manager
- Features: Real-time monitoring, visual config, cross-platform

## Service Management

### Gateway Commands

```bash
# Start service (background daemon)
openclaw gateway start

# Stop service
openclaw gateway stop

# Restart service
openclaw gateway restart

# Check status
openclaw gateway status

# Run in foreground (debugging)
source ~/.openclaw/env && openclaw gateway

# View logs
openclaw logs

# Real-time logs
openclaw logs --follow
```

### Configuration Management

```bash
# Open configuration file
openclaw config

# Run onboarding wizard
openclaw onboard

# Diagnose issues
openclaw doctor

# Health check
openclaw health

# Run interactive config menu
bash ~/.openclaw/config-menu.sh

# Or download and run
curl -fsSL https://raw.githubusercontent.com/miaoxworld/OpenClawInstaller/main/config-menu.sh | bash
```

## AI Model Configuration

### Anthropic Claude (with Custom API Support)

```bash
# Environment variables in ~/.openclaw/env
export ANTHROPIC_API_KEY=sk-ant-xxxxx
export ANTHROPIC_BASE_URL=https://your-api-proxy.com  # Optional

# Set model via CLI
openclaw models set claude-sonnet-4-5-20250929

# Or use config menu: [2] AI 模型配置 → [1] Anthropic Claude
```

Custom provider in `~/.openclaw/openclaw.json`:

```json
{
  "models": {
    "providers": {
      "anthropic-custom": {
        "baseUrl": "https://your-api-proxy.com",
        "apiKey": "${ANTHROPIC_API_KEY}",
        "models": [
          {
            "id": "claude-sonnet-4-5-20250929",
            "name": "claude-sonnet-4-5-20250929",
            "api": "anthropic-messages",
            "input": ["text"],
            "contextWindow": 200000,
            "maxTokens": 8192
          }
        ]
      }
    }
  }
}
```

### OpenAI GPT (Requires v1/responses Support)

```bash
# Environment variables
export OPENAI_API_KEY=sk-xxxxx
export OPENAI_BASE_URL=https://your-api-proxy.com/v1  # Must support v1/responses

# Set model
openclaw models set gpt-4o
```

**Important**: Custom OpenAI endpoints MUST support the Responses API (`v1/responses`), not just Chat Completions (`v1/chat/completions`). Verify proxy compatibility before use.

### Other Supported Models

```bash
# Google Gemini
export GOOGLE_API_KEY=AIzaSyXXXXX
openclaw models set gemini-2.0-flash

# OpenRouter (multi-model gateway)
export OPENROUTER_API_KEY=sk-or-xxxxx
openclaw models set openrouter/anthropic/claude-sonnet-4

# Groq (fast inference)
export GROQ_API_KEY=gsk_xxxxx
openclaw models set groq/llama-3.3-70b-versatile

# Mistral AI
export MISTRAL_API_KEY=xxxxx
openclaw models set mistral-large-latest

# Ollama (local)
export OLLAMA_BASE_URL=http://localhost:11434
openclaw models set ollama/llama3
```

## Messaging Channel Configuration

### Telegram Bot

```bash
# 1. Create bot via @BotFather
# 2. Get User ID from @userinfobot
# 3. Configure

openclaw config set telegram.token YOUR_BOT_TOKEN
openclaw config set telegram.userId YOUR_USER_ID

# Restart gateway
openclaw gateway restart
```

### Discord Bot

```bash
# 1. Create application at https://discord.com/developers/applications
# 2. Enable "Message Content Intent" in Bot settings
# 3. Invite bot with permissions: View Channels, Send Messages, Read Message History
# 4. Get Channel ID (right-click channel with Developer Mode enabled)

openclaw config set discord.token YOUR_BOT_TOKEN
openclaw config set discord.channelId YOUR_CHANNEL_ID

openclaw gateway restart
```

### Feishu (Lark) Bot

```bash
# 1. Create app at https://open.feishu.cn/
# 2. Add "Bot" capability
# 3. Grant permissions: im:message, im:message:send_as_bot, im:chat:readonly
# 4. Publish app version
# 5. Enable long connection in "Event Subscription" (add im.message.receive_v1)

openclaw config set feishu.appId YOUR_APP_ID
openclaw config set feishu.appSecret YOUR_APP_SECRET

# Start gateway BEFORE saving long connection settings
openclaw gateway start

# No webhook URL needed - uses WebSocket long connection
```

Environment variables in `~/.openclaw/env`:

```bash
export FEISHU_APP_ID=cli_xxxxx
export FEISHU_APP_SECRET=xxxxx
```

### WhatsApp (QR Code Login)

```bash
# 1. Enable in config menu: [3] 消息渠道配置 → [3] WhatsApp
# 2. Scan QR code shown in terminal
# 3. Restart gateway

openclaw gateway restart

# Test by messaging yourself - bot will reply
```

**Note**: WhatsApp Web can only be active on one device. Previous sessions will be logged out.

### Configuration File Structure

`~/.openclaw/openclaw.json` example:

```json
{
  "identity": {
    "name": "Alex",
    "role": "developer",
    "timezone": "Asia/Shanghai"
  },
  "models": {
    "default": "claude-sonnet-4-5-20250929",
    "providers": {
      "anthropic": {
        "apiKey": "${ANTHROPIC_API_KEY}",
        "baseUrl": "${ANTHROPIC_BASE_URL}"
      }
    }
  },
  "channels": {
    "telegram": {
      "enabled": true,
      "token": "${TELEGRAM_BOT_TOKEN}",
      "userId": "${TELEGRAM_USER_ID}"
    },
    "discord": {
      "enabled": true,
      "token": "${DISCORD_BOT_TOKEN}",
      "channelId": "${DISCORD_CHANNEL_ID}"
    },
    "feishu": {
      "enabled": true,
      "appId": "${FEISHU_APP_ID}",
      "appSecret": "${FEISHU_APP_SECRET}"
    },
    "whatsapp": {
      "enabled": true
    }
  }
}
```

## Common Patterns

### Full Deployment Script

```bash
#!/bin/bash
# Deploy OpenClaw with Claude and Telegram

# 1. Install OpenClaw
curl -fsSL https://raw.githubusercontent.com/miaoxworld/OpenClawInstaller/main/install.sh | bash

# 2. Configure environment
cat > ~/.openclaw/env << EOF
export ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
export TELEGRAM_BOT_TOKEN=${TELEGRAM_BOT_TOKEN}
export TELEGRAM_USER_ID=${TELEGRAM_USER_ID}
EOF

# 3. Set model
source ~/.openclaw/env
openclaw models set claude-sonnet-4-5-20250929

# 4. Configure Telegram
openclaw config set telegram.token ${TELEGRAM_BOT_TOKEN}
openclaw config set telegram.userId ${TELEGRAM_USER_ID}

# 5. Start service
openclaw gateway start

# 6. Verify
openclaw gateway status
openclaw health
```

### Multi-Channel Setup

```bash
# Enable multiple channels simultaneously
openclaw config set telegram.enabled true
openclaw config set discord.enabled true
openclaw config set feishu.enabled true
openclaw config set whatsapp.enabled true

# Restart to apply
openclaw gateway restart

# Monitor logs for all channels
openclaw logs --follow
```

### Switching AI Models

```bash
# List available models
openclaw models list

# Switch to GPT
export OPENAI_API_KEY=sk-xxxxx
export OPENAI_BASE_URL=https://your-proxy.com/v1
openclaw models set gpt-4o

# Switch to Gemini
export GOOGLE_API_KEY=AIzaSyXXXX
openclaw models set gemini-2.0-flash

# Switch back to Claude
openclaw models set claude-sonnet-4-5-20250929

# Restart required
openclaw gateway restart
```

### Data Management

```bash
# Export conversation history
openclaw export --format json > conversations.json

# Clear memory
openclaw memory clear

# Backup configuration
openclaw backup

# Restore from backup
openclaw restore ~/.openclaw/backups/backup-2026-05-17.tar.gz
```

## Troubleshooting

### Service Won't Start

```bash
# Check diagnostics
openclaw doctor

# View error logs
openclaw logs | tail -n 50

# Check port conflicts
lsof -i :3000  # Default gateway port

# Verify environment variables
source ~/.openclaw/env
echo $ANTHROPIC_API_KEY
echo $TELEGRAM_BOT_TOKEN

# Reinstall if needed
npm uninstall -g openclaw
npm install -g openclaw
```

### API Connection Issues

```bash
# Test API connectivity
curl -H "x-api-key: ${ANTHROPIC_API_KEY}" \
     -H "anthropic-version: 2023-06-01" \
     https://api.anthropic.com/v1/messages

# For custom base URL
curl -H "x-api-key: ${ANTHROPIC_API_KEY}" \
     -H "anthropic-version: 2023-06-01" \
     ${ANTHROPIC_BASE_URL}/v1/messages

# Check health endpoint
openclaw health
```

### OpenAI v1/responses Error

If using custom OpenAI endpoint and getting errors:

```bash
# Verify proxy supports v1/responses
curl ${OPENAI_BASE_URL}/v1/responses \
  -H "Authorization: Bearer ${OPENAI_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-4o",
    "messages": [{"role": "user", "content": "test"}]
  }'

# If not supported, switch to Claude or Gemini
openclaw models set claude-sonnet-4-5-20250929
```

### Telegram Bot Not Responding

```bash
# Verify bot token
curl https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/getMe

# Check webhook conflicts (should be empty)
curl https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/getWebhookInfo

# Delete webhook if set
curl https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/deleteWebhook

# Restart gateway
openclaw gateway restart

# Test by sending message to bot
```

### Discord Bot Missing Messages

```bash
# Verify Message Content Intent is enabled
# 1. Go to https://discord.com/developers/applications
# 2. Select your app → Bot
# 3. Ensure "Message Content Intent" is ON

# Check bot permissions in channel
# Required: View Channels, Send Messages, Read Message History

# Verify channelId
echo $DISCORD_CHANNEL_ID

# Restart and monitor logs
openclaw gateway restart
openclaw logs --follow | grep discord
```

### Feishu Long Connection Failed

```bash
# Ensure gateway is running BEFORE configuring event subscription
openclaw gateway status

# If stopped, start it
openclaw gateway start

# Then configure long connection in Feishu console
# Event Subscription → Use long connection → Add im.message.receive_v1

# Check logs for connection status
openclaw logs | grep feishu
```

### WhatsApp QR Code Won't Scan

```bash
# Ensure terminal window is large enough to display QR
# Use iTerm2/Windows Terminal with good Unicode support

# If QR corrupted, restart gateway
openclaw gateway restart

# Check session exists
ls ~/.openclaw/whatsapp-session/

# Clear session and re-authenticate
rm -rf ~/.openclaw/whatsapp-session/
openclaw gateway restart
```

### Configuration Menu Issues

```bash
# Ensure scripts are executable
chmod +x ~/.openclaw/config-menu.sh

# Run with bash explicitly
bash ~/.openclaw/config-menu.sh

# Download latest version
curl -fsSL https://raw.githubusercontent.com/miaoxworld/OpenClawInstaller/main/config-menu.sh -o ~/.openclaw/config-menu.sh
chmod +x ~/.openclaw/config-menu.sh
bash ~/.openclaw/config-menu.sh
```

## Directory Structure

```
~/.openclaw/
├── openclaw.json        # Core configuration
├── env                  # Environment variables (API keys)
├── config-menu.sh       # Interactive configuration script
├── backups/             # Configuration backups
├── logs/                # Service logs
└── whatsapp-session/    # WhatsApp session data
```

## System Requirements

- **OS**: macOS 12+ / Ubuntu 20.04+ / Debian 11+ / CentOS 8+
- **Node.js**: v22 or higher
- **RAM**: Minimum 2GB, recommended 4GB+
- **Disk**: Minimum 1GB free space
