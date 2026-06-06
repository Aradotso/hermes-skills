---
name: openclaw-windows-companion
description: Build and extend the OpenClaw Windows companion suite - WinUI tray app, shared library, CLI tools, and node capabilities for AI-powered personal assistant integration
triggers:
  - how do I build the OpenClaw Windows tray app
  - add a new node capability to OpenClaw Windows
  - connect the OpenClaw Windows companion to my gateway
  - configure OpenClaw Windows Hub node mode
  - create a custom notification handler for OpenClaw tray
  - debug OpenClaw Windows WebSocket connection
  - extend the OpenClaw.Shared gateway client library
  - add a new command to the OpenClaw CLI validator
---

# OpenClaw Windows Companion Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

OpenClaw Windows Hub is a native Windows companion suite for [OpenClaw](https://openclaw.ai), an AI-powered personal assistant. This monorepo contains:

- **OpenClaw.Tray.WinUI**: System tray app (WinUI 3) with Quick Send, Web Chat, Command Center, notifications, and node capabilities
- **OpenClaw.Shared**: Shared gateway client library for WebSocket communication
- **OpenClaw.Cli**: CLI validator for WebSocket connect/send/probe using tray settings

The tray app can run as a **node** that the OpenClaw agent can control - executing commands, capturing screenshots/camera, showing notifications, presenting canvas UI, text-to-speech, speech-to-text, and more.

## Prerequisites

- Windows 10 (20H2+) or Windows 11
- .NET 10.0 SDK
- Windows 10 SDK (for WinUI build)
- WebView2 Runtime (pre-installed on modern Windows)

## Installation & Build

### Quick Build

```powershell
# Check prerequisites
.\build.ps1 -CheckOnly

# Build all projects
.\build.ps1

# Build specific project
.\build.ps1 -Project WinUI
```

### Manual Build

```powershell
# Build all
dotnet build

# Build WinUI for specific architecture
dotnet build src/OpenClaw.Tray.WinUI/OpenClaw.Tray.WinUI.csproj -r win-x64
dotnet build src/OpenClaw.Tray.WinUI/OpenClaw.Tray.WinUI.csproj -r win-arm64

# Build MSIX package (for camera/mic consent prompts)
dotnet build src/OpenClaw.Tray.WinUI -r win-x64 -p:PackageMsix=true
```

### Run Tray App

```powershell
# Build and launch unpackaged WinUI tray app
.\run-app-local.ps1

# Skip rebuild, launch existing Debug output
.\run-app-local.ps1 -NoBuild

# Run isolated from normal tray settings (multi-worktree dev)
.\run-app-local.ps1 -Isolated

# Test alpha updates from Release build
.\run-app-local.ps1 -Configuration Release -Isolated -UpdateChannel alpha
```

### Run CLI Validator

```powershell
# Show help
dotnet run --project src/OpenClaw.Cli -- --help

# Use tray settings and send one message
dotnet run --project src/OpenClaw.Cli -- --message "test message"

# Loop sends with API probing
dotnet run --project src/OpenClaw.Cli -- --repeat 5 --delay-ms 1000 --probe-read --verbose

# Override gateway URL/token
dotnet run --project src/OpenClaw.Cli -- --url ws://127.0.0.1:18789 --token "your-token" --message "override test"
```

## Project Structure

```
src/
├── OpenClaw.Tray.WinUI/        # WinUI 3 system tray application
│   ├── Services/               # Core services (Gateway, Node, Notification)
│   ├── ViewModels/            # MVVM view models
│   ├── Views/                 # XAML pages and controls
│   ├── Handlers/              # Node capability handlers
│   └── Package.appxmanifest   # MSIX manifest for capabilities
├── OpenClaw.Shared/           # Shared gateway client library
│   ├── GatewayClient.cs       # WebSocket client
│   └── Models/                # DTOs and models
└── OpenClaw.Cli/              # CLI validator
    └── Program.cs             # WebSocket test harness
```

## Key Configuration

### Tray App Settings

Settings are stored in `%APPDATA%\OpenClawTray\settings.json`:

```json
{
  "GatewayUrl": "ws://localhost:18789",
  "GatewayToken": "your-operator-token",
  "NodeMode": true,
  "Notifications": true,
  "AutoStart": true,
  "Theme": "System"
}
```

### Gateway Configuration

For node capabilities, configure `~/.openclaw/openclaw.json`:

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

## Using OpenClaw.Shared Library

### Basic Gateway Client

```csharp
using OpenClaw.Shared;
using OpenClaw.Shared.Models;

// Create client with settings
var settings = new GatewaySettings
{
    Url = "ws://localhost:18789",
    Token = Environment.GetEnvironmentVariable("OPENCLAW_TOKEN"),
    ClientId = "my-app-client"
};

var client = new GatewayClient(settings);

// Connect
await client.ConnectAsync();

// Send chat message
var response = await client.SendChatMessageAsync("Hello, OpenClaw!");
Console.WriteLine($"Response: {response.Content}");

// Listen for notifications
client.OnNotification += (sender, notification) =>
{
    Console.WriteLine($"Notification: {notification.Title}");
    Console.WriteLine($"Body: {notification.Body}");
};

// Disconnect
await client.DisconnectAsync();
```

### Request with Retry

```csharp
using OpenClaw.Shared;
using System.Text.Json;

var client = new GatewayClient(settings);
await client.ConnectAsync();

var request = new JsonRpcRequest
{
    Id = Guid.NewGuid().ToString(),
    Method = "sessions.list",
    Params = new { }
};

var response = await client.SendRequestAsync<SessionsResponse>(
    request,
    maxRetries: 3,
    retryDelayMs: 1000
);

foreach (var session in response.Sessions)
{
    Console.WriteLine($"Session: {session.Id} - {session.Status}");
}
```

### Subscribe to Events

```csharp
var client = new GatewayClient(settings);

// Subscribe to connection state changes
client.OnConnectionStateChanged += (sender, state) =>
{
    Console.WriteLine($"Connection state: {state}");
};

// Subscribe to usage updates
client.OnUsageUpdate += (sender, usage) =>
{
    Console.WriteLine($"Provider: {usage.Provider}");
    Console.WriteLine($"Cost: ${usage.TotalCost:F4}");
};

// Subscribe to node commands (when in node mode)
client.OnNodeCommand += async (sender, command) =>
{
    if (command.Method == "system.notify")
    {
        var notifyParams = JsonSerializer.Deserialize<NotifyParams>(
            command.Params.GetRawText()
        );
        
        // Show notification
        await ShowNotificationAsync(notifyParams.Title, notifyParams.Body);
        
        // Send result
        return new { success = true };
    }
    
    return new { error = "Unknown command" };
};

await client.ConnectAsync();
```

## Adding Node Capabilities

### Create a New Handler

```csharp
using OpenClaw.Tray.WinUI.Handlers;
using OpenClaw.Shared.Models;

namespace OpenClaw.Tray.WinUI.Handlers
{
    public class CustomCapabilityHandler : INodeCommandHandler
    {
        public string[] SupportedMethods => new[] { "custom.doSomething" };

        public async Task<object> HandleAsync(NodeCommand command)
        {
            var parameters = JsonSerializer.Deserialize<CustomParams>(
                command.Params.GetRawText()
            );

            // Your capability logic here
            var result = await PerformCustomActionAsync(parameters);

            return new
            {
                success = true,
                data = result
            };
        }

        private async Task<string> PerformCustomActionAsync(CustomParams parameters)
        {
            // Implementation
            return await Task.FromResult("Custom action completed");
        }
    }

    public class CustomParams
    {
        public string Action { get; set; }
        public Dictionary<string, object> Options { get; set; }
    }
}
```

### Register Handler

In `NodeService.cs`:

```csharp
public class NodeService
{
    private readonly List<INodeCommandHandler> _handlers;

    public NodeService(GatewayClient gatewayClient)
    {
        _handlers = new List<INodeCommandHandler>
        {
            new SystemHandler(),
            new CanvasHandler(),
            new ScreenHandler(),
            new CameraHandler(),
            new CustomCapabilityHandler(), // Add your handler
        };

        gatewayClient.OnNodeCommand += HandleNodeCommandAsync;
    }

    private async Task<object> HandleNodeCommandAsync(object sender, NodeCommand command)
    {
        var handler = _handlers.FirstOrDefault(h => 
            h.SupportedMethods.Contains(command.Method)
        );

        if (handler == null)
        {
            return new { error = $"Unsupported method: {command.Method}" };
        }

        return await handler.HandleAsync(command);
    }
}
```

### Update Package.appxmanifest

If your capability needs Windows permissions:

```xml
<Capabilities>
    <rescap:Capability Name="runFullTrust" />
    <DeviceCapability Name="webcam" />
    <DeviceCapability Name="microphone" />
    <DeviceCapability Name="location" />
    <!-- Add new capability -->
    <DeviceCapability Name="bluetooth" />
</Capabilities>
```

## Notification System

### Send Notifications

```csharp
using OpenClaw.Tray.WinUI.Services;

public class NotificationExample
{
    private readonly NotificationService _notificationService;

    public NotificationExample(NotificationService notificationService)
    {
        _notificationService = notificationService;
    }

    public async Task SendSimpleNotificationAsync()
    {
        var notification = new NotificationRequest
        {
            Title = "Task Complete",
            Body = "Your long-running task has finished",
            Category = "task.complete"
        };

        await _notificationService.ShowAsync(notification);
    }

    public async Task SendInteractiveNotificationAsync()
    {
        var notification = new NotificationRequest
        {
            Title = "Approval Required",
            Body = "Command execution requires approval",
            Category = "security.approval",
            Actions = new[]
            {
                new NotificationAction
                {
                    Label = "Approve",
                    ActionId = "approve",
                    Arguments = "exec-id-123"
                },
                new NotificationAction
                {
                    Label = "Deny",
                    ActionId = "deny",
                    Arguments = "exec-id-123"
                }
            }
        };

        // Handle action response
        _notificationService.OnActionInvoked += (sender, args) =>
        {
            if (args.ActionId == "approve")
            {
                // Approve execution
                ApproveExecution(args.Arguments);
            }
        };

        await _notificationService.ShowAsync(notification);
    }

    private void ApproveExecution(string execId)
    {
        // Implementation
    }
}
```

### Notification Categories

The system uses smart categorization (see `docs/NOTIFICATION_CATEGORIZATION.md`):

```csharp
public static class NotificationCategories
{
    // Session lifecycle
    public const string SessionStarted = "session.started";
    public const string SessionEnded = "session.ended";
    
    // Agent state
    public const string AgentThinking = "agent.thinking";
    public const string AgentResponded = "agent.responded";
    
    // Channel events
    public const string ChannelMessage = "channel.message";
    public const string ChannelConnected = "channel.connected";
    
    // Security
    public const string SecurityApproval = "security.approval";
    public const string SecurityAlert = "security.alert";
    
    // Task completion
    public const string TaskComplete = "task.complete";
    public const string TaskFailed = "task.failed";
}
```

## Canvas API

### Present Canvas Window

```csharp
using OpenClaw.Tray.WinUI.Handlers;

public class CanvasExample
{
    private readonly CanvasHandler _canvasHandler;

    public async Task ShowCustomUIAsync()
    {
        var command = new NodeCommand
        {
            Method = "canvas.present",
            Params = JsonSerializer.SerializeToElement(new
            {
                url = "https://example.com/custom-ui",
                title = "Custom Dashboard",
                width = 1200,
                height = 800
            })
        };

        var result = await _canvasHandler.HandleAsync(command);
    }

    public async Task EvaluateJavaScriptAsync()
    {
        var command = new NodeCommand
        {
            Method = "canvas.eval",
            Params = JsonSerializer.SerializeToElement(new
            {
                script = @"
                    document.body.style.backgroundColor = '#1e1e1e';
                    return { theme: 'dark', ready: true };
                "
            })
        };

        var result = await _canvasHandler.HandleAsync(command);
        Console.WriteLine($"Eval result: {result}");
    }

    public async Task PushA2UIContentAsync()
    {
        var command = new NodeCommand
        {
            Method = "canvas.a2ui.push",
            Params = JsonSerializer.SerializeToElement(new
            {
                type = "message",
                content = new
                {
                    text = "Hello from agent!",
                    timestamp = DateTime.UtcNow
                }
            })
        };

        await _canvasHandler.HandleAsync(command);
    }
}
```

## Screen Capture

### Take Screenshot

```csharp
using OpenClaw.Tray.WinUI.Handlers;

public class ScreenCaptureExample
{
    private readonly ScreenHandler _screenHandler;

    public async Task<string> CaptureScreenAsync()
    {
        var command = new NodeCommand
        {
            Method = "screen.snapshot",
            Params = JsonSerializer.SerializeToElement(new
            {
                format = "png",
                quality = 90
            })
        };

        var result = await _screenHandler.HandleAsync(command);
        var response = JsonSerializer.Deserialize<ScreenshotResponse>(
            JsonSerializer.Serialize(result)
        );

        // result.data contains base64-encoded PNG
        return response.Data;
    }

    public async Task<string> RecordScreenAsync()
    {
        var command = new NodeCommand
        {
            Method = "screen.record",
            Params = JsonSerializer.SerializeToElement(new
            {
                durationSeconds = 10,
                format = "mp4",
                quality = "high"
            })
        };

        var result = await _screenHandler.HandleAsync(command);
        var response = JsonSerializer.Deserialize<RecordingResponse>(
            JsonSerializer.Serialize(result)
        );

        return response.FilePath;
    }
}

public class ScreenshotResponse
{
    public string Data { get; set; }
    public string Format { get; set; }
}

public class RecordingResponse
{
    public string FilePath { get; set; }
    public int DurationSeconds { get; set; }
}
```

## Camera Integration

### List and Capture

```csharp
using OpenClaw.Tray.WinUI.Handlers;

public class CameraExample
{
    private readonly CameraHandler _cameraHandler;

    public async Task<List<CameraDevice>> ListCamerasAsync()
    {
        var command = new NodeCommand
        {
            Method = "camera.list",
            Params = JsonSerializer.SerializeToElement(new { })
        };

        var result = await _cameraHandler.HandleAsync(command);
        var response = JsonSerializer.Deserialize<CameraListResponse>(
            JsonSerializer.Serialize(result)
        );

        return response.Cameras;
    }

    public async Task<string> CapturePhotoAsync(string deviceId = null)
    {
        var command = new NodeCommand
        {
            Method = "camera.snap",
            Params = JsonSerializer.SerializeToElement(new
            {
                deviceId = deviceId, // null = default camera
                format = "jpeg",
                quality = 95
            })
        };

        var result = await _cameraHandler.HandleAsync(command);
        var response = JsonSerializer.Deserialize<CameraSnapResponse>(
            JsonSerializer.Serialize(result)
        );

        return response.Data; // base64
    }

    public async Task<string> CaptureVideoClipAsync(int durationSeconds = 5)
    {
        var command = new NodeCommand
        {
            Method = "camera.clip",
            Params = JsonSerializer.SerializeToElement(new
            {
                durationSeconds = durationSeconds,
                format = "mp4"
            })
        };

        var result = await _cameraHandler.HandleAsync(command);
        var response = JsonSerializer.Deserialize<CameraClipResponse>(
            JsonSerializer.Serialize(result)
        );

        return response.FilePath;
    }
}

public class CameraListResponse
{
    public List<CameraDevice> Cameras { get; set; }
}

public class CameraDevice
{
    public string Id { get; set; }
    public string Name { get; set; }
}

public class CameraSnapResponse
{
    public string Data { get; set; }
    public string Format { get; set; }
}

public class CameraClipResponse
{
    public string FilePath { get; set; }
}
```

## Command Execution with Policy

### System.Run with Approval Flow

```csharp
using OpenClaw.Tray.WinUI.Handlers;

public class SystemCommandExample
{
    private readonly SystemHandler _systemHandler;

    public async Task ExecuteCommandAsync()
    {
        // Step 1: Prepare command (returns policy-based approval requirement)
        var prepareCommand = new NodeCommand
        {
            Method = "system.run.prepare",
            Params = JsonSerializer.SerializeToElement(new
            {
                command = "powershell",
                args = new[] { "-Command", "Get-Process | Select-Object -First 5" },
                workingDirectory = Environment.GetFolderPath(Environment.SpecialFolder.UserProfile)
            })
        };

        var prepareResult = await _systemHandler.HandleAsync(prepareCommand);
        var policy = JsonSerializer.Deserialize<ExecPolicyResponse>(
            JsonSerializer.Serialize(prepareResult)
        );

        if (policy.RequiresApproval)
        {
            // Show approval UI
            bool approved = await RequestUserApprovalAsync(policy);
            if (!approved)
            {
                return;
            }
        }

        // Step 2: Execute command
        var execCommand = new NodeCommand
        {
            Method = "system.run",
            Params = JsonSerializer.SerializeToElement(new
            {
                command = "powershell",
                args = new[] { "-Command", "Get-Process | Select-Object -First 5" },
                approvalId = policy.ApprovalId // if approved
            })
        };

        var execResult = await _systemHandler.HandleAsync(execCommand);
        var response = JsonSerializer.Deserialize<ExecResponse>(
            JsonSerializer.Serialize(execResult)
        );

        Console.WriteLine($"Exit code: {response.ExitCode}");
        Console.WriteLine($"Stdout: {response.Stdout}");
    }

    private async Task<bool> RequestUserApprovalAsync(ExecPolicyResponse policy)
    {
        // Show notification with approve/deny actions
        var notification = new NotificationRequest
        {
            Title = "Command Approval Required",
            Body = $"Execute: {policy.CommandSummary}",
            Category = "security.approval",
            Actions = new[]
            {
                new NotificationAction { Label = "Approve", ActionId = "approve" },
                new NotificationAction { Label = "Deny", ActionId = "deny" }
            }
        };

        var tcs = new TaskCompletionSource<bool>();
        
        _notificationService.OnActionInvoked += (sender, args) =>
        {
            tcs.SetResult(args.ActionId == "approve");
        };

        await _notificationService.ShowAsync(notification);
        return await tcs.Task;
    }
}

public class ExecPolicyResponse
{
    public bool RequiresApproval { get; set; }
    public string ApprovalId { get; set; }
    public string CommandSummary { get; set; }
}

public class ExecResponse
{
    public int ExitCode { get; set; }
    public string Stdout { get; set; }
    public string Stderr { get; set; }
}
```

## Speech-to-Text (STT)

### Transcribe Audio

```csharp
using OpenClaw.Tray.WinUI.Handlers;

public class STTExample
{
    private readonly STTHandler _sttHandler;

    public async Task<string> TranscribeAsync()
    {
        // Requires opt-in via Settings and microphone permission
        var command = new NodeCommand
        {
            Method = "stt.transcribe",
            Params = JsonSerializer.SerializeToElement(new
            {
                durationSeconds = 5,
                language = "en-US"
            })
        };

        var result = await _sttHandler.HandleAsync(command);
        var response = JsonSerializer.Deserialize<TranscribeResponse>(
            JsonSerializer.Serialize(result)
        );

        return response.Text;
    }
}

public class TranscribeResponse
{
    public string Text { get; set; }
    public double Confidence { get; set; }
}
```

## Text-to-Speech (TTS)

### Speak Text

```csharp
using OpenClaw.Tray.WinUI.Handlers;

public class TTSExample
{
    private readonly TTSHandler _ttsHandler;

    public async Task SpeakAsync(string text)
    {
        var command = new NodeCommand
        {
            Method = "tts.speak",
            Params = JsonSerializer.SerializeToElement(new
            {
                text = text,
                voice = "default", // or "elevenlabs" if configured
                rate = 1.0,
                volume = 0.8
            })
        };

        await _ttsHandler.HandleAsync(command);
    }

    public async Task SpeakWithElevenLabsAsync(string text)
    {
        // Requires ELEVENLABS_API_KEY env var
        var command = new NodeCommand
        {
            Method = "tts.speak",
            Params = JsonSerializer.SerializeToElement(new
            {
                text = text,
                voice = "elevenlabs",
                voiceId = "21m00Tcm4TlvDq8ikWAM", // Rachel voice
                stability = 0.5,
                similarityBoost = 0.75
            })
        };

        await _ttsHandler.HandleAsync(command);
    }
}
```

## Quick Send Feature

Quick Send requires `operator.write` scope on the operator token.

### Implement Quick Send

```csharp
using OpenClaw.Tray.WinUI.Services;

public class QuickSendExample
{
    private readonly GatewayClient _gatewayClient;

    public async Task SendQuickMessageAsync(string message)
    {
        try
        {
            var response = await _gatewayClient.SendChatMessageAsync(message);
            
            // Show success notification
            await _notificationService.ShowAsync(new NotificationRequest
            {
                Title = "Message Sent",
                Body = response.Content.Substring(0, 100),
                Category = "agent.responded"
            });
        }
        catch (GatewayException ex) when (ex.Code == "missing_scope")
        {
            // Copy remediation to clipboard
            var remediation = $@"
Quick Send requires operator.write scope.

Your identity:
- Client ID: {_gatewayClient.ClientId}
- Device ID: {ex.Data["deviceId"]}
- Current scopes: {string.Join(", ", (string[])ex.Data["currentScopes"])}

Update your operator token to include 'operator.write' scope.
            ";
            
            Clipboard.SetText(remediation);
            
            await _notificationService.ShowAsync(new NotificationRequest
            {
                Title = "Quick Send Failed",
                Body = "Missing operator.write scope. Remediation copied to clipboard.",
                Category = "security.alert"
            });
        }
    }
}
```

## CLI Validator Usage

### Basic WebSocket Testing

```powershell
# Test connection and send message
dotnet run --project src/OpenClaw.Cli -- --message "test message"

# Verbose output with connection details
dotnet run --project src/OpenClaw.Cli -- --message "test" --verbose

# Loop testing with delays
dotnet run --project src/OpenClaw.Cli -- --repeat 10 --delay-ms 500 --message "loop test"

# Probe gateway APIs (sessions, usage, nodes)
dotnet run --project src/OpenClaw.Cli -- --probe-read --verbose

# Combined: send messages and probe APIs
dotnet run --project src/OpenClaw.Cli -- --repeat 5 --message "combined test" --probe-read
```

### Override Settings

```powershell
# Use different gateway
dotnet run --project src/OpenClaw.Cli -- `
  --url ws://192.168.1.100:18789 `
  --token $env:OPENCLAW_TOKEN `
  --message "remote gateway test"

# Test with custom client ID
dotnet run --project src/OpenClaw.Cli -- `
  --client-id "test-harness-001" `
  --message "custom client"
```

## Troubleshooting

### Connection Issues

```csharp
using OpenClaw.Shared;

public class DiagnosticsHelper
{
    public static async Task<bool> TestConnectionAsync(GatewaySettings settings)
    {
        var client = new GatewayClient(settings);
        
        try
        {
            await client.ConnectAsync();
            Console.WriteLine("✓ Connected to gateway");
            
            // Test basic request
            var response = await client.SendRequestAsync<object>(
                new JsonRpcRequest
                {
                    Id = Guid.NewGuid().ToString(),
                    Method = "device.info",
                    Params = new { }
                }
            );
            
            Console.WriteLine("✓ Device info retrieved");
            return true;
        }
        catch (WebSocketException ex)
        {
            Console.WriteLine($"✗ WebSocket error: {ex.Message}");
            return false;
        }
        catch (GatewayException ex)
        {
            Console.WriteLine($"✗ Gateway error: {ex.Code} - {ex.Message}");
            return false;
        }
        finally
        {
            await client.DisconnectAsync();
        }
    }
}
```

### Pairing Issues

If Quick Send or node commands fail with `NOT_PAIRED`:

```powershell
# On gateway machine, list devices
openclaw devices list

# Find your Windows device (look for hostname)
# Approve it
openclaw devices approve <device-id>
```

### Node Capability Not Working

1. Check gateway `allowCommands` in `~/.openclaw/openclaw.json`
2. Verify device is approved: `openclaw devices list`
3. Check Windows permissions (Settings → Privacy)
4. Review logs: `%APPDATA%\OpenClawTray\logs\`

### WebSocket Disconnects

```csharp
public class ReconnectionHelper
{
    private readonly GatewayClient _client;
    private readonly Timer _reconnectTimer;

    public ReconnectionHelper(GatewayClient client)
    {
        _client = client;
        _reconnectTimer = new Timer(5000); // 5 second retry
        _reconnectTimer.Elapsed += async (s, e) => await TryReconnectAsync();

        _client.OnConnectionStateChanged += (s, state) =>
        {
            if (state == ConnectionState.Disconnected)
            {
                _reconnectTimer.Start();
            }
            else if (state == ConnectionState.Connected)
            {
                _reconnectTimer.Stop();
            }
        };
    }

    private async Task TryReconnectAsync()
    {
        try
        {
            await _client.ConnectAsync();
            Console.WriteLine("Reconnected to gateway");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Reconnection failed: {ex.Message}");
        }
    }
}
```

### Build Errors

```powershell
# Clean build
dotnet clean
Remove-Item -Recurse -Force bin,obj

# Restore packages
dotnet restore

# Build with diagnostics
dotnet build -v diagnostic

# Check SDK version
dotnet --version  # Should be 10.0.x

# Verify Windows SDK
reg query "HKLM\SOFTWARE\Microsoft\Windows Kits\Installed Roots" /v KitsRoot10
```

## Common Patterns

### MVVM ViewModel Example

```csharp
using CommunityToolkit.Mvvm.ComponentModel;
using CommunityToolkit.Mvvm.Input;

namespace OpenClaw.Tray.WinUI.ViewModels
{
    public partial class DashboardViewModel : ObservableObject
    {
        private readonly GatewayClient _gatewayClient;
        
        [ObservableProperty]
        private string _connectionStatus = "Disconnected";
        
        [ObservableProperty]
        private int _sessionCount;
        
        [ObservableProperty]
        private List<SessionInfo> _sessions = new();

        public DashboardViewModel(GatewayClient gatewayClient)
        {
            _gatewayClient = gatewayClient;
            _gatewayClient.OnConnection
