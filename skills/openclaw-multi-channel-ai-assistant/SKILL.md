---
name: openclaw-multi-channel-ai-assistant
description: Self-hosted multi-channel AI assistant platform with unified Gateway, supporting WhatsApp, Telegram, Discord, and 20+ chat platforms with Claude, GPT, DeepSeek integration
triggers:
  - how do I set up OpenClaw with Telegram
  - configure OpenClaw gateway and channels
  - deploy OpenClaw AI assistant
  - connect OpenClaw to WhatsApp or Discord
  - OpenClaw agent configuration and tools
  - troubleshoot OpenClaw channel connection
  - setup OpenClaw with Claude or GPT
  - OpenClaw node and plugin system
---

# OpenClaw Multi-Channel AI Assistant

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

OpenClaw is a self-hosted multi-channel AI assistant platform that connects Web control UI, chat channels (WhatsApp, Telegram, Discord, Slack, Signal, Feishu, etc.), nodes, tools, and AI models through a unified Gateway architecture. This skill covers installation, configuration, channel setup, AI provider integration, plugin development, and troubleshooting.

## What OpenClaw Does

- **Unified Gateway**: Central control plane that routes messages between channels, nodes, and AI agents
- **Multi-Channel Support**: 20+ chat platforms including WhatsApp, Telegram, Discord, Slack, Signal, iMessage, Feishu, Teams, Matrix
- **Multi-Model Support**: Anthropic Claude, OpenAI GPT, DeepSeek, Qwen, Kimi, GLM, Ollama, and more
- **Tool System**: Browser automation, code execution, skills, sub-agents
- **Plugin Architecture**: Hooks, adapters, and extensible agent framework
- **Mobile Nodes**: Remote execution nodes for distributed AI workflows
- **Web UI**: Browser-based control panel for configuration and monitoring

## Installation

### Quick Start (Docker)

```bash
# Clone the repository
git clone https://github.com/openclaw/openclaw.git
cd openclaw

# Copy environment template
cp .env.example .env

# Edit .env with your configuration
# Required: OPENAI_API_KEY or ANTHROPIC_API_KEY
nano .env

# Start with Docker Compose
docker-compose up -d

# Access Web UI at http://localhost:3000
```

### Node.js Installation

```bash
# Prerequisites: Node.js 18+ and npm
git clone https://github.com/openclaw/openclaw.git
cd openclaw

# Install dependencies
npm install

# Configure environment
cp .env.example .env
# Edit .env with API keys and settings

# Start Gateway
npm run gateway:start

# Start Web UI (separate terminal)
npm run web:start

# Start a channel adapter (example: Telegram)
npm run channel:telegram
```

### Cloud Deployment

```bash
# Deploy to VPS (Ubuntu/Debian)
curl -fsSL https://get.openclaw.ai | bash

# Or use the official installer
wget https://github.com/openclaw/openclaw/releases/latest/download/install.sh
chmod +x install.sh
./install.sh

# Follow prompts to configure Gateway, channels, and models
```

## Core Architecture

### Gateway Configuration

The Gateway is the central hub. Configure in `config/gateway.yaml`:

```yaml
gateway:
  host: 0.0.0.0
  port: 3000
  secret: ${GATEWAY_SECRET}
  
channels:
  - type: telegram
    enabled: true
    token: ${TELEGRAM_BOT_TOKEN}
  
  - type: whatsapp
    enabled: true
    provider: baileys  # or twilio
    
  - type: discord
    enabled: true
    token: ${DISCORD_BOT_TOKEN}

models:
  - provider: anthropic
    model: claude-3-5-sonnet-20241022
    apiKey: ${ANTHROPIC_API_KEY}
    
  - provider: openai
    model: gpt-4-turbo
    apiKey: ${OPENAI_API_KEY}
    
  - provider: ollama
    model: llama3
    baseURL: http://localhost:11434

agent:
  defaultModel: claude-3-5-sonnet-20241022
  temperature: 0.7
  maxTokens: 4096
  contextWindow: 200000
  
memory:
  provider: sqlite  # or postgres, redis
  retentionDays: 30
  
tools:
  browser: true
  codeExecution: false  # requires sandboxed environment
  webSearch: true
```

### Environment Variables

Create `.env` file:

