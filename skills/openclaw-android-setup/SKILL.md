---
name: openclaw-android-setup
description: Expert in setting up and running OpenClaw AI agent framework on Android devices via Termux without proot
triggers:
  - how do I install OpenClaw on Android
  - set up OpenClaw on my Android phone
  - run OpenClaw on Android without Linux
  - configure OpenClaw gateway on Termux
  - manage OpenClaw Android installation
  - troubleshoot OpenClaw on Android
  - backup and restore OpenClaw on Android
  - keep OpenClaw running on Android
---

# OpenClaw Android Setup Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

OpenClaw on Android (`openclaw-android`) enables running the OpenClaw AI agent framework on Android devices through Termux, without requiring a full Linux distribution via proot-distro. It achieves this by installing only the glibc dynamic linker, resulting in ~200MB overhead instead of 1-2GB, with native performance.

**Key benefits:**
- Single-command installation
- No proot-distro required (saves 1-2GB storage)
- Native performance (no proot layer overhead)
- Automatic path conversion and environment setup
- Built-in CLI for updates, backups, and optional tool installation

## Installation

### Prerequisites

- Android 7.0+ (Android 10+ recommended)
- ~1GB free storage
- Termux from F-Droid (NOT Play Store version)

### Initial Termux Setup

```bash
# Update package manager and install curl
pkg update -y && pkg install -y curl
```

### Install OpenClaw on Android

```bash
# Single command installation
curl -sL myopenclawhub.com/install | bash && source ~/.bashrc
```

This installs:
- glibc dynamic linker (glibc-runner via pacman)
- Node.js (official linux-arm64 with ld.so loader)
- OpenClaw framework
- Path conversion and environment patches
- `oa` CLI management tool

Installation takes 3-10 minutes depending on network speed.

## Initial Configuration

### Onboarding

```bash
# Run initial setup wizard
openclaw onboard
```

Follow prompts to configure:
- Authentication token
- Gateway port
- Optional tools (code-server, OpenCode, AI CLIs)

### Starting the Gateway

**Important:** Run gateway directly in Termux app, not via SSH (SSH disconnection will stop the gateway).

```bash
# Start OpenClaw gateway (occupies terminal)
openclaw gateway
```

To stop: Press `Ctrl+C` (NOT `Ctrl+Z` which only suspends).

### Opening New Termux Sessions

Use the hamburger menu (☰) or swipe from left edge to open side menu, then tap "NEW SESSION" to run additional commands while gateway is running.

## CLI Reference

### `oa` Management Tool

```bash
# Update OpenClaw and all installed components
oa --update && source ~/.bashrc

# Install optional tools interactively
oa --install

# Create backup of OpenClaw data
oa --backup

# Restore from backup (interactive selection)
oa --restore

# Show installation status
oa --status

# Uninstall OpenClaw on Android
oa --uninstall

# Show version
oa --version

# Show help
oa --help
```

### OpenClaw Core Commands

```bash
# Show OpenClaw version
openclaw --version

# Start gateway (main daemon)
openclaw gateway

# Run onboarding wizard
openclaw onboard

# List available commands
openclaw --help
```

## Backup and Restore

### Creating Backups

```bash
# Create backup in default location (~/.openclaw-android/backup/)
oa --backup

# Create backup in custom location
oa --backup ~/my-backups/
```

Backup includes:
- Configuration files
- State and workspace data
- Agent definitions
- All OpenClaw data from `~/.openclaw/`

Backup format: Timestamped tar.gz (e.g., `2026-03-14T00-00-00.000Z-openclaw-backup.tar.gz`)

### Restoring Backups

```bash
# Interactive restore (lists available backups)
oa --restore
```

The restore process:
1. Lists all backups in default directory
2. Prompts for selection by number
3. Validates backup manifest
4. Confirms before overwriting existing data
5. Extracts to `~/.openclaw/`

## Configuration

### Environment Variables

OpenClaw configuration is stored in `~/.openclaw/`. Common environment variables:

```bash
# Set gateway port (default 3000)
export OPENCLAW_PORT=3000

# Set authentication token
export OPENCLAW_TOKEN=your_token_here

# Set log level
export OPENCLAW_LOG_LEVEL=info
```

### SSH Access and Dashboard

Set up SSH to access dashboard from PC:

```bash
# In Termux, install OpenSSH
pkg install openssh

# Start SSH daemon
sshd

# Get device IP
ifconfig wlan0 | grep inet
```

From PC, create SSH tunnel:

```bash
# Tunnel gateway port to localhost
ssh -L 3000:localhost:3000 -p 8022 192.168.1.XXX

# Access dashboard at http://localhost:3000
```

### Managing Multiple Devices

Use Dashboard Connect tool at myopenclawhub.com:
- Save connection settings per device (IP, token, ports)
- Auto-generates SSH tunnel commands
- All data stored locally in browser localStorage

