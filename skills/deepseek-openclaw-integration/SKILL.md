---
name: deepseek-openclaw-integration
description: Generate OpenClaw configuration and onboarding commands for DeepSeek AI models with a local-first, secret-safe UI.
triggers:
  - how do I set up DeepSeek with OpenClaw
  - generate OpenClaw command for DeepSeek
  - configure DeepSeek models in OpenClaw
  - create DeepSeek OpenClaw integration
  - what DeepSeek models work with OpenClaw
  - show me DeepSeek OpenClaw config
  - how to use DeepSeek reasoner with OpenClaw
  - set up DeepSeek API key for OpenClaw
---

# DeepSeek OpenClaw Integration

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

**DeepSeek OpenClaw** is a local-first web application that helps developers integrate DeepSeek AI models with OpenClaw agent framework. It generates copy-ready onboarding commands and configuration snippets without sending API keys to any backend, ensuring secure local-only credential handling.

## What It Does

- **Model Catalog**: Browse DeepSeek models compatible with OpenClaw (v4-flash, v4-pro, chat, reasoner)
- **Command Generator**: Create OpenClaw onboarding commands with proper DeepSeek provider syntax
- **Config Snippets**: Generate redacted OpenClaw configuration JSON for DeepSeek integration
- **Secret-Safe UI**: All credential handling happens client-side; secrets never leave your browser

## Installation

### Clone and Run Locally

```bash
git clone https://github.com/MageExemplify/deepseek-openclaw-648.git
cd deepseek-openclaw-648
npm install
npm start
```

### Development Mode

```bash
npm run dev
```

### Production Build

```bash
npm run build
npm run preview
```

## Project Structure

```
src/
├── App.tsx           # Main React component
├── main.tsx          # Entry point
├── models.ts         # DeepSeek model definitions
├── openclaw.ts       # OpenClaw command/config generators
├── types.ts          # TypeScript interfaces
└── styles.css        # UI styles
```

## Key Components

### Model Definitions (`models.ts`)

The app includes pre-configured DeepSeek models for OpenClaw:

```typescript
export interface DeepSeekModel {
  id: string;
  name: string;
  description: string;
  useCase: string;
}

export const DEEPSEEK_MODELS: DeepSeekModel[] = [
  {
    id: "deepseek/deepseek-v4-flash",
    name: "DeepSeek v4 Flash",
    description: "Fast, efficient model for everyday automation",
    useCase: "Quick responses, general tasks"
  },
  {
    id: "deepseek/deepseek-v4-pro",
    name: "DeepSeek v4 Pro",
    description: "Advanced model for complex coding and planning",
    useCase: "Complex reasoning, code generation"
  },
  {
    id: "deepseek/deepseek-chat",
    name: "DeepSeek Chat",
    description: "General-purpose conversational model",
    useCase: "Standard chat interactions"
  },
  {
    id: "deepseek/deepseek-reasoner",
    name: "DeepSeek Reasoner",
    description: "Reasoning-focused model for deep analysis",
    useCase: "Multi-step reasoning, analysis"
  }
];
```

### OpenClaw Command Generator (`openclaw.ts`)

```typescript
export function generateOnboardingCommand(
  modelId: string,
  apiKey?: string
): string {
  const keyVar = apiKey ? apiKey : "${DEEPSEEK_API_KEY}";
  
  return `openclaw onboard \\
  --provider deepseek \\
  --model ${modelId} \\
  --api-key ${keyVar}`;
}

export function generateConfigSnippet(
  modelId: string,
  apiKey?: string
): string {
  const redactedKey = apiKey ? "sk-***redacted***" : "${DEEPSEEK_API_KEY}";
  
  return JSON.stringify({
    env: {
      DEEPSEEK_API_KEY: redactedKey
    },
    agents: {
      defaults: {
        model: {
          primary: modelId
        }
      }
    }
  }, null, 2);
}
```

### Main App Component (`App.tsx`)