```bash
# Gateway
GATEWAY_SECRET=your-secure-random-secret
GATEWAY_PORT=3000

# AI Providers
ANTHROPIC_API_KEY=sk-ant-xxxxx
OPENAI_API_KEY=sk-xxxxx
DEEPSEEK_API_KEY=sk-xxxxx

# Channels - Telegram
TELEGRAM_BOT_TOKEN=123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11

# Channels - Discord
DISCORD_BOT_TOKEN=MTk4NjIy...

# Channels - WhatsApp (Baileys)
WHATSAPP_SESSION_PATH=./sessions/whatsapp

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/openclaw
# or
DATABASE_URL=sqlite:./data/openclaw.db

# Memory
MEMORY_PROVIDER=sqlite
MEMORY_RETENTION_DAYS=30

# Tools
ENABLE_BROWSER_TOOL=true
ENABLE_CODE_EXECUTION=false
SEARXNG_URL=http://localhost:8080
```

## CLI Commands

### Gateway Management

```bash
# Start Gateway server
npm run gateway:start
# or
./openclaw gateway start

# Check Gateway status
./openclaw gateway status

# Reload configuration without restart
./openclaw gateway reload

# View Gateway logs
./openclaw gateway logs --tail 100
```

### Channel Management

```bash
# List available channels
./openclaw channels list

# Start a specific channel adapter
./openclaw channel start telegram
./openclaw channel start whatsapp
./openclaw channel start discord

# Stop a channel
./openclaw channel stop telegram

# Channel authentication (WhatsApp)
./openclaw channel auth whatsapp
# Scan QR code with WhatsApp mobile app
```

### Node Management

```bash
# Register a new node
./openclaw node register --name "mobile-node-1" --type mobile

# List active nodes
./openclaw node list

# Start a node
./openclaw node start mobile-node-1

# Node health check
./openclaw node health mobile-node-1
```

### Plugin Management

```bash
# List installed plugins
./openclaw plugin list

# Install a plugin
./openclaw plugin install @openclaw/plugin-calendar

# Create new plugin scaffold
./openclaw plugin create my-custom-plugin

# Enable/disable plugin
./openclaw plugin enable my-custom-plugin
./openclaw plugin disable my-custom-plugin
```

## Channel Adapter Implementation

### Creating a Custom Channel Adapter

```javascript
// plugins/channels/my-channel/index.js
import { ChannelAdapter } from '@openclaw/core';

export default class MyChannelAdapter extends ChannelAdapter {
  constructor(config) {
    super(config);
    this.client = null;
  }

  async initialize() {
    // Initialize your channel client
    this.client = new MyChannelClient({
      token: this.config.token,
      apiUrl: this.config.apiUrl
    });

    // Set up incoming message handler
    this.client.on('message', this.handleIncomingMessage.bind(this));
    
    await this.client.connect();
    this.logger.info('MyChannel adapter initialized');
  }

  async handleIncomingMessage(rawMessage) {
    // Normalize to OpenClaw message format
    const message = {
      id: rawMessage.id,
      channelType: 'my-channel',
      channelUserId: rawMessage.sender.id,
      text: rawMessage.content,
      timestamp: rawMessage.timestamp,
      metadata: {
        chatId: rawMessage.chat.id,
        messageType: rawMessage.type
      }
    };

    // Route to Gateway for Agent processing
    await this.routeToAgent(message);
  }

  async sendMessage(sessionKey, response) {
    // Extract channel-specific routing info
    const { chatId } = this.extractRoutingInfo(sessionKey);
    
    // Send through channel API
    await this.client.sendMessage({
      chatId,
      text: response.text,
      attachments: response.attachments
    });
  }

  async cleanup() {
    if (this.client) {
      await this.client.disconnect();
    }
  }
}

// Plugin manifest
export const manifest = {
  name: 'my-channel',
  version: '1.0.0',
  type: 'channel-adapter',
  requiredConfig: ['token'],
  optionalConfig: ['apiUrl']
};
```

### Register Channel in Gateway

```javascript
// config/channels.js
import MyChannelAdapter from './plugins/channels/my-channel/index.js';

export const channelRegistry = {
  telegram: TelegramAdapter,
  whatsapp: WhatsAppAdapter,
  discord: DiscordAdapter,
  'my-channel': MyChannelAdapter
};
```

## Agent Configuration & Tools

### Custom Agent Prompt

```javascript
// config/agent.js
export const agentConfig = {
  systemPrompt: `You are a helpful AI assistant with access to various tools.
  
Available tools:
- browser: Navigate websites and extract information
- webSearch: Search the internet using SearXNG
- executeCode: Run Python code (sandboxed)

