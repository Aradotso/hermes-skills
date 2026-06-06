---
name: openclaw-windows-node
description: Build and extend the OpenClaw Windows companion suite - System Tray app, Node capabilities, CLI tools, and gateway integration
triggers:
  - how do I build the OpenClaw Windows tray app
  - add a new node capability to OpenClaw Windows
  - debug OpenClaw Windows gateway connection
  - create a custom notification in OpenClaw tray
  - extend the OpenClaw Windows command center
  - implement a new channel control in OpenClaw
  - troubleshoot OpenClaw Windows WebSocket issues
  - add MCP tool support to OpenClaw Windows node
---

# OpenClaw Windows Node

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

Windows companion suite for OpenClaw AI assistant. Provides a WinUI 3 system tray app (Molty), shared gateway client library, and CLI utilities. The tray app connects to the OpenClaw gateway via WebSocket, displays status/sessions/usage/channels/nodes, provides Quick Send, embedded Web Chat, toast notifications, and optional Node Mode for agent control of Windows.

## What It Does

- **System Tray App** - WinUI 3 app with flyout menu, gateway status, sessions, channels, usage, nodes, Command Center diagnostics, auto-updates
- **Node Capabilities** - When enabled, exposes Windows as a controllable node: `system.notify`, `system.run`, `canvas.present`, `screen.snapshot`, `camera.snap`, `stt.transcribe`, `tts.speak`, etc.
- **Gateway Client** - Shared C# library for WebSocket connection, pairing, pub/sub, RPC
- **CLI Validator** - Command-line tool to test gateway connectivity and `chat.send`
- **Auto-Updates** - GitHub Releases integration with channel selection (stable/alpha/dev)
- **Deep Links** - `openclaw://` URL scheme with named pipe IPC for external activation

## Installation

### Prerequisites

```powershell
# Windows 10 20H2+ or Windows 11
# .NET 10.0 SDK
winget install Microsoft.DotNet.SDK.10

# Windows 10 SDK (for WinUI build) - via Visual Studio or standalone
# WebView2 Runtime (pre-installed on modern Windows)
```

### Build

```powershell
# Check prerequisites
.\build.ps1 -CheckOnly

# Build all projects
.\build.ps1

# Build specific project
.\build.ps1 -Project WinUI

# Build for ARM64
dotnet build src/OpenClaw.Tray.WinUI/OpenClaw.Tray.WinUI.csproj -r win-arm64

# Build MSIX package (for camera/mic consent prompts)
dotnet build src/OpenClaw.Tray.WinUI -r win-x64 -p:PackageMsix=true
```

### Run Tray App

```powershell
# Build and launch the tray app
.\run-app-local.ps1

# Run without rebuilding
.\run-app-local.ps1 -NoBuild

# Run isolated (separate settings file for testing)
.\run-app-local.ps1 -Isolated

# Test alpha updates from Release build
.\run-app-local.ps1 -Configuration Release -Isolated -UpdateChannel alpha
```

### Run CLI

```powershell
# Show help
dotnet run --project src/OpenClaw.Cli -- --help

# Send message using tray settings
dotnet run --project src/OpenClaw.Cli -- --message "test message"

# Loop sends with probing
dotnet run --project src/OpenClaw.Cli -- --repeat 5 --delay-ms 1000 --probe-read --verbose

# Override gateway URL/token
dotnet run --project src/OpenClaw.Cli -- --url ws://127.0.0.1:18789 --token "$env:OPENCLAW_TOKEN" --message "test"
```

## Project Structure

```
openclaw-windows-node/
├── src/
│   ├── OpenClaw.Tray.WinUI/       # System tray app (WinUI 3)
│   │   ├── Services/              # Gateway, Notification, Update, Node
│   │   ├── ViewModels/            # MVVM view models
│   │   ├── Views/                 # XAML pages and windows
│   │   ├── Capabilities/          # Node capability implementations
│   │   └── Models/                # Data models
│   ├── OpenClaw.Shared/           # Gateway client library
│   │   ├── GatewayClient.cs       # WebSocket client
│   │   ├── Models/                # DTOs for gateway API
│   │   └── Extensions/            # Helper methods
│   └── OpenClaw.Cli/              # CLI validator
└── docs/                          # Documentation
```

