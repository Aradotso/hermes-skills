```markdown
---
name: openclaw-termux-android
description: Run OpenClaw AI Gateway on Android via Flutter app or Termux CLI with proot Ubuntu, built-in terminal, web dashboard, and device node capabilities
triggers:
  - how do i run openclaw on android
  - set up openclaw gateway on my phone
  - openclaw termux flutter app installation
  - configure openclaw android node device capabilities
  - run openclaw ai gateway in termux
  - openclaw android ssh remote access setup
  - troubleshoot openclaw flutter app battery optimization
  - install tweetclaw plugin in openclaw termux
---

# OpenClaw Termux Android

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

OpenClaw Termux brings the OpenClaw AI gateway to Android devices without root. It provides two deployment modes: a standalone Flutter app with built-in terminal, web dashboard, and one-tap setup, or a Termux CLI package that installs proot-distro + Ubuntu + Node.js 22 + OpenClaw. The Flutter app exposes Android hardware (camera, location, sensors, flash, haptics, screen recording) to the AI gateway via WebSocket node protocol with 15 commands across 7 capabilities.

## Installation

### Flutter App (Recommended)

Download the latest APK from releases:

```bash
# Via browser
https://github.com/mithun50/openclaw-termux/releases/latest

# Or build from source
git clone https://github.com/mithun50/openclaw-termux.git
cd openclaw-termux/flutter_app
flutter build apk --release
# APK location: build/app/outputs/flutter-apk/app-release.apk
```

Install the APK on Android 10+ device, open the app, and tap **Begin Setup**. The app downloads ~500MB (Ubuntu rootfs + Node.js) automatically.

### Termux CLI

Install Termux from [F-Droid](https://f-droid.org/packages/com.termux/) (NOT Google Play), then:

```bash
# One-liner installation
curl -fsSL https://raw.githubusercontent.com/mithun50/openclaw-termux/main/install.sh | bash

# Or via npm
npm install -g openclaw-termux
openclawx setup
```

## Key Commands

### Flutter App Operations

All operations are available via the app UI:

- **Setup**: Tap "Begin Setup" on first launch
- **Start Gateway**: Dashboard → "Start Gateway" button
- **Configure API Keys**: Dashboard → "Onboarding" or Settings
- **View Logs**: Dashboard → "Logs" tab (real-time with search/filter)
- **Web Dashboard**: Dashboard → "Web" tab (embedded WebView)
- **Terminal**: Dashboard → "Terminal" tab (full emulator with copy/paste)
- **SSH Server**: Settings → SSH section → "Start SSH Server"
- **Optional Packages**: Settings → "Install Go/Homebrew/OpenSSH"
- **Node Capabilities**: Settings → "Enable Node Device" (requires permissions)

### Termux CLI Commands

```bash
# First-time setup (installs everything)
openclawx setup

# Check installation status
openclawx status

# Start OpenClaw gateway
openclawx start

# Run onboarding (configure API keys)
openclawx onboarding

# Enter Ubuntu proot shell
openclawx shell

# Run openclaw configure interactively
openclawx configure

# Check gateway health
openclawx doctor

# View gateway logs
openclawx logs

# Stop gateway
openclawx stop

# Any native OpenClaw command works
openclawx gateway --verbose
openclawx plugins list
openclawx skills info
```

## Configuration

### API Keys Setup (Flutter App)

1. Dashboard → **Onboarding** button
2. Select provider (Anthropic, OpenAI, Google Gemini, OpenRouter, NVIDIA NIM, DeepSeek, xAI)
3. Enter API key (stored in `/data/user/0/com.example.openclaw_flutter/app_flutter/.openclaw/config.json`)
4. Select default model
5. Optionally configure custom binding (default: `0.0.0.0:18789`)

### API Keys Setup (Termux CLI)

```bash
# Interactive onboarding
openclawx onboarding

