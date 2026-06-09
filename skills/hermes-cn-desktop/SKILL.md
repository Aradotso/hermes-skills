---
name: hermes-cn-desktop
description: Hermes Agent CN Desktop - Windows-first AI agent desktop app built with Tauri, TypeScript, and Rust
triggers:
  - build hermes agent desktop app
  - develop tauri desktop application
  - integrate hermes agent core
  - setup hermes cn desktop dev environment
  - create hermes agent ui components
  - manage hermes runtime installation
  - debug tauri rust commands
  - implement hermes agent features
---

# Hermes CN Desktop Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

Hermes Agent CN Desktop is a native desktop client for Hermes Agent, built with Tauri v2, Rust, React, and TypeScript. It provides a Windows-first, cross-platform desktop experience with embedded Hermes Agent core runtime, supporting local and cloud LLM providers with Chinese ecosystem integration.

## What This Project Does

Hermes CN Desktop wraps the Hermes Agent dashboard in a native desktop application that:

- Manages local Hermes Agent runtime (install, update, health checks, rollback)
- Provides native file dialogs, window management, and system integration
- Proxies REST/SSE requests through Rust to bypass CORS in production mode
- Offers complete Agent UI: chat, streaming, attachments, MCP tools, Skills, Memory
- Supports Chinese LLM providers (Zhipu, Baichuan, DeepSeek, etc.) and local models (Ollama, vLLM)
- Includes built-in runtime diagnostics and health monitoring

## Installation & Setup

### Prerequisites

```bash
# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Install Node.js 20+ and pnpm 9+
npm install -g pnpm

# macOS only: Install Xcode Command Line Tools
xcode-select --install
```

### Clone and Install

```bash
git clone https://github.com/Eynzof/Hermes-CN-Desktop.git
cd Hermes-CN-Desktop
pnpm install
```

### Development Setup

You need a running Hermes Dashboard. Either:

**Option A: Use Hermes-CN-Core**
```bash
# Clone and run Hermes-CN-Core in parallel
git clone https://github.com/Eynzof/Hermes-CN-Core.git
cd Hermes-CN-Core
hermes dashboard --host 127.0.0.1 --port 9120 --no-open
```

**Option B: Use local Hermes CLI**
```bash
hermes dashboard --host 127.0.0.1 --port 9120 --no-open
```

Start desktop dev mode:
```bash
# Terminal 1: Start Vite dev server
pnpm web:dev

# Terminal 2: Run Tauri app
cargo run

# Or use combined command
pnpm tauri:dev
```

## Key Commands

### Development

| Command | Description |
|---------|-------------|
| `pnpm web:dev` | Start Vite dev server (port 9545) |
| `cargo run` | Compile and run Tauri desktop window |
| `pnpm tauri:dev` | Combined Tauri dev mode (auto-starts Vite) |
| `pnpm typecheck` | Run TypeScript type checking |
| `pnpm test:unit` | Run Vitest unit tests |
| `cargo check` | Run Rust compile check |
| `cargo test --all-features` | Run Rust tests |

### Building

```bash
# Production build for current platform
pnpm tauri:build

# Debug build with symbols
pnpm tauri:build:debug
```

Build artifacts:
- Production: `target/release/bundle/`
- Debug: `target/debug/bundle/`

### Quality Checks

```bash
# Frontend checks
pnpm typecheck
pnpm test:unit

# Rust checks
cargo fmt --all -- --check
cargo clippy --all-targets -- -D warnings
cargo test --all-features --no-fail-fast
```

## Project Structure

```
hermes-cn-desktop/
├── src/                    # Rust backend (Tauri commands, process mgmt)
│   ├── main.rs            # Entry point
│   ├── commands/          # Tauri IPC commands
│   ├── runtime/           # Runtime manager
│   └── proxy/             # REST/SSE proxy layer
├── web/                    # React frontend (Vite + TanStack)
│   ├── src/
│   │   ├── components/    # UI components
│   │   ├── hooks/         # React hooks
│   │   ├── stores/        # Jotai state
│   │   └── api/           # API clients
│   └── vite.config.ts
├── packages/
│   ├── protocol/          # Zod schemas, API contracts
│   └── shared-ui/         # Design tokens, shared components
├── static/                # Bundled Dashboard & runtime
├── scripts/               # Build & staging scripts
└── tauri.conf.json        # Tauri configuration
```

## Configuration

### Tauri Configuration (`tauri.conf.json`)

```json
{
  "productName": "Hermes Agent CN Desktop",
  "identifier": "cn.hermesagent.desktop",
  "build": {
    "beforeDevCommand": "pnpm web:dev",
    "beforeBuildCommand": "pnpm web:build",
    "devUrl": "http://localhost:9545",
    "frontendDist": "../web/dist"
  },
  "windows": [
    {
      "title": "Hermes Agent CN",
      "width": 1200,
      "height": 800,
      "minWidth": 800,
      "minHeight": 600
    }
  ]
}
```

