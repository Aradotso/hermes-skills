---
name: runbookhermes-aiops-agent
description: Hermes-native AIOps agent for evidence-driven incident response, approval-gated remediation, and runbook learning
triggers:
  - set up runbookhermes for incident response
  - configure aiops agent with hermes
  - create incident response workflow with runbookhermes
  - integrate observability tools for automated remediation
  - build approval-gated remediation pipeline
  - generate runbook skills from incident data
  - deploy hermes agent for production incidents
  - configure evidence-driven root cause analysis
---

# RunbookHermes AIOps Agent Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

RunbookHermes is a Hermes-native AIOps agent that specializes in incident response workflows. It extends Hermes Agent's runtime with evidence collection from observability tools (Prometheus, Loki, Jaeger), approval-gated remediation, checkpoint/rollback capabilities, and automatic runbook skill generation from resolved incidents.

## What RunbookHermes Does

- **Evidence-driven incident analysis**: Collects metrics, logs, traces, and deployment history
- **Approval-gated remediation**: Requires human approval before risky actions
- **Runbook learning**: Converts successful incident resolutions into reusable skills
- **Multi-channel intake**: Accepts incidents from Web UI, Alertmanager, Feishu, WeCom, API
- **EvidenceStack context engine**: Compresses observability data for model reasoning
- **IncidentMemory**: Remembers service profiles, incident patterns, team preferences

## Installation

### Prerequisites

- Python 3.10+
- Hermes Agent (included as `agent/` subdirectory)
- Docker and Docker Compose (for local payment demo environment)

### Clone and Install

```bash
git clone https://github.com/Tommy-yw/RunbookHermes.git
cd RunbookHermes

# Install dependencies
pip install -r requirements.txt

# Or use Poetry
poetry install
```

### Environment Configuration

Create `.env` file in project root:

```bash
# Model provider (optional, for AI-assisted summaries)
OPENAI_API_KEY=${OPENAI_API_KEY}
OPENAI_BASE_URL=https://api.openai.com/v1
OPENAI_MODEL=gpt-4o

# Observability backends
PROMETHEUS_URL=http://localhost:9090
LOKI_URL=http://localhost:3100
JAEGER_URL=http://localhost:16686

# Deploy history backend
DEPLOY_BACKEND_TYPE=local_json
DEPLOY_HISTORY_PATH=./data/payment_demo/deploy_history.json

# Execution backend (for rollback/remediation)
EXECUTION_BACKEND_TYPE=local_reference
EXECUTION_CONFIG_PATH=./data/payment_demo/execution_config.json

# Feishu integration (optional)
FEISHU_APP_ID=${FEISHU_APP_ID}
FEISHU_APP_SECRET=${FEISHU_APP_SECRET}

# WeCom integration (optional)
WECOM_CORP_ID=${WECOM_CORP_ID}
WECOM_AGENT_SECRET=${WECOM_AGENT_SECRET}

# Web API
RUNBOOK_API_HOST=0.0.0.0
RUNBOOK_API_PORT=8000
```

### Start Local Payment Demo Environment

```bash
cd demo/payment_system
docker-compose up -d
cd ../..

# Verify services are running
curl http://localhost:8001/health  # payment-service
curl http://localhost:8002/health  # coupon-service
curl http://localhost:8003/health  # order-service
```

### Start RunbookHermes API Server

```bash
# From project root
python -m apps.runbook_api.main

# Or with uvicorn directly
uvicorn apps.runbook_api.main:app --host 0.0.0.0 --port 8000 --reload
```

Access Web Console at `http://localhost:8000`

## Core Concepts

### 1. Hermes Profile Integration

RunbookHermes runs as a Hermes Agent profile located at `profiles/runbook-hermes/`:

```yaml
# profiles/runbook-hermes/profile.yaml
name: runbook-hermes
version: 1.0.0
description: AIOps agent for incident response
persona: incident_responder
tools:
  - runbook-hermes
context_engine: evidence_stack
memory_provider: incident_memory
```

### 2. Evidence Collection Tools

The `runbook-hermes` tool plugin provides incident-response capabilities:

```python
# Example: Query metrics evidence
from plugins.runbook_hermes.tools import query_metrics

evidence = query_metrics(
    service="payment-service",
    metric_type="http_5xx_rate",
    time_window="5m"
)
```