# Or manually edit config
openclawx shell
cd ~/.openclaw
nano config.json
```

Example `config.json`:

```json
{
  "anthropic": {
    "apiKey": "sk-ant-..."
  },
  "openai": {
    "apiKey": "sk-..."
  },
  "defaultProvider": "anthropic",
  "defaultModel": "claude-sonnet-4-20250514",
  "server": {
    "bind": "0.0.0.0",
    "port": 18789
  }
}
```

### Node Device Capabilities (Flutter App Only)

Enable device node in Settings → "Enable Node Device". The app automatically patches `openclaw.json` to allow all 15 commands:

| Capability | Commands | Permission Required |
|------------|----------|---------------------|
| Camera | `camera.snap`, `camera.clip`, `camera.list` | Camera |
| Canvas | `canvas.navigate`, `canvas.eval`, `canvas.snapshot` | None (not implemented) |
| Flash | `flash.on`, `flash.off`, `flash.toggle`, `flash.status` | Camera (torch) |
| Location | `location.get` | Location (coarse + fine) |
| Screen | `screen.record` | MediaProjection consent dialog |
| Sensor | `sensor.read`, `sensor.list` | Body Sensors |
| Haptic | `haptic.vibrate` | None |

The node connects to `ws://127.0.0.1:18789/nodes/gateway` and identifies as device ID from `Settings.Secure.ANDROID_ID`.

### SSH Remote Access (Flutter App)

1. Settings → SSH section → "Start SSH Server"
2. Set root password (default: `rootpass`)
3. Copy connection command: `ssh root@<device-ip> -p 2222`
4. Connect from PC on same network

```bash
# From your PC
ssh root@192.168.1.100 -p 2222
# Password: rootpass (or your custom password)

# Inside SSH session
cd ~/.openclaw
openclaw doctor
openclaw gateway --verbose
```

## Code Examples

### Dart: Starting Gateway with Health Check

```dart
import 'package:flutter/material.dart';
import 'package:openclaw_flutter/services/gateway_service.dart';

class GatewayController extends StatefulWidget {
  @override
  _GatewayControllerState createState() => _GatewayControllerState();
}

class _GatewayControllerState extends State<GatewayController> {
  final GatewayService _gatewayService = GatewayService();
  bool _isRunning = false;
  bool _isHealthy = false;

  Future<void> _startGateway() async {
    setState(() => _isRunning = true);
    
    final result = await _gatewayService.startGateway();
    
    if (result['success']) {
      // Wait for gateway to be ready
      await Future.delayed(Duration(seconds: 3));
      
      // Check health
      final health = await _gatewayService.checkHealth();
      setState(() {
        _isHealthy = health['healthy'] ?? false;
      });
      
      if (_isHealthy) {
        ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(content: Text('Gateway started successfully')),
        );
      }
    } else {
      setState(() => _isRunning = false);
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('Failed to start gateway: ${result['error']}')),
      );
    }
  }

  Future<void> _stopGateway() async {
    await _gatewayService.stopGateway();
    setState(() {
      _isRunning = false;
      _isHealthy = false;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text(_isHealthy ? 'Gateway Running ✓' : 'Gateway Stopped'),
        ElevatedButton(
          onPressed: _isRunning ? _stopGateway : _startGateway,
          child: Text(_isRunning ? 'Stop Gateway' : 'Start Gateway'),
        ),
      ],
    );
  }
}
```

### Dart: Node Device Camera Capability