### Environment Variables

```bash
# Development
VITE_API_BASE_URL=http://127.0.0.1:9120
VITE_DEV_MODE=true

# Runtime configuration (passed to Hermes Core)
HERMES_YOLO_MODE=false  # Auto-approve dangerous commands
HERMES_PORT=9120
HERMES_HOST=127.0.0.1
```

## Code Examples

### Rust: Tauri Command

```rust
// src/commands/runtime.rs
use tauri::State;
use crate::runtime::RuntimeManager;

#[tauri::command]
pub async fn start_runtime(
    runtime_manager: State<'_, RuntimeManager>,
) -> Result<String, String> {
    runtime_manager
        .start()
        .await
        .map(|pid| format!("Runtime started with PID: {}", pid))
        .map_err(|e| format!("Failed to start runtime: {}", e))
}

#[tauri::command]
pub async fn get_runtime_health(
    runtime_manager: State<'_, RuntimeManager>,
) -> Result<serde_json::Value, String> {
    runtime_manager
        .health_check()
        .await
        .map_err(|e| e.to_string())
}
```

### TypeScript: React Component with Tauri IPC

```typescript
// web/src/components/RuntimeStatus.tsx
import { invoke } from '@tauri-apps/api/core';
import { useQuery } from '@tanstack/react-query';

export function RuntimeStatus() {
  const { data: health, isLoading } = useQuery({
    queryKey: ['runtime-health'],
    queryFn: async () => {
      return await invoke<HealthStatus>('get_runtime_health');
    },
    refetchInterval: 5000,
  });

  if (isLoading) return <div>Loading runtime status...</div>;

  return (
    <div className="runtime-status">
      <h3>Runtime Health</h3>
      <div>Status: {health?.status}</div>
      <div>Version: {health?.version}</div>
      <div>Uptime: {health?.uptime}s</div>
    </div>
  );
}
```

### TypeScript: API Client with Zod Validation

```typescript
// web/src/api/runtime.ts
import { z } from 'zod';

const RuntimeConfigSchema = z.object({
  port: z.number(),
  host: z.string(),
  yolo_mode: z.boolean(),
});

export type RuntimeConfig = z.infer<typeof RuntimeConfigSchema>;

export async function updateRuntimeConfig(
  config: RuntimeConfig
): Promise<void> {
  const validated = RuntimeConfigSchema.parse(config);
  
  await fetch('/api/runtime/config', {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(validated),
  });
}
```

### Rust: REST Proxy Command

```rust
// src/commands/proxy.rs
use reqwest::Client;
use serde_json::Value;

#[tauri::command]
pub async fn proxy_request(
    url: String,
    method: String,
    body: Option<Value>,
) -> Result<Value, String> {
    let client = Client::new();
    
    let request = match method.as_str() {
        "GET" => client.get(&url),
        "POST" => client.post(&url).json(&body),
        "PUT" => client.put(&url).json(&body),
        "DELETE" => client.delete(&url),
        _ => return Err("Unsupported HTTP method".to_string()),
    };

    let response = request
        .send()
        .await
        .map_err(|e| e.to_string())?;

    response
        .json::<Value>()
        .await
        .map_err(|e| e.to_string())
}
```

### TypeScript: Jotai State Management

```typescript
// web/src/stores/settings.ts
import { atom } from 'jotai';
import { atomWithStorage } from 'jotai/utils';

export const themeAtom = atomWithStorage<'light' | 'dark'>(
  'hermes-theme',
  'light'
);

export const yoloModeAtom = atom(false);

export const modelProviderAtom = atomWithStorage(
  'hermes-model-provider',
  {
    provider: 'openai',
    apiKey: '',
    baseUrl: '',
  }
);
```

## Common Patterns

### Runtime Lifecycle Management

```rust
// src/runtime/manager.rs
use std::process::{Child, Command};
use tokio::sync::RwLock;

pub struct RuntimeManager {
    process: RwLock<Option<Child>>,
    config_path: PathBuf,
}

impl RuntimeManager {
    pub async fn start(&self) -> Result<u32, RuntimeError> {
        let mut process_lock = self.process.write().await;
        
        if process_lock.is_some() {
            return Err(RuntimeError::AlreadyRunning);
        }

        let child = Command::new("hermes")
            .arg("start")
            .arg("--config")
            .arg(&self.config_path)
            .spawn()?;

        let pid = child.id();
        *process_lock = Some(child);
        
        Ok(pid)
    }

    pub async fn stop(&self) -> Result<(), RuntimeError> {
        let mut process_lock = self.process.write().await;
        
        if let Some(mut child) = process_lock.take() {
            child.kill()?;
            child.wait()?;
        }
        
        Ok(())
    }
}
```

