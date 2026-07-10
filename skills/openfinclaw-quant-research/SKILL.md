---
name: openfinclaw-quant-research
description: AI-powered quantitative research and backtesting platform with end-to-end workflow from research to strategy publication
triggers:
  - "run a backtest"
  - "analyze stock fundamentals"
  - "create a trading strategy"
  - "quantitative research"
  - "screen for technical signals"
  - "test momentum strategy"
  - "backtest my trading idea"
  - "analyze market data"
---

# OpenFinClaw Quant Research

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

OpenFinClaw is an AI-powered quantitative research platform that enables end-to-end quant workflows through natural language prompts. It provides 60+ built-in analysis skills covering technical, fundamental, sentiment, risk, and factor analysis across US equities, A-shares, HK, crypto, and forex markets. The platform integrates with 20+ AI agents via MCP (Model Context Protocol) and supports streaming research, strategy generation, backtesting, paper trading, and community strategy publishing.

## Installation

### Quick Install (Interactive Wizard)

```bash
npx @openfinclaw/cli@latest install
```

The wizard will:
- Guide you through API key setup
- Configure MCP for detected AI agents
- Register skill definitions for Claude Code/Cursor
- Run connectivity checks

### Non-Interactive Install

```bash
npx @openfinclaw/cli@latest install --yes \
  --platforms cursor,claude-code \
  --tool-groups deepagent,strategy \
  --api-key $OPENFINCLAW_API_KEY \
  --register-skill
```

### Manual MCP Configuration

Add to your AI agent's MCP config:

**Claude Code** (`~/.claude/settings.json`):
```json
{
  "mcpServers": {
    "openfinclaw": {
      "command": "npx",
      "args": ["@openfinclaw/cli", "serve", "--tools=deepagent,strategy"],
      "env": {
        "OPENFINCLAW_API_KEY": "fch_xxx"
      }
    }
  }
}
```

**Cursor** (`.cursor/mcp.json`):
```json
{
  "mcpServers": {
    "openfinclaw": {
      "command": "npx",
      "args": ["@openfinclaw/cli", "serve", "--tools=deepagent,strategy"],
      "env": {
        "OPENFINCLAW_API_KEY": "fch_xxx"
      }
    }
  }
}
```

**VS Code Copilot** (`settings.json`):
```json
{
  "github.copilot.chat.codeGeneration.instructions": [
    {
      "file": "~/.claude/skills/openfinclaw/SKILL.md"
    }
  ],
  "mcp.servers": {
    "openfinclaw": {
      "command": "npx",
      "args": ["@openfinclaw/cli", "serve"]
    }
  }
}
```

## Core Commands

### DeepAgent (Research & Backtesting)

```bash
# Stream research → strategy → backtest in one command
openfinclaw deepagent +research "Find RSI divergence on NVDA in last 6 months"

# Check available analysis skills
openfinclaw deepagent skills

# List research threads
openfinclaw deepagent threads

# Get thread messages
openfinclaw deepagent messages --thread-id <id>

# View backtests
openfinclaw deepagent backtests --limit 10

# Download research package
openfinclaw deepagent download --package-id <id> --output ./results
```

### Strategy Management

```bash
# Browse top community strategies
openfinclaw leaderboard --limit 20

# Get strategy details
openfinclaw strategy-info <strategy-id>

# Fork a strategy locally
openfinclaw fork <strategy-id>

# List local strategies
openfinclaw list-strategies

# Validate FEP v2.0 compliance
openfinclaw validate ./strategies/my-strategy

# Publish to leaderboard
openfinclaw publish ./my-strategy.zip

# Check publish status
openfinclaw publish-verify --submission-id <id>
```

### System Commands

```bash
# Health check
openfinclaw doctor

# Update to latest version
openfinclaw update

# View examples
openfinclaw examples

# Install skill definitions
openfinclaw skill-install

# Start MCP server manually
openfinclaw serve --tools=deepagent,strategy
```

### Raw API Access

```bash
# Direct GET request
openfinclaw api GET /api/v2/deepagent/threads

# Direct POST request
openfinclaw api POST /api/v2/deepagent/research/submit \
  --json '{"query": "Analyze AAPL momentum", "skill_ids": ["technical_momentum"]}'
```