```dart
import 'dart:convert';
import 'dart:io';
import 'package:camera/camera.dart';
import 'package:permission_handler/permission_handler.dart';

class CameraCapability {
  CameraController? _controller;
  List<CameraDescription> _cameras = [];

  Future<void> initialize() async {
    // Request camera permission
    final status = await Permission.camera.request();
    if (!status.isGranted) {
      throw Exception('Camera permission denied');
    }
    
    _cameras = await availableCameras();
  }

  Future<Map<String, dynamic>> handleCommand(String command, Map<String, dynamic> params) async {
    switch (command) {
      case 'camera.snap':
        return await _snapPhoto(params);
      case 'camera.clip':
        return await _recordVideo(params);
      case 'camera.list':
        return _listCameras();
      default:
        return {'error': 'Unknown camera command: $command'};
    }
  }

  Future<Map<String, dynamic>> _snapPhoto(Map<String, dynamic> params) async {
    final facing = params['facing'] ?? 'back';
    final camera = _cameras.firstWhere(
      (c) => c.lensDirection == (facing == 'back' ? CameraLensDirection.back : CameraLensDirection.front),
      orElse: () => _cameras.first,
    );

    _controller = CameraController(camera, ResolutionPreset.high);
    await _controller!.initialize();

    final image = await _controller!.takePicture();
    await _controller!.dispose();

    return {
      'success': true,
      'path': image.path,
      'timestamp': DateTime.now().toIso8601String(),
    };
  }

  Future<Map<String, dynamic>> _recordVideo(Map<String, dynamic> params) async {
    final duration = params['duration'] ?? 5; // seconds
    final facing = params['facing'] ?? 'back';
    
    final camera = _cameras.firstWhere(
      (c) => c.lensDirection == (facing == 'back' ? CameraLensDirection.back : CameraLensDirection.front),
      orElse: () => _cameras.first,
    );

    _controller = CameraController(camera, ResolutionPreset.high);
    await _controller!.initialize();
    await _controller!.startVideoRecording();

    await Future.delayed(Duration(seconds: duration));

    final video = await _controller!.stopVideoRecording();
    await _controller!.dispose();

    return {
      'success': true,
      'path': video.path,
      'duration': duration,
      'timestamp': DateTime.now().toIso8601String(),
    };
  }

  Map<String, dynamic> _listCameras() {
    return {
      'cameras': _cameras.map((c) => {
        'name': c.name,
        'facing': c.lensDirection.toString().split('.').last,
        'sensorOrientation': c.sensorOrientation,
      }).toList(),
    };
  }
}
```

### Kotlin: PRoot Terminal Execution

```kotlin
// android/app/src/main/kotlin/com/example/openclaw_flutter/services/ProotManager.kt
package com.example.openclaw_flutter.services

import android.content.Context
import java.io.File
import java.io.BufferedReader
import java.io.InputStreamReader

class ProotManager(private val context: Context) {
    private val filesDir = context.filesDir
    private val prootPath = "$filesDir/proot-static/proot"
    private val ubuntuRoot = "$filesDir/ubuntu-rootfs"

    fun executeCommand(command: String): ProcessResult {
        val prootCmd = listOf(
            prootPath,
            "--rootfs=$ubuntuRoot",
            "--bind=/dev",
            "--bind=/proc",
            "--bind=/sys",
            "--cwd=/root",
            "/bin/bash", "-c", command
        )

        val processBuilder = ProcessBuilder(prootCmd)
        processBuilder.environment().apply {
            put("HOME", "/root")
            put("PATH", "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin")
            put("TERM", "xterm-256color")
        }

        val process = processBuilder.start()
        val output = StringBuilder()
        val error = StringBuilder()

        // Read stdout
        BufferedReader(InputStreamReader(process.inputStream)).use { reader ->
            reader.forEachLine { output.append(it).append("\n") }
        }

        // Read stderr
        BufferedReader(InputStreamReader(process.errorStream)).use { reader ->
            reader.forEachLine { error.append(it).append("\n") }
        }

        val exitCode = process.waitFor()

        return ProcessResult(
            exitCode = exitCode,
            stdout = output.toString(),
            stderr = error.toString()
        )
    }

    fun startGateway(): ProcessResult {
        return executeCommand("cd ~/.openclaw && openclaw gateway --daemon")
    }

    fun stopGateway(): ProcessResult {
        return executeCommand("pkill -f 'openclaw gateway'")
    }

    fun checkGatewayStatus(): Boolean {
        val result = executeCommand("pgrep -f 'openclaw gateway'")
        return result.exitCode == 0 && result.stdout.isNotBlank()
    }
}

data class ProcessResult(
    val exitCode: Int,
    val stdout: String,
    val stderr: String
)
```

### Bash: Plugin Installation (TweetClaw)

