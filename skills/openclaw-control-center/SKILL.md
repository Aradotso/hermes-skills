---
name: openclaw-control-center
description: Run a local observability and control dashboard for OpenClaw AI agents with real-time collaboration, task management, and safety-first defaults.
triggers:
  - set up openclaw control center dashboard
  - create openclaw observability interface
  - monitor openclaw agents and tasks
  - implement openclaw collaboration hall
  - configure openclaw control center
  - debug openclaw agent activity
  - track openclaw usage and spend
  - manage openclaw task approvals
---

# OpenClaw Control Center

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

OpenClaw Control Center is a TypeScript-based local dashboard that transforms OpenClaw from a black box into a transparent control center. It provides real-time observability for agent activity, token usage, task execution, multi-agent collaboration, and approval workflows—all with safety-first defaults (read-only mode, local token auth, and disabled mutations by default).

## What It Does

- **Overview**: Health dashboard showing agent status, decisions waiting, and operator summaries
- **Collaboration Hall**: Multi-agent chat workspace with live discussion, assignment, handoff, and review
- **Staff Management**: Real-time view of active vs. queued agents
- **Task Management**: Execution chains, approvals, runtime evidence, and blocked work
- **Usage Tracking**: Token consumption, spend trends, context pressure, quota monitoring
- **Memory & Documents**: Source-backed workbenches for agent memory and markdown documents
- **Safety First**: Read-only by default, local token auth required, mutation routes disabled

## Installation

```bash
# Clone the repository
git clone https://github.com/TianyiDataScience/openclaw-control-center.git
cd openclaw-control-center

# Install dependencies
npm install

# Set up environment
cp .env.example .env

# Build the project
npm run build

# Run tests
npm test

# Run smoke tests
npm run smoke:ui
npm run smoke:hall

# Start development server
npm run dev:ui
```

## Configuration

### Environment Variables (.env)

```bash
# Safety and security (defaults)
READONLY_MODE=true
LOCAL_TOKEN_AUTH_REQUIRED=true
LOCAL_API_TOKEN=your-long-random-secret-here

# Import/export protection
IMPORT_MUTATION_ENABLED=false
IMPORT_MUTATION_DRY_RUN=false

# Approval actions
APPROVAL_ACTIONS_ENABLED=false
APPROVAL_ACTIONS_DRY_RUN=true

# UI configuration
UI_PORT=4310
UI_BIND_ADDRESS=127.0.0.1

# For Tailscale or remote access
OPENCLAW_CONTROL_UI_URL=http://<tailscale-host>:4310/
# UI_BIND_ADDRESS=0.0.0.0  # Auto-set when OPENCLAW_CONTROL_UI_URL is provided
```

### Directory Structure

```
control-center/
├── src/
│   ├── ui/           # Frontend TypeScript/React components
│   ├── server/       # Backend API routes
│   ├── lib/          # Shared utilities
│   └── types/        # TypeScript definitions
├── docs/
│   ├── assets/       # Screenshots and images
│   └── FAQ.md        # Troubleshooting guide
├── HALL.md           # Shared collaboration style guide
└── .env              # Local configuration
```

## Key Commands

### Development

```bash
# Start UI development server (recommended, cross-platform)
npm run dev:ui

# Single monitor pass without UI
npm run dev

# Build production bundle
npm run build

# Run all tests
npm test

# Smoke tests for UI and hall
npm run smoke:ui
npm run smoke:hall

# Linting
npm run lint
npm run lint:fix
```

### Accessing the UI

```bash
# English interface
http://127.0.0.1:4310/?section=overview&lang=en

# Chinese interface
http://127.0.0.1:4310/?section=overview&lang=zh

# Direct section access
http://127.0.0.1:4310/?section=collaboration&lang=en
http://127.0.0.1:4310/?section=staff&lang=en
http://127.0.0.1:4310/?section=tasks&lang=en
```

## Real Code Examples

### Enabling Write Operations

```typescript
// .env configuration for enabling mutations
READONLY_MODE=false
LOCAL_TOKEN_AUTH_REQUIRED=true
LOCAL_API_TOKEN=generate-a-secure-random-token-here
IMPORT_MUTATION_ENABLED=true
APPROVAL_ACTIONS_ENABLED=true
APPROVAL_ACTIONS_DRY_RUN=false
```

