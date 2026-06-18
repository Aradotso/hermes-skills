---
name: openclaw-polymarket-trading-bot
description: AI-powered prediction and automated trading bot for Polymarket's 5-minute Bitcoin Up/Down markets
triggers:
  - "set up OpenClaw Polymarket trading bot"
  - "configure Polymarket AI trading bot with CLOB"
  - "run Polymarket prediction bot for BTC markets"
  - "deploy OpenClaw bot with whale flow analysis"
  - "integrate OpenAI with Polymarket trading strategy"
  - "build custom Polymarket trading indicators"
  - "debug Polymarket CLOB order execution"
  - "analyze whale activity on Polymarket BTC markets"
---

# OpenClaw Polymarket Trading Bot

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

OpenClaw is a production-ready TypeScript bot that predicts whether Polymarket's 5-minute BTC Up/Down markets will move up (YES) or down (NO) and executes real orders via the Polymarket CLOB API. It combines momentum indicators, volatility analysis, whale flow tracking, and optional LLM scoring to generate trading signals.

## What It Does

The bot runs a continuous loop that:
1. Identifies the active 5-minute BTC Up/Down market on Polymarket
2. Collects real-time price data and whale flow (large trades ≥ $200)
3. Computes indicators (momentum, volatility, whale bias)
4. Generates predictions using AI-enhanced scoring
5. Places market orders when edge threshold is exceeded
6. Optionally closes positions after a timed interval

## Installation

```bash
git clone https://github.com/Golrypavium/openclaw-polymarket-trading-bot.git
cd openclaw-polymarket-trading-bot
npm install
cp .env.example .env
```

Edit `.env` with your Polymarket CLOB credentials:

```bash
# Required CLOB credentials
PRIVATE_KEY=your_wallet_private_key_hex
CLOB_API_KEY=your_clob_api_key
CLOB_SECRET=your_clob_secret
CLOB_PASS_PHRASE=your_clob_passphrase

# Optional AI enhancement
OPENAI_API_KEY=your_openai_key
```

## Key Commands

```bash
# Run bot in development mode
npm run dev

# Build for production
npm run build

# Run production build
npm start

# Start Signal Lab dashboard
npm run serve
```

## Configuration

### Environment Variables

| Variable | Purpose | Default |
|----------|---------|---------|
| `PRIVATE_KEY` | Wallet private key (64 hex chars) | Required |
| `CLOB_API_KEY` | Polymarket CLOB API key | Required |
| `CLOB_SECRET` | CLOB secret | Required |
| `CLOB_PASS_PHRASE` | CLOB passphrase | Required |
| `LOOP_SECONDS` | Cycle interval | `15` |
| `MAX_POSITION_USD` | Order size in USD | `100` |
| `EDGE_THRESHOLD` | Minimum edge to trade | `0.03` |
| `CLOSE_AFTER_SECONDS` | Auto-close timer (0=hold) | `0` |
| `OPENAI_API_KEY` | Optional LLM enhancement | Empty |
| `OPENAI_MODEL` | Model for predictions | `gpt-4o-mini` |

### Strategy Parameters

The edge threshold determines when to trade:
- `pUp5m > 0.5 + EDGE_THRESHOLD` → Open YES position
- `pUp5m < 0.5 - EDGE_THRESHOLD` → Open NO position
- Otherwise → Hold

Example with `EDGE_THRESHOLD=0.03`:
- Trade YES if pUp5m > 0.53
- Trade NO if pUp5m < 0.47

## Core Architecture

### Data Flow

```typescript
// core/entry.ts - Main bot loop
import { opts } from './settings';
import { getMarketSnapshot } from './adapters/eventSource';
import { computeIndicators } from './strategy/indicators';
import { generateForecast } from './strategy/forecast';
import { decideAction } from './strategy/simulator';
import { submitBuy } from './adapters/orderBridge';

async function runCycle() {
  // 1. Get current market and price data
  const snapshot = await getMarketSnapshot();
  
  // 2. Build indicators
  const indicators = computeIndicators(snapshot.ticks, snapshot.whale);
  
  // 3. Generate forecast
  const forecast = await generateForecast(indicators);
  
  // 4. Decide action
  const action = decideAction(forecast, snapshot.currentPrice);
  
  // 5. Execute if needed
  if (action.type === 'OPEN_YES') {
    await submitBuy('YES', action.size, snapshot.market);
  }
}
```

