---
name: openclaw-nerve-cockpit
description: Real-time web cockpit for OpenClaw providing voice conversations, agent fleet control, kanban boards, workspace management, and usage visibility
triggers:
  - set up nerve for openclaw
  - add voice control to my openclaw agent
  - create an openclaw dashboard
  - manage multiple openclaw agents with nerve
  - configure nerve kanban board
  - integrate nerve with openclaw gateway
  - troubleshoot nerve installation
  - add charts to openclaw responses
---

# OpenClaw Nerve Cockpit

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

Nerve is a real-time web cockpit for OpenClaw that transforms agent interaction from chat-only to full operational control. It provides voice conversations, multi-agent fleet management, automated kanban boards, workspace/file control, sub-agent sessions, inline charts, and comprehensive usage visibility.

## What Nerve Does

Nerve sits between your browser and the OpenClaw Gateway, providing:

- **Fleet Control**: Manage multiple agents from one interface, each with its own workspace, memory, identity, and skills
- **Voice Interface**: Push-to-talk, wake word, local Whisper transcription, multilingual commands, multiple TTS providers
- **Full Context**: Live workspace browser, tabbed editor, memory editing, config editing, skills inspection
- **Operations Layer**: Session trees, cron scheduling, kanban task boards, review flows, proposal inbox, model overrides
- **Rich Output**: Charts, diffs, previews, syntax-highlighted code, structured tool rendering, streaming UI
- **Observability**: Token usage, cost tracking, context meter, agent logs, event logs

## Architecture

```text
Browser ─── Nerve (:3080) ─── OpenClaw Gateway (:18789)
 │           │
 ├─ WS ──────┤ proxied to gateway
 ├─ SSE ─────┤ file watchers, real-time sync
 └─ REST ────┘ files, memories, TTS, models
```

Frontend: React 19, Tailwind CSS 4, shadcn/ui, Vite 7  
Backend: Hono 4 on Node.js 22+

## Installation

### Quick Install (Recommended)

```bash
curl -fsSL https://raw.githubusercontent.com/daggerhashimoto/openclaw-nerve/master/install.sh | bash
```

The installer handles dependencies, clone, build, and launches the setup wizard with guided access modes (localhost, LAN, Tailscale tailnet IP, Tailscale Serve).

### Manual Install

```bash
git clone https://github.com/daggerhashimoto/openclaw-nerve.git
cd openclaw-nerve
npm install
npm run setup
npm run prod
```

### Install from Next Branch (Latest Features)

```bash
curl -fsSL https://raw.githubusercontent.com/daggerhashimoto/openclaw-nerve/master/install.sh | bash -s -- --branch next
```

Or switch existing install:

```bash
cd ~/nerve
git fetch origin
git switch next || git switch -c next --track origin/next
git pull --ff-only
npm install
npm run build
npm run prod
```

## Key Commands

```bash
# Development
npm run dev                    # Start frontend (Vite on :3080)
PORT=3081 npm run dev:server   # Start backend (explicit split-port dev)

# Production
npm run prod                   # Build and run production server

# Management
npm run setup                  # Interactive setup wizard
npm run update -- --yes        # Auto-update with rollback on failure
npm run build                  # Build frontend and backend

# Utilities
npm run lint                   # Lint TypeScript files
npm run type-check             # TypeScript type checking
```

## Configuration

Nerve uses a `.env` file in the project root. Key variables:

### Core Settings

```bash
# Server binding
HOST=127.0.0.1                 # Bind to localhost (default)
# HOST=0.0.0.0                 # Bind to all interfaces (enables network access)
PORT=3080                      # Server port

# OpenClaw Gateway
GATEWAY_URL=http://localhost:18789
GATEWAY_TOKEN=                 # Optional: if gateway requires auth

# Security (required when HOST=0.0.0.0)
PASSWORD_HASH=                 # bcrypt hash of UI password
SESSION_SECRET=                # Random string for session signing (32+ chars)
TRUSTED_CONNECTION=false       # Set true for server-side gateway token injection
```

### Voice Settings

```bash
# Voice input
WHISPER_MODEL_PATH=/path/to/whisper/model  # Local Whisper model
WAKE_WORD=nerve                            # Wake word for voice activation
LANGUAGE=en                                # Transcription language

# TTS Provider (choose one)
TTS_PROVIDER=openai            # Options: openai, elevenlabs, edge
OPENAI_API_KEY=                # Required if TTS_PROVIDER=openai
ELEVENLABS_API_KEY=            # Required if TTS_PROVIDER=elevenlabs
TTS_VOICE=alloy                # Voice ID for chosen provider
```

