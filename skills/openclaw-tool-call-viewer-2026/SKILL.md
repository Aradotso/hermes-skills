---
name: openclaw-tool-call-viewer-2026
description: Browser-based interface for viewing and filtering OpenClaw session tool call history with zero dependencies for local network deployment.
triggers:
  - view openclaw session tool calls
  - filter openclaw session history
  - inspect openclaw tool call logs
  - browse openclaw session data
  - set up openclaw tool call viewer
  - debug openclaw session activity
  - review openclaw tool usage
  - examine openclaw call history
---

# OpenClaw Tool Call Viewer 2026 Skill

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

## Overview

OpenClaw Tool Call Viewer is a compact, zero-dependency web interface for browsing and filtering OpenClaw session tool call history. It provides a browser-based UI for inspecting session activity, tracking tool usage patterns, and filtering through dense logs during development and debugging sessions.

The viewer is designed for local network deployment with no runtime dependencies, making it ideal for internal developer workflows and session review tasks.

## Installation

### Clone and Deploy

```bash
# Clone the repository
git clone https://github.com/reedchris43/openclaw-tool-call-viewer-2026.git
cd openclaw-tool-call-viewer-2026

# Serve locally (using Python)
python3 -m http.server 8080

# Or using Node.js
npx http-server -p 8080

# Or using PHP
php -S localhost:8080
```

### Nginx Configuration Example

```nginx
server {
    listen 8080;
    server_name localhost;
    
    root /path/to/openclaw-tool-call-viewer-2026;
    index index.html;
    
    location / {
        try_files $uri $uri/ =404;
    }
    
    # Optional CORS headers for local network access
    add_header Access-Control-Allow-Origin *;
}
```

### Apache Configuration Example

```apache
<VirtualHost *:8080>
    DocumentRoot "/path/to/openclaw-tool-call-viewer-2026"
    
    <Directory "/path/to/openclaw-tool-call-viewer-2026">
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>
    
    # Optional CORS headers
    Header set Access-Control-Allow-Origin "*"
</VirtualHost>
```

## Project Structure

```
openclaw-tool-call-viewer-2026/
├── index.html          # Main viewer interface
├── styles.css          # UI styling (if separate)
├── script.js           # Filtering and display logic (if separate)
├── README.md           # Project documentation
└── LICENSE             # GPL-3.0 license
```

## Data Format

The viewer expects OpenClaw session data in JSON format. Here's the typical structure:

```json
{
  "session_id": "session-2026-07-11-001",
  "timestamp": "2026-07-11T00:53:25Z",
  "tool_calls": [
    {
      "id": "call_001",
      "timestamp": "2026-07-11T00:53:30Z",
      "tool_name": "code_interpreter",
      "parameters": {
        "language": "python",
        "code": "print('Hello, OpenClaw!')"
      },
      "result": {
        "status": "success",
        "output": "Hello, OpenClaw!"
      }
    },
    {
      "id": "call_002",
      "timestamp": "2026-07-11T00:54:15Z",
      "tool_name": "file_reader",
      "parameters": {
        "path": "/workspace/config.json"
      },
      "result": {
        "status": "success",
        "content": "..."
      }
    }
  ]
}
```

## Loading Session Data

### Inline Data Loading

```html
<!DOCTYPE html>
<html>
<head>
    <title>OpenClaw Tool Call Viewer</title>
</head>
<body>
    <div id="viewer"></div>
    
    <script>
        // Load session data
        const sessionData = {
            session_id: "session-001",
            tool_calls: [
                {
                    id: "call_001",
                    timestamp: "2026-07-11T00:53:30Z",
                    tool_name: "code_interpreter",
                    parameters: { language: "python" },
                    result: { status: "success" }
                }
            ]
        };
        
        // Initialize viewer
        function initViewer(data) {
            const viewer = document.getElementById('viewer');
            data.tool_calls.forEach(call => {
                const div = document.createElement('div');
                div.className = 'tool-call';
                div.innerHTML = `
                    <h3>${call.tool_name}</h3>
                    <p>ID: ${call.id}</p>
                    <p>Time: ${call.timestamp}</p>
                    <p>Status: ${call.result.status}</p>
                `;
                viewer.appendChild(div);
            });
        }
        
        initViewer(sessionData);
    </script>
</body>
</html>
```

