---
name: hermes-browser-extension
description: Browser extension that connects web context to local/remote Hermes Agent runtime via side panel
triggers:
  - install the hermes browser extension
  - set up hermes browser side panel
  - connect browser to hermes agent
  - configure hermes browser extension
  - use hermes with browser context
  - send webpage content to hermes
  - attach browser context to hermes
  - troubleshoot hermes browser extension
---

# hermes-browser-extension

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## What it does

Hermes Browser Extension is a Chrome/Edge/Chromium side panel that connects active web page context to a Hermes Agent runtime (local or remote). It is **not** a standalone chatbot—it's a browser interface for the real Hermes Gateway/API server, accessing your configured models, tools, skills, sessions, memory, and MCP servers.

Key capabilities:
- Side panel UI powered by Chrome Side Panel API (MV3)
- Connects to local (`http://127.0.0.1:8642`) or remote Hermes API server
- Syncs providers, models, profiles, skills, sessions, and capabilities from Hermes
- Captures and sends active tab context (title, URL, readable text, selected text, metadata, headings, links, forms, buttons)
- Supports per-tab or global side panel mode
- Streams responses from Hermes
- Voice dictation via Hermes STT or browser speech fallback
- Read-only context capture (no browser control, no form submission)
- Multiple themes (Nous, Midnight, Ember, Mono, Cyberpunk, Slate) and light/dark modes

## Installation

### 1. Clone and build

```bash
git clone https://github.com/abundantbeing/hermes-browser-extension.git
cd hermes-browser-extension
npm install
npm run build
```

The extension builds to `dist/`.

### 2. Load unpacked in browser

1. Open `chrome://extensions` or `edge://extensions`
2. Enable **Developer mode**
3. Click **Load unpacked**
4. Select the `dist/` folder (not repo root, not `extension/`)
5. Pin the Hermes extension icon and click to open side panel

After code changes, run `npm run build` and click **Reload** on the extension card.

### 3. Verify installation

```bash
# Check that dist/ contains manifest.json with version 0.1.6+
cat dist/manifest.json | grep version
```

## Configuration

### Local API server (recommended default)

On the machine running Hermes, edit `~/.hermes/.env`:

```bash
API_SERVER_ENABLED=true
API_SERVER_HOST=127.0.0.1
API_SERVER_PORT=8642
API_SERVER_KEY=your-secret-key-here
API_SERVER_CORS_ORIGINS=chrome-extension://your-extension-id
```

Find your extension ID at `chrome://extensions` (looks like `abcdefghijklmnopqrstuvwxyz123456`).

Start/restart Hermes Gateway:

```bash
hermes gateway run
```

Verify API server is reachable:

```bash
curl http://127.0.0.1:8642/health
curl -H "Authorization: Bearer your-secret-key-here" http://127.0.0.1:8642/v1/models
```

### Connect extension to local API

In the extension side panel:

1. Click **Connect to Hermes**
2. If approval flow is available, approve locally in Hermes Desktop/gateway
3. Otherwise, click **Manual setup** → **Local gateway**
4. Gateway URL: `http://127.0.0.1:8642`
5. API Key: paste your `API_SERVER_KEY` or scoped browser token
6. Click **Test connection**, then **Save settings**
7. Navigate to a normal webpage and test: `Summarize this page in one sentence.`

### Remote API server

For remote Hermes (same LAN, Tailscale, VPN), bind to reachable interface:

```bash
API_SERVER_ENABLED=true
API_SERVER_HOST=0.0.0.0
API_SERVER_PORT=8642
API_SERVER_KEY=your-secret-key-here
API_SERVER_CORS_ORIGINS=chrome-extension://your-extension-id
```

**Security**: Use private network/VPN with HTTP, or put behind HTTPS reverse proxy. Never expose Hermes API server naked to public internet.

Example URLs:
```
http://192.168.1.50:8642
http://hermes-desktop.local:8642
https://hermes.example.com
```