### API Authentication Pattern

```typescript
// src/lib/api-client.ts
interface ApiRequest {
  endpoint: string;
  method: 'GET' | 'POST' | 'PUT' | 'DELETE';
  body?: any;
  requiresAuth?: boolean;
}

async function callApi({ endpoint, method, body, requiresAuth = true }: ApiRequest) {
  const headers: Record<string, string> = {
    'Content-Type': 'application/json',
  };

  // Add authorization for protected endpoints
  if (requiresAuth) {
    const token = process.env.LOCAL_API_TOKEN;
    if (!token) {
      throw new Error('LOCAL_API_TOKEN not configured');
    }
    headers['Authorization'] = `Bearer ${token}`;
  }

  const response = await fetch(`http://127.0.0.1:4310${endpoint}`, {
    method,
    headers,
    body: body ? JSON.stringify(body) : undefined,
  });

  if (!response.ok) {
    throw new Error(`API error: ${response.status} ${response.statusText}`);
  }

  return response.json();
}

// Example: Save task with authentication
async function saveTask(taskId: string, updates: any) {
  return callApi({
    endpoint: `/api/tasks/${taskId}`,
    method: 'PUT',
    body: updates,
    requiresAuth: true,
  });
}
```

### Collaboration Hall Workflow

```typescript
// src/types/hall.ts
interface HallMessage {
  id: string;
  taskId: string;
  agentId: string;
  content: string;
  phase: 'discussion' | 'execution' | 'review' | 'blocked';
  timestamp: string;
  mentions?: string[];
}

interface ExecutionOrder {
  taskId: string;
  owners: string[];
  currentOwnerIndex: number;
  nextHandoff?: string;
}

// Example: Create hall task and arrange execution
async function createHallTask(content: string, roster: string[]) {
  // 1. Create task in hall
  const task = await callApi({
    endpoint: '/api/hall/tasks',
    method: 'POST',
    body: { content, phase: 'discussion' },
  });

  // 2. Let agents discuss (wait for 2+ replies)
  await waitForDiscussion(task.id, 2);

  // 3. Arrange execution order
  const executionOrder = await callApi({
    endpoint: `/api/hall/tasks/${task.id}/execution-order`,
    method: 'PUT',
    body: {
      owners: ['agent-manager', 'agent-builder', 'agent-qa'],
    },
  });

  // 4. Start execution
  const execution = await callApi({
    endpoint: `/api/hall/tasks/${task.id}/start`,
    method: 'POST',
  });

  return { task, executionOrder, execution };
}
```

### Staff Status Monitoring

```typescript
// src/lib/staff-monitor.ts
interface AgentStatus {
  agentId: string;
  displayName: string;
  status: 'active' | 'idle' | 'blocked' | 'queued';
  currentTask?: string;
  lastActivity: string;
  tokenUsage24h: number;
}

async function getStaffStatus(): Promise<AgentStatus[]> {
  const response = await callApi({
    endpoint: '/api/staff/status',
    method: 'GET',
    requiresAuth: false, // Read-only endpoint
  });

  return response.agents.map((agent: any) => ({
    agentId: agent.id,
    displayName: agent.display_name || agent.id,
    status: determineStatus(agent),
    currentTask: agent.current_task_id,
    lastActivity: agent.last_seen,
    tokenUsage24h: agent.token_usage_24h || 0,
  }));
}

function determineStatus(agent: any): AgentStatus['status'] {
  if (agent.blocked_reason) return 'blocked';
  if (agent.current_task_id) return 'active';
  if (agent.queued_tasks > 0) return 'queued';
  return 'idle';
}
```

### Task Approval Flow

```typescript
// src/lib/approval-handler.ts
interface ApprovalRequest {
  taskId: string;
  action: 'approve' | 'reject' | 'request-changes';
  comment?: string;
  reviewerId: string;
}