```typescript
import React, { useState } from 'react';
import { DEEPSEEK_MODELS } from './models';
import { generateOnboardingCommand, generateConfigSnippet } from './openclaw';

export default function App() {
  const [selectedModel, setSelectedModel] = useState(DEEPSEEK_MODELS[0].id);
  const [apiKey, setApiKey] = useState('');
  const [showKey, setShowKey] = useState(false);

  const command = generateOnboardingCommand(selectedModel, apiKey);
  const config = generateConfigSnippet(selectedModel, apiKey);

  return (
    <div className="container">
      <header>
        <h1>DeepSeek OpenClaw</h1>
        <p>Configure DeepSeek models for OpenClaw integration</p>
      </header>

      <section className="model-selector">
        <h2>Select Model</h2>
        {DEEPSEEK_MODELS.map(model => (
          <label key={model.id}>
            <input
              type="radio"
              value={model.id}
              checked={selectedModel === model.id}
              onChange={(e) => setSelectedModel(e.target.value)}
            />
            <strong>{model.name}</strong>
            <span>{model.description}</span>
          </label>
        ))}
      </section>

      <section className="api-key-input">
        <h2>DeepSeek API Key (Optional)</h2>
        <input
          type={showKey ? "text" : "password"}
          placeholder="sk-..."
          value={apiKey}
          onChange={(e) => setApiKey(e.target.value)}
        />
        <button onClick={() => setShowKey(!showKey)}>
          {showKey ? "Hide" : "Show"}
        </button>
      </section>

      <section className="output">
        <h2>Onboarding Command</h2>
        <pre>{command}</pre>
        
        <h2>OpenClaw Config</h2>
        <pre>{config}</pre>
      </section>
    </div>
  );
}
```

## OpenClaw Integration Patterns

### Environment Variable Setup

Always use environment variables for API keys:

```bash
export DEEPSEEK_API_KEY="sk-your-actual-key-here"
```

### Manual OpenClaw Configuration

Create or update `~/.openclaw/config.json`:

```json
{
  "env": {
    "DEEPSEEK_API_KEY": "${DEEPSEEK_API_KEY}"
  },
  "providers": {
    "deepseek": {
      "apiKey": "${DEEPSEEK_API_KEY}"
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "deepseek/deepseek-v4-flash",
        "fallback": "deepseek/deepseek-chat"
      }
    },
    "reasoning-agent": {
      "model": {
        "primary": "deepseek/deepseek-reasoner"
      }
    },
    "code-agent": {
      "model": {
        "primary": "deepseek/deepseek-v4-pro"
      }
    }
  }
}
```

### Onboarding Command Examples

**Quick setup with Flash model:**
```bash
openclaw onboard \
  --provider deepseek \
  --model deepseek/deepseek-v4-flash \
  --api-key ${DEEPSEEK_API_KEY}
```

**Complex reasoning setup:**
```bash
openclaw onboard \
  --provider deepseek \
  --model deepseek/deepseek-reasoner \
  --api-key ${DEEPSEEK_API_KEY} \
  --agent reasoning-assistant
```

**Multi-agent configuration:**
```bash
# Fast agent
openclaw agent create fast-agent \
  --model deepseek/deepseek-v4-flash

# Pro agent
openclaw agent create code-agent \
  --model deepseek/deepseek-v4-pro
```

## Model Selection Guide

| Model | Best For | Speed | Reasoning Depth |
|-------|----------|-------|-----------------|
| `deepseek-v4-flash` | Quick responses, automation | ⚡⚡⚡ | ⭐⭐ |
| `deepseek-v4-pro` | Complex code, planning | ⚡⚡ | ⭐⭐⭐⭐ |
| `deepseek-chat` | General conversation | ⚡⚡⚡ | ⭐⭐⭐ |
| `deepseek-reasoner` | Deep analysis, multi-step | ⚡ | ⭐⭐⭐⭐⭐ |

## Security Best Practices

### Never Hardcode Keys

❌ **Wrong:**
```typescript
const apiKey = "sk-abc123def456";
```

✅ **Correct:**
```typescript
const apiKey = process.env.DEEPSEEK_API_KEY;
```

### Use .env Files

Create `.env` (never commit this):
```bash
DEEPSEEK_API_KEY=sk-your-actual-key
```

