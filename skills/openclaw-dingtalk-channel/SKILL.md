---
name: openclaw-dingtalk-channel
description: DingTalk channel plugin for OpenClaw that enables enterprise bot integration using Stream mode without public IP requirements
triggers:
  - how do I set up the DingTalk channel for OpenClaw
  - configure OpenClaw with DingTalk enterprise bot
  - install the dingtalk plugin for openclaw
  - set up AI card streaming in DingTalk
  - configure multi-agent routing in DingTalk channel
  - troubleshoot OpenClaw DingTalk connection
  - implement btw bypass mode in DingTalk
  - handle DingTalk message types in OpenClaw
---

# OpenClaw DingTalk Channel Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill provides expertise in configuring and using the **@soimy/dingtalk** channel plugin for OpenClaw, which enables DingTalk enterprise bot integration with Stream mode (no public IP required).

## What It Does

The DingTalk channel plugin connects OpenClaw to DingTalk enterprise bots, supporting:

- **Stream mode**: No webhook or public IP needed
- **Message types**: Text, images, voice, video, files, DingTalk docs/cards
- **Reply modes**: Markdown and AI Card (v2 structured blocks with streaming)
- **Multi-agent routing**: Bind multiple agents/bots, use `@agent-name` routing
- **Bypass mode**: `/btw` for quick answers outside main session lock
- **Real-time abort**: Stop AI generation mid-stream
- **Device Flow auth**: Scan QR code to auto-register credentials

## Installation

### Prerequisites

Requires OpenClaw >= 2026.3.24.

### Install from ClawHub

```bash
openclaw plugins install @soimy/dingtalk
```

### Install from Local Source

```bash
git clone https://github.com/soimy/openclaw-channel-dingtalk.git
cd openclaw-channel-dingtalk
npm install
openclaw plugins install -l .
```

### Enable Plugin

Add to OpenClaw configuration:

```json5
{
  "plugins": {
    "enabled": true,
    "allow": ["dingtalk"]
  }
}
```

## Configuration

### Interactive Setup (Recommended)

```bash
openclaw onboard
```

Or configure channels section:

```bash
openclaw configure --section channels
```

### Minimal Manual Configuration

```json5
{
  "channels": {
    "dingtalk": {
      "enabled": true,
      "clientId": "dingxxxxxx",
      "clientSecret": "your-app-secret",
      "dmPolicy": "open",
      "groupPolicy": "open",
      "messageType": "markdown"
    }
  }
}
```

### Full Configuration Example

```json5
{
  "channels": {
    "dingtalk": {
      "enabled": true,
      "clientId": process.env.DINGTALK_CLIENT_ID,
      "clientSecret": process.env.DINGTALK_CLIENT_SECRET,
      
      // Access policies
      "dmPolicy": "open",        // "open" | "whitelist" | "blacklist" | "closed"
      "groupPolicy": "open",     // Same options
      "dmWhitelist": [],         // User IDs if dmPolicy is "whitelist"
      "groupWhitelist": [],      // Conversation IDs if groupPolicy is "whitelist"
      
      // Message type: "markdown" or "aiCard"
      "messageType": "aiCard",
      
      // AI Card streaming options
      "aiCardStreamingMode": true,
      "aiCardStreamingInterval": 800,  // ms between chunks
      
      // Multi-agent bindings
      "agentBindings": [
        {
          "agentName": "default",
          "robotCode": "dingxxxxxx",
          "isDefault": true
        },
        {
          "agentName": "analyst",
          "robotCode": "dingyyyyyyy",
          "isDefault": false
        }
      ],
      
      // Routing
      "enableAtAgentRouting": true,
      "atAgentRoutingPrefix": "@",
      
      // Features
      "enableBtwBypass": true,
      "enableRealTimeAbort": true,
      "abortKeywords": ["停止", "stop", "/stop", "esc"],
      
      // Security
      "maxMessageLength": 10000,
      "rateLimitPerUser": 20,
      "rateLimitWindow": 60000  // 1 minute
    }
  }
}
```

### Environment Variables

```bash
export DINGTALK_CLIENT_ID="dingxxxxxx"
export DINGTALK_CLIENT_SECRET="your-app-secret"
```

## DingTalk App Setup

### Required Permissions

In DingTalk Developer Console, configure:

1. **Application Credentials**: Client ID and Client Secret
2. **Stream Mode**: Enable and configure
3. **API Permissions**:
   - `qyapi_robot_sendmsg` - Send messages
   - `qyapi_chat_manage` - Group chat management
   - `contact.User.Read` - Read user info
   - `im.Chat.Read` - Read conversation info
4. **Event Subscriptions**:
   - `chat_update_title` - Group name changes
   - `chat_update_owner` - Owner changes
   - `im_robot_at_message` - @ mentions
   - `im_robot_message` - Direct messages

