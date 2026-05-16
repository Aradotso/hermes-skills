---
name: hermes-agent-framework
description: Expert guide for building, configuring, and deploying AI agents using Nous Research's Hermes Agent framework with self-improving learning loops and Skills
triggers:
  - "help me set up hermes agent"
  - "how do I create a custom skill for hermes"
  - "configure hermes agent memory system"
  - "build an AI agent with hermes framework"
  - "hermes agent learning loop explained"
  - "integrate tools with hermes agent"
  - "troubleshoot hermes agent installation"
  - "create multi-agent system with hermes"
---

# Hermes Agent Framework

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

Expert guide for **Hermes Agent** by Nous Research — an open-source AI Agent framework with built-in self-improving learning loops, three-layer memory system, and automatic Skill creation and evolution.

## What is Hermes Agent?

Hermes Agent is a production-ready AI agent framework that implements:
- **Self-improving learning loop**: Agents learn from interactions and improve over time
- **Three-layer memory system**: Short-term, working, and long-term memory
- **Skills system**: Reusable, composable capabilities that agents can create and evolve
- **Tool ecosystem**: Extensible integration with external tools and APIs
- **Harness Engineering**: Built-in instructions, constraints, feedback, memory, and orchestration

Unlike traditional agentic frameworks, Hermes ships with "reins" — structured control mechanisms that keep agents aligned while allowing autonomous improvement.

## Installation

### Prerequisites
- Python 3.9+
- Node.js 16+ (for web interface)
- API keys for LLM providers (OpenAI, Anthropic, or local models)

### Quick Start

```bash
# Clone the repository
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent

# Install Python dependencies
pip install -r requirements.txt

# Set up environment variables
cp .env.example .env
# Edit .env with your API keys

# Initialize the agent
python -m hermes init

# Start the agent
python -m hermes start
```

### Environment Configuration

```bash
# .env
HERMES_LLM_PROVIDER=openai  # or anthropic, local
OPENAI_API_KEY=${OPENAI_API_KEY}
ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}

# Memory settings
HERMES_MEMORY_BACKEND=sqlite  # or postgres, redis
HERMES_MEMORY_PATH=./data/memory.db

# Learning loop settings
HERMES_LEARNING_ENABLED=true
HERMES_FEEDBACK_THRESHOLD=0.7

# Skills directory
HERMES_SKILLS_PATH=./skills
```

## Core Concepts

### 1. Learning Loop

The self-improving cycle that allows agents to learn from interactions:

```python
from hermes import Agent, LearningConfig

# Configure learning parameters
learning_config = LearningConfig(
    enabled=True,
    feedback_threshold=0.7,
    improvement_interval=10,  # iterations
    memory_consolidation=True
)

agent = Agent(
    name="my-agent",
    learning_config=learning_config
)

# Agent automatically learns from interactions
response = agent.run("Help me analyze this dataset")

# View learning metrics
metrics = agent.get_learning_metrics()
print(f"Improvement rate: {metrics['improvement_rate']}")
print(f"Skills acquired: {metrics['skills_acquired']}")
```

### 2. Three-Layer Memory System

```python
from hermes import Agent, MemoryConfig

memory_config = MemoryConfig(
    short_term_size=50,      # Recent interactions
    working_memory_size=10,  # Current context
    long_term_backend="sqlite",  # Persistent storage
    retrieval_method="semantic"  # or "keyword", "hybrid"
)

agent = Agent(name="memory-agent", memory_config=memory_config)

# Memory is automatically managed
agent.remember("User prefers Python over JavaScript")
agent.remember("Project uses FastAPI framework")

# Query memory
context = agent.recall("What framework does the project use?")
print(context)  # Retrieves relevant memories

# Memory consolidation (moves important short-term to long-term)
agent.consolidate_memory()
```

### 3. Skills System

Skills are reusable capabilities that agents can learn, create, and evolve:

```python
from hermes import Agent, Skill

# Define a custom skill
@Skill(
    name="python-debugger",
    description="Debug Python code by analyzing errors and suggesting fixes",
    triggers=["debug this code", "fix this error", "what's wrong with this"]
)
def debug_python(code: str, error: str) -> dict:
    """
    Analyzes Python code and errors to suggest fixes.
    """
    analysis = {
        "error_type": extract_error_type(error),
        "root_cause": analyze_root_cause(code, error),
        "suggested_fix": generate_fix(code, error),
        "explanation": explain_fix()
    }
    return analysis

# Register skill with agent
agent = Agent(name="dev-agent")
agent.register_skill(debug_python)

# Agent automatically uses skill when triggered
response = agent.run("Debug this code: print(undefined_var)")
```

### Creating Skills from YAML

```yaml
# skills/code-reviewer.yaml
name: code-reviewer
description: Review code for quality, security, and best practices
triggers:
  - "review this code"
  - "check code quality"
  - "security audit"

parameters:
  language: string
  code: string
  focus_areas:
    type: array
    default: ["quality", "security", "performance"]

implementation: |
  def review_code(language, code, focus_areas):
      results = {}
      
      if "quality" in focus_areas:
          results["quality"] = analyze_code_quality(code, language)
      
      if "security" in focus_areas:
          results["security"] = scan_security_issues(code, language)
      
      if "performance" in focus_areas:
          results["performance"] = check_performance(code, language)
      
      return {
          "summary": generate_summary(results),
          "details": results,
          "recommendations": generate_recommendations(results)
      }
```

Load skills from directory:

```python
from hermes import Agent

agent = Agent(name="code-agent")
agent.load_skills_from_directory("./skills")

# Skills are now available
response = agent.run("Review this Python function for security issues")
```

## Tool Integration

### Built-in Tools

```python
from hermes import Agent
from hermes.tools import WebSearch, FileSystem, CodeExecution, Database

agent = Agent(
    name="assistant",
    tools=[
        WebSearch(api_key="${SERPER_API_KEY}"),
        FileSystem(allowed_paths=["./workspace"]),
        CodeExecution(languages=["python", "javascript"]),
        Database(connection_string="${DATABASE_URL}")
    ]
)

# Agent automatically selects and uses tools
response = agent.run("Search for the latest Python best practices and save them to a file")
```

### Custom Tools

```python
from hermes.tools import Tool, ToolParameter

class CustomAPI(Tool):
    name = "custom-api"
    description = "Interact with custom REST API"
    
    parameters = [
        ToolParameter(
            name="endpoint",
            type="string",
            description="API endpoint path",
            required=True
        ),
        ToolParameter(
            name="method",
            type="string",
            description="HTTP method",
            default="GET"
        )
    ]
    
    def execute(self, endpoint: str, method: str = "GET", **kwargs):
        import requests
        base_url = os.getenv("CUSTOM_API_BASE_URL")
        api_key = os.getenv("CUSTOM_API_KEY")
        
        headers = {"Authorization": f"Bearer {api_key}"}
        url = f"{base_url}/{endpoint}"
        
        response = requests.request(method, url, headers=headers, **kwargs)
        return response.json()

# Register custom tool
agent.register_tool(CustomAPI())
```

## Configuration Patterns

### Agent Configuration File

```yaml
# agent-config.yaml
name: production-agent
model:
  provider: openai
  model: gpt-4
  temperature: 0.7
  max_tokens: 2000

memory:
  backend: postgres
  connection: ${DATABASE_URL}
  short_term_size: 100
  consolidation_interval: 1000

learning:
  enabled: true
  feedback_threshold: 0.75
  improvement_interval: 50
  auto_skill_creation: true

tools:
  - name: web-search
    config:
      api_key: ${SERPER_API_KEY}
      max_results: 10
  
  - name: file-system
    config:
      allowed_paths:
        - ./workspace
        - ./data
      denied_patterns:
        - "*.env"
        - "*.key"
  
  - name: code-execution
    config:
      languages: [python, javascript, bash]
      timeout: 30
      sandbox: true

constraints:
  - "Never expose API keys or secrets"
  - "Always validate user input"
  - "Request confirmation for destructive operations"
  - "Explain reasoning before executing complex tasks"

skills_directory: ./skills
```