## Keeping Processes Alive

Android aggressively kills background processes. Required settings:

### Developer Options

```bash
# Enable Developer Options:
# Settings → About Phone → Tap "Build Number" 7 times
```

Enable in Developer Options:
- **Stay awake** (while charging)
- **Disable Phantom Process Killer** (if available)

### Battery Optimization

```bash
# Disable battery optimization for Termux:
# Settings → Apps → Termux → Battery → Unrestricted
```

### Charge Limit (optional)

For devices used 24/7, limit charge to 80% to extend battery life:
- Settings → Battery → Battery Protection / Charge Limit → 80%

## Common Patterns

### Running Gateway as Background Service

```bash
# Use tmux to persist sessions (install via oa --install)
tmux new -s gateway
openclaw gateway

# Detach: Ctrl+b, then d
# Reattach: tmux attach -t gateway
```

### Checking Gateway Status

```bash
# Check if gateway is running
pgrep -f "openclaw gateway"

# View gateway logs (if running in tmux)
tmux attach -t gateway
```

### Updating All Components

```bash
# Single command to update everything
oa --update && source ~/.bashrc

# This updates:
# - OpenClaw core (openclaw@latest)
# - code-server (if installed)
# - OpenCode (if installed)
# - AI CLI tools (if installed)
# - Android compatibility patches
```

### Installing Optional Tools

```bash
# Interactive installation menu
oa --install

# Available options:
# - tmux (session management)
# - code-server (browser IDE)
# - OpenCode (AI coding assistant)
# - Claude Code CLI
# - Gemini CLI
# - Codex CLI
```

## Architecture

### Standard Approach (Avoided)

```
Linux Kernel
└── Android + Bionic libc + Termux
    └── proot-distro + Debian/Ubuntu (1-2GB)
        └── GNU glibc
            └── Node.js → OpenClaw
```

### OpenClaw-Android Approach

```
Linux Kernel
└── Android + Bionic libc + Termux
    └── glibc ld.so (linker only, ~200MB)
        └── Node.js → OpenClaw
```

Benefits:
- 5-10x less storage overhead
- Native performance (no proot layer)
- Automatic path conversion (/tmp, /bin/sh, /usr/bin/env)
- Single-command setup

## Troubleshooting

### Gateway Won't Start

```bash
# Check if port is already in use
netstat -tulpn | grep 3000

# Kill existing process
pkill -f "openclaw gateway"

# Check logs
openclaw gateway --verbose
```

### "Command not found" After Installation

```bash
# Reload shell configuration
source ~/.bashrc

# Verify installation
which openclaw
which oa
```

### Gateway Stops When Screen Locks

Ensure battery optimization is disabled:
1. Settings → Apps → Termux → Battery → Unrestricted
2. Enable "Stay awake" in Developer Options (while charging)
3. Use tmux to persist sessions

### Node.js Binary Issues

```bash
# Reinstall with proper glibc wrapper
oa --update && source ~/.bashrc

# Verify Node.js is using glibc loader
which node
cat $(which node)  # Should show ld.so wrapper script
```

### Backup Fails with "Operation not permitted"

```bash
# Use oa --backup instead of openclaw backup
oa --backup

# This uses tar directly, avoiding hardlink issues in Android
```

### SSH Connection Refused

```bash
# Ensure sshd is running
pgrep sshd

# If not running, start it
sshd

# Check SSH port (default 8022 in Termux)
netstat -tulpn | grep sshd
```

### Out of Storage Space

```bash
# Check storage usage
df -h ~

# Clean package cache
pkg clean

# Remove unused backups
ls ~/.openclaw-android/backup/
rm ~/.openclaw-android/backup/old-backup.tar.gz
```

### Gateway Crashes on Android 7-9

Some older Android versions have issues with certain Node.js features:

```bash
# Update to latest version (may include compatibility fixes)
oa --update && source ~/.bashrc

# Check Android version
getprop ro.build.version.release

# If issues persist, report with Android version and device model
```

## Best Practices

1. **Always run gateway in Termux app** (not via SSH) to prevent disconnection issues
2. **Use tmux** for persistent sessions that survive terminal closures
3. **Regular backups** before major updates: `oa --backup`
4. **Monitor storage** on devices with limited space
5. **Keep device charged** and plugged in for 24/7 operation
6. **Update regularly** to get latest Android compatibility patches: `oa --update`
7. **Disable phantom process killer** on Android 12+ for stability
8. **Use Wi-Fi** for initial installation (faster, more reliable)

## Additional Resources

- GitHub: https://github.com/AidanPark/openclaw-android
- Dashboard Connect: https://myopenclawhub.com
- Termux SSH Guide: docs/termux-ssh-guide.md
- Keeping Processes Alive: docs/disable-phantom-process-killer.md
