---
name: openclaw-chinese-documentation
description: Expert guidance for installing, configuring, and deploying OpenClaw AI agent platform using comprehensive Chinese documentation
triggers:
  - how do I install OpenClaw
  - configure OpenClaw gateway and channels
  - set up OpenClaw with Telegram or WhatsApp
  - OpenClaw AI provider configuration
  - troubleshoot OpenClaw deployment issues
  - OpenClaw plugin and tool system
  - OpenClaw multi-channel bot setup
  - read OpenClaw Chinese documentation
---

# OpenClaw Chinese Documentation Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

Expert knowledge for installing, configuring, and deploying **OpenClaw**, a self-hosted multi-channel AI assistant platform. This skill covers the comprehensive Chinese documentation repository (`yeuxuan/openclaw-docs`) which provides 767 tutorials across installation, architecture, AI framework, and channel adapters.

## What is OpenClaw?

OpenClaw is an open-source personal AI assistant platform that runs on your own infrastructure. It uses a **Gateway** architecture to unify:
- **Web Control UI** for management
- **Chat Channels** (WhatsApp, Telegram, Discord, Slack, Feishu, etc.)
- **Nodes** (mobile/remote execution endpoints)
- **Tools** (browser, code execution, sub-agents)
- **AI Models** (Claude, GPT, DeepSeek, Qwen, Ollama, etc.)

## Documentation Structure

The `openclaw-docs` repository contains 767 articles organized into 4 learning tracks:

### Track 0: Installation Tutorials (680 articles)
- Quick start and guided installation
- Docker / Node.js / cloud server deployment
- Gateway configuration and operations
- Channel integration (Telegram, WhatsApp, Discord, etc.)
- AI provider setup (Anthropic, OpenAI, DeepSeek, Ollama, etc.)
- Core concepts (context, memory, state machine)
- Tools system (browser, execution, skills, sub-agents)
- Plugin development (Manifest, SDK, Hooks)
- Platform support (macOS, Windows, Linux, mobile, VPS)
- Troubleshooting and diagnostics

### Track A: Complete Engineering Path (62 articles)
- CLI startup framework
- Gateway control plane
- Plugin and channel adapter architecture
- Node system
- Routing and session keys
- Agent execution pipeline
- Function-level source code analysis

### Track B: AI Core Framework (24 articles)
- Context engineering
- Agent state machine
- Tool strategy and approval
- Model fallback
- Memory system
- Hook plugin injection mechanism

### Track C: Channel Adapters
- Interface contracts
- Registration pipeline
- Account lifecycle
- Inbound routing
- Outbound sending decoupling

## Running the Documentation Locally

```bash
# Clone the documentation repository
git clone https://github.com/yeuxuan/openclaw-docs.git
cd openclaw-docs

# Install dependencies
npm install

# Run development server
npm run docs:dev

# Build static site
npm run docs:build

# Preview built site
npm run docs:preview
```

