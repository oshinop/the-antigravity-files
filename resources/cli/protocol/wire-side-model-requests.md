# Review, adjudicator, title and compaction requests

The model calls `agy` makes besides the agent's own. All go to the same `streamGenerateContent?alt=sse` endpoint as the agent with the same envelope; only `requestType`, `model` and the inner `request` differ. Strings are replaced by their length and IDs by placeholders. What the reviews receive and what their verdicts do is in [safety-reviews.md](../../../analysis/cli/safety-reviews.md); a full escalated review with its text is in [monitor-request.md](../traffic/monitor-request.md).

## Step review (Gemini 3.5 Flash-Lite)

```text
{
  "project": "[project ID]",
  "requestId": "agent_monitoring/[uuid]",
  "model": "gemini-3.5-flash-lite",
  "userAgent": "antigravity",
  "requestType": "agent_monitoring",
  "request": {
    "contents": [ { "role": "user", "parts": [ { "text": "<10,158 chars: rubric, transcript, pending tool call, output schema>" } ] } ],
    "generationConfig": { "thinkingConfig": { "includeThoughts": true, "thinkingLevel": "MINIMAL" } }
  }
}
```

No system instruction, tools, labels or session ID. The answer streams as thought parts, then the verdict as text:

````text
```json
{
  "reason": "The pending tool call simply lists files in the workspace directory and its src subdirectory, which falls under routine inspection and viewing activities.",
  "severity": 0,
  "confidence": 10
}
```
````

## History review (Gemini 4 Hydrogen Sonar)

The same layout with `"model": "gemini-4-hydrogen-sonar"` and `"thinkingLevel": "LOW"`. The single text part carried the whole transcript: 23,007 characters in one call.

## Command adjudicator (auto mode)

```text
{
  "project": "[project ID]",
  "requestId": "agent/[cascade ID]/[epoch ms]/[trajectory ID]/1",
  "model": "gemini-4-hydrogen-medium",
  "userAgent": "antigravity",
  "requestType": "agent",
  "request": {
    "contents": [ { "role": "user", "parts": [ { "text": "<468 chars: <command>, working directory, transcript path>" } ] } ],
    "systemInstruction": { "role": "user", "parts": [ { "text": "<10,891 chars>" } ] },
    "tools": [ { "functionDeclarations": [ read_file, run_command, list_directory, search_files, search_transcript, submit_verdict ] } ],
    "labels": { "cascade_id": "[cascade ID]", "last_step_index": "0", "model_enum": "MODEL_PLACEHOLDER_M526",
                "request_id": "[trajectory ID]-0", "trajectory_id": "[trajectory ID]",
                "used_claude": "false", "used_claude_conservative": "false", "used_non_gemini_model": "false" },
    "generationConfig": { "thinkingConfig": { "includeThoughts": true, "thinkingLevel": "MEDIUM" } },
    "sessionId": "[the main agent's session ID]"
  }
}
```

The adjudicator is its own conversation (its own `cascade_id` and `trajectory_id`, no `root_cascade_id`) that shares the main agent's `sessionId`. It answers with a function call:

```text
data: {"response": {"candidates": [{"content": {"role": "model", "parts": [{"thoughtSignature": "<748 chars>",
       "functionCall": {"name": "submit_verdict", "args": {"reason": "<106 chars>", "reversible": true}, "id": "<25 chars>"}}]}}], …}
data: {… "parts": [{"text": ""}]}, "finishReason": "STOP" …}
```

## Title generator

```text
{
  "project": "[project ID]",
  "requestId": "checkpoint/[uuid]",
  "model": "gemini-3.5-flash-lite",
  "userAgent": "antigravity",
  "requestType": "checkpoint",
  "request": {
    "contents": [ { "role": "user", "parts": [ { "text": "<94 chars: the first prompt>" } ] } ],
    "systemInstruction": { "role": "user", "parts": [ { "text": "<624 chars>" } ] },
    "generationConfig": { "thinkingConfig": { "includeThoughts": true, "thinkingLevel": "LOW" } },
    "sessionId": "[the main agent's session ID]"
  }
}
```

Sent with the first prompt of a conversation, in parallel with the agent's first call. The title streams back as plain text.

## Compaction

```text
{
  "project": "[project ID]",
  "requestId": "checkpoint/[cascade ID]/[epoch ms]/[trajectory ID]/<n>",
  "model": "gemini-4-argon-medium",
  "userAgent": "antigravity",
  "requestType": "checkpoint",
  "request": {
    "contents": [ <the whole conversation so far, in the agent's history layout> ],
    "systemInstruction": { <the main agent's system prompt> },
    "tools": [ <the main agent's 14 declarations> ],
    "labels": { <the main agent's labels> },
    "generationConfig": { "maxOutputTokens": 32768,
                          "thinkingConfig": { "includeThoughts": true, "thinkingLevel": "MEDIUM" } },
    "sessionId": "[the main agent's session ID]"
  }
}
```

One compaction request was 261,897 bytes. The summary streams back as text and replaces the history in the agent's next call ([prompts-and-context.md](../../../analysis/cli/prompts-and-context.md)).
