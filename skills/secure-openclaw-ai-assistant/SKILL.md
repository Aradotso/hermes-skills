---
name: secure-openclaw-ai-assistant
description: Deploy and configure Secure OpenClaw, a 24/7 AI assistant on WhatsApp, Telegram, Signal, and iMessage with Claude, tool access, and 500+ app integrations
triggers:
  - set up secure openclaw assistant
  - deploy openclaw to digitalocean
  - configure whatsapp bot with claude
  - add telegram integration to openclaw
  - set up ai assistant with memory
  - configure composio tool router
  - deploy personal ai assistant remotely
  - set up openclaw reminders and scheduling
---

# Secure OpenClaw AI Assistant

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

Secure OpenClaw is a personal 24/7 AI assistant that runs on messaging platforms (WhatsApp, Telegram, Signal, iMessage) powered by Claude with full tool access, persistent memory, scheduled reminders, and integrations with 500+ apps via Composio.

## Installation

```bash
git clone https://github.com/composio-community/secure-openclaw.git
cd secure-openclaw
npm install
```

### Prerequisites

- Node.js 18+
- Anthropic API key from https://console.anthropic.com/
- Composio API key: `curl -fsSL https://composio.dev/install | bash && composio login`

### Install AI Provider

**Claude Code** (recommended):
```bash
npm install -g @anthropic-ai/claude-code
claude  # authenticate interactively
```

**Opencode** (alternative):
```bash
curl -fsSL https://opencode.ai/install | bash
```

### Set API Keys

```bash
export ANTHROPIC_API_KEY=sk-ant-...
export COMPOSIO_API_KEY=your-key

# Add to ~/.zshrc or ~/.bashrc for persistence
echo 'export ANTHROPIC_API_KEY=sk-ant-...' >> ~/.zshrc
echo 'export COMPOSIO_API_KEY=your-key' >> ~/.zshrc
```

## Quick Start

### Local Development

```bash
# Interactive menu
node cli.js

# Direct commands
node cli.js chat     # terminal chat
node cli.js start    # start messaging gateway
```

### Remote Deployment (DigitalOcean)

```bash
# 1. Create Ubuntu 24.04 droplet ($6/mo)
# 2. SSH in and add swap
ssh root@YOUR_DROPLET_IP
fallocate -l 2G /swapfile && chmod 600 /swapfile && mkswap /swapfile && swapon /swapfile
echo '/swapfile none swap sw 0 0' >> /etc/fstab

# 3. Install Docker
curl -fsSL https://get.docker.com | sh

# 4. Clone and configure
git clone https://github.com/YOUR_USERNAME/secure-openclaw.git
cd secure-openclaw
cp .env.example .env
nano .env  # Add ANTHROPIC_API_KEY, COMPOSIO_API_KEY, etc.

# 5. Deploy
docker compose up -d --build
ufw allow 4096

# 6. Connect WhatsApp
# Visit http://YOUR_DROPLET_IP:4096/qr and scan QR code
```

## Configuration

### config.js Structure

```javascript
module.exports = {
  agentId: 'secure-openclaw',
  
  // Messaging platforms
  whatsapp: {
    enabled: true,
    allowedDMs: ['+1234567890'],      // Specific numbers
    allowedGroups: ['*'],              // All groups
    respondToMentionsOnly: true        // Only respond when @mentioned
  },
  
  telegram: {
    enabled: false,
    token: process.env.TELEGRAM_BOT_TOKEN,
    allowedDMs: ['*'],
    allowedGroups: []
  },
  
  signal: {
    enabled: false,
    phoneNumber: '+1234567890',
    signalCliPath: 'signal-cli',
    allowedDMs: ['*']
  },
  
  imessage: {
    enabled: false,  // macOS only
    allowedDMs: ['user@icloud.com'],
    allowedGroups: []
  },
  
  // Agent configuration
  agent: {
    workspace: '~/secure-openclaw',
    maxTurns: 100,
    allowedTools: ['Read', 'Write', 'Edit', 'Bash', 'Glob', 'Grep'],
    provider: 'claude',  // or 'opencode'
    
    opencode: {
      model: 'opencode/gpt-5-nano',
      hostname: '127.0.0.1',
      port: 4096
    }
  }
};
```