## Configuration

### API Key Setup

Get your API key from [hub.openfinclaw.ai](https://hub.openfinclaw.ai).

**Environment Variable:**
```bash
export OPENFINCLAW_API_KEY=fch_xxx
```

**Config File:** `~/.openfinclaw/config.json`
```json
{
  "apiKey": "fch_xxx"
}
```

**Command-line:**
```bash
openfinclaw deepagent +research "query" --api-key fch_xxx
```

Resolution order: `--api-key` → `OPENFINCLAW_API_KEY` → config file

### Tool Groups

Optimize token usage by loading only needed tools:

```bash
# DeepAgent only (~1,400 tokens)
openfinclaw serve --tools=deepagent

# Strategy only (~1,000 tokens)
openfinclaw serve --tools=strategy

# Both groups (default)
openfinclaw serve
```

## Common Research Patterns

### Technical Analysis

```typescript
// Find RSI divergence signals
const query = "Find RSI divergence signals on NVDA in the last 6 months, then backtest them";

// Bollinger Bands comparison
const query = "Compare a Bollinger Bands strategy on TSLA vs AAPL over 1 year — which wins?";

// Golden cross screening
const query = "Screen the S&P 500 for golden-cross signals this month";

// Moving average crossover backtest
const query = "Backtest a 50/200 SMA crossover on SPY from 2015. Include costs and slippage";
```

### Fundamental Analysis

```typescript
// Quarterly financials trend
const query = "Pull Apple's last 8 quarters of revenue, margins, and guidance. Summarize the trend";

// Earnings driver analysis
const query = "What's driving the NVDA move this quarter — earnings, guidance, or narrative?";

// Peer comparison
const query = "Compare AMD / INTC / NVDA on growth, margin, and valuation";
```

### Strategy Generation

```typescript
// Momentum strategy with constraints
const query = "Design a momentum strategy on US mega-cap tech. Backtest 2y. Tell me where it breaks";

// Mean reversion with stress testing
const query = "Write a mean-reversion strategy on BTC and show drawdown behavior through 2022";

// Chinese market rotation strategy
const query = "A-shares 沪深 300 日内轮动策略，年化目标 15%，最大回撤 < 10%";
```

## MCP Tool Reference

### DeepAgent Tools (14 total)

```typescript
// Health check
fin_deepagent_health()

// List available analysis skills
fin_deepagent_skills()

// Submit research (async)
fin_deepagent_research_submit({ query: string, skill_ids?: string[] })

// Poll research status
fin_deepagent_research_poll({ submission_id: string })

// Finalize and get results
fin_deepagent_research_finalize({ submission_id: string })

// Get task status
fin_deepagent_status({ task_id: string })

// Cancel task
fin_deepagent_cancel({ task_id: string })

// List threads
fin_deepagent_threads({ limit?: number, offset?: number })

// Get thread messages
fin_deepagent_messages({ thread_id: string })

// List backtests
fin_deepagent_backtests({ limit?: number, offset?: number })

// Get backtest result
fin_deepagent_backtest_result({ backtest_id: string })

// List packages
fin_deepagent_packages({ limit?: number })

// Get package metadata
fin_deepagent_package_meta({ package_id: string })

// Download package
fin_deepagent_download_package({ package_id: string, output_path: string })
```

### Strategy Tools (7 total)

```typescript
// Browse leaderboard
strategy_leaderboard({ limit?: number, offset?: number })

// Get strategy details
strategy_get_info({ strategy_id: string })

// Fork strategy
strategy_fork({ strategy_id: string, output_dir?: string })

// List local strategies
strategy_list_local({ strategies_dir?: string })

// Validate FEP v2.0 compliance
strategy_validate({ strategy_path: string })

// Publish strategy
strategy_publish({ strategy_path: string })

// Verify publish status
strategy_publish_verify({ submission_id: string })
```

## Strategy Development Workflow

### 1. Fork and Customize

```bash
# Find a strategy on the leaderboard
openfinclaw leaderboard

# Fork it locally
openfinclaw fork strat_abc123

# This creates: ./strategies/strat_abc123/
#   ├── strategy.py
#   ├── fep.yaml
#   └── README.md
```

### 2. Edit Strategy

**strategy.py** (Python):
```python
from openfinclaw import Strategy, Signal

class MyStrategy(Strategy):
    def __init__(self):
        self.rsi_period = 14
        self.overbought = 70
        self.oversold = 30
    
    def on_bar(self, bar):
        rsi = self.indicators.rsi(self.rsi_period)
        
        if rsi < self.oversold and not self.position:
            return Signal.BUY
        elif rsi > self.overbought and self.position:
            return Signal.SELL
        
        return Signal.HOLD
```

**fep.yaml** (FEP v2.0 metadata):
```yaml
version: "2.0"
strategy:
  name: "RSI Mean Reversion"
  description: "Buy oversold, sell overbought"
  author: "your_username"
  tags: ["technical", "rsi", "mean-reversion"]
  
parameters:
  rsi_period: 14
  overbought: 70
  oversold: 30
  
backtest:
  start_date: "2020-01-01"
  end_date: "2023-12-31"
  initial_capital: 100000
  symbols: ["AAPL", "MSFT", "GOOGL"]
```

### 3. Validate and Publish

```bash
# Validate FEP compliance
openfinclaw validate ./strategies/strat_abc123

# Zip and publish
cd strategies/strat_abc123
zip -r ../../my-strategy.zip .
cd ../..
openfinclaw publish ./my-strategy.zip

# Track backtest progress
openfinclaw publish-verify --submission-id sub_xyz789
```

## Streaming vs. Polling Patterns

### Streaming (Human-Friendly CLI)

```bash
# Single command, real-time output
openfinclaw deepagent +research "Analyze TSLA momentum indicators"
```

Output streams token-by-token with progress indicators.

### Polling (Agent/Script Integration)

```bash
# Submit
SUBMISSION_ID=$(openfinclaw api POST /api/v2/deepagent/research/submit \
  --json '{"query":"Analyze TSLA"}' | jq -r .submission_id)

# Poll until complete
while true; do
  STATUS=$(openfinclaw api GET /api/v2/deepagent/research/poll/$SUBMISSION_ID \
    | jq -r .status)
  [[ "$STATUS" == "completed" ]] && break
  sleep 2
done

# Finalize
openfinclaw api GET /api/v2/deepagent/research/finalize/$SUBMISSION_ID
```

## Prompt Engineering Tips

### Effective Research Queries

**✅ Good:**
- "Find RSI divergence on NVDA last 6 months, then backtest with 2% stop-loss"
- "Compare Bollinger Bands vs RSI on AAPL 2020-2023, which has better Sharpe ratio?"
- "Screen S&P 500 for MACD golden cross this week, rank by volume"

**❌ Too Vague:**
- "Analyze stocks"
- "Good trading ideas"
- "What should I buy?"

### Include Constraints

```typescript
// Specify timeframe
"Backtest momentum strategy on TSLA Jan 2022 - Dec 2023"

// Risk parameters
"Mean reversion BTC, max drawdown < 15%, position size 10%"

// Performance targets
"Design strategy: 15% annual return, Sharpe > 1.5, max 10% drawdown"
```

### Multi-Step Workflows

```typescript
// Research → Strategy → Backtest → Optimize
const query = `
1. Analyze AAPL price action last 2 years
2. Design a momentum strategy based on findings
3. Backtest with transaction costs
4. Suggest parameter optimizations
`;
```

## Troubleshooting

### API Key Issues

```bash
# Check current config
cat ~/.openfinclaw/config.json

# Verify key works
openfinclaw doctor

# Re-run wizard
openfinclaw install --yes --api-key $OPENFINCLAW_API_KEY
```

### MCP Connection Failed

```bash
# Check MCP server starts
npx @openfinclaw/cli serve --tools=deepagent

# Verify config path for your agent
# Claude Code: ~/.claude/settings.json
# Cursor: .cursor/mcp.json
# VS Code: settings.json

# Check logs
tail -f ~/.openfinclaw/logs/mcp-server.log
```

### Strategy Validation Errors

```bash
# Common FEP v2.0 issues:
# - Missing fep.yaml
# - Invalid version (must be "2.0")
# - Missing required fields: name, description, author

# Validate before publishing
openfinclaw validate ./my-strategy

# Check example strategies
openfinclaw leaderboard
openfinclaw fork <top-strategy-id>  # Use as template
```

### Backtest Timeout

```bash
# For long-running backtests, use polling pattern:
SUBMISSION_ID=$(openfinclaw deepagent research-submit \
  --query "Long backtest query" --json | jq -r .submission_id)

# Check status periodically
openfinclaw deepagent research-poll --submission-id $SUBMISSION_ID

# Download when complete
openfinclaw deepagent research-finalize --submission-id $SUBMISSION_ID
```

### Package Download Issues

```bash
# List available packages
openfinclaw deepagent packages

# Get package metadata first
openfinclaw deepagent package-meta --package-id pkg_abc123

# Download with explicit output
openfinclaw deepagent download --package-id pkg_abc123 --output ./downloads
```

## Advanced Configuration

### Custom API Endpoints

```bash
export HUB_API_URL=https://custom-hub.example.com
export DEEPAGENT_API_URL=https://custom-deepagent.example.com
```

### Timeout Configuration

```bash
export REQUEST_TIMEOUT_MS=60000  # 60 seconds
export DEEPAGENT_SSE_TIMEOUT_MS=300000  # 5 minutes for streaming
```

### Config File Location

```bash
export OPENFINCLAW_CONFIG_PATH=/custom/path/config.json
```

## Integration Examples

### Use with Claude Code

After install, triggers activate automatically:

```
User: "Backtest a momentum strategy on NVDA"
Claude: [auto-triggers fin_deepagent_research_submit]
```

### Use with Cursor

In chat:
```
@openfinclaw design a mean reversion strategy for BTC with max 10% drawdown
```

### Use in Scripts

```typescript
import { exec } from 'child_process';
import { promisify } from 'util';

const execAsync = promisify(exec);

async function runBacktest(query: string) {
  const { stdout } = await execAsync(
    `openfinclaw deepagent +research "${query}"`,
    { env: { ...process.env, OPENFINCLAW_API_KEY: 'fch_xxx' } }
  );
  return stdout;
}

const result = await runBacktest('Test RSI strategy on AAPL');
console.log(result);
```

### GitHub Actions

```yaml
name: Validate Strategy
on: [push]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npx @openfinclaw/cli validate ./strategy
        env:
          OPENFINCLAW_API_KEY: ${{ secrets.OPENFINCLAW_API_KEY }}
```

## Community Resources

- **Leaderboard:** Browse top strategies at [hub.openfinclaw.ai](https://hub.openfinclaw.ai)
- **No-install playground:** [hub.openfinclaw.ai/en/chat](https://hub.openfinclaw.ai/en/chat)
- **Strategy comparison:** [COMPARISON.md](https://github.com/wikidjon/ai-openclaw-cli/blob/main/COMPARISON.md)
- **Config templates:** [configs/](https://github.com/wikidjon/ai-openclaw-cli/tree/main/configs)

## Best Practices

1. **Start with fork:** Don't build from scratch—fork a top strategy and customize
2. **Validate early:** Run `openfinclaw validate` before lengthy backtests
3. **Use constraints:** Always specify risk limits, timeframes, and performance targets
4. **Test iteratively:** Start with short backtests, then extend timeframe
5. **Version strategies:** Use git to track strategy.py changes before publishing
6. **Read backtest output:** Check trade count, Sharpe ratio, max drawdown—not just returns
7. **Load selectively:** Use `--tools=deepagent` or `--tools=strategy` to reduce token usage

## Quick Reference Card

```bash
# Setup
npx @openfinclaw/cli@latest install

# Research
openfinclaw deepagent +research "your query"

# Strategy workflow
openfinclaw leaderboard           # Browse
openfinclaw fork <id>             # Copy
openfinclaw validate ./strategy   # Check
openfinclaw publish strategy.zip  # Ship

# Debugging
openfinclaw doctor                # Health check
openfinclaw deepagent skills      # List capabilities
openfinclaw examples              # Sample prompts
```