## Key Components

### GatewayClient (Shared Library)

The core WebSocket client for OpenClaw gateway integration.

```csharp
using OpenClaw.Shared;
using OpenClaw.Shared.Models;

// Create client
var client = new GatewayClient("ws://127.0.0.1:18789", "your-token-here");

// Handle events
client.MessageReceived += (s, msg) => {
    Console.WriteLine($"Topic: {msg.Topic}, Data: {msg.Data}");
};

client.ConnectionStateChanged += (s, state) => {
    Console.WriteLine($"Connection: {state}");
};

// Connect
await client.ConnectAsync();

// Subscribe to topics
await client.SubscribeAsync(new[] { 
    "gateway.sessions", 
    "gateway.usage", 
    "gateway.channels" 
});

// Send RPC request
var response = await client.SendRequestAsync<ChatSendResponse>(
    "chat.send",
    new { message = "Hello from Windows!", sessionId = "session-123" }
);

// Unsubscribe
await client.UnsubscribeAsync(new[] { "gateway.sessions" });

// Disconnect
await client.DisconnectAsync();
```

### Node Registration and Capabilities

When Node Mode is enabled, the tray app registers as a node and handles capability requests.

```csharp
// In NodeService.cs
public class NodeService
{
    private readonly GatewayClient _gatewayClient;
    private readonly Dictionary<string, INodeCapability> _capabilities;

    public async Task RegisterNodeAsync()
    {
        // Register capabilities
        var capabilities = new[]
        {
            "system.notify",
            "system.run",
            "canvas.present",
            "screen.snapshot",
            "camera.snap",
            "stt.transcribe",
            "tts.speak",
            "location.get",
            "device.info"
        };

        var request = new
        {
            deviceId = GetDeviceId(),
            capabilities = capabilities,
            metadata = new
            {
                platform = "windows",
                version = GetAppVersion(),
                hostname = Environment.MachineName
            }
        };

        await _gatewayClient.SendRequestAsync<object>("node.register", request);
    }

    public async Task HandleNodeRequestAsync(NodeRequest request)
    {
        if (!_capabilities.TryGetValue(request.Method, out var capability))
        {
            throw new NotSupportedException($"Capability not supported: {request.Method}");
        }

        var result = await capability.ExecuteAsync(request.Params);
        
        await _gatewayClient.SendRequestAsync<object>("node.response", new
        {
            requestId = request.Id,
            result = result
        });
    }
}
```

### Implementing a Node Capability

```csharp
// In Capabilities/SystemNotifyCapability.cs
using Windows.UI.Notifications;
using Microsoft.Toolkit.Uwp.Notifications;

public class SystemNotifyCapability : INodeCapability
{
    public string Method => "system.notify";

    public async Task<object> ExecuteAsync(JsonElement? parameters)
    {
        var title = parameters?.GetProperty("title").GetString() ?? "Notification";
        var message = parameters?.GetProperty("message").GetString() ?? "";
        var priority = parameters?.GetProperty("priority").GetString() ?? "normal";

        // Create toast notification
        new ToastContentBuilder()
            .AddText(title)
            .AddText(message)
            .AddArgument("action", "viewConversation")
            .AddArgument("conversationId", Guid.NewGuid().ToString())
            .Show();

        return new { success = true };
    }
}
```

### Screen Snapshot Capability