Load configuration:

```python
from hermes import Agent

agent = Agent.from_config("agent-config.yaml")
agent.start()
```

### Multi-Agent System

```python
from hermes import Agent, Orchestrator

# Create specialized agents
research_agent = Agent(
    name="researcher",
    model="gpt-4",
    tools=["web-search", "pdf-reader"],
    skills=["research-synthesis", "fact-checking"]
)

coding_agent = Agent(
    name="coder",
    model="gpt-4",
    tools=["file-system", "code-execution"],
    skills=["code-generation", "debugging", "refactoring"]
)

writing_agent = Agent(
    name="writer",
    model="gpt-4",
    tools=["grammar-check", "style-analyzer"],
    skills=["content-creation", "editing"]
)

# Orchestrate agents
orchestrator = Orchestrator(
    agents=[research_agent, coding_agent, writing_agent],
    coordination_strategy="sequential"  # or "parallel", "dynamic"
)

# Complex task delegation
result = orchestrator.execute(
    "Research the latest AI trends, write a Python script to analyze them, and create a summary report"
)

# Orchestrator automatically:
# 1. Routes subtasks to appropriate agents
# 2. Manages inter-agent communication
# 3. Consolidates results
print(result)
```

## Real-World Examples

### Knowledge Assistant

```python
from hermes import Agent
from hermes.tools import WebSearch, FileSystem, VectorDB

# Configure knowledge assistant
assistant = Agent(
    name="knowledge-assistant",
    model="gpt-4",
    tools=[
        WebSearch(api_key="${SERPER_API_KEY}"),
        FileSystem(allowed_paths=["./documents"]),
        VectorDB(
            provider="pinecone",
            api_key="${PINECONE_API_KEY}",
            index="knowledge-base"
        )
    ]
)

# Load domain-specific skills
assistant.load_skills_from_directory("./skills/research")

# Ingest documents into knowledge base
assistant.run("Index all PDF files in ./documents into the vector database")

# Query with context
response = assistant.run(
    "What are the key findings from the Q4 reports? Compare with Q3."
)

print(response.answer)
print(f"Sources: {response.sources}")
```

### Development Automation

```python
from hermes import Agent
from hermes.tools import GitHub, FileSystem, CodeExecution

dev_agent = Agent(
    name="dev-automation",
    model="gpt-4",
    tools=[
        GitHub(token="${GITHUB_TOKEN}"),
        FileSystem(allowed_paths=["./projects"]),
        CodeExecution(languages=["python", "javascript", "bash"])
    ],
    skills=[
        "code-generation",
        "testing",
        "git-workflow",
        "ci-cd-setup"
    ]
)

# Automated workflow
task = """
1. Clone the repository at github.com/user/project
2. Analyze the codebase structure
3. Generate unit tests for untested modules
4. Create a PR with the tests
5. Set up GitHub Actions for CI
"""

result = dev_agent.run(task)

# Agent maintains context across subtasks
print(result.summary)
print(f"PR created: {result.metadata['pr_url']}")
```

### Content Creation Pipeline

```python
from hermes import Agent, Orchestrator

# Content planning agent
planner = Agent(
    name="content-planner",
    tools=["web-search", "trend-analyzer"],
    skills=["topic-research", "audience-analysis"]
)

# Content writing agent
writer = Agent(
    name="content-writer",
    tools=["grammar-check", "seo-optimizer"],
    skills=["blog-writing", "copywriting"]
)

# Content review agent
reviewer = Agent(
    name="content-reviewer",
    tools=["plagiarism-check", "readability-analyzer"],
    skills=["editing", "fact-checking"]
)

# Create content pipeline
pipeline = Orchestrator(
    agents=[planner, writer, reviewer],
    coordination_strategy="sequential"
)

# Execute content creation
content = pipeline.execute(
    "Create a blog post about AI agent frameworks, optimized for SEO"
)

print(content.final_output)
```