async function handleTaskApproval(request: ApprovalRequest) {
  // Ensure approval actions are enabled
  if (process.env.APPROVAL_ACTIONS_ENABLED !== 'true') {
    throw new Error('Approval actions are disabled');
  }

  const isDryRun = process.env.APPROVAL_ACTIONS_DRY_RUN === 'true';

  const response = await callApi({
    endpoint: `/api/tasks/${request.taskId}/approve`,
    method: 'POST',
    body: {
      action: request.action,
      comment: request.comment,
      reviewer_id: request.reviewerId,
      dry_run: isDryRun,
    },
    requiresAuth: true,
  });

  if (isDryRun) {
    console.log('[DRY RUN] Would have:', request.action, request.taskId);
  }

  return response;
}
```

### Usage Tracking

```typescript
// src/lib/usage-tracker.ts
interface UsageMetrics {
  period: 'today' | '7d' | '30d';
  tokenCount: number;
  estimatedCost: number;
  topConsumers: Array<{ agentId: string; tokens: number }>;
  contextPressure: Array<{ sessionId: string; usage: number; limit: number }>;
}

async function getUsageMetrics(period: UsageMetrics['period']): Promise<UsageMetrics> {
  const response = await callApi({
    endpoint: `/api/usage/metrics?period=${period}`,
    method: 'GET',
    requiresAuth: false,
  });

  return {
    period,
    tokenCount: response.total_tokens,
    estimatedCost: response.estimated_cost_usd,
    topConsumers: response.agents
      .sort((a: any, b: any) => b.tokens - a.tokens)
      .slice(0, 5),
    contextPressure: response.sessions.filter(
      (s: any) => s.usage / s.limit > 0.75
    ),
  };
}
```

## Common Patterns

### Hall Collaboration Workflow

```typescript
// Complete hall workflow from task creation to completion
async function runHallCollaboration(taskDescription: string) {
  // 1. Create task
  const task = await callApi({
    endpoint: '/api/hall/tasks',
    method: 'POST',
    body: { content: taskDescription, phase: 'discussion' },
  });

  // 2. Wait for discussion (at least 2 agents should reply)
  console.log('Waiting for agent discussion...');
  await pollUntil(
    async () => {
      const messages = await callApi({
        endpoint: `/api/hall/tasks/${task.id}/messages`,
        method: 'GET',
        requiresAuth: false,
      });
      return messages.length >= 2;
    },
    { intervalMs: 2000, timeoutMs: 60000 }
  );

  // 3. Arrange execution order
  await callApi({
    endpoint: `/api/hall/tasks/${task.id}/execution-order`,
    method: 'PUT',
    body: {
      owners: ['agent-planner', 'agent-builder', 'agent-qa'],
    },
  });

  // 4. Start execution
  await callApi({
    endpoint: `/api/hall/tasks/${task.id}/start`,
    method: 'POST',
  });

  // 5. Monitor execution with SSE
  const eventSource = new EventSource(
    `http://127.0.0.1:4310/api/hall/tasks/${task.id}/stream`
  );

  eventSource.onmessage = (event) => {
    const update = JSON.parse(event.data);
    console.log(`[${update.agentId}] ${update.content}`);
  };

  // 6. Wait for completion
  await pollUntil(
    async () => {
      const status = await callApi({
        endpoint: `/api/hall/tasks/${task.id}`,
        method: 'GET',
        requiresAuth: false,
      });
      return status.phase === 'review' || status.phase === 'blocked';
    },
    { intervalMs: 5000, timeoutMs: 600000 }
  );

  eventSource.close();
}