### Advanced Settings

```bash
# Multi-agent setup
AGENT_CONFIG_DIR=/path/to/agents  # Directory with per-agent configs

# Performance
WS_PING_INTERVAL=30000         # WebSocket ping interval (ms)
FILE_WATCH_DEBOUNCE=300        # File watcher debounce (ms)

# Logging
LOG_LEVEL=info                 # Options: debug, info, warn, error
```

### Generating PASSWORD_HASH

```typescript
// scripts/hash-password.ts
import bcrypt from 'bcrypt';

const password = process.argv[2];
if (!password) {
  console.error('Usage: tsx scripts/hash-password.ts <password>');
  process.exit(1);
}

const hash = await bcrypt.hash(password, 10);
console.log(hash);
```

Run:
```bash
npx tsx scripts/hash-password.ts mypassword
```

## Deployment Modes

### Local (Default)
Run Nerve and Gateway on the same machine. Best for reliability and simplicity.

```bash
HOST=127.0.0.1
PORT=3080
GATEWAY_URL=http://localhost:18789
```

### LAN Access
Expose Nerve on your local network.

```bash
HOST=0.0.0.0
PORT=3080
PASSWORD_HASH=<bcrypt_hash>
SESSION_SECRET=<random_32_char_string>
GATEWAY_URL=http://localhost:18789
```

### Tailscale Access
Expose via Tailscale tailnet IP or Tailscale Serve.

```bash
# Via tailnet IP
HOST=0.0.0.0
PORT=3080
PASSWORD_HASH=<bcrypt_hash>
SESSION_SECRET=<random_32_char_string>

# Via Tailscale Serve (alternative)
HOST=127.0.0.1
PORT=3080
# Run: tailscale serve https / http://127.0.0.1:3080
```

### Hybrid
Nerve local, Gateway in cloud.

```bash
HOST=127.0.0.1
PORT=3080
GATEWAY_URL=https://your-gateway.example.com
GATEWAY_TOKEN=<gateway_token>
```

### Full Cloud
Both Nerve and Gateway in cloud.

```bash
HOST=0.0.0.0
PORT=3080
PASSWORD_HASH=<bcrypt_hash>
SESSION_SECRET=<random_32_char_string>
GATEWAY_URL=https://your-gateway.example.com
GATEWAY_TOKEN=<gateway_token>
```

## Agent Markers for Rich Output

Nerve recognizes special markers in agent responses to render rich UI components.

### Charts

```markdown
{{CHART_START}}
{
  "type": "line",
  "data": {
    "labels": ["Jan", "Feb", "Mar", "Apr"],
    "datasets": [{
      "label": "Revenue",
      "data": [12000, 19000, 15000, 25000]
    }]
  }
}
{{CHART_END}}
```

Supported chart types: `line`, `bar`, `pie`, `doughnut`, `radar`, `scatter`

### Kanban Tasks

```markdown
{{KANBAN_START}}
{
  "title": "Implement user authentication",
  "description": "Add JWT-based auth to API endpoints",
  "priority": "high",
  "tags": ["security", "backend"],
  "assignee": "agent-1"
}
{{KANBAN_END}}
```

### TTS Control

```markdown
{{TTS_START}}
This text will be read aloud using the configured TTS provider.
{{TTS_END}}
```

### Code Diffs

```diff
{{DIFF_START}}
- const old = "previous version";
+ const updated = "new version";
{{DIFF_END}}
```

## Code Examples

### Creating a Custom Backend Route

```typescript
// server/routes/custom.ts
import { Hono } from 'hono';
import type { AppContext } from '../types';

const custom = new Hono<AppContext>();

// Protected route (requires auth when HOST=0.0.0.0)
custom.get('/agent-status', async (c) => {
  const gatewayUrl = c.env.GATEWAY_URL;
  const token = c.env.GATEWAY_TOKEN;
  
  const response = await fetch(`${gatewayUrl}/api/agents`, {
    headers: token ? { Authorization: `Bearer ${token}` } : {},
  });
  
  const agents = await response.json();
  
  return c.json({
    total: agents.length,
    active: agents.filter(a => a.status === 'active').length,
    agents,
  });
});

export default custom;
```