Available tools in the plugin:
- `query_metrics` - Prometheus metrics collection
- `query_logs` - Loki log search
- `query_traces` - Jaeger trace analysis
- `get_deploy_history` - Recent deployment records
- `create_checkpoint` - Save system state before remediation
- `request_approval` - Gate risky actions
- `execute_rollback` - Controlled rollback execution
- `verify_recovery` - Post-remediation health check

### 3. EvidenceStack Context Engine

Compresses observability data for model consumption:

```python
from plugins.context_engine.evidence_stack.engine import EvidenceStackEngine

engine = EvidenceStackEngine()

# Add evidence
engine.add_evidence({
    "type": "metric",
    "service": "payment-service",
    "signal": "http_503_rate_spike",
    "value": "45 req/s",
    "severity": "critical"
})

# Get compressed context
context = engine.get_context()
# Returns: alert summary, key evidence, hypotheses, action plan
```

### 4. IncidentMemory Provider

Stores operational knowledge:

```python
from plugins.memory.incident_memory.provider import IncidentMemoryProvider

memory = IncidentMemoryProvider()

# Remember service profile
memory.save_service_profile("payment-service", {
    "critical_metrics": ["http_5xx_rate", "p95_latency"],
    "dependencies": ["coupon-service", "order-service"],
    "rollback_safe": True
})

# Recall incident patterns
similar = memory.recall_similar_incidents(
    service="payment-service",
    symptom="http_503_spike"
)
```

## Creating and Managing Incidents

### Via Web Console

Navigate to `http://localhost:8000/incidents/create` and fill the form:

- Service name
- Severity (critical, high, medium, low)
- Description
- Alert data (optional)

### Via API

```python
import requests

response = requests.post("http://localhost:8000/api/incidents", json={
    "service": "payment-service",
    "severity": "critical",
    "description": "HTTP 503 rate spike detected",
    "alert": {
        "metric": "http_5xx_rate",
        "value": 45.2,
        "threshold": 5.0
    },
    "metadata": {
        "source": "alertmanager",
        "runbook_url": "https://wiki.example.com/payment-503"
    }
})

incident_id = response.json()["incident_id"]
```

### Via Hermes CLI

```bash
# Run incident response through Hermes profile
hermes run \
  --profile runbook-hermes \
  --input "Payment service showing HTTP 503 errors at 45 req/s" \
  --context '{"service": "payment-service", "severity": "critical"}'
```

### Via Alertmanager Webhook

Configure Alertmanager to send webhooks:

```yaml
# alertmanager.yml
receivers:
  - name: runbook-hermes
    webhook_configs:
      - url: http://localhost:8000/gateway/alertmanager
        send_resolved: true
```

## Approval Workflow

RunbookHermes gates risky actions behind approval:

```python
# In your incident response logic
from runbook_hermes.approval import ApprovalManager

approval_mgr = ApprovalManager()

# Request approval for rollback
approval_id = approval_mgr.request_approval(
    incident_id="inc_001",
    action_type="rollback",
    target_service="payment-service",
    target_version="v1.2.3",
    risk_level="high",
    reason="Rollback to last known good version due to 503 spike",
    checkpoint_id="chk_001"
)

# Check approval status
status = approval_mgr.get_status(approval_id)
if status == "approved":
    # Execute rollback
    execute_rollback(service="payment-service", version="v1.2.3")
```

### Approve via Web Console

Navigate to `http://localhost:8000/approvals` to review and approve/reject pending actions.

### Approve via API

```python
requests.post(f"http://localhost:8000/api/approvals/{approval_id}/approve", json={
    "operator": "alice",
    "comment": "Approved after verifying checkpoint"
})
```

## Checkpoint and Rollback

### Create Checkpoint Before Remediation

```python
from runbook_hermes.checkpoint import CheckpointManager

checkpoint_mgr = CheckpointManager()

checkpoint = checkpoint_mgr.create(
    incident_id="inc_001",
    service="payment-service",
    snapshot_type="deployment",
    metadata={
        "current_version": "v1.2.4",
        "replica_count": 3,
        "config_hash": "abc123"
    }
)
```

### Execute Rollback