```csharp
// In Capabilities/ScreenSnapshotCapability.cs
using System.Drawing;
using System.Drawing.Imaging;
using Windows.Graphics.Capture;

public class ScreenSnapshotCapability : INodeCapability
{
    public string Method => "screen.snapshot";

    public async Task<object> ExecuteAsync(JsonElement? parameters)
    {
        var displayIndex = parameters?.GetProperty("display").GetInt32() ?? 0;
        var format = parameters?.GetProperty("format").GetString() ?? "png";
        
        // Capture screen using Windows.Graphics.Capture
        var bounds = Screen.AllScreens[displayIndex].Bounds;
        using var bitmap = new Bitmap(bounds.Width, bounds.Height);
        using var graphics = Graphics.FromImage(bitmap);
        
        graphics.CopyFromScreen(
            bounds.Location, 
            Point.Empty, 
            bounds.Size
        );

        // Convert to base64
        using var ms = new MemoryStream();
        var imageFormat = format.ToLower() switch
        {
            "jpg" => ImageFormat.Jpeg,
            "jpeg" => ImageFormat.Jpeg,
            _ => ImageFormat.Png
        };
        
        bitmap.Save(ms, imageFormat);
        var base64 = Convert.ToBase64String(ms.ToArray());

        return new
        {
            data = base64,
            format = format,
            width = bounds.Width,
            height = bounds.Height
        };
    }
}
```

### Camera Capability

```csharp
// In Capabilities/CameraCapability.cs
using Windows.Media.Capture;
using Windows.Media.MediaProperties;
using Windows.Storage;

public class CameraSnapCapability : INodeCapability
{
    public string Method => "camera.snap";

    public async Task<object> ExecuteAsync(JsonElement? parameters)
    {
        var deviceId = parameters?.GetProperty("deviceId").GetString();
        var format = parameters?.GetProperty("format").GetString() ?? "jpg";

        // Initialize MediaCapture
        var capture = new MediaCapture();
        var settings = new MediaCaptureInitializationSettings();
        
        if (!string.IsNullOrEmpty(deviceId))
        {
            settings.VideoDeviceId = deviceId;
        }

        await capture.InitializeAsync(settings);

        // Capture photo
        var file = await ApplicationData.Current.TemporaryFolder
            .CreateFileAsync($"snap_{Guid.NewGuid()}.{format}");

        var imageEncoding = format.ToLower() == "png" 
            ? ImageEncodingProperties.CreatePng()
            : ImageEncodingProperties.CreateJpeg();

        await capture.CapturePhotoToStorageFileAsync(imageEncoding, file);

        // Read and encode
        var buffer = await FileIO.ReadBufferAsync(file);
        var bytes = buffer.ToArray();
        var base64 = Convert.ToBase64String(bytes);

        // Cleanup
        await file.DeleteAsync();
        capture.Dispose();

        return new
        {
            data = base64,
            format = format
        };
    }
}
```

### STT (Speech-to-Text) Capability

```csharp
// In Capabilities/SttTranscribeCapability.cs
using Windows.Media.Capture;
using Windows.Media.SpeechRecognition;

public class SttTranscribeCapability : INodeCapability
{
    public string Method => "stt.transcribe";

    public async Task<object> ExecuteAsync(JsonElement? parameters)
    {
        var durationSec = parameters?.GetProperty("durationSec").GetDouble() ?? 5.0;
        var language = parameters?.GetProperty("language").GetString() ?? "en-US";

        // Check if STT is enabled in settings
        if (!SettingsService.Current.IsSttEnabled)
        {
            throw new InvalidOperationException("STT is disabled in settings");
        }

        // Initialize speech recognizer
        var recognizer = new SpeechRecognizer(new Windows.Globalization.Language(language));
        await recognizer.CompileConstraintsAsync();

        // Start continuous recognition
        var tcs = new TaskCompletionSource<string>();
        var recognizedText = new StringBuilder();

        recognizer.ContinuousRecognitionSession.ResultGenerated += (s, e) =>
        {
            if (e.Result.Status == SpeechRecognitionResultStatus.Success)
            {
                recognizedText.AppendLine(e.Result.Text);
            }
        };

        await recognizer.ContinuousRecognitionSession.StartAsync();
        await Task.Delay(TimeSpan.FromSeconds(durationSec));
        await recognizer.ContinuousRecognitionSession.StopAsync();

        recognizer.Dispose();

        return new
        {
            text = recognizedText.ToString().Trim(),
            durationSec = durationSec,
            language = language
        };
    }
}
```