Register in `server/index.ts`:

```typescript
import custom from './routes/custom';

app.route('/api/custom', custom);
```

### Adding a Frontend Component

```typescript
// src/components/AgentStatusCard.tsx
import { useEffect, useState } from 'react';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';

interface AgentStatus {
  total: number;
  active: number;
}

export function AgentStatusCard() {
  const [status, setStatus] = useState<AgentStatus | null>(null);

  useEffect(() => {
    const fetchStatus = async () => {
      const response = await fetch('/api/custom/agent-status');
      const data = await response.json();
      setStatus(data);
    };

    fetchStatus();
    const interval = setInterval(fetchStatus, 5000);
    return () => clearInterval(interval);
  }, []);

  if (!status) return <div>Loading...</div>;

  return (
    <Card>
      <CardHeader>
        <CardTitle>Agent Fleet Status</CardTitle>
      </CardHeader>
      <CardContent>
        <div className="text-2xl font-bold">{status.active}/{status.total}</div>
        <p className="text-sm text-muted-foreground">Active agents</p>
      </CardContent>
    </Card>
  );
}
```

### WebSocket Event Handling

```typescript
// src/hooks/useGatewayWebSocket.ts
import { useEffect, useRef, useState } from 'react';

interface GatewayMessage {
  type: string;
  payload: unknown;
}

export function useGatewayWebSocket(agentId: string) {
  const [messages, setMessages] = useState<GatewayMessage[]>([]);
  const wsRef = useRef<WebSocket | null>(null);

  useEffect(() => {
    const protocol = window.location.protocol === 'https:' ? 'wss:' : 'ws:';
    const ws = new WebSocket(`${protocol}//${window.location.host}/ws/gateway`);

    ws.onopen = () => {
      ws.send(JSON.stringify({ type: 'subscribe', agentId }));
    };

    ws.onmessage = (event) => {
      const message = JSON.parse(event.data);
      setMessages((prev) => [...prev, message]);
    };

    ws.onerror = (error) => {
      console.error('WebSocket error:', error);
    };

    wsRef.current = ws;

    return () => {
      ws.close();
    };
  }, [agentId]);

  return { messages, ws: wsRef.current };
}
```

### Server-Side Event Streaming

```typescript
// server/routes/events.ts
import { Hono } from 'hono';
import { streamSSE } from 'hono/streaming';
import type { AppContext } from '../types';

const events = new Hono<AppContext>();

events.get('/file-changes', async (c) => {
  return streamSSE(c, async (stream) => {
    const watcher = watchWorkspace(c.env.WORKSPACE_PATH);
    
    watcher.on('change', (file) => {
      stream.writeSSE({
        data: JSON.stringify({ type: 'file_changed', file }),
      });
    });

    // Keep connection alive
    const ping = setInterval(() => {
      stream.writeSSE({ data: 'ping' });
    }, 30000);

    // Cleanup on disconnect
    stream.onAbort(() => {
      clearInterval(ping);
      watcher.close();
    });
  });
});

export default events;
```

### Custom TTS Provider Integration

```typescript
// server/services/tts/custom-provider.ts
import type { TTSProvider, TTSOptions } from './types';

export class CustomTTSProvider implements TTSProvider {
  private apiKey: string;
  private endpoint: string;

  constructor(apiKey: string, endpoint: string) {
    this.apiKey = apiKey;
    this.endpoint = endpoint;
  }