```python
from runbook_hermes.remediation import RemediationExecutor

executor = RemediationExecutor()

result = executor.rollback(
    service="payment-service",
    target_version="v1.2.3",
    checkpoint_id=checkpoint.id,
    dry_run=False
)

# Verify recovery
recovery_status = executor.verify_recovery(
    service="payment-service",
    expected_metrics={"http_5xx_rate": "<5"}
)
```

## Runbook Skill Generation

After resolving an incident, generate a reusable skill:

```python
from runbook_hermes.skills import SkillGenerator

generator = SkillGenerator()

skill = generator.generate_from_incident(
    incident_id="inc_001",
    skill_name="payment-http-503-rollback",
    trigger_conditions=["payment service 503 spike", "payment 5xx rate > 40"],
    steps=[
        "collect_evidence",
        "verify_deploy_change",
        "create_checkpoint",
        "request_approval",
        "rollback_deployment",
        "verify_recovery"
    ]
)

# Save to skills directory
skill.save("skills/runbooks/payment-http-503-rollback.yaml")
```

Generated skill format:

```yaml
# skills/runbooks/payment-http-503-rollback.yaml
name: payment-http-503-rollback
version: 1.0.0
triggers:
  - payment service 503 spike
  - payment 5xx rate > 40
steps:
  - name: collect_evidence
    tool: query_metrics
    params:
      service: payment-service
      metric: http_5xx_rate
  - name: verify_deploy_change
    tool: get_deploy_history
    params:
      service: payment-service
      limit: 5
  - name: create_checkpoint
    tool: create_checkpoint
  - name: request_approval
    tool: request_approval
    risk_level: high
  - name: rollback_deployment
    tool: execute_rollback
  - name: verify_recovery
    tool: verify_recovery
```

## Observability Integration

### Prometheus Metrics

```python
from integrations.observability.prometheus_adapter import PrometheusAdapter

prom = PrometheusAdapter(base_url="http://localhost:9090")

# Query current 5xx rate
result = prom.query_range(
    query='rate(http_requests_total{status=~"5..", service="payment-service"}[5m])',
    start="-15m",
    end="now",
    step="30s"
)

# Extract evidence
if result.has_spike(threshold=5.0):
    evidence = {
        "type": "metric",
        "signal": "http_5xx_spike",
        "max_value": result.max_value(),
        "timestamp": result.max_timestamp()
    }
```

### Loki Logs

```python
from integrations.observability.loki_adapter import LokiAdapter

loki = LokiAdapter(base_url="http://localhost:3100")

# Search error logs
logs = loki.query_range(
    query='{service="payment-service"} |= "error" | json',
    start="-15m",
    limit=100
)

# Extract patterns
error_patterns = logs.extract_patterns(min_frequency=5)
```

### Jaeger Traces

```python
from integrations.observability.jaeger_adapter import JaegerAdapter

jaeger = JaegerAdapter(base_url="http://localhost:16686")

# Find slow traces
traces = jaeger.search_traces(
    service="payment-service",
    start="-15m",
    min_duration="500ms",
    limit=20
)

# Analyze error traces
for trace in traces.with_errors():
    root_cause_span = trace.find_slowest_span()
```

## Running Hermes Agent with RunbookHermes Profile

### Direct CLI Invocation

```bash
# Run incident triage
hermes run \
  --profile runbook-hermes \
  --input "Payment service p95 latency is 2.5s, normal is 200ms" \
  --verbose

# Run with specific tool selection
hermes run \
  --profile runbook-hermes \
  --input "Check payment service deployment history" \
  --tools query_metrics,get_deploy_history
```

### Programmatic Invocation

```python
from agent.runtime import HermesRuntime
from agent.config import AgentConfig

config = AgentConfig(
    profile="runbook-hermes",
    tools=["runbook-hermes"],
    context_engine="evidence_stack",
    memory_provider="incident_memory"
)

runtime = HermesRuntime(config)

response = runtime.run(
    input_text="Investigate payment-service HTTP 503 spike",
    context={
        "service": "payment-service",
        "incident_id": "inc_001",
        "severity": "critical"
    }
)

print(response.final_answer)
print(response.evidence_chain)
print(response.recommended_actions)
```

## Common Patterns

### Pattern 1: Full Incident Response Workflow

