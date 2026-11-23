# ACP handshake

What `agy acp` answers on stdout to an `initialize` request, and to `session/new` before sign-in. Both responses were captured from `agy` 1.5.11; the 1.5.14 binary contains the same server name, auth method IDs and capability keys. The message flow is in [Protocols and request flows](protocol-and-requests.md#acp).

```text
→ {"jsonrpc": "2.0", "id": 1, "method": "initialize", …}

← {
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
      "protocolVersion": 1,
      "agentCapabilities": {
        "loadSession": true,
        "load_session": true,
        "promptCapabilities": { "image": true, "audio": false, "embeddedContext": true },
        "sessionCapabilities":  { "resume": {}, "close": {}, "load": {}, "list": {}, "additionalDirectories": {} },
        "session_capabilities": { "resume": {}, "close": {}, "load": {}, "list": {}, "additionalDirectories": {} },
        "auth": { "logout": {} },
        "_meta": { "antigravity": { "tier": true, "systemPrompt": { "append": true } } }
      },
      "agentInfo": { "name": "Antigravity ACP Server", "version": "1.5.11" },
      "authMethods": [
        { "id": "oauth-personal", "name": "Log in with Google",
          "description": "Log in with your Google account" },
        { "id": "oauth-business", "name": "Log in with Gemini Enterprise",
          "description": "Log in with your Gemini Enterprise account" },
        { "id": "gemini-api-key", "name": "Gemini API key",
          "description": "Use an API key with Gemini Developer API" },
        { "id": "agent-platform", "name": "Gemini Enterprise Agent Platform",
          "description": "Use Gemini Enterprise Agent Platform (formerly Vertex AI) with Application Default Credentials" }
      ]
    }
  }

→ {"jsonrpc": "2.0", "id": 2, "method": "session/new", …}

← {"jsonrpc": "2.0", "error": {"code": -32000, "message": "Authentication required"}, "id": 2}
```

`loadSession` and `sessionCapabilities` are each sent twice, once in camelCase and once in snake_case. The `_meta.antigravity` block advertises appending to the system prompt. Nothing in the handshake mentions subagents, forks or MCP capabilities.