### Environment Variables (.env)

```bash
ANTHROPIC_API_KEY=sk-ant-...
COMPOSIO_API_KEY=your-composio-key

# Platform tokens
TELEGRAM_BOT_TOKEN=123456:ABC-DEF...
SIGNAL_PHONE_NUMBER=+1234567890

# Optional
NODE_ENV=production
PORT=4096
```

## Messaging Platform Setup

### WhatsApp

No bot token needed — uses QR authentication:

```javascript
// config.js
whatsapp: {
  enabled: true,
  allowedDMs: ['+1234567890', '+0987654321'],
  allowedGroups: ['Group Name', 'Another Group'],
  respondToMentionsOnly: true
}
```

Start gateway, scan QR in terminal or visit `http://localhost:4096/qr`. Session persists in `auth_whatsapp/`.

### Telegram

```bash
# 1. Get token from @BotFather
# Send /newbot, follow prompts, copy token

# 2. Configure
export TELEGRAM_BOT_TOKEN=123456:ABC-DEF...
```

```javascript
// config.js
telegram: {
  enabled: true,
  token: process.env.TELEGRAM_BOT_TOKEN,
  allowedDMs: ['*'],  // All users
  allowedGroups: []   // No groups
}
```

### Signal

```bash
# Install signal-cli
brew install signal-cli  # macOS
apt-get install signal-cli  # Linux

# Register
signal-cli -u +1234567890 register
signal-cli -u +1234567890 verify CODE_FROM_SMS
```

```javascript
// config.js
signal: {
  enabled: true,
  phoneNumber: '+1234567890',
  signalCliPath: 'signal-cli',
  allowedDMs: ['*']
}
```

### iMessage (macOS only)

```bash
brew install steipete/formulae/imsg
```

```javascript
// config.js
imessage: {
  enabled: true,
  allowedDMs: ['user@icloud.com', '+1234567890'],
  allowedGroups: []
}
```

## Core Features

### Terminal Chat

```bash
node cli.js chat
```

Commands:
- `/model` — Switch AI model
- `/memory` — View or search memories
- `/clear` — Clear conversation
- `/help` — Show commands
- `/exit` — Exit chat

### Memory System

Persistent memory at `~/secure-openclaw/`:

```
~/secure-openclaw/
  MEMORY.md              # Long-term: preferences, people, decisions
  memory/
    2026-07-29.md        # Daily logs
    project-alpha.md     # Topic-specific notes
```

Usage:
```
User: Remember that I prefer meetings after 2pm
Assistant: [Writes to MEMORY.md]

User: What are my meeting preferences?
Assistant: [Reads MEMORY.md] You prefer meetings after 2pm.
```

### Scheduling and Reminders

```
User: Remind me in 30 minutes to check the oven
Assistant: [Creates one-time cron job]

User: Every day at 9am send me a standup reminder
Assistant: [Creates cron: 0 9 * * *]

User: Every weekday at 8am
Assistant: [Creates cron: 0 8 * * 1-5]
```

Jobs persist in `~/.secure-openclaw/cron-jobs.json`.

### Tool Approvals

When assistant needs permission to run tools:

**Terminal chat:**
```
🔧 Claude wants to use: Bash
Command: npm install axios
Approve? (y/n): y
```

**Messaging platforms:**
```
Claude wants to use Bash to install axios.
Reply Y to allow, N to deny.
```

Timeout: 2 minutes. Configure in `agent.allowedTools`.

## Composio Integration

500+ app integrations (Gmail, Slack, GitHub, Calendar, etc.):

```bash
# Install Composio
curl -fsSL https://composio.dev/install | bash
composio login

# Connect apps
composio add gmail
composio add slack
composio add github

# List connected apps
composio apps
```

Usage in chat:
```
User: Send an email to john@example.com about the meeting
Assistant: [Uses Gmail integration]

User: Create a GitHub issue in my-repo: "Fix login bug"
Assistant: [Uses GitHub integration]

User: Add "Team standup" to my calendar at 9am tomorrow
Assistant: [Uses Google Calendar integration]
```

