---
name: openclaw-installer-deployment
description: Deploy and configure OpenClaw AI assistant with multi-model support and messaging channels
triggers:
  - how do I install openclaw
  - set up openclaw with claude
  - configure telegram bot for openclaw
  - deploy openclaw ai assistant
  - openclaw discord bot setup
  - manage openclaw gateway service
  - configure feishu with openclaw
  - troubleshoot openclaw installation
---

# OpenClaw Installer & Deployment

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

OpenClaw is a personal AI assistant framework with persistent memory, multi-platform messaging support, and extensible skills system. OpenClawInstaller provides one-click deployment scripts for macOS and Linux with interactive configuration menus for AI models (Claude, GPT, Gemini, etc.) and messaging channels (Telegram, Discord, WhatsApp, Feishu, WeChat, iMessage).

**Key capabilities:**
- Multi-model AI support (Anthropic, OpenAI, Google, Groq, Ollama, etc.)
- Custom API proxy/gateway support (OneAPI, NewAPI)
- Multi-channel messaging (Telegram, Discord, WhatsApp, Feishu, Slack, WeChat, iMessage)
- Persistent memory across conversations
- Background daemon service management
- Interactive configuration wizard

## Installation

### One-Line Install (Recommended)

```bash
curl -fsSL https://raw.githubusercontent.com/miaoxworld/OpenClawInstaller/main/install.sh | bash
```

This automatically:
1. Detects system and installs Node.js v22+ if needed
2. Installs OpenClaw via npm
3. Runs interactive configuration wizard for AI model and identity
4. Tests API connection
5. Offers to start the gateway service
6. Optionally opens configuration menu for channels

### Manual Install

```bash
git clone https://github.com/miaoxworld/OpenClawInstaller.git
cd OpenClawInstaller
chmod +x install.sh config-menu.sh
./install.sh
```

### Manual OpenClaw Package Install

```bash
npm install -g openclaw
```

## Directory Structure

```
~/.openclaw/
├── openclaw.json        # Core configuration (auto-managed)
├── env                  # Environment variables (API keys)
├── config-menu.sh       # Configuration wizard script
├── backups/             # Configuration backups
└── logs/                # Service logs
```

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

# Follow logs in real-time
openclaw logs --follow
```

### Configuration Commands

```bash
# Open configuration file
openclaw config

# Run onboarding wizard
openclaw onboard

# Diagnose configuration issues
openclaw doctor

# Health check
openclaw health

# Run interactive menu (installer script)
bash ~/.openclaw/config-menu.sh
```

## AI Model Configuration

### Environment Variables Setup

Edit `~/.openclaw/env`:

```bash
# Anthropic Claude
export ANTHROPIC_API_KEY=sk-ant-xxxxx
export ANTHROPIC_BASE_URL=https://your-proxy.com  # Optional custom endpoint

# OpenAI GPT
export OPENAI_API_KEY=sk-xxxxx
export OPENAI_BASE_URL=https://your-proxy.com/v1  # Optional, must support v1/responses

# Google Gemini
export GOOGLE_API_KEY=your-key

# Groq
export GROQ_API_KEY=your-key

# OpenRouter
export OPENROUTER_API_KEY=your-key
```

### Custom Provider Configuration

For custom API proxies (OneAPI, NewAPI), the installer creates provider configs in `~/.openclaw/openclaw.json`:

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
            "name": "claude-sonnet-4-5",
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

### Setting Active Model

```bash
# Via CLI
openclaw models set anthropic-custom/claude-sonnet-4-5-20250929

