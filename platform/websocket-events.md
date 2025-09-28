# WebSocket Event System Documentation

## Overview

The Agentflare WebSocket event system provides real-time communication for MCP observability, enabling live monitoring of tool calls, parameter updates, and session metrics.

## Event Types

### Connection Events

#### `connection:established`

Sent when a client successfully connects and authenticates.

```typescript
{
  id: string;
  type: 'connection:established';
  timestamp: string;
  payload: {
    clientId: string;
    version: string;
    capabilities: string[];
  };
}
```

#### `connection:error`

Sent when a connection fails.

```typescript
{
  id: string;
  type: 'connection:error';
  timestamp: string;
  payload: {
    code: string;
    message: string;
    details?: any;
    retry?: boolean;
  };
}
```

#### `connection:closed`

Sent when a connection is closed.

```typescript
{
  id: string;
  type: "connection:closed";
  timestamp: string;
  payload: {
    reason: string;
    code: number;
  }
}
```

### Authentication Events

#### `auth:required`

Sent when authentication is required.

```typescript
{
  id: string;
  type: 'auth:required';
  timestamp: string;
  payload: {
    methods: string[];
    challenge?: string;
  };
}
```

#### `auth:success`

Sent when authentication succeeds.

```typescript
{
  id: string;
  type: 'auth:success';
  timestamp: string;
  payload: {
    userId: string;
    workspaceId: string;
    permissions: string[];
  };
}
```

#### `auth:failure`

Sent when authentication fails.

```typescript
{
  id: string;
  type: 'auth:failure';
  timestamp: string;
  payload: {
    code: string;
    message: string;
    retry?: boolean;
  };
}
```

### Subscription Events

#### `subscribe`

Client request to subscribe to channels.

```typescript
{
  id: string;
  type: 'subscribe';
  timestamp: string;
  payload: {
    channels: string[];
    filters?: Record<string, any>;
  };
}
```

#### `unsubscribe`

Client request to unsubscribe from channels.

```typescript
{
  id: string;
  type: 'unsubscribe';
  timestamp: string;
  payload: {
    channels: string[];
  };
}
```

#### `subscription:confirmed`

Server confirmation of subscription.

```typescript
{
  id: string;
  type: 'subscription:confirmed';
  timestamp: string;
  payload: {
    channels: string[];
    filters?: Record<string, any>;
  };
}
```

#### `subscription:error`

Server error for subscription request.

```typescript
{
  id: string;
  type: 'subscription:error';
  timestamp: string;
  payload: {
    code: string;
    message: string;
    channels?: string[];
  };
}
```

### Parameter Update Events

#### `parameter:update`

Sent when a single parameter is updated.

```typescript
{
  id: string;
  type: 'parameter:update';
  timestamp: string;
  payload: {
    parameterId: string;
    parameterName: string;
    oldValue: any;
    newValue: any;
    source: string;
    metadata?: Record<string, any>;
  };
}
```

#### `parameter:batch_update`

Sent when multiple parameters are updated.

```typescript
{
  id: string;
  type: "parameter:batch_update";
  timestamp: string;
  payload: Array<{
    parameterId: string;
    parameterName: string;
    oldValue: any;
    newValue: any;
    source: string;
    metadata?: Record<string, any>;
  }>;
}
```

### Tool Call Events

#### `tool_call:start`

Sent when a tool call begins.

```typescript
{
  id: string;
  type: "tool_call:start";
  timestamp: string;
  payload: {
    callId: string;
    toolName: string;
    parameters: Record<string, any>;
    startTime: string;
    status: "pending";
  }
}
```

#### `tool_call:progress`

Sent during tool call execution.

```typescript
{
  id: string;
  type: 'tool_call:progress';
  timestamp: string;
  payload: {
    callId: string;
    status: 'running';
    reasoning?: string;
    confidence?: number;
    [key: string]: any;
  };
}
```

#### `tool_call:complete`

Sent when a tool call completes successfully.

```typescript
{
  id: string;
  type: 'tool_call:complete';
  timestamp: string;
  payload: {
    callId: string;
    endTime: string;
    duration: number;
    status: 'completed';
    result: any;
    cost?: {
      estimated: number;
      actual?: number;
      currency: string;
    };
  };
}
```

#### `tool_call:error`

Sent when a tool call fails.

```typescript
{
  id: string;
  type: "tool_call:error";
  timestamp: string;
  payload: {
    callId: string;
    status: "failed";
    error: string;
  }
}
```

### Session Events

#### `session:start`

Sent when a new session begins.

```typescript
{
  id: string;
  type: "session:start";
  timestamp: string;
  payload: {
    sessionId: string;
  }
}
```

#### `session:end`

Sent when a session ends.

```typescript
{
  id: string;
  type: "session:end";
  timestamp: string;
  payload: {
    sessionId: string;
  }
}
```

#### `session:metrics`

Sent with session performance metrics.

```typescript
{
  id: string;
  type: "session:metrics";
  timestamp: string;
  payload: {
    sessionId: string;
    toolCalls: number;
    totalCost: number;
    duration: number;
    errors: number;
    performance: {
      avgResponseTime: number;
      p95ResponseTime: number;
      p99ResponseTime: number;
    }
  }
}
```

### Heartbeat Events

#### `ping`

Client heartbeat to server.

```typescript
{
  id: string;
  type: "ping";
  timestamp: string;
  payload: {
    timestamp: number;
  }
}
```