### Fetch External Session Data

```javascript
// Fetch session data from API or file
async function loadSession(sessionPath) {
    try {
        const response = await fetch(sessionPath);
        const data = await response.json();
        return data;
    } catch (error) {
        console.error('Failed to load session:', error);
        return null;
    }
}

// Usage
loadSession('/data/sessions/session-001.json')
    .then(data => {
        if (data) {
            initViewer(data);
        }
    });
```

## Filtering Implementation

### Basic Filter Functions

```javascript
// Filter by tool name
function filterByToolName(toolCalls, toolName) {
    return toolCalls.filter(call => 
        call.tool_name.toLowerCase().includes(toolName.toLowerCase())
    );
}

// Filter by status
function filterByStatus(toolCalls, status) {
    return toolCalls.filter(call => 
        call.result.status === status
    );
}

// Filter by time range
function filterByTimeRange(toolCalls, startTime, endTime) {
    const start = new Date(startTime);
    const end = new Date(endTime);
    return toolCalls.filter(call => {
        const callTime = new Date(call.timestamp);
        return callTime >= start && callTime <= end;
    });
}

// Combined filter
function applyFilters(toolCalls, filters) {
    let filtered = toolCalls;
    
    if (filters.toolName) {
        filtered = filterByToolName(filtered, filters.toolName);
    }
    
    if (filters.status) {
        filtered = filterByStatus(filtered, filters.status);
    }
    
    if (filters.startTime && filters.endTime) {
        filtered = filterByTimeRange(filtered, filters.startTime, filters.endTime);
    }
    
    return filtered;
}
```

### Interactive Filter UI

```html
<div id="filters">
    <input type="text" id="toolNameFilter" placeholder="Filter by tool name">
    <select id="statusFilter">
        <option value="">All Statuses</option>
        <option value="success">Success</option>
        <option value="error">Error</option>
        <option value="pending">Pending</option>
    </select>
    <input type="datetime-local" id="startTime">
    <input type="datetime-local" id="endTime">
    <button id="applyFilters">Apply Filters</button>
    <button id="clearFilters">Clear Filters</button>
</div>

<script>
document.getElementById('applyFilters').addEventListener('click', () => {
    const filters = {
        toolName: document.getElementById('toolNameFilter').value,
        status: document.getElementById('statusFilter').value,
        startTime: document.getElementById('startTime').value,
        endTime: document.getElementById('endTime').value
    };
    
    const filtered = applyFilters(sessionData.tool_calls, filters);
    renderToolCalls(filtered);
});

document.getElementById('clearFilters').addEventListener('click', () => {
    document.getElementById('toolNameFilter').value = '';
    document.getElementById('statusFilter').value = '';
    document.getElementById('startTime').value = '';
    document.getElementById('endTime').value = '';
    renderToolCalls(sessionData.tool_calls);
});
</script>
```

## Rendering Tool Calls

### Complete Rendering Function

```javascript
function renderToolCalls(toolCalls) {
    const viewer = document.getElementById('viewer');
    viewer.innerHTML = '';
    
    if (toolCalls.length === 0) {
        viewer.innerHTML = '<p>No tool calls found.</p>';
        return;
    }
    
    toolCalls.forEach(call => {
        const callDiv = document.createElement('div');
        callDiv.className = `tool-call ${call.result.status}`;
        
        callDiv.innerHTML = `
            <div class="tool-call-header">
                <span class="tool-name">${call.tool_name}</span>
                <span class="status-badge ${call.result.status}">${call.result.status}</span>
            </div>
            <div class="tool-call-meta">
                <span class="call-id">ID: ${call.id}</span>
                <span class="timestamp">${formatTimestamp(call.timestamp)}</span>
            </div>
            <details>
                <summary>Parameters</summary>
                <pre>${JSON.stringify(call.parameters, null, 2)}</pre>
            </details>
            <details>
                <summary>Result</summary>
                <pre>${JSON.stringify(call.result, null, 2)}</pre>
            </details>
        `;
        
        viewer.appendChild(callDiv);
    });
}

function formatTimestamp(timestamp) {
    const date = new Date(timestamp);
    return date.toLocaleString();
}
```