async function pollUntil(
  condition: () => Promise<boolean>,
  options: { intervalMs: number; timeoutMs: number }
): Promise<void> {
  const startTime = Date.now();
  while (!(await condition())) {
    if (Date.now() - startTime > options.timeoutMs) {
      throw new Error('Polling timeout');
    }
    await new Promise((resolve) => setTimeout(resolve, options.intervalMs));
  }
}
```

### Memory Inspection

```typescript
// src/lib/memory-inspector.ts
async function inspectAgentMemory(agentId: string) {
  // Get memory status
  const status = await callApi({
    endpoint: `/api/memory/${agentId}/status`,
    method: 'GET',
    requiresAuth: false,
  });

  console.log(`Memory status for ${agentId}:`, {
    isUsable: status.is_usable,
    isSearchable: status.is_searchable,
    fileCount: status.file_count,
    totalSizeKb: status.total_size_kb,
  });

  // Read daily memory
  const dailyMemory = await callApi({
    endpoint: `/api/memory/${agentId}/daily`,
    method: 'GET',
    requiresAuth: false,
  });

  console.log('Recent daily entries:', dailyMemory.entries.slice(-5));

  // Search memory (if enabled)
  if (status.is_searchable) {
    const searchResults = await callApi({
      endpoint: `/api/memory/${agentId}/search?q=task+completion`,
      method: 'GET',
      requiresAuth: false,
    });
    console.log('Search results:', searchResults.matches);
  }
}
```

### Document Management

```typescript
// src/lib/document-manager.ts
async function updateSharedDocument(filename: string, content: string) {
  // Ensure write mode is enabled
  if (process.env.READONLY_MODE === 'true') {
    throw new Error('Cannot save in READONLY_MODE');
  }

  return callApi({
    endpoint: '/api/documents/shared',
    method: 'PUT',
    body: {
      filename,
      content,
    },
    requiresAuth: true,
  });
}

async function getAgentDocument(agentId: string, docType: string) {
  return callApi({
    endpoint: `/api/documents/agent/${agentId}/${docType}`,
    method: 'GET',
    requiresAuth: false,
  });
}
```

## Troubleshooting

### "Role not defined in workspace"

Add agent definitions to `~/.openclaw/openclaw.json`:

```json
{
  "agents": [
    {
      "id": "agent-manager",
      "display_name": "Manager",
      "role": "Coordinates work and assigns tasks"
    },
    {
      "id": "agent-builder",
      "display_name": "Builder",
      "role": "Implements features and writes code"
    }
  ]
}
```

### Connection Issues

Check connection health in Settings → Connection health card. Common issues:

```typescript
// Verify OpenClaw is reachable
const health = await callApi({
  endpoint: '/api/health',
  method: 'GET',
  requiresAuth: false,
});

console.log('OpenClaw connection:', health.openclaw_reachable);
console.log('Memory accessible:', health.memory_accessible);
console.log('Task storage:', health.task_storage_ready);
```

### Authentication Failures

Ensure `LOCAL_API_TOKEN` is set and matches in both `.env` and UI:

```bash
# Generate a secure token
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"

# Add to .env
LOCAL_API_TOKEN=<generated-token>
```

### Hall Execution Not Starting

1. Verify agents are defined in OpenClaw roster
2. Check that execution order is saved before starting
3. Ensure at least one owner is in the queue
4. Check logs for runtime dispatch errors

```typescript
// Debug execution state
const taskState = await callApi({
  endpoint: `/api/hall/tasks/${taskId}`,
  method: 'GET',
  requiresAuth: false,
});

console.log('Phase:', taskState.phase);
console.log('Owners:', taskState.execution_order?.owners);
console.log('Current owner index:', taskState.execution_order?.current_owner_index);
```

### High Context Pressure

Monitor context usage in Usage → Context pressure card:

```typescript
const contextMetrics = await callApi({
  endpoint: '/api/usage/context-pressure',
  method: 'GET',
  requiresAuth: false,
});

// Find sessions near limit
const critical = contextMetrics.sessions.filter(
  (s: any) => s.usage / s.limit > 0.9
);

console.log('Critical sessions:', critical);
```

## Security Best Practices

1. **Keep defaults**: Don't disable `READONLY_MODE` or `LOCAL_TOKEN_AUTH_REQUIRED` unless necessary
2. **Strong tokens**: Use `crypto.randomBytes(32).toString('hex')` for `LOCAL_API_TOKEN`
3. **Network binding**: Keep `UI_BIND_ADDRESS=127.0.0.1` unless you need remote access
4. **Dry-run first**: Test approval actions with `APPROVAL_ACTIONS_DRY_RUN=true` before enabling live mutations
5. **Monitor Security risk summary**: Check Settings page for current risk assessment

## Further Resources

- [FAQ & Best Practices](docs/FAQ.md)
- [GitHub Repository](https://github.com/TianyiDataScience/openclaw-control-center)
- [Chinese README](README.zh-CN.md)