## Advanced Features

### Feedback Loop

```python
from hermes import Agent, Feedback

agent = Agent(name="learning-agent", learning_config={"enabled": True})

# Explicit feedback
response = agent.run("Summarize this article")

feedback = Feedback(
    interaction_id=response.id,
    rating=4.5,  # 0-5 scale
    comments="Good summary but missed key point about X",
    corrections={"missed_points": ["Point about X"]}
)

agent.provide_feedback(feedback)

# Agent adjusts behavior based on feedback
improved_response = agent.run("Summarize this other article")
```

### Memory Retrieval Strategies

```python
from hermes import Agent, MemoryRetrieval

agent = Agent(name="memory-agent")

# Semantic retrieval (default)
semantic_results = agent.recall(
    "What did we discuss about database optimization?",
    method="semantic",
    top_k=5
)

# Keyword retrieval
keyword_results = agent.recall(
    "database optimization",
    method="keyword",
    top_k=5
)

# Hybrid retrieval
hybrid_results = agent.recall(
    "How to optimize database queries?",
    method="hybrid",
    semantic_weight=0.7,
    keyword_weight=0.3,
    top_k=5
)

# Temporal retrieval (recent first)
recent_results = agent.recall(
    "database",
    method="temporal",
    time_decay=0.9,  # Recent memories weighted higher
    top_k=5
)
```

### Skill Evolution

```python
from hermes import Agent

agent = Agent(
    name="evolving-agent",
    learning_config={
        "enabled": True,
        "auto_skill_creation": True,
        "skill_evolution": True
    }
)

# Agent automatically creates skills from repeated patterns
for _ in range(10):
    agent.run("Parse this JSON and extract the user field")

# After several iterations, agent creates a reusable skill
evolved_skills = agent.get_evolved_skills()
print(evolved_skills)
# Output: ['json-user-extractor']

# Skill is now available for reuse
agent.run("Extract user from this JSON: {...}")  # Uses evolved skill
```

## CLI Commands

### Basic Commands

```bash
# Initialize new agent
hermes init <agent-name>

# Start agent
hermes start [--config CONFIG_FILE] [--debug]

# Interactive mode
hermes chat [--agent AGENT_NAME]

# List agents
hermes list

# View agent status
hermes status <agent-name>

# Stop agent
hermes stop <agent-name>
```

### Skills Management

```bash
# List available skills
hermes skills list

# Install skill from repository
hermes skills install <skill-name>

# Create new skill template
hermes skills create <skill-name>

# Test skill
hermes skills test <skill-name> [--input INPUT_FILE]

# Export skills
hermes skills export <agent-name> --output skills-backup.zip
```

### Memory Management

```bash
# View memory statistics
hermes memory stats <agent-name>

# Search memory
hermes memory search <agent-name> "query text"

# Consolidate memory
hermes memory consolidate <agent-name>

# Export memory
hermes memory export <agent-name> --output memory-backup.json

# Clear memory
hermes memory clear <agent-name> [--confirm]
```

### Learning & Metrics

```bash
# View learning metrics
hermes metrics <agent-name>

# Export learning history
hermes learning export <agent-name>

# Reset learning state
hermes learning reset <agent-name> [--confirm]
```

## API Usage

### REST API

Start API server:

```bash
hermes serve --port 8000 --host 0.0.0.0
```

API endpoints:

