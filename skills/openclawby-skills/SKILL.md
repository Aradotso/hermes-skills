---
name: openclawby-skills
description: AI agent skills for live market, on-chain, social data and crypto trade execution via unified Clawby API
triggers:
  - "get live market data for stocks or crypto"
  - "check on-chain metrics or blockchain data"
  - "search social media for trading sentiment"
  - "execute trades on crypto exchanges"
  - "analyze prediction market odds on Polymarket"
  - "get short interest or dark pool data"
  - "fetch memecoin or DEX pair information"
  - "query funding rates or open interest"
---

# Openclawby Skills

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

Official AI agent skills for real-time financial, on-chain, and social data **plus crypto trade execution**. The `clawby-data` skill provides 280+ data interfaces across equities, options, crypto, DeFi, social media, and prediction markets — unified under one API key with local trade-execution connectors for major exchanges.

## What it does

- **Equities & Options**: Real-time quotes, fundamentals, SEC filings, short interest, dark pool flows
- **Crypto**: Derivatives data (funding rates, open interest, liquidations), DEX pairs, memecoin analytics, Hyperliquid perps
- **On-Chain**: Address labels, AML risk scoring, raw RPC access across 75+ chains
- **Social**: X/Twitter, Facebook, Instagram search for sentiment analysis
- **Prediction Markets**: Polymarket odds and market data
- **Trade Execution**: Local connectors for Binance, OKX, Bitget, Polymarket, GMGN (keys stay local, never sent to Clawby)

## Installation

### Quick Setup

**For Claude Code / Codex:**

```bash
# Download and install the skill bundle
curl -L https://www.openclawby.com/clawby-data.zip -o clawby-data.zip
unzip clawby-data.zip -d ~/.claude/skills/clawby-data/

# Or clone from GitHub
git clone https://github.com/openclawby/openclawby-skills.git
cp -R openclawby-skills/clawby-data ~/.claude/skills/clawby-data
```

### Get API Key

