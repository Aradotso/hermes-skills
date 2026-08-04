---
name: openclaw-saas-platform
description: Multi-tenant SaaS platform built on OpenClaw with auth, billing, workspace isolation, and AI agent execution gateway
triggers:
  - how do I set up openclaw saas
  - implement openclaw multi-tenant platform
  - create openclaw saas billing system
  - add user authentication to openclaw
  - configure openclaw workspace isolation
  - integrate openclaw agent gateway
  - deploy openclaw saas platform
  - test openclaw saas features
---

# OpenClaw SaaS Platform

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

OpenClaw SaaS transforms the open-source OpenClaw AI Agent into a multi-tenant SaaS platform with account management, credit-based billing, subscription orders, workspace isolation, and real-time agent execution. Built with FastAPI backend, React 19 frontend, and dual-container Docker architecture.

## Core Architecture

**Dual-Container Design:**
- **Backend Container**: FastAPI app handling auth, billing, orders, API gateway
- **OpenClaw Gateway Container**: Isolated AI agent execution engine
- **Shared Volume**: `/opt/workspaces/{agent_id}/` for file isolation
- **Dependencies**: MySQL 8.4, Redis 7

**Key Systems:**
- Account system with SMS verification + JWT
- Credit-based billing with transaction ledger
- Subscription orders with state machine
- Multi-user workspace isolation
- Real-time Socket.IO communication
- Rate limiting and blacklist protection

## Installation

### Local Development Setup

```bash
# 1. Clone and setup dependencies
git clone https://github.com/xingzhicn/openclaw-saas.git
cd openclaw-saas

# 2. Start MySQL + Redis via Docker
docker-compose -f docker-compose.local.yml up -d

# 3. Backend setup
python3.12 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# 4. Configure environment
cat > .env <<EOF
DATABASE_URL=mysql+aiomysql://openclaw:openclaw_dev_pwd@localhost:3307/openclaw_saas
REDIS_URL=redis://localhost:6380/0
ENCRYPTION_KEY=$(openssl rand -hex 32)
JWT_SECRET_KEY=$(openssl rand -hex 32)
OPENCLAW_TOKEN=$(openssl rand -hex 16)
WORKSPACE_BASE=/opt/workspaces
ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
OPENAI_API_KEY=${OPENAI_API_KEY}
DEBUG=true
EOF

# 5. Initialize database
python -c "from backend.app.database import init_db; import asyncio; asyncio.run(init_db())"

# 6. Start backend
uvicorn backend.app.main:app --reload --port 8000

# 7. Start frontend (new terminal)
cd frontend
npm install
npm run dev  # http://localhost:5173
```

### Production Deployment

```bash
# 1. Build Docker images
./scripts/pack-backend.sh
./scripts/pack-openclaw.sh

# 2. Transfer to server
scp openclaw-*.tar.gz deploy-*.sh root@your-server:/opt/

# 3. Deploy on server
ssh root@your-server
cd /opt
./deploy-all.sh
```

## Core API Patterns

### Authentication Flow

