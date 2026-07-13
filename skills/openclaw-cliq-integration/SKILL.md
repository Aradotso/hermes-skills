---
name: openclaw-cliq-integration
description: SprintCX OpenClaw Channel Plugin for Zoho Cliq — connect OpenClaw agents to Zoho Cliq with bot-per-agent, markdown conversion, streaming, and group chat support
triggers:
  - "integrate openclaw with zoho cliq"
  - "set up cliq channel for openclaw agent"
  - "configure zoho cliq bot for openclaw"
  - "connect my openclaw gateway to cliq"
  - "enable cliq messaging in openclaw"
  - "troubleshoot openclaw cliq webhook"
  - "implement zoho cliq channel plugin"
  - "configure cliq oauth for openclaw"
---

# OpenClaw Cliq Integration

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This skill covers the **openclaw-cliq** channel plugin that connects OpenClaw agents to **Zoho Cliq** for DMs, channel @mentions, streaming previews, cards, buttons, and message actions. The plugin implements OAuth 2.0, multi-data-center support, and handles both direct messages (via `client_credentials`) and channel posts/edits (via user-context refresh token).

## What It Does

- **Messaging**: Handles DMs and channel @mentions via Deluge webhook, supports image/file/voice attachments
- **Rich Replies**: Markdown → Cliq formatting, live-edit streaming, interactive cards/buttons
- **Message Actions**: Edit, delete, react to sent messages from the agent
- **Security**: DM allowlist/pairing policies, per-channel tool policies, hardened webhook with rate limiting
- **OAuth**: Dual-grant OAuth 2.0 (client_credentials for DMs, refresh token for channels)
- **Multi-Region**: Works across all Zoho data centers (US, EU, IN, AU, JP, CA, SA, CN)

## Installation

```bash
# Install the plugin
openclaw plugins install clawhub:@sprintcx/openclaw-cliq

# Run interactive setup wizard
openclaw setup  # Pick "Zoho Cliq" — writes account config

# Check status
openclaw status
openclaw channels  # List configured channels
```

## Zoho Cliq Setup

### 1. Create Bot in Cliq

1. Profile picture → **My Cliq** → **Bots & Tools** → **Bots** → **Create Bot**
2. Set **Bot Name** (display, e.g., `OpenClaw Agent`) and **Bot Unique Name** (e.g., `openclaw_agent` — this is your `botId`)
3. Choose **Custom Bot** type
4. Enable **Mention Handler** and **Message Handler**
5. Publish/activate the bot
6. Invite bot to channels where it should respond

### 2. Get OAuth Credentials