The documentation is built with **VitePress** and deployed on Azure Static Web Apps at [openclaw-docs.dx3n.cn](https://openclaw-docs.dx3n.cn).

## Quick Start: Installing OpenClaw

### Prerequisites

```bash
# Node.js 18+ required
node --version

# Docker (optional, for containerized deployment)
docker --version
```

### Basic Installation

```bash
# Install OpenClaw globally via npm
npm install -g openclaw

# Or use npx for one-time execution
npx openclaw init

# Initialize a new OpenClaw instance
openclaw init my-assistant
cd my-assistant

# Start the gateway
openclaw start
```

### Docker Installation

```bash
# Create docker-compose.yml
cat > docker-compose.yml << 'EOF'
version: '3.8'
services:
  openclaw-gateway:
    image: openclaw/gateway:latest
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - GATEWAY_PORT=3000
      - DATABASE_URL=${DATABASE_URL}
    volumes:
      - ./config:/app/config
      - ./data:/app/data
    restart: unless-stopped
EOF

# Start with Docker Compose
docker-compose up -d

# View logs
docker-compose logs -f openclaw-gateway
```

## Configuration

### Gateway Configuration (`config/gateway.yml`)

```yaml
gateway:
  port: 3000
  host: 0.0.0.0
  baseUrl: https://your-domain.com

database:
  type: sqlite
  path: ./data/openclaw.db

ai:
  defaultProvider: anthropic
  providers:
    anthropic:
      apiKey: ${ANTHROPIC_API_KEY}
      model: claude-3-5-sonnet-20241022
    
    openai:
      apiKey: ${OPENAI_API_KEY}
      model: gpt-4
    
    ollama:
      baseUrl: http://localhost:11434
      model: llama2

memory:
  enabled: true
  maxTokens: 100000
  provider: sqlite

tools:
  browser:
    enabled: true
    headless: true
  
  codeExecution:
    enabled: true
    sandboxed: true
```

### Environment Variables (`.env`)

```bash
# AI Provider Keys
ANTHROPIC_API_KEY=your_key_here
OPENAI_API_KEY=your_key_here
DEEPSEEK_API_KEY=your_key_here

# Database
DATABASE_URL=sqlite:./data/openclaw.db

# Gateway
GATEWAY_SECRET=your_secret_here
NODE_ENV=production

# Channel Tokens
TELEGRAM_BOT_TOKEN=your_token_here
DISCORD_BOT_TOKEN=your_token_here
WHATSAPP_API_KEY=your_key_here
FEISHU_APP_ID=your_app_id_here
FEISHU_APP_SECRET=your_app_secret_here
```

## Channel Integration

### Telegram Setup

```javascript
// config/channels/telegram.js
export default {
  type: 'telegram',
  enabled: true,
  config: {
    token: process.env.TELEGRAM_BOT_TOKEN,
    polling: true,
    commands: {
      start: 'Welcome to OpenClaw!',
      help: 'I can help you with various tasks.'
    }
  },
  routing: {
    sessionKey: 'telegram:{chatId}',
    defaultAgent: 'main'
  }
};
```

### WhatsApp Setup (via WhatsApp Business API)

```javascript
// config/channels/whatsapp.js
export default {
  type: 'whatsapp',
  enabled: true,
  config: {
    apiKey: process.env.WHATSAPP_API_KEY,
    webhookUrl: '/webhook/whatsapp',
    phoneNumberId: process.env.WHATSAPP_PHONE_NUMBER_ID,
    verifyToken: process.env.WHATSAPP_VERIFY_TOKEN
  },
  routing: {
    sessionKey: 'whatsapp:{phoneNumber}',
    defaultAgent: 'main'
  }
};
```

### Discord Setup

```javascript
// config/channels/discord.js
export default {
  type: 'discord',
  enabled: true,
  config: {
    token: process.env.DISCORD_BOT_TOKEN,
    intents: ['GUILDS', 'GUILD_MESSAGES', 'DIRECT_MESSAGES'],
    commandPrefix: '!'
  },
  routing: {
    sessionKey: 'discord:{userId}',
    defaultAgent: 'main'
  }
};
```

### Feishu (Lark) Setup

```javascript
// config/channels/feishu.js
export default {
  type: 'feishu',
  enabled: true,
  config: {
    appId: process.env.FEISHU_APP_ID,
    appSecret: process.env.FEISHU_APP_SECRET,
    verificationToken: process.env.FEISHU_VERIFICATION_TOKEN,
    encryptKey: process.env.FEISHU_ENCRYPT_KEY
  },
  routing: {
    sessionKey: 'feishu:{openId}',
    defaultAgent: 'main'
  }
};
```

## AI Provider Configuration

### Using Multiple Providers with Fallback

```javascript
// config/ai.js
export default {
  providers: [
    {
      name: 'primary',
      type: 'anthropic',
      apiKey: process.env.ANTHROPIC_API_KEY,
      model: 'claude-3-5-sonnet-20241022',
      priority: 1
    },
    {
      name: 'fallback',
      type: 'openai',
      apiKey: process.env.OPENAI_API_KEY,
      model: 'gpt-4',
      priority: 2
    },
    {
      name: 'local',
      type: 'ollama',
      baseUrl: 'http://localhost:11434',
      model: 'llama2',
      priority: 3
    }
  ],
  
  fallbackStrategy: 'cascade',
  retryAttempts: 3,
  timeout: 30000
};
```

### Chinese AI Providers

```javascript
// config/providers/chinese.js
export default {
  deepseek: {
    apiKey: process.env.DEEPSEEK_API_KEY,
    baseUrl: 'https://api.deepseek.com',
    model: 'deepseek-chat'
  },
  
  qwen: {
    apiKey: process.env.QWEN_API_KEY,
    baseUrl: 'https://dashscope.aliyuncs.com/api/v1',
    model: 'qwen-max'
  },
  
  kimi: {
    apiKey: process.env.KIMI_API_KEY,
    baseUrl: 'https://api.moonshot.cn/v1',
    model: 'moonshot-v1-8k'
  },
  
  glm: {
    apiKey: process.env.GLM_API_KEY,
    baseUrl: 'https://open.bigmodel.cn/api/paas/v4',
    model: 'glm-4'
  }
};
```

## Plugin Development

### Basic Plugin Structure

```javascript
// plugins/my-plugin/manifest.json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "Custom OpenClaw plugin",
  "author": "Your Name",
  "hooks": {
    "onMessage": "./hooks/onMessage.js",
    "beforeResponse": "./hooks/beforeResponse.js"
  },
  "tools": [
    {
      "name": "customTool",
      "description": "A custom tool",
      "handler": "./tools/customTool.js"
    }
  ]
}
```

### Hook Implementation

```javascript
// plugins/my-plugin/hooks/onMessage.js
export default async function onMessage(context, next) {
  const { message, session, agent } = context;
  
  // Pre-process message
  console.log(`Received message: ${message.text}`);
  
  // Add custom data to context
  context.customData = {
    timestamp: Date.now(),
    channelType: message.channel
  };
  
  // Continue to next middleware
  await next();
  
  // Post-process after agent response
  console.log(`Response sent to ${session.id}`);
}
```

### Custom Tool Implementation

```javascript
// plugins/my-plugin/tools/customTool.js
export default {
  name: 'customTool',
  description: 'Performs a custom action',
  
  parameters: {
    type: 'object',
    properties: {
      action: {
        type: 'string',
        description: 'The action to perform'
      },
      data: {
        type: 'object',
        description: 'Data for the action'
      }
    },
    required: ['action']
  },
  
  async execute({ action, data }, context) {
    // Tool implementation
    switch (action) {
      case 'query':
        return await performQuery(data);
      
      case 'update':
        return await performUpdate(data);
      
      default:
        throw new Error(`Unknown action: ${action}`);
    }
  }
};

async function performQuery(data) {
  // Implementation
  return { status: 'success', result: data };
}

async function performUpdate(data) {
  // Implementation
  return { status: 'updated', data };
}
```

## CLI Commands

```bash
# Initialize new instance
openclaw init [name]

# Start gateway
openclaw start

# Start with specific config
openclaw start --config ./custom-config.yml

# Stop gateway
openclaw stop

# Check status
openclaw status

# View logs
openclaw logs

# Plugin management
openclaw plugin install <plugin-name>
openclaw plugin list
openclaw plugin remove <plugin-name>

# Channel management
openclaw channel add telegram
openclaw channel list
openclaw channel enable telegram
openclaw channel disable telegram

# Node management
openclaw node add <node-name> <node-url>
openclaw node list
openclaw node remove <node-name>

# Database migrations
openclaw migrate up
openclaw migrate down
openclaw migrate status
```

## Common Patterns

### Multi-Channel Routing

```javascript
// config/routing.js
export default {
  routes: [
    {
      // Route Telegram messages to specialized agent
      match: { channel: 'telegram' },
      agent: 'telegram-agent',
      sessionKey: 'telegram:{chatId}'
    },
    {
      // Route WhatsApp business inquiries
      match: { 
        channel: 'whatsapp',
        messageContains: ['price', 'product', 'order']
      },
      agent: 'sales-agent',
      sessionKey: 'whatsapp:{phoneNumber}'
    },
    {
      // Default route
      match: {},
      agent: 'main',
      sessionKey: '{channel}:{userId}'
    }
  ]
};
```

### Agent Configuration with Tools

```javascript
// config/agents/main.js
export default {
  name: 'main',
  description: 'Main assistant agent',
  
  provider: 'anthropic',
  model: 'claude-3-5-sonnet-20241022',
  
  systemPrompt: `You are a helpful AI assistant.
You have access to various tools to help users.
Always be polite and professional.`,
  
  tools: [
    'browser',
    'codeExecution',
    'search',
    'calculator'
  ],
  
  memory: {
    enabled: true,
    maxTokens: 100000,
    summaryThreshold: 80000
  },
  
  temperature: 0.7,
  maxTokens: 4096
};
```

### Context and Memory Management

```javascript
// Example of accessing context in a plugin
export default async function contextAwareHook(context, next) {
  const { session, memory } = context;
  
  // Retrieve conversation history
  const history = await memory.getHistory(session.id, { limit: 10 });
  
  // Add context to current message
  context.enrichedContext = {
    previousTopics: extractTopics(history),
    userPreferences: await memory.get(`${session.id}:preferences`),
    conversationSummary: await memory.getSummary(session.id)
  };
  
  await next();
  
  // Store important information
  if (context.response.containsImportantInfo) {
    await memory.set(
      `${session.id}:important`,
      context.response.importantInfo,
      { ttl: 86400 * 30 } // 30 days
    );
  }
}
```

## Troubleshooting

### Gateway Won't Start

```bash
# Check if port is already in use
lsof -i :3000
netstat -an | grep 3000

# Check configuration syntax
openclaw validate-config

# View detailed logs
openclaw start --verbose

# Check database connection
openclaw db:check
```

### Channel Connection Issues

```javascript
// Enable debug logging for specific channel
// config/channels/telegram.js
export default {
  type: 'telegram',
  enabled: true,
  debug: true, // Enable detailed logging
  config: {
    token: process.env.TELEGRAM_BOT_TOKEN,
    polling: true
  }
};
```

### AI Provider Errors

```bash
# Test provider connection
openclaw test-provider anthropic

# Check API key validity
curl -H "x-api-key: ${ANTHROPIC_API_KEY}" \
  https://api.anthropic.com/v1/messages \
  -X POST -d '{"model":"claude-3-5-sonnet-20241022","max_tokens":10,"messages":[{"role":"user","content":"hi"}]}'

# Use fallback provider
# Edit config/ai.js to set fallbackStrategy: 'immediate'
```

### Memory/Performance Issues

```yaml
# Adjust memory settings in config/gateway.yml
memory:
  enabled: true
  maxTokens: 50000  # Reduce if hitting limits
  provider: redis   # Switch from sqlite for better performance
  redis:
    url: redis://localhost:6379

# Enable compression
compression:
  enabled: true
  algorithm: gzip

# Set rate limits
rateLimit:
  enabled: true
  windowMs: 60000
  maxRequests: 100
```

## Documentation Navigation

Access the full Chinese documentation at: [openclaw-docs.dx3n.cn](https://openclaw-docs.dx3n.cn)

Key sections:
- **Installation**: `/tutorials/installation/`
- **Gateway Config**: `/tutorials/gateway/`
- **Channel Setup**: `/tutorials/channels/`
- **AI Providers**: `/tutorials/providers/`
- **Plugin Development**: `/tutorials/plugins/`
- **Architecture Deep Dive**: `/beginner-openclaw-guide/`
- **AI Framework**: `/beginner-openclaw-framework-focus/`
- **Troubleshooting**: `/tutorials/help/`

## Resources

- Official OpenClaw: [github.com/openclaw](https://github.com/openclaw)
- English Documentation: [docs.openclaw.ai](https://docs.openclaw.ai)
- Chinese Documentation: [openclaw-docs.dx3n.cn](https://openclaw-docs.dx3n.cn)
- Documentation Repository: [github.com/yeuxuan/openclaw-docs](https://github.com/yeuxuan/openclaw-docs)
