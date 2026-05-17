---
name: openclaw-mission-control
description: AI Agent Orchestration Dashboard for managing AI agents, tasks, and multi-agent collaboration via OpenClaw Gateway
triggers:
  - "set up openclaw mission control"
  - "deploy ai agent orchestration dashboard"
  - "configure openclaw gateway management"
  - "create agent workflow with approval controls"
  - "manage multi-agent collaboration tasks"
  - "set up openclaw authentication"
  - "troubleshoot openclaw mission control"
  - "integrate openclaw api automation"
---

# OpenClaw Mission Control

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

OpenClaw Mission Control is a centralized operations and governance platform for AI agent orchestration. It provides a unified dashboard for managing AI agents, assigning tasks, coordinating multi-agent collaboration, and implementing approval-driven governance. Built with TypeScript, it offers both web UI and API-first access for automation.

## What It Does

Mission Control serves as a control plane for OpenClaw operations:

- **Work Orchestration**: Manage organizations, board groups, boards, tasks, and tags in hierarchical structures
- **Agent Operations**: Create, inspect, and manage agent lifecycle from a unified interface
- **Governance & Approvals**: Route sensitive actions through explicit approval workflows with audit trails
- **Gateway Management**: Connect and operate distributed execution environments and gateway integrations
- **Activity Visibility**: Review complete timeline of system actions for debugging and accountability
- **API-First Design**: Support both web workflows and automation clients from the same platform

## Installation

### Quick Start with Installer

```bash
# One-line installation (clones repo if needed)
curl -fsSL https://raw.githubusercontent.com/abhi1693/openclaw-mission-control/master/install.sh | bash

# Or if you already cloned the repo
./install.sh
```

The installer is interactive and handles:
- Deployment mode selection (Docker or local)
- System dependency installation
- Environment file generation
- Bootstrap and startup

### Manual Docker Setup

```bash
# Clone repository
git clone https://github.com/abhi1693/openclaw-mission-control.git
cd openclaw-mission-control

# Configure environment
cp .env.example .env

# Edit .env and set required values:
# - LOCAL_AUTH_TOKEN (minimum 50 characters)
# - BASE_URL (backend origin)
# - NEXT_PUBLIC_API_URL (frontend API endpoint)

# Start services
docker compose -f compose.yml --env-file .env up -d --build
```

### Manual Local Setup

Prerequisites: Node.js 22+, PostgreSQL, Redis

```bash
# Backend setup
cd backend
cp .env.example .env
# Edit .env with database credentials and auth config
npm install
npm run db:migrate
npm run dev

# Frontend setup (in new terminal)
cd frontend
cp .env.example .env
# Edit .env with API URL
npm install
npm run dev
```

Access:
- UI: http://localhost:3000
- API: http://localhost:8000
- Health check: http://localhost:8000/healthz

## Authentication Configuration

Mission Control supports two authentication modes:

### Local Token Mode (Default for Self-Hosted)

```bash
# .env
AUTH_MODE=local
LOCAL_AUTH_TOKEN=your-secure-token-minimum-50-characters-required
```

Use in API requests:
```bash
curl -H "Authorization: Bearer ${LOCAL_AUTH_TOKEN}" \
  http://localhost:8000/api/organizations
```

### Clerk JWT Mode

```bash
# .env
AUTH_MODE=clerk
CLERK_PUBLISHABLE_KEY=${CLERK_PUBLISHABLE_KEY}
CLERK_SECRET_KEY=${CLERK_SECRET_KEY}

# frontend/.env
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=${CLERK_PUBLISHABLE_KEY}
```

## Core Concepts

### Hierarchy Structure

```
Organization
  └── Board Group
      └── Board
          └── Task
              └── Tag (optional)
```

### Agent Lifecycle

1. **Creation**: Define agent with name, description, capabilities
2. **Assignment**: Link agent to boards and tasks
3. **Execution**: Agent processes tasks according to workflow
4. **Approval**: Sensitive actions require explicit approval
5. **Audit**: All actions logged in activity timeline

## API Usage

### Organizations