### CSS Styling Example

```css
.tool-call {
    border: 1px solid #ddd;
    margin: 10px 0;
    padding: 15px;
    border-radius: 5px;
    background: #fff;
}

.tool-call.success {
    border-left: 4px solid #28a745;
}

.tool-call.error {
    border-left: 4px solid #dc3545;
}

.tool-call.pending {
    border-left: 4px solid #ffc107;
}

.tool-call-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
}

.tool-name {
    font-weight: bold;
    font-size: 1.1em;
}

.status-badge {
    padding: 3px 8px;
    border-radius: 3px;
    font-size: 0.85em;
    text-transform: uppercase;
}

.status-badge.success {
    background: #28a745;
    color: white;
}

.status-badge.error {
    background: #dc3545;
    color: white;
}

.tool-call-meta {
    color: #666;
    font-size: 0.9em;
    margin-bottom: 10px;
}

details {
    margin-top: 10px;
}

pre {
    background: #f5f5f5;
    padding: 10px;
    border-radius: 3px;
    overflow-x: auto;
}
```

## Common Usage Patterns

### Pattern 1: Local Development Server

```bash
# Quick local server for development
cd openclaw-tool-call-viewer-2026
python3 -m http.server 8080

# Access at http://localhost:8080
# Place session JSON files in /data directory
```

### Pattern 2: Network-Wide Deployment

```bash
# Deploy to internal network server
scp -r openclaw-tool-call-viewer-2026 user@internal-server:/var/www/html/openclaw-viewer

# Configure server to listen on internal IP
# Access at http://192.168.1.100:8080
```

### Pattern 3: Docker Deployment

```dockerfile
FROM nginx:alpine
COPY . /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

```bash
# Build and run
docker build -t openclaw-viewer .
docker run -d -p 8080:80 openclaw-viewer
```

### Pattern 4: Session Export Integration

```javascript
// Export OpenClaw session to viewer format
function exportSessionForViewer(openclawSession) {
    const viewerFormat = {
        session_id: openclawSession.id,
        timestamp: openclawSession.created_at,
        tool_calls: openclawSession.events
            .filter(event => event.type === 'tool_call')
            .map(event => ({
                id: event.id,
                timestamp: event.timestamp,
                tool_name: event.tool,
                parameters: event.input,
                result: {
                    status: event.status,
                    output: event.output,
                    error: event.error
                }
            }))
    };
    
    return JSON.stringify(viewerFormat, null, 2);
}

// Save to file for viewer
const fs = require('fs');
const sessionJson = exportSessionForViewer(openclawSession);
fs.writeFileSync('./viewer-data/session-001.json', sessionJson);
```

## Configuration Examples

### Environment-Based Data Path

```javascript
// Configure data source via environment or config
const config = {
    dataPath: process.env.OPENCLAW_DATA_PATH || '/data/sessions',
    apiEndpoint: process.env.OPENCLAW_API_ENDPOINT || null,
    refreshInterval: parseInt(process.env.REFRESH_INTERVAL) || 30000
};

// Load session from configured source
async function loadSessionData(sessionId) {
    if (config.apiEndpoint) {
        return fetch(`${config.apiEndpoint}/sessions/${sessionId}`).then(r => r.json());
    } else {
        return fetch(`${config.dataPath}/${sessionId}.json`).then(r => r.json());
    }
}
```

### Auto-Refresh Configuration

```javascript
// Auto-refresh session data
let refreshTimer = null;

function enableAutoRefresh(sessionId, interval = 30000) {
    refreshTimer = setInterval(async () => {
        const data = await loadSessionData(sessionId);
        if (data) {
            updateViewer(data);
        }
    }, interval);
}