  async synthesize(text: string, options: TTSOptions): Promise<Buffer> {
    const response = await fetch(this.endpoint, {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${this.apiKey}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        text,
        voice: options.voice || 'default',
        language: options.language || 'en',
      }),
    });

    if (!response.ok) {
      throw new Error(`TTS failed: ${response.statusText}`);
    }

    return Buffer.from(await response.arrayBuffer());
  }

  getSupportedVoices(): string[] {
    return ['voice-1', 'voice-2', 'voice-3'];
  }
}
```

## Common Patterns

### Multi-Agent Setup

Create agent-specific config files:

```bash
mkdir -p ~/.config/nerve/agents
```

```json
// ~/.config/nerve/agents/researcher.json
{
  "id": "researcher",
  "name": "Research Agent",
  "workspace": "/path/to/research-workspace",
  "memory": "/path/to/research-memory.json",
  "identity": "You are a research specialist focused on gathering and analyzing information.",
  "skills": ["web-search", "data-analysis", "summarization"],
  "model": "claude-3-5-sonnet-20241022"
}
```

```bash
# In .env
AGENT_CONFIG_DIR=/home/user/.config/nerve/agents
```

### Cron Task Scheduling

```typescript
// Add to agent identity or system message
"You can schedule recurring tasks using cron syntax. Example:
{{CRON_START}}
{
  \"schedule\": \"0 9 * * *\",
  \"task\": \"Generate daily report\",
  \"agent\": \"researcher\"
}
{{CRON_END}}"
```

### Review Flow Integration

```typescript
// Agent proposes changes for review
"{{PROPOSAL_START}}
{
  \"type\": \"code_change\",
  \"file\": \"src/app.ts\",
  \"description\": \"Refactor authentication logic\",
  \"diff\": \"...\"
}
{{PROPOSAL_END}}"
```

### Context Pressure Monitoring

```typescript
// src/hooks/useContextMeter.ts
export function useContextMeter(sessionId: string) {
  const [context, setContext] = useState({
    used: 0,
    limit: 200000,
    percentage: 0,
  });

  useEffect(() => {
    const checkContext = async () => {
      const response = await fetch(`/api/sessions/${sessionId}/context`);
      const data = await response.json();
      setContext({
        used: data.tokens,
        limit: data.limit,
        percentage: (data.tokens / data.limit) * 100,
      });
    };

    checkContext();
    const interval = setInterval(checkContext, 10000);
    return () => clearInterval(interval);
  }, [sessionId]);

  return context;
}
```

## Troubleshooting

### Gateway Connection Issues

```bash
# Check gateway is running
curl http://localhost:18789/health

# Verify Nerve can reach gateway
# Check GATEWAY_URL in .env
# If using GATEWAY_TOKEN, verify it's correct
```

### WebSocket Connection Fails

```typescript
// Check browser console for:
// - CORS errors (ensure gateway allows origin)
// - Authentication failures (check PASSWORD_HASH)
// - Network tab shows 101 Switching Protocols

// Server-side debugging:
// Set LOG_LEVEL=debug in .env
// Check server/logs/websocket.log
```

### Voice Transcription Not Working

```bash
# Verify Whisper model path
ls -la $WHISPER_MODEL_PATH

# Check microphone permissions in browser
# Chrome: Settings > Privacy and security > Site Settings > Microphone

# Test with different model sizes
# WHISPER_MODEL_PATH=/path/to/ggml-base.en.bin  # Faster
# WHISPER_MODEL_PATH=/path/to/ggml-large-v3.bin # More accurate
```

### File Watcher Not Updating

```bash
# Increase OS file watch limit (Linux)
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

# Adjust debounce in .env
FILE_WATCH_DEBOUNCE=500  # Increase if updates too frequent
```

### Authentication Failures (HOST=0.0.0.0)

```bash
# Regenerate password hash
npx tsx scripts/hash-password.ts newpassword

# Update .env with new hash
PASSWORD_HASH=<new_hash>

# Ensure SESSION_SECRET is set and >= 32 chars
SESSION_SECRET=$(openssl rand -base64 32)
```

### Update Fails to Roll Back

```bash
# Manual rollback
cd ~/nerve
git reflog  # Find previous commit
git reset --hard HEAD@{1}
npm install
npm run build
npm run prod
```

### Port Already in Use

```bash
# Find process using port
lsof -i :3080
# or
netstat -tuln | grep 3080

# Kill process
kill -9 <PID>

# Or use different port
PORT=3081 npm run prod
```

## Resources

- **Documentation**: [docs/](https://github.com/daggerhashimoto/openclaw-nerve/tree/master/docs)
- **Architecture**: [docs/ARCHITECTURE.md](https://github.com/daggerhashimoto/openclaw-nerve/blob/master/docs/ARCHITECTURE.md)
- **Security**: [docs/SECURITY.md](https://github.com/daggerhashimoto/openclaw-nerve/blob/master/docs/SECURITY.md)
- **Discord**: [discord.gg/Sh9ZGtctva](https://discord.gg/Sh9ZGtctva)
- **Homepage**: [nerve.zone](https://nerve.zone)