```typescript
// Create organization
const createOrganization = async (token: string) => {
  const response = await fetch('http://localhost:8000/api/organizations', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      name: 'Engineering Team',
      description: 'Primary engineering organization',
    }),
  });
  return response.json();
};

// List organizations
const listOrganizations = async (token: string) => {
  const response = await fetch('http://localhost:8000/api/organizations', {
    headers: {
      'Authorization': `Bearer ${token}`,
    },
  });
  return response.json();
};
```

### Board Groups

```typescript
// Create board group
const createBoardGroup = async (token: string, orgId: string) => {
  const response = await fetch('http://localhost:8000/api/board-groups', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      organization_id: orgId,
      name: 'Q1 2026 Projects',
      description: 'First quarter project boards',
    }),
  });
  return response.json();
};
```

### Boards

```typescript
// Create board
const createBoard = async (token: string, groupId: string) => {
  const response = await fetch('http://localhost:8000/api/boards', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      board_group_id: groupId,
      name: 'API Development',
      description: 'Backend API development tasks',
    }),
  });
  return response.json();
};

// Get board with tasks
const getBoard = async (token: string, boardId: string) => {
  const response = await fetch(`http://localhost:8000/api/boards/${boardId}`, {
    headers: {
      'Authorization': `Bearer ${token}`,
    },
  });
  return response.json();
};
```

### Tasks

```typescript
// Create task
const createTask = async (token: string, boardId: string) => {
  const response = await fetch('http://localhost:8000/api/tasks', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      board_id: boardId,
      title: 'Implement user authentication',
      description: 'Add JWT-based authentication to API endpoints',
      status: 'todo',
      priority: 'high',
      assigned_agent_id: null, // Optional agent assignment
    }),
  });
  return response.json();
};

// Update task status
const updateTaskStatus = async (token: string, taskId: string, status: string) => {
  const response = await fetch(`http://localhost:8000/api/tasks/${taskId}`, {
    method: 'PATCH',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      status, // 'todo' | 'in_progress' | 'review' | 'done'
    }),
  });
  return response.json();
};
```

### Agents

```typescript
// Create agent
const createAgent = async (token: string) => {
  const response = await fetch('http://localhost:8000/api/agents', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      name: 'Code Review Agent',
      description: 'Automated code review and quality checks',
      capabilities: ['code-analysis', 'security-scan', 'style-check'],
      config: {
        model: 'gpt-4',
        temperature: 0.3,
        max_tokens: 2000,
      },
    }),
  });
  return response.json();
};

// Assign agent to task
const assignAgent = async (token: string, taskId: string, agentId: string) => {
  const response = await fetch(`http://localhost:8000/api/tasks/${taskId}`, {
    method: 'PATCH',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      assigned_agent_id: agentId,
    }),
  });
  return response.json();
};
```

### Approvals

```typescript
// Request approval for sensitive action
const requestApproval = async (token: string, taskId: string) => {
  const response = await fetch('http://localhost:8000/api/approvals', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      task_id: taskId,
      action: 'deploy_to_production',
      reason: 'Deploy feature to production environment',
      metadata: {
        environment: 'production',
        service: 'api-gateway',
      },
    }),
  });
  return response.json();
};

// Approve or reject
const processApproval = async (token: string, approvalId: string, approved: boolean) => {
  const response = await fetch(`http://localhost:8000/api/approvals/${approvalId}`, {
    method: 'PATCH',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      status: approved ? 'approved' : 'rejected',
      comment: approved ? 'LGTM' : 'Needs additional testing',
    }),
  });
  return response.json();
};
```

### Gateway Management

```typescript
// Register gateway
const registerGateway = async (token: string) => {
  const response = await fetch('http://localhost:8000/api/gateways', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      name: 'Production Gateway',
      endpoint: 'https://gateway.example.com',
      auth_token: process.env.GATEWAY_AUTH_TOKEN,
      capabilities: ['task-execution', 'log-streaming'],
    }),
  });
  return response.json();
};

// Execute task via gateway
const executeViaGateway = async (token: string, gatewayId: string, taskId: string) => {
  const response = await fetch(`http://localhost:8000/api/gateways/${gatewayId}/execute`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      task_id: taskId,
      parameters: {
        environment: 'production',
      },
    }),
  });
  return response.json();
};
```

### Activity Timeline

```typescript
// Get activity log
const getActivity = async (token: string, filters?: object) => {
  const params = new URLSearchParams(filters as Record<string, string>);
  const response = await fetch(`http://localhost:8000/api/activity?${params}`, {
    headers: {
      'Authorization': `Bearer ${token}`,
    },
  });
  return response.json();
};