### Market Selection

```typescript
// core/adapters/eventSource.ts
import { opts } from '../settings';

export async function getCurrentMarket(): Promise<MarketInfo> {
  const gammaUrl = `${opts.polymarketRestBase}/markets`;
  const response = await fetch(gammaUrl);
  const markets = await response.json();
  
  // Find active 5-min BTC market by time bucket
  const now = Date.now();
  const btcMarket = markets.find(m => 
    m.question.includes('BTC') &&
    m.question.includes('5 minutes') &&
    m.endDate > now &&
    m.startDate <= now
  );
  
  return {
    conditionId: btcMarket.conditionId,
    question: btcMarket.question,
    yesTokenId: btcMarket.tokens.YES,
    noTokenId: btcMarket.tokens.NO
  };
}
```

### Indicator Calculation

```typescript
// core/strategy/indicators.ts
import { PricePoint, WhaleActivity, IndicatorSet } from '../schema/defs';

export function computeIndicators(
  ticks: PricePoint[],
  whale: WhaleActivity
): IndicatorSet {
  // Momentum: recent price changes
  const shortReturn = computeReturn(ticks, 2); // ~30s ago
  const medReturn = computeReturn(ticks, 8);   // ~2min ago
  
  // Volatility: recent price range
  const recentPrices = ticks.slice(-8).map(t => t.price);
  const volatility = Math.max(...recentPrices) - Math.min(...recentPrices);
  
  // Whale bias: net YES vs NO from large trades
  const whaleBias = (whale.yesVolume - whale.noVolume) / 
    (whale.yesVolume + whale.noVolume + 0.01);
  
  return {
    shortReturn,
    medReturn,
    volatility,
    whaleBias,
    whaleIntensity: whale.yesVolume + whale.noVolume
  };
}

function computeReturn(ticks: PricePoint[], lookback: number): number {
  if (ticks.length < lookback + 1) return 0;
  const current = ticks[ticks.length - 1].price;
  const past = ticks[ticks.length - lookback - 1].price;
  return (current - past) / (past + 0.01);
}
```

### Forecast Generation

```typescript
// core/strategy/forecast.ts
import { IndicatorSet, Forecast } from '../schema/defs';
import { scoreLLM } from '../ai/remoteScorer';
import { opts } from '../settings';

export async function generateForecast(
  indicators: IndicatorSet
): Promise<Forecast> {
  // Base probability from momentum and whale bias
  let pUp5m = 0.5;
  
  // Momentum component (40% weight)
  const momentumSignal = (indicators.shortReturn * 0.6 + 
                          indicators.medReturn * 0.4);
  pUp5m += momentumSignal * 0.4;
  
  // Whale bias component (30% weight)
  pUp5m += indicators.whaleBias * 0.3;
  
  // Volatility dampening
  const volFactor = Math.min(indicators.volatility * 2, 0.1);
  pUp5m = 0.5 + (pUp5m - 0.5) * (1 - volFactor);
  
  // Optional LLM enhancement (30% weight)
  let llmBias = 0;
  if (opts.openaiApiKey) {
    llmBias = await scoreLLM(indicators);
    pUp5m += llmBias * 0.3;
  }
  
  // Clamp to [0.2, 0.8]
  pUp5m = Math.max(0.2, Math.min(0.8, pUp5m));
  
  const confidence = Math.abs(pUp5m - 0.5) * 2; // [0, 1]
  
  return { pUp5m, confidence, llmBias };
}
```

### LLM Integration

```typescript
// core/ai/remoteScorer.ts
import { IndicatorSet } from '../schema/defs';
import { opts } from '../settings';

export async function scoreLLM(indicators: IndicatorSet): Promise<number> {
  const prompt = `Predict Bitcoin 5-minute direction.