### TTS (Text-to-Speech) Capability

```csharp
// In Capabilities/TtsSpeakCapability.cs
using Windows.Media.SpeechSynthesis;
using Windows.Media.Playback;

public class TtsSpeakCapability : INodeCapability
{
    public string Method => "tts.speak";

    public async Task<object> ExecuteAsync(JsonElement? parameters)
    {
        var text = parameters?.GetProperty("text").GetString() 
            ?? throw new ArgumentException("text is required");
        var voice = parameters?.GetProperty("voice").GetString();
        var provider = parameters?.GetProperty("provider").GetString() ?? "system";

        if (provider == "elevenlabs")
        {
            return await SpeakWithElevenLabsAsync(text, voice);
        }

        // Use Windows speech synthesis
        var synthesizer = new SpeechSynthesizer();
        
        if (!string.IsNullOrEmpty(voice))
        {
            var selectedVoice = SpeechSynthesizer.AllVoices
                .FirstOrDefault(v => v.DisplayName == voice);
            if (selectedVoice != null)
            {
                synthesizer.Voice = selectedVoice;
            }
        }

        var stream = await synthesizer.SynthesizeTextToStreamAsync(text);
        
        var player = new MediaPlayer();
        player.Source = Windows.Media.Core.MediaSource.CreateFromStream(
            stream, 
            stream.ContentType
        );
        player.Play();

        // Wait for playback to complete
        var tcs = new TaskCompletionSource<bool>();
        player.MediaEnded += (s, e) => tcs.SetResult(true);
        await tcs.Task;

        return new { success = true };
    }

    private async Task<object> SpeakWithElevenLabsAsync(string text, string voice)
    {
        var apiKey = Environment.GetEnvironmentVariable("ELEVENLABS_API_KEY")
            ?? throw new InvalidOperationException("ELEVENLABS_API_KEY not set");

        var voiceId = voice ?? "21m00Tcm4TlvDq8ikWAM"; // Default voice

        using var httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Add("xi-api-key", apiKey);

        var request = new
        {
            text = text,
            model_id = "eleven_monolingual_v1",
            voice_settings = new
            {
                stability = 0.5,
                similarity_boost = 0.75
            }
        };

        var response = await httpClient.PostAsJsonAsync(
            $"https://api.elevenlabs.io/v1/text-to-speech/{voiceId}",
            request
        );

        response.EnsureSuccessStatusCode();
        var audioBytes = await response.Content.ReadAsByteArrayAsync();

        // Play audio
        var tempFile = Path.GetTempFileName() + ".mp3";
        await File.WriteAllBytesAsync(tempFile, audioBytes);

        var player = new MediaPlayer();
        player.Source = Windows.Media.Core.MediaSource.CreateFromUri(new Uri(tempFile));
        player.Play();

        var tcs = new TaskCompletionSource<bool>();
        player.MediaEnded += (s, e) => tcs.SetResult(true);
        await tcs.Task;

        File.Delete(tempFile);

        return new { success = true };
    }
}
```

### Canvas Capability

```csharp
// In Capabilities/CanvasPresentCapability.cs
using Microsoft.UI.Xaml.Controls;

public class CanvasPresentCapability : INodeCapability
{
    public string Method => "canvas.present";
    private WebView2 _canvasWebView;

    public async Task<object> ExecuteAsync(JsonElement? parameters)
    {
        var url = parameters?.GetProperty("url").GetString();
        var html = parameters?.GetProperty("html").GetString();
        var width = parameters?.GetProperty("width").GetInt32() ?? 800;
        var height = parameters?.GetProperty("height").GetInt32() ?? 600;

        // Show canvas window on UI thread
        await App.MainWindow.DispatcherQueue.EnqueueAsync(async () =>
        {
            if (_canvasWebView == null)
            {
                var window = new CanvasWindow
                {
                    Width = width,
                    Height = height
                };
                _canvasWebView = window.WebView;
                window.Activate();
            }

            if (!string.IsNullOrEmpty(url))
            {
                _canvasWebView.Source = new Uri(url);
            }
            else if (!string.IsNullOrEmpty(html))
            {
                _canvasWebView.NavigateToString(html);
            }
        });

        return new { success = true };
    }
}
```

