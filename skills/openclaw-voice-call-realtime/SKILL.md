---
name: openclaw-voice-call-realtime
description: Give your AI assistant a phone — OpenClaw plugin for real phone calls via Twilio + OpenAI Realtime API with in-call tools, transcripts, and call screening
triggers:
  - "set up voice calls for my AI assistant"
  - "configure OpenClaw to make phone calls"
  - "how do I let my agent call people with Twilio"
  - "enable real-time voice conversations with OpenAI Realtime"
  - "make my assistant book reservations by phone"
  - "set up AI phone call handling with transcripts"
  - "configure OpenClaw voice call plugin"
  - "integrate Twilio voice with OpenClaw agent"
---

# OpenClaw Voice Call — Realtime Edition

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

This OpenClaw plugin gives your AI assistant the ability to place and receive real phone calls using Twilio Programmable Voice and the OpenAI Realtime API. The assistant can navigate IVR menus, hold natural full-duplex conversations, report structured outcomes, generate transcripts, and hang up autonomously when done.

## What It Does

- **Real phone calls** — Your agent dials numbers, talks to humans/IVR systems, and handles inbound calls
- **Full-duplex conversation** — OpenAI Realtime API (speech-to-speech) with sub-second turnaround and natural interruptions
- **In-call tools** — Press DTMF keys, report outcomes, end calls gracefully
- **Transcripts & summaries** — Every call generates a Markdown transcript with AI summary and structured outcome
- **Call screening** — Identifies itself to Google Call Screen and voicemail systems
- **Goal-directed** — Pass talking points and the AI stays on task, confirms details, wraps up

## Installation

### 1. Clone the Plugin

```bash
mkdir -p ~/.openclaw/plugins-src
git clone https://github.com/TristanBrotherton/openclaw-voice-call-realtime.git \
  ~/.openclaw/plugins-src/voice-call-realtime
cd ~/.openclaw/plugins-src/voice-call-realtime
npm install --omit=dev
```

### 2. Set Up Twilio

