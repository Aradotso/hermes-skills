---
name: openclaw-marketing-skills
description: Use 37 battle-tested marketing skills covering CRO, copywriting, SEO, paid ads, email, growth, and strategy with real data connectors for Google Ads, Search Console, Meta Ads, and X/Twitter
triggers:
  - set up openclaw marketing skills
  - install marketing skills for openclaw agent
  - connect google ads to openclaw
  - audit conversion rate with openclaw
  - write marketing copy with openclaw
  - optimize SEO with openclaw marketing
  - create email sequence with openclaw
  - analyze paid ads performance with openclaw
---

# openclaw-marketing-skills

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

openclaw-marketing-skills provides 37 production-ready marketing skills for OpenClaw agents, covering the full marketing stack: CRO, copywriting, SEO, paid ads, email, growth, retention, sales enablement, and strategy. Includes data connectors for Google Ads, Google Search Console, Meta Ads, and X/Twitter to pull live account data and social signals directly into agent workflows.

## Installation

### Via ClawHub (recommended)

```bash
# Install all 37 skills
clawhub install LeoYeAI/openclaw-marketing-skills

# Install specific skills only
clawhub install LeoYeAI/openclaw-marketing-skills --skill copywriting page-cro seo-audit google-ads-connect
```

### Manual installation

```bash
git clone https://github.com/LeoYeAI/openclaw-marketing-skills.git
cp -r openclaw-marketing-skills/skills/* ~/.agents/skills/
```

### Verify installation

Check that skills are available:

```bash
ls ~/.agents/skills/ | grep -E "(product-marketing-context|copywriting|page-cro|google-ads-connect)"
```

## Initial Setup

### 1. Create product marketing context (required first step)

All 37 skills automatically reference `.agents/product-marketing-context.md`. Create it once:

```bash
# Invoke the product-marketing-context skill
openclaw ask "Set up my product marketing context"
```

The agent will ask:
- Product name and what it does
- Target customer (role, company size, industry)
- Key value proposition and differentiation
- Pricing model and tiers
- Main competitors
- Current stage (pre-launch, early, growth, scale)

This context file is automatically loaded by all skills, eliminating repeated questions.

### 2. Connect data sources (optional but recommended)

#### Google Ads

```bash
openclaw ask "Connect my Google Ads account"
# Skill: google-ads-connect
```

Requires:
- `GOOGLE_ADS_CLIENT_ID` (OAuth2 client ID)
- `GOOGLE_ADS_CLIENT_SECRET`
- `GOOGLE_ADS_DEVELOPER_TOKEN`
- `GOOGLE_ADS_CUSTOMER_ID`
- `GOOGLE_ADS_REFRESH_TOKEN`

Set in `.env` or agent config:

```bash
export GOOGLE_ADS_CLIENT_ID="your-client-id"
export GOOGLE_ADS_CLIENT_SECRET="your-secret"
export GOOGLE_ADS_DEVELOPER_TOKEN="your-dev-token"
export GOOGLE_ADS_CUSTOMER_ID="123-456-7890"
export GOOGLE_ADS_REFRESH_TOKEN="your-refresh-token"
```

#### Google Search Console

```bash
openclaw ask "Connect Google Search Console"
# Skill: search-console-connect
```

Requires:
- `GOOGLE_SEARCH_CONSOLE_SITE_URL` (verified property URL)
- Service account JSON or OAuth2 credentials

```bash
export GOOGLE_SEARCH_CONSOLE_SITE_URL="https://example.com"
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/service-account.json"
```

#### Meta Ads

```bash
openclaw ask "Connect my Meta Ads account"
# Skill: meta-ads-connect
```

Requires:
- `META_ACCESS_TOKEN` (long-lived user access token)
- `META_AD_ACCOUNT_ID` (act_123456789)

```bash
export META_ACCESS_TOKEN="your-long-lived-token"
export META_AD_ACCOUNT_ID="act_123456789"
```

#### X/Twitter (via TweetClaw)

