---
name: moontv-openclaw-skill
description: Daily movie and TV show info aggregator with LLM-generated highlights, multi-source scraping, and smart ranking
triggers:
  - "set up moontv daily feed"
  - "create a movie and TV show recommendation system"
  - "scrape CMS movie sources"
  - "generate daily movie highlights with LLM"
  - "build a watchlist tracker"
  - "aggregate multiple video sources"
  - "rank movies by douban score and popularity"
  - "create automated daily media reports"
---

# MoonTV OpenClaw Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill enables AI agents to help developers use **MoonTV OpenClaw**, a Python-based movie and TV show aggregator that scrapes multiple CMS sources, ranks content by Douban scores and popularity, generates LLM-powered highlights, and produces daily Markdown reports with watchlist tracking.

## What MoonTV Does

MoonTV OpenClaw:
- **Multi-source aggregation**: Concurrently fetches from 400+ CMS sources via a gateway API
- **Smart deduplication**: Removes duplicates by `vod_name`, keeping first occurrence
- **5-category ranking**: Movies, TV shows, variety shows, short dramas, and special content (Top 5 each)
- **Dual-path scoring**: Uses Douban ratings when available, otherwise falls back to popularity-based scoring
- **Watchlist tracking**: Monitors configured shows for updates
- **LLM highlights**: Generates content highlights via GPT-4o-mini (with fallback to synopsis truncation)
- **Auto cleanup**: Removes reports older than 7 days

## Installation

### Prerequisites

- Python 3.11+
- Network access to CMS sources

### Clone and Setup

```bash
git clone https://github.com/doane2002cn/moontv-openclaw-skill.git
cd moontv-openclaw-skill

# Copy environment template
cp .env.example .env
```

### Configure Environment

Edit `.env`:

```env
# Required: MoonTV gateway and play URL
MOONTV_GATEWAY=https://moontv-api.12879737.xyz
MOONTV_PLAY_URL=https://moontv.dduan2002cn.xyz/play

# Optional: Watchlist configuration
WATCHLIST_FILE=config/watchlist.json

# Optional: LLM for highlights (auto-fallback if not set)
LLM_GATEWAY=https://api.gptgod.online/v1
LLM_MODEL=gpt-4o-mini
LLM_API_KEY=your-api-key-from-env
```

**Never hardcode API keys**. Use environment variables or secret managers.

## Project Structure

```
moontv-openclaw/
├── scripts/
│   ├── moontv_daily.py         # Main scraper & orchestrator
│   ├── report_template.py      # Markdown report renderer
│   ├── highlight_generator.py  # LLM highlight generator
│   └── test_moontv.py          # Test suite
├── config/
│   └── watchlist.json          # Watchlist configuration
├── output/                     # Generated reports (auto-created)
└── .env                        # Environment variables
```

## Running MoonTV

### Generate Daily Report

```bash
cd scripts
python moontv_daily.py
```

This will:
1. Fetch available CMS sources from gateway
2. Scrape all sources concurrently (with 10-second timeout per source)
3. Deduplicate by `vod_name`
4. Classify into 5 categories
5. Score and rank items
6. Match watchlist items (if configured)
7. Generate LLM highlights (or fallback)
8. Render Markdown report to `output/`
9. Clean up old reports (>7 days)

### Run Tests

```bash
cd scripts
python -m pytest test_moontv.py -v
```

## Key Python Modules

### 1. Main Scraper (`moontv_daily.py`)

**Fetch Gateway Sources:**

```python
import requests
import os
from dotenv import load_dotenv

load_dotenv()

def fetch_gateway():
    """Fetch available CMS sources from gateway"""
    gateway_url = os.getenv("MOONTV_GATEWAY")
    response = requests.get(f"{gateway_url}/api/resource/sources", timeout=10)
    response.raise_for_status()
    data = response.json()
    
    if data["code"] != 0 or not data["data"]:
        raise Exception("Gateway returned no sources")
    
    return data["data"]  # List of dicts: [{"name": "...", "api": "..."}, ...]
```

**Scrape CMS Source:**

