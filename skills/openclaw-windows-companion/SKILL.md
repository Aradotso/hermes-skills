---
name: openclaw-windows-companion
description: Windows companion suite for OpenClaw AI assistant - system tray app, shared library, CLI tools, and node capabilities
triggers:
  - "set up OpenClaw Windows companion"
  - "build OpenClaw tray app"
  - "configure OpenClaw node mode"
  - "use OpenClaw CLI validator"
  - "implement OpenClaw WebSocket client"
  - "troubleshoot OpenClaw Windows pairing"
  - "create OpenClaw canvas commands"
  - "handle OpenClaw notifications"
---

# OpenClaw Windows Companion

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

The OpenClaw Windows Hub is a native Windows companion suite for [OpenClaw](https://openclaw.ai) - an AI-powered personal assistant. It provides a WinUI 3 system tray application (Molty), shared gateway client libraries, and CLI utilities for WebSocket connectivity validation.

## What It Does

- **System Tray App (Molty)**: Modern Windows 11-style tray companion with live gateway status, session management, channel control, usage tracking, and Quick Send hotkey (Ctrl+Alt+Shift+C)
- **Node Mode**: Turns your Windows PC into an OpenClaw node that the agent can control (notifications, command execution, screen capture, camera, canvas WebView2, TTS, location, device info)
- **WebSocket Client**: Shared library (`OpenClaw.Shared`) for gateway connectivity
- **CLI Validator**: `OpenClaw.Cli` for testing WebSocket connect/send/probe operations

## Installation

### End Users

Download the installer for your architecture:
- x64: [OpenClawCompanion-Setup-x64.exe](https://github.com/openclaw/openclaw/releases/latest/download/OpenClawCompanion-Setup-x64.exe)
- ARM64: [OpenClawCompanion-Setup-arm64.exe](https://github.com/openclaw/openclaw/releases/latest/download/OpenClawCompanion-Setup-arm64.exe)

### Developers

**Prerequisites:**
- Windows 10 (20H2+) or Windows 11
- .NET 10.0 SDK: https://dotnet.microsoft.com/download/dotnet/10.0
- Windows 10 SDK (for WinUI)
- WebView2 Runtime (pre-installed on modern Windows)

**Build:**

```powershell
# Clone repository
git clone https://github.com/openclaw/openclaw-windows-node.git
cd openclaw-windows-node

# Check prerequisites
.\build.ps1 -CheckOnly

# Build all projects
.\build.ps1

# Build specific project
.\build.ps1 -Project WinUI
```

**Run locally:**

```powershell
# Build and launch tray app
.\run-app-local.ps1

# Skip rebuild, launch existing Debug build
.\run-app-local.ps1 -NoBuild

# Isolated mode (separate settings for multiple worktrees)
.\run-app-local.ps1 -Isolated

# Test alpha updates from Release build
.\run-app-local.ps1 -Configuration Release -Isolated -UpdateChannel alpha
```

## Key Components

### 1. Tray Application (OpenClaw.Tray.WinUI)

WinUI 3 system tray app with flyout menu, Command Center diagnostics, embedded WebView2 chat, and toast notifications.

**Build WinUI project:**

```powershell
# ARM64
dotnet build src/OpenClaw.Tray.WinUI/OpenClaw.Tray.WinUI.csproj -r win-arm64

# x64
dotnet build src/OpenClaw.Tray.WinUI/OpenClaw.Tray.WinUI.csproj -r win-x64

# With MSIX packaging (for camera/mic consent prompts)
dotnet build src/OpenClaw.Tray.WinUI -r win-arm64 -p:PackageMsix=true
```

**Settings location:**
```
%APPDATA%\OpenClawTray\settings.json
```

**Deep link scheme:**
```
openclaw://action?param=value
```

### 2. Shared Library (OpenClaw.Shared)

Gateway client library for WebSocket connectivity.

**Add to your project:**

```xml
<ProjectReference Include="..\OpenClaw.Shared\OpenClaw.Shared.csproj" />
```

**Usage example:**

```csharp
using OpenClaw.Shared;
using OpenClaw.Shared.Models;

// Initialize gateway client
var settings = new GatewaySettings
{
    Url = "ws://127.0.0.1:18789",
    Token = Environment.GetEnvironmentVariable("OPENCLAW_TOKEN")
};

var client = new GatewayClient(settings);

// Connect
await client.ConnectAsync();

// Send chat message
var response = await client.SendChatAsync(new ChatSendRequest
{
    Message = "Hello from Windows!",
    SessionId = "test-session"
});

// Listen for events
client.OnSessionEvent += (sender, evt) =>
{
    Console.WriteLine($"Session event: {evt.Type} - {evt.Message}");
};

client.OnUsageEvent += (sender, evt) =>
{
    Console.WriteLine($"Usage: {evt.Provider} - ${evt.Cost}");
};

// Disconnect
await client.DisconnectAsync();
```

**Gateway API methods:**

```csharp
// Chat
await client.SendChatAsync(new ChatSendRequest { Message = "test" });

// Sessions
var sessions = await client.GetSessionsAsync();

// Usage
var usage = await client.GetUsageAsync();

// Nodes
var nodes = await client.GetNodesAsync();

// Channels
await client.StartChannelAsync("telegram");
await client.StopChannelAsync("telegram");

// Pairing
var pairings = await client.GetPairingsAsync();
await client.ApprovePairingAsync(pairingId);
```

### 3. CLI Validator (OpenClaw.Cli)

Command-line tool for testing gateway connectivity.

**Basic usage:**

```powershell
# Show help
dotnet run --project src/OpenClaw.Cli -- --help

# Send message using tray settings
dotnet run --project src/OpenClaw.Cli -- --message "test message"

# Loop with probes
dotnet run --project src/OpenClaw.Cli -- --repeat 5 --delay-ms 1000 --probe-read --verbose

# Override URL/token
dotnet run --project src/OpenClaw.Cli -- `
  --url ws://127.0.0.1:18789 `
  --token $env:OPENCLAW_TOKEN `
  --message "custom gateway test"
```

**CLI arguments:**

| Argument | Description |
|----------|-------------|
| `--url <ws-url>` | Gateway WebSocket URL (overrides settings.json) |
| `--token <token>` | Gateway auth token (overrides settings.json) |
| `--message <text>` | Message to send via chat.send |
| `--repeat <n>` | Number of times to send message |
| `--delay-ms <ms>` | Delay between sends (default: 1000ms) |
| `--probe-read` | Also probe sessions/usage/nodes APIs |
| `--verbose` | Detailed logging |

## Node Mode Configuration

### Enable Node Capabilities

1. **In Tray Settings**: Enable "Node Mode" (enabled by default)

2. **Approve Device on Gateway**:

```bash
# List devices
openclaw devices list

# Approve your Windows device
openclaw devices approve <device-id>
```

3. **Configure Allowed Commands** in `~/.openclaw/openclaw.json`:

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

### Node Capabilities Reference

#### System Commands

```csharp
// Show Windows toast notification
await nodeClient.SendCommandAsync("system.notify", new
{
    title = "Hello",
    message = "Notification from agent",
    tag = "demo"
});

// Execute command with approval policy
await nodeClient.SendCommandAsync("system.run", new
{
    command = "git status",
    cwd = "C:\\Projects\\myapp",
    requestId = Guid.NewGuid().ToString()
});

// Prepare command for approval
await nodeClient.SendCommandAsync("system.run.prepare", new
{
    command = "npm install",
    cwd = "C:\\Projects\\myapp"
});

// Get execution approvals
var approvals = await nodeClient.SendCommandAsync("system.execApprovals.get", new { });

// Update approval policy
await nodeClient.SendCommandAsync("system.execApprovals.set", new
{
    approvals = new[]
    {
        new { pattern = "git.*", policy = "allow" },
        new { pattern = "rm -rf.*", policy = "deny" }
    }
});
```

#### Canvas (WebView2 Window)

```csharp
// Present canvas window
await nodeClient.SendCommandAsync("canvas.present", new
{
    url = "https://example.com",
    title = "Demo Canvas",
    width = 800,
    height = 600
});

// Navigate to new URL
await nodeClient.SendCommandAsync("canvas.navigate", new
{
    url = "https://openclaw.ai"
});

// Execute JavaScript
await nodeClient.SendCommandAsync("canvas.eval", new
{
    script = "document.querySelector('h1').textContent = 'Updated by agent';"
});

// Take screenshot
var snapshot = await nodeClient.SendCommandAsync("canvas.snapshot", new { });

// Push A2UI content
await nodeClient.SendCommandAsync("canvas.a2ui.push", new
{
    content = "<h1>Hello from agent</h1>"
});

// Hide canvas
await nodeClient.SendCommandAsync("canvas.hide", new { });
```

#### Screen Capture

```csharp
// Take screenshot
var screenshot = await nodeClient.SendCommandAsync("screen.snapshot", new
{
    format = "png", // or "jpeg"
    quality = 90
});
// Returns base64-encoded image

// Record screen (fixed duration)
var recording = await nodeClient.SendCommandAsync("screen.record", new
{
    duration = 5.0, // seconds
    fps = 30
});
// Returns base64-encoded MP4
```

#### Camera

```csharp
// List cameras
var cameras = await nodeClient.SendCommandAsync("camera.list", new { });

// Take photo
var photo = await nodeClient.SendCommandAsync("camera.snap", new
{
    deviceId = "\\\\?\\USB#VID_046D...", // from camera.list
    format = "jpeg",
    quality = 85
});
// Returns base64-encoded image

// Record video clip
var clip = await nodeClient.SendCommandAsync("camera.clip", new
{
    deviceId = "\\\\?\\USB#VID_046D...",
    duration = 3.0 // seconds
});
// Returns base64-encoded MP4
```

#### Speech-to-Text

```csharp
// Transcribe from microphone (requires opt-in in Settings)
var transcription = await nodeClient.SendCommandAsync("stt.transcribe", new
{
    duration = 5.0, // seconds
    language = "en-US" // optional
});
// Returns { text: "transcribed speech..." }
```

#### Location

```csharp
// Get Windows geolocation
var location = await nodeClient.SendCommandAsync("location.get", new { });
// Returns { latitude: 47.6062, longitude: -122.3321, accuracy: 20 }
```

#### Device Info

```csharp
// Get device metadata
var info = await nodeClient.SendCommandAsync("device.info", new { });
// Returns { hostname, os, version, arch, ... }

// Get device status
var status = await nodeClient.SendCommandAsync("device.status", new { });
// Returns { cpuUsage, memoryUsage, ... }
```

#### Text-to-Speech

```csharp
// Speak text (Windows SAPI or ElevenLabs)
await nodeClient.SendCommandAsync("tts.speak", new
{
    text = "Hello from the agent!",
    voice = "default", // or ElevenLabs voice ID
    rate = 1.0,
    volume = 0.8
});
```

## Configuration

### Settings Structure

`%APPDATA%\OpenClawTray\settings.json`:

```json
{
  "gatewayUrl": "ws://127.0.0.1:18789",
  "gatewayToken": "your-operator-token",
  "autoStart": true,
  "notificationsEnabled": true,
  "nodeModeEnabled": true,
  "quickSendHotkey": "Ctrl+Alt+Shift+C",
  "updateChannel": "stable",
  "theme": "system",
  "sttEnabled": false,
  "elevenLabsApiKey": "",
  "elevenLabsVoiceId": ""
}
```

### Environment Variables

```powershell
# Override gateway URL
$env:OPENCLAW_GATEWAY_URL = "ws://custom-host:18789"

# Override auth token
$env:OPENCLAW_TOKEN = "your-token-here"

# Enable debug logging
$env:OPENCLAW_DEBUG = "1"
```

## Common Patterns

### Custom Gateway Client Integration

```csharp
using OpenClaw.Shared;
using OpenClaw.Shared.Models;

public class MyService
{
    private readonly GatewayClient _client;

    public MyService()
    {
        var settings = new GatewaySettings
        {
            Url = Environment.GetEnvironmentVariable("OPENCLAW_GATEWAY_URL"),
            Token = Environment.GetEnvironmentVariable("OPENCLAW_TOKEN"),
            ReconnectDelay = TimeSpan.FromSeconds(5),
            MaxReconnectAttempts = 10
        };

        _client = new GatewayClient(settings);
        
        // Event handlers
        _client.OnConnected += HandleConnected;
        _client.OnDisconnected += HandleDisconnected;
        _client.OnSessionEvent += HandleSessionEvent;
        _client.OnUsageEvent += HandleUsageEvent;
        _client.OnNodeEvent += HandleNodeEvent;
        _client.OnNotification += HandleNotification;
    }

    public async Task StartAsync()
    {
        await _client.ConnectAsync();
    }

    private void HandleConnected(object sender, EventArgs e)
    {
        Console.WriteLine("Gateway connected!");
    }

    private void HandleSessionEvent(object sender, SessionEvent evt)
    {
        Console.WriteLine($"Session {evt.SessionId}: {evt.Message}");
    }

    private void HandleNotification(object sender, NotificationEvent evt)
    {
        // Show UI notification
        ShowToast(evt.Title, evt.Message);
    }

    public async Task SendMessageAsync(string message)
    {
        var response = await _client.SendChatAsync(new ChatSendRequest
        {
            Message = message,
            SessionId = Guid.NewGuid().ToString()
        });
        
        Console.WriteLine($"Response: {response.Message}");
    }
}
```

### Implementing Node Command Handlers

```csharp
using OpenClaw.Shared.Node;

public class CustomNodeHandler : INodeCommandHandler
{
    public async Task<NodeCommandResponse> HandleAsync(NodeCommand command)
    {
        return command.Method switch
        {
            "system.notify" => await HandleNotifyAsync(command),
            "screen.snapshot" => await HandleScreenshotAsync(command),
            "canvas.present" => await HandleCanvasPresentAsync(command),
            _ => new NodeCommandResponse
            {
                Success = false,
                Error = $"Unknown command: {command.Method}"
            }
        };
    }

    private async Task<NodeCommandResponse> HandleNotifyAsync(NodeCommand cmd)
    {
        var title = cmd.Params["title"]?.ToString();
        var message = cmd.Params["message"]?.ToString();
        
        // Show Windows toast
        await ShowToastNotificationAsync(title, message);
        
        return new NodeCommandResponse { Success = true };
    }

    private async Task<NodeCommandResponse> HandleScreenshotAsync(NodeCommand cmd)
    {
        var format = cmd.Params["format"]?.ToString() ?? "png";
        var quality = cmd.Params["quality"]?.ToObject<int>() ?? 90;
        
        var imageBytes = await CaptureScreenAsync(format, quality);
        var base64 = Convert.ToBase64String(imageBytes);
        
        return new NodeCommandResponse
        {
            Success = true,
            Data = new { image = base64, format }
        };
    }
}
```

### Quick Send Implementation

```csharp
using OpenClaw.Shared;
using System.Windows.Input;

public class QuickSendService
{
    private readonly GatewayClient _client;
    private readonly GlobalHotkey _hotkey;

    public QuickSendService(GatewayClient client)
    {
        _client = client;
        
        // Register Ctrl+Alt+Shift+C
        _hotkey = new GlobalHotkey(
            ModifierKeys.Control | ModifierKeys.Alt | ModifierKeys.Shift,
            Key.C
        );
        
        _hotkey.Pressed += OnHotkeyPressed;
    }

    private async void OnHotkeyPressed(object sender, EventArgs e)
    {
        // Show input dialog
        var message = await ShowQuickSendDialogAsync();
        if (string.IsNullOrWhiteSpace(message)) return;

        try
        {
            var response = await _client.SendChatAsync(new ChatSendRequest
            {
                Message = message,
                SessionId = Guid.NewGuid().ToString()
            });

            ShowToast("Quick Send", "Message sent successfully!");
        }
        catch (Exception ex)
        {
            if (ex.Message.Contains("missing scope: operator.write"))
            {
                // Copy remediation to clipboard
                var remediation = GenerateRemediationGuidance();
                Clipboard.SetText(remediation);
                ShowToast("Scope Error", "Token needs operator.write scope. Details copied to clipboard.");
            }
            else if (ex.Message.Contains("NOT_PAIRED"))
            {
                ShowToast("Pairing Required", "Approve this device on the gateway first.");
            }
            else
            {
                ShowToast("Error", ex.Message);
            }
        }
    }
}
```

## Troubleshooting

### Connection Issues

**Problem**: Tray app shows "Disconnected" status

**Solutions**:
```powershell
# 1. Verify gateway is running
openclaw gateway status

# 2. Test WebSocket connectivity with CLI
dotnet run --project src/OpenClaw.Cli -- --verbose

# 3. Check settings.json
notepad %APPDATA%\OpenClawTray\settings.json

# 4. Verify token has required scopes
# Token should include: operator.read, operator.write (for Quick Send)

# 5. Check logs
notepad %APPDATA%\OpenClawTray\logs\app.log
```

### Pairing Issues

**Problem**: Node commands fail with "NOT_PAIRED" or "PAIRING_PENDING"

**Solutions**:
```bash
# List pending pairings
openclaw devices list --pending

# Approve device
openclaw devices approve <device-id>

# Verify approval
openclaw devices list
```

### Quick Send Scope Error

**Problem**: Quick Send fails with "missing scope: operator.write"

**Solutions**:
1. Check token scopes in gateway config
2. Regenerate operator token with `operator.write` scope
3. Update `gatewayToken` in settings.json
4. Restart tray app

**Generate new token**:
```bash
# In gateway config or admin UI, ensure operator token includes:
{
  "scopes": ["operator.read", "operator.write"]
}
```

### Node Commands Not Working

**Problem**: Agent can't control Windows node

**Checklist**:
```json
// 1. Verify gateway.nodes.allowCommands in ~/.openclaw/openclaw.json
{
  "gateway": {
    "nodes": {
      "allowCommands": ["system.notify", "canvas.present", ...]
    }
  }
}

// 2. Confirm device is approved
// openclaw devices list

// 3. Check Node Mode is enabled in tray Settings

// 4. Review node logs
// %APPDATA%\OpenClawTray\logs\node.log
```

### Build Errors

**Problem**: WinUI build fails

**Solutions**:
```powershell
# 1. Install Windows SDK
winget install Microsoft.WindowsSDK.10.0.22621

# 2. Ensure .NET 10 SDK is installed
dotnet --version

# 3. Clean and rebuild
dotnet clean
dotnet build -r win-x64

# 4. For MSIX packaging issues, install WinAppSDK
dotnet workload install microsoft-net-sdk-windowsdesktop
```

### WebView2 Issues

**Problem**: Embedded chat or canvas won't load

**Solutions**:
```powershell
# 1. Install/update WebView2 Runtime
# Download from: https://developer.microsoft.com/microsoft-edge/webview2

# 2. Check WebView2 version
Get-AppxPackage -Name Microsoft.WebView2

# 3. Clear WebView2 cache
Remove-Item -Recurse "$env:LOCALAPPDATA\OpenClawTray\WebView2" -Force
```

### Update Failures

**Problem**: Auto-update fails or hangs

**Solutions**:
```powershell
# 1. Check update channel in settings
notepad %APPDATA%\OpenClawTray\settings.json
# "updateChannel": "stable" or "alpha"

# 2. Manually download installer
# https://github.com/openclaw/openclaw/releases/latest

# 3. Force update check from tray menu
# Right-click tray icon → Settings → Check for Updates

# 4. Review update logs
notepad %APPDATA%\OpenClawTray\logs\update.log
```

### Permission Errors (Camera/Mic/Location)

**Problem**: Node commands fail with permission denied

**Solutions**:
1. Use **packaged MSIX build** for proper capability declarations
2. Grant permissions in Windows Settings → Privacy & Security
3. For camera: Settings → Privacy → Camera → Allow apps to access
4. For microphone: Settings → Privacy → Microphone → Allow apps to access
5. For location: Settings → Privacy → Location → Allow apps to access

```powershell
# Build with MSIX packaging
dotnet build src/OpenClaw.Tray.WinUI -r win-x64 -p:PackageMsix=true
```