Load in your app:
```typescript
import dotenv from 'dotenv';
dotenv.config();

const apiKey = process.env.DEEPSEEK_API_KEY;
```

### Redact in Logs

```typescript
function safeLog(apiKey: string) {
  const redacted = apiKey 
    ? `${apiKey.slice(0, 7)}***${apiKey.slice(-4)}`
    : 'not-set';
  console.log(`API Key: ${redacted}`);
}
```

## Common Workflows

### 1. First-Time Setup

```bash
# Clone and install
git clone https://github.com/MageExemplify/deepseek-openclaw-648.git
cd deepseek-openclaw-648
npm install

# Set environment variable
export DEEPSEEK_API_KEY="sk-your-key"

# Run the app
npm start

# Open browser to http://localhost:5173
# Select model, copy generated command
# Run command in terminal
```

### 2. Switching Models

```bash
# Update OpenClaw config
openclaw config set agents.defaults.model.primary deepseek/deepseek-v4-pro

# Verify
openclaw config get agents.defaults.model.primary
```

### 3. Testing Integration

```bash
# Test connection
openclaw test --provider deepseek

# Run a simple query
openclaw chat "Hello, test DeepSeek integration"
```

## Troubleshooting

### "Provider not found" Error

**Cause:** OpenClaw doesn't recognize the DeepSeek provider.

**Solution:**
```bash
# Ensure OpenClaw is up to date
openclaw --version

# Reinstall provider
openclaw provider install deepseek

# Verify installation
openclaw provider list | grep deepseek
```

### "Invalid API Key" Error

**Cause:** API key not set or incorrect.

**Solution:**
```bash
# Check if variable is set
echo $DEEPSEEK_API_KEY

# Re-export correctly
export DEEPSEEK_API_KEY="sk-your-actual-key"

# Test directly
curl https://api.deepseek.com/v1/models \
  -H "Authorization: Bearer $DEEPSEEK_API_KEY"
```

### Model Not Available

**Cause:** Selected model ID doesn't match DeepSeek's current offerings.

**Solution:**
- Check [DeepSeek API docs](https://api-docs.deepseek.com) for current model names
- Update `models.ts` with correct IDs
- Use `deepseek/deepseek-chat` as fallback

### Build Errors

**Cause:** Missing dependencies or TypeScript issues.

**Solution:**
```bash
# Clean install
rm -rf node_modules package-lock.json
npm install

# Check TypeScript
npm run typecheck

# Rebuild
npm run build
```

## Advanced Configuration

### Custom Agent Profiles

```json
{
  "agents": {
    "quick-assistant": {
      "model": {
        "primary": "deepseek/deepseek-v4-flash"
      },
      "temperature": 0.7,
      "maxTokens": 2000
    },
    "deep-thinker": {
      "model": {
        "primary": "deepseek/deepseek-reasoner"
      },
      "temperature": 0.3,
      "maxTokens": 8000,
      "reasoning": {
        "enabled": true,
        "maxSteps": 10
      }
    }
  }
}
```

### Rate Limiting

```json
{
  "providers": {
    "deepseek": {
      "rateLimit": {
        "requests": 100,
        "period": "1m"
      },
      "retry": {
        "attempts": 3,
        "backoff": "exponential"
      }
    }
  }
}
```

## References

- [OpenClaw Documentation](https://docs.openclaw.ai/)
- [OpenClaw DeepSeek Provider](https://docs.openclaw.ai/providers/deepseek)
- [DeepSeek API Docs](https://api-docs.deepseek.com/)
- [DeepSeek OpenClaw Integration](https://api-docs.deepseek.com/quick_start/agent_integrations/openclaw)

## Contributing

The project uses:
- **Vite** for bundling
- **React 18** for UI
- **TypeScript** for type safety
- **GitHub Actions** for CI/CD

To contribute:
```bash
# Fork and clone
git clone https://github.com/YOUR_USERNAME/deepseek-openclaw-648.git

# Create feature branch
git checkout -b feature/my-improvement

# Make changes, test locally
npm run dev

# Submit PR
```

---

**License:** MIT