```python
def fetch_cms_data(api_url):
    """Fetch today's data from a single CMS source"""
    response = requests.get(
        f"{api_url}?ac=videolist&t=1,2,3,4,5",
        timeout=10
    )
    response.raise_for_status()
    return response.json()
```

**Deduplicate:**

```python
def deduplicate(items):
    """Remove duplicates by vod_name, keep first occurrence"""
    seen = set()
    unique = []
    for item in items:
        name = item.get("vod_name")
        if name and name not in seen:
            seen.add(name)
            unique.append(item)
    return unique
```

**Classify Items:**

```python
def classify(items):
    """Classify into 5 categories with priority"""
    categories = {
        "电影": [], "剧集": [], "综艺": [], "短剧": [], "福利": []
    }
    
    for item in items:
        type_name = item.get("type_name", "")
        
        # Priority order: 电影 > 剧集 > 综艺 > 短剧 > 福利
        if "电影" in type_name:
            categories["电影"].append(item)
        elif any(x in type_name for x in ["连续", "电视剧", "美剧", "韩剧"]):
            categories["剧集"].append(item)
        elif "综艺" in type_name:
            categories["综艺"].append(item)
        elif "短剧" in type_name:
            categories["短剧"].append(item)
        else:
            categories["福利"].append(item)
    
    return categories
```

**Scoring Algorithm:**

```python
from datetime import datetime, timedelta

def calculate_score(item, max_hits):
    """Dual-path weighted scoring"""
    douban = float(item.get("vod_douban_score", 0))
    hits = int(item.get("vod_hits", 0))
    time_str = item.get("vod_time", "")
    
    # Normalize popularity (0-10)
    normalized_hits = (hits / max_hits * 10) if max_hits > 0 else 0
    
    # Time bonus (10 if within 12 hours, else 0)
    time_bonus = 0
    try:
        vod_time = datetime.fromisoformat(time_str)
        if datetime.now() - vod_time < timedelta(hours=12):
            time_bonus = 10
    except:
        pass
    
    # Dual-path scoring
    if douban > 0:
        score = douban * 0.6 + normalized_hits * 0.3 + time_bonus * 0.1
    else:
        baseline = 5.0
        score = normalized_hits * 0.6 + time_bonus * 0.3 + baseline * 0.1
    
    return round(score, 2)
```

### 2. Report Renderer (`report_template.py`)

**Format Single Item:**

```python
def format_item(item, rank, play_base_url):
    """Format a single movie/TV item as Markdown"""
    name = item.get("vod_name", "未知")
    score = item.get("综合评分", 0)
    douban = item.get("vod_douban_score", 0)
    vod_id = item.get("vod_id", "")
    episode = extract_episode(item)
    
    # Build play URL
    play_url = f"{play_base_url}?id={vod_id}"
    
    # Format output
    lines = [
        f"**{rank}. [{name}]({play_url})**",
        f"   - 综合评分：{score}"
    ]
    
    if douban > 0:
        lines.append(f"   - 豆瓣评分：{douban}")
    
    if episode:
        lines.append(f"   - 最新：{episode}")
    
    # Add highlight if available
    if "亮点" in item and item["亮点"]:
        lines.append(f"   - 💡 {item['亮点']}")
    
    return "\n".join(lines)
```

**Render Full Report:**

```python
def render_report(top_items, watchlist_updates, date_str):
    """Render complete Markdown report"""
    lines = [
        f"# 📺 MoonTV 每日精选 ({date_str})",
        "",
        "---",
        ""
    ]
    
    # Categories
    categories = ["电影", "剧集", "综艺", "短剧", "福利"]
    for cat in categories:
        if cat in top_items and top_items[cat]:
            lines.append(f"## {cat} Top 5")
            lines.append("")
            for i, item in enumerate(top_items[cat], 1):
                lines.append(format_item(item, i, play_base_url))
                lines.append("")
    
    # Watchlist section
    if watchlist_updates:
        lines.append("## 📌 追剧更新")
        lines.append("")
        for item in watchlist_updates:
            lines.append(format_item(item, "📌", play_base_url))
            lines.append("")
    
    return "\n".join(lines)
```