## Notification Service

Handles Windows toast notifications with smart categorization.

```csharp
using OpenClaw.Tray.WinUI.Services;
using Windows.UI.Notifications;

// Show notification
await notificationService.ShowNotificationAsync(
    "New Message",
    "You have a new message from the agent",
    category: NotificationCategory.AgentMessage
);

// Handle notification click
ToastNotificationManagerCompat.OnActivated += async (toastArgs) =>
{
    var args = ToastArguments.Parse(toastArgs.Argument);
    if (args.Contains("action"))
    {
        var action = args["action"];
        // Handle action
    }
};
```

### Notification Categories

```csharp
public enum NotificationCategory
{
    AgentMessage,      // Standard agent responses
    ToolExecution,     // Tool/command execution
    ErrorWarning,      // Errors and warnings
    SystemEvent,       // System-level events
    ChannelActivity    // Channel updates (Telegram, WhatsApp)
}
```

## Auto-Update Service

Implements GitHub Releases-based auto-updates with channel selection.

```csharp
using OpenClaw.Tray.WinUI.Services;

var updateService = new UpdateService();

// Check for updates
var hasUpdate = await updateService.CheckForUpdatesAsync();
if (hasUpdate)
{
    var updateInfo = updateService.LatestVersion;
    Console.WriteLine($"Update available: {updateInfo.Version}");
    
    // Download and install
    await updateService.DownloadAndInstallAsync();
}

// Change update channel
updateService.SetUpdateChannel(UpdateChannel.Alpha);
```

### Update Channels

```csharp
public enum UpdateChannel
{
    Stable,  // Production releases (default)
    Alpha,   // Pre-release builds
    Dev      // Development builds
}
```

## Deep Links

The tray app registers `openclaw://` URL scheme for external activation.

```csharp
// From external app or browser:
// openclaw://action/quicksend?message=Hello

// In DeepLinkService.cs
public class DeepLinkService
{
    public void RegisterProtocol()
    {
        // Register protocol in registry
        var key = Registry.CurrentUser.CreateSubKey(@"Software\Classes\openclaw");
        key.SetValue("", "URL:OpenClaw Protocol");
        key.SetValue("URL Protocol", "");
        // ... set command
    }

    public async Task HandleDeepLinkAsync(string uri)
    {
        var parsed = new Uri(uri);
        var action = parsed.Host; // "action"
        var path = parsed.LocalPath.TrimStart('/'); // "quicksend"
        var query = HttpUtility.ParseQueryString(parsed.Query);

        if (path == "quicksend" && query["message"] != null)
        {
            await quickSendService.SendMessageAsync(query["message"]);
        }
    }
}
```

## CLI Usage

### Basic Commands

```powershell
# Send a message using tray settings
dotnet run --project src/OpenClaw.Cli -- --message "Hello agent"

# Loop with probing
dotnet run --project src/OpenClaw.Cli -- `
    --repeat 10 `
    --delay-ms 2000 `
    --probe-read `
    --verbose

# Override connection
dotnet run --project src/OpenClaw.Cli -- `
    --url ws://192.168.1.100:18789 `
    --token "$env:OPENCLAW_TOKEN" `
    --message "Remote test"
```

### CLI Arguments

```
--url <url>          Gateway WebSocket URL
--token <token>      Bearer token
--message <msg>      Message to send
--repeat <n>         Number of sends (default: 1)
--delay-ms <ms>      Delay between sends (default: 1000)
--probe-read         Query sessions/usage/nodes APIs
--verbose            Verbose logging
```

## Configuration