# Or use config menu
bash ~/.openclaw/config-menu.sh
# Select [2] AI Model Configuration
```

### Supported Models

- **Anthropic Claude**: claude-sonnet-4-5, claude-opus-4-5, claude-haiku-4-5
- **OpenAI GPT**: gpt-4o, gpt-4o-mini, gpt-4-turbo (requires v1/responses support)
- **Google Gemini**: gemini-2.0-flash, gemini-1.5-pro, gemini-1.5-flash
- **OpenRouter**: Multi-model gateway with single API key
- **Groq**: llama-3.3-70b-versatile, llama-3.1-8b-instant
- **Mistral AI**: mistral-large-latest, mistral-small-latest
- **Ollama**: Local models (llama3, mistral, etc.)

## Messaging Channel Configuration

### Telegram Bot

1. Create bot via `@BotFather` in Telegram:
   ```
   /newbot
   ```
2. Get your User ID from `@userinfobot`
3. Configure in `~/.openclaw/openclaw.json`:

```json
{
  "channels": {
    "telegram": {
      "enabled": true,
      "token": "${TELEGRAM_BOT_TOKEN}",
      "allowedUserIds": ["123456789"]
    }
  }
}
```

Set environment variable in `~/.openclaw/env`:
```bash
export TELEGRAM_BOT_TOKEN=your-bot-token
```

### Discord Bot

1. Create application at [Discord Developer Portal](https://discord.com/developers/applications)
2. Create bot, copy token, **enable Message Content Intent**
3. Invite bot with OAuth2 URL Generator (scopes: `bot`, permissions: View Channels, Send Messages, Read Message History)
4. Get Channel ID (enable Developer Mode in Discord settings, right-click channel)

```json
{
  "channels": {
    "discord": {
      "enabled": true,
      "token": "${DISCORD_BOT_TOKEN}",
      "channelIds": ["1234567890123456789"]
    }
  }
}
```

```bash
export DISCORD_BOT_TOKEN=your-bot-token
```

### Feishu (Lark) Bot

**No public server required** — uses WebSocket long-connection mode.

1. Create app at [Feishu Open Platform](https://open.feishu.cn/)
2. Add "Bot" capability
3. Add permissions: `im:message`, `im:message:send_as_bot`, `im:chat:readonly`
4. Publish app
5. Configure event subscription with **long-connection mode** (not webhook):
   - Add event: `im.message.receive_v1`
   - No webhook URL needed
6. Add bot to group chat

```json
{
  "channels": {
    "feishu": {
      "enabled": true,
      "appId": "${FEISHU_APP_ID}",
      "appSecret": "${FEISHU_APP_SECRET}"
    }
  }
}
```

```bash
export FEISHU_APP_ID=cli_xxxxx
export FEISHU_APP_SECRET=your-secret
```

### WhatsApp

**No Business API required** — uses QR code login.

1. Enable in configuration menu or edit config:

```json
{
  "channels": {
    "whatsapp": {
      "enabled": true
    }
  }
}
```

2. Start gateway — QR code will appear in terminal
3. Scan with WhatsApp mobile app
4. Send message to yourself to test

## Common Patterns

### Initial Setup Workflow

```bash
# 1. Install
curl -fsSL https://raw.githubusercontent.com/miaoxworld/OpenClawInstaller/main/install.sh | bash

# 2. Configure AI model (follow prompts)
# Select provider, enter API key, choose model

# 3. Start service
openclaw gateway start

# 4. Configure channels (optional)
bash ~/.openclaw/config-menu.sh
# Select [3] Message Channel Configuration

# 5. Verify
openclaw gateway status
openclaw logs --follow
```

### Updating Configuration

```bash
# Stop service
openclaw gateway stop

# Edit environment variables
nano ~/.openclaw/env

# Or use interactive menu
bash ~/.openclaw/config-menu.sh

# Restart
openclaw gateway start
```

### Switching AI Models

```bash
# Quick switch via menu
bash ~/.openclaw/config-menu.sh
# [2] AI Model Configuration → [1] Select Provider

# Or manual
openclaw models set anthropic/claude-sonnet-4-5-20250929

# Restart gateway
openclaw gateway restart
```

### Adding Multiple Channels

```bash
# Run config menu
bash ~/.openclaw/config-menu.sh

# Configure each channel:
# [3] Message Channel Configuration
# [1] Telegram
# [2] Discord
# [3] WhatsApp
# [4] Feishu
# etc.

# Restart to apply
openclaw gateway restart
```

### Testing API Connection

```bash
# Via config menu
bash ~/.openclaw/config-menu.sh
# [4] Quick Test → [1] Test API Connection

