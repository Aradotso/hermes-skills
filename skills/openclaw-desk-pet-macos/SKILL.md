---
name: openclaw-desk-pet-macos
description: Build and configure the OpenClaw Desk Pet, a native macOS desktop companion that visualizes AI agent activity with animated cat states.
triggers:
  - set up openclaw desktop pet
  - configure openclaw desk sprite
  - integrate openclaw gateway visualization
  - build macos openclaw companion
  - create ai agent desktop pet
  - connect openclaw to desktop animation
  - customize openclaw desk pet quick tasks
  - troubleshoot openclaw desk pet connection
---

# OpenClaw Desk Pet macOS Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

OpenClaw Desk Pet is a native macOS desktop companion that visualizes OpenClaw agent activity through an animated cat character. It listens to OpenClaw Gateway events and maps agent states (thinking, tool execution, idle, sleeping) to corresponding animations. Users can trigger tasks via quick-action buttons or text input, and see real-time status updates without switching to the console.

## Installation

```bash
# Clone the repository
git clone https://github.com/LeoZhaorx/openclaw-desk-pet.git
cd openclaw-desk-pet

# Copy and secure the environment file
cp desk-sprite/.desk-sprite.env.example desk-sprite/.desk-sprite.env
chmod 600 desk-sprite/.desk-sprite.env
```

**Requirements:**
- macOS 13+
- Swift 5.9+ (Xcode Command Line Tools)
- Python 3.9+
- OpenClaw installed and configured

## Configuration

Edit `desk-sprite/.desk-sprite.env`:

```bash
# OpenClaw installation directory
OPENCLAW_ROOT="$HOME/.openclaw"

# Gateway WebSocket endpoint
OPENCLAW_GATEWAY_URL="ws://127.0.0.1:18789"

# Gateway token (auto-discovered if empty)
OPENCLAW_GATEWAY_TOKEN=""

# File fallback active window (seconds)
OPENCLAW_ACTIVE_WINDOW_SECONDS="20"

# Optional startup script (absolute path)
OPENCLAW_START_SCRIPT=""

# Local config console port
DESK_SPRITE_CONSOLE_PORT="17890"

# Animation assets directory
DESK_SPRITE_ASSETS="./media/"
```

**Token Discovery Order:**
1. Explicit `OPENCLAW_GATEWAY_TOKEN` environment variable
2. OpenClaw config gateway token
3. OpenClaw `.env` file

## Key Commands

```bash
# Start the desk pet (from repository root)
./start-desk-pet.command

# Stop the desk pet
./stop-desk-pet.command

# Restart the desk pet
./restart-desk-pet.command

# Inside desk-sprite/ directory
cd desk-sprite/
./launch.sh    # Start
./halt.sh      # Stop
./health.sh    # Check status
```

## Configuration Console

Access the local configuration panel at `http://127.0.0.1:17890/` (only listens on localhost).

**Features:**
- Set OpenClaw root directory
- Configure Gateway URL and token
- Define startup script
- Adjust file fallback window
- Create and reorder quick-action buttons
- Auto-save and reload configuration

## Swift Integration Patterns

### State Machine Architecture

The desk pet uses a state coordinator that maps OpenClaw Gateway events to animation states:

```swift
enum DeskSpriteState {
    case idle           // Waiting for tasks
    case thinking       // Processing request
    case taskStarting   // Task initiated
    case tooling        // Tool execution (exec, web, sessions_spawn)
    case completed      // Task finished
    case sleeping       // Long idle (shallow → deep sleep)
}
```

### Gateway Event Handling

```swift
import Foundation

struct GatewayEvent: Codable {
    let type: String  // "chat", "agent", "tool_call", etc.
    let data: [String: AnyCodable]
}

class StateCoordinator {
    func processGatewayEvent(_ event: GatewayEvent) -> DeskSpriteState {
        switch event.type {
        case "agent":
            if let status = event.data["status"]?.stringValue {
                switch status {
                case "thinking": return .thinking
                case "tool_call": return .tooling
                case "completed": return .completed
                default: return .idle
                }
            }
        case "chat":
            return .taskStarting
        default:
            break
        }
        return .idle
    }
}
```