Guidelines:
- Be concise but helpful
- Always cite sources when using web search
- Ask for confirmation before executing code
- Respect user privacy`,

  model: 'claude-3-5-sonnet-20241022',
  temperature: 0.7,
  maxTokens: 4096,
  
  // Tool configuration
  tools: {
    browser: {
      enabled: true,
      timeout: 30000,
      userAgent: 'OpenClaw/1.0'
    },
    webSearch: {
      enabled: true,
      searxngUrl: process.env.SEARXNG_URL,
      maxResults: 5
    },
    codeExecution: {
      enabled: false, // Requires secure sandbox
      languages: ['python', 'javascript'],
      timeout: 10000
    }
  },
  
  // Memory configuration
  memory: {
    enabled: true,
    contextWindow: 50, // Last 50 messages
    summarization: true,
    summaryThreshold: 30
  }
};
```

### Implementing a Custom Tool

```javascript
// plugins/tools/weather-tool.js
import { Tool } from '@openclaw/core';

export default class WeatherTool extends Tool {
  constructor() {
    super({
      name: 'getWeather',
      description: 'Get current weather for a location',
      parameters: {
        type: 'object',
        properties: {
          location: {
            type: 'string',
            description: 'City name or coordinates'
          },
          units: {
            type: 'string',
            enum: ['metric', 'imperial'],
            default: 'metric'
          }
        },
        required: ['location']
      }
    });
  }

  async execute({ location, units = 'metric' }) {
    const apiKey = process.env.WEATHER_API_KEY;
    const response = await fetch(
      `https://api.openweathermap.org/data/2.5/weather?q=${location}&units=${units}&appid=${apiKey}`
    );
    
    if (!response.ok) {
      throw new Error(`Weather API error: ${response.statusText}`);
    }
    
    const data = await response.json();
    
    return {
      location: data.name,
      temperature: data.main.temp,
      condition: data.weather[0].description,
      humidity: data.main.humidity,
      units
    };
  }
}

// Register tool
import { registerTool } from '@openclaw/core';
registerTool(new WeatherTool());
```

### Agent with Tool Usage

```javascript
// Example: Agent processing with tool invocation
import { Agent } from '@openclaw/core';

const agent = new Agent({
  model: 'claude-3-5-sonnet-20241022',
  apiKey: process.env.ANTHROPIC_API_KEY,
  tools: ['getWeather', 'webSearch', 'browser']
});

async function processUserMessage(message, sessionKey) {
  // Load conversation context
  const context = await agent.getContext(sessionKey);
  
  // Process with tool support
  const response = await agent.process({
    messages: [...context, { role: 'user', content: message }],
    sessionKey,
    toolChoice: 'auto' // Let agent decide when to use tools
  });
  
  // Response includes tool calls if any were made
  if (response.toolCalls) {
    console.log('Tools used:', response.toolCalls.map(t => t.name));
  }
  
  // Save to memory
  await agent.saveContext(sessionKey, response);
  
  return response.content;
}
```

## Session & Context Management

### Session Key Format

OpenClaw uses session keys to route conversations:

```javascript
// Format: channel:channelUserId:chatId
const sessionKey = 'telegram:123456789:987654321';
const sessionKey = 'whatsapp:+1234567890:group123';
const sessionKey = 'discord:user123:channel456';

// Parse session key
function parseSessionKey(sessionKey) {
  const [channel, userId, chatId] = sessionKey.split(':');
  return { channel, userId, chatId };
}

// Create session key
function createSessionKey(channel, userId, chatId) {
  return `${channel}:${userId}:${chatId || userId}`;
}
```

### Context Window Management

```javascript
// services/context-manager.js
import { ContextManager } from '@openclaw/core';

const contextManager = new ContextManager({
  provider: 'sqlite',
  dbPath: './data/context.db',
  maxMessages: 50,
  summarizationThreshold: 30
});

// Store message
await contextManager.addMessage(sessionKey, {
  role: 'user',
  content: 'What is the weather in Tokyo?',
  timestamp: Date.now()
});

// Retrieve context with automatic summarization
const context = await contextManager.getContext(sessionKey, {
  maxTokens: 4000, // Token budget for context
  includeSummary: true
});

// Manual context pruning
await contextManager.pruneContext(sessionKey, {
  keepLast: 20,
  summarizeRest: true
});

// Clear context for session
await contextManager.clearContext(sessionKey);
```

## Plugin Development

### Plugin Hook System

```javascript
// plugins/my-plugin/index.js
export default class MyPlugin {
  constructor(core) {
    this.core = core;
  }

  // Hook: Before message is sent to agent
  async onBeforeAgentProcess(message, context) {
    console.log('Processing message:', message.text);
    
    // Modify message or context
    if (message.text.includes('urgent')) {
      context.priority = 'high';
    }
    
    return { message, context };
  }