### 3. Highlight Generator (`highlight_generator.py`)

**Build LLM Prompt:**

```python
def build_prompt(items):
    """Build batch prompt for LLM highlight generation"""
    item_list = []
    for item in items:
        item_list.append({
            "name": item.get("vod_name", ""),
            "synopsis": item.get("vod_content", "")[:200]  # Truncate long synopses
        })
    
    prompt = f"""请为以下影视作品生成简短亮点（15字以内），以JSON数组返回。

作品列表：
{json.dumps(item_list, ensure_ascii=False, indent=2)}

返回格式示例：
[
  {{"name": "作品名", "highlight": "亮点描述"}},
  ...
]
"""
    return prompt
```

**Call LLM:**

```python
import openai
import os

def generate_highlights(items):
    """Generate highlights via LLM, fallback to synopsis truncation"""
    api_key = os.getenv("LLM_API_KEY")
    if not api_key:
        return fallback_highlights(items)
    
    try:
        client = openai.OpenAI(
            api_key=api_key,
            base_url=os.getenv("LLM_GATEWAY")
        )
        
        prompt = build_prompt(items)
        
        response = client.chat.completions.create(
            model=os.getenv("LLM_MODEL", "gpt-4o-mini"),
            messages=[{"role": "user", "content": prompt}],
            temperature=0.7
        )
        
        result = response.choices[0].message.content
        highlights = json.loads(result)
        
        # Map highlights back to items
        highlight_map = {h["name"]: h["highlight"] for h in highlights}
        for item in items:
            item["亮点"] = highlight_map.get(item.get("vod_name", ""), "")
        
        return items
    
    except Exception as e:
        print(f"LLM failed: {e}, using fallback")
        return fallback_highlights(items)
```

**Fallback Highlights:**

```python
import re

def fallback_highlights(items):
    """Fallback: truncate synopsis to 30 chars"""
    for item in items:
        synopsis = item.get("vod_content", "")
        # Remove punctuation, take first 30 chars
        clean = re.sub(r'[，。！？、；：""''（）《》【】]', '', synopsis)
        highlight = clean[:30] if clean else "精彩内容，不容错过"
        item["亮点"] = highlight
    return items
```

## Watchlist Configuration

Create `config/watchlist.json`:

```json
{
  "watchlist": [
    {"name": "大唐迷雾", "type": "剧集"},
    {"name": "认识的哥哥", "type": "综艺"},
    {"name": "梦魇绝镇", "type": "剧集"}
  ]
}
```

**Match Watchlist in Code:**

```python
import json

def match_watchlist(all_items, watchlist_file):
    """Match items against watchlist"""
    try:
        with open(watchlist_file, 'r', encoding='utf-8') as f:
            config = json.load(f)
        watchlist = config.get("watchlist", [])
    except:
        return []
    
    matches = []
    for watch in watchlist:
        for item in all_items:
            if item.get("vod_name") == watch["name"]:
                matches.append(item)
                break
    
    return matches
```

## Common Patterns

### 1. Concurrent Source Scraping

```python
from concurrent.futures import ThreadPoolExecutor, as_completed

def scrape_all_sources(sources):
    """Scrape all CMS sources concurrently"""
    all_items = []
    
    with ThreadPoolExecutor(max_workers=10) as executor:
        futures = {
            executor.submit(fetch_cms_data, src["api"]): src["name"]
            for src in sources
        }
        
        for future in as_completed(futures):
            source_name = futures[future]
            try:
                data = future.result()
                items = data.get("list", [])
                all_items.extend(items)
                print(f"✓ {source_name}: {len(items)} items")
            except Exception as e:
                print(f"✗ {source_name}: {e}")
    
    return all_items
```

### 2. Filter Low-Quality Content

```python
def filter_low_quality(items):
    """Remove items with no name, score, or hits"""
    return [
        item for item in items
        if item.get("vod_name") 
        and (item.get("vod_douban_score", 0) > 0 or item.get("vod_hits", 0) > 0)
    ]
```

### 3. Extract Episode Number

