---
name: openclaw-windows-companion
description: Windows companion suite for OpenClaw AI assistant - system tray app, node capabilities, WebSocket gateway client, and CLI tools
triggers:
  - build and run the OpenClaw Windows tray app
  - connect to OpenClaw gateway from Windows
  - set up OpenClaw node mode on Windows
  - use OpenClaw CLI to test WebSocket connection
  - configure OpenClaw Windows companion settings
  - enable camera and screen capture for OpenClaw agent
  - troubleshoot OpenClaw Windows connection issues
  - create OpenClaw Windows installer package
---

# OpenClaw Windows Companion

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

The OpenClaw Windows Companion is a native C# suite that connects Windows PCs to the OpenClaw AI assistant. It includes a WinUI 3 system tray app (Molty), shared gateway client library, and CLI tools for WebSocket validation.

**Key capabilities:**
- System tray app with quick send (Ctrl+Alt+Shift+C)
- Node mode: turn your Windows PC into a controllable agent node
- WebView2-based chat and canvas windows
- Screen capture, camera access, speech-to-text, text-to-speech
- Real-time gateway status, sessions, channels, usage tracking
- Toast notifications with smart categorization
- Auto-updates from GitHub releases

## Architecture

```
openclaw-windows-node/
├── src/
│   ├── OpenClaw.Tray.WinUI/      # System tray app (WinUI 3)
│   ├── OpenClaw.Shared/          # Gateway client library
│   └── OpenClaw.Cli/             # WebSocket validator CLI
├── build.ps1                     # Build script with prereq checks
└── run-app-local.ps1            # Launch tray app for development
```

## Prerequisites

```powershell
# Check prerequisites
.\build.ps1 -CheckOnly
```

**Required:**
- Windows 10 (20H2+) or Windows 11
- .NET 10.0 SDK
- Windows 10 SDK (for WinUI build)
- WebView2 Runtime (pre-installed on modern Windows)

## Building

### Build all projects

```powershell
# Recommended: use build script
.\build.ps1

# Or build specific project
.\build.ps1 -Project WinUI
.\build.ps1 -Project Shared
.\build.ps1 -Project Cli
```

### Build with dotnet directly

```powershell
# Build for ARM64
dotnet build src/OpenClaw.Tray.WinUI/OpenClaw.Tray.WinUI.csproj -r win-arm64

# Build for x64
dotnet build src/OpenClaw.Tray.WinUI/OpenClaw.Tray.WinUI.csproj -r win-x64

# Build MSIX package (for camera/mic consent)
dotnet build src/OpenClaw.Tray.WinUI -r win-arm64 -p:PackageMsix=true
dotnet build src/OpenClaw.Tray.WinUI -r win-x64 -p:PackageMsix=true
```

## Running the Tray App

```powershell
# Build and launch (Debug)
.\run-app-local.ps1

# Skip rebuild if already built
.\run-app-local.ps1 -NoBuild

# Run isolated (separate settings)
.\run-app-local.ps1 -Isolated

# Test alpha updates from Release build
.\run-app-local.ps1 -Configuration Release -Isolated -UpdateChannel alpha

# Launch through WinAppCLI (manifest validation)
.\run-app-local.ps1 -UseWinApp -NoBuild
```

## Configuration

Settings are stored in `%APPDATA%\OpenClawTray\settings.json`:

```json
{
  "GatewayUrl": "ws://127.0.0.1:18789",
  "AuthToken": "your-gateway-token",
  "AutoStart": true,
  "NotificationsEnabled": true,
  "NodeMode": {
    "Enabled": true,
    "DeviceId": "auto-generated-uuid"
  },
  "UpdateChannel": "stable"
}
```

### Gateway Connection

```csharp
using OpenClaw.Shared;

// Read settings
var settings = SettingsManager.Load();

// Create gateway client
var client = new GatewayClient(settings.GatewayUrl, settings.AuthToken);

// Connect
await client.ConnectAsync();

// Send chat message (requires operator.write scope)
await client.SendChatMessageAsync("Hello from Windows!");

// Subscribe to events
client.OnSessionUpdate += (sender, session) => {
    Console.WriteLine($"Session {session.Id}: {session.Status}");
};

client.OnUsageUpdate += (sender, usage) => {
    Console.WriteLine($"Usage: ${usage.TotalCost:F4}");
};
```

## CLI WebSocket Validator

Test gateway connectivity and `chat.send` without the tray UI:

```powershell
# Show help
dotnet run --project src/OpenClaw.Cli -- --help

# Use tray settings and send one message
dotnet run --project src/OpenClaw.Cli -- --message "test message"

# Loop sends with API probes
dotnet run --project src/OpenClaw.Cli -- `
  --repeat 5 `
  --delay-ms 1000 `
  --probe-read `
  --verbose

# Override gateway URL/token
dotnet run --project src/OpenClaw.Cli -- `
  --url ws://127.0.0.1:18789 `
  --token $env:OPENCLAW_TOKEN `
  --message "override test"
```

### CLI Options

```
--url <url>           Gateway WebSocket URL (default: from settings.json)
--token <token>       Auth token (default: from settings.json)
--message <text>      Message to send via chat.send
--repeat <count>      Number of times to send (default: 1)
--delay-ms <ms>       Delay between sends (default: 0)
--probe-read          Also call sessions/usage/nodes APIs
--verbose             Detailed logging
```

## Node Mode Setup

Enable Node Mode to let the OpenClaw agent control your Windows PC:

### 1. Enable in Settings

```csharp
// Enable node mode programmatically
var settings = SettingsManager.Load();
settings.NodeMode.Enabled = true;
SettingsManager.Save(settings);
```

Or toggle in the tray app Settings page.

### 2. Approve Device on Gateway

First connection creates a pairing request:

```bash
# List devices
openclaw devices list

# Approve your Windows device
openclaw devices approve <device-id>
```

### 3. Configure Gateway Allowlist

Edit `~/.openclaw/openclaw.json` on your gateway:

```json
{
  "gateway": {
    "nodes": {
      "allowCommands": [
        "system.notify",
        "system.run",
        "system.run.prepare",
        "system.which",
        "system.execApprovals.get",
        "system.execApprovals.set",
        "canvas.present",
        "canvas.hide",
        "canvas.navigate",
        "canvas.eval",
        "canvas.snapshot",
        "canvas.a2ui.push",
        "canvas.a2ui.pushJSONL",
        "canvas.a2ui.reset",
        "screen.snapshot",
        "screen.record",
        "camera.list",
        "camera.snap",
        "camera.clip",
        "stt.transcribe",
        "location.get",
        "device.info",
        "device.status",
        "tts.speak"
      ]
    }
  }
}
```

## Node Capabilities

### System Commands

```csharp
// Show Windows toast notification
await nodeHandler.HandleSystemNotifyAsync(new {
    title = "OpenClaw",
    message = "Task completed!",
    sound = "default"
});

// Execute command (subject to exec approval policy)
await nodeHandler.HandleSystemRunAsync(new {
    command = "powershell",
    args = new[] { "-Command", "Get-Date" },
    timeout = 5000
});

// Check if command is in PATH
var whichResult = await nodeHandler.HandleSystemWhichAsync(new {
    command = "git"
});
```

### Canvas Control

```csharp
// Present WebView2 canvas window
await nodeHandler.HandleCanvasPresentAsync(new {
    url = "https://example.com",
    width = 1200,
    height = 800
});

// Navigate to new URL
await nodeHandler.HandleCanvasNavigateAsync(new {
    url = "https://openclaw.ai"
});

// Execute JavaScript
await nodeHandler.HandleCanvasEvalAsync(new {
    script = "document.title"
});

// Take screenshot
var snapshot = await nodeHandler.HandleCanvasSnapshotAsync(new {
    format = "png"
});
```

### Screen Capture

```csharp
// Capture screenshot
var screenshot = await nodeHandler.HandleScreenSnapshotAsync(new {
    display = 0,  // Primary display
    format = "png"
});

// Record screen (fixed duration)
var recording = await nodeHandler.HandleScreenRecordAsync(new {
    display = 0,
    duration = 10000,  // 10 seconds
    format = "mp4"
});
```

### Camera Access

```csharp
// List cameras
var cameras = await nodeHandler.HandleCameraListAsync();

// Capture photo
var photo = await nodeHandler.HandleCameraSnapAsync(new {
    device = 0,  // First camera
    format = "jpeg"
});

// Capture video clip
var clip = await nodeHandler.HandleCameraClipAsync(new {
    device = 0,
    duration = 5000,  // 5 seconds
    format = "mp4"
});
```

### Speech-to-Text

```csharp
// Transcribe from microphone (opt-in via Settings)
var transcription = await nodeHandler.HandleSttTranscribeAsync(new {
    duration = 10000,  // 10 seconds
    language = "en-US"
});
```

### Text-to-Speech