## Key Features & Usage

### Reply Modes

#### Markdown Mode

Simple text responses with Markdown formatting:

```json5
{
  "messageType": "markdown"
}
```

User sees plain Markdown-formatted text.

#### AI Card Mode

Rich interactive cards with streaming support:

```json5
{
  "messageType": "aiCard",
  "aiCardStreamingMode": true,
  "aiCardStreamingInterval": 800
}
```

Features:
- Structured block rendering
- Inline images
- Task metadata display
- Real-time streaming updates

### /btw Bypass Mode

Quick answers outside the main session lock:

**User**: `/btw What's the weather?`

The message bypasses conversation locking and gets immediate response in a separate session. Prefix is configurable:

```json5
{
  "enableBtwBypass": true,
  "btwPrefix": "/btw"  // Optional, defaults to "/btw"
}
```

### Multi-Agent & Bot Bindings

Bind multiple OpenClaw agents to different DingTalk bots:

```json5
{
  "agentBindings": [
    {
      "agentName": "default",
      "robotCode": "dingbot001",
      "isDefault": true
    },
    {
      "agentName": "analyst",
      "robotCode": "dingbot002",
      "isDefault": false
    },
    {
      "agentName": "writer",
      "robotCode": "dingbot003",
      "isDefault": false
    }
  ]
}
```

### @Agent Routing

Route to specific agents by mentioning them:

**User**: `@analyst analyze Q4 revenue`

```json5
{
  "enableAtAgentRouting": true,
  "atAgentRoutingPrefix": "@"
}
```

The message routes to the "analyst" agent if bound.

### Real-Time Abort

Stop AI generation mid-stream:

**User**: `stop` or `停止` or `esc`

```json5
{
  "enableRealTimeAbort": true,
  "abortKeywords": ["停止", "stop", "/stop", "esc"]
}
```

### Message Type Handling

Plugin automatically handles:

- **Text**: Direct processing
- **Images**: Extracted and passed to vision-capable models
- **Voice**: Transcribed if supported
- **Files**: Content extracted when possible
- **DingTalk Docs**: Content fetched via API
- **Reply/Quote**: Original message context included

## Code Examples

### TypeScript Plugin Development

```typescript
import type { ChannelPlugin, InboundMessage, OutboundMessage } from '@openclaw/types';

export class DingTalkChannel implements ChannelPlugin {
  name = 'dingtalk';
  
  async processInbound(rawMessage: any): Promise<InboundMessage> {
    const { msgtype, text, senderStaffId, conversationId } = rawMessage;
    
    return {
      channelId: 'dingtalk',
      userId: senderStaffId,
      conversationId,
      content: {
        type: 'text',
        text: text?.content || ''
      },
      metadata: {
        msgId: rawMessage.msgId,
        timestamp: rawMessage.createAt
      }
    };
  }
  
  async processOutbound(message: OutboundMessage): Promise<void> {
    const { conversationId, content, metadata } = message;
    
    if (this.config.messageType === 'aiCard') {
      await this.sendAICard(conversationId, content, metadata);
    } else {
      await this.sendMarkdown(conversationId, content);
    }
  }
  
  private async sendAICard(
    conversationId: string,
    content: any,
    metadata?: any
  ): Promise<void> {
    const cardData = {
      cardTemplateId: 'standard',
      outTrackId: metadata?.trackId,
      cardData: {
        cardParamMap: {
          content: content.text,
          blocks: this.formatBlocks(content)
        }
      }
    };
    
    await this.client.sendInteractiveCard({
      conversationId,
      ...cardData
    });
  }
}
```

### Handling Streaming Updates

```typescript
async streamAIResponse(
  conversationId: string,
  generator: AsyncGenerator<string>
): Promise<void> {
  let buffer = '';
  let lastUpdate = 0;
  const interval = this.config.aiCardStreamingInterval || 800;
  
  for await (const chunk of generator) {
    buffer += chunk;
    const now = Date.now();
    
    if (now - lastUpdate >= interval) {
      await this.updateAICard(conversationId, buffer);
      lastUpdate = now;
    }
  }
  
  // Final update
  await this.updateAICard(conversationId, buffer, { finished: true });
}
```

### Custom Agent Routing Logic

```typescript
function resolveAgent(message: InboundMessage): string {
  const { content, metadata } = message;
  
  // Check for @agent mention
  const atMatch = content.text.match(/@(\w+)\s/);
  if (atMatch) {
    const agentName = atMatch[1];
    const binding = this.config.agentBindings.find(
      b => b.agentName === agentName
    );
    if (binding) return agentName;
  }
  
  // Check robot code mapping
  const robotCode = metadata.robotCode;
  const binding = this.config.agentBindings.find(
    b => b.robotCode === robotCode
  );
  
  return binding?.agentName || 'default';
}
```