```bash
#!/bin/bash
# Install TweetClaw plugin for X/Twitter workflows

# Enter proot environment
openclawx shell

# Install plugin
openclaw plugins install @xquik/tweetclaw

# Verify installation
openclaw plugins inspect tweetclaw --runtime

# Check available skills
openclaw skills info tweetclaw

# Configure credentials (use env vars, not hardcoded)
cat > ~/.openclaw/tweetclaw-credentials.json <<EOF
{
  "accounts": {
    "main": {
      "username": "${TWITTER_USERNAME}",
      "password": "${TWITTER_PASSWORD}",
      "email": "${TWITTER_EMAIL}",
      "authToken": "${TWITTER_AUTH_TOKEN}",
      "ct0": "${TWITTER_CT0}"
    }
  }
}
EOF

# Test with a safe operation (scrape own profile)
openclaw run tweetclaw scrape-tweets --username="${TWITTER_USERNAME}" --limit=10
```

## Common Patterns

### Pattern 1: Gateway Lifecycle Management

```dart
// lib/services/gateway_lifecycle.dart
import 'dart:async';
import 'package:openclaw_flutter/services/gateway_service.dart';
import 'package:openclaw_flutter/services/foreground_service.dart';

class GatewayLifecycle {
  final GatewayService _gateway = GatewayService();
  final ForegroundService _foreground = ForegroundService();
  Timer? _healthCheckTimer;

  Future<void> start({bool background = false}) async {
    // Start gateway process
    final result = await _gateway.startGateway();
    if (!result['success']) {
      throw Exception('Failed to start gateway: ${result['error']}');
    }

    // Wait for gateway to be ready
    await _waitForReady(timeout: Duration(seconds: 30));

    // Start foreground service if background mode
    if (background) {
      await _foreground.start();
    }

    // Start health check timer
    _startHealthCheck();
  }

  Future<void> stop() async {
    _healthCheckTimer?.cancel();
    await _foreground.stop();
    await _gateway.stopGateway();
  }

  Future<void> _waitForReady({required Duration timeout}) async {
    final deadline = DateTime.now().add(timeout);
    while (DateTime.now().isBefore(deadline)) {
      final health = await _gateway.checkHealth();
      if (health['healthy'] == true) {
        return;
      }
      await Future.delayed(Duration(seconds: 2));
    }
    throw TimeoutException('Gateway did not become ready in ${timeout.inSeconds}s');
  }

  void _startHealthCheck() {
    _healthCheckTimer = Timer.periodic(Duration(seconds: 30), (timer) async {
      final health = await _gateway.checkHealth();
      if (health['healthy'] != true) {
        // Attempt restart
        await stop();
        await start(background: true);
      }
    });
  }
}
```

### Pattern 2: Setup Progress Notification

```kotlin
// android/app/src/main/kotlin/com/example/openclaw_flutter/services/SetupService.kt
package com.example.openclaw_flutter.services

import android.app.NotificationChannel
import android.app.NotificationManager
import android.content.Context
import androidx.core.app.NotificationCompat
import com.example.openclaw_flutter.R

class SetupService(private val context: Context) {
    private val notificationManager = context.getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
    private val channelId = "setup_progress"
    private val notificationId = 1001

    init {
        createNotificationChannel()
    }

    private fun createNotificationChannel() {
        val channel = NotificationChannel(
            channelId,
            "Setup Progress",
            NotificationManager.IMPORTANCE_LOW
        ).apply {
            description = "Shows OpenClaw setup progress"
        }
        notificationManager.createNotificationChannel(channel)
    }

    fun showProgress(title: String, progress: Int, max: Int = 100) {
        val notification = NotificationCompat.Builder(context, channelId)
            .setContentTitle(title)
            .setSmallIcon(R.drawable.ic_launcher_foreground)
            .setProgress(max, progress, false)
            .setOngoing(true)
            .build()

        notificationManager.notify(notificationId, notification)
    }

    fun showComplete(title: String, message: String) {
        val notification = NotificationCompat.Builder(context, channelId)
            .setContentTitle(title)
            .setContentText(message)
            .setSmallIcon(R.drawable.ic_launcher_foreground)
            .setAutoCancel(true)
            .build()

        notificationManager.notify(notificationId, notification)
    }

    fun dismiss() {
        notificationManager.cancel(notificationId)
    }
}
```

### Pattern 3: WebSocket Node Protocol