### Animation Transition Logic

```swift
import AVFoundation

class AnimationController {
    private var currentSegment: AnimationSegment = .idleLoop
    private var player: AVPlayer?
    
    enum AnimationSegment {
        case idleLoop, thinkingEnter, thinkingLoop
        case toolingEnter, toolingLoop, toolingExit
        case sleepEnter, sleepShallow, sleepDeep
    }
    
    func transitionTo(state: DeskSpriteState) {
        // Wait for safe transition point (segment boundary)
        let nextSegment = mapStateToSegment(state)
        
        if canTransition(from: currentSegment, to: nextSegment) {
            playSegment(nextSegment)
            currentSegment = nextSegment
        }
    }
    
    private func mapStateToSegment(_ state: DeskSpriteState) -> AnimationSegment {
        switch state {
        case .idle: return .idleLoop
        case .thinking: return .thinkingEnter
        case .tooling: return .toolingEnter
        case .sleeping: return .sleepEnter
        default: return currentSegment
        }
    }
}
```

### File Fallback Mode

When Gateway is unavailable, the desk pet reads local session files:

```swift
import Foundation

class FileFallbackMonitor {
    private let openclawRoot: String
    private let activeWindowSeconds: Int
    
    func getCurrentState() -> DeskSpriteState {
        // Read sessions.json
        let sessionsPath = "\(openclawRoot)/sessions/sessions.json"
        guard let data = try? Data(contentsOf: URL(fileURLWithPath: sessionsPath)),
              let json = try? JSONDecoder().decode(SessionsFile.self, from: data) else {
            return .idle
        }
        
        // Get most recent session
        guard let recentSession = json.sessions.max(by: { $0.timestamp < $1.timestamp }) else {
            return .idle
        }
        
        // Check if within active window
        let now = Date()
        let sessionTime = Date(timeIntervalSince1970: recentSession.timestamp)
        let elapsed = now.timeIntervalSince(sessionTime)
        
        if elapsed > Double(activeWindowSeconds) {
            return .sleeping
        }
        
        // Parse JSONL tail for state
        return parseJSONLTail(sessionPath: recentSession.path)
    }
    
    private func parseJSONLTail(sessionPath: String) -> DeskSpriteState {
        let jsonlPath = "\(openclawRoot)/sessions/\(sessionPath)/log.jsonl"
        // Read last N lines efficiently
        // ...
        return .idle
    }
}
```

## Quick Task Configuration

Quick tasks are defined in the configuration and accessible via the desk pet UI:

```swift
struct QuickTask: Codable, Identifiable {
    let id: UUID
    let label: String
    let prompt: String
    let order: Int
}

// Example configuration (saved automatically)
let quickTasks = [
    QuickTask(
        id: UUID(),
        label: "Check Email",
        prompt: "Check my unread emails and summarize",
        order: 1
    ),
    QuickTask(
        id: UUID(),
        label: "Weather",
        prompt: "What's the weather forecast for today?",
        order: 2
    ),
    QuickTask(
        id: UUID(),
        label: "Daily Brief",
        prompt: "Generate my daily briefing with tasks and calendar",
        order: 3
    )
]
```

## Sending Tasks from Desktop

```swift
import Foundation

class TaskDispatcher {
    enum DispatchMethod {
        case console    // Open OpenClaw console
        case cli        // Use openclaw CLI
        case gateway    // Direct WebSocket
    }
    
    func sendTask(_ prompt: String) async throws {
        // Try methods in order
        if let console = try? await sendToConsole(prompt) {
            return
        }
        
        if let cli = try? await sendViaCLI(prompt) {
            return
        }
        
        if let gateway = try? await sendToGateway(prompt) {
            return
        }
        
        throw TaskDispatchError.allMethodsFailed
    }
    
    private func sendToGateway(_ prompt: String) async throws {
        let url = URL(string: ProcessInfo.processInfo.environment["OPENCLAW_GATEWAY_URL"] ?? "")!
        let token = ProcessInfo.processInfo.environment["OPENCLAW_GATEWAY_TOKEN"]
        
        var request = URLRequest(url: url)
        request.setValue("Bearer \(token ?? "")", forHTTPHeaderField: "Authorization")
        
        let message = ["type": "chat", "content": prompt]
        request.httpBody = try JSONEncoder().encode(message)
        
        let (_, response) = try await URLSession.shared.data(for: request)
        guard (response as? HTTPURLResponse)?.statusCode == 200 else {
            throw TaskDispatchError.gatewayFailed
        }
    }
}
```