```python
import requests

BASE_URL = "http://localhost:8000"
API_KEY = os.getenv("HERMES_API_KEY")

headers = {"Authorization": f"Bearer {API_KEY}"}

# Create agent
response = requests.post(
    f"{BASE_URL}/agents",
    json={
        "name": "api-agent",
        "model": "gpt-4",
        "tools": ["web-search", "file-system"]
    },
    headers=headers
)
agent_id = response.json()["agent_id"]

# Execute task
response = requests.post(
    f"{BASE_URL}/agents/{agent_id}/execute",
    json={
        "task": "Search for Python best practices and save to a file",
        "stream": False
    },
    headers=headers
)
result = response.json()

# Stream responses
response = requests.post(
    f"{BASE_URL}/agents/{agent_id}/execute",
    json={"task": "Write a long article", "stream": True},
    headers=headers,
    stream=True
)

for line in response.iter_lines():
    if line:
        print(json.loads(line))

# Get agent status
response = requests.get(
    f"{BASE_URL}/agents/{agent_id}",
    headers=headers
)
status = response.json()
```

### WebSocket API

```javascript
// JavaScript/Node.js WebSocket client
const WebSocket = require('ws');

const ws = new WebSocket('ws://localhost:8000/ws');

ws.on('open', () => {
    // Authenticate
    ws.send(JSON.stringify({
        type: 'auth',
        api_key: process.env.HERMES_API_KEY
    }));
    
    // Create agent
    ws.send(JSON.stringify({
        type: 'create_agent',
        data: {
            name: 'ws-agent',
            model: 'gpt-4'
        }
    }));
});

ws.on('message', (data) => {
    const message = JSON.parse(data);
    
    if (message.type === 'agent_created') {
        const agentId = message.agent_id;
        
        // Execute task
        ws.send(JSON.stringify({
            type: 'execute',
            agent_id: agentId,
            task: 'Explain quantum computing'
        }));
    }
    
    if (message.type === 'response_chunk') {
        process.stdout.write(message.content);
    }
    
    if (message.type === 'response_complete') {
        console.log('\n\nTask completed!');
        console.log('Metadata:', message.metadata);
    }
});
```

## Common Patterns

### Error Handling

```python
from hermes import Agent, HermesError, ToolError, MemoryError

agent = Agent(name="resilient-agent")

try:
    response = agent.run("Complex task that might fail")
except ToolError as e:
    print(f"Tool execution failed: {e.tool_name}")
    print(f"Error: {e.message}")
    # Retry with different tool
    response = agent.run(
        "Same task",
        exclude_tools=[e.tool_name]
    )
except MemoryError as e:
    print(f"Memory operation failed: {e}")
    # Clear and retry
    agent.clear_short_term_memory()
    response = agent.run("Retry task")
except HermesError as e:
    print(f"General Hermes error: {e}")
    # Fallback behavior
finally:
    agent.save_state()
```

### Context Management

```python
from hermes import Agent, Context

agent = Agent(name="context-agent")

# Create explicit context
context = Context()
context.add("project", "E-commerce platform")
context.add("tech_stack", ["Python", "FastAPI", "PostgreSQL"])
context.add("priority", "Performance optimization")

# Run with context
response = agent.run(
    "Suggest database improvements",
    context=context
)

# Context persists across interactions
response2 = agent.run("What database are we using?")
# Agent knows: "You're using PostgreSQL for your e-commerce platform"
```

### Async Usage

```python
import asyncio
from hermes import AsyncAgent

async def main():
    agent = AsyncAgent(name="async-agent")
    
    # Concurrent task execution
    tasks = [
        agent.run("Research topic A"),
        agent.run("Research topic B"),
        agent.run("Research topic C")
    ]
    
    results = await asyncio.gather(*tasks)
    
    for i, result in enumerate(results):
        print(f"Task {i+1}: {result.summary}")

asyncio.run(main())
```

### State Persistence

```python
from hermes import Agent

# Create agent with state persistence
agent = Agent(
    name="persistent-agent",
    state_backend="redis",
    state_config={
        "host": "localhost",
        "port": 6379,
        "db": 0
    }
)

# State automatically saved after each interaction
agent.run("Task 1")
agent.run("Task 2")

# Restore agent state (e.g., after restart)
agent = Agent.restore("persistent-agent")

# Agent remembers all previous context
response = agent.run("What did we work on?")
```

## Troubleshooting