### Frontend Data Fetching Pattern

```typescript
// web/src/hooks/useRuntimeStatus.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { invoke } from '@tauri-apps/api/core';

export function useRuntimeStatus() {
  const queryClient = useQueryClient();

  const status = useQuery({
    queryKey: ['runtime-status'],
    queryFn: () => invoke<RuntimeStatus>('get_runtime_status'),
    refetchInterval: 3000,
  });

  const start = useMutation({
    mutationFn: () => invoke('start_runtime'),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['runtime-status'] });
    },
  });

  const stop = useMutation({
    mutationFn: () => invoke('stop_runtime'),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['runtime-status'] });
    },
  });

  return { status, start, stop };
}
```

### SSE Streaming Pattern

```typescript
// web/src/api/streaming.ts
import { invoke } from '@tauri-apps/api/core';

export async function streamChatResponse(
  message: string,
  onChunk: (chunk: string) => void,
  onError: (error: string) => void
) {
  try {
    await invoke('stream_chat', {
      message,
      onChunk: (chunk: string) => {
        onChunk(chunk);
      },
    });
  } catch (error) {
    onError(error as string);
  }
}
```

## Troubleshooting

### Runtime Won't Start

```bash
# Check runtime binary exists
ls static/runtime/

# Verify runtime permissions (macOS/Linux)
chmod +x static/runtime/hermes

# Check runtime logs
tail -f ~/.hermes/logs/runtime.log

# Test runtime independently
./static/runtime/hermes start --debug
```

### CORS Issues in Development

Development mode runs Vite on `localhost:9545` accessing Dashboard on `127.0.0.1:9120`. Use proxy command:

```typescript
// Instead of direct fetch
const response = await fetch('http://127.0.0.1:9120/api/chat');

// Use Tauri proxy
const response = await invoke('proxy_request', {
  url: 'http://127.0.0.1:9120/api/chat',
  method: 'GET',
});
```

### Build Failures

```bash
# Clear build cache
cargo clean
rm -rf web/dist web/node_modules/.vite

# Reinstall dependencies
pnpm install

# Check Rust toolchain
rustup update stable

# Check Node version
node -v  # Should be 20+
```

### macOS Code Signing

```bash
# Development: disable code signing temporarily
export TAURI_SKIP_DEVSERVER_PROMPT=true

# Production: configure in tauri.conf.json
{
  "bundle": {
    "macOS": {
      "signingIdentity": "-",  # Ad-hoc signing
      "entitlements": null
    }
  }
}
```

### Windows Installer Issues

```bash
# Ensure WiX toolset is installed
# Download from https://wixtoolset.org/

# Or use NSIS instead (tauri.conf.json)
{
  "bundle": {
    "targets": ["nsis"]
  }
}
```

### Runtime Version Mismatch

```rust
// Force runtime update
#[tauri::command]
pub async fn force_runtime_update(
    runtime_manager: State<'_, RuntimeManager>,
) -> Result<(), String> {
    runtime_manager.stop().await?;
    runtime_manager.download_latest().await?;
    runtime_manager.verify_signature().await?;
    runtime_manager.start().await?;
    Ok(())
}
```

### Memory Leaks in Frontend

```typescript
// Proper cleanup in React components
useEffect(() => {
  const unlisten = listen('runtime-event', (event) => {
    console.log(event.payload);
  });

  return () => {
    unlisten.then(f => f());  // Cleanup listener
  };
}, []);
```

## Release Process

Create and push a SemVer tag:

```bash
git tag v0.3.2
git push origin v0.3.2
```

This triggers `.github/workflows/release-desktop.yml` which:
1. Builds for Windows (x64) and macOS (Intel + Apple Silicon)
2. Creates GitHub Release with artifacts
3. Marks alpha/beta tags as pre-release

Build artifacts include:
- `Hermes.Agent.CN.Desktop_0.3.2_aarch64.dmg` (macOS ARM)
- `Hermes.Agent.CN.Desktop_0.3.2_x64.dmg` (macOS Intel)
- `Hermes.Agent.CN.Desktop_0.3.2_x64-setup.exe` (Windows)

## Additional Resources

- [Official Website](https://desktop.hermesagent.org.cn)
- [Hermes Agent CN Community](https://hermesagent.org.cn)
- [Hermes-CN-Core Repository](https://github.com/Eynzof/Hermes-CN-Core)
- [Tauri v2 Documentation](https://v2.tauri.app/)
- [Contributing Guide](https://github.com/Eynzof/Hermes-CN-Desktop/blob/main/CONTRIBUTING.md)
