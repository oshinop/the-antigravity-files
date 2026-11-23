# One `agy` 1.5.14 session

Two prompts to Gemini 4 Neon (Medium) in a small Python project, with default settings. The first asks the research helper to explore the project and summarize it; the second asks for a `discount(total, pct)` function and `git diff --stat`.

![Timeline of the session](timeline.png)

- **The helper outlives the turn.** The main agent delegates, then ends its first turn at 12.6 s with the helper still running. The helper keeps working until 61.5 s, through the whole second turn, and reports back with `send_message`.
- **Review models check tool calls.** Eight requests go to Gemini 3.5 Flash-Lite with the session and the pending tool call before the call runs. All eight returned severity 0.
- **Telemetry outweighs the work.** The agent's own model requests sent 0.54 MB. Clearcut sent 2.12 MB in 15 batches, every one containing `file:///` URIs, and three trajectory uploads sent another 0.62 MB.

## One turn, simplified

```mermaid
sequenceDiagram
  participant U as You
  participant A as agy
  participant M as Gemini 4 Neon
  participant R as Review model
  participant C as Clearcut
  participant T as Trajectory upload
  U->>A: prompt
  A->>M: system prompt, tools, history
  M-->>A: tool call
  A->>R: session history + pending tool call
  R-->>A: severity 0
  A->>A: run the tool
  A->>C: tool step, including its result
  A->>M: tool result
  M-->>A: reply
  A->>T: the whole turn
```

## Files

| File | Contents |
|---|---|
| `trace.jsonl` | One line per request: seconds since start, duration, endpoint, bytes sent and received, and a few fields per request type (model, tool names and arguments, review verdicts, how many `file:///` URIs a Clearcut batch held) |
| `timeline.json` | The same events as a Chrome trace. Open [ui.perfetto.dev](https://ui.perfetto.dev) and drag the file in to zoom, pan and click any request |
| `timeline.png` | The first 95 seconds of the timeline |