### Implementing Bypass Mode

```typescript
function isBtwMessage(text: string): boolean {
  const prefix = this.config.btwPrefix || '/btw';
  return text.trim().startsWith(prefix);
}

async processBtwMessage(message: InboundMessage): Promise<void> {
  const prefix = this.config.btwPrefix || '/btw';
  const cleanText = message.content.text.replace(prefix, '').trim();
  
  // Create isolated session
  const btwSession = await this.createBypassSession(message.userId);
  
  // Process independently
  const response = await this.agent.chat(cleanText, {
    sessionId: btwSession.id,
    bypass: true
  });
  
  await this.send(message.conversationId, response);
}
```

## Common Patterns

### Policy-Based Access Control

```typescript
function checkAccess(
  policy: 'open' | 'whitelist' | 'blacklist' | 'closed',
  identifier: string,
  whitelist: string[],
  blacklist: string[]
): boolean {
  switch (policy) {
    case 'closed':
      return false;
    case 'whitelist':
      return whitelist.includes(identifier);
    case 'blacklist':
      return !blacklist.includes(identifier);
    case 'open':
    default:
      return true;
  }
}

// Usage
const canDM = checkAccess(
  config.dmPolicy,
  message.userId,
  config.dmWhitelist,
  config.dmBlacklist
);

const canGroup = checkAccess(
  config.groupPolicy,
  message.conversationId,
  config.groupWhitelist,
  config.groupBlacklist
);
```

### Rate Limiting

```typescript
class RateLimiter {
  private counts = new Map<string, number[]>();
  
  check(userId: string, limit: number, window: number): boolean {
    const now = Date.now();
    const timestamps = this.counts.get(userId) || [];
    
    // Remove old timestamps
    const recent = timestamps.filter(t => now - t < window);
    
    if (recent.length >= limit) {
      return false;
    }
    
    recent.push(now);
    this.counts.set(userId, recent);
    return true;
  }
}

// Usage
if (!this.rateLimiter.check(
  message.userId,
  config.rateLimitPerUser,
  config.rateLimitWindow
)) {
  throw new Error('Rate limit exceeded');
}
```

### Error Recovery

```typescript
async sendWithRetry(
  conversationId: string,
  content: any,
  maxRetries = 3
): Promise<void> {
  for (let i = 0; i < maxRetries; i++) {
    try {
      await this.send(conversationId, content);
      return;
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      
      // Exponential backoff
      await new Promise(r => setTimeout(r, 1000 * Math.pow(2, i)));
    }
  }
}
```

## Troubleshooting

### Plugin Not Loading

**Check plugin allowlist**:

```bash
openclaw config get plugins.allow
```

Should include `"dingtalk"`.

**Verify installation**:

```bash
openclaw plugins list
```

### No Messages Received

**Check Stream connection**:

```bash
openclaw gateway status
```

**Verify credentials**:

```bash
openclaw config get channels.dingtalk.clientId
openclaw config get channels.dingtalk.clientSecret
```

**Test DingTalk app permissions** in Developer Console → Application Details → Permissions.

### AI Card Not Streaming

**Enable streaming mode**:

```json5
{
  "messageType": "aiCard",
  "aiCardStreamingMode": true
}
```

**Check interval** (minimum 500ms recommended):

```json5
{
  "aiCardStreamingInterval": 800
}
```

### Rate Limit Errors

**Increase limits** (if quota allows):

```json5
{
  "rateLimitPerUser": 30,
  "rateLimitWindow": 60000
}
```

**Check DingTalk quota** in Developer Console → Resource Management.

### Agent Routing Fails

**Verify bindings**:

```bash
openclaw config get channels.dingtalk.agentBindings
```

**Check agent names match**:

```bash
openclaw agents list
```

**Enable routing**:

```json5
{
  "enableAtAgentRouting": true
}
```

### Device Flow Registration

If auto-registration fails:

1. Check network connectivity
2. Verify `clientId` and `clientSecret`
3. Ensure DingTalk app has Device Flow enabled
4. Check logs: `openclaw logs --filter dingtalk`

## Updates

### Update from ClawHub

```bash
openclaw plugins update dingtalk
```

### Update from Local Source

```bash
cd openclaw-channel-dingtalk
git pull
openclaw gateway restart
```

## Resources

- **Documentation**: https://dingtalk-channel.nanoo.app/
- **GitHub**: https://github.com/soimy/openclaw-channel-dingtalk
- **NPM**: https://www.npmjs.com/package/@soimy/dingtalk
- **License**: MIT
- **Citation**: See CITATION.cff for attribution requirements

## Version Compatibility

- Minimum OpenClaw version: `2026.3.24`
- Recommended: Latest stable release
- DingTalk API quota: Free unlimited until 2026-03-31 (or 2026-04-30 if approved)