```python
# backend/app/api/auth.py
from fastapi import APIRouter, Depends, HTTPException
from backend.app.middleware.auth import get_current_user
from backend.app.models.user import User
from backend.app.utils.sms import send_sms_code
from backend.app.utils.jwt import create_access_token

router = APIRouter(prefix="/api/v1/auth", tags=["auth"])

@router.post("/send-code")
async def send_verification_code(request: dict, db: AsyncSession = Depends(get_db)):
    """Send SMS verification code with rate limiting"""
    phone = request["phone"]
    
    # Check rate limit: 1 request per 60s
    cache_key = f"sms:ratelimit:{phone}"
    if await redis_client.exists(cache_key):
        raise HTTPException(status_code=429, detail="请等待60秒后再试")
    
    # Check daily limit: 3 requests per day
    daily_key = f"sms:daily:{phone}"
    daily_count = await redis_client.get(daily_key)
    if daily_count and int(daily_count) >= 3:
        raise HTTPException(status_code=429, detail="今日发送次数已达上限")
    
    # Generate and send code
    code = generate_sms_code()
    await send_sms_code(phone, code)
    
    # Cache code for 5 minutes
    await redis_client.setex(f"sms:code:{phone}", 300, code)
    await redis_client.setex(cache_key, 60, "1")
    await redis_client.incr(daily_key)
    await redis_client.expire(daily_key, 86400)
    
    return {"message": "验证码已发送"}

@router.post("/login")
async def login(request: dict, db: AsyncSession = Depends(get_db)):
    """Login with phone and SMS code"""
    phone, code = request["phone"], request["code"]
    
    # Verify code
    cached_code = await redis_client.get(f"sms:code:{phone}")
    if not cached_code or cached_code != code:
        raise HTTPException(status_code=400, detail="验证码错误或已过期")
    
    # Get or create user
    result = await db.execute(select(User).where(User.phone == phone))
    user = result.scalar_one_or_none()
    
    if not user:
        user = User(phone=phone, credits=1000)  # 1000 initial credits
        db.add(user)
        await db.commit()
        await db.refresh(user)
    
    # Generate JWT
    token = create_access_token({"sub": str(user.id)})
    
    return {
        "access_token": token,
        "user": {
            "id": user.id,
            "phone": user.phone,
            "credits": user.credits
        }
    }
```

### Credit Billing System

```python
# backend/app/api/credits.py
from backend.app.models.credit_transaction import CreditTransaction, TransactionType
from backend.app.utils.credits import freeze_credits, consume_credits, refund_credits

@router.post("/freeze")
async def freeze_user_credits(
    request: dict,
    current_user: User = Depends(get_current_user),
    db: AsyncSession = Depends(get_db)
):
    """Freeze credits before agent execution"""
    amount = request["amount"]
    request_id = request["request_id"]
    
    # Check sufficient credits
    if current_user.credits < amount:
        raise HTTPException(status_code=400, detail="积分不足")
    
    # Freeze in Redis
    await redis_client.hset(
        f"freeze:{request_id}",
        mapping={"user_id": current_user.id, "amount": amount}
    )
    await redis_client.expire(f"freeze:{request_id}", 3600)
    
    # Deduct from user balance
    current_user.credits -= amount
    await db.commit()
    
    # Create freeze transaction
    transaction = CreditTransaction(
        user_id=current_user.id,
        amount=amount,
        type=TransactionType.FREEZE,
        description=f"冻结积分: {request_id}",
        request_id=request_id
    )
    db.add(transaction)
    await db.commit()
    
    return {"frozen_amount": amount, "remaining_credits": current_user.credits}

@router.post("/consume")
async def consume_frozen_credits(
    request: dict,
    db: AsyncSession = Depends(get_db)
):
    """Consume frozen credits after agent execution"""
    request_id = request["request_id"]
    actual_amount = request["actual_amount"]
    
    # Get freeze info
    freeze_data = await redis_client.hgetall(f"freeze:{request_id}")
    if not freeze_data:
        raise HTTPException(status_code=404, detail="冻结记录不存在")
    
    frozen_amount = int(freeze_data["amount"])
    user_id = int(freeze_data["user_id"])
    
    # Consume transaction
    transaction = CreditTransaction(
        user_id=user_id,
        amount=actual_amount,
        type=TransactionType.CONSUME,
        description=f"消耗积分: {request_id}",
        request_id=request_id
    )
    db.add(transaction)
    
    # Refund excess if any
    if actual_amount < frozen_amount:
        refund_amount = frozen_amount - actual_amount
        result = await db.execute(select(User).where(User.id == user_id))
        user = result.scalar_one()
        user.credits += refund_amount
        
        refund_tx = CreditTransaction(
            user_id=user_id,
            amount=refund_amount,
            type=TransactionType.REFUND,
            description=f"退回冻结余额: {request_id}",
            request_id=request_id
        )
        db.add(refund_tx)
    
    await redis_client.delete(f"freeze:{request_id}")
    await db.commit()
    
    return {"consumed": actual_amount, "refunded": frozen_amount - actual_amount}
```