```bash
# Install TweetClaw plugin first
openclaw plugins install @xquik/tweetclaw

# Connect X/Twitter
openclaw ask "Connect TweetClaw for X/Twitter research"
# Skill: x-twitter-connect
```

Requires:
- X/Twitter API credentials (via TweetClaw config)

```bash
export TWITTER_API_KEY="your-api-key"
export TWITTER_API_SECRET="your-api-secret"
export TWITTER_ACCESS_TOKEN="your-access-token"
export TWITTER_ACCESS_SECRET="your-access-secret"
```

## Core Skills Reference

### Foundation

**product-marketing-context** — Creates the central product context file that all other skills reference

```bash
openclaw ask "Set up my product marketing context"
openclaw ask "Update our target customer in the marketing context"
```

### CRO (Conversion Rate Optimization)

**page-cro** — Audits marketing pages for conversion killers

```bash
openclaw ask "Audit /pricing page for conversion issues"
openclaw ask "Review homepage and prioritize CRO fixes by impact"
```

**signup-flow-cro** — Reduces friction in registration flows

```bash
openclaw ask "Optimize our signup flow for conversions"
openclaw ask "Analyze trial activation funnel and identify dropoff points"
```

**onboarding-cro** — Improves time-to-value and activation rates

```bash
openclaw ask "Audit our user onboarding and find the aha moment blockers"
```

**form-cro** — Optimizes lead gen, checkout, and contact forms

```bash
openclaw ask "Review our demo request form and reduce abandonment"
```

**popup-cro** — Designs exit-intent modals and overlays

```bash
openclaw ask "Create an exit-intent popup for trial users"
```

**paywall-upgrade-cro** — Improves in-app upgrade conversion

```bash
openclaw ask "Optimize our paywall upgrade moment"
```

### Copywriting & Content

**copywriting** — Writes homepage, landing page, and feature copy

```bash
openclaw ask "Write homepage copy for our new product"
openclaw ask "Create landing page copy for enterprise plan"
```

**copy-editing** — Sharpens existing copy

```bash
openclaw ask "Edit this feature description to be clearer and more benefit-focused: [paste copy]"
```

**cold-email** — B2B outreach sequences

```bash
openclaw ask "Write a 3-email cold outreach sequence for SaaS CTOs"
```

**email-sequence** — Automated drip and lifecycle emails

```bash
openclaw ask "Create a 5-email welcome sequence for trial users"
openclaw ask "Write a win-back sequence for churned customers"
```

**social-content** — LinkedIn, Twitter/X, Instagram content

```bash
openclaw ask "Write 10 LinkedIn posts about our new feature launch"
openclaw ask "Create Twitter thread announcing our Series A"
```

### SEO

**seo-audit** — Technical and on-page SEO diagnosis

```bash
openclaw ask "Run full SEO audit on example.com and prioritize fixes"
openclaw ask "Audit /blog for SEO issues"
```

**ai-seo** — Optimizes for AI search engines

```bash
openclaw ask "Optimize our product pages for ChatGPT and Perplexity"
```

**programmatic-seo** — Builds SEO pages at scale

```bash
openclaw ask "Design programmatic SEO system for 500 city landing pages"
```

**site-architecture** — Page hierarchy and URL structure

```bash
openclaw ask "Design site architecture for our new help center"
```

**schema-markup** — JSON-LD structured data

```bash
openclaw ask "Generate schema markup for our SaaS product page"
```

**content-strategy** — Content calendar and keyword mapping

```bash
openclaw ask "Create 3-month content strategy targeting SaaS founders"
```

### Paid Ads & Analytics

**paid-ads** — Campaign strategy across platforms

```bash
openclaw ask "Plan Google Ads campaign for enterprise product launch"
openclaw ask "Design Meta ads strategy for B2C mobile app"
```

**ad-creative** — Generates ad copy at scale

```bash
openclaw ask "Write 20 Google Search ad headlines for project management software"
openclaw ask "Create Meta ad variations testing 3 value props"
```

