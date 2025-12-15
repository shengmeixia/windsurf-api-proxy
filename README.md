# Windsurf API Proxy

A lightweight HTTP API proxy for interacting with Windsurf Chat programmatically.

## Features

- Send messages to Windsurf Chat and receive AI responses via HTTP API
- Manage conversations (list, switch, create new)
- Continue existing conversations or start new ones
- Cross-platform support (macOS, Windows, Linux)

## Quick Start

### 1. Start Windsurf with Remote Debugging

```bash
# macOS
/Applications/Windsurf.app/Contents/MacOS/Electron --remote-debugging-port=9222

# Windows
"C:\Users\<user>\AppData\Local\Programs\Windsurf\Windsurf.exe" --remote-debugging-port=9222

# Linux
windsurf --remote-debugging-port=9222
```

### 2. Run the Proxy Server

```bash
./windsurf-proxy
```

The server starts on `http://localhost:58080` by default.

## API Endpoints

| Method | Endpoint | Description | Request Body |
|--------|----------|-------------|--------------|
| GET | `/health` | Health check | `{}` |
| POST | `/cdp/connect` | Connect to Windsurf CDP | `{}` |
| POST | `/cdp/disconnect` | Disconnect from CDP | `{}` |
| POST | `/cdp/send` | Send message and get response | `{"message": "Hello", "timeout": 60, "new_conversation": true}` |
| GET | `/cdp/status` | Get connection status | `{}` |
| GET | `/cdp/conversations` | List all conversations | `{}` |
| POST | `/cdp/conversations/delete-all` | Delete all conversations | `{}` |
| POST | `/cdp/switch` | Switch to a specific conversation | `{"title": "Previous Chat"}` |

## Usage Examples

### Send a Message (New Conversation)

```bash
curl -X POST http://localhost:58080/cdp/send \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello, what is 1+1?"}'
```

Response:
```json
{
  "success": true,
  "response": "1+1 equals 2."
}
```

### Continue Existing Conversation

```bash
curl -X POST http://localhost:58080/cdp/send \
  -H "Content-Type: application/json" \
  -d '{"message": "And what about 2+2?", "new_conversation": false}'
```

### List Conversations

```bash
curl http://localhost:58080/cdp/conversations
```

Response:
```json
{
  "current": "My Chat Session",
  "conversations": [
    {"title": "My Chat Session", "time": "now"},
    {"title": "Previous Chat", "time": "1h"},
    {"title": "Old Discussion", "time": "1d"}
  ]
}
```

### Switch to a Conversation

```bash
curl -X POST http://localhost:58080/cdp/switch \
  -H "Content-Type: application/json" \
  -d '{"title": "Previous Chat"}'
```

Response:
```json
{
  "status": "switched",
  "title": "Previous Chat"
}
```

## Request Parameters

### POST /cdp/send

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `message` | string | Yes | - | Message content to send |
| `timeout` | int | No | 60 | Timeout in seconds |
| `new_conversation` | bool | No | true | Start a new conversation or continue current |

### POST /cdp/switch

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `title` | string | Yes | Conversation title to switch to |

## Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `PORT` | 58080 | HTTP server port |
| `CDP_PORT` | 9222 | Windsurf CDP debugging port |