```dart
// lib/services/node_device_service.dart
import 'dart:convert';
import 'package:web_socket_channel/web_socket_channel.dart';
import 'package:device_info_plus/device_info_plus.dart';

class NodeDeviceService {
  WebSocketChannel? _channel;
  String? _deviceId;
  final Map<String, Function> _capabilities = {};

  Future<void> connect(String gatewayUrl) async {
    // Get device ID
    final deviceInfo = DeviceInfoPlugin();
    final androidInfo = await deviceInfo.androidInfo;
    _deviceId = androidInfo.id;

    // Connect to gateway
    final wsUrl = gatewayUrl.replaceFirst('http://', 'ws://').replaceFirst('https://', 'wss://');
    _channel = WebSocketChannel.connect(Uri.parse('$wsUrl/nodes/gateway'));

    // Send registration
    _send({
      'type': 'register',
      'deviceId': _deviceId,
      'capabilities': _capabilities.keys.toList(),
      'metadata': {
        'platform': 'android',
        'model': '${androidInfo.manufacturer} ${androidInfo.model}',
        'version': androidInfo.version.release,
      },
    });

    // Listen for commands
    _channel!.stream.listen((message) {
      final data = jsonDecode(message);
      _handleCommand(data);
    });
  }

  void registerCapability(String name, Function handler) {
    _capabilities[name] = handler;
  }

  Future<void> _handleCommand(Map<String, dynamic> data) async {
    final command = data['command'];
    final params = data['params'] ?? {};
    final requestId = data['requestId'];

    // Extract capability from command (e.g., 'camera.snap' -> 'camera')
    final parts = command.split('.');
    final capability = parts.first;

    if (_capabilities.containsKey(capability)) {
      try {
        final result = await _capabilities[capability]!(command, params);
        _send({
          'type': 'response',
          'requestId': requestId,
          'success': true,
          'data': result,
        });
      } catch (e) {
        _send({
          'type': 'response',
          'requestId': requestId,
          'success': false,
          'error': e.toString(),
        });
      }
    } else {
      _send({
        'type': 'response',
        'requestId': requestId,
        'success': false,
        'error': 'Unknown capability: $capability',
      });
    }
  }

  void _send(Map<String, dynamic> data) {
    _channel?.sink.add(jsonEncode(data));
  }

  void disconnect() {
    _channel?.sink.close();
  }
}
```

## Troubleshooting

### Gateway Crashes After Few Minutes

**Cause**: Android battery optimization kills the process.

**Solution**:

```dart
// Request battery optimization exemption
import 'package:battery_optimization/battery_optimization.dart';

Future<void> requestBatteryOptimizationExemption() async {
  final isIgnoring = await BatteryOptimization.isIgnoringBatteryOptimizations();
  
  if (!isIgnoring) {
    await BatteryOptimization.openBatteryOptimizationSettings();
    // User must manually disable optimization
  }
}
```

Or manually in Android Settings:
1. Settings → Apps → OpenClaw
2. Battery → Unrestricted
3. Or Settings → Battery → Battery Optimization → All Apps → OpenClaw → Don't optimize

### Storage Permission Prompt (Unwanted)

**Cause**: Previous versions auto-requested `MANAGE_EXTERNAL_STORAGE`.

**Solution**: Deny the permission. The app works without it. Storage access is now opt-in from Settings only.

```xml
<!-- android/app/src/main/AndroidManifest.xml -->
<!-- Remove this if present -->
<uses-permission android:name="android.permission.MANAGE_EXTERNAL_STORAGE" />
```

### Node Commands Not Working

**Cause**: Permissions not granted or `openclaw.json` not patched.

**Solution**:

```dart
// Check if node device is enabled
final prefs = await SharedPreferences.getInstance();
final nodeEnabled = prefs.getBool('node_device_enabled') ?? false;

if (!nodeEnabled) {
  // Enable in Settings → "Enable Node Device"
}

// Verify openclaw.json patch
final configPath = '$filesDir/ubuntu-rootfs/root/.openclaw/openclaw.json';
final config = jsonDecode(File(configPath).readAsStringSync());
print('allowCommands: ${config['allowCommands']}');
// Should be: ["camera.*", "canvas.*", "flash.*", "location.*", "screen.*", "sensor.*", "haptic.*"]
```