### Workspace Isolation

```python
# backend/app/utils/workspace.py
import os
import shutil
from pathlib import Path

class WorkspaceManager:
    def __init__(self, base_path: str = "/opt/workspaces"):
        self.base_path = Path(base_path)
    
    def get_workspace_path(self, agent_id: str) -> Path:
        """Get isolated workspace path for agent"""
        workspace = self.base_path / str(agent_id)
        workspace.mkdir(parents=True, exist_ok=True)
        return workspace
    
    def create_workspace(self, agent_id: str) -> Path:
        """Create new workspace with initial structure"""
        workspace = self.get_workspace_path(agent_id)
        
        # Create subdirectories
        (workspace / "input").mkdir(exist_ok=True)
        (workspace / "output").mkdir(exist_ok=True)
        (workspace / "logs").mkdir(exist_ok=True)
        
        return workspace
    
    def cleanup_workspace(self, agent_id: str):
        """Remove workspace files (keep for 7 days in production)"""
        workspace = self.get_workspace_path(agent_id)
        if workspace.exists():
            shutil.rmtree(workspace)
    
    def write_input_file(self, agent_id: str, filename: str, content: str):
        """Write file to workspace input directory"""
        workspace = self.get_workspace_path(agent_id)
        input_path = workspace / "input" / filename
        input_path.write_text(content)
        return input_path
    
    def read_output_file(self, agent_id: str, filename: str) -> str:
        """Read file from workspace output directory"""
        workspace = self.get_workspace_path(agent_id)
        output_path = workspace / "output" / filename
        if not output_path.exists():
            raise FileNotFoundError(f"{filename} not found in output")
        return output_path.read_text()

# Usage in API
@router.post("/agents/execute")
async def execute_agent(
    request: dict,
    current_user: User = Depends(get_current_user),
    db: AsyncSession = Depends(get_db)
):
    agent_id = str(uuid.uuid4())
    workspace_manager = WorkspaceManager()
    
    # Create isolated workspace
    workspace = workspace_manager.create_workspace(agent_id)
    
    # Write input files
    workspace_manager.write_input_file(agent_id, "task.txt", request["task"])
    
    # Execute agent in OpenClaw Gateway container
    # (shares /opt/workspaces via Docker volume)
    result = await execute_openclaw_agent(agent_id, workspace)
    
    # Read output
    output = workspace_manager.read_output_file(agent_id, "result.txt")
    
    return {"agent_id": agent_id, "output": output}
```

### Agent Execution Gateway

```python
# backend/app/tasks/agent_executor.py
import httpx
from backend.app.config import settings

async def execute_openclaw_agent(agent_id: str, workspace: Path) -> dict:
    """Execute agent in isolated OpenClaw Gateway container"""
    
    # OpenClaw Gateway runs in separate container
    gateway_url = settings.OPENCLAW_GATEWAY_URL
    
    async with httpx.AsyncClient() as client:
        response = await client.post(
            f"{gateway_url}/execute",
            json={
                "agent_id": agent_id,
                "workspace": str(workspace),
                "model": "claude-3-5-sonnet-20241022",
                "api_key": settings.ANTHROPIC_API_KEY
            },
            headers={"Authorization": f"Bearer {settings.OPENCLAW_TOKEN}"},
            timeout=300.0
        )
        
        if response.status_code != 200:
            raise Exception(f"Agent execution failed: {response.text}")
        
        return response.json()

# Real-time status updates via Socket.IO
from socketio import AsyncServer

sio = AsyncServer(async_mode='asgi', cors_allowed_origins='*')

async def stream_agent_status(agent_id: str, user_id: int):
    """Stream agent execution status to frontend"""
    
    await sio.emit('agent_status', {
        'agent_id': agent_id,
        'status': 'running'
    }, room=f"user_{user_id}")
    
    # ... agent execution ...
    
    await sio.emit('agent_status', {
        'agent_id': agent_id,
        'status': 'completed',
        'result': result
    }, room=f"user_{user_id}")
```