In extension:
1. Choose **Remote gateway**
2. Paste remote URL (include `http://` or `https://`)
3. Paste API key/browser token
4. Click **Test connection**

### Remote dashboard mode (no API server)

If you only expose the OAuth-gated Hermes dashboard (no API server):

1. Select **Remote**
2. Enter dashboard `https://` URL
3. Leave API key **blank**
4. Open dashboard URL in normal tab and sign in (keep tab open)
5. Extension mints WebSocket ticket from signed-in tab
6. Click **Test connection** (opens socket and loads models)

Limitations: image attachments inline-only, skills/profiles unavailable (REST-only).

## Usage patterns

### Basic prompting with page context

```text
# Side panel captures active tab automatically
Summarize this page
Explain the main concept here
What are the key action items on this page?
```

### Quick commands

The extension includes built-in quick commands:

```text
/summarize - Summarize active page
/explain - Explain main concepts
/rewrite - Rewrite or improve content
/tabs - Analyze all open tabs
/action-items - Extract action items
```

### Context scope control

Click the context menu in composer header to choose:

- **Chat only** - no browser context attached
- **Follow active tab** - context auto-updates when you switch tabs
- **Page only** - pin to specific tab
- **Choose tabs** - select which open tabs to include

### Voice dictation

Click microphone icon in composer:
- Uses Hermes audio transcription if available
- Falls back to browser speech recognition otherwise

### What Hermes saw

After each turn, expand "What Hermes saw" to inspect:
- Active tab title, URL
- Selected text (if any)
- Readable page text (first N chars)
- Metadata, headings, links, forms, buttons
- Open tabs list (if included)

This is the exact context sent to Hermes.

## Code examples

### Programmatically send message (content script)

```javascript
// From a content script, send message to background
chrome.runtime.sendMessage({
  type: 'HERMES_PROMPT',
  payload: {
    message: 'Summarize this page',
    context: {
      url: window.location.href,
      title: document.title,
      selectedText: window.getSelection().toString()
    }
  }
});
```

### Access stored settings

```javascript
// From extension context (popup, side panel, background)
import { getSettings } from './lib/storage';

const settings = await getSettings();
console.log('Gateway URL:', settings.gatewayUrl);
console.log('Theme:', settings.appearance?.theme);
console.log('Context mode:', settings.browserBehavior?.contextScope);
```

### Capture page context

```javascript
// From content script (extension does this automatically)
const context = {
  url: window.location.href,
  title: document.title,
  selectedText: window.getSelection().toString().trim(),
  readableText: document.body.innerText.slice(0, 50000),
  metadata: {
    description: document.querySelector('meta[name="description"]')?.content,
    author: document.querySelector('meta[name="author"]')?.content
  },
  headings: Array.from(document.querySelectorAll('h1, h2, h3')).map(h => ({
    level: h.tagName,
    text: h.textContent.trim()
  })),
  links: Array.from(document.querySelectorAll('a[href]')).slice(0, 50).map(a => ({
    text: a.textContent.trim(),
    href: a.href
  }))
};
```

### Stream Hermes response