  // Hook: After agent response, before sending
  async onBeforeMessageSend(response, sessionKey) {
    // Add custom footer
    response.text += '\n\n_Powered by OpenClaw_';
    
    // Log analytics
    await this.logAnalytics(sessionKey, response);
    
    return response;
  }

  // Hook: On tool execution
  async onToolExecute(toolName, params, result) {
    console.log(`Tool ${toolName} executed with:`, params);
    return result;
  }

  async logAnalytics(sessionKey, response) {
    // Custom analytics logic
  }
}

export const manifest = {
  name: 'my-plugin',
  version: '1.0.0',
  hooks: [
    'onBeforeAgentProcess',
    'onBeforeMessageSend',
    'onToolExecute'
  ]
};
```

### Plugin Configuration

```yaml
# config/plugins.yaml
plugins:
  - name: my-plugin
    enabled: true
    config:
      customSetting: value
      
  - name: analytics-plugin
    enabled: true
    config:
      endpoint: https://analytics.example.com
      apiKey: ${ANALYTICS_API_KEY}
      
  - name: calendar-integration
    enabled: false
```

## Advanced Configuration

### Multi-Model Fallback

```javascript
// config/models.js
export const modelConfig = {
  providers: [
    {
      name: 'primary',
      provider: 'anthropic',
      model: 'claude-3-5-sonnet-20241022',
      apiKey: process.env.ANTHROPIC_API_KEY,
      priority: 1
    },
    {
      name: 'fallback-1',
      provider: 'openai',
      model: 'gpt-4-turbo',
      apiKey: process.env.OPENAI_API_KEY,
      priority: 2
    },
    {
      name: 'fallback-2',
      provider: 'ollama',
      model: 'llama3',
      baseURL: 'http://localhost:11434',
      priority: 3
    }
  ],
  
  fallbackStrategy: 'priority', // or 'round-robin', 'random'
  retryAttempts: 3,
  timeoutMs: 30000
};

// Agent will automatically fallback on errors
import { Agent } from '@openclaw/core';
const agent = new Agent({ models: modelConfig });
```

### Memory Backends

```javascript
// SQLite (default)
export const memoryConfig = {
  provider: 'sqlite',
  path: './data/memory.db'
};

// PostgreSQL
export const memoryConfig = {
  provider: 'postgres',
  connectionString: process.env.DATABASE_URL,
  schema: 'openclaw_memory'
};

// Redis
export const memoryConfig = {
  provider: 'redis',
  host: process.env.REDIS_HOST || 'localhost',
  port: process.env.REDIS_PORT || 6379,
  password: process.env.REDIS_PASSWORD,
  db: 0,
  ttl: 2592000 // 30 days in seconds
};
```

## Common Patterns

### Multi-Channel Broadcasting

```javascript
// Broadcast message to multiple channels
import { Gateway } from '@openclaw/core';

const gateway = new Gateway();

async function broadcast(message, channels) {
  const promises = channels.map(async ({ channel, userId }) => {
    const sessionKey = `${channel}:${userId}:${userId}`;
    await gateway.sendMessage(sessionKey, {
      text: message,
      metadata: { broadcast: true }
    });
  });
  
  await Promise.all(promises);
}

// Usage
await broadcast('System maintenance in 10 minutes', [
  { channel: 'telegram', userId: '123456' },
  { channel: 'discord', userId: '789012' },
  { channel: 'whatsapp', userId: '+1234567890' }
]);
```

### Scheduled Tasks with Cron

```javascript
// plugins/scheduler/index.js
import cron from 'node-cron';
import { Gateway } from '@openclaw/core';

export default class SchedulerPlugin {
  constructor(core) {
    this.core = core;
    this.gateway = new Gateway();
    this.jobs = [];
  }

  async initialize() {
    // Daily summary at 9 AM
    const dailySummary = cron.schedule('0 9 * * *', async () => {
      await this.sendDailySummary();
    });
    
    this.jobs.push(dailySummary);
    
    // Hourly reminder check
    const reminderCheck = cron.schedule('0 * * * *', async () => {
      await this.checkReminders();
    });
    
    this.jobs.push(reminderCheck);
  }

  async sendDailySummary() {
    const users = await this.core.getActiveUsers();
    
    for (const user of users) {
      const summary = await this.generateSummary(user);
      await this.gateway.sendMessage(user.sessionKey, {
        text: `📊 Daily Summary\n\n${summary}`
      });
    }
  }

  async checkReminders() {
    // Implementation
  }

  async cleanup() {
    this.jobs.forEach(job => job.stop());
  }
}
```

### Webhook Integration

```javascript
// services/webhook-handler.js
import express from 'express';
import { Gateway } from '@openclaw/core';

const app = express();
const gateway = new Gateway();