1. Create account at [twilio.com](https://www.twilio.com/try-twilio) and upgrade billing for production use
2. Buy a voice-capable phone number (Console → Phone Numbers → Buy a number)
3. Get credentials from Console → Account Info:
   - Account SID (starts with `AC`)
   - Auth Token (click to reveal)
4. For international calls: Console → Voice → Settings → Geo permissions → enable countries
5. For inbound calls (optional): Configure phone number webhook to point to your public URL

### 3. Expose Webhook Publicly

Twilio needs HTTPS access to your webhook server (port 3336). Use Cloudflare Tunnel (recommended):

```bash
# Install cloudflared
brew install cloudflared

# Set up tunnel
cloudflared tunnel login
cloudflared tunnel create voice
cloudflared tunnel route dns voice voice.yourdomain.com
```

Create `~/.cloudflared/config.yml`:

```yaml
tunnel: voice
credentials-file: /Users/username/.cloudflared/<tunnel-id>.json
ingress:
  - hostname: voice.yourdomain.com
    service: http://localhost:3336
  - service: http_status:404
```

Run tunnel (and make it survive reboots with systemd/LaunchAgent):

```bash
cloudflared tunnel run voice
```

Alternatives: `ngrok http 3336` or `tailscale funnel 3336`

### 4. Configure OpenClaw

Edit `~/.openclaw/openclaw.json`:

```json
{
  "plugins": {
    "allow": ["voice-call-tristan"],
    "load": {
      "paths": ["/Users/username/.openclaw/plugins-src"]
    },
    "entries": {
      "voice-call-tristan": {
        "enabled": true,
        "config": {
          "enabled": true,
          "provider": "twilio",
          "fromNumber": "+15551234567",
          "toNumber": "+15557654321",
          "twilio": {
            "accountSid": "${TWILIO_ACCOUNT_SID}",
            "authToken": "${TWILIO_AUTH_TOKEN}"
          },
          "serve": {
            "port": 3336,
            "bind": "127.0.0.1",
            "path": "/voice/webhook"
          },
          "publicUrl": "https://voice.yourdomain.com/voice/webhook",
          "outbound": {
            "defaultMode": "conversation"
          },
          "maxDurationSeconds": 3600,
          "streaming": {
            "enabled": true,
            "sttProvider": "openai-realtime-conversation",
            "realtimeModel": "gpt-realtime-2.1",
            "realtimeVoice": "alloy",
            "openaiApiKey": "${OPENAI_API_KEY}",
            "realtimePolicy": {
              "idleTimeoutMs": 120000,
              "maxSessionMs": 7200000
            }
          },
          "callScreening": {
            "enabled": true,
            "callerIdentity": "Hi, this is your AI assistant calling on your behalf."
          }
        }
      }
    }
  }
}
```

Validate and restart:

```bash
openclaw config validate
openclaw gateway restart
```

Verify webhook accessibility:

```bash
# Should return 200
curl -s -o /dev/null -w "%{http_code}\n" -X POST https://voice.yourdomain.com/voice/webhook

# Should return 101 (WebSocket upgrade)
curl -s -o /dev/null -w "%{http_code}\n" \
  -H "Connection: Upgrade" \
  -H "Upgrade: websocket" \
  -H "Sec-WebSocket-Version: 13" \
  -H "Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==" \
  https://voice.yourdomain.com/voice/stream
```

## Using the Tool

The plugin provides a `voice_call` tool to your OpenClaw agent with three actions:

### Initiate Call

```typescript
{
  "action": "initiate_call",
  "to": "+15558675309",
  "message": "Hi! I'm calling on behalf of Alex to book a table.",
  "mode": "conversation",  // or "tts-stt" for legacy
  "call_party": "third-party",  // or "first-party" when calling yourself
  "caller_identity": "Hi, this is Sam, Alex's assistant.",  // optional
  "talking_points": [
    "Table for 2 people",
    "Friday around 7pm",
    "Get a confirmation number"
  ]
}
```

### Check Status

```typescript
{
  "action": "get_status",
  "callId": "call_abc123xyz"
}
```

### Get Transcript

```typescript
{
  "action": "get_transcript",
  "callId": "call_abc123xyz"
}
```

## In-Call Tools

During a call, the OpenAI Realtime session has access to these tools (invoked autonomously):

### press_phone_keys

Navigate IVR menus by pressing DTMF tones:

```typescript
{
  "name": "press_phone_keys",
  "parameters": {
    "keys": "2",  // single digit or sequence like "123#"
    "reason": "Pressing 2 for reservations menu"
  }
}
```

### report_call_outcome

Capture structured outcome data during or at end of call:

```typescript
{
  "name": "report_call_outcome",
  "parameters": {
    "status": "success",  // success, partial_success, failure, no_answer, voicemail
    "summary": "Booked table for 2 on Friday at 7pm",
    "details": {
      "confirmation_number": "RES-12345",
      "date": "2026-07-12",
      "time": "19:00",
      "party_size": 2,
      "notes": "Parties over 15 minutes late lose the table"
    }
  }
}
```

### end_call

Gracefully hang up after speaking a closing line:

```typescript
{
  "name": "end_call",
  "parameters": {
    "closing_line": "Thanks so much, have a great day!",
    "reason": "Reservation confirmed, all information collected"
  }
}
```

The plugin waits for the closing line to fully play out on the line before disconnecting (using Twilio mark echo).

## Configuration Options

### Voice & Model

```json
"streaming": {
  "realtimeModel": "gpt-realtime-2.1",  // or gpt-4o-realtime-preview
  "realtimeVoice": "alloy",  // alloy, echo, fable, onyx, nova, shimmer
  "realtimePolicy": {
    "idleTimeoutMs": 120000,     // hang up after 2 min silence
    "maxSessionMs": 7200000      // hard cap at 2 hours
  }
}
```

### Call Screening

```json
"callScreening": {
  "enabled": true,
  "callerIdentity": "Hi, this is Alex's AI assistant calling on their behalf.",
  "maxIdentityRepeats": 2  // how many times to repeat if call screener is detected
}
```

### Inbound Calls

```json
"inbound": {
  "enabled": true,
  "allowedCallers": ["+15551234567"],  // empty = allow all
  "greeting": "Hello! This is Alex's AI assistant. How can I help you?"
}
```

### Device Profiles

Override behavior for specific callers:

```json
"deviceProfiles": {
  "+15551234567": {
    "name": "My iPhone",
    "maxResponseLength": "short",  // short, medium, long
    "forbiddenActions": ["end_call"],  // prevent AI from hanging up on you
    "extraInstructions": "This is Alex calling. Be casual and use first-person."
  }
}
```

## Real Usage Patterns

### Book a Restaurant Reservation

Natural language to agent:

```
"Call Luigi's Pizza at +1-555-867-5309 and book a table for two on Friday at 7pm"
```

The agent translates this to:

```typescript
{
  "action": "initiate_call",
  "to": "+15558675309",
  "message": "Hi! I'm calling to make a reservation.",
  "call_party": "third-party",
  "talking_points": [
    "Table for 2 people",
    "This Friday evening around 7pm",
    "Get confirmation number",
    "Ask about cancellation policy"
  ]
}
```

### Check Store Hours

```
"Call the hardware store and ask if they're open today until 6pm"
```

Agent makes the call, navigates any IVR, asks the question, gets the answer, hangs up, reports back.

### Call Yourself for Urgent Decisions

```typescript
{
  "action": "initiate_call",
  "to": "+15551234567",  // your number
  "call_party": "first-party",
  "message": "Hey Alex, the contractor called back. They can do Tuesday or Thursday. Which works better for you?",
  "talking_points": [
    "Get preferred date",
    "Confirm morning or afternoon"
  ]
}
```

With `"call_party": "first-party"` and your number in device profiles with `"forbiddenActions": ["end_call"]`, the AI won't hang up on you — you control when the call ends.

## Transcript Example

After a call ends, retrieve the transcript:

```typescript
{
  "action": "get_transcript",
  "callId": "call_abc123xyz"
}
```

Transcripts are saved to `~/.openclaw/voice-calls/transcripts/<callId>.md`:

```markdown
# Call Transcript

**Call ID:** call_abc123xyz  
**From:** +15551234567  
**To:** +15558675309  
**Started:** 2026-07-08 14:23:11 UTC  
**Ended:** 2026-07-08 14:25:43 UTC  
**Duration:** 2m 32s  
**Status:** completed  

## Outcome

**Status:** success  
**Summary:** Reserved table for 2 on Friday July 12 at 7pm, confirmation RES-12345

**Details:**
- confirmation_number: RES-12345
- date: 2026-07-12
- time: 19:00
- party_size: 2
- notes: Parties over 15 minutes late lose the table

## AI Summary

Successfully booked a table for 2 at Luigi's Pizza for Friday, July 12 at 7:00 PM...

## Full Transcript

**[14:23:15] Assistant:** Hi! I'm calling to make a reservation for this Friday.

**[14:23:18] Host:** Sure! What time and how many people?

**[14:23:20] Assistant:** Two people, around 7 PM if you have availability.

**[14:23:23] Host:** Let me check... yes, I have 7 PM available. Can I get a name?

**[14:23:26] Assistant:** Alex.

**[14:23:28] Host:** Great, table for 2 under Alex on Friday the 12th at 7. Your confirmation is RES-12345...
```

## Troubleshooting

### Webhook Returns 404

- Verify `publicUrl` matches your tunnel hostname exactly
- Check tunnel is running: `cloudflared tunnel info voice`
- Test direct: `curl -X POST https://voice.yourdomain.com/voice/webhook`

### Calls Immediately Disconnect

- Check Twilio webhook signature verification isn't failing (logs will show `Invalid signature`)
- Verify auth token in config matches Console
- Ensure webhook returns valid TwiML (check gateway logs for errors)

### No Audio / Silent Call

- WebSocket stream must be publicly accessible at `/voice/stream`
- Test upgrade: `curl -i -H "Connection: Upgrade" -H "Upgrade: websocket" ...`
- Check OpenAI API key has Realtime API access
- Verify `streaming.enabled: true` and `sttProvider: "openai-realtime-conversation"`

### AI Won't Press Keys / End Call

- Ensure `mode: "conversation"` (not `"tts-stt"`)
- Tools are only available in OpenAI Realtime mode
- Check session instructions include tool descriptions (plugin handles this automatically)

### Transcripts Missing

- Transcripts finalize ~10 seconds after call ends
- Check `~/.openclaw/voice-calls/transcripts/` directory exists and is writable
- Look for finalization errors in gateway logs

### Call Stays Open After AI Says Goodbye

- Plugin waits for Twilio `mark` echo to confirm closing line played
- If mark never arrives (network issue), call will hit idle timeout (default 120s)
- Check for WebSocket disconnections in logs

### International Calls Fail

- Enable geo permissions: Twilio Console → Voice → Settings → Geo permissions
- Check number format is E.164 (`+` + country code + number)
- Verify account isn't trial (trial can only call verified numbers)

## Environment Variables

Set these in your shell before starting OpenClaw, or use a `.env` file:

```bash
export TWILIO_ACCOUNT_SID="ACxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
export TWILIO_AUTH_TOKEN="your_twilio_auth_token"
export OPENAI_API_KEY="sk-proj-..."
```

Reference in config as `${TWILIO_ACCOUNT_SID}`, etc.

## Advanced: Custom System Prompt

Override the voice AI's instructions per call:

```typescript
{
  "action": "initiate_call",
  "to": "+15558675309",
  "message": "Hi!",
  "talking_points": ["..."],
  "streaming": {
    "customInstructions": "You are a friendly but professional concierge. Always confirm spelling of names."
  }
}
```

## Providers

Default is Twilio with OpenAI Realtime (recommended). Legacy providers available:

- **Telnyx** — alternative to Twilio (set `provider: "telnyx"`)
- **Plivo** — another alternative (set `provider: "plivo"`)
- **Mock** — for testing without real calls (set `provider: "mock"`)

Legacy mode uses separate STT + LLM + TTS pipeline instead of Realtime API. Set `outbound.defaultMode: "tts-stt"` and configure `deepgram`, `openai`, etc. providers. **Not recommended** — conversation mode is superior.

## Security Notes

- Webhook signature verification is enabled by default — don't disable it
- Per-call stream auth tokens expire after connection
- Pre-auth connection throttling prevents DoS
- Inbound calls respect allowlist when configured
- Call duration hard caps prevent runaway costs
- SSRF guards on provider API calls

## Cost Estimates

- **Twilio:** ~$1.15/month for phone number + ~$0.014/min for calls (US)
- **OpenAI Realtime:** ~$0.06/min audio input + $0.24/min audio output (gpt-realtime-2.1)
- **Example:** 10-minute call ≈ $0.14 Twilio + $3.00 OpenAI = **$3.14 total**

Use `maxDurationSeconds` and `realtimePolicy.maxSessionMs` to prevent surprises.