# Or manual health check
openclaw health
openclaw doctor
```

## Troubleshooting

### Service Won't Start

```bash
# Check status and logs
openclaw gateway status
openclaw logs

# Common issues:
# 1. Port conflict (default 3000)
ps aux | grep openclaw
kill <pid>

# 2. Missing API key
cat ~/.openclaw/env
# Ensure keys are exported

# 3. Invalid configuration
openclaw doctor
openclaw config
```

### API Connection Fails

```bash
# Test connection manually
source ~/.openclaw/env
curl -H "x-api-key: $ANTHROPIC_API_KEY" \
     "${ANTHROPIC_BASE_URL:-https://api.anthropic.com}/v1/messages"

# Check environment variables
echo $ANTHROPIC_API_KEY
echo $ANTHROPIC_BASE_URL

# Verify custom endpoint supports required API
# For OpenAI: must support v1/responses, not just v1/chat/completions
```

### Bot Not Responding (Telegram/Discord)

```bash
# 1. Check service is running
openclaw gateway status

# 2. Verify bot token in logs
openclaw logs | grep -i telegram
openclaw logs | grep -i discord

# 3. Check configuration
cat ~/.openclaw/openclaw.json | grep -A 5 telegram

# 4. Telegram: verify user ID is in allowedUserIds
# 5. Discord: verify Message Content Intent is enabled
```

### Feishu Bot Issues

```bash
# 1. Ensure service is running BEFORE configuring event subscription
openclaw gateway status

# 2. Check logs for WebSocket connection
openclaw logs --follow | grep -i feishu

# 3. Verify event subscription uses "long-connection" mode, not webhook

# 4. Check permissions in Feishu admin console
```

### WhatsApp QR Code Not Appearing

```bash
# 1. Stop service
openclaw gateway stop

# 2. Clear WhatsApp session
rm -rf ~/.openclaw/.wwebjs_auth

# 3. Start in foreground to see QR
source ~/.openclaw/env && openclaw gateway

# 4. Wait ~30 seconds for QR generation
```

### Configuration Reset

```bash
# Backup current config
cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.backup
cp ~/.openclaw/env ~/.openclaw/env.backup

# Re-run onboarding
openclaw onboard

# Or use installer
curl -fsSL https://raw.githubusercontent.com/miaoxworld/OpenClawInstaller/main/install.sh | bash
```

### Permission Errors (macOS)

```bash
# If install.sh blocked by Gatekeeper
chmod +x install.sh
xattr -d com.apple.quarantine install.sh

# Or manually install OpenClaw first
npm install -g openclaw
# Then run installer for configuration only
./install.sh
```

## Advanced Usage

### Custom Skills

Create Markdown skill files in `~/.openclaw/skills/`:

```markdown
# My Custom Skill

## Trigger
When user asks about {topic}

## Action
1. Analyze request
2. Execute logic
3. Return formatted response
```

### Memory Management

```bash
# Export conversation history
openclaw export --format json > conversations.json

# Clear memory
openclaw memory clear

# Backup data
openclaw backup
```

### Environment Variable Reference

```bash
# ~/.openclaw/env
export ANTHROPIC_API_KEY=sk-ant-xxxxx
export ANTHROPIC_BASE_URL=https://custom-proxy.com
export OPENAI_API_KEY=sk-xxxxx
export OPENAI_BASE_URL=https://custom-proxy.com/v1
export GOOGLE_API_KEY=xxxxx
export GROQ_API_KEY=xxxxx
export OPENROUTER_API_KEY=xxxxx
export TELEGRAM_BOT_TOKEN=xxxxx
export DISCORD_BOT_TOKEN=xxxxx
export FEISHU_APP_ID=cli_xxxxx
export FEISHU_APP_SECRET=xxxxx
```

### Running Multiple Instances

```bash
# Use different ports and config directories
OPENCLAW_CONFIG_DIR=~/.openclaw-dev \
OPENCLAW_PORT=3001 \
openclaw gateway start

# Separate environment files
source ~/.openclaw-dev/env
openclaw gateway
```