Settings are stored in `%APPDATA%\OpenClawTray\settings.json`:

```json
{
  "GatewayUrl": "ws://127.0.0.1:18789",
  "BearerToken": "your-token-here",
  "EnableNotifications": true,
  "StartWithWindows": true,
  "NodeMode": {
    "Enabled": true,
    "DeviceId": "win-desktop-abc123",
    "AllowedCapabilities": [
      "system.notify",
      "system.run",
      "canvas.present",
      "screen.snapshot"
    ]
  },
  "UpdateChannel": "Stable",
  "Theme": "System",
  "QuickSendHotkey": "Ctrl+Alt+Shift+C"
}
```

### Load Settings

```csharp
using OpenClaw.Tray.WinUI.Services;

var settings = SettingsService.Current;
var gatewayUrl = settings.GatewayUrl;
var token = settings.BearerToken;

// Update settings
settings.GatewayUrl = "ws://new-url:18789";
settings.EnableNotifications = true;
await settings.SaveAsync();
```

## Common Patterns

### Subscribe to Gateway Events

```csharp
var client = new GatewayClient(gatewayUrl, token);

client.MessageReceived += async (s, msg) =>
{
    switch (msg.Topic)
    {
        case "gateway.sessions":
            var sessions = JsonSerializer.Deserialize<Session[]>(msg.Data);
            await UpdateSessionsUIAsync(sessions);
            break;
            
        case "gateway.usage":
            var usage = JsonSerializer.Deserialize<UsageUpdate>(msg.Data);
            await UpdateUsageUIAsync(usage);
            break;
            
        case "gateway.notifications":
            var notification = JsonSerializer.Deserialize<Notification>(msg.Data);
            await ShowNotificationAsync(notification);
            break;
    }
};

await client.ConnectAsync();
await client.SubscribeAsync(new[] { 
    "gateway.sessions", 
    "gateway.usage",
    "gateway.notifications"
});
```

### Handle Pairing Flow

```csharp
// When node mode is enabled
var nodeService = new NodeService(gatewayClient);

try
{
    await nodeService.RegisterNodeAsync();
}
catch (UnauthorizedException)
{
    // Device not approved yet
    Console.WriteLine("Pairing required. Approve device on gateway:");
    Console.WriteLine($"openclaw devices list");
    Console.WriteLine($"openclaw devices approve {nodeService.DeviceId}");
    
    // Show pairing UI
    await ShowPairingDialogAsync();
}
```

### Send Chat Message (Quick Send)

```csharp
public async Task SendQuickMessageAsync(string message)
{
    try
    {
        var response = await _gatewayClient.SendRequestAsync<ChatSendResponse>(
            "chat.send",
            new
            {
                message = message,
                sessionId = _currentSessionId ?? await GetOrCreateSessionIdAsync()
            }
        );

        if (response?.Success == true)
        {
            await ShowNotificationAsync("Message sent", message);
        }
    }
    catch (ScopeException ex)
    {
        // Missing operator.write scope
        var remediation = $@"
Error: {ex.Message}

Your operator token needs 'operator.write' scope.

Identity:
- Role: {ex.OperatorRole}
- Client ID: {ex.ClientId}
- Device ID: {ex.DeviceId}
- Current Scopes: {string.Join(", ", ex.CurrentScopes)}

Fix: Update your token in Settings to include operator.write scope.
";
        Clipboard.SetText(remediation);
        await ShowErrorDialogAsync("Scope Error", remediation);
    }
}
```

### Control Channels

```csharp
// Start Telegram channel
await _gatewayClient.SendRequestAsync<object>(
    "channels.telegram.start",
    new { }
);

// Stop WhatsApp channel
await _gatewayClient.SendRequestAsync<object>(
    "channels.whatsapp.stop",
    new { }
);

// Get channel status
var status = await _gatewayClient.SendRequestAsync<ChannelStatus>(
    "channels.status",
    new { channel = "telegram" }
);
```

## Troubleshooting

### Connection Issues