Indicators:
- Short momentum: ${indicators.shortReturn.toFixed(4)}
- Medium momentum: ${indicators.medReturn.toFixed(4)}
- Volatility: ${indicators.volatility.toFixed(4)}
- Whale bias: ${indicators.whaleBias.toFixed(3)}

Respond with a number from -1 (strong DOWN) to +1 (strong UP).`;

  const response = await fetch(`${opts.openaiBaseUrl}/chat/completions`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${opts.openaiApiKey}`
    },
    body: JSON.stringify({
      model: opts.openaiModel,
      messages: [{ role: 'user', content: prompt }],
      temperature: 0.3,
      max_tokens: 10
    })
  });
  
  const data = await response.json();
  const text = data.choices[0].message.content.trim();
  const bias = parseFloat(text);
  
  return isNaN(bias) ? 0 : Math.max(-1, Math.min(1, bias));
}
```

### Order Execution

```typescript
// core/adapters/orderBridge.ts
import { ClobClient } from '@polymarket/clob-client';
import { opts } from '../settings';

const clobClient = new ClobClient(
  opts.clobApiUrl,
  opts.clobChainId,
  opts.privateKey,
  {
    apiKey: opts.clobApiKey,
    secret: opts.clobSecret,
    passphrase: opts.clobPassPhrase
  }
);

export async function submitBuy(
  side: 'YES' | 'NO',
  usdSize: number,
  market: MarketInfo
): Promise<string> {
  const tokenId = side === 'YES' ? market.yesTokenId : market.noTokenId;
  
  // Get current best price
  const book = await clobClient.getOrderBook(tokenId);
  const price = side === 'YES' 
    ? parseFloat(book.asks[0]?.price || '0.5')
    : parseFloat(book.bids[0]?.price || '0.5');
  
  const shares = usdSize / price;
  
  const order = await clobClient.createMarketBuyOrder({
    tokenID: tokenId,
    amount: shares.toString(),
    feeRateBps: '0',
    nonce: Date.now()
  });
  
  const result = await clobClient.postOrder(order);
  
  // Record position
  recordPosition({
    orderId: result.orderID,
    side,
    entryPrice: price,
    shares,
    timestamp: Date.now(),
    marketId: market.conditionId
  });
  
  return result.orderID;
}
```

### Position Management

```typescript
// core/strategy/book.ts
import fs from 'fs';

interface Position {
  orderId: string;
  side: 'YES' | 'NO';
  entryPrice: number;
  shares: number;
  timestamp: number;
  marketId: string;
}

const POSITIONS_FILE = './positions.json';

export function recordPosition(pos: Position): void {
  const positions = loadPositions();
  positions.push(pos);
  fs.writeFileSync(POSITIONS_FILE, JSON.stringify(positions, null, 2));
}

export function getExpiredPositions(closeAfterSeconds: number): Position[] {
  const positions = loadPositions();
  const now = Date.now();
  return positions.filter(p => 
    (now - p.timestamp) / 1000 > closeAfterSeconds
  );
}

export function removePosition(orderId: string): void {
  const positions = loadPositions();
  const filtered = positions.filter(p => p.orderId !== orderId);
  fs.writeFileSync(POSITIONS_FILE, JSON.stringify(filtered, null, 2));
}

function loadPositions(): Position[] {
  if (!fs.existsSync(POSITIONS_FILE)) return [];
  return JSON.parse(fs.readFileSync(POSITIONS_FILE, 'utf-8'));
}
```

### Timed Position Closing

```typescript
// In core/entry.ts cycle
import { getExpiredPositions, removePosition } from './strategy/book';
import { submitSell } from './adapters/orderBridge';