**ab-test-setup** — Designs proper A/B experiments

```bash
openclaw ask "Set up A/B test for new pricing page layout"
```

**analytics-tracking** — GA4, GTM, and event tracking

```bash
openclaw ask "Design GA4 event tracking for trial signup funnel"
```

**google-ads-connect** — Live Google Ads data analysis

```bash
openclaw ask "Audit my Google Ads account and find wasted spend"
openclaw ask "Which keywords have low quality scores and should be paused?"
```

Returns real data: campaign health scores, wasted spend analysis, keyword performance, quality score issues.

**search-console-connect** — Live Search Console data

```bash
openclaw ask "Why did organic traffic drop last week?"
openclaw ask "Find quick-win keywords I'm ranking 5-10 for"
openclaw ask "Detect keyword cannibalization in my content"
```

Returns: traffic trend analysis, ranking changes, affected pages, algorithm update correlation.

**meta-ads-connect** — Live Meta Ads data

```bash
openclaw ask "Our Meta ads are getting more expensive. What's happening?"
openclaw ask "Which ad creatives are fatigued and need refresh?"
openclaw ask "Find audience overlap issues in our ad sets"
```

Returns: creative fatigue detection, Learning Phase triage, ROAS by ad set, audience overlap.

**x-twitter-connect** — Live X/Twitter signals via TweetClaw

```bash
openclaw ask "Find X/Twitter conversations about our product category this week"
openclaw ask "Search for people asking about alternatives to [competitor]"
openclaw ask "Monitor keyword [product category] and draft responses"
```

Returns: tweet search results, reply threads, follower lists, conversation groupings, drafted responses for review.

### Growth & Retention

**referral-program** — Designs referral and affiliate programs

```bash
openclaw ask "Design referral program for B2B SaaS with $99/mo pricing"
```

**free-tool-strategy** — Builds free tools as lead magnets

```bash
openclaw ask "Design free tool strategy for email marketing SaaS"
```

**churn-prevention** — Cancel flows and win-back campaigns

```bash
openclaw ask "Create cancel flow with retention offers"
openclaw ask "Write 3-email win-back sequence for churned users"
```

### Sales & GTM

**revops** — Lead lifecycle and pipeline operations

```bash
openclaw ask "Design lead scoring model for B2B SaaS"
openclaw ask "Map lead lifecycle stages and routing rules"
```

**sales-enablement** — Pitch decks, one-pagers, objection handling

```bash
openclaw ask "Create sales one-pager for enterprise prospects"
openclaw ask "Write objection handling guide for common pricing concerns"
```

**launch-strategy** — Product launch playbooks

```bash
openclaw ask "Plan go-to-market strategy for new AI feature"
openclaw ask "Create launch week timeline and channel plan"
```

**pricing-strategy** — Pricing and packaging strategy

```bash
openclaw ask "Audit our pricing strategy and recommend changes"
openclaw ask "Design value-based pricing tiers for new product"
```

**competitor-alternatives** — Comparison and alternative pages

```bash
openclaw ask "Write '[Competitor] Alternative' comparison page"
openclaw ask "Create comparison table for us vs top 3 competitors"
```

### Strategy

**marketing-ideas** — 139 proven SaaS marketing tactics

```bash
openclaw ask "Give me 10 marketing ideas for early-stage B2B SaaS"
openclaw ask "What growth tactics work for freemium products?"
```

**marketing-psychology** — Behavioral economics and persuasion

```bash
openclaw ask "Apply Cialdini's principles to our pricing page"
openclaw ask "Use JTBD framework to rewrite our value prop"
```

**lead-magnets** — Lead magnet creation and optimization

```bash
openclaw ask "Design lead magnet for SaaS founders in pre-launch stage"
```

## Configuration

### Environment variables

Store all credentials in `.env` or agent config:

```bash
# Google Ads
GOOGLE_ADS_CLIENT_ID=your-client-id
GOOGLE_ADS_CLIENT_SECRET=your-secret
GOOGLE_ADS_DEVELOPER_TOKEN=your-dev-token
GOOGLE_ADS_CUSTOMER_ID=123-456-7890
GOOGLE_ADS_REFRESH_TOKEN=your-refresh-token

# Google Search Console
GOOGLE_SEARCH_CONSOLE_SITE_URL=https://example.com
GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json

# Meta Ads
META_ACCESS_TOKEN=your-long-lived-token
META_AD_ACCOUNT_ID=act_123456789

# X/Twitter (via TweetClaw)
TWITTER_API_KEY=your-api-key
TWITTER_API_SECRET=your-api-secret
TWITTER_ACCESS_TOKEN=your-access-token
TWITTER_ACCESS_SECRET=your-access-secret
```

### Product context file location

```
.agents/
├── product-marketing-context.md    ← Central product context
├── skills/
│   ├── copywriting/
│   ├── page-cro/
│   ├── google-ads-connect/
│   └── ...
```

## Common Patterns

### Pattern: Full CRO audit with data

```bash
# 1. Set up context
openclaw ask "Set up my product marketing context"

# 2. Connect data sources
openclaw ask "Connect Google Ads account"
openclaw ask "Connect Google Search Console"

# 3. Run audits
openclaw ask "Audit homepage for conversion issues"
openclaw ask "Analyze Google Ads account for wasted spend"
openclaw ask "Find SEO quick wins in Search Console"

# 4. Implement fixes
openclaw ask "Write improved homepage copy based on CRO audit"
openclaw ask "Create A/B test plan for new homepage"
```

### Pattern: Product launch campaign

```bash
# 1. Strategy
openclaw ask "Plan go-to-market strategy for new AI feature launching in 2 weeks"

# 2. Content creation
openclaw ask "Write launch email sequence for existing users"
openclaw ask "Create 10 LinkedIn posts announcing the launch"
openclaw ask "Write comparison page positioning new feature vs competitors"

# 3. Paid promotion
openclaw ask "Design Google Ads campaign for launch week"
openclaw ask "Create Meta ad creative variations for 3 audience segments"

# 4. Social monitoring
openclaw ask "Monitor X/Twitter for reactions to our launch announcement"
openclaw ask "Find conversations about [feature category] to join"
```

### Pattern: SEO optimization workflow

```bash
# 1. Connect data
openclaw ask "Connect Google Search Console"

# 2. Audit
openclaw ask "Run full SEO audit and prioritize fixes by impact"
openclaw ask "Analyze traffic drop from last month"

# 3. Content optimization
openclaw ask "Find keywords ranking 5-10 we should optimize for"
openclaw ask "Optimize for AI search: ChatGPT, Perplexity, Google AI Overviews"

# 4. Scale
openclaw ask "Design programmatic SEO for 200 location pages"
openclaw ask "Generate schema markup for all product pages"
```

### Pattern: Paid ads optimization

```bash
# 1. Connect accounts
openclaw ask "Connect Google Ads account"
openclaw ask "Connect Meta Ads account"

# 2. Audit current performance
openclaw ask "Audit Google Ads and find wasted spend"
openclaw ask "Which Meta ad creatives are fatigued?"

# 3. Fix issues
openclaw ask "Pause keywords with QS ≤ 3 and zero conversions"
openclaw ask "Generate 5 new Meta ad creative variations to replace fatigued ads"

# 4. Scale winners
openclaw ask "Write 30 Google Search ad headlines testing 3 value props"
openclaw ask "Design A/B test for best-performing ad angle"
```

## Troubleshooting

### Skills not loading

```bash
# Verify skill directory structure
ls ~/.agents/skills/

# Should show skill folders like:
# copywriting/  page-cro/  google-ads-connect/  etc.

# If empty, reinstall:
clawhub install LeoYeAI/openclaw-marketing-skills
```

### Data connector authentication errors