// Get activity for specific resource
const getResourceActivity = async (token: string, resourceType: string, resourceId: string) => {
  const response = await fetch(
    `http://localhost:8000/api/activity?resource_type=${resourceType}&resource_id=${resourceId}`,
    {
      headers: {
        'Authorization': `Bearer ${token}`,
      },
    }
  );
  return response.json();
};
```

## Common Patterns

### Multi-Agent Workflow

```typescript
// Create workflow with multiple agents
const createMultiAgentWorkflow = async (token: string) => {
  // 1. Create organization and board
  const org = await createOrganization(token);
  const group = await createBoardGroup(token, org.id);
  const board = await createBoard(token, group.id);
  
  // 2. Create specialized agents
  const codeAgent = await createAgent(token, {
    name: 'Code Generator',
    capabilities: ['code-generation'],
  });
  
  const reviewAgent = await createAgent(token, {
    name: 'Code Reviewer',
    capabilities: ['code-review'],
  });
  
  const testAgent = await createAgent(token, {
    name: 'Test Runner',
    capabilities: ['test-execution'],
  });
  
  // 3. Create sequential tasks
  const codeTask = await createTask(token, board.id, {
    title: 'Generate feature code',
    assigned_agent_id: codeAgent.id,
  });
  
  const reviewTask = await createTask(token, board.id, {
    title: 'Review generated code',
    assigned_agent_id: reviewAgent.id,
    dependencies: [codeTask.id],
  });
  
  const testTask = await createTask(token, board.id, {
    title: 'Run integration tests',
    assigned_agent_id: testAgent.id,
    dependencies: [reviewTask.id],
  });
  
  return { board, tasks: [codeTask, reviewTask, testTask] };
};
```

### Approval-Gated Deployment

```typescript
// Deployment with approval requirement
const deployWithApproval = async (token: string, taskId: string) => {
  // 1. Request approval
  const approval = await requestApproval(token, taskId);
  
  // 2. Wait for approval (polling or webhook)
  let approvalStatus = approval;
  while (approvalStatus.status === 'pending') {
    await new Promise(resolve => setTimeout(resolve, 5000));
    const response = await fetch(
      `http://localhost:8000/api/approvals/${approval.id}`,
      { headers: { 'Authorization': `Bearer ${token}` } }
    );
    approvalStatus = await response.json();
  }
  
  // 3. Execute if approved
  if (approvalStatus.status === 'approved') {
    await fetch(`http://localhost:8000/api/tasks/${taskId}/execute`, {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json',
      },
    });
    return { success: true };
  }
  
  return { success: false, reason: approvalStatus.comment };
};
```

### Gateway-Based Distributed Execution

```typescript
// Execute tasks across multiple gateways
const distributeExecution = async (token: string, tasks: any[], gateways: any[]) => {
  const executions = [];
  
  for (let i = 0; i < tasks.length; i++) {
    const gateway = gateways[i % gateways.length]; // Round-robin
    const execution = await executeViaGateway(token, gateway.id, tasks[i].id);
    executions.push({ task: tasks[i], gateway: gateway.name, execution });
  }
  
  return executions;
};
```

## Environment Configuration

### Docker Deployment

```bash
# .env
AUTH_MODE=local
LOCAL_AUTH_TOKEN=${LOCAL_AUTH_TOKEN}
BASE_URL=http://localhost:8000
NEXT_PUBLIC_API_URL=auto

# Database
DATABASE_URL=postgresql://postgres:postgres@db:5432/mission_control
REDIS_URL=redis://redis:6379

# Optional: Clerk authentication
# AUTH_MODE=clerk
# CLERK_PUBLISHABLE_KEY=${CLERK_PUBLISHABLE_KEY}
# CLERK_SECRET_KEY=${CLERK_SECRET_KEY}
```

### Production Configuration

```bash
# .env
AUTH_MODE=clerk
BASE_URL=https://api.yourdomain.com
NEXT_PUBLIC_API_URL=https://api.yourdomain.com

# Database (managed PostgreSQL)
DATABASE_URL=${DATABASE_URL}
REDIS_URL=${REDIS_URL}