async function closeExpiredPositions() {
  if (opts.closeAfterSeconds === 0) return;
  
  const expired = getExpiredPositions(opts.closeAfterSeconds);
  
  for (const pos of expired) {
    try {
      await submitSell(pos.side, pos.shares, pos.marketId);
      removePosition(pos.orderId);
      console.log(`Closed position ${pos.orderId} after timeout`);
    } catch (err) {
      console.error(`Failed to close ${pos.orderId}:`, err);
    }
  }
}
```

## Signal Lab Dashboard

### Starting the Dashboard

```bash
npm run serve
# Opens on http://localhost:8787
```

### Prediction API

```typescript
// core/dashboard.ts
import express from 'express';
import { getMarketSnapshot } from './adapters/eventSource';
import { computeIndicators } from './strategy/indicators';
import { generateForecast } from './strategy/forecast';

const app = express();

app.get('/api/prediction', async (req, res) => {
  try {
    const snapshot = await getMarketSnapshot();
    const indicators = computeIndicators(snapshot.ticks, snapshot.whale);
    const forecast = await generateForecast(indicators);
    
    res.json({
      market: snapshot.market.question,
      currentPrice: snapshot.currentPrice,
      prediction: forecast.pUp5m,
      confidence: forecast.confidence,
      recommendedSide: forecast.pUp5m > 0.5 ? 'YES' : 'NO',
      indicators: {
        momentum: indicators.shortReturn,
        volatility: indicators.volatility,
        whaleBias: indicators.whaleBias
      }
    });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

app.listen(8787);
```

### Auto-Compare Feature

The dashboard can backtest predictions:

1. Click "Get Prediction" to fetch current forecast
2. Set "Entry YES price" and "Settle after (sec)"
3. Click "Auto Compare"
4. Dashboard waits, then fetches new price and records accuracy

Results are stored in browser localStorage and displayed in the History table.

## Common Patterns

### Custom Indicator

```typescript
// Add to core/strategy/indicators.ts
export function computeCustomIndicator(ticks: PricePoint[]): number {
  // Example: RSI-like oscillator
  const gains = [];
  const losses = [];
  
  for (let i = 1; i < ticks.length; i++) {
    const change = ticks[i].price - ticks[i-1].price;
    if (change > 0) gains.push(change);
    else losses.push(-change);
  }
  
  const avgGain = gains.reduce((a, b) => a + b, 0) / (gains.length || 1);
  const avgLoss = losses.reduce((a, b) => a + b, 0) / (losses.length || 1);
  
  return avgGain / (avgGain + avgLoss + 0.01); // [0, 1]
}
```

### Multi-Market Support

```typescript
// Extend getMarketSnapshot to handle multiple markets
export async function getAllActiveMarkets(): Promise<MarketInfo[]> {
  const response = await fetch(`${opts.polymarketRestBase}/markets`);
  const markets = await response.json();
  
  return markets.filter(m => 
    m.active && 
    m.question.includes('5 minutes')
  );
}

// Run separate strategies per market
async function runMultiMarket() {
  const markets = await getAllActiveMarkets();
  
  for (const market of markets) {
    const snapshot = await getMarketSnapshot(market.conditionId);
    const indicators = computeIndicators(snapshot.ticks, snapshot.whale);
    const forecast = await generateForecast(indicators);
    // ... trade logic
  }
}
```

### Risk Management

```typescript
// Add to core/strategy/simulator.ts
export function applySizeLimit(
  forecast: Forecast,
  maxUsd: number,
  openPositions: Position[]
): number {
  // Limit by confidence
  const baseSize = maxUsd * forecast.confidence;
  
  // Reduce size if already holding positions
  const exposureMultiplier = 1 - (openPositions.length * 0.2);
  
  return baseSize * Math.max(0.2, exposureMultiplier);
}
```

### Webhook Notifications

```typescript
// Add to core/entry.ts after order execution
async function notifyTrade(action: string, orderId: string, price: number) {
  if (!process.env.WEBHOOK_URL) return;
  
  await fetch(process.env.WEBHOOK_URL, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      action,
      orderId,
      price,
      timestamp: new Date().toISOString()
    })
  });
}
```

## Troubleshooting

### CLOB Authentication Errors

```
Error: Invalid API credentials
```

**Solution**: Verify `.env` credentials match your Polymarket CLOB API keys:

```bash
# Test credentials
node -e "console.log(process.env.CLOB_API_KEY?.length)"
# Should output key length, not undefined
```

Re-generate keys at https://clob.polymarket.com if needed.

### Market Not Found

```
Error: No active 5-minute BTC market found
```

**Solution**: The bot requires an active 5-min BTC Up/Down market. Check Polymarket manually or adjust market selection logic:

```typescript
// Fallback to any active binary market
const fallback = markets.find(m => m.active && m.outcomes.length === 2);
```

### Whale Flow Empty

```
Whale activity: { yesVolume: 0, noVolume: 0 }
```

**Solution**: Increase lookback window or lower whale threshold:

```typescript
// In core/adapters/eventSource.ts
const WHALE_THRESHOLD = 100; // Lower from default 200
const LOOKBACK_MINUTES = 10; // Increase from default 5
```

### LLM Timeouts

```
Error: OpenAI request timeout
```

**Solution**: Add retry logic or use a faster model:

```typescript
// core/ai/remoteScorer.ts
export async function scoreLLM(indicators: IndicatorSet): Promise<number> {
  try {
    // Existing LLM call with timeout
    const controller = new AbortController();
    const timeout = setTimeout(() => controller.abort(), 5000);
    
    const response = await fetch(url, { 
      signal: controller.signal,
      ...options 
    });
    
    clearTimeout(timeout);
    return parseBias(response);
  } catch (err) {
    console.warn('LLM timeout, using fallback bias');
    return 0; // Neutral fallback
  }
}
```

### Position File Corruption

```
Error: Unexpected token in JSON at position 0
```

**Solution**: Reset positions file:

```bash
echo "[]" > positions.json
```

Or add error handling:

```typescript
function loadPositions(): Position[] {
  try {
    if (!fs.existsSync(POSITIONS_FILE)) return [];
    return JSON.parse(fs.readFileSync(POSITIONS_FILE, 'utf-8'));
  } catch (err) {
    console.error('Corrupted positions file, resetting:', err);
    fs.writeFileSync(POSITIONS_FILE, '[]');
    return [];
  }
}
```

### Insufficient Balance

```
Error: Insufficient balance for order
```

**Solution**: Check wallet balance on Polygon:

```typescript
import { ethers } from 'ethers';