```python
import re

def extract_episode(item):
    """Extract episode info from vod_remarks"""
    remarks = item.get("vod_remarks", "")
    
    # Match patterns like "更新至12集", "第10集", "EP08"
    patterns = [
        r'更新至(\d+)集',
        r'第(\d+)集',
        r'EP(\d+)',
        r'(\d+)集全'
    ]
    
    for pattern in patterns:
        match = re.search(pattern, remarks)
        if match:
            return f"第{match.group(1)}集"
    
    return remarks if remarks else ""
```

### 4. Clean Old Reports

```python
import os
from datetime import datetime, timedelta
from pathlib import Path

def cleanup_old_reports(output_dir, days=7):
    """Delete reports older than N days"""
    cutoff = datetime.now() - timedelta(days=days)
    
    for file in Path(output_dir).glob("moontv_*.md"):
        if file.stat().st_mtime < cutoff.timestamp():
            file.unlink()
            print(f"Deleted old report: {file.name}")
```

## Troubleshooting

### Gateway Not Accessible

**Symptom**: `requests.exceptions.RequestException` when calling gateway

**Solution**: Check `MOONTV_GATEWAY` in `.env`, verify network access:

```bash
curl https://moontv-api.12879737.xyz/api/resource/sources
```

### No Data Returned

**Symptom**: "Gateway returned no sources"

**Solution**: Gateway may be rate-limited or down. Wait and retry, or check gateway status.

### LLM Highlights Not Generated

**Symptom**: All highlights are truncated synopses

**Solution**: 
- Verify `LLM_API_KEY` is set correctly
- Check `LLM_GATEWAY` is accessible
- Review LLM API quota/credits
- This is expected behavior when LLM is unavailable (automatic fallback)

### Import Errors

**Symptom**: `ModuleNotFoundError: No module named 'openai'`

**Solution**: Install dependencies:

```bash
pip install requests openai python-dotenv
```

### Watchlist Not Matching

**Symptom**: Watchlist section empty despite configured items

**Solution**:
- Ensure `WATCHLIST_FILE` path is correct
- Verify `name` in `watchlist.json` matches exact `vod_name` from CMS
- Check watchlist items are actually present in scraped data

### Empty Categories

**Symptom**: Some Top 5 sections missing

**Solution**: 
- This is normal if certain categories have no data from sources
- Check `classify()` logic matches your CMS `type_name` values
- Verify sources are returning data for those categories

## Testing

Run the test suite:

```bash
cd scripts
python -m pytest test_moontv.py -v
```

Key test coverage:
- Deduplication logic
- Classification priority
- Scoring algorithms (with/without Douban)
- Report formatting
- LLM prompt building
- Fallback highlights

## Performance Tips

1. **Concurrent scraping**: Adjust `max_workers` in `ThreadPoolExecutor` based on your network
2. **Timeout tuning**: Default 10s timeout per source; increase for slow networks
3. **LLM batching**: All highlights generated in single LLM call to save tokens
4. **Caching**: Consider caching gateway sources for 1 hour to reduce API calls

## Advanced Usage

### Custom Scoring Weights

Modify weights in `calculate_score()`:

```python
if douban > 0:
    # Prioritize Douban score more
    score = douban * 0.7 + normalized_hits * 0.2 + time_bonus * 0.1
else:
    # Trust popularity more
    score = normalized_hits * 0.7 + time_bonus * 0.2 + baseline * 0.1
```

### Add New Categories

Extend `classify()`:

```python
categories = {
    "电影": [], "剧集": [], "综艺": [], "短剧": [], "福利": [],
    "动漫": []  # New category
}

# Add classification logic
elif "动漫" in type_name or "动画" in type_name:
    categories["动漫"].append(item)
```

### Custom Report Format

Modify `render_report()` in `report_template.py`:

```python
# Add emoji icons per category
icons = {
    "电影": "🎬",
    "剧集": "📺",
    "综艺": "🎤",
    "短剧": "📱",
    "福利": "🎁"
}

lines.append(f"## {icons.get(cat, '📌')} {cat} Top 5")
```

This skill provides everything an AI agent needs to help developers deploy, configure, and customize MoonTV OpenClaw for automated movie/TV content aggregation and recommendation.
