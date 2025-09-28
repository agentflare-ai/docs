# WebSocket Protocol Documentation

## Overview

The Agentflare WebSocket system provides real-time bidirectional communication for the platform, enabling instant updates for tool calls, parameter changes, cache invalidations, and error notifications.

## Connection

### Endpoint

```
wss://your-domain.com/ws
```

### Authentication

Authentication is required during the handshake phase. Provide your authentication token in one of these ways:

1. **Auth object** (recommended):

```javascript
const socket = io("wss://your-domain.com", {
  path: "/ws",
  auth: {
    token: "your-auth-token",
  },
});
```

2. **Authorization header**:

```javascript
const socket = io("wss://your-domain.com", {
  path: "/ws",
  extraHeaders: {
    Authorization: "Bearer your-auth-token",
  },
});
```

### Connection Response

Upon successful connection, you'll receive:

```json
{
  "id": "msg_1234567890_abc123",
  "type": "connection:established",
  "timestamp": "2024-01-20T12:00:00.000Z",
  "payload": {
    "clientId": "socket_abc123",
    "version": "1.0.0",
    "capabilities": ["parameter_updates", "tool_calls", "session_metrics"]
  }
}
```

## Event Types

### Connection Events

#### CONNECTION_ESTABLISHED

Sent when connection is successfully established.

```json
{
  "type": "connection:established",
  "payload": {
    "clientId": "socket_abc123",
    "version": "1.0.0",
    "capabilities": ["parameter_updates", "tool_calls", "session_metrics"]
  }
}
```

#### CONNECTION_ERROR

Sent when a connection error occurs.

```json
{
  "type": "connection:error",
  "payload": {
    "code": "MAX_CONNECTIONS",
    "message": "Maximum connections exceeded",
    "retryAfter": 60000
  }
}
```

### Subscription Events

#### SUBSCRIBE

Client-to-server event to subscribe to channels.

```json
{
  "type": "subscribe",
  "payload": {
    "channels": ["workspace:123", "user:456"],
    "filters": {
      "toolName": "fetch_data",
      "sessionId": "session_789"
    }
  }
}
```

#### SUBSCRIPTION_CONFIRMED

Server response confirming subscription.

```json
{
  "type": "subscription:confirmed",
  "payload": {
    "channels": ["workspace:123", "user:456"],
    "filters": {}
  }
}
```

### Tool Call Events

#### TOOL_CALL_START

Broadcast when a tool call begins.

```json
{
  "type": "tool_call:start",
  "payload": {
    "callId": "call_123",
    "toolName": "fetch_data",
    "parameters": {
      "url": "https://api.example.com/data",
      "method": "GET"
    },
    "startTime": "2024-01-20T12:00:00.000Z",
    "status": "pending"
  }
}
```

#### TOOL_CALL_PROGRESS

Updates during tool execution.

```json
{
  "type": "tool_call:progress",
  "payload": {
    "callId": "call_123",
    "status": "running",
    "reasoning": "Fetching data from external API",
    "confidence": 0.85
  }
}
```

#### TOOL_CALL_COMPLETE

Sent when tool call completes successfully.

```json
{
  "type": "tool_call:complete",
  "payload": {
    "callId": "call_123",
    "endTime": "2024-01-20T12:00:05.000Z",
    "duration": 5000,
    "status": "completed",
    "result": {
      "data": [1, 2, 3]
    },
    "cost": {
      "estimated": 0.002,
      "actual": 0.0018,
      "currency": "USD"
    }
  }
}
```

#### TOOL_CALL_ERROR

Sent when tool call fails.

```json
{
  "type": "tool_call:error",
  "payload": {
    "callId": "call_123",
    "status": "failed",
    "error": "Connection timeout"
  }
}
```

### Parameter Update Events

#### PARAMETER_UPDATE

Single parameter update.

```json
{
  "type": "parameter:update",
  "payload": {
    "parameterId": "param_123",
    "parameterName": "api_key",
    "oldValue": "old_key",
    "newValue": "new_key",
    "source": "user_update",
    "metadata": {
      "timestamp": 1705762800000
    }
  }
}
```

#### PARAMETER_BATCH_UPDATE

Multiple parameter updates.

```json
{
  "type": "parameter:batch_update",
  "payload": [
    {
      "parameterId": "param_123",
      "parameterName": "api_key",
      "oldValue": "old_key",
      "newValue": "new_key",
      "source": "bulk_import"
    },
    {
      "parameterId": "param_456",
      "parameterName": "timeout",
      "oldValue": 30,
      "newValue": 60,
      "source": "bulk_import"
    }
  ]
}
```