## Testing Patterns

OpenClaw SaaS uses "real" testing with database/Redis operations and four-dimensional verification:

```python
# tests/test_credits.py
import pytest
from tests.helpers.verifier import verify_db, verify_cache, verify_workspace, verify_response

def test_freeze_consume_workflow(client, auth_headers, test_user, db, redis_client):
    """Test complete freeze -> consume -> refund workflow"""
    request_id = "req_freeze_001"
    
    # 1. Freeze credits
    response = client.post(
        "/api/v1/credits/freeze",
        json={"amount": 500, "request_id": request_id},
        headers=auth_headers
    )
    
    verify_response(response, 200, {"frozen_amount": 500})
    verify_db(db, "credit_transactions", {
        "user_id": test_user.id,
        "request_id": request_id,
        "type": "freeze"
    }, {"amount": 500})
    verify_cache(redis_client, f"freeze:{request_id}", {
        "user_id": str(test_user.id),
        "amount": "500"
    })
    
    # 2. Consume credits (less than frozen)
    response = client.post(
        "/api/v1/credits/consume",
        json={"request_id": request_id, "actual_amount": 300}
    )
    
    verify_response(response, 200, {"consumed": 300, "refunded": 200})
    verify_db(db, "credit_transactions", {
        "request_id": request_id,
        "type": "consume"
    }, {"amount": 300})
    verify_db(db, "credit_transactions", {
        "request_id": request_id,
        "type": "refund"
    }, {"amount": 200})
    
    # Verify cache cleanup
    assert not redis_client.exists(f"freeze:{request_id}")

def test_workspace_isolation(client, auth_headers, test_user, workspace_manager):
    """Test workspace file isolation between users"""
    agent_id_1 = "agent_user1"
    agent_id_2 = "agent_user2"
    
    # User 1 workspace
    workspace_manager.write_input_file(agent_id_1, "task.txt", "User 1 task")
    verify_workspace(agent_id_1, expected_files=["input/task.txt"])
    
    # User 2 workspace (isolated)
    workspace_manager.write_input_file(agent_id_2, "task.txt", "User 2 task")
    verify_workspace(agent_id_2, expected_files=["input/task.txt"])
    
    # Verify isolation
    content_1 = workspace_manager.read_output_file(agent_id_1, "../input/task.txt")
    assert content_1 == "User 1 task"
    
    content_2 = workspace_manager.read_output_file(agent_id_2, "../input/task.txt")
    assert content_2 == "User 2 task"
```

Run tests:

```bash
# All tests
pytest tests/ -v

# Specific module
pytest tests/test_credits.py -v

# With coverage
pytest tests/ --cov=backend/app --cov-report=html
```

## Frontend Integration

```typescript
// frontend/src/services/api.ts
import axios from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL || 'http://localhost:8000',
  headers: { 'Content-Type': 'application/json' }
});

// Auto-attach JWT token
api.interceptors.request.use(config => {
  const token = localStorage.getItem('access_token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Auth API
export const authAPI = {
  sendCode: (phone: string) => api.post('/api/v1/auth/send-code', { phone }),
  login: (phone: string, code: string) => api.post('/api/v1/auth/login', { phone, code }),
  getCurrentUser: () => api.post('/api/v1/auth/me')
};

// Credits API
export const creditsAPI = {
  getBalance: () => api.post('/api/v1/credits/balance'),
  getTransactions: (page = 1, size = 20) => 
    api.post('/api/v1/credits/transactions', { page, size }),
  freezeCredits: (amount: number, requestId: string) =>
    api.post('/api/v1/credits/freeze', { amount, request_id: requestId })
};

// Agent API
export const agentAPI = {
  execute: (task: string) => api.post('/api/v1/agents/execute', { task }),
  getStatus: (agentId: string) => api.post(`/api/v1/agents/${agentId}/status`)
};
```