## Code Examples

### Custom Gateway Adapter

```javascript
// adapters/discord.js
const { EventEmitter } = require('events');

class DiscordAdapter extends EventEmitter {
  constructor(config) {
    super();
    this.config = config;
    this.client = null;
  }

  async start() {
    const { Client, GatewayIntentBits } = require('discord.js');
    
    this.client = new Client({
      intents: [
        GatewayIntentBits.Guilds,
        GatewayIntentBits.GuildMessages,
        GatewayIntentBits.MessageContent
      ]
    });

    this.client.on('messageCreate', async (message) => {
      if (message.author.bot) return;
      
      // Security check
      if (!this.isAllowed(message.author.id, message.channel.id)) {
        return;
      }

      this.emit('message', {
        text: message.content,
        sender: message.author.id,
        conversation: message.channel.id,
        platform: 'discord',
        sendReply: async (reply) => {
          await message.channel.send(reply);
        }
      });
    });

    await this.client.login(this.config.token);
  }

  isAllowed(userId, channelId) {
    const { allowedDMs, allowedChannels } = this.config;
    if (allowedDMs.includes('*')) return true;
    return allowedDMs.includes(userId) || allowedChannels.includes(channelId);
  }

  async stop() {
    if (this.client) await this.client.destroy();
  }
}

module.exports = DiscordAdapter;
```

### Register in Gateway

```javascript
// gateway.js
const DiscordAdapter = require('./adapters/discord');

class Gateway extends EventEmitter {
  constructor(config) {
    super();
    this.adapters = [];
    
    if (config.discord?.enabled) {
      const adapter = new DiscordAdapter(config.discord);
      this.adapters.push(adapter);
      adapter.on('message', (msg) => this.handleMessage(msg));
    }
    // ... other adapters
  }
}
```

### Custom Tool Permission Handler

```javascript
// agent.js
class Agent {
  async requestPermission(tool, args, context) {
    const { platform, sender } = context;
    
    // Auto-approve safe tools
    const safeLists = ['Read', 'Glob', 'Grep'];
    if (safeLists.includes(tool)) return true;
    
    // Request approval for sensitive tools
    if (platform === 'whatsapp') {
      return new Promise((resolve) => {
        this.pendingApprovals.set(sender, {
          resolve,
          tool,
          args,
          timeout: setTimeout(() => {
            this.pendingApprovals.delete(sender);
            resolve(false);
          }, 120000)  // 2 min timeout
        });
        
        this.sendMessage(sender, 
          `Claude wants to use ${tool}.\nReply Y to allow, N to deny.`
        );
      });
    }
    
    return false;  // Deny by default
  }
  
  handleApprovalResponse(sender, response) {
    const pending = this.pendingApprovals.get(sender);
    if (!pending) return;
    
    clearTimeout(pending.timeout);
    this.pendingApprovals.delete(sender);
    
    const approved = response.toLowerCase().startsWith('y');
    pending.resolve(approved);
  }
}
```

## Docker Deployment

### Dockerfile

```dockerfile
FROM node:18-alpine

RUN apk add --no-cache bash curl git python3 make g++

# Install Claude Code
RUN npm install -g @anthropic-ai/claude-code

# Install Opencode
RUN curl -fsSL https://opencode.ai/install | bash

WORKDIR /app

COPY package*.json ./
RUN npm ci --production

COPY . .

# Create volumes for persistence
VOLUME ["/root/.secure-openclaw", "/app/auth_whatsapp"]

EXPOSE 4096

CMD ["node", "cli.js", "start"]
```

### docker-compose.yml

```yaml
version: '3.8'

services:
  openclaw:
    build: .
    environment:
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
      - COMPOSIO_API_KEY=${COMPOSIO_API_KEY}
      - TELEGRAM_BOT_TOKEN=${TELEGRAM_BOT_TOKEN}
      - NODE_ENV=production
    ports:
      - "4096:4096"
    volumes:
      - openclaw-memory:/root/.secure-openclaw
      - whatsapp-auth:/app/auth_whatsapp
    restart: unless-stopped

volumes:
  openclaw-memory:
  whatsapp-auth:
```