### Cache Events

#### CACHE_INVALIDATE

Notifies clients to invalidate cached data.

```json
{
  "type": "cache:invalidate",
  "payload": {
    "cacheKey": "schema:tool_123",
    "cacheType": "schema",
    "pattern": "schema:*",
    "reason": "Schema updated",
    "timestamp": 1705762800000
  }
}
```

### Session Events

#### SESSION_START

New session started.

```json
{
  "type": "session:start",
  "payload": {
    "sessionId": "session_789"
  }
}
```

#### SESSION_METRICS

Session performance metrics.

```json
{
  "type": "session:metrics",
  "payload": {
    "sessionId": "session_789",
    "toolCalls": 25,
    "totalCost": 0.125,
    "duration": 300000,
    "errors": 2,
    "performance": {
      "avgResponseTime": 250,
      "p95ResponseTime": 450,
      "p99ResponseTime": 800
    }
  }
}
```

### Error Events

#### ERROR

General error notification.

```json
{
  "type": "error",
  "payload": {
    "code": "INTERNAL_ERROR",
    "message": "An unexpected error occurred",
    "details": {
      "component": "websocket_service"
    },
    "retry": true
  }
}
```

## Rate Limiting

The WebSocket server implements rate limiting to prevent abuse:

- **Default limit**: 100 messages per minute per connection
- **Window**: 60 seconds rolling window
- **Response on limit exceeded**:

```json
{
  "type": "connection:error",
  "payload": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests",
    "retryAfter": 60000
  }
}
```

## Connection Limits

- **Per IP**: Maximum 10 concurrent connections per IP address
- **Per User**: Maximum 5 concurrent connections per authenticated user
- **Total**: Maximum 10,000 concurrent connections server-wide

## Compression

Large payloads are automatically compressed:

- **Threshold**: Messages over 1KB are compressed
- **Algorithm**: Socket.IO built-in compression (permessage-deflate)

## Event Buffering

Events are buffered for offline clients:

- **Buffer size**: Up to 100 messages per client
- **Expiry**: Buffered messages expire after 5 minutes
- **Delivery**: Buffered messages are sent immediately upon reconnection

## Heartbeat

The server implements automatic heartbeat to detect stale connections:

- **Ping interval**: 25 seconds
- **Ping timeout**: 60 seconds
- **Client response**: Clients must respond to PING with PONG

## Client SDK Usage

### JavaScript/TypeScript

```typescript
import { getWebSocketClient } from "@/lib/websocket/client";

// Initialize client
const client = getWebSocketClient({
  url: "wss://your-domain.com",
  path: "/ws",
  token: "your-auth-token",
  reconnection: true,
  reconnectionAttempts: 5,
});

// Connect
await client.connect();

// Subscribe to events
client.on(WebSocketEventType.TOOL_CALL_START, (message) => {
  console.log("Tool call started:", message.payload);
});

// Subscribe to channels
await client.subscribe(["workspace:123"], {
  toolName: "fetch_data",
});

// Disconnect when done
client.disconnect();
```

## Horizontal Scaling

The WebSocket server supports horizontal scaling via Redis adapter:

1. **Redis Configuration**: Set Redis connection details in environment variables
2. **Automatic sync**: Events are automatically synchronized across server instances
3. **Sticky sessions**: Not required - clients can connect to any server instance

## Security Considerations

1. **Authentication**: All connections must be authenticated
2. **Authorization**: Channel subscriptions are permission-checked
3. **Rate limiting**: Prevents DoS attacks
4. **Connection limits**: Prevents resource exhaustion
5. **TLS**: Always use WSS (WebSocket Secure) in production

## Error Handling

### Connection Errors

- Authentication failures result in immediate disconnection
- Rate limit violations receive error messages
- Connection limit violations prevent new connections

### Message Errors

- Invalid message formats are silently dropped
- Handler errors are logged but don't disconnect clients
- Subscription errors receive error responses

## Best Practices

1. **Reconnection**: Always implement automatic reconnection logic
2. **Event handlers**: Wrap handlers in try-catch blocks
3. **Subscriptions**: Unsubscribe from channels when no longer needed
4. **Heartbeat**: Respond to PING events to maintain connection
5. **Buffering**: Handle potential duplicate messages after reconnection
6. **Rate limiting**: Implement client-side rate limiting to avoid server limits