```csharp
// Speak via Windows TTS
await nodeHandler.HandleTtsSpeakAsync(new {
    text = "Hello from OpenClaw!",
    voice = "Microsoft David Desktop"
});

// Or use ElevenLabs (if configured)
await nodeHandler.HandleTtsSpeakAsync(new {
    text = "Hello from OpenClaw!",
    provider = "elevenlabs",
    voice = "21m00Tcm4TlvDq8ikWAM"
});
```

## Shared Library Usage

The `OpenClaw.Shared` library provides the gateway client and models:

```csharp
using OpenClaw.Shared;
using OpenClaw.Shared.Models;

// Initialize client
var client = new GatewayClient(
    "ws://127.0.0.1:18789",
    "your-auth-token"
);

// Event subscriptions
client.OnConnected += async (sender, args) => {
    Console.WriteLine("Connected to gateway");
    await client.SubscribeToSessionsAsync();
    await client.SubscribeToUsageAsync();
};

client.OnDisconnected += (sender, reason) => {
    Console.WriteLine($"Disconnected: {reason}");
};

client.OnSessionUpdate += (sender, session) => {
    Console.WriteLine($"Session {session.Id} status: {session.Status}");
};

client.OnUsageUpdate += (sender, usage) => {
    Console.WriteLine($"Total cost: ${usage.TotalCost:F4}");
};

// Connect
await client.ConnectAsync();

// Send message
var result = await client.SendChatMessageAsync("What's the weather?");

// Get sessions
var sessions = await client.GetSessionsAsync();
foreach (var session in sessions) {
    Console.WriteLine($"{session.Id}: {session.Status}");
}

// Get usage
var usage = await client.GetUsageAsync();
Console.WriteLine($"Providers: {string.Join(", ", usage.Providers.Keys)}");
```

## Quick Send Scope Requirements

Quick Send uses `chat.send` and requires `operator.write` scope.

If you get `missing scope: operator.write`:

1. The error response includes your `client.id` and operator device ID
2. Update your gateway token to include `operator.write` scope
3. Reconnect and retry

If you get `pairing required` / `NOT_PAIRED`:

1. Approve the device in gateway pairing approvals
2. Reconnect and retry

## Troubleshooting

### Connection Issues

```powershell
# Test WebSocket connection with CLI
dotnet run --project src/OpenClaw.Cli -- `
  --url ws://127.0.0.1:18789 `
  --token $env:OPENCLAW_TOKEN `
  --verbose
```

Check gateway logs:
```bash
tail -f ~/.openclaw/logs/gateway.log
```

### Node Pairing

```bash
# List pairing requests
openclaw devices list

# Check device status
openclaw devices status <device-id>

# Re-approve if needed
openclaw devices approve <device-id>
```

### Camera/Microphone Permissions

MSIX packages declare capabilities, but Windows may still prompt for consent. If capabilities fail:

1. Check Windows Settings → Privacy & security → Camera/Microphone
2. Ensure OpenClaw is allowed
3. Rebuild with MSIX package: `dotnet build -p:PackageMsix=true`

### Build Errors

```powershell
# Clean and rebuild
dotnet clean
.\build.ps1

# Check prerequisites
.\build.ps1 -CheckOnly

# Verify Windows SDK
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows Kits\Installed Roots"
```

### WebView2 Issues

```powershell
# Check WebView2 runtime
Get-AppxPackage -Name Microsoft.WebView2

# Install if missing
winget install Microsoft.Edge.WebView2Runtime
```

## Creating Installers

Build installers for distribution:

```powershell
# Build x64 installer
dotnet build src/OpenClaw.Tray.WinUI -r win-x64 -p:PackageMsix=true -c Release

# Build ARM64 installer
dotnet build src/OpenClaw.Tray.WinUI -r win-arm64 -p:PackageMsix=true -c Release
```

Output: `OpenClawCompanion-Setup-{arch}.exe`

## Environment Variables

```powershell
# Override gateway URL
$env:OPENCLAW_GATEWAY_URL = "ws://custom-gateway:18789"

# Override auth token
$env:OPENCLAW_TOKEN = "your-token-here"

# Run with overrides
.\run-app-local.ps1
```

## Deep Links

The app registers the `openclaw://` URL scheme:

```
openclaw://open-chat
openclaw://quick-send
openclaw://settings
openclaw://command-center
```

Use from PowerShell:
```powershell
Start-Process "openclaw://open-chat"
```

## Auto-Start Configuration

```csharp
// Enable auto-start
var autoStart = new AutoStartManager();
await autoStart.EnableAsync();

// Disable
await autoStart.DisableAsync();

// Check status
bool isEnabled = await autoStart.IsEnabledAsync();
```

Registry key: `HKCU\Software\Microsoft\Windows\CurrentVersion\Run\OpenClawTray`