### Installation Issues

```bash
# If pip install fails, try:
pip install --upgrade pip
pip install -r requirements.txt --no-cache-dir

# For M1/M2 Mac users:
ARCHFLAGS="-arch arm64" pip install -r requirements.txt

# If SQLite version issues:
pip install pysqlite3-binary
```

### Memory Issues

```python
# If memory consumption is high:
agent = Agent(
    name="optimized-agent",
    memory_config={
        "short_term_size": 20,  # Reduce from default 50
        "consolidation_interval": 100,  # More frequent consolidation
        "compression_enabled": True  # Enable memory compression
    }
)

# Manually trigger memory cleanup
agent.consolidate_memory()
agent.clear_expired_memories(max_age_days=7)
```

### Performance Optimization

```python
# Enable caching
agent = Agent(
    name="fast-agent",
    cache_config={
        "enabled": True,
        "backend": "redis",
        "ttl": 3600,  # 1 hour
        "max_size": 1000
    }
)

# Use lighter model for simple tasks
agent.set_model(
    provider="openai",
    model="gpt-3.5-turbo",  # Faster than gpt-4
    task_complexity_threshold=0.5  # Auto-switch to gpt-4 for complex tasks
)

# Parallel tool execution
agent.configure_tools(execution_mode="parallel", max_concurrent=3)
```

### Learning Not Improving

```python
# Increase feedback granularity
agent = Agent(
    name="better-learner",
    learning_config={
        "enabled": True,
        "feedback_threshold": 0.6,  # Lower threshold
        "improvement_interval": 5,  # More frequent improvements
        "learning_rate": 0.01,  # Increase learning rate
        "exploration_rate": 0.2  # Allow more exploration
    }
)

# Provide explicit feedback
for interaction in recent_interactions:
    agent.provide_feedback(Feedback(
        interaction_id=interaction.id,
        rating=calculate_rating(interaction),
        corrections=extract_corrections(interaction)
    ))
```

### Debugging

```python
from hermes import Agent
import logging

# Enable debug logging
logging.basicConfig(level=logging.DEBUG)

agent = Agent(
    name="debug-agent",
    debug=True  # Enable debug mode
)

# Inspect agent state
print(agent.get_state())
print(agent.get_tool_usage_stats())
print(agent.get_skill_performance())

# Trace execution
response = agent.run("Task", trace=True)
print(response.trace)  # Full execution trace

# Export debug info
agent.export_debug_info("debug-export.json")
```

### API Rate Limiting

```python
from hermes import Agent, RateLimiter

agent = Agent(
    name="rate-limited-agent",
    rate_limiter=RateLimiter(
        requests_per_minute=60,
        burst_limit=10,
        retry_on_limit=True,
        backoff_factor=2
    )
)

# Agent automatically handles rate limits with exponential backoff
```

## Best Practices

1. **Start with clear constraints**: Define what the agent should and shouldn't do
2. **Use appropriate memory sizes**: Don't over-retain; consolidate regularly
3. **Provide feedback early**: Help the learning loop converge faster
4. **Create reusable skills**: Extract common patterns into skills
5. **Monitor learning metrics**: Track improvement rate and adjust configuration
6. **Use tool sandboxing**: Enable sandboxes for code execution and file system access
7. **Version your agent configs**: Track configuration changes over time
8. **Test skills in isolation**: Verify skill behavior before integration
9. **Implement fallbacks**: Handle tool failures gracefully
10. **Secure your secrets**: Never hardcode API keys; use environment variables

## Resources

- Official Documentation: https://hermes-agent.nousresearch.com/docs/
- GitHub Repository: https://github.com/NousResearch/hermes-agent
- Community Discord: https://discord.gg/nousresearch
- Orange Book Guide (Chinese): See repository PDF downloads
- Example Skills: https://github.com/NousResearch/hermes-skills

---

**Note**: Hermes Agent is under active development. Features and APIs may change. Always refer to the official documentation for the most up-to-date information.