```python
from runbook_hermes.workflow import IncidentResponseWorkflow

workflow = IncidentResponseWorkflow()

# Execute end-to-end
result = workflow.execute(
    service="payment-service",
    symptom="http_503_spike",
    severity="critical",
    auto_approve=False  # Require human approval
)

print(f"Root cause: {result.root_cause}")
print(f"Remediation: {result.remediation_action}")
print(f"Status: {result.status}")
```

### Pattern 2: Evidence-Driven Diagnosis

```python
from runbook_hermes.diagnosis import EvidenceDiagnosis

diagnosis = EvidenceDiagnosis(service="payment-service")

# Collect all evidence types
diagnosis.collect_metrics(time_window="15m")
diagnosis.collect_logs(time_window="15m", error_only=True)
diagnosis.collect_traces(time_window="15m", min_duration="500ms")
diagnosis.collect_deploy_history(limit=10)

# Analyze
root_cause = diagnosis.analyze()

print(f"Most likely cause: {root_cause.hypothesis}")
print(f"Confidence: {root_cause.confidence}")
print(f"Supporting evidence: {root_cause.evidence_ids}")
```

### Pattern 3: Safe Remediation with Approval

```python
from runbook_hermes.remediation import SafeRemediation

remediation = SafeRemediation(incident_id="inc_001")

# Plan action
plan = remediation.plan_rollback(
    service="payment-service",
    target_version="v1.2.3"
)

# Create checkpoint
checkpoint = remediation.create_checkpoint()

# Request approval (blocks until human decision)
approval = remediation.request_approval(
    action=plan,
    checkpoint=checkpoint,
    timeout_minutes=30
)

if approval.is_approved():
    # Execute with dry-run first
    dry_run_result = remediation.execute(dry_run=True)
    
    if dry_run_result.success:
        # Real execution
        result = remediation.execute(dry_run=False)
        
        # Verify recovery
        if remediation.verify_recovery():
            print("Remediation successful")
        else:
            # Auto-rollback to checkpoint
            remediation.restore_checkpoint(checkpoint.id)
```

### Pattern 4: Multi-Service Impact Analysis

```python
from runbook_hermes.topology import ServiceTopology

topology = ServiceTopology()

# Build dependency graph
graph = topology.build_graph(
    root_service="payment-service",
    depth=2
)

# Analyze impact
impact = topology.analyze_impact(
    failing_service="payment-service",
    failure_type="http_503"
)

print(f"Directly impacted: {impact.direct}")
print(f"Indirectly impacted: {impact.indirect}")
print(f"Suggested investigation order: {impact.priority_list}")
```

## Configuration Reference

### RunbookHermes Config File

Create `config/runbook_hermes.yaml`:

```yaml
# Incident response settings
incident:
  auto_create_from_alert: true
  default_severity: high
  evidence_collection_timeout: 300  # seconds
  
# Evidence collection
evidence:
  metrics:
    enabled: true
    time_window: 15m
    retention_days: 30
  logs:
    enabled: true
    max_lines: 1000
    error_patterns_only: false
  traces:
    enabled: true
    sample_limit: 100
    min_duration: 200ms
    
# Approval settings
approval:
  required_for:
    - rollback
    - restart
    - config_change
    - scale_down
  auto_approve_on_critical: false
  approval_timeout_minutes: 30
  require_checkpoint: true
  
# Remediation
remediation:
  dry_run_first: true
  verify_recovery: true
  recovery_check_interval: 30  # seconds
  max_recovery_wait: 300  # seconds
  auto_rollback_on_failure: true
  
# Runbook skill generation
skills:
  auto_generate: true
  min_success_count: 1
  output_dir: skills/runbooks
  
# Model-assisted analysis (optional)
model:
  enabled: true
  provider: openai
  temperature: 0.3
  max_tokens: 2000
```

### Tool Configuration

```yaml
# plugins/runbook_hermes/config.yaml
tools:
  query_metrics:
    timeout: 30
    max_results: 1000
  query_logs:
    timeout: 60
    max_lines: 5000
  query_traces:
    timeout: 45
    max_traces: 200
  execute_rollback:
    require_approval: true
    require_checkpoint: true
    dry_run_first: true
```

## Troubleshooting

