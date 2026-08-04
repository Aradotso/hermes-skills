---
name: opik-openclaw-observability
description: Add Opik LLM observability and tracing to OpenClaw AI agents for monitoring prompts, responses, tool calls, and agent behavior.
triggers:
  - how do I add Opik tracing to OpenClaw
  - set up observability for my OpenClaw agent
  - monitor LLM costs and tokens in OpenClaw
  - trace OpenClaw agent tool calls
  - configure Opik plugin for OpenClaw
  - debug OpenClaw agent runs with Opik
  - track OpenClaw subagent spawning
  - export OpenClaw traces to Opik
---

# opik-openclaw-observability

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## What It Does

`@opik/opik-openclaw` is the official plugin that adds native Opik observability to OpenClaw AI agents. It automatically traces:

- LLM request/response spans with prompts, completions, and usage metadata
- Sub-agent spawning and lifecycle events
- Tool call spans with inputs, outputs, errors, and duration
- Run-level finalization metadata
- Cost and token usage tracking

All traces export to [Opik](https://github.com/comet-ml/opik), the open-source LLM observability platform by Comet, for visualization, debugging, and evaluation.

## Installation

### Prerequisites

- OpenClaw `>=2026.3.2`
- Node.js `>=22.12.0`
- npm `>=10`

### Install via OpenClaw CLI

**For OpenClaw >= 2026.3.23:**

```bash
openclaw plugins install clawhub:@opik/opik-openclaw
```

**For older versions:**

```bash
openclaw plugins install @opik/opik-openclaw
```

Restart the OpenClaw Gateway after installation.

## Configuration

### Interactive Setup

Run the configuration wizard to set up Opik credentials and endpoint:

```bash
openclaw opik configure
```

The wizard will:
- Prompt for Opik Cloud or self-hosted endpoint
- Validate API credentials
- Set project name and workspace
- Write config to `openclaw.json`

### Check Configuration

Verify your settings:

```bash
openclaw opik status
```

### Manual Configuration

Add to your `openclaw.json`:

```json
{
  "plugins": {
    "allow": ["opik-openclaw"],
    "entries": {
      "opik-openclaw": {
        "enabled": true,
        "hooks": {
          "allowConversationAccess": true
        },
        "config": {
          "enabled": true,
          "apiKey": "your-api-key-here",
          "apiUrl": "https://www.comet.com/opik/api",
          "projectName": "openclaw",
          "workspaceName": "default",
          "tags": ["openclaw", "production"],
          "toolResultPersistSanitizeEnabled": false,
          "staleTraceCleanupEnabled": true,
          "staleTraceTimeoutMs": 300000,
          "staleSweepIntervalMs": 60000,
          "flushRetryCount": 2,
          "flushRetryBaseDelayMs": 250
        }
      }
    }
  }
}
```

### Environment Variables

Use environment variables for sensitive credentials:

```bash
export OPIK_API_KEY="your-api-key"
export OPIK_URL_OVERRIDE="https://www.comet.com/opik/api"
export OPIK_PROJECT_NAME="my-openclaw-project"
export OPIK_WORKSPACE="my-team"
```

Environment variables take precedence over config file settings.

## Key CLI Commands

| Command | Description |
|---------|-------------|
| `openclaw plugins install @opik/opik-openclaw` | Install the plugin |
| `openclaw opik configure` | Interactive setup wizard |
| `openclaw opik status` | Display effective configuration |
| `openclaw plugins list` | List all installed plugins |
| `openclaw gateway run` | Start OpenClaw gateway with plugin active |
| `openclaw message send "text"` | Send test message to trace |

## Usage Patterns

### Basic Tracing

Once configured, tracing is automatic. Start the gateway and send messages:

```bash
# Start gateway with plugin active
openclaw gateway run

# In another terminal, send a message
openclaw message send "Analyze the current market trends"
```

Traces appear automatically in your Opik project dashboard.

### Tagged Runs

Add custom tags in configuration to organize traces:

```json
{
  "config": {
    "tags": ["production", "market-analysis", "v2.0"]
  }
}
```

### Self-Hosted Opik

Point to your self-hosted instance:

```json
{
  "config": {
    "apiUrl": "http://localhost:5000/api",
    "apiKey": null
  }
}
```

Self-hosted Opik typically doesn't require an API key.

### Multiple Projects

Switch projects per environment:

```bash
# Development
export OPIK_PROJECT_NAME="openclaw-dev"

# Staging
export OPIK_PROJECT_NAME="openclaw-staging"

# Production
export OPIK_PROJECT_NAME="openclaw-prod"
```

## Event Mapping

Understanding how OpenClaw events map to Opik traces:

| OpenClaw Event | Opik Entity | Data Captured |
|----------------|-------------|---------------|
| `llm_input` | Trace + LLM Span | Model name, prompt, parameters |
| `llm_output` | LLM Span End | Completion, tokens, cost, duration |
| `before_tool_call` | Tool Span Start | Tool name, input arguments |
| `after_tool_call` | Tool Span End | Output, error, duration |
| `subagent_spawning` | Subagent Span Start | Subagent metadata |
| `subagent_spawned` | Subagent Span Update | Run ID, initialization data |
| `subagent_ended` | Subagent Span End | Outcome, error if any |
| `agent_end` | Trace Finalize | Closes all pending spans |

## Configuration Options Reference

### Core Settings

```typescript
{
  enabled: boolean;              // Enable/disable plugin
  apiKey: string;                // Opik API key (or use OPIK_API_KEY env)
  apiUrl: string;                // Opik endpoint URL
  projectName: string;           // Opik project name
  workspaceName: string;         // Opik workspace name
  tags: string[];                // Custom tags for all traces
}
```

### Advanced Settings

```typescript
{
  toolResultPersistSanitizeEnabled: boolean;  // Rewrite local image refs in tool results
  staleTraceCleanupEnabled: boolean;          // Auto-cleanup orphaned traces
  staleTraceTimeoutMs: number;                // Trace staleness timeout (default: 300000)
  staleSweepIntervalMs: number;               // Cleanup check interval (default: 60000)
  flushRetryCount: number;                    // Retry failed trace uploads (default: 2)
  flushRetryBaseDelayMs: number;              // Base retry delay (default: 250)
}
```

## Security & Trust

### Plugin Allowlist

OpenClaw requires explicit trust for community plugins:

```json
{
  "plugins": {
    "allow": ["opik-openclaw"]
  }
}
```

### Conversation Hook Access

The plugin needs conversation access to trace LLM interactions:

```json
{
  "plugins": {
    "entries": {
      "opik-openclaw": {
        "hooks": {
          "allowConversationAccess": true
        }
      }
    }
  }
}
```

## Troubleshooting

### Traces Not Appearing

1. **Check plugin status:**
   ```bash
   openclaw opik status
   ```

2. **Verify API key:**
   ```bash
   echo $OPIK_API_KEY
   ```

3. **Check gateway logs:**
   Look for Opik plugin initialization and trace flush messages.

4. **Confirm plugin is enabled:**
   ```json
   {
     "config": {
       "enabled": true
     }
   }
   ```

### Authentication Errors

- For Opik Cloud, ensure you have a valid API key from [comet.com](https://www.comet.com/)
- For self-hosted, verify `apiUrl` points to your instance
- Check that `OPIK_API_KEY` or config `apiKey` is set correctly

### Stale Traces

If traces accumulate without finishing:

```json
{
  "config": {
    "staleTraceCleanupEnabled": true,
    "staleTraceTimeoutMs": 300000,
    "staleSweepIntervalMs": 60000
  }
}
```

This auto-closes traces older than 5 minutes.

### Plugin Not Loading

1. **Reinstall the plugin:**
   ```bash
   openclaw plugins uninstall @opik/opik-openclaw
   openclaw plugins install @opik/opik-openclaw
   ```

2. **Restart gateway:**
   ```bash
   # Stop gateway (Ctrl+C)
   openclaw gateway run
   ```

3. **Check OpenClaw version:**
   ```bash
   openclaw --version
   # Should be >= 2026.3.2
   ```

### Remote Gateway

If running OpenClaw Gateway on a remote host:

1. Install the plugin on the gateway host, not the client
2. Configure credentials on the gateway host
3. Restart the remote gateway process

## Development & Testing

### Local Development

```bash
# Clone and install
git clone https://github.com/comet-ml/opik-openclaw
cd opik-openclaw
npm ci

# Build
npm run build

# Run tests
npm run test

# Type check
npm run typecheck

# Lint
npm run lint
```

### Live End-to-End Test

Test against a real OpenClaw gateway:

```bash
# Set required environment variables
export OPIK_API_KEY="your-key"
export OPENAI_API_KEY="your-openai-key"

# Optional overrides
export OPENCLAW_LIVE_MODEL="gpt-4o"
export OPENCLAW_LIVE_OPENCLAW_VERSION="latest"

# Run live test
npm run test:live
```

The live test:
- Creates isolated OpenClaw home in `.artifacts/live-e2e/<run-id>/`
- Installs current plugin build
- Starts gateway and sends test message
- Verifies trace upload to Opik

### Package Verification

```bash
# Check package contents before publish
npm run pack:check
```

## Real-World Examples

### Production Agent with Cost Tracking

```json
{
  "plugins": {
    "allow": ["opik-openclaw"],
    "entries": {
      "opik-openclaw": {
        "enabled": true,
        "hooks": {
          "allowConversationAccess": true
        },
        "config": {
          "enabled": true,
          "apiKey": "${OPIK_API_KEY}",
          "apiUrl": "https://www.comet.com/opik/api",
          "projectName": "customer-support-agent",
          "workspaceName": "production",
          "tags": ["production", "customer-support", "v1.5"]
        }
      }
    }
  }
}
```

### Development with Self-Hosted Opik

```json
{
  "plugins": {
    "allow": ["opik-openclaw"],
    "entries": {
      "opik-openclaw": {
        "enabled": true,
        "hooks": {
          "allowConversationAccess": true
        },
        "config": {
          "enabled": true,
          "apiUrl": "http://localhost:5000/api",
          "projectName": "dev-experiments",
          "workspaceName": "engineering",
          "tags": ["dev", "local"]
        }
      }
    }
  }
}
```

### Multi-Environment Setup Script

```bash
#!/bin/bash

ENV=${1:-dev}

case $ENV in
  dev)
    export OPIK_PROJECT_NAME="openclaw-dev"
    export OPIK_WORKSPACE="engineering"
    ;;
  staging)
    export OPIK_PROJECT_NAME="openclaw-staging"
    export OPIK_WORKSPACE="qa"
    ;;
  prod)
    export OPIK_PROJECT_NAME="openclaw-prod"
    export OPIK_WORKSPACE="production"
    ;;
esac

openclaw gateway run
```

## Best Practices

1. **Use environment variables** for API keys in production
2. **Tag traces** by environment, version, or feature
3. **Enable stale cleanup** to prevent memory leaks from orphaned traces
4. **Set up separate projects** for dev, staging, and production
5. **Review cost metrics** regularly in Opik dashboard
6. **Monitor error spans** to catch tool failures early
7. **Use allowlist** to control plugin trust explicitly

## Additional Resources

- [Opik Documentation](https://www.comet.com/docs/opik/)
- [OpenClaw Documentation](https://github.com/openclaw/openclaw)
- [Plugin Repository](https://github.com/comet-ml/opik-openclaw)
- [Contributing Guide](https://github.com/comet-ml/opik-openclaw/blob/main/CONTRIBUTING.md)