1. Open [Zoho API Console](https://api-console.zoho.com) (use your data center's domain)
2. Create **Self Client** or **Server-based Application**
3. Note **Client ID** and **Client Secret**
4. Consent these scopes:
   - `ZohoCliq.Webhooks.CREATE` (DMs)
   - `ZohoCliq.Channels.UPDATE` (channel posts)
   - `ZohoCliq.Channels.CREATE` (v3 cards)
   - `ZohoCliq.Channels.READ` (metadata)
   - `ZohoCliq.Users.READ` (user info)
   - `ZohoCliq.Messages.UPDATE` (edit/stream)
   - `ZohoCliq.Messages.READ` (attachments)
   - `ZohoCliq.Messages.DELETE` (delete)
   - `ZohoCliq.messageactions.CREATE` (reactions)

### 3. Generate Webhook Secret

```bash
openssl rand -hex 32
```

Save this as your `webhookSecret`.

### 4. Obtain Refresh Token (Required for Channels)

Generate authorization URL (replace placeholders):

```typescript
const authUrl = `https://accounts.zoho.eu/oauth/v2/auth?scope=ZohoCliq.Channels.UPDATE,ZohoCliq.Messages.UPDATE,ZohoCliq.Messages.READ,ZohoCliq.Messages.DELETE,ZohoCliq.Channels.CREATE,ZohoCliq.messageactions.CREATE&client_id=${CLIENT_ID}&response_type=code&access_type=offline&redirect_uri=http://localhost:8080/callback&prompt=consent`;
```

Visit URL, consent, capture the `code` from redirect, then exchange:

```bash
curl -X POST https://accounts.zoho.eu/oauth/v2/token \
  -d "grant_type=authorization_code" \
  -d "client_id=${CLIENT_ID}" \
  -d "client_secret=${CLIENT_SECRET}" \
  -d "code=${CODE}" \
  -d "redirect_uri=http://localhost:8080/callback"
```

Extract `refresh_token` from response.

### 5. Deluge Webhook Handler

Paste into bot's **Message Handler** and **Mention Handler**:

```javascript
// Message Handler / Mention Handler
response = Map();
webhookUrl = "https://your-gateway.example.com/cliq/webhook";
webhookSecret = "your-webhook-secret-here";

headers = {"x-cliq-webhook-secret": webhookSecret, "Content-Type": "application/json"};

try {
    webhookResp = invokeurl [
        url: webhookUrl
        type: POST
        parameters: arguments
        headers: headers
    ];
    info "Webhook forwarded: " + webhookResp;
    response = {"text": ""};  // Silent ack
} catch (e) {
    info "Webhook error: " + e;
    response = {"text": "⚠️ Processing error"};
}

return response;
```

Replace `webhookUrl` with `https://<your-gateway>/cliq/webhook` and `webhookSecret` with your generated secret.

## Configuration

Create `~/.openclaw/accounts/cliq-main.yaml` (or use `openclaw setup`):

```yaml
channel: cliq
enabled: true

# Bot identity
botId: openclaw_agent          # Bot Unique Name from Cliq
botName: OpenClaw Agent         # Display name

# OAuth credentials (use SecretRef or env vars)
clientId: ${ZOHO_CLIENT_ID}
clientSecret: ${ZOHO_CLIENT_SECRET}
refreshToken: ${ZOHO_REFRESH_TOKEN}  # From §4, required for channels

# Webhook
webhookSecret: ${CLIQ_WEBHOOK_SECRET}

# Data center (EU default, adjust for your region)
oauthBase: https://accounts.zoho.eu
apiBase: https://cliq.zoho.eu

# DM security policy
dmPolicy: pairing  # or: allowlist, open, disabled
# dmAllowlist:
#   - user@example.com

# Channel settings
channels:
  - uniqueName: engineering        # Cliq channel unique name
    requireMention: true            # Only respond to @mentions
    admissionPolicy: open           # or: allowlist, admin-only
    # userAllowlist:
    #   - alice@example.com

# API version preferences (opt-in to v3 features)
apiVersion:
  channelCard: v3     # Use v3 cards for channels (modern-inline, prompt, poll themes)
  # dmCard: v3        # v3 DM cards (default already v3 via dmPost)
  # delete: v3        # Bulk delete endpoint
  # react: v3         # v3 reaction endpoint

# Operational
maxMessageLength: 8000
streamingMinInterval: 1000      # ms between edit updates
outboundRetry:
  maxAttempts: 3
  backoffMs: 1000
```

### Data Center Mapping

Set `oauthBase` and `apiBase` for your region:

| Region | OAuth Base | API Base |
|--------|-----------|----------|
| **US** | `https://accounts.zoho.com` | `https://cliq.zoho.com` |
| **EU** | `https://accounts.zoho.eu` | `https://cliq.zoho.eu` |
| **IN** | `https://accounts.zoho.in` | `https://cliq.zoho.in` |
| **AU** | `https://accounts.zoho.com.au` | `https://cliq.zoho.com.au` |
| **JP** | `https://accounts.zoho.jp` | `https://cliq.zoho.jp` |
| **CA** | `https://accounts.zohocloud.ca` | `https://cliq.zohocloud.ca` |
| **SA** | `https://accounts.zoho.sa` | `https://cliq.zoho.sa` |
| **CN** | `https://accounts.zoho.com.cn` | `https://cliq.zoho.com.cn` |

## Key Commands

```bash
# List configured channel accounts
openclaw channels

# Check plugin health
openclaw status

# Directory lookup (resolve user/channel)
openclaw directory --channel cliq-main --query alice@example.com

# Plugin diagnostics
openclaw doctor cliq

# Interactive setup
openclaw setup  # Choose Zoho Cliq

# Test webhook (local dev)
curl -X POST http://localhost:3000/cliq/webhook \
  -H "x-cliq-webhook-secret: ${CLIQ_WEBHOOK_SECRET}" \
  -H "Content-Type: application/json" \
  -d '{
    "bot": {"unique_name": "openclaw_agent"},
    "message": {"text": "hello"},
    "user": {"id": "123", "email": "user@example.com"},
    "type": "message"
  }'
```

## Code Examples

### Send DM (Client Credentials Flow)

```typescript
// Plugin handles token fetch automatically
// POST /api/v2/bots/{botId}/message
const dmPayload = {
  userids: ["user_id_123"],
  text: "Hello from OpenClaw!"
};
// Scope: ZohoCliq.Webhooks.CREATE (client_credentials grant)
```

### Send Channel Message (Refresh Token Flow)

```typescript
// POST /api/v2/channelsbyname/{uniqueName}/message
const channelPayload = {
  text: "*Bold* and _italic_ markdown",
  bot: {
    name: "OpenClaw Agent",
    image: "https://example.com/avatar.png"
  }
};
// Scope: ZohoCliq.Channels.UPDATE (refresh token grant)
```

### Live-Edit Streaming

```typescript
// Initial message
const msgId = await sendMessage(channelId, "Thinking...");

// Streaming updates (edits in place)
for await (const chunk of agentStream) {
  await editMessage(chatId, msgId, chunk.text);
  await sleep(1000);  // Respect streamingMinInterval
}
// Scope: ZohoCliq.Messages.UPDATE
```

### Send Interactive Card (v3)

```typescript
// Requires apiVersion.channelCard: v3 and ZohoCliq.Channels.CREATE
const card = {
  bot: { name: "OpenClaw Agent" },
  card: {
    theme: "modern-inline",
    thumbnail: "https://example.com/preview.png",
    title: "Choose Action",
    buttons: [
      {
        label: "Approve",
        type: "+",
        action: {
          type: "invoke.function",
          data: { action: "approve" }
        }
      }
    ],
    slides: [
      {
        type: "text",
        data: "Review the following details:"
      },
      {
        type: "table",
        data: {
          headers: ["Field", "Value"],
          rows: [["Status", "Pending"]]
        }
      }
    ]
  }
};
// POST /api/v2/channelsbyname/{uniqueName}/message
```

### Delete Message (v3 Bulk Delete)

```typescript
// Requires apiVersion.delete: v3 and ZohoCliq.Messages.DELETE
const deletePayload = {
  message_ids: [messageId]
};
// POST /api/v2/chats/{chatId}/messages/delete
```

### Add Reaction

```typescript
// Requires ZohoCliq.messageactions.CREATE
const reaction = {
  emoji: "👍"
};
// POST /api/v2/chats/{chatId}/messages/{messageId}/reactions
```

### Handle Cliq Form Submission

```typescript
// Inbound webhook payload when user submits a form
{
  "type": "form_submit",
  "form": {
    "name": "ParameterCapture",
    "values": {
      "project": "openclaw",
      "priority": "high"
    }
  },
  "user": { "email": "alice@example.com" }
}
// Plugin surfaces as FormValues/FormName to agent context
```

### DM Pairing Flow

```typescript
// User DMs bot for first time (dmPolicy: pairing)
// Plugin sends approval request card
const approvalCard = {
  text: "alice@example.com wants to chat. Approve?",
  card: {
    buttons: [
      { label: "Approve", action: { type: "invoke.function", data: { action: "approve_dm", userId: "123" } } },
      { label: "Deny", action: { type: "invoke.function", data: { action: "deny_dm" } } }
    ]
  }
};
// Admin clicks Approve → user added to allowlist, receives welcome
```

## Common Patterns

### Multi-Account Setup

```yaml
# ~/.openclaw/accounts/cliq-prod.yaml
channel: cliq
botId: prod_agent
# ... prod OAuth/webhook config

# ~/.openclaw/accounts/cliq-dev.yaml
channel: cliq
botId: dev_agent
# ... dev OAuth/webhook config
```

### Per-Channel Tool Policy

```yaml
channels:
  - uniqueName: public-support
    requireMention: true
    toolPolicy:
      allowedTools: ["search", "docs"]
      deniedTools: ["admin", "deploy"]
    userToolOverrides:
      admin@example.com:
        allowedTools: ["admin", "deploy"]
```

### Secure Webhook Validation

```typescript
// Plugin validates x-cliq-webhook-secret header with constant-time compare
// Failed auth triggers rate limiting (10 failures/IP → 1hr block)
// webhook.ts snippet:
if (!timingSafeEqual(Buffer.from(receivedSecret), Buffer.from(configSecret))) {
  logFailedAuth(req.ip);
  if (isRateLimited(req.ip)) {
    return res.status(429).send("Too many failed attempts");
  }
  return res.status(401).send("Invalid webhook secret");
}
```

### Attachment Download

```typescript
// Inbound image/file → plugin fetches via Messages.READ scope
// GET /api/v2/chats/{chatId}/messages → find file message → extract downloadable_url
// Downloads bytes, surfaces to agent as ContentPart with image/audio/file type
```

### Channel Admission Control

```yaml
channels:
  - uniqueName: restricted-channel
    admissionPolicy: allowlist
    userAllowlist:
      - alice@example.com
      - bob@example.com
    requireMention: true  # Bot only replies if @mentioned
```

## Troubleshooting

### "oauthtoken_scope_invalid" on Channel Post

**Symptom**: DMs work, channel posts fail with `{"code":"oauthtoken_scope_invalid"}`

**Cause**: Missing or expired refresh token. The `client_credentials` grant cannot obtain `ZohoCliq.Channels.UPDATE` scope.

**Fix**: Complete §3c to obtain a refresh token, set it in config:

```yaml
refreshToken: ${ZOHO_REFRESH_TOKEN}
```

### Webhook Not Receiving Events

**Checklist**:
1. Bot is **published/active** in Cliq
2. Bot is **invited** to the channel (for mentions)
3. Deluge handler is **deployed** to Message/Mention handlers
4. Webhook URL is **publicly accessible** (test with `curl` from external host)
5. `webhookSecret` matches between Deluge and plugin config
6. Check OpenClaw gateway logs: `openclaw logs --filter cliq`

### Live Streaming Not Working

**Symptom**: Messages appear as separate posts, not edits

**Requirements**:
- `refreshToken` configured (edit uses `Messages.UPDATE` scope)
- `streamingMinInterval` respected (default 1000ms, Cliq rate-limits edits)
- Message ID returned from initial send and cached

**Debug**:

```typescript
// Check if edit endpoint is reachable
// PUT /api/v2/chats/{chatId}/messages/{messageId}
// Response 200 → working, 403 → scope issue, 404 → bad message ID
```

### Wrong Data Center

**Symptom**: OAuth/API calls fail with 4xx/5xx, or token requests hang

**Fix**: Set correct `oauthBase` and `apiBase` in config (see [Data Center Mapping](#data-center-mapping))

```yaml
# Example for Australia
oauthBase: https://accounts.zoho.com.au
apiBase: https://cliq.zoho.com.au
```

### Rate Limiting

**Zoho Cliq API limits**: ~10 req/sec per endpoint

**Plugin mitigation**:
- Token caching (1h TTL, reused across requests)
- Edit throttling (`streamingMinInterval`)
- Exponential backoff on 429 (configurable via `outboundRetry`)

### Message Too Long

**Limit**: 8000 chars (configurable via `maxMessageLength`)

**Plugin behavior**: Auto-chunks long messages into multiple posts

```yaml
maxMessageLength: 8000  # Adjust if hitting truncation
```

### Bot Loop Protection

**Symptom**: Bot replies to itself infinitely

**Built-in protection**: Plugin filters out messages where `user.id === bot.id` or `user.email` matches bot owner.

**Manual**: Add to Deluge handler:

```javascript
if (arguments.get("user").get("id") == arguments.get("bot").get("id")) {
    return {"text": ""};  // Ignore self-messages
}
```

### Form Submissions Not Recognized

**Requirements**:
- Bot has **Form Handler** defined in Cliq
- Webhook forwards `type: "form_submit"` events
- `form.name` and `form.values` present in payload

**Debug**:

```bash
# Check webhook payload
openclaw logs --filter cliq --level debug
# Look for: "Inbound form submission: FormName=..."
```

### File Attachments Not Downloading

**Requirements**:
- `ZohoCliq.Messages.READ` scope consented
- Refresh token configured
- Chat ID available (DM chats return `chatId` in webhook; channel messages need lookup)

**Fallback**: Without `Messages.READ`, attachments surface as name-only (no bytes reach agent).

---

## References

- [Zoho Cliq Bot Developer Guide](https://www.zoho.com/cliq/help/platform/bot-guide.html)
- [Zoho API Console](https://api-console.zoho.com)
- [OpenClaw Plugin SDK Docs](https://github.com/openclaw/openclaw)
- Plugin source: [sprintberlin/openclaw-cliq](https://github.com/sprintberlin/openclaw-cliq)