# Clerk
CLERK_PUBLISHABLE_KEY=${CLERK_PUBLISHABLE_KEY}
CLERK_SECRET_KEY=${CLERK_SECRET_KEY}

# Security
NODE_ENV=production
CORS_ORIGINS=https://app.yourdomain.com
```

## Docker Commands

```bash
# Start services
docker compose -f compose.yml --env-file .env up -d --build

# Watch mode (auto-rebuild on changes)
docker compose -f compose.yml --env-file .env up --build --watch

# View logs
docker compose -f compose.yml --env-file .env logs -f

# Rebuild after pulling changes
docker compose -f compose.yml --env-file .env up -d --build --force-recreate

# Clean rebuild (no cache)
docker compose -f compose.yml --env-file .env build --no-cache --pull
docker compose -f compose.yml --env-file .env up -d --force-recreate

# Stop services
docker compose -f compose.yml --env-file .env down

# Stop and remove volumes
docker compose -f compose.yml --env-file .env down -v
```

## Troubleshooting

### Authentication Errors

**Problem**: `401 Unauthorized` responses

```bash
# Verify token is set and long enough (50+ chars)
echo ${LOCAL_AUTH_TOKEN} | wc -c

# Check AUTH_MODE matches configuration
grep AUTH_MODE .env

# Test authentication
curl -H "Authorization: Bearer ${LOCAL_AUTH_TOKEN}" \
  http://localhost:8000/healthz
```

### Database Connection Issues

**Problem**: Backend fails to connect to database

```bash
# Check database is running
docker compose -f compose.yml --env-file .env ps db

# Verify DATABASE_URL format
# Should be: postgresql://user:pass@host:port/dbname
grep DATABASE_URL .env

# Check database logs
docker compose -f compose.yml --env-file .env logs db

# Run migrations manually
docker compose -f compose.yml --env-file .env exec backend npm run db:migrate
```

### Frontend Can't Reach API

**Problem**: API requests fail from frontend

```bash
# Check NEXT_PUBLIC_API_URL configuration
grep NEXT_PUBLIC_API_URL frontend/.env

# For auto mode, ensure BASE_URL is correct
grep BASE_URL .env

# Test API accessibility
curl http://localhost:8000/healthz

# Check CORS settings if using different origins
grep CORS_ORIGINS .env
```

### Port Conflicts

**Problem**: Ports 3000 or 8000 already in use

```bash
# Find processes using ports
lsof -i :3000
lsof -i :8000

# Change ports in .env
# Backend: PORT=8001
# Frontend: edit docker-compose.yml or run locally on different port
```

### Watch Mode Not Working

**Problem**: Docker Compose watch not detecting changes

```bash
# Verify Docker Compose version (requires 2.22.0+)
docker compose version

# Upgrade Docker Compose if needed
# Then restart with watch
docker compose -f compose.yml --env-file .env down
docker compose -f compose.yml --env-file .env up --build --watch
```

### Gateway Connection Failures

**Problem**: Gateway registration or execution fails

```bash
# Verify gateway endpoint is accessible
curl -H "Authorization: Bearer ${GATEWAY_AUTH_TOKEN}" \
  https://gateway.example.com/healthz

# Check gateway configuration
curl -H "Authorization: Bearer ${LOCAL_AUTH_TOKEN}" \
  http://localhost:8000/api/gateways

# Review gateway logs in activity timeline
curl -H "Authorization: Bearer ${LOCAL_AUTH_TOKEN}" \
  "http://localhost:8000/api/activity?resource_type=gateway"
```

### Reset Database

```bash
# Stop services
docker compose -f compose.yml --env-file .env down

# Remove database volume
docker volume rm openclaw-mission-control_postgres-data

# Restart and migrations will run automatically
docker compose -f compose.yml --env-file .env up -d --build
```

## Testing

```bash
# Backend tests
cd backend
npm test

# Frontend tests
cd frontend
npm test

# E2E tests (if configured)
npm run test:e2e
```

## Production Deployment

See [`docs/`](https://github.com/abhi1693/openclaw-mission-control/tree/master/docs) for:
- Production deployment guide
- Security hardening checklist
- Performance optimization
- Monitoring and observability setup
- Backup and disaster recovery