function disableAutoRefresh() {
    if (refreshTimer) {
        clearInterval(refreshTimer);
        refreshTimer = null;
    }
}
```

## Troubleshooting

### Issue: Page Doesn't Load

**Check file serving:**
```bash
# Verify files are accessible
ls -la openclaw-tool-call-viewer-2026/

# Test with simple server
cd openclaw-tool-call-viewer-2026
python3 -m http.server 8080

# Check in browser console for errors
```

### Issue: Session Data Not Displaying

**Verify data format:**
```javascript
// Validate JSON structure
function validateSessionData(data) {
    if (!data.tool_calls || !Array.isArray(data.tool_calls)) {
        console.error('Invalid session data: tool_calls must be an array');
        return false;
    }
    
    for (const call of data.tool_calls) {
        if (!call.id || !call.tool_name || !call.result) {
            console.error('Invalid tool call:', call);
            return false;
        }
    }
    
    return true;
}

// Use before rendering
if (validateSessionData(sessionData)) {
    renderToolCalls(sessionData.tool_calls);
}
```

### Issue: Filters Not Working

**Debug filter logic:**
```javascript
// Add logging to filters
function applyFiltersWithLogging(toolCalls, filters) {
    console.log('Original count:', toolCalls.length);
    console.log('Filters:', filters);
    
    let filtered = toolCalls;
    
    if (filters.toolName) {
        filtered = filterByToolName(filtered, filters.toolName);
        console.log('After tool name filter:', filtered.length);
    }
    
    if (filters.status) {
        filtered = filterByStatus(filtered, filters.status);
        console.log('After status filter:', filtered.length);
    }
    
    console.log('Final count:', filtered.length);
    return filtered;
}
```

### Issue: CORS Errors

**Enable CORS for local development:**
```javascript
// If fetching from different origin
const response = await fetch(sessionPath, {
    mode: 'cors',
    headers: {
        'Content-Type': 'application/json'
    }
});
```

**Server-side CORS configuration (Node.js):**
```javascript
const express = require('express');
const app = express();

app.use((req, res, next) => {
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Headers', 'Origin, X-Requested-With, Content-Type, Accept');
    next();
});

app.use(express.static('openclaw-tool-call-viewer-2026'));
app.listen(8080);
```

## Advanced Features

### Search Across Sessions

```javascript
// Search multiple session files
async function searchSessions(searchTerm) {
    const sessionFiles = ['session-001.json', 'session-002.json', 'session-003.json'];
    const results = [];
    
    for (const file of sessionFiles) {
        const data = await loadSession(`/data/${file}`);
        if (data) {
            const matches = data.tool_calls.filter(call =>
                JSON.stringify(call).toLowerCase().includes(searchTerm.toLowerCase())
            );
            
            if (matches.length > 0) {
                results.push({
                    session: data.session_id,
                    matches: matches
                });
            }
        }
    }
    
    return results;
}
```

### Export Filtered Results

```javascript
// Export filtered tool calls as JSON
function exportResults(toolCalls) {
    const dataStr = JSON.stringify(toolCalls, null, 2);
    const dataUri = 'data:application/json;charset=utf-8,' + encodeURIComponent(dataStr);
    
    const exportLink = document.createElement('a');
    exportLink.setAttribute('href', dataUri);
    exportLink.setAttribute('download', 'filtered-results.json');
    exportLink.click();
}

// Export as CSV
function exportAsCSV(toolCalls) {
    const headers = ['ID', 'Tool Name', 'Timestamp', 'Status'];
    const rows = toolCalls.map(call => [
        call.id,
        call.tool_name,
        call.timestamp,
        call.result.status
    ]);
    
    const csvContent = [
        headers.join(','),
        ...rows.map(row => row.join(','))
    ].join('\n');
    
    const dataUri = 'data:text/csv;charset=utf-8,' + encodeURIComponent(csvContent);
    const exportLink = document.createElement('a');
    exportLink.setAttribute('href', dataUri);
    exportLink.setAttribute('download', 'filtered-results.csv');
    exportLink.click();
}
```

This skill provides comprehensive guidance for deploying, configuring, and using the OpenClaw Tool Call Viewer for session inspection and debugging workflows.