### Setup Download Fails

**Cause**: Unstable network or insufficient storage.

**Solution**:

```bash
# Check available storage
adb shell df -h /data

# Retry setup with verbose logs
openclawx setup --verbose

# Or in Flutter app, check logs in Dashboard → Logs tab
```

### SSH Connection Refused

**Cause**: SSH server not started or firewall blocks port 2222.

**Solution**:

```dart
// Verify SSH server status
final sshService = SSHService();
final isRunning = await sshService.isRunning();

if (!isRunning) {
  await sshService.start();
}

// Check binding
final info = await sshService.getConnectionInfo();
print('SSH listening on: ${info['ip']}:${info['port']}');
// Ensure device and PC are on same network
```

### TweetClaw Credentials Issue

**Cause**: Credentials not exported from mobile browser or missing in config.

**Solution**:

```bash
# Export cookies from mobile Chrome/Firefox using EditThisCookie extension
# Then manually create credentials file

openclawx shell
cd ~/.openclaw

cat > tweetclaw-credentials.json <<EOF
{
  "accounts": {
    "main": {
      "username": "your_username",
      "authToken": "your_auth_token_from_cookies",
      "ct0": "your_ct0_from_cookies"
    }
  }
}
EOF

# Test connection
openclaw run tweetclaw scrape-tweets --username=your_username --limit=1
```

### Bionic libc Crash (Termux CLI)

**Cause**: Node.js `os.networkInterfaces()` crashes on Android's Bionic libc.

**Solution**: The CLI automatically patches OpenClaw's entry point to stub out the problematic call. If you see crashes, verify the patch:

```bash
openclawx shell
cat ~/.openclaw/node_modules/openclaw/dist/index.js | grep "networkInterfaces"
# Should return empty output (call removed)
```

### Foreground Service Not Starting

**Cause**: Android 12+ requires foreground service permissions.

**Solution**:

```xml
<!-- android/app/src/main/AndroidManifest.xml -->
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
<uses-permission android:name="android.permission.FOREGROUND_SERVICE_DATA_SYNC" />

<service
    android:name=".services.GatewayForegroundService"
    android:foregroundServiceType="dataSync"
    android:exported="false" />
```

### Web Dashboard 401 Unauthorized

**Cause**: Auth token not captured or expired.

**Solution**:

```dart
// Capture token from onboarding flow
final token = await _gatewayService.getAuthToken();

// Inject into WebView
_webViewController.loadRequest(
  Uri.parse('http://127.0.0.1:18789'),
  headers: {'Authorization': 'Bearer $token'},
);
```

Or manually in Settings → Token URL → copy token → add as query param: `http://127.0.0.1:18789?token=your_token`

## Environment Variables

Reference these in your code instead of hardcoding secrets:

- `ANTHROPIC_API_KEY` — Anthropic Claude API key
- `OPENAI_API_KEY` — OpenAI API key
- `GOOGLE_API_KEY` — Google Gemini API key
- `OPENROUTER_API_KEY` — OpenRouter API key
- `NVIDIA_API_KEY` — NVIDIA NIM API key
- `DEEPSEEK_API_KEY` — DeepSeek API key
- `XAI_API_KEY` — xAI Grok API key
- `TWITTER_USERNAME` — X/Twitter account username (for TweetClaw)
- `TWITTER_PASSWORD` — X/Twitter account password
- `TWITTER_EMAIL` — X/Twitter account email
- `TWITTER_AUTH_TOKEN` — X/Twitter auth token from cookies
- `TWITTER_CT0` — X/Twitter CSRF token (ct0) from cookies

Configure these via `openclaw configure` or in the Flutter app's Onboarding flow.

## Resources

- GitHub: https://github.com/mithun50/openclaw-termux
- Releases: https://github.com/mithun50/openclaw-termux/releases
- NPM Package: https://www.npmjs.com/package/openclaw-termux
- OpenClaw Docs: https://github.com/openclaw/openclaw
- TweetClaw Mobile Workflows: https://github.com/mithun50/openclaw-termux/blob/main/docs/tweetclaw-mobile-workflows.md
- F-Droid Termux: https://f-droid.org/packages/com.termux/
```