## Window Management

The desk pet uses a transparent, borderless window that persists across macOS Spaces:

```swift
import AppKit

class DeskPetWindowController: NSWindowController {
    override func windowDidLoad() {
        super.windowDidLoad()
        
        guard let window = window else { return }
        
        // Transparent borderless window
        window.styleMask = [.borderless, .nonactivatingPanel]
        window.isOpaque = false
        window.backgroundColor = .clear
        window.level = .floating
        window.collectionBehavior = [.canJoinAllSpaces, .stationary]
        
        // Enable dragging
        window.isMovableByWindowBackground = true
        
        // Restore saved position
        if let savedFrame = UserDefaults.standard.string(forKey: "DeskPetWindowFrame") {
            window.setFrameFromString(savedFrame)
        }
    }
    
    func savePosition() {
        if let window = window {
            let frameString = window.frameString
            UserDefaults.standard.set(frameString, forKey: "DeskPetWindowFrame")
        }
    }
}
```

## Tool Label Display

When OpenClaw executes tools, the desk pet shows corresponding labels:

```swift
struct ToolBubbleView: View {
    let toolType: String
    
    var body: some View {
        Text(toolLabel)
            .font(.system(size: 10, weight: .medium))
            .padding(.horizontal, 8)
            .padding(.vertical, 4)
            .background(
                Capsule()
                    .fill(toolColor.opacity(0.9))
            )
            .foregroundColor(.white)
    }
    
    private var toolLabel: String {
        switch toolType {
        case "exec": return "exec"
        case "web_browser", "web": return "web"
        case "sessions_spawn": return "spawn"
        case "file_read", "file_write": return "file"
        default: return toolType
        }
    }
    
    private var toolColor: Color {
        switch toolType {
        case "exec": return .blue
        case "web_browser", "web": return .green
        case "sessions_spawn": return .purple
        default: return .gray
        }
    }
}
```

## Result Bubble Display

Task results are shown in a chat-like bubble with cleaned formatting:

```swift
struct ResultBubbleView: View {
    let content: String
    
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            ForEach(cleanedSegments, id: \.self) { segment in
                Text(segment)
                    .font(.system(size: 12))
                    .padding(10)
                    .background(
                        RoundedRectangle(cornerRadius: 12)
                            .fill(Color.white.opacity(0.95))
                    )
                    .foregroundColor(.primary)
            }
        }
    }
    
    private var cleanedSegments: [String] {
        // Remove control markers and split into segments
        let cleaned = content
            .replacingOccurrences(of: #"\[THINKING\]"#, with: "", options: .regularExpression)
            .replacingOccurrences(of: #"\[TOOL:.*?\]"#, with: "", options: .regularExpression)
            .trimmingCharacters(in: .whitespacesAndNewlines)
        
        return cleaned.components(separatedBy: "\n\n").filter { !$0.isEmpty }
    }
}
```

## Troubleshooting

### Desk Pet Not Connecting to Gateway

```bash
# Check Gateway is running
openclaw gateway status

# Verify Gateway URL
echo $OPENCLAW_GATEWAY_URL

# Test Gateway directly
curl -H "Authorization: Bearer $OPENCLAW_GATEWAY_TOKEN" \
  http://127.0.0.1:18789/health

# Check desk pet logs
tail -f desk-sprite/logs/desk-sprite.log
```

### Animation Not Playing