```bash
# Google Ads "invalid credentials"
# 1. Verify env vars are set:
echo $GOOGLE_ADS_CLIENT_ID

# 2. Refresh OAuth token:
# Follow Google Ads API OAuth2 flow to get new refresh token

# 3. Test connection:
openclaw ask "Test Google Ads connection"
```

```bash
# Search Console "property not verified"
# 1. Verify property in Google Search Console UI
# 2. Ensure GOOGLE_SEARCH_CONSOLE_SITE_URL matches exactly:
export GOOGLE_SEARCH_CONSOLE_SITE_URL="https://example.com"  # include protocol

# 3. Check service account has access to property
```

```bash
# Meta Ads "token expired"
# 1. Generate new long-lived token (60 days):
# https://developers.facebook.com/tools/explorer/

# 2. Update env var:
export META_ACCESS_TOKEN="new-long-lived-token"
```

```bash
# X/Twitter rate limits
# TweetClaw enforces rate limits automatically
# If "rate limit exceeded":
# 1. Wait 15 minutes (search endpoints)
# 2. Use bearer token for higher limits
# 3. Reduce query volume
```

### Product context not loading

```bash
# Verify context file exists:
cat .agents/product-marketing-context.md

# If missing, recreate:
openclaw ask "Set up my product marketing context"

# If file exists but skills ask questions anyway:
# 1. Check file is in correct location (.agents/ at project root)
# 2. Verify file has valid markdown content
# 3. Try explicit file path in agent config
```

### Skill choosing wrong approach

```bash
# Be specific in your prompt:
# Instead of: "improve our ads"
# Use: "Audit Google Ads account and find wasted spend using google-ads-connect"

# Force specific skill:
openclaw ask --skill page-cro "Audit /pricing page"
```

### Output quality issues

```bash
# 1. Ensure product-marketing-context.md is complete and up-to-date
openclaw ask "Review and update product marketing context"

# 2. Provide more context in your request:
# Instead of: "write copy"
# Use: "Write homepage hero section copy for B2B SaaS targeting CTOs, emphasizing security and compliance"

# 3. Iterate:
openclaw ask "Rewrite the headline to be more specific and outcome-focused"
```

## Advanced Usage

### Combining multiple skills

```bash
# SEO + paid ads alignment
openclaw ask "Find top 10 SEO keywords, then write Google Ads headlines targeting them"

# CRO + copywriting
openclaw ask "Audit /pricing page for conversion issues, then rewrite copy addressing top 3 issues"

# Data connector + content creation
openclaw ask "Search X/Twitter for objections to our product category, then write objection-handling FAQ page"
```

### Batch operations

```bash
# Audit multiple pages
openclaw ask "Run CRO audit on /pricing, /features, /enterprise and prioritize all fixes by impact"

# Generate content at scale
openclaw ask "Write 50 Google Search ad headlines, 5 variations each for 10 keyword themes"

# Multi-channel launch
openclaw ask "Create launch assets: email sequence, 10 social posts, 5 ad creatives, comparison page, and launch week timeline"
```

### Custom skill combinations

Create aliases for frequent workflows:

```bash
# In .bashrc or .zshrc:
alias claw-audit='openclaw ask "Connect Google Ads, Search Console, and Meta Ads, then run full marketing audit with prioritized action plan"'

alias claw-launch='openclaw ask "Plan product launch: strategy, email sequence, social content, paid ads, and X/Twitter monitoring"'

alias claw-seo='openclaw ask "Connect Search Console, audit SEO, find quick wins, and create optimization plan"'
```

## Resources

- **GitHub**: https://github.com/LeoYeAI/openclaw-marketing-skills
- **ClawHub**: https://clawhub.ai/LeoYeAI/openclaw-marketing-skills
- **MyClaw.ai** (hosted OpenClaw): https://myclaw.ai
- **TweetClaw** (X/Twitter plugin): https://github.com/Xquik-dev/tweetclaw

## License

MIT — see repository for full license text.