```javascript
// Extension internal pattern
async function streamHermesResponse(sessionId, messages, signal) {
  const response = await fetch(`${gatewayUrl}/v1/chat/completions`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${apiKey}`
    },
    body: JSON.stringify({
      session_id: sessionId,
      messages,
      stream: true
    }),
    signal
  });

  const reader = response.body.getReader();
  const decoder = new TextDecoder();
  let buffer = '';

  while (true) {
    const { done, value } = await reader.read();
    if (done) break;

    buffer += decoder.decode(value, { stream: true });
    const lines = buffer.split('\n');
    buffer = lines.pop();

    for (const line of lines) {
      if (line.startsWith('data: ') && line !== 'data: [DONE]') {
        const data = JSON.parse(line.slice(6));
        const content = data.choices?.[0]?.delta?.content;
        if (content) {
          // Append to UI
          yield content;
        }
      }
    }
  }
}
```

### Custom theme integration

```javascript
// Extension appearance settings
const themes = {
  nous: { primary: 'rgb(79, 70, 229)', secondary: 'rgb(99, 102, 241)' },
  midnight: { primary: 'rgb(59, 130, 246)', secondary: 'rgb(96, 165, 250)' },
  ember: { primary: 'rgb(249, 115, 22)', secondary: 'rgb(251, 146, 60)' },
  mono: { primary: 'rgb(71, 85, 105)', secondary: 'rgb(100, 116, 139)' },
  cyberpunk: { primary: 'rgb(236, 72, 153)', secondary: 'rgb(244, 114, 182)' },
  slate: { primary: 'rgb(100, 116, 139)', secondary: 'rgb(148, 163, 184)' }
};

// Apply theme
function applyTheme(themeName, colorMode) {
  const theme = themes[themeName];
  document.documentElement.style.setProperty('--color-primary', theme.primary);
  document.documentElement.style.setProperty('--color-secondary', theme.secondary);
  document.documentElement.classList.toggle('dark', colorMode === 'dark');
}
```

## Environment variables

Extension does not use `.env` files directly. Configuration is stored in Chrome storage and managed via settings UI.

For Hermes Gateway API server (machine running Hermes):

```bash
# ~/.hermes/.env
API_SERVER_ENABLED=true
API_SERVER_HOST=127.0.0.1
API_SERVER_PORT=8642
API_SERVER_KEY=<use-env-var-or-secret-manager>
API_SERVER_CORS_ORIGINS=chrome-extension://<extension-id>
```

**Never commit API keys**. Use `$(hermes auth token create --scope browser)` to generate scoped tokens, or reference secrets from environment/vault.

## Troubleshooting

### Extension loaded but nothing works

- Ensure you loaded `dist/`, not repo root
- Selected folder must contain `manifest.json` directly
- Check `chrome://extensions` shows version 0.1.6+

### Cannot connect to Hermes

```bash
# Verify API server is running and reachable
curl http://127.0.0.1:8642/health

# Check models endpoint with auth
curl -H "Authorization: Bearer your-key-here" http://127.0.0.1:8642/v1/models
```

Common issues:
- `API_SERVER_ENABLED=true` not set in `~/.hermes/.env`
- Hermes Gateway not running (`hermes gateway run`)
- Wrong `API_SERVER_KEY` or extension API key
- CORS origin mismatch (extension ID not in `API_SERVER_CORS_ORIGINS`)
- Firewall blocking port 8642

### Wrong version after update

Browser cached old unpacked folder:

1. Run `npm run build` to generate fresh `dist/`
2. Open `chrome://extensions`
3. Click **Reload** on Hermes Browser Extension card
4. If still wrong, click **Remove**, then **Load unpacked** and select fresh `dist/`
5. Verify version: click **Details** → see Version field

### No page context captured

Extension cannot read restricted pages:
- `chrome://` or `edge://` internal pages
- `chrome-extension://` pages
- Banking, crypto, password, payment, health, government domains (intentional security restriction)

On normal `https://` pages, check:
- Context scope is not "Chat only"
- Page has readable text (not just images/video)
- Click "What Hermes saw" to inspect captured context

### Voice dictation not working

- Check Hermes capabilities: extension queries `/v1/capabilities` for STT support
- If Hermes STT unavailable, extension falls back to browser speech recognition
- Browser speech requires microphone permission (granted on first use)
- Some browsers/OSes may not support Web Speech API

### Computer use / browser automation not working

Hermes Browser Extension is **read-only**. It does not have browser control permissions (`debugger`, click, type, form submit).

For native computer use:
```bash
# On machine running Hermes
hermes tools list
hermes computer-use status
```

Computer use comes from Hermes Agent's `computer_use` toolset via `cua-driver`, not the browser extension.