#### `pong`

Server response to client heartbeat.

```typescript
{
  id: string;
  type: "pong";
  timestamp: string;
  payload: {
    timestamp: number;
  }
}
```

## Channels

### Available Channels

- `global`: All events (default)
- `parameter_updates`: Parameter change events
- `tool_calls`: Tool call events
- `session_metrics`: Session performance metrics
- `user:{userId}`: Events for specific user
- `workspace:{workspaceId}`: Events for specific workspace

### Channel Filters

Filters can be applied to subscriptions to limit events:

```typescript
{
  channels: ['tool_calls'],
  filters: {
    toolName: 'search_web',
    status: ['completed', 'failed']
  }
}
```

## Authentication

### Bearer Token Authentication

```typescript
// During connection
{
  auth: {
    token: 'your-bearer-token'
  }
}

// Or via headers
{
  headers: {
    'Authorization': 'Bearer your-bearer-token'
  }
}
```

### API Key Authentication

```typescript
{
  auth: {
    token: 'your-api-key',
    method: 'api_key'
  }
}
```

## Client Usage Examples

### Basic Connection

```typescript
import { WebSocketClient } from "@/lib/websocket/client";

const client = new WebSocketClient({
  url: "wss://api.agentflare.com",
  token: "your-token",
});

await client.connect();
```

### Subscribing to Events

```typescript
// Subscribe to tool calls
await client.subscribe(["tool_calls"]);

// Listen for tool call events
client.on("tool_call:start", (event) => {
  console.log("Tool call started:", event.payload);
});

client.on("tool_call:complete", (event) => {
  console.log("Tool call completed:", event.payload);
});
```

### Parameter Updates

```typescript
// Subscribe to parameter updates
await client.subscribe(["parameter_updates"]);

// Listen for parameter changes
client.on("parameter:update", (event) => {
  const { parameterName, oldValue, newValue } = event.payload;
  console.log(`${parameterName} changed from ${oldValue} to ${newValue}`);
});
```

### Session Monitoring

```typescript
// Subscribe to session metrics
await client.subscribe(["session_metrics"]);

// Listen for session events
client.on("session:start", (event) => {
  console.log("Session started:", event.payload.sessionId);
});

client.on("session:metrics", (event) => {
  const { toolCalls, totalCost, performance } = event.payload;
  console.log(
    `Session metrics: ${toolCalls} calls, $${totalCost}, ${performance.avgResponseTime}ms avg`
  );
});
```

## React Hook Usage

### useWebSocket Hook

```typescript
import { useWebSocket } from '@/hooks/useWebSocket';

function MyComponent() {
  const {
    connected,
    authenticated,
    connect,
    disconnect,
    subscribe,
    on,
    off
  } = useWebSocket({
    url: 'wss://api.agentflare.com',
    token: 'your-token',
    autoConnect: true,
    channels: ['tool_calls', 'parameter_updates']
  });

  useEffect(() => {
    if (connected) {
      const handleToolCall = (event) => {
        console.log('Tool call:', event.payload);
      };

      on('tool_call:start', handleToolCall);

      return () => {
        off('tool_call:start', handleToolCall);
      };
    }
  }, [connected, on, off]);

  return (
    <div>
      <p>Status: {connected ? 'Connected' : 'Disconnected'}</p>
      {!connected && (
        <button onClick={connect}>Connect</button>
      )}
    </div>
  );
}
```

## Error Handling

### Connection Errors

```typescript
client.on("connection:error", (event) => {
  const { code, message, retry } = event.payload;

  if (retry) {
    setTimeout(() => client.connect(), 5000);
  } else {
    console.error("Connection failed:", message);
  }
});
```

### Authentication Errors

```typescript
client.on("auth:failure", (event) => {
  const { code, message } = event.payload;

  if (code === "TOKEN_EXPIRED") {
    // Refresh token and reconnect
    refreshToken().then((newToken) => {
      client.disconnect();
      client = new WebSocketClient({ token: newToken });
      client.connect();
    });
  }
});
```

### Subscription Errors

```typescript
client.on("subscription:error", (event) => {
  const { code, message, channels } = event.payload;

  if (code === "PERMISSION_DENIED") {
    console.warn("Insufficient permissions for channels:", channels);
  }
});
```

## Performance Considerations

### Message Batching

The server may batch parameter updates to reduce message frequency:

```typescript
// Instead of multiple parameter:update events
// Server sends one parameter:batch_update event
client.on("parameter:batch_update", (event) => {
  event.payload.forEach((update) => {
    handleParameterUpdate(update);
  });
});
```

### Connection Limits

- Maximum 1000 concurrent connections per workspace
- Message rate limit: 100 messages per second per connection
- Subscription limit: 50 channels per connection

### Reconnection Strategy

The client implements exponential backoff for reconnection:

```typescript
{
  reconnectionDelay: 1000,      // Initial delay
  reconnectionDelayMax: 5000,   // Maximum delay
  reconnectionAttempts: Infinity // Unlimited attempts
}
```

## Security

### Token Validation

Tokens are validated on every connection attempt and periodically during the session.

### Rate Limiting

- Connection attempts: 10 per minute per IP
- Message sending: 100 per second per connection
- Subscription changes: 20 per minute per connection

### CORS Configuration

```typescript
{
  cors: {
    origin: process.env.ALLOWED_ORIGINS?.split(',') || '*',
    credentials: true
  }
}
```