```swift
// Verify assets path
let assetsPath = ProcessInfo.processInfo.environment["DESK_SPRITE_ASSETS"]!
print("Assets path: \(assetsPath)")

// Check for required animation files
let requiredFiles = [
    "idle-loop.mov",
    "thinking-enter.mov",
    "thinking-loop.mov",
    "tooling-enter.mov",
    "tooling-loop.mov",
    "sleep-shallow.mov",
    "sleep-deep.mov"
]

for file in requiredFiles {
    let path = "\(assetsPath)/\(file)"
    if !FileManager.default.fileExists(atPath: path) {
        print("Missing: \(file)")
    }
}
```

### Quick Tasks Not Sending

```bash
# Verify OpenClaw CLI is accessible
which openclaw

# Test CLI directly
openclaw chat "Test message"

# Check Gateway WebSocket connection
# (desk pet will fall back to CLI if Gateway fails)
```

### Configuration Changes Not Applied

```bash
# Restart the desk pet to reload configuration
./restart-desk-pet.command

# Or click "Reload" in the configuration console
open http://127.0.0.1:17890/

# Verify environment file permissions
ls -l desk-sprite/.desk-sprite.env
# Should show: -rw------- (600)
```

### File Fallback Mode Not Working

```swift
// Verify OpenClaw root path
let root = ProcessInfo.processInfo.environment["OPENCLAW_ROOT"]!
print("OpenClaw root: \(root)")

// Check sessions.json exists and is readable
let sessionsPath = "\(root)/sessions/sessions.json"
if FileManager.default.isReadableFile(atPath: sessionsPath) {
    print("sessions.json is accessible")
} else {
    print("Cannot read sessions.json at \(sessionsPath)")
}

// Verify active window setting
let activeWindow = ProcessInfo.processInfo.environment["OPENCLAW_ACTIVE_WINDOW_SECONDS"]!
print("Active window: \(activeWindow)s")
```

## Common Patterns

### Custom Animation States

Add custom states by extending the state machine:

```swift
enum DeskSpriteState {
    case idle
    case thinking
    case tooling
    case completed
    case sleeping
    case custom(String)  // Add custom states
}

func mapEventToState(_ event: GatewayEvent) -> DeskSpriteState {
    // Map custom events to states
    if event.type == "custom_event" {
        return .custom("myState")
    }
    // ...existing logic
}
```

### Persistent Window Position Across Launches

```swift
class DeskPetWindowController: NSWindowController {
    private let positionKey = "DeskPetWindowFrame"
    
    override func windowDidLoad() {
        super.windowDidLoad()
        restorePosition()
        
        // Save position on move
        NotificationCenter.default.addObserver(
            self,
            selector: #selector(windowDidMove),
            name: NSWindow.didMoveNotification,
            object: window
        )
    }
    
    @objc func windowDidMove(_ notification: Notification) {
        savePosition()
    }
    
    private func restorePosition() {
        if let savedFrame = UserDefaults.standard.string(forKey: positionKey) {
            window?.setFrameFromString(savedFrame)
        }
    }
    
    private func savePosition() {
        if let window = window {
            UserDefaults.standard.set(window.frameString, forKey: positionKey)
        }
    }
}
```

### Dynamic Quick Task Loading

```swift
struct QuickTaskManager {
    private let configPath: String
    
    func loadQuickTasks() -> [QuickTask] {
        guard let data = try? Data(contentsOf: URL(fileURLWithPath: configPath)),
              let tasks = try? JSONDecoder().decode([QuickTask].self, from: data) else {
            return defaultQuickTasks()
        }
        return tasks.sorted { $0.order < $1.order }
    }
    
    func saveQuickTasks(_ tasks: [QuickTask]) throws {
        let data = try JSONEncoder().encode(tasks)
        try data.write(to: URL(fileURLWithPath: configPath))
    }
    
    private func defaultQuickTasks() -> [QuickTask] {
        [
            QuickTask(id: UUID(), label: "Check Email", prompt: "Summarize unread emails", order: 1),
            QuickTask(id: UUID(), label: "Weather", prompt: "Today's weather forecast", order: 2)
        ]
    }
}
```

This skill provides comprehensive guidance for AI coding agents to help developers install, configure, and extend the OpenClaw Desk Pet macOS application.