```typescript
// frontend/src/hooks/useAgentExecution.ts
import { io } from 'socket.io-client';
import { useEffect, useState } from 'react';

export function useAgentExecution(userId: number) {
  const [status, setStatus] = useState<string>('idle');
  const [result, setResult] = useState<any>(null);
  
  useEffect(() => {
    const socket = io(import.meta.env.VITE_API_URL);
    
    socket.emit('join', `user_${userId}`);
    
    socket.on('agent_status', (data) => {
      setStatus(data.status);
      if (data.status === 'completed') {
        setResult(data.result);
      }
    });
    
    return () => { socket.disconnect(); };
  }, [userId]);
  
  return { status, result };
}
```

## Configuration

Key environment variables:

```bash
# Database
DATABASE_URL=mysql+aiomysql://user:pass@host:3306/dbname

# Redis
REDIS_URL=redis://host:6379/0

# Security
ENCRYPTION_KEY=<64-char-hex>  # openssl rand -hex 32
JWT_SECRET_KEY=<64-char-hex>
JWT_ALGORITHM=HS256
JWT_EXPIRATION_HOURS=720

# OpenClaw Gateway
OPENCLAW_GATEWAY_URL=http://openclaw-gateway:8001
OPENCLAW_TOKEN=<32-char-hex>  # openssl rand -hex 16
WORKSPACE_BASE=/opt/workspaces

# AI Models
ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
OPENAI_API_KEY=${OPENAI_API_KEY}

# Features
DEBUG=false
ENABLE_SMS=true
SMS_PROVIDER=aliyun
```

## Common Troubleshooting

**SMS code not received (DEBUG mode):**
```python
# In DEBUG mode, code is always "123456"
if settings.DEBUG:
    code = "123456"
```

**Workspace permission denied:**
```bash
# Ensure shared volume has correct permissions
chown -R 1000:1000 /opt/workspaces
chmod -R 755 /opt/workspaces
```

**Database connection pool exhausted:**
```python
# backend/app/database.py
engine = create_async_engine(
    DATABASE_URL,
    pool_size=20,          # Increase from default 5
    max_overflow=40,       # Increase from default 10
    pool_pre_ping=True
)
```

**Redis connection timeout:**
```python
# backend/app/database.py
redis_client = redis.from_url(
    REDIS_URL,
    decode_responses=True,
    socket_timeout=5.0,
    socket_connect_timeout=5.0
)
```

**JWT token expired:**
```typescript
// frontend/src/services/api.ts
api.interceptors.response.use(
  response => response,
  error => {
    if (error.response?.status === 401) {
      localStorage.removeItem('access_token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);
```

## Production Deployment Checklist

1. **Security:**
   - [ ] Set `DEBUG=false`
   - [ ] Rotate `ENCRYPTION_KEY` and `JWT_SECRET_KEY`
   - [ ] Enable HTTPS with valid SSL certificate
   - [ ] Configure firewall rules (only 80/443 public)

2. **Database:**
   - [ ] Enable MySQL slow query log
   - [ ] Configure automated backups (daily)
   - [ ] Set proper connection pool limits

3. **Monitoring:**
   - [ ] Setup Sentry for error tracking
   - [ ] Configure log aggregation (ELK/Loki)
   - [ ] Enable Prometheus metrics

4. **Scaling:**
   - [ ] Use Redis Sentinel for HA
   - [ ] Deploy multiple backend replicas behind load balancer
   - [ ] Setup auto-scaling for OpenClaw Gateway containers

5. **Workspace Cleanup:**
   - [ ] Schedule cron job to delete workspaces older than 7 days
   ```bash
   0 2 * * * find /opt/workspaces -mtime +7 -exec rm -rf {} \;
   ```