### Deploy Commands

```bash
# Build and start
docker compose up -d --build

# View logs
docker compose logs -f
docker compose logs -f --tail 50

# Restart
docker compose down && docker compose up -d

# Shell into container
docker compose exec openclaw sh

# Update
git pull && docker compose up -d --build
```

## Troubleshooting

### WhatsApp QR Not Scanning

```bash
# Check gateway is running
docker compose logs -f | grep "Gateway Ready"

# Firewall
ufw allow 4096

# Visit QR page
curl http://YOUR_IP:4096/qr
```

### Out of Memory During Build

```bash
# Add swap (1 GB RAM needs 2 GB swap)
fallocate -l 2G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile none swap sw 0 0' >> /etc/fstab

# Verify
free -h
```

### Claude Authentication Failed

```bash
# Local
claude  # Re-authenticate

# Remote/Docker
docker compose exec openclaw env | grep ANTHROPIC_API_KEY
# Ensure key is in .env and valid
```

### Telegram Bot Not Responding

```bash
# Verify token
curl https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/getMe

# Check allowlist
# config.js telegram.allowedDMs must include user ID or '*'

# Get user ID
# Start bot, send message, check logs for sender ID
docker compose logs -f | grep telegram
```

### Signal Messages Not Sending

```bash
# Test signal-cli
signal-cli -u +1234567890 send -m "test" +0987654321

# Re-register if needed
signal-cli -u +1234567890 unregister
signal-cli -u +1234567890 register
signal-cli -u +1234567890 verify CODE
```

### Memory Not Persisting

```bash
# Check volume mount
docker compose exec openclaw ls -la /root/.secure-openclaw

# Verify workspace path in config.js
agent: {
  workspace: '~/secure-openclaw'  # Expands to /root/.secure-openclaw
}
```

### Tool Execution Failing

```javascript
// Check allowedTools in config.js
agent: {
  allowedTools: [
    'Read', 'Write', 'Edit',  // File operations
    'Bash',                    // Shell commands
    'Glob', 'Grep'            // Search tools
  ]
}

// Check permission mode
// Default: asks for approval
// To auto-approve all: set permissionMode: 'allow'
```

## CLI Reference

```bash
# Interactive menu
node cli.js

# Direct commands
node cli.js chat              # Terminal chat
node cli.js start             # Start gateway
node cli.js setup             # Configure platforms
node cli.js config            # Show config
node cli.js test              # Test API connections
node cli.js provider claude   # Switch to Claude
node cli.js provider opencode # Switch to Opencode
```

## Common Patterns

### Multi-Platform Deployment

```javascript
// config.js - Enable all platforms
module.exports = {
  whatsapp: { enabled: true, allowedDMs: ['*'] },
  telegram: { enabled: true, allowedDMs: ['*'] },
  signal: { enabled: true, allowedDMs: ['*'] },
  imessage: { enabled: true, allowedDMs: ['*'] }
};
```

### Restrict to Specific Users

```javascript
whatsapp: {
  enabled: true,
  allowedDMs: ['+1234567890'],
  allowedGroups: ['Family', 'Work Team'],
  respondToMentionsOnly: true
}
```

### Production with Auto-Restart

```yaml
# docker-compose.yml
services:
  openclaw:
    restart: always
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:4096/"]
      interval: 30s
      timeout: 10s
      retries: 3
```

### Custom Memory Structure

```javascript
// agent.js - Custom memory loader
async loadMemory() {
  const memories = [
    fs.readFileSync(`${this.workspace}/MEMORY.md`, 'utf8'),
    fs.readFileSync(`${this.workspace}/memory/preferences.md`, 'utf8'),
    fs.readFileSync(`${this.workspace}/memory/contacts.md`, 'utf8')
  ];
  return memories.join('\n\n');
}
```

This skill enables AI agents to deploy and configure Secure OpenClaw for personal AI assistant functionality across multiple messaging platforms with full tool access and app integrations.