1. Sign up at [openclawby.com](https://www.openclawby.com/) (free account includes 100 paid-interface calls)
2. Copy your API key (format: `pk_xxx`)
3. Set environment variable:

```bash
export CLAWBY_API_KEY=your_key_here
```

For persistent config, add to `~/.bashrc`, `~/.zshrc`, or your shell's config file.

## Core API Usage

### HTTP Relay Endpoint

All data queries go through the unified relay:

```python
import requests
import os

def query_clawby(interface_name: str, params: dict) -> dict:
    """Query any Clawby data interface."""
    url = "https://api.openclawby.com/api/relay"
    headers = {
        "X-API-Key": os.getenv("CLAWBY_API_KEY"),
        "Content-Type": "application/json"
    }
    payload = {
        "name": interface_name,
        "params": params
    }
    
    response = requests.post(url, json=payload, headers=headers)
    response.raise_for_status()
    return response.json()

# Example: Search Twitter for BTC sentiment
result = query_clawby("x_search", {
    "query": "BTC ETF",
    "sort": "Latest"
})
print(result["data"])
print(f"Credits remaining: {result['credits']['remaining']}")
```

### cURL Examples

```bash
# Stock short interest
curl -X POST https://api.openclawby.com/api/relay \
  -H "X-API-Key: $CLAWBY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name":"stock_short_interest","params":{"symbol":"GME"}}'

# Crypto funding rates
curl -X POST https://api.openclawby.com/api/relay \
  -H "X-API-Key: $CLAWBY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name":"crypto_funding_rates","params":{"symbol":"BTC"}}'

# Polymarket prediction odds
curl -X POST https://api.openclawby.com/api/relay \
  -H "X-API-Key: $CLAWBY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name":"polymarket_odds","params":{"market":"fed-rate-cut"}}'

# On-chain address labels
curl -X POST https://api.openclawby.com/api/relay \
  -H "X-API-Key: $CLAWBY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name":"address_labels","params":{"address":"0x123...","chain":"ethereum"}}'
```

## Key Data Categories

### Equities

```python
# Real-time quote
query_clawby("stock_quote", {"symbol": "AAPL"})

# Fundamentals
query_clawby("stock_fundamentals", {"symbol": "TSLA"})

# SEC filings
query_clawby("sec_filings", {"symbol": "NVDA", "form_type": "10-K"})

# Dark pool activity
query_clawby("dark_pool_trades", {"symbol": "GME"})
```

### Crypto & DeFi

```python
# Perpetual futures funding
query_clawby("crypto_funding_rates", {
    "symbol": "ETH",
    "exchange": "binance"
})

# Open interest
query_clawby("crypto_open_interest", {"symbol": "BTC"})

# Liquidations
query_clawby("crypto_liquidations", {
    "symbol": "SOL",
    "timeframe": "24h"
})

# DEX pair analysis
query_clawby("dex_pair_info", {
    "pair_address": "0xabc...",
    "chain": "ethereum"
})

# Memecoin intel
query_clawby("memecoin_analysis", {
    "token_address": "0xdef...",
    "chain": "solana"
})
```

### On-Chain

```python
# Raw RPC call (80+ chains supported)
query_clawby("rpc_call", {
    "chain": "ethereum",
    "method": "eth_blockNumber",
    "params": []
})

# Address risk scoring
query_clawby("aml_risk_check", {
    "address": "0x123...",
    "chain": "ethereum"
})
```

### Social Media

```python
# X/Twitter search
query_clawby("x_search", {
    "query": "Solana price prediction",
    "sort": "Top",
    "count": 20
})

# Facebook sentiment
query_clawby("facebook_search", {
    "query": "stock market crash",
    "type": "posts"
})
```

## Trade Execution (Local Connectors)

**IMPORTANT**: Trade execution runs **locally** — your exchange API keys are never sent to Clawby servers. Connectors install on first use.

### Setup Exchange Credentials

```bash
# Binance
export BINANCE_API_KEY=your_binance_key
export BINANCE_API_SECRET=your_binance_secret

# OKX
export OKX_API_KEY=your_okx_key
export OKX_API_SECRET=your_okx_secret
export OKX_PASSPHRASE=your_passphrase

# Bitget
export BITGET_API_KEY=your_bitget_key
export BITGET_API_SECRET=your_bitget_secret
export BITGET_PASSPHRASE=your_passphrase

# Polymarket (requires private key)
export POLYMARKET_PRIVATE_KEY=your_ethereum_private_key

# GMGN
export GMGN_PRIVATE_KEY=your_solana_private_key
```

### Python Trade Execution Examples

```python
from clawby_exec import BinanceExecutor, PolymarketExecutor

# Binance spot trade
binance = BinanceExecutor(
    api_key=os.getenv("BINANCE_API_KEY"),
    api_secret=os.getenv("BINANCE_API_SECRET")
)

# Market order
order = binance.place_order(
    symbol="BTCUSDT",
    side="BUY",
    order_type="MARKET",
    quantity=0.001
)

# Limit order
limit_order = binance.place_order(
    symbol="ETHUSDT",
    side="SELL",
    order_type="LIMIT",
    quantity=0.1,
    price=3000.0
)

# Polymarket prediction trade
poly = PolymarketExecutor(
    private_key=os.getenv("POLYMARKET_PRIVATE_KEY")
)

bet = poly.place_bet(
    market_id="fed-rate-cut-march-2026",
    outcome="YES",
    amount_usdc=100,
    price=0.65  # betting at 65% odds
)
```

### CLI Trade Execution

```bash
# Install connector on first use (interactive)
python ~/.claude/skills/clawby-data/exec/binance_connector.py --setup

# Place order (requires confirmation)
python ~/.claude/skills/clawby-data/exec/binance_connector.py \
  --order \
  --symbol BTCUSDT \
  --side BUY \
  --type MARKET \
  --quantity 0.001
```

## Skill File Structure

```
clawby-data/
├── SKILL.md                 # This file (entrypoint)
├── update-protocol.md       # Auto-update rules (asks permission)
├── manifest.json            # Version tracking
├── api/
│   ├── catalog/
│   │   ├── equities.md     # Stock/options interfaces
│   │   ├── crypto.md       # Crypto derivatives interfaces
│   │   ├── onchain.md      # Blockchain/RPC interfaces
│   │   ├── social.md       # Social search interfaces
│   │   └── predictions.md  # Prediction market interfaces
│   └── clawby.yaml         # Machine-readable catalog (auto-updated)
├── exec/
│   ├── binance_connector.py
│   ├── okx_connector.py
│   ├── bitget_connector.py
│   ├── polymarket_connector.py
│   └── gmgn_connector.py
├── report/
│   └── template.md         # Professional PDF report generation
└── local/                  # User state (never overwritten by updates)
    ├── config.json
    └── api_keys.enc
```

## Common Patterns

### Multi-Source Analysis

```python
def analyze_asset(symbol: str):
    """Combine multiple data sources for comprehensive analysis."""
    # Get market data
    quote = query_clawby("stock_quote", {"symbol": symbol})
    
    # Check short interest
    shorts = query_clawby("stock_short_interest", {"symbol": symbol})
    
    # Get social sentiment
    social = query_clawby("x_search", {"query": f"${symbol}", "sort": "Latest"})
    
    # Analyze dark pool activity
    dark_pool = query_clawby("dark_pool_trades", {"symbol": symbol})
    
    return {
        "price": quote["data"]["price"],
        "short_interest": shorts["data"]["short_percent"],
        "social_sentiment": social["data"]["sentiment_score"],
        "dark_pool_volume": dark_pool["data"]["total_volume"]
    }
```

### Cross-Chain Token Tracking

```python
def track_token_across_chains(token_symbol: str):
    """Monitor a token across multiple chains."""
    chains = ["ethereum", "bsc", "polygon", "arbitrum"]
    
    results = {}
    for chain in chains:
        try:
            pair_data = query_clawby("dex_pair_info", {
                "symbol": token_symbol,
                "chain": chain
            })
            results[chain] = pair_data["data"]
        except:
            results[chain] = None
    
    return results
```

### Automated Risk Check

```python
def check_wallet_risk(address: str, chain: str = "ethereum"):
    """Check AML risk and get address labels."""
    labels = query_clawby("address_labels", {
        "address": address,
        "chain": chain
    })
    
    risk = query_clawby("aml_risk_check", {
        "address": address,
        "chain": chain
    })
    
    return {
        "labels": labels["data"],
        "risk_score": risk["data"]["score"],
        "risk_level": risk["data"]["level"],
        "safe_to_interact": risk["data"]["score"] < 50
    }
```

## Configuration

### Skill-Level Config

The skill checks `local/config.json` for user preferences:

```json
{
  "default_chain": "ethereum",
  "auto_update": true,
  "update_check_interval_hours": 24,
  "trade_confirmation_required": true,
  "max_trade_usd": 1000
}
```

### Interface Routing

The skill automatically routes natural language requests to the correct interface. See `SKILL.md` in the installed directory for the full routing table.

**Example routing logic:**

- "What's GME's short interest?" → `stock_short_interest`
- "BTC funding rates on Binance" → `crypto_funding_rates`
- "Check this Solana wallet" → `address_labels` + `aml_risk_check`
- "Buy 0.1 ETH on Binance" → `exec/binance_connector.py`

## Troubleshooting

### Authentication Errors

```
Error: Invalid API key
```

**Solution**: Verify `CLAWBY_API_KEY` is set correctly:

```bash
echo $CLAWBY_API_KEY  # Should show pk_xxx
# If empty:
export CLAWBY_API_KEY=your_key_here
```

### Credit Limit Reached

```
Error: Credit limit exceeded
```

**Solution**: Check remaining credits:

```python
result = query_clawby("credit_status", {})
print(result["data"]["remaining"])
```

Upgrade plan at [openclawby.com/pricing](https://www.openclawby.com/pricing)

### Trade Execution Fails

```
Error: Exchange API credentials not found
```

**Solution**: Ensure exchange credentials are set as environment variables (see Trade Execution section). Run connector setup:

```bash
python ~/.claude/skills/clawby-data/exec/binance_connector.py --setup
```

### Rate Limiting

```
Error: Rate limit exceeded
```

**Solution**: The API enforces rate limits per tier. Free tier: 10 req/min. Add delays between requests:

```python
import time
results = []
for symbol in symbols:
    results.append(query_clawby("stock_quote", {"symbol": symbol}))
    time.sleep(6)  # 10 req/min = 6s spacing
```

### Interface Not Found

```
Error: Unknown interface name
```

**Solution**: Check the catalog:

```bash
# List all available interfaces
cat ~/.claude/skills/clawby-data/api/clawby.yaml

# Or query the API
curl -X GET https://api.openclawby.com/api/interfaces \
  -H "X-API-Key: $CLAWBY_API_KEY"
```

## Updates

The skill self-updates (with permission). It checks `manifest.json` version once per 24h and prompts before applying changes. Your `local/` directory is never modified.

**Manual update:**

```bash
cd ~/.claude/skills/clawby-data
git pull origin main  # If installed via git
# Or re-download: curl -L https://www.openclawby.com/clawby-data.zip
```

**Version check:**

```bash
cat ~/.claude/skills/clawby-data/manifest.json | grep version
```

## Security Notes

- **Data API**: Calls routed through Clawby with your API key; upstream provider keys stay server-side
- **Trade Execution**: Runs **100% locally** — exchange keys never leave your machine
- **Secrets**: Store only in environment variables or encrypted `local/` files
- **Confirmations**: All trades require explicit user confirmation (configurable via `trade_confirmation_required`)

## Links

- **Website**: [openclawby.com](https://www.openclawby.com/)
- **Data Coverage**: [openclawby.com/data](https://www.openclawby.com/data)
- **Examples**: [openclawby.com/examples](https://www.openclawby.com/examples) (60+ ready-to-use prompts)
- **Pricing**: [openclawby.com/pricing](https://www.openclawby.com/pricing)
- **GitHub**: [github.com/openclawby/openclawby-skills](https://github.com/openclawby/openclawby-skills)
- **X/Twitter**: [@openclawby](https://x.com/openclawby)

---

*Market data is provided for analysis purposes. Not financial or investment advice.*