### CORS errors in console

Extension origin must be allowlisted on Hermes API server:

```bash
# ~/.hermes/.env
API_SERVER_CORS_ORIGINS=chrome-extension://your-extension-id-here
```

Find extension ID at `chrome://extensions`. Restart `hermes gateway run` after changing.

### Session persistence issues

- Extension binds one Hermes session per pinned tab
- Switching tabs creates new sessions (unless "Follow active tab" is enabled)
- Check `/api/sessions` in Hermes to see active sessions
- Clear local history: Settings → Browser behavior → Clear history

### Streaming response fails

Extension auto-falls back to non-streaming if:
- Gateway does not support `stream: true`
- Network drops chunks
- Response is not SSE format

Check gateway logs for streaming errors. Non-streaming mode still works.

## Security notes

Extension is intentionally conservative:

- **Read-only**: no click, type, form submit, download, or browser control
- **No sensitive permissions**: no `debugger`, `nativeMessaging`, `cookies`, `history`, `downloads`, `bookmarks`
- **Content wrapped as untrusted**: page text marked as external context before Hermes sees it
- **Local-first default**: connects to `127.0.0.1:8642` unless you configure remote
- **Bearer auth required**: strong API key for all API access
- **CORS allowlist**: extension origin must be explicitly allowed

See repo docs: `SECURITY.md`, `PERMISSIONS.md`, `DATA-FLOW.md`, `PRIVACY.md`.

## Key files

```
dist/                     # Built extension (load this)
extension/
  manifest.json          # MV3 manifest with side_panel, activeTab, scripting
  background.js          # Service worker, message routing
  sidepanel/
    index.html           # Side panel UI entry
    app.jsx              # Main React app
  content/
    content-script.js    # Page context capture
  lib/
    hermes-client.js     # Hermes API/WebSocket client
    storage.js           # Chrome storage wrapper
    context-capture.js   # DOM scraping logic
```

## Common workflows

### Install and connect to local Hermes

```bash
git clone https://github.com/abundantbeing/hermes-browser-extension.git
cd hermes-browser-extension
npm install
npm run build

# On Hermes machine, edit ~/.hermes/.env:
# API_SERVER_ENABLED=true
# API_SERVER_HOST=127.0.0.1
# API_SERVER_PORT=8642
# API_SERVER_KEY=<secret>
# API_SERVER_CORS_ORIGINS=chrome-extension://<id>

hermes gateway run

# In browser: chrome://extensions → Load unpacked → select dist/
# In side panel: Connect to Hermes → Local gateway → http://127.0.0.1:8642 → paste key → Test → Save
```

### Switch between multiple local Hermes instances

Settings → Localhost agent picker:

```
Port 8642 - Default gateway
Port 8643 - Dev gateway
Port 8644 - Testing gateway
```

Click to switch. Extension stores per-port settings.

### Use with remote Hermes on Tailscale

```bash
# On remote Hermes machine
API_SERVER_HOST=0.0.0.0
API_SERVER_PORT=8642
API_SERVER_CORS_ORIGINS=chrome-extension://<id>

# In extension
# Remote gateway → http://100.x.y.z:8642 → paste key → Test → Save
```

### Capture and analyze open tabs

In side panel:
```text
/tabs

# Or manually:
List all my open tabs and summarize each one
```

Extension sends tab list (title, URL) for all open tabs to Hermes.

### Pin to specific page for isolated conversation

1. Open page you want to analyze
2. Click context menu in composer header → **Page only**
3. Panel shows pinned tab indicator
4. All messages in this panel now tied to that tab, with isolated Hermes session
5. Switch tabs → panel stays on pinned tab
6. Click context menu → **Follow active tab** to resume auto-switching

---

**This skill covers**: installation, local/remote configuration, context capture, voice dictation, quick commands, streaming, theming, security model, and troubleshooting for the Hermes Browser Extension.
