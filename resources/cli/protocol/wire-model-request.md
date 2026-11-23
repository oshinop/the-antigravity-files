# Model request and response stream

One main-agent call from the second prompt of a session, after 17 steps, and the stream that answered it. Strings are replaced by their length, IDs by placeholders, and repeated history entries are cut; field names, numbers and enum values are as sent. Background is in [Protocols and request flows](protocol-and-requests.md#the-model-call).

```text
POST daily-cloudcode-pa.googleapis.com/v1internal:streamGenerateContent?alt=sse
Content-Type: application/json
Authorization: Bearer [OAuth access token]
User-Agent: antigravity/cli/1.5.14 (aidev_client; os_type=[os]; arch=[arch]; cl=1026970498; auth_method=consumer)
(51,375 bytes)

{
  "project": "[project ID]",
  "requestId": "agent/[cascade ID]/[epoch ms]/[trajectory ID]/18",
  "model": "gemini-4-neon-medium",
  "userAgent": "antigravity",
  "requestType": "agent",
  "request": {
    "contents": [
      { "role": "user",  "parts": [ { "text": "<495 chars: <USER_REQUEST>…<ADDITIONAL_METADATA>…<USER_SETTINGS_CHANGE>…>" } ] },
      { "role": "model", "parts": [
          { "functionCall": { "name": "read_file", "args": { "Path": "…" }, "id": "<23 chars>" },
            "thoughtSignature": "<base64>" },
          { "functionCall": { "name": "run_command",
                              "args": { "CommandLine": "…", "WaitMsBeforeAsync": 5000, "toolSummary": "…" },
                              "id": "<23 chars>" } } ] },
      { "role": "model", "parts": [
          { "functionResponse": { "id": "…", "name": "read_file",   "response": { "output": "<273 chars>" } } },
          { "functionResponse": { "id": "…", "name": "run_command", "response": { "output": "<586 chars>" } } } ] },
      […]
      { "role": "model", "parts": [
          { "functionCall": { "name": "edit_file", "args": { "Path": "…", "Target": "…", "Replacement": "…" }, "id": "<24 chars>" },
            "thoughtSignature": "<base64>" } ] },
      { "role": "model", "parts": [
          { "functionResponse": { "id": "…", "name": "edit_file", "response": { "output": "<716 chars>" } } } ] },
      […]
      { "role": "model", "parts": [ { "text": "<264 chars: the answer to the first prompt>", "thoughtSignature": "<base64>" } ] },
      { "role": "user",  "parts": [ { "text": "<203 chars: <USER_REQUEST>…<ADDITIONAL_METADATA>…>" } ] },
      { "role": "model", "parts": [
          { "functionCall": { "name": "run_command", "args": { … }, "id": "<24 chars>" }, "thoughtSignature": "<base64>" } ] },
      { "role": "model", "parts": [
          { "functionResponse": { "id": "…", "name": "run_command", "response": { "output": "<414 chars>" } } } ] }
    ],
    "systemInstruction": { "role": "user", "parts": [ { "text": "<15,739 chars>" } ] },
    "tools": [ { "functionDeclarations": [
      { "name": "read_file",
        "description": "…",
        "parameters": { "type": "OBJECT",
                        "properties": { "ByteOffset": { "type": "INTEGER", "description": "…" },
                                        "EndLine":    { "type": "INTEGER", "description": "…" },
                                        "Path":       { "type": "STRING",  "description": "…" },
                                        […] },
                        "required": [ "Path" ] } },
      […13 more: run_command, manage_task, send_message, schedule, invoke_subagent, define_subagent,
       manage_subagents, write_file, edit_file, search_web, run_workflow, swarm, ask_question]
    ] } ],
    "labels": {
      "cascade_id": "[cascade ID]",
      "last_execution_id": "[execution ID]",
      "last_step_index": "17",
      "model_enum": "MODEL_PLACEHOLDER_M533",
      "request_id": "[trajectory ID]-<n>",
      "root_cascade_id": "[cascade ID]",
      "trajectory_id": "[trajectory ID]",
      "used_claude": "false",
      "used_claude_conservative": "false",
      "used_non_gemini_model": "false"
    },
    "generationConfig": {
      "maxOutputTokens": 262144,
      "thinkingConfig": { "includeThoughts": true, "thinkingLevel": "MEDIUM" }
    },
    "sessionId": "[signed 64-bit integer]"
  }
}
```

The response, as four server-sent events. `usageMetadata` repeats in every event; the last one closes the stream:

```text
200 OK
Content-Type: text/event-stream
Content-Disposition: attachment

data: {"response": {"candidates": [{"content": {"role": "model", "parts": [{"text": "<57 chars>"}]}}],
       "usageMetadata": {"promptTokenCount": 11718, "candidatesTokenCount": 13, "totalTokenCount": 11792,
                         "cachedContentTokenCount": 8971, "thoughtsTokenCount": 61},
       "modelVersion": "gemini-4-neon", "responseId": "<23 chars>"},
       "traceId": "<16 hex>", "metadata": {}}

data: {… "parts": [{"text": "<99 chars>"}] … "candidatesTokenCount": 42 …}

data: {… "parts": [{"text": "<6 chars>"}] … "candidatesTokenCount": 46 …}

data: {"response": {"candidates": [{"content": {"role": "model",
                                                "parts": [{"thoughtSignature": "<628 chars>", "text": ""}]},
                                    "finishReason": "STOP"}],
       "usageMetadata": {"promptTokenCount": 11718, "candidatesTokenCount": 47, "totalTokenCount": 11826,
                         "cachedContentTokenCount": 8971, "thoughtsTokenCount": 61},
       "modelVersion": "gemini-4-neon", "responseId": "<23 chars>"},
       "traceId": "<16 hex>", "metadata": {}}
```

A reply that calls tools streams `functionCall` parts the same way; a thinking reply starts with parts marked `"thought": true`. A failed call is not streamed; it comes back as one JSON error:

```text
503 Service Unavailable

{
  "error": {
    "code": 503,
    "message": "No capacity available for model gemini-4-argon-medium on the server",
    "status": "UNAVAILABLE",
    "details": [
      {
        "@type": "type.googleapis.com/google.rpc.ErrorInfo",
        "reason": "MODEL_CAPACITY_EXHAUSTED",
        "domain": "cloudcode-pa.googleapis.com",
        "metadata": {
          "error_number": "2010",
          "model": "gemini-4-argon-medium",
          "OVERLOADED_TOO_MANY_RETRIES_PER_REQUEST": "true"
        }
      }
    ]
  }
}
```