app.use(express.json());

// GitHub webhook
app.post('/webhooks/github', async (req, res) => {
  const event = req.headers['x-github-event'];
  const payload = req.body;
  
  if (event === 'push') {
    const message = `🔔 New push to ${payload.repository.full_name}\n` +
                   `Branch: ${payload.ref}\n` +
                   `Commits: ${payload.commits.length}\n` +
                   `Author: ${payload.pusher.name}`;
    
    // Send to configured channel
    await gateway.sendMessage(
      process.env.GITHUB_NOTIFICATION_SESSION,
      { text: message }
    );
  }
  
  res.status(200).send('OK');
});

app.listen(3001, () => {
  console.log('Webhook server running on port 3001');
});
```

## Troubleshooting

### Gateway Connection Issues

```bash
# Check Gateway is running
curl http://localhost:3000/health

# Check Gateway logs
tail -f logs/gateway.log

# Verify environment variables
./openclaw config verify

# Test channel connectivity
./openclaw channel test telegram
```

### Channel Adapter Not Receiving Messages

```javascript
// Enable debug logging
// config/gateway.yaml
logging:
  level: debug
  channels: true
  
// Or via environment
DEBUG=openclaw:channel:* npm run gateway:start

// Common issues:
// 1. Webhook not configured (Telegram, Discord)
// 2. Wrong bot token
// 3. Firewall blocking incoming connections
// 4. WhatsApp session expired (re-auth needed)
```

### Memory/Context Issues

```javascript
// Check context size
import { ContextManager } from '@openclaw/core';
const cm = new ContextManager();

const stats = await cm.getContextStats(sessionKey);
console.log('Messages:', stats.messageCount);
console.log('Estimated tokens:', stats.tokenCount);

// If context too large:
await cm.summarizeContext(sessionKey);

// Or reset completely:
await cm.clearContext(sessionKey);
```

### Model API Errors

```bash
# Test API connectivity
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "content-type: application/json" \
  -d '{"model":"claude-3-5-sonnet-20241022","max_tokens":1024,"messages":[{"role":"user","content":"test"}]}'

# Check API key validity
./openclaw models test anthropic

# Enable model fallback
# config/agent.js
export const agentConfig = {
  enableFallback: true,
  fallbackModels: ['openai/gpt-4-turbo', 'ollama/llama3']
};
```

### WhatsApp QR Code Not Appearing

```bash
# Clear WhatsApp session
rm -rf sessions/whatsapp/*

# Restart WhatsApp adapter with fresh auth
./openclaw channel stop whatsapp
./openclaw channel start whatsapp --fresh-auth

# Check logs for QR code
tail -f logs/whatsapp.log

# If using Docker, ensure terminal is attached
docker-compose logs -f whatsapp
```

### Database Migration Issues

```bash
# Check current migration status
./openclaw db status

# Run pending migrations
./openclaw db migrate

# Rollback last migration
./openclaw db rollback

# Reset database (WARNING: deletes all data)
./openclaw db reset --confirm
```

### Performance Optimization

```javascript
// config/performance.js
export const performanceConfig = {
  // Enable response streaming for faster UX
  streaming: true,
  
  // Parallel tool execution
  parallelTools: true,
  maxParallelTools: 3,
  
  // Context caching (reduces API calls)
  contextCache: {
    enabled: true,
    ttl: 300, // 5 minutes
    maxSize: 100 // Cache 100 sessions
  },
  
  // Rate limiting per user
  rateLimit: {
    enabled: true,
    maxRequests: 50,
    windowMs: 60000 // 1 minute
  }
};
```

## Production Deployment Checklist

```bash
# 1. Set secure secrets
GATEWAY_SECRET=$(openssl rand -hex 32)

# 2. Use environment-specific configs
NODE_ENV=production

# 3. Configure process manager
# pm2 ecosystem.config.js
module.exports = {
  apps: [{
    name: 'openclaw-gateway',
    script: './dist/gateway.js',
    instances: 2,
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'production'
    }
  }]
};

# 4. Set up monitoring
# - Health check endpoint: /health
# - Metrics endpoint: /metrics (Prometheus)
# - Logging: Winston + external aggregator

# 5. Enable HTTPS
# Use reverse proxy (nginx, Caddy) with SSL cert

# 6. Database backups
# PostgreSQL: pg_dump scheduled via cron
# SQLite: File-based backups

# 7. Update strategy
# Blue-green deployment or rolling updates
# Test in staging first
```

This skill covers OpenClaw installation, channel configuration, agent setup, plugin development, and production deployment. All code examples use environment variables for secrets and demonstrate real integration patterns.