### Issue: Evidence collection returns empty results

**Cause**: Observability backends not reachable or no data in time window

**Solution**:
```python
# Test backend connectivity
from integrations.observability.health import check_backends

health = check_backends()
print(f"Prometheus: {health['prometheus']}")
print(f"Loki: {health['loki']}")
print(f"Jaeger: {health['jaeger']}")

# Verify time window
# Ensure time_window matches your metric retention
evidence = query_metrics(
    service="payment-service",
    time_window="1h"  # Increase window
)
```

### Issue: Approval requests timeout

**Cause**: No operator reviewing approvals in time

**Solution**:
```yaml
# config/runbook_hermes.yaml
approval:
  approval_timeout_minutes: 60  # Increase timeout
  fallback_to_auto_reject: false  # Prevent auto-reject
  
# Or configure notification
notification:
  on_approval_request:
    - type: feishu
      webhook_url: ${FEISHU_APPROVAL_WEBHOOK}
```

### Issue: Runbook skills not generating

**Cause**: Incident not marked as resolved or missing evidence

**Solution**:
```python
# Explicitly mark incident resolved
from runbook_hermes.incident import IncidentManager

mgr = IncidentManager()
mgr.mark_resolved(
    incident_id="inc_001",
    resolution="Rolled back to v1.2.3",
    root_cause="Bad deployment v1.2.4"
)

# Manually trigger skill generation
from runbook_hermes.skills import SkillGenerator

generator = SkillGenerator()
skill = generator.generate_from_incident("inc_001")
skill.save()
```

### Issue: Model-assisted summaries failing

**Cause**: Model API key not configured or endpoint unreachable

**Solution**:
```bash
# Verify environment variables
echo $OPENAI_API_KEY
echo $OPENAI_BASE_URL

# Test model connectivity
curl $OPENAI_BASE_URL/models \
  -H "Authorization: Bearer $OPENAI_API_KEY"

# Disable model if not needed
# config/runbook_hermes.yaml
model:
  enabled: false  # Fall back to evidence-only mode
```

### Issue: Hermes profile not found

**Cause**: Profile directory not in Hermes search path

**Solution**:
```bash
# Add RunbookHermes profiles to Hermes config
export HERMES_PROFILE_PATH="./profiles/runbook-hermes:$HERMES_PROFILE_PATH"

# Or copy profile to Hermes profiles directory
cp -r profiles/runbook-hermes ~/.hermes/profiles/
```

### Debug Mode

Enable verbose logging:

```python
import logging
logging.basicConfig(level=logging.DEBUG)

# Or set environment variable
export RUNBOOK_HERMES_LOG_LEVEL=DEBUG
```

```bash
# Run with debug output
hermes run \
  --profile runbook-hermes \
  --input "Debug payment service issue" \
  --debug \
  --trace-tools
```

## Advanced Usage

### Custom Tool Integration

Add domain-specific tools:

```python
# plugins/runbook_hermes/custom_tools.py
from agent.tools import Tool, ToolParameter

class CheckDatabaseConnectionTool(Tool):
    name = "check_database_connection"
    description = "Verify database connectivity and connection pool status"
    
    parameters = [
        ToolParameter(name="service", type="string", required=True),
        ToolParameter(name="db_name", type="string", required=True)
    ]
    
    def execute(self, service: str, db_name: str) -> dict:
        # Your custom logic
        return {
            "status": "healthy",
            "active_connections": 25,
            "max_connections": 100
        }

# Register tool
from plugins.runbook_hermes.registry import register_tool
register_tool(CheckDatabaseConnectionTool())
```

### Custom Evidence Type

```python
# runbook_hermes/evidence/custom_evidence.py
from runbook_hermes.evidence import EvidenceCollector

class CostEvidenceCollector(EvidenceCollector):
    def collect(self, service: str, time_window: str) -> dict:
        # Collect cost metrics from billing API
        return {
            "type": "cost_spike",
            "service": service,
            "cost_increase_pct": 150,
            "period": time_window
        }

# Register collector
from runbook_hermes.evidence import register_collector
register_collector("cost", CostEvidenceCollector())
```

This skill enables AI coding agents to help developers deploy, configure, and operate RunbookHermes for production incident response with Hermes Agent integration.