```csharp
// Enable verbose logging
var client = new GatewayClient(url, token)
{
    LogLevel = LogLevel.Debug
};

client.ConnectionStateChanged += (s, state) =>
{
    Console.WriteLine($"State: {state}");
    if (state == ConnectionState.Disconnected)
    {
        // Check gateway is running
        // Check token is valid
        // Check firewall settings
    }
};
```

### WebSocket Handshake Errors

```powershell
# Test connection with CLI
dotnet run --project src/OpenClaw.Cli -- `
    --url ws://127.0.0.1:18789 `
    --token "$env:OPENCLAW_TOKEN" `
    --verbose

# Common issues:
# - Gateway not running (check `openclaw status`)
# - Wrong port (default 18789)
# - Invalid token (regenerate with `openclaw tokens create`)
# - Firewall blocking WebSocket
```

### Node Registration Fails

```bash
# On gateway, check pairing approvals
openclaw devices list

# Approve the device
openclaw devices approve <device-id>

# Check gateway allowCommands in ~/.openclaw/openclaw.json
cat ~/.openclaw/openclaw.json | jq '.gateway.nodes.allowCommands'
```

### Quick Send Scope Error

```
Error: missing scope: operator.write
```

**Fix**: Update the bearer token in Settings to include `operator.write` scope.

```bash
# On gateway, create new token with operator.write
openclaw tokens create --scopes operator.read,operator.write
```

### MSIX Package Build Errors

```powershell
# Ensure Windows SDK is installed
Get-WindowsCapability -Online | Where-Object Name -like 'Tools.SDK*'

# Install if missing
Add-WindowsCapability -Online -Name 'Tools.SDK~~~~'

# Build with explicit SDK version
dotnet build -r win-x64 -p:PackageMsix=true -p:WindowsSdkVersion=10.0.22621.0
```

### WebView2 Not Found

```powershell
# Check WebView2 Runtime
Get-AppxPackage -Name "Microsoft.WebView2"

# Install if missing
winget install Microsoft.EdgeWebView2Runtime

# Or download from:
# https://developer.microsoft.com/microsoft-edge/webview2
```

### Camera/Microphone Permission Denied

For packaged (MSIX) builds, Windows requires capability declarations. The `Package.appxmanifest` should include:

```xml
<Capabilities>
  <DeviceCapability Name="webcam" />
  <DeviceCapability Name="microphone" />
  <DeviceCapability Name="location" />
</Capabilities>
```

If permission is denied:
1. Check Settings → Privacy → Camera/Microphone
2. Ensure OpenClaw is allowed
3. For unpackaged builds, some APIs may require elevation

## Testing

### Unit Tests

```powershell
# Run all tests
dotnet test

# Run specific test
dotnet test --filter "FullyQualifiedName~GatewayClientTests.ConnectAsync_Success"

# With coverage
dotnet test --collect:"XPlat Code Coverage"
```

### Integration Tests

```csharp
// Test gateway connection
[TestMethod]
public async Task TestGatewayConnection()
{
    var client = new GatewayClient(
        Environment.GetEnvironmentVariable("OPENCLAW_GATEWAY_URL"),
        Environment.GetEnvironmentVariable("OPENCLAW_TOKEN")
    );

    await client.ConnectAsync();
    Assert.AreEqual(ConnectionState.Connected, client.State);

    await client.DisconnectAsync();
}
```

## Best Practices

1. **Always validate tokens** - Check token format and scopes before connecting
2. **Handle reconnection** - Implement exponential backoff for WebSocket reconnection
3. **Dispose resources** - Use `using` statements for `GatewayClient`, `MediaCapture`, etc.
4. **UI thread safety** - Use `DispatcherQueue` for UI updates from background events
5. **Error handling** - Catch and log WebSocket, pairing, and capability errors
6. **Settings persistence** - Always save settings after updates
7. **Capability approval** - Respect gateway `allowCommands` allowlist
8. **Secure storage** - Never hardcode tokens; use environment variables or secure storage