const provider = new ethers.JsonRpcProvider('https://polygon-rpc.com');
const wallet = new ethers.Wallet(opts.privateKey, provider);
const balance = await wallet.getBalance();
console.log(`Balance: ${ethers.formatEther(balance)} MATIC`);
```

Reduce `MAX_POSITION_USD` if needed.

## Validation on Startup

The bot validates configuration before running:

```typescript
// core/setupGuard.ts
export function validateSetup(): string[] {
  const errors: string[] = [];
  
  if (!opts.privateKey || !/^[0-9a-fA-F]{64}$/.test(opts.privateKey)) {
    errors.push('PRIVATE_KEY must be 64 hex characters');
  }
  
  if (opts.clobApiKey === 'your_clob_api_key') {
    errors.push('CLOB_API_KEY is still placeholder');
  }
  
  if (opts.maxPositionUsd <= 0 || opts.maxPositionUsd > 10000) {
    errors.push('MAX_POSITION_USD must be between 0 and 10000');
  }
  
  return errors;
}

// In core/entry.ts
const setupErrors = validateSetup();
if (setupErrors.length > 0) {
  console.error('Setup validation failed:');
  setupErrors.forEach(e => console.error(`  - ${e}`));
  process.exit(1);
}
```

## Best Practices

1. **Start small**: Begin with low `MAX_POSITION_USD` (e.g., $10) while testing
2. **Monitor logs**: Watch for `LIVE BUY` and position updates
3. **Use timed close**: Set `CLOSE_AFTER_SECONDS=300` to avoid holding through market resolution
4. **Test without LLM first**: Validate core strategy before adding OpenAI costs
5. **Backtest with Signal Lab**: Use Auto-Compare to validate prediction accuracy before live trading
6. **Version control positions**: Keep `positions.json` in `.gitignore` but backup periodically
7. **Rate limiting**: Polymarket CLOB has rate limits; avoid `LOOP_SECONDS` < 10
